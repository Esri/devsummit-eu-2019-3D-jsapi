<!-- .slide: data-background="images/bg-1.png" data-title="add-scene-layer" class="title" -->

# <span style="font-size: 0.8em;">ArcGIS API for JavaScript</span><br>Building Interactive 3D Web Apps Using Open Data


Jochen Manegold, Esri Germany \
Arno Fiva, Esri R&D Center Zürich

ESRI EUROPEAN DEVELOPER SUMMIT​

![Logo](images/esri-logo.png)

---

<!-- .slide: data-background="images/bg-4.png"  data-background-size="cover" data-title="earthquakes-intro" -->


## Earthquakes

<iframe data-src="./samples/3d-dashboard-earthquake/" style="width: 50%"></iframe>

---

<!-- .slide: data-background="images/bg-2.png" data-background-size="cover" -->

## Data


---

<!-- .slide: data-background="images/bg-2.png" data-background-size="cover" -->

## Basemap

<iframe data-src="http://www.arcgis.com/home/item.html?id=a66bfb7dd3b14228bf7ba42b138fe2ea"></iframe>

[http://www.arcgis.com/home/item.html?id=a66bfb7dd3b14228bf7ba42b138fe2ea](http://www.arcgis.com/home/item.html?id=a66bfb7dd3b14228bf7ba42b138fe2ea)


---

<!-- .slide: data-background="images/bg-2.png" data-background-size="cover" -->

## Basemap

<div class="two-columns">
  <div class="left-column">

<div class="code-snippet">
<pre><code class="lang-html">&lt;!-- Use dark theme --&gt;
&lt;link rel="stylesheet"
  href="https://js.arcgis.com/4.13/esri/themes/dark/main.css"
&gt;
</code></pre>
</div>

<div class="code-snippet">
<pre><code class="lang-ts">// Add Firefly basemap
Layer.fromPortalItem({
  portalItem: {
    id: "a66bfb7dd3b14228bf7ba42b138fe2ea"
  }
}).then(function(layer) {
  map.add(layer);
});</code></pre>
</div>

<div class="code-snippet">
<pre><code class="lang-ts">// Enable stars
map.basemap = null;
view.environment.lighting.starsEnabled = true;</code></pre>
</div>


  </div>
  <div class="right-column">
    <iframe data-src="./samples/3d-dashboard-earthquake/" ></iframe>
  </div>
</div>


---

<!-- .slide: data-background="images/bg-2.png" -->

## Visualize Data

<div class="two-columns">
  <div class="left-column">

<div class="code-snippet">
<button class="play" id="addFeatureLayer"></button>
<pre><code class="lang-ts">// Feature Layer URL
var url =
  "https://services1.arcgis.com/wdMHWK8xfn32LjIy/...";
// Add FeatureLayer to scene
const earthquakesLayer = new FeatureLayer({
  url,
  outFields: ["dateTime", "mag", "depth",
    "latitude", "longitude"],
  screenSizePerspectiveEnabled: false
});</code></pre>
</div>

<div class="code-snippet">
<button class="play" id="addElevationInfo"></button>
<pre><code class="lang-ts">// Invert and exeggerate earthquake depths
earthquakesLayer.elevationInfo = {
  mode: "absolute-height",
  featureExpressionInfo: {
    expression: "$feature.depth * 2"
  },
  unit: "kilometers"
};
</code></pre>
</div>

  </div>
  <div class="right-column">
    <iframe data-src="./samples/3d-dashboard-earthquake/" ></iframe>
  </div>
</div>

---

<!-- .slide: data-background="images/bg-2.png" data-title="earthquakes-renderer" -->

## Visualize Data

<div class="two-columns">
  <div class="left-column">

<div class="code-snippet">
<button class="play" id="addRenderer"></button>
<pre><code class="lang-ts" style="max-height: 450px; overflow: scroll;">// Color points according to magnitude
var magValues = {
  min: {
    value: 4,
    size: 1,
    color: [255, 221, 48]
  },
  max: {
    value: 7,
    size: 8,
    color: [255, 58, 48]
  }
};
// Use visualVariables on SimpleRenderer
earthquakesLayer.renderer = {
  type: "simple",
  symbol: {
    type: "point-3d",
    symbolLayers: [{
      type: "icon",
      resource: { primitive: "circle" }
    }]
  },
  visualVariables: [
    {
      type: "size",
      field: "mag",
      stops: [{
        value: magValues.min.value,
        size: magValues.min.size+"px"
      }, {
        value: magValues.max.value,
        size: magValues.max.size+"px"
      }]
    },
    {
      type: "color",
      field: "mag",
      stops: [{
        value: magValues.min.value,
        color: magValues.min.color,
      }, {
        value: magValues.max.value,
        color: magValues.max.color
      }]
    }
  ]
}
</code></pre>
</div>

  </div>
  <div class="right-column">
    <iframe data-src="./samples/3d-dashboard-earthquake/" ></iframe>
  </div>
</div>

---

<!-- .slide: data-background="images/bg-2.png" data-title="earthquakes-highlight" -->

## Interactions

Client-side Queries

<div class="two-columns">
  <div class="left-column">

<div class="code-snippet">
<button class="play" id="addHighlights"></button>
<pre><code class="lang-ts">// Highlight earthquakes near pointer
view.on("pointer-move", function (event) {

  // Query features on layer view
  view.whenLayerView(earthquakesLayer)
    .then(layerView => {
      var query = layerView.createQuery();
      query.geometry = view.toMap(event);
      query.distance = 500;
      query.units = "kilometers";
      return layerView.queryFeatures(query)
        .then(results => {
          layerView.highlight(results.features)
        });
    });

});</code></pre>
</div>

  </div>
  <div class="right-column">
    <iframe data-src="./samples/3d-dashboard-earthquake/" ></iframe>
  </div>
</div>

---

<!-- .slide: data-background="images/bg-2.png" data-title="earthquakes-stats" -->

## Interactions

Dashboard using [Chart.js](https://www.chartjs.org/)

<div class="two-columns">
  <div class="left-column">

<div class="code-snippet">
<button class="play" id="addStats"></button>
<pre><code class="lang-ts">// Highlight earthquakes near pointer
return layerView.queryFeatures(query).then(results => {
  layerView.highlight(results.features)</code></pre>
</div>

<div class="code-snippet fragment">
<pre><code class="lang-ts">  // All magnitudes
  var magValues = results.features.map(
    e => e.attributes.mag
  );</code></pre>
</div>

<div class="code-snippet fragment">
<pre><code class="lang-ts">  // Average magnitude
  var avg = magValues.reduce((a,b) => a + b, 0)
            / magValues.length;</code></pre>
</div>

<div class="code-snippet fragment">
<pre><code class="lang-ts">  // Max magnitude
  var max = Math.max(...magValues);</code></pre>
</div>

<div class="code-snippet">
<pre><code class="lang-ts">});</code></pre>
</div>

  </div>
  <div class="right-column">
    <iframe data-src="./samples/3d-dashboard-earthquake/" ></iframe>
  </div>
</div>

---

<!-- .slide: data-background="images/bg-2.png" data-background-size="cover" -->

Visualize

![Overview](images/earthquakes/overview.png)

---

<!-- .slide: data-background="images/bg-2.png"  data-background-size="cover" -->

Client-side Queries

![Overview](images/earthquakes/client-side-filtering.png)

---

<!-- .slide: data-background="images/bg-4.png"  data-background-size="cover" -->

## City Visualizations

---

<!-- .slide: data-background="images/bg-2.png"  data-background-size="cover" -->

2D Data

![Overview](images/2d-buildings/data.png)

---

<!-- .slide: data-background="images/bg-2.png"  data-background-size="cover" -->

Extrude Buildings

![Overview](images/2d-buildings/extrusion.png)

---

<!-- .slide: data-background="images/bg-2.png"  data-background-size="cover" -->

Use WebStyle Symbols

![Overview](images/2d-buildings/3d-symbols.png)

---

<!-- .slide: data-background="images/bg-2.png"  data-background-size="cover" -->

Extrude Building Heights

![Overview](images/2d-buildings/extrusion-with-height.png)

---

<!-- .slide: data-background="images/bg-4.png"  data-background-size="cover" -->

## 3D Building Models

---

<!-- .slide: data-background="images/bg-2.png"  data-background-size="cover" -->

Airport Sketch

![Overview](images/3d-buildings/sketch.png)
