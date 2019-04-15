

<h2>Minimal Sample</h2>

Graphs are constructed by creating one or more templates, with desired properties data-bound, and adding model data.

```html
<script src="go.js"></script>

<script id="code">
  function init() {
    var $ = go.GraphObject.make;  // for conciseness in defining templates

    var myDiagram = $(go.Diagram, "myDiagramDiv",  // create a Diagram for the DIV HTML element
                  {
                    // enable undo & redo
                    "undoManager.isEnabled": true
                  });

    // define a simple Node template
    myDiagram.nodeTemplate =
      $(go.Node, "Auto",  // the Shape will go around the TextBlock
        $(go.Shape, "RoundedRectangle", { strokeWidth: 0, fill: "white" },
          // Shape.fill is bound to Node.data.color
          new go.Binding("fill", "color")),
        $(go.TextBlock,
          { margin: 8 },  // some room around the text
          // TextBlock.text is bound to Node.data.key
          new go.Binding("text", "key"))
      );

    // but use the default Link template, by not setting Diagram.linkTemplate

    // create the model data that will be represented by Nodes and Links
    myDiagram.model = new go.GraphLinksModel(
    [
      { key: "Alpha", color: "lightblue" },
      { key: "Beta", color: "orange" },
      { key: "Gamma", color: "lightgreen" },
      { key: "Delta", color: "pink" }
    ],
    [
      { from: "Alpha", to: "Beta" },
      { from: "Alpha", to: "Gamma" },
      { from: "Beta", to: "Beta" },
      { from: "Gamma", to: "Delta" },
      { from: "Delta", to: "Alpha" }
    ]);
  }
</script>
```

Creates this graph. The user can now click on nodes or links to select them, copy-and-paste them, drag them, delete them, scroll, pan, and zoom, with a mouse or with fingers.

[<img width="200" height="200" src="https://gojs.net/latest/assets/images/screenshots/minimal.png">](https://gojs.net/latest/samples/minimal.html)

*Click the image to see the interactive GoJS Diagram*


<h2>Support</h2>

Northwoods Software offers a month of free developer-to-developer support for GoJS to help you get started on your project.

Read and search the official <a href="https://forum.nwoods.com/c/gojs">GoJS forum</a> for any topics related to your questions.

Posting in the forum is the fastest and most effective way of obtaining support for any GoJS related inquiries.
Please register for support at Northwoods Software's <a href="https://www.nwoods.com/products/register.html">registration form</a> before posting in the forum.

For any nontechnical questions about GoJS, such as about sales or licensing,
please visit Northwoods Software's <a href="https://www.nwoods.com/contact.html">contact form</a>.


<h2>License</h2>

The GoJS <a href="https://gojs.net/latest/license.html">software license</a>.


Copyright (c) Northwoods Software Corporation
