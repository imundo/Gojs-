<h2>Agregar dependencia a tu Index.html</h2>

        <!-- AngularJS -->
         <script src="bower_components/gojs/release/go.js"></script>
    
    
<h2>Agregar como directiva</h2>

        .directive('goDiagram', function() {
        return {
            restrict: 'E',
            template: '<div></div>',  // just an empty DIV element
            replace: true,
            scope: { model: '=goModel' },
            link: function(scope, element, attrs) {

                if (window.goSamples) goSamples(); // init for these samples -- you don't need to call this

                var $ = go.GraphObject.make;
                var diagram =  // create a Diagram for the given HTML DIV element
                    $(go.Diagram, element[0],
                        {
      
                            nodeTemplate: $(go.Node, "Auto", 
                                new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
                                $(go.Shape, "RoundedRectangle", new go.Binding("fill", "color"),
                                    {
                                        portId: "", cursor: "pointer", strokeWidth: 0,
                                        fromLinkable: false, toLinkable: false,
                                        fromLinkableSelfNode: false, toLinkableSelfNode: false,
                                        fromLinkableDuplicates: false, toLinkableDuplicates: false
                                    }),
                                $(go.TextBlock, { margin: 8, editable: false, alignment: go.Spot.Center },
                                    new go.Binding("text", "name").makeTwoWay())
                            ),
                            linkTemplate: $(go.Link,
                                { relinkableFrom: true, relinkableTo: true },
                                $(go.Shape),
                                $(go.Shape, { toArrow: "Standard", stroke: null, strokeWidth: 0 })
                            ),
                            initialContentAlignment: go.Spot.Center,
                            initialDocumentSpot : go.Spot.Center, 
                            initialViewportSpot: go.Spot.Center,
                            "ModelChanged": updateAngular,
                            "ChangedSelection": updateSelection,
                            "undoManager.isEnabled": false
                        });
                          
        //              diagram.initialContentAlignment = go.Spot.Center;
                       diagram.requestUpdate(); // Needed!
                function updateAngular(e) {
                    if (e.isTransactionFinished) {
                        scope.$apply();
                    }
                }

                // update the Angular model when the Diagram.selection changes
                function updateSelection(e) {
                    diagram.model.selectedNodeData = null;
                    var it = diagram.selection.iterator;
                    while (it.next()) {
                        var selnode = it.value;
                        // ignore a selected link or a deleted node
                        if (selnode instanceof go.Node && selnode.data !== null) {
                            diagram.model.selectedNodeData = selnode.data;
                            break;
                        }
                    }
                    scope.$apply();
                }

                // notice when the value of "model" changes: update the Diagram.model
                scope.$watch("model", function(newmodel) {
                    var oldmodel = diagram.model;
                    if (oldmodel !== newmodel) {
                        diagram.removeDiagramListener("ChangedSelection", updateSelection);
                        diagram.model = newmodel;
                        diagram.addDiagramListener("ChangedSelection", updateSelection);
                    }
                });

                scope.$watch("model.selectedNodeData.name", function(newname) {
                    if (!diagram.model.selectedNodeData) return;
                    // disable recursive updates
                    diagram.removeModelChangedListener(updateAngular);
                    // change the name
                    diagram.startTransaction("change name");
                    // the data property has already been modified, so setDataProperty would have no effect
                    var node = diagram.findNodeForData(diagram.model.selectedNodeData);
                    if (node !== null) node.updateTargetBindings("name");
                    diagram.commitTransaction("change name");
                    // re-enable normal updates
                    diagram.addModelChangedListener(updateAngular);
                    diagram.initialContentAlignment = go.Spot.Center;

                });
            }
        };
    })


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

