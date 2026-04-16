
### As we saw in _Figure 3.3_, the Fyne canvas is also capable of displaying gradients. Much like the raster in the previous section, a gradient will display using all the available pixels for the best output possible for each device. Adding a gradient, however, is much simpler than managing raster content.

There are two types of gradient: linear and radial.

### Linear gradient

A `LinearGradient` displays an even transition from one color to another and is normally presented as horizontal or vertical. A vertical gradient changes color from the start at the top of the area and the end color at the bottom; each row of pixels will have the same color, creating a gradient area that transitions from top to bottom. A horizontal gradient performs the same operation, but with the start color at the left of the area and the end at the right, which means that each column of pixels will have the same color.

For example, the following lines would create a horizontal vertical gradient respectively from white to black using the provided convenience constructors:

```markup
canvas.NewHorizontalGradient(color.White, color.Black)
canvas.NewVerticalGradient(color.White, color.Black)
```

By passing each of these to `Window.SetContent`, as we have done with other examples, you can see the following result, with a horizontal gradient on the left and a vertical gradient on the right:

![Figure 3.9 – Horizontal and vertical gradients](https://static.packt-cdn.com/products/9781800563162/graphics/image/Figure_3.19_B16820.jpg)

Figure 3.9 – Horizontal and vertical gradients

It is also possible to specify the exact angle of a linear gradient. The `NewLinearGradient` constructor takes a third parameter, the angle in degrees to orient. The vertical gradient is at `0` degrees and the horizontal is `270` degrees (equivalent to a `90`-degree counter-clockwise rotation). So, the usage of the horizontal gradient helper could also have been written as follows:

```markup
canvas.NewLinearGradient(color.White, color.Black, 270)
```

Sometimes, however, a gradient that forms a curve is required; for this, we use a radial gradient.

### Radial gradient

A radial gradient is one where the start color is at the center of the area (although this can be offset using `CenterOffsetX` and `CenterOffsetY`) and progresses to the end color at the edge of the area. The gradient is drawn such that the end color fully appears within the bounds on the horizontal and vertical lines from the center of the gradient. This means that the corners of the area this gradient occupies will be outside of the gradient calculation; for this reason, it can be useful for the end color to be `color.Transparent`. We set up a gradient similar to the `LinearGradient` example from white to black, as follows:

```markup
canvas.NewRadialGradient(color.White, color.Black)
```

This code will result in the following image when placed in the contents of a window:

![Figure 3.10 – A radial gradient transitioning from white to black](https://static.packt-cdn.com/products/9781800563162/graphics/image/Figure_3.21_B16820.jpg)

Figure 3.10 – A radial gradient transitioning from white to black

We have seen the various ways that we can output content, but it is also possible to animate it so that your application appears more interactive. We will see how to do this in the next section.

Animation of the draw properties

The Fyne `canvas` package also includes the facility to handle the animation of objects and properties. Using these APIs will help you to manage smooth transitions for a better user experience.