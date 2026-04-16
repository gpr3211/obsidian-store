
# Implementing a simple game

In the first example application of this book, we will see how the canvas elements come together by building the graphical elements of a _snake game_ (for a history of this game, see the Wikipedia entry at [https://en.wikipedia.org/wiki/Snake_(video_game_genre)](https://en.wikipedia.org/wiki/Snake_(video_game_genre)). The main element of this game is a snake character that the user will control as it moves around the screen. We will build the snake from a row of rectangles and add animation elements to bring it to life. Let's start by drawing the initial screen.

Drawing a snake on screen

To start the work of displaying the game canvas, we will see create a simple snake that consists of a row of 10 green squares. Let's begin:

1. Firstly, we will create a setup function that will build the game screen. We will call this function `setupGame` and create an empty list that we will populate. The return from this method is a container with no layout so that we can later use a manual layout for the visual elements:
    
    ```markup
    func setupGame() *fyne.Container {
         var segments []fyne.CanvasObject
         ...
         return container.NewWithoutLayout(segments...)
    }
    ```
    

To set up the graphical elements, we will iterate through a loop of 10 elements (`i` from `0` to `9`) and make a new `Rectangle` for each position. The elements are all created 10 x 10 in size and placed one above the other using the `Move` function. We will add them all to the segment slice created previously. This completes our setup code:

```markup
    for i := 0; i < 10; i++ {
         r := canvas.NewRectangle(&color.RGBA{G: 0x66, 
             A: 0xff})
         r.Resize(fyne.NewSize(10, 10))
         r.Move(fyne.NewPos(90, float32(50+i*10)))
         segments = append(segments, r)
    }
```

- The preceding color specification is using a hexadecimal format, where `0xff` is the maximum for a channel and a missing channel (like red and blue in this code) defaults to `0`. The result is a green of medium brightness.
    
- With the graphical setup code, we can wrap this in the usual application load code, this time passing the result of `setupGame()` to the `SetContent` function. As this game will not have dynamic sizing, we will call `SetFixedSize(true)` so that the window cannot be resized:
    
    ```markup
    func main() {
        a := app.New()
        w := a.NewWindow("Snake")
        w.SetContent(setupGame())
        w.Resize(fyne.NewSize(200, 200))
        w.SetFixedSize(true)
        w.ShowAndRun()
    }
    ```
    
Now we can build and run the code in the usual way:

```markup
Chapter03/example$ go run main.go
```

1. You will see the following result:

![Figure 3.11 – A simple snake drawn in the window](https://static.packt-cdn.com/products/9781800563162/graphics/image/Figure_3.23_B16820.jpg)

Figure 3.11 – A simple snake drawn in the window

Next, we will bring the snake to life with some simple movement code.

## Adding a timer to move the snake

The next step is to add some motion to the game. We will start with a simple timer that repositions the snake on screen:

1. To help manage the game state, we will define a new type to store the `x`, `y` value of each snake segment, named `snakePart`. We then make a slice that contains all of the elements, and this is what we will update as the snake moves around the screen. We will also define the game variable that we will use to refresh the screen when needed:
    
    ```markup
    type snakePart struct {
        x, y float32
    }
    var (
        snakeParts []snakePart
        game       *fyne.Container
    )
    ```
    

Inside `setupGame`, we need to create the representation of snake segments, one for each of the rectangles we created before. Adding the following lines to the loop will set up the state:

```markup
        seg := snakePart{9, float32(5 + i)}
        snakeParts = append(snakeParts, seg)
```

To make sure that the game refreshes each time we move the snake, we need to move the rectangles and call `Refresh()`. We create a new function that will update the rectangles that we created earlier based on updated snake section information. We call this `refreshGame()`:

```markup
func refreshGame() {
    for i, seg := range snakeParts {
        rect := game.Objects[i]
        rect.Move(fyne.NewPos(seg.x*10, seg.y*10))
    }
    game.Refresh()
}
```

To run the main game loop, we need one more function that will use a timer to move the snake. We call this function `runGame`. This code waits 250 milliseconds and then moves the snake forward. To move it, we copy the position of each element from that of the element that is one segment further forward, working from the tail to the head. Lastly, the code moves the head to a new position, in this case further up the screen (by using `snakeParts[0].y--`). Refer to the following function:

```markup
func runGame() {
    for {
        time.Sleep(time.Millisecond * 250)
        for i := len(snakeParts) - 1; i >= 1; i-- {
            snakeParts[i] = snakeParts[i-1]
        }
        snakeParts[0].y--
        refreshGame()
    }
}
```

To start the game timer, we need to update the `main()` function. It must assign the `game` variable so that we can refresh it later, and it will start a new goroutine executing the `runGame` code. We do this by changing the `SetContent` and `ShowAndRun` calls to read as follows:

```markup
    game = setupGame()
    w.SetContent(game)
    go runGame()
    w.ShowAndRun()
```

Running the updated code will initially show the same screen, but the green shape will then move up the screen until it leaves the window:

```markup
Chapter03/example$ go run main.go
```

With the draw and basic movement code in place, we want to be able to control the game, which we will look at next.

## Using keys to control direction

To control the snake's direction, we will need to handle some keyboard events. Unfortunately, this will be specific to desktop or mobile devices with a hardware keyboard; to add touchscreen controls would require using widgets (such as a button) that we will not explore until [_Chapter 5_](https://subscription.packtpub.com/book/programming/9781800563162/7)_, Widget Library and Themes_:

1. To start with, we define a new type (`moveType`) that will be used to describe the next direction in which to move. We use the Go built-in instruction `iota`, which is similar to `enum` in other languages. The `move` variable is then defined to hold the next move direction:
    
    ```markup
    type moveType int
    const (
        moveUp moveType = iota
        moveDown
        moveLeft
        moveRight
    )
    var move = moveUp
    ```
    

Next, we will translate from key events to the movement type that we just defined. Create a new `keyTyped` function as follows that will perform the keyboard mapping:

```markup
func keyTyped(e *fyne.KeyEvent) {
    switch e.Name {
    case fyne.KeyUp:
        move = moveUp
    case fyne.KeyDown:
        move = moveDown
    case fyne.KeyLeft:
        move = moveLeft
    case fyne.KeyRight:
        move = moveRight
    }
}
```

For the key events to be triggered, we must specify that this key handler should be used for the current window. We do this using the `SetOnKeyTyped()` function on the window's canvas:

```markup
w.Canvas().SetOnTypedKey(keyTyped)
```

To make the snake move according to these events, we need to update the `runGame()` function to apply the correct movement. In place of the line `snakeParts[0].y--` (just before `refreshGame()`), we add the following code that will position the head for each new move:

```markup
        switch move {
        case moveUp:
            snakeParts[0].y--
        case moveDown:
            snakeParts[0].y++
        case moveLeft:
            snakeParts[0].x--
        case moveRight:
            snakeParts[0].x++
        }
        refreshGame()
```

We can now run the updated code sample to test the keyboard handling:

```markup
Chapter03/example$ go run main.go
```

1. After the app loads and you press the left and then down keys, you should see something like the following:

![Figure 3.12 – The snake can move around the screen](https://static.packt-cdn.com/products/9781800563162/graphics/image/Figure_3.25_B16820.jpg)

Figure 3.12 – The snake can move around the screen

Although this is now technically an animated game, we can make it smoother. Using the animation API will allow us to draw smoother movements.

## Animating the movement

The motion created by our run loop in the previous section provides the motion that the game is based on, but it is not very smooth. In this last section, we will improve the motion by using the animation API. We will create a new rectangle for the head segment that will move ahead of the snake animation and also move the tail to its new position smoothly. The rest of the elements can remain fixed. Let's see how this is done:

1. We first define a new rectangle that represents the moving head segment:
    
    ```markup
    var head *canvas.Rectangle
    ```
    

We set this up by adding the following code to the `setupGame` function:

```markup
    head = canvas.NewRectangle(&color.RGBA{G: 0x66, A: 
        0xff})
    head.Resize(fyne.NewSize(10, 10))
    head.Move(fyne.NewPos(snakeParts[0].x*10,
        snakeParts[0].y*10))
    segments = append(segments, head)
```

To start drawing the head in front of the current body position, we add the following code to the top of the `runGame` function so that the next segment is calculated in the movement before the snake reaches that position:

```markup
    nextPart := snakePart{snakeParts[0].x, 
        snakeParts[0].y - 1}
```

We set up our animations inside the `for` loop in the `runGame` function, before the timer pause. Firstly, we calculate where the head is and then its new position and set up a new animation to make that transition:

```markup
    oldPos := fyne.NewPos(snakeParts[0].x*10,
        snakeParts[0].y*10)
    newPos := fyne.NewPos(nextPart.x*10, next
        Part.y*10)
    canvas.NewPositionAnimation(oldPos, newPos,
        time.Millisecond*250, func(p fyne.Position) {
        head.Move(p)
        canvas.Refresh(head)
    }).Start()
```

We also create another animation to transition the tail to its new position, as follows:

```markup
    end := len(snakeParts) - 1
    canvas.NewPositionAnimation(
        fyne.NewPos(snakeParts[end].x*10,
            snakeParts[end].y*10),
        fyne.NewPos(snakeParts[end-1].x*10,
            snakeParts[end-1].y*10),
        time.Millisecond*250,
        func(p fyne.Position) {
            tail := game.Objects[end]
            tail.Move(p)
            canvas.Refresh(tail)
        }).Start()
```

After the `time.Sleep` in our game loop, we need to use the new `nextPart` variable to set up the new head position, as follows:

```markup
    snakeParts[0] = nextPart
    refreshGame()
```

After the refresh line, we need to update the movement calculations to set up `nextPart` ready for the next movement:

```Go
    switch move {
    case moveUp:
        nextPart = snakePart{nextPart.x, 
            nextPart.y - 1}
    case moveDown:
        nextPart = snakePart{nextPart.x, 
            nextPart.y + 1}
    case moveLeft:
        nextPart = snakePart{nextPart.x - 1, 
            nextPart.y}
    case moveRight:
        nextPart = snakePart{nextPart.x + 1, 
            nextPart.y}
    }
```

Run the updated code and you will see this behavior, but with a smooth transition along the path:

```markup
Chapter03/example$ go run main.go
```

For the complete code for this example, you can use the book's GitHub repository at [https://github.com/PacktPublishing/Building-Cross-Platform-GUI-Applications-with-Fyne/tree/master/Chapter03/example](https://github.com/PacktPublishing/Building-Cross-Platform-GUI-Applications-with-Fyne/tree/master/Chapter03/example).

Although many more features could be added to this example, we have explored all the application and canvas operations required to build a full game.

Summary

In this chapter, we started our journey with the Fyne toolkit, exploring how it is organized and how a Fyne application operates. We saw how it uses vector graphics to create a high-quality output at any resolution, allowing it to scale well over desktop computers, smart phones, and more.

We explored the features of the canvas package and saw how it can be used to draw individual elements to the screen. By combining these graphical primitives using `fyne.Container`, we were able to draw more complex output to our window. We also saw how the animation APIs can be used to display transitions of an object's size, position, and other properties.

To bring this knowledge together, we built a small snake game that displayed elements to the screen and animated them based on user input. Although we could add many more features and graphical polish to this game, we will move on to other topics.

In the next chapter, we will explore how layout algorithms can manage the contents of a window and the best practices of file handling that are needed to build an image browser application.