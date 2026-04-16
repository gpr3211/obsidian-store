An animation in Fyne, at its very basic level, is a function that will be called for every graphical frame that it runs. Once it is started, it will run as long as the `Duration` field specifies. A basic animation can be created using the following:

```markup
anim := fyne.NewAnimation(time.Duration, func(float32))
```

The animation that is returned from this constructor function can be started by calling `anim.Start()`. When the animation is started, its end time will be calculated based on the duration of time that passes. The callback that is passed in will be executed each time the graphics are updated. The `float32` parameter to this function will be `0.0` when it starts and `1.0` immediately before it ends; each intermediate call will have a value between these two.

To provide a more concrete illustration, we can set up a position animation. This is one of the helpful animations provided by the `canvas` package. It, like many others, takes two additional parameters: the `start` and `end` value of the animation. In this case, it expects a `start` and `end` `fyne.Position`. Note that that the `callback` function will provide the current position value instead of a `float32` _offset_ parameter. We create a new position animation that will run for one second:

```markup
start := fyne.NewPos(10, 10)
end := fyne.NewPos(90, 10)
anim := canvas.NewPositionAnimation(start, end,
    time.Second, callback)
```

The `callback` function is responsible for applying the position value to a graphical object. In this case, we will create a text object that will move across the window:

```markup
text := canvas.NewText("Hi", color.Black)
callback := func(p fyne.Position) {
    text.Move(p)
    canvas.Refresh(text)
}
```

We then simply start this animation using the `Start()` method:

```markup
anim.Start()
```

These animations will run just once, but they can also be asked to loop.