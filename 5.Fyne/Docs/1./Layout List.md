# Layout List

## Standard Layouts

---

### Horizontal Box (HBox)

Horizontal Box arranges items in a horizontal row. Every element will have the same height (the height of the tallest item in the container) and objects will be left-aligned at their minimum width.

![](https://docs.fyne.io/images/layouts/hbox-light.png)

### Vertical Box (VBox)

Vertical Box arranges items in a vertical column. Every element will have the same width (the width of the widest item in the container) and objects will be top-aligned at their minimum height.

![](https://docs.fyne.io/images/layouts/vbox-light.png)

### Center

Center layout positions all container elements in the center of the container. Every object will be set to it’s minimum size.

![](https://docs.fyne.io/images/layouts/center-light.png)

### Form

Form layout arranges items in pairs where the first column is at minimum width. This is normally useful for labelling elements in a form, where the label is in the first column and the item it describes is in the second. You should always add an even number of elements to a form layout.

![](https://docs.fyne.io/images/layouts/form-light.png)

### Grid

Grid layout arranges items equally in the available space. A number of columns is specified, with objects being positioned horizontally until the number of columns is reached at which point a new row is started. All objects have the same size, that is width divided by column total and the height will be total height divided by the number of rows required. Minus padding.

![](https://docs.fyne.io/images/layouts/grid-light.png)

### GridWrap

GridWrap layout arranges all items to flow along a row, wrapping to a new row if there is insufficient space. All objects will be set to the same size, which is the size passed to the layout. This layout may not respect item MinSize to manage this uniform layout. Often used in file managers or image thumbnail lists.

![](https://docs.fyne.io/images/layouts/gridwrap-light.png)

### Border

Border layout supports positioning of items at the outside of available space. The border is passed pointers to the objects for (top, left, bottom, right). All items in the container that are not positioned on a border will fill the remaining space.

![](https://docs.fyne.io/images/layouts/border-light.png)

### Stack (was Max)

Stack layout positions all container elements to fill the available space. The objects will all be full-sized and drawn in the order they were added to the container (last-most is on top).

![](https://docs.fyne.io/images/layouts/max-light.png)

### Padded

Padded layout positions all container elements to fill the available space but with a small padding around the outside. The size of the padding is theme specific. The objects will all be drawn in the order they were added to the container (last-most is on top).

![](https://docs.fyne.io/images/layouts/padded-light.png)

You can also specify each site separately using `CustomPadded` layout.

![](https://docs.fyne.io/images/layouts/custompadded-light.png)

## Combining Layouts

It is possible to build up more complex application structures by using multiple layouts. Multiple containers that each have their own layout can be nested to create complete user interface arrangements using only the standard layouts listed above. For example a horizontal box for a header, a vertical box for a left side file panel and a grid wrap layout in the content area - all inside a container using a border layout can build the result illustrated below.

![](https://docs.fyne.io/images/layouts/combined-light.png)