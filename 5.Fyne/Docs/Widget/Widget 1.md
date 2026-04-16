A `fyne.Widget` is a special type of canvas object that has interactive elements associated with it. In widgets the logic is separate from the way that it looks (also called the `WidgetRenderer`).

Widgets are also types of `CanvasObject` and so we can set the content of our window to a single widget. See how we create a new `widget.Entry` and set it as the content of the window in this example.

```
package main

import (
	"fyne.io/fyne/v2/app"
	"fyne.io/fyne/v2/widget"
)

func main() {
	myApp := app.New()
	myWindow := myApp.NewWindow("Widget")

	myWindow.SetContent(widget.NewEntry())
	myWindow.ShowAndRun()
}
```