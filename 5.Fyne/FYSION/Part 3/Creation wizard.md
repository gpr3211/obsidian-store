internal/dialogs/wizard.go

```Go
wizard := NewWizard("My Wizard", firstContent) wizard.Show(window) 
// Later, to push new content: 
wizard.Push("Second Page", secondContent)
```

