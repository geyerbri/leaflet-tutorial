# leaflet-tutorial
Files for use in LEADR tutorial. The [Leaflet JS library can be found here](https://leafletjs.com/download.html). Sign up for a [Mapbox account here](https://mapbox.com). Leaflet has a [quick start guide](https://leafletjs.com/examples/quick-start/). Leaflet's more complete [documentation is here](https://leafletjs.com/reference-1.6.0.html).
## Fork this repo
## Set your forked repo's Pages branch to "master"
## Edit index.html to add a ine below the paragraph
```html
<div id="map-here" style="height: 500px"></div>
  ```
## Add this stylesheet to head
```html
<link rel="stylesheet" type="text/css" href="leaflet/leaflet.css">
```
## Add this script to `body`, just above end of `body`
```html
<script src="leaflet/leaflet.js"></script>
```
## Write new script, just below the one just pasted in (still inside `body`)
```html
<script type="text/javascript">

</script>
```
## Add the function calling the map inside the new script
```javascript
var map = L.map('map-here').setView([42.731105, -84.475239], 17);
```
At this point, if you save index.html and load the page in a browser tab, you'll see a map with no tiles. In this code, the browser will use the L.map function to create a map in the map variable, which is tied to the map-here ID in the html code. The view is set to center on that latitude and longitude, and the view is set to the 17th level (higher level numbers are closer in on the map).
## Add a tile layer to the initialized map, in the same script container
```javascript
L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="https://mapbox.com">Mapbox</a> simple streets; example by Brian'
}).addTo(map);
```
Note the attribution, which will add itself to the bottom-right attribution on the map. Notice the final funtion, telling it to add itself to the map that's already been initialized. The `addTo()` part is telling the browser to add the tile layer to the variable that is named "map" in the script. If you change the variable's name (some tutorias use "mymap"), the referenced variable in the `addTo()` function will need to be changed too.
## Swap out OpenStreetMaps for Mapbox
Change url inside L.tileLayer to
```javascript
https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token={accessToken}
```
Notice the variables `{id}` and `{accessToken}`. You'll need to provide values to those variables, in the same way that the map's `attribution` is provided. Get these from your own Mapbox account. Add a comma to the end of the `attribution` line, then add these four lines below that (the first two account for size differences between Mapbox styles and LeafletJS expectations):
```javascript
tileSize: 512,
zoomOffset: -1,
id: '{your mapbox style id}',
accessToken: '{your mapbox access token}'
```
Leave the single quotes, but replace the values with your own. Or, use one of Mapbox's public styles, listed in their [Map documentation](https://docs.mapbox.com/mapbox-gl-js/api/#map), like `mapbox/streets-v11`. Notice that the style ID is only the last two portions of the "Style URL" supplied by Mapbox. In their documentation, they describe this as a ":owner/:style".
## Common parameters
You can add other common variables to the same section as `attribution`, `id`, and `accessToken`, such as `minZoom` (the furthest out a map can display), `maxZoom` (the furthest in), and others (see [full documentation](https://leafletjs.com/reference-1.6.0.html)).
## Set Boundaries
Set the map boundaries, so that the user can't scroll the map to another part of the world. Start by defining two new variables just above `map`, with their values using the LatLng function to define two points with latitude and longitude:
```javascript
var northwest = L.latLng(42.75, -85);
var southeast = L.latLng(42.7, -84);
var bounds = L.latLngBounds(northwest, southeast);
```
Then, add the following betweem the `)` and `.` in `L.map('map').setView(` so the value now looks like this:
```javascript
L.map('map').setMaxBounds(bounds).setView([42.731105, -84.475239], 17);
```
## Add Marker with popup content
Create a marker with a popup by adding this new code inside the same script container, below everything else.
```javascript
var popupImg = "https://farm7.static.flickr.com/6223/6240985827_66d54a66b2_b.jpg",
    popupTitle = "LEADR",
    popupLoc = "Old Horticulture",
    content = "<img src=" + popupImg + " style='float:left;height:150px;padding-right:10px'><strong>" + popupTitle + "</strong><br>" + popupLoc + "<div style='clear:both'></div>",
    marker = L.marker([42.732217, -84.478339]).addTo(map);
    marker.bindPopup(content, {minWidth: 400, autoPanPadding: [5,5], closeButton: true});
```
## Use geoJSON data instead
Replace the previous code with the following
```javascript
var pins = L.geoJSON().addTo(map);
var geoJSON = [
  {
    type: 'Feature',
      geometry: {
        type: 'Point',
        coordinates: [-84.478339, 42.732217]
      },
      properties: {
        title: 'LEADR',
        building: 'Old Horticulture',
        url: 'http://leadr.msu.edu',					
        image: 'https://farm7.static.flickr.com/6223/6240985827_66d54a66b2_b.jpg',
      }
    },
    {
      type: 'Feature',
      geometry: {
        type: 'Point',
        coordinates: [-84.473638, 42.729234]
      },
      properties: {
        title: 'Department of Anthropology',
        building: 'Baker Hall',
        url: 'https://anthropology.msu.edu',
        image: 'https://upload.wikimedia.org/wikipedia/commons/b/b5/MSU_Baker_Hall.jpg',
      }
    }
];
pins.on('layeradd', function(e) {
  var marker = e.layer,
      feature = marker.feature,
      content = "<img src=" + feature.properties.image + " style='float:left;height:150px; padding-right:10px'><strong><a title=" + feature.properties.title + " target='_blank' href=" + feature.properties.url + ">" + feature.properties.title + "</a></strong><br>" + feature.properties.building + "<div style='clear:both'></div>";
  marker.bindPopup(content, {minWidth: 400, autoPanPadding: [5,5], closeButton: true});
});
pins.addData(geoJSON);
```
An important point to note about using geoJSON data is that the coordinates in a geoJSON setup are in \[longitude, latitude] format, instead of \[latitude, longitide] (the format used in most instances of using Leaflet, like `setView`.
