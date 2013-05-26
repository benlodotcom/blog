---
layout: post
title:  "Clear the HTML5 Application Cache of an UIWebView"
date:   2013-04-02 22:42:07
categories: ios
---

Today we will talk about how the [HTML5 application cache](https://developer.mozilla.org/en-US/docs/HTML/Using_the_application_cache) is implemented on iOS. And particularly, as HTML5 doesn't offer yet an API to flush the application cache, we will see how we can do so using plain Objective-C.  

Let's take the following use case: 

* You have an hybrid app that displays articles from a blog in a UIWebView.
* You leverage the application cache to have some articles available in offline mode and each article has its own cache manifest.

Over time your application cache will grow, you will eventually reach the 10MB storage limit imposed by the UIWebView and it will start throwing javascript errors if you try to cache more data.

[![application cache javascript error]({% asset_path application_cache_js_error.png %})]({% asset_path application_cache_js_error.png %})

So you would like to free some space in the application cache preferably flushing resources from articles that have not been consulted recently. 

In this scenario HTML5 doesn't offer any API that could help us achieve what we want. So let's go to the native side and try to find where the iOS version of Webkit stores the application cache data.

**Disclaimer: This method could break anytime with a new version of iOS, use it at your know risks.**

In `Library/Caches/yourBundleId/` you'll find a file named `ApplicationCache.db`. Looks promising, now open it with your favourite sqlite viewer, bingo we found the sqlite database that Apple uses to manage the application cache. 

This database contains some interesting tables :

* *`CacheGroups`* : Conceptually, a cache group is a set of resources (.html, .css, .js, images …) associated to a cache manifest. 
This table contains an entry for each cache group which contains the ID of the cache group but also the url of the corresponding manifest.
* *`CacheEntries`, `CacheResources`* : These two tables store some metadatas about each cached resource (HTTP header, mime type, url …).
* `CacheResourcesData` : In this table webkit stores as a `BLOB` (Binary Large OBject) each cached resource.
* *`Caches`* : For each Cache Group this table stores the total size of the associated resources. 

Looking at the sqlite master table you'll find the following [`ON DELETION TRIGGERS`](http://www.sqlite.org/lang_createtrigger.html): 

* `CREATE TRIGGER CacheDeleted AFTER DELETE ON Caches FOR EACH ROW BEGIN  DELETE FROM CacheEntries WHERE cache = OLD.id;  DELETE FROM CacheWhitelistURLs WHERE cache = OLD.id;  DELETE FROM CacheAllowsAllNetworkRequests WHERE cache = OLD.id;  DELETE FROM FallbackURLs WHERE cache = OLD.id; END`
* `CREATE TRIGGER CacheEntryDeleted AFTER DELETE ON CacheEntries FOR EACH ROW BEGIN  DELETE FROM CacheResources WHERE id = OLD.resource; END`
* `CREATE TRIGGER CacheResourceDeleted AFTER DELETE ON CacheResources FOR EACH ROW BEGIN  DELETE FROM CacheResourceData WHERE id = OLD.data; END`
* `CREATE TRIGGER CacheResourceDataDeleted AFTER DELETE ON CacheResourceData FOR EACH ROW WHEN OLD.path NOT NULL BEGIN  INSERT INTO DeletedCacheResources (path) values (OLD.path); END`

These triggers tell us that deleting a row in the `Caches` table will cascade delete all the related data in all the other tables except `CacheGroups`.

Now you have all the information to solve the initial problem.
To flush resources associated to an old article you just have to find the ID of the cache group associated to the cache manifest of the article, and then delete the corresponding rows in the `CacheGroups` and `Caches` table (the `ON DELETION TRIGGERS` will take care of the rest).

> * Note: Some of you would like to get a better granularity when deleting cached resources and be able to delete only some resources in a cache group. Unfortunately if you simply delete a row in CacheEntries this will make the database inconsistent with what Webkit expects and your app will crash.
* I haven't found a workaround around this yet but if any of you have, I would be very interested in hearing about your solution.

As a line of code says more than a thousand words, here is some commented code that will allow you to flush all the resources associated to one or many cache manifests.

{% gist 5296180 %}

##Wrap up
Again, be careful using this trick. As said previously it could break anytime, for instance if Apple decides to change the structure of the database. Finally, big thanks to [@oliamb](http://www.twitter.com/oliamb) for working with me on this problem.

As usual, feel free to ask questions and give me feedbacks on [twitter (@benlodotcom)](http://www.twitter.com/benlodotcom) or in the comment section.