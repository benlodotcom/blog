---
layout: post
title:  "Augmented Reality on iPhone using ARToolKitPlus"
date:   2010-07-24 22:43:07
categories: ios
---

Augmented reality can be qualified as a "hot topic" in IT, especially since the arrival of all the phone on steroids (iPhone, Android ...) as I like to call them. I'm working on a small application which uses ARToolKitPlus and the new camera APIs of the iPhone SDK4 to display 3D objects in augmented reality. You'll find in this post a video showing how it looks like for now and some explanations about augmented reality. **Also I'll release the code under GPL (as ARToolKitPlus is) soon , so stay tuned !**


##Augmented reality

*“Augmented reality (AR) is a term for a live direct or indirect view of a physical real-world environment whose elements are augmented by virtual computer-generated imagery”* - [Wikipedia page][] For now, there are two manners of doing augmented reality:

-   The first one is using the positioning of the person and the direction he is looking at to overlay pertinent informations.
-   The second is using markers, when a marker is detected a virtual object is displayed over it, the behavior of the object is the same as if the marker was a table it was stuck on.

I had the opportunity to work on both methods for iPhone but today i’m going to tell you about the second one.

##iPhone app

I made a small iPhone app using ARToolKitPlus for the marker detection and OpenGL ES for the displaying of the 3D object. Moreover, I used the new camera APIs released in SDK4 - take a look at [this post][] if you want to know more - which make the process of capturing images from the camera very fast. Here is how it looks like for now :

<div class="videoWrapper">
<iframe width="560" height="315" src="http://www.youtube.com/embed/QrS2Zttw4ZM?rel=0" frameborder="0" allowfullscreen></iframe>
</div>

The display of the video and of the object are realized in two different threads, this way the video is always fluid and we have a better user experience. I still have some works to do on the code before getting something I can release, but if you are interested you can watch the blog or [follow me on twitter][] to be the first to know when I’ll put all this on github. And if you want to ask questions don’t hesitate to post comments.


Ben

  [Wikipedia page]: http://en.wikipedia.org/wiki/Augmented_reality
  [this post]: posts/2-ios4-and-direct-access-to-the-camera
  [follow me on twitter]: http://twitter.com/benlodotco