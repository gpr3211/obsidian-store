Although the `Label` widget (described earlier) does provide some text formatting, there are some applications that require styles to be applied per-character. For syntax highlighting in a code editor or for showing error rows indicated on a console output, there is the `TextGrid` widget.

Inside a `TextGrid` widget, the content is split into each rune of the string representation, and each rune has a `TextGridStyle` applied to it. This style allows the foreground and background color to be specified for each character or cell of the grid. Additionally, each row of the grid can have a style specified. This style will be used for any cell that does not have its own style specified. If a cell has a character and a row style available, the two will be combined so that the foreground color that’s been set on the cell will adopt a background color from the row; that is, unless the character style specifies both.

Despite the styles that allow specific colors to be set, there are a number of semantic style definitions that allow code to annotate intent instead of absolute colors. One of the most commonly used styles is `TextGridStyleWhitespace` that uses the theme definition to show characters in a muted color. Using the built-in styles, a developer can delegate to the current theme to define colors for each intent.

The `TextGrid` widget also provides common functionality for technical text displays, including `ShowLineNumbers`, which displays the line number at the beginning of each row. Also, `ShowWhitespace` can be set to true for a visual indicator of otherwise invisible spacing characters such as tab, space, and newline. The following code example illustrates some of the ways you can control text in a `TextGrid`:

```markup
grid := widget.NewTextGridFromString(
    "TextGrid\n  Content  ")
grid.SetStyleRange(0, 4, 0, 7,
    &widget.CustomTextGridStyle{BGColor:
        &color.NRGBA{R: 64, G: 64, B: 192, A: 128}})
grid.Rows[1].Style = &widget.CustomTextGridStyle{BGColor:
        &color.NRGBA{R: 64, G: 192, B: 64, A: 128}}
grid.ShowLineNumbers = true
grid.ShowWhitespace = true
```

The following image shows the result of the preceding code. Here, we can see that a background style has been applied to all the cells used in the 4 letters of _Grid_ and that a row style has been applied to the second row (index 1). They also have line numbers and the whitespace options turned on:

![Figure 5.19 – Styled content presented using TextGrid in the light and dark themes](https://static.packt-cdn.com/products/9781800563162/graphics/image/Figure_5.19_B16820.jpg)

Figure 5.19 – Styled content presented using TextGrid in the light and dark themes

The style for a cell can be assigned using the `SetStyle` method. However, when the style needs to be applied to many runes, developers can use the more efficient `SetStyleRange` utility method. The `SetRowStyle` method can assist in setting row styles, as shown in the previous image.