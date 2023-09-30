---
layout: post
title: "Drawing the Swift logo bird in SwiftUI"
categories: [iOS Development, SwiftUI, Swift]
tags: [SwiftUI, Swift, Path, Shape]
---
In this post we'll learn how to draw the Swift logo bird in SwiftUI using Shape and Path. I decided to get the official logo than simply Googling for an image since you find so many variations online. If you go to the [resources section](https://developer.apple.com/swift/resources/) on Apple's developer website you'll find a .zip package at the bottom of the page that you can download along with guidelines. The guidelines are especially important if you plan to use the logo in your application.

I began by creating a SwiftUI project and importing the image, then displaying it as resizable inside a 200x200pt Image view. For the next step I identified the points of interest that I'd need to draw curves from in order to draw the bird(see image below). I identified a total of 11 such points marked from A-K. Notice that I decided to break down the lower wing curve into 3 points for better handling when drawing. You could probably do it with fewer points. Now that the points were identified, I needed to get the position of the points within the 200x200pt grid. I did this by overlaying a Grid with GridRow(s) of Rectangles of 50x50pt each with a 1pt stroke border, giving me a 4x4 grid that would make it easy to approximate the positions. Note that I say approximate because the positions of the points are not entirely accurate and are only approximations since I wanted to do this quickly.

![image](/assets/images/post5/SwiftLogoDrawing.png)

From here it defined a `struct SwiftBird` which would be a `Shape` that would make it easier to use anywhere in other SwiftUI views. Finally I implemented the required path function that returns a path of the drawing. I start by moving to point A which is the upper wing tip and then progress by [adding quadratic curves](https://developer.apple.com/documentation/uikit/uibezierpath/1624351-addquadcurve) with control points in a clockwise fashion with the `addQuadCurve` method. This adds quadratic Bezier curves between two points using a control point that allows us to control the curvature.

![image](/assets/images/post5/quad-bezier-curve.jpeg)

Think of the control point as stretching a rubber band between 2 fingers and pulling on one strand to elongate it. Notice how the shape changes when you pull the strand in different directions. Depending on how high or low you are pulling on the strand, the shape and curvature of the band changes giving you different results.

The code for the SwiftBird shape is as follows:

<script src="https://gist.github.com/anupdsouza/d0a0deae3ae029fb147f05b31d90bc70.js"></script>

Note that the fractional values that you see are positions of the curve points (A-K) as a fraction of the width when placed in a 200x200pt container. So for example, `path.move(to: CGPoint(x: 0.565, y: 0.145))` in the code above simply means move to `CGPoint(x: 0.565 * 200, y: 0.145 * 200)` i.e. `CGPoint(x: 113, y: 29)` along the canvas. This makes it easier to draw the shape given any square frame container.

Finally I add the SwiftBird shape to a SwiftUI view like so:
```
SwiftBird()
   .fill(Color.white)
   .frame(width: 100, height: 100)
```

And that's it! I've added some interesting effects to the shapes such as animating the paths which you can view [here](https://www.youtube.com/watch?v=wtUraksUcsQ).The complete code can be found [here.](https://github.com/anupdsouza/ios-swift-logo-drawing) Using the stoke path I also created an animated constellation scene that you can view in my project [here](https://github.com/anupdsouza/ios-swift-constellation). 

![image](/assets/images/post5/swiftconstellation.png)

Share this article if you found it useful !


