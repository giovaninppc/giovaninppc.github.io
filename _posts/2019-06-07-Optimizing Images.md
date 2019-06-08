---
layout: post
title: "Optimizing Images"
featured-img: Events/Altconf/sticker
categories: [Event]
---

# Optimizing Images
### Based on the speech of Jordan Morgan, Altconf 2019 - San Jose

Creating apps, most of the time we are not worried about the content we download and display on it. If you are working on a company, the images, content, JSONs are mostly made for mobile applications and it should work nicely, right?

On this speech, Jordan talked about how images can occupy a very large space on the memory of your app, slowing its execution, spending more battery and many other non-desirable effects.

To understand a little more about it, we have to understand more about how the images are displayed on the iOS devices.

### Buffers

We have 3 main types of buffers for this case:
- Data Buffer
    - Holds any type of data
- Image Buffer
    - Per pixel information of an image, which the display hardware can render
- Frame Buffer
    - Where the information to be directed displayed is contented
    - Update the frame buffer, update the screen

### UIImage + UIImageView

While we are not using `SwiftUI`, we'll stick around with our good old `UIKit` image stuff.

When we add an `UIImage` to a `UIImageView` and makes it be displayed, what is happening?
The `UIImge` has an data buffer - which is essentially the size of your image file (This can be improved by changing the image you downloaded or improving your assets), and after add it to a `UIImageView` the image is *decoded* into and image buffer. The thing is, the image buffer has the size of the image in pixels to be displayed.

And, when it's being displayed, he information of the image buffer is sent to the frame buffer, which is basically a 1:1 representations of every pixel onscreen. Changing the frame buffer, changes what is going to be displayed onscreen.

*UIImage: has a data buffer -> decode -> UIImageView: render (image buffer)
-> iPhone: display (frame buffer)*

> - Image buffer size == image size
- Frame buffer refreshes, a lot!
- Decoding is related to the Images buffer size

So, even you have a small image file - let's say, 200kb, but the image actually has 1780 x 2048 dimension, it uses around a 13.4 Mb buffer.

[1780 x 2048 x 4 / 1024 / 1024 ~= 13.4]

### Downsampling

To reduce the size occupied by the image frame, we can downsample it - which is basically resize the image to be the size you want it to be displayed instead of keeping the original size of it.

Here's an example code for downsampling, provided by Jordan:

```swift
func downsampleImage(at URL:NSURL, maxSize:Float) -> UIImage
{
    let sourceOptions = [kCGImageSourceShouldCache:false] as CFDictionary
    let source = CGImageSourceCreateWithURL(URL as CFURL, sourceOptions)!
    let downsampleOptions = [kCGImageSourceCreateThumbnailFromImageAlways:true,
                             kCGImageSourceThumbnailMaxPixelSize:maxSize
                             kCGImageSourceShouldCacheImmediately:true,
                             kCGImageSourceCreateThumbnailWithTransform:true,
                             ] as CFDictionary

    let downsampledImage = CGImageSourceCreateThumbnailAtIndex(source, 0, downsampleOptions)!

    return UIImage(cgImage: downsampledImage)
}
```

The trick here is create a *thumbnail* of the image you want to display, before displaying it:

*Load -> thumbnail -> decode -> display*

### A few more things to consider...

- You'll probably not need to worry about all of this, if the image comes from the assets catalog of your project. The iOS, Xcode, Apple basically does a lot of sorcery to make these work more efficiently.
- Consider downsample images being downloaded for your app, and treating what you are doing with you
- Multithread concerning
    - May cause thread explosion - wtf? I have never heard of *thread explosion* before, but it seems that doing a lot of downsampling how I showed you guys before (like in a `cellForRowAt`) may cause the operation to be executed on multiple threads, and may threads that doesn't exist
    - So, plz, serialize the downsample - just use a serial queue for this 😉

#### Where to go from Here

There is a nice [session on WWDC18](https://developer.apple.com/videos/play/wwdc2018/219) about this, and of course, the material provided by jordan at: [swiftjectivec](https://www.swiftjectivec.com/optimizing-images/) website.
