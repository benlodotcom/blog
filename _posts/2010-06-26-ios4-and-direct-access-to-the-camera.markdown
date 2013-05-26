---
layout: post
title:  "iOS4 and direct access to the camera"
date:   2010-06-26 20:51:00
categories: ios
---

The iPhone SDK4 brought a lot of interesting features. Among them the direct access to the camera is a real asset for AR (Augmented reality) applications or in a more general way for all applications processing the image/video to modify it or extract informations. I played around with the new related APIs so I’m going to tell you more about it and share some snippets showing how to use these APIs. If you want to know more about the [AVFoundation][] framework, [AVCaptureDeviceInput][], [AVCaptureVideoDataOutput][] and [AVCaptureSession][] this article is for you.

[AVFoundation]: http://developer.apple.com/library/ios/#DOCUMENTATION/AVFoundation/Reference/AVFoundationFramework
[AVCaptureDeviceInput]: http://developer.apple.com/library/ios/#documentation/AVFoundation/Reference/AVCaptureDeviceInput_Class
[AVCaptureVideoDataOutput]: https://developer.apple.com/library/ios/#documentation/AVFoundation/Reference/AVCaptureVideoDataOutput_Class
[AVCaptureSession]: https://developer.apple.com/library/ios/#documentation/AVFoundation/Reference/AVCaptureSession_Class

##How to access the raw data of the camera

So, apple finally released the “V” of the AVFoundation framework. They provide handy components to get the raw data from the camera. Basically, the path to follow to get these is :

-   Setup an `AVCaptureDeviceInput` instance and tell it to read the data provided by the camera.
-   Setup an `AVCaptureVideoDataOutput` instance. This object will output the data and will pass it to its delegate.
-   Now that the input and the output are ready you have to setup the component which will connect them. `AVCaptureSession` plays this role. To setup an `AVCaptureSession` instance you have to specify the input and the output and then you can call `[yourAVCaptureSession startRunning]`
-   Now every time a new frame is processed, the `AVCaptureVideoDataOutput` delegate will be notified.

##Let’s do some coding

Here you have a code sample that you can use to try some possibilities offered by the SDK 4.

**[Update] I made a small Xcode project which shows how to use this sample. [It’s on my github](http://github.com/benlodotcom/MyAVControllerDemo)**

**[Update] As a lot of people were asking about how to use a different queue than the main one to do the processing, I updated the code to show how to do it. Pay attention to the fact that we have to call all the
display stuff in the main thread to make it work (UIKit is not thread safe).**

{% gist 451924 MyAVController.h %}
{% gist 451924 MyAVController.m %}

##Ok cool, but what can I do with it ?

Actually what you can do is pretty simple but very instructive. This code shows how to capture data from the camera and then output them on a view using three different ways :

1. Using an `AVCaptureVideoPreviewLayer` instance, this class
    created by Apple is a subclass of `CALayer`. You can specify the
    capture session you want to output the video.
2. Getting the raw data, process them to create a `CGImageRef` and
    write it on a `CALayer`
3.  Getting the raw data, process them to create a `CGImageRef`,
    then process this `CGImageRef` to create a `UIImage` and finally
    write it on a `UIImageView`.

### Performances

The code can probably be improved but for now the only way to get something as smooth as what you can get with the Camera app is using the first method. So if you want to add informations over the camera output my advice would be:

-   To display the `AVCaptureVideoPreviewLayer`.
-   Use the delegate method to process the data (do some pattern detection for instance).
-   And then create an overlay - using a `CALayer` for example - where you display your informations.

You could also try to apply filters on the video, you can try modifying the `AVCaptureVideoPreviewLayer` as if it was a regular `CALayer` - as it is a subclass of it. And if you do it please let me know the results ;-)

Finally if you really need to show an image computed from the raw data, the faster is surprisingly to use the `UIImage` method.


 Ben