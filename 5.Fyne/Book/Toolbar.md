f there are lots of regularly accessed features in an application, the `Toolbar` widget can be an efficient way to present these options. The main elements of a toolbar are the `ToolbarAction` items, which are simple icons that, when tapped, execute a function parameter that’s been passed to `NewToolbarAction`. To group action elements, you can use `ToolbarSeparator`, which creates a visual divider between the item on its left and right. Additionally, a gap can be created between actions using the `ToolbarSpacer` type. This will expand, causing the elements after it to be right aligned. Using one spacer will show items before it on the left and items after it on the right. Using two will mean that the elements between the spacers will be central in the toolbar.

To construct a toolbar containing four action elements and a separator, we can use the following code:

```markup
widget.NewToolbar(
     widget.NewToolbarAction(theme.MailComposeIcon(),
         func() {}),
     widget.NewToolbarSeparator(),
     widget.NewToolbarSpacer(),
     widget.NewToolbarAction(theme.ContentCutIcon(),
         func() {}),
     widget.NewToolbarAction(theme.ContentCopyIcon(),
         func() {}),
     widget.NewToolbarAction(theme.ContentPasteIcon(),
         func() {}),
)
```

The preceding code snippet results in the following. The following image shows it in both the light and dark themes:

![Figure 5.20 – Toolbar widget with some possible icons in the light and dark themes](https://static.packt-cdn.com/products/9781800563162/graphics/image/Figure_5.20_B16820.jpg)

Figure 5.20 – Toolbar widget with some possible icons in the light and dark themes

The widgets that we have explored so far are fairly standard and can be created with simple constructors, or through initializing the struct directly. Some of these take a callback function that can be used to inform us when an action has occurred.

In the next section, we’ll look at some more complex widgets that are designed to manage thousands of sub-widgets. To do so, we will learn how they make use of more function parameters to query large datasets and efficiently display a subset of the data.