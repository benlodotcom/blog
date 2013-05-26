---
layout: post
title:  "Virtual reality on iPhone [code inside]"
date:   2010-08-28 23:05:07
categories: ios
---

For those who have been waiting for the code of the virtual reality application for iOS, here it is. Note that it has been released under GPL license.
<div class="videoWrapper">
<iframe width="420" height="315" src="http://www.youtube.com/embed/4HGSIyKgcyw?html5=1" frameborder="0" allowfullscreen></iframe>
</div>

##Features

Here is an overview of the functionalities provided by this iPhone app :

-   Single marker detection (the marker detection is done using an objective-c wrapper developed over ARToolkitPlus).
-   Loading of 3D objects using custom xml and .h files (or .obj but the parser is very slow for now). Only one texture file is supported.
-   The association between a markerID and an object is done using a .plist file
-   GUI to modify the display parameters associated to an object

##The code and the documentation

The code repository is [here](https://github.com/benlodotcom/VRToolKit) on github. The documentation is included in the repository, it is also available [here](http://www.benjaminloulier.com/projects/VRToolKit/doc/index.html) (Doxygen has been used to generate the [documentation of the code](http://www.benjaminloulier.com/projects/VRToolKit/doc/code_detailed/index.html)).

##Be a part of it

Well I'll be glad if some people want to work on this project, basically you have two solutions :
-   Fork me on github
-   I can also add collaborators for this project so send me an email.
I'd like to add some fancy content in the app (like buildings, statues ...). The thing is I don't know anything about 3D modeling. So if somebody can do it I'll be more than happy to integrate it in the application.

There are also other improvements like multi marker detection that could be achieved.

##Conclusion

That's all for VRToolKit, if you like it you can share it.

If you have any questions you can use the comment section, I'm also on [twitter](http://www.twitter.com/benlodotcom).

Ben