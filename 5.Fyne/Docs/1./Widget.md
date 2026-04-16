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
# Widget List

## Standard Widgets (in `widget` package)

---

### Accordion

Accordion displays a list of AccordionItems. Each item is represented by a button that reveals a detailed view when tapped.

![](https://docs.fyne.io/images/widgets/accordion-light.png)

### Activity

Display an animated activity indicator.

![](https://docs.fyne.io/images/widgets/activity-light.png)

### Button

[Button](https://docs.fyne.io/widget/button) widget has a text label and icon, both are optional.

![](https://docs.fyne.io/images/widgets/button-light.png)

### Card

Card widget groups elements with a header and subheader, all are optional.

![](https://docs.fyne.io/images/widgets/card-light.png)

### Check

Check widget has a text label and a checked (or unchecked) icon.

![](https://docs.fyne.io/images/widgets/check-light.png)

### Entry

[Entry](https://docs.fyne.io/widget/entry) widget allows simple text to be input when focused.

![](https://docs.fyne.io/images/widgets/entry-light.png)

![](https://docs.fyne.io/images/widgets/entry-valid-light.png)

![](https://docs.fyne.io/images/widgets/entry-invalid-light.png)

PasswordEntry widget hides text input and adds a button to display the text.

![](https://docs.fyne.io/images/widgets/password-light.png)

### FileIcon

FileIcon provides helpful standard icons for various types of file. It displays the type of file as an indicator icon and shows the extension of the file type.

![](https://docs.fyne.io/images/widgets/fileicon-light.png)

### Form

[Form](https://docs.fyne.io/widget/form) widget is two column grid where each row has a label and a widget (usually an input). The last row of the grid will contain the appropriate form control buttons if any should be shown.

![](https://docs.fyne.io/images/widgets/form-light.png)

### Hyperlink

Hyperlink widget is a text component with appropriate padding and layout. When clicked, the URL opens in your default web browser.

![](https://docs.fyne.io/images/widgets/hyperlink-light.png)

### Icon

Icon widget is a basic image component that load’s its resource to match the theme.

![](https://docs.fyne.io/images/widgets/icon-light.png)

### Label

[Label](https://docs.fyne.io/widget/label) widget is a label component with appropriate padding and layout.

![](https://docs.fyne.io/images/widgets/label-light.png)

### Progress bar

[ProgressBar](https://docs.fyne.io/widget/progressbar) widget creates a horizontal panel that indicates progress.

![](https://docs.fyne.io/images/widgets/progress-light.png)

ProgressBarInfinite widget creates a horizontal panel that indicates waiting indefinitely An infinite progress bar loops 0% -> 100% repeatedly until Stop() is called.

![](https://docs.fyne.io/images/widgets/progressinf-light.png)

### RadioGroup

RadioGroup widget has a list of text labels and radio check icons next to each.

![](https://docs.fyne.io/images/widgets/radiogroup-light.png)

### RichText

RichText widget is a text component that shows various styles and embedded objects. It supports markdown parsing to construct the widget with ease.

![](https://docs.fyne.io/images/widgets/richtext-light.png)

### Select

Select widget has a list of options, with the current one shown, and triggers an event function when clicked.

![](https://docs.fyne.io/images/widgets/select-light.png)

### SelectEntry

Select entry widget adds an editable component to the select widget. Users can select an option or enter their own value.

![](https://docs.fyne.io/images/widgets/selectentry-light.png)

### Separator

Separator widget shows a dividing line between other elements.

![](https://docs.fyne.io/images/widgets/separator-light.png)

### Slider

Slider if a widget that can slide between two fixed values.

![](https://docs.fyne.io/images/widgets/slider-light.png)

### TextGrid

TextGrid is a monospaced grid of characters. This is designed to be used by a text editor, code preview or terminal emulator.

![](https://docs.fyne.io/images/widgets/textgrid-light.png)

### Toolbar

[Toolbar](https://docs.fyne.io/widget/toolbar) widget creates a horizontal list of tool buttons.

![](https://docs.fyne.io/images/widgets/toolbar-light.png)

## Collection Widgets (in `widget` package)

Collection widgets provide advanced caching functionality to provide high performance rendering of massive data. This does lead to a more complex constructor, but is a good balance for the outcome it enables. Each of these widgets uses a series of callbacks, the minimum set is defined by their constructor function, which includes the data size, the creation of template items that can be re-used and finally the function that applies data to a widget as it is about to be added to the display.

### List

[List](https://docs.fyne.io/collection/list) provides a high performance vertical scroll of many sub-items.

![](https://docs.fyne.io/images/widgets/list-light.png)

### Table

[Table](https://docs.fyne.io/collection/table) provides a high performance scrolled two dimensional display of many sub-items.

![](https://docs.fyne.io/images/widgets/table-light.png)

### Tree

[Tree](https://docs.fyne.io/collection/tree) provides a high performance vertical scroll of items that can be expanded to reveal child elements..

![](https://docs.fyne.io/images/widgets/tree-light.png)

### GridWrap

GridWrap is a collection widget that display each child item at the same size and wraps them to new lines as required.

![](https://docs.fyne.io/images/widgets/gridwrap-light.png)

## Container Widgets (in `container` package)

Container widgets are like regular containers but they provide some additional functionality.

### AppTabs

[AppTabs](https://docs.fyne.io/container/apptabs) widget allows switching visible content from a list of TabItems. Each item is represented by a button at the top of the widget.

![](https://docs.fyne.io/images/widgets/apptabs-light.png)

### Scroll

ScrollContainer defines a container that is smaller than the Content.

![](https://docs.fyne.io/images/widgets/scroll-light.png)

### Split

SplitContainer defines a container whose size is split between two children.

![](https://docs.fyne.io/images/widgets/split-light.png)