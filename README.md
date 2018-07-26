# Semantic Segmentation Editor

A web based labeling editor dedicated to the creation of training data for machine learning.
The tool has been developed in the context of autonomous driving research.
It supports images (.jpg and .png files) and point clouds (.pcd files).
It is a [Meteor](http://www.meteor.com) app developed with [React](http://reactjs.org),
[Paper.js](http://paperjs.org/) and [three.js](https://threejs.org/).

Bitmap Image Editor

<a href="https://github.com/dmandrioli/sse-extra/raw/master/Capture2D1.PNG"><img width="400" src="https://github.com/dmandrioli/sse-extra/raw/master/Capture2D1.jpg"/></a>
<a href="https://github.com/dmandrioli/sse-extra/raw/master/Capture2D2.PNG"><img width="400" src="https://github.com/dmandrioli/sse-extra/raw/master/Capture2D2.jpg"/></a>

(click images to zoom)

PCD Point Cloud Editor

<a href="https://github.com/dmandrioli/sse-extra/raw/master/Capture3D1.PNG"><img width="400" src="https://github.com/dmandrioli/sse-extra/raw/master/Capture3D1.jpg"/></a>
<a href="https://github.com/dmandrioli/sse-extra/raw/master/Capture3D2.PNG"><img width="400" src="https://github.com/dmandrioli/sse-extra/raw/master/Capture3D2.jpg"/></a>

(click images to zoom)

## How to run

```shell
git clone https://github.com/Hitachi-Automotive-And-Industry-Lab/semantic-segmentation-editor
cd semantic-segmentation-editor
```
__Change 'input-folder' and 'output-folder' according to your needs in config.json__
```
meteor npm install
meteor
```

Like any Meteor app, the editor will run by default on `http://localhost:3000` (-p to change the port)

Check [Meteor Environment Variables](https://docs.meteor.com/environment-variables.html) to configure your app
(`MONGO_URL`, `DISABLE_WEBSOCKETS`, etc...)


## Configuration File: config.json
Modifying this file let's you configure where are stored data of the app as well as
the sets of classes available in the tool.
```
{
  "configuration": {
    "input-folder": "/mnt/images", // The root folder containing images and PCD files
    "output-folder": "/mnt/pointcloud" // Segmentation data (only 3D) will be stored in this folder
  },
  // The different sets of classes available in the tool
  // For object classes, only the 'label' field is mandatory
  // The icon field can be set with an icon from the mdi-material-ui package
  "sets-of-classes": [
    {
      "name": "Cityscapes", "objects": [
      {"label": "VOID", "color": "#CFCFCF"},
      {"label": "Road", "color": "#804080", "icon": "Road"},
      {"label": "Sidewalk", "color": "#F423E8", "icon": "NaturePeople"},
      {"label": "Parking", "color": "#FAAAA0", "icon": "Parking"},
      {"label": "Rail Track", "color": "#E6968C", "icon": "Train"},
      {"label": "Person", "color": "#DC143C", "icon": "Walk"},
      {"label": "Rider", "color": "#FF0000", "icon": "Motorbike"},
      {"label": "Car", "color": "#0000E8", "icon": "Car"}
    },
    { ... }
  ]
}
```

## How to use

The editor is built around 3 different screens:
  - The file navigator let's you browse available files to select a bitmap images or a point cloud for labeling
  <img width="300" src="https://github.com/dmandrioli/sse-extra/raw/master/CaptureN1.jpg"/>
  - The bitmap image editor is dedicated to the labeling of jpg and png files
  <img width="300" src="https://github.com/dmandrioli/sse-extra/raw/master/Capture2D2.jpg"/>
  - The point cloud editor is dedicated to the labeling of point clouds by creating objetcts made of subsets of
  3D points
  <img width="300" src="https://github.com/dmandrioli/sse-extra/raw/master/Capture3D3.jpg"/>


### Using the bitmap image editor

There are several tools to create labeling polygons:
<p><strong>Polygon Drawing Tool (P)</strong></p>
<p className="italic">Click and/or drag to create points</p>
<p>Type ESC to remove last created points in reverse order</p>
<p>Drag the mouse pointer or hold Shift to create a complex polygon without having to click for each point</p>
<p>Type ENTER or double click the first point to close the polygon</p>

<p><strong>Magic Tool (A)</strong></p>
<p className="italic">Create a polygon automatically using contrast threshold detection </p>
<p>This tool is only useful to draw the outline of objects that have sharp contrasted edges (examples: sky, lane
marking)</p>
<p>Click inside the area you want to outline, then adjusts any sliders on the right to adjust the result</p>
<p>Type ENTER to validate the result</p>

<p><strong>Manipulation Tool (Alt)</strong></p>
<p className="italic">Select, move and add point(s) to existing polygons</p>
<p>Click inside a polygon to select it</p>
<p>Click a point to select it</p>
<p>Draw a lasso around multiple points to select them</p>
<p>Drag a point with the mouse to move it</p>
<p>Hold Shift to separate points that belongs to more than one polygon</p>
<p>Click the line of a polygon to create a new point and drag the newly created point to place it</p>

<p><strong>Cutting/Expanding Tool (C)</strong></p>
<p className="italic">Modify the shape of an existing polygon</p>
<p>Select the polygon you want to modify</p>
<p>Draw a line starting and ending on the outline of a polygon</p>
<p>The new line replace the existing path between starting and ending points</p>
<p>The resulting shape is always the largest one</p>

<p><strong>Contiguous Polygon Tool (F)</strong></p>
<p className="italic">Create contiguous polygons easily</p>
<p>Start a new polygon with the Polygon Drawing Tool</p>
<p>Create the starting point by snapping
    to the outline of the polygon you want to workaround</p>
<p>Create the ending point by snapping to another outline, at this point you must have a
straight line crossing one or more existing polygons</p>
<p>Hit F one or several times to choose what workaround path to use</p>


### Using the point cloud editor

  - Mouse left button: Rotate the point cloud around the current focused point (the center of the point cloud by
  default), clickon a single point to add it to the current selection
  - Mouse wheel: Zoom in/out
  - Mouse middle button (or Ctrl+Click): Change the target of the camera
  - Mouse right button: Used to select multiple points at the same time depending on the current Selection Tool and
  Selection Mode.

### API Endpoints

 - <code>/api/listing</code>: List all annotated images
 - <code>/api/json/[PATH_TO_FILE]</code>: (2D only) Get the polygons and other data for that file
 - <code>/api/pcdtext/[PATH_TO_FILE]</code>: (3D only) Get the labeling of a pcd file using 2 addditional
 columns: <code>label</code>
 and <code>object</code>
 -  <code>/api/pcdfile/[PATH_TO_FILE]</code>: (3D only) The same but returned as "plain/text" attachment file download


