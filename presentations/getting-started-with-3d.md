<!-- .slide: data-background="images/bg-1.png" data-title="add-scene-layer" class="title" -->

# <span style="font-size: 0.8em;">ArcGIS API for JavaScript</span><br>Getting Started with 3D


Sébastien Szollosi, Esri France \
Arno Fiva, Esri R&D Center Zürich

ESRI EUROPEAN DEVELOPER SUMMIT​

![Logo](images/esri-logo.png)

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

### Add SceneLayer

<div class="two-columns">
  <div class="left-column">

<div class="code-snippet">
<button class="play" id="addSceneLayerButton"></button>
<pre><code class="lang-ts">// Add layer showing housing density in NYC
var buildingsLayer = new SceneLayer({
  portalItem: {
    id: "2e0761b9a4274b8db52c4bf34356911e"
  }
});
map.layers.add(buildingsLayer);
</code></pre>
</div>

  </div>
  <div class="right-column">
    <iframe id="go-to-demo" data-src="./samples/berlin-3d" ></iframe>
  </div>
</div>

---

## Primitive (Basic) Types

- `boolean`, `number`, `string`, `[]`, `{}`
- `any`

```ts
type Foo = number;

const foo: Foo = 8;
const bar: string = "Lorem ipsum";

// Here be dragons
const waldo: any = {
  doStuff: (things: any) => something
};
```