<!-- .slide: data-background="images/bg-1.png" data-title="add-scene-layer" class="title" -->

# <span style="font-size: 0.8em;">ArcGIS API for JavaScript</span><br>Using Animations


Yannik Messerli, Esri R&D Center Zürich \
Arno Fiva, Esri R&D Center Zürich

ESRI EUROPEAN DEVELOPER SUMMIT​

![Logo](images/esri-logo.png)

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

### Agenda

* 1. Built-in API capabilities (Y)
  * `view.goTo()` / `slide.apply()`
  * Promises
  * Water
* 2. Custom animation + libraries
  * Daylight using `requestAnimationFrame()` (Y)
  * 3D Symbols using [anime.js](https://animejs.com/) (A, plane airport approach)<<<<<<< HEAD
  * Camera paths (A, fly along Berlin wall)
  * Line geometry (A, Cyclades sailing route)
* 3. External renderer (Y)

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

# Animation

From Latin _animationem_: successive drawings to create an illusion of movement.

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

### Type of animations:

- Animate UI element (CSS) <small>_not covered_</small>
- **Animate camera**
- Animate scene's elements
    + **Visual variables _(colors, positions, ...)_**
    + 3D texture <small>_not covered_</small>
    + **3D environment properties**
    + 3D geometries <small>_partially covered_</small>
    + **Full 3D animations** <small>_extension_</small>

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

# 1. Built-in API capabilities

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

The `view` provides a function `goTo` that interpolates the camera between to point in the view. 

---

## [`SceneView.goTo`](https://developers.arcgis.com/javascript/beta/api-reference/esri-views-SceneView.html#goTo)

- Camera control work horse: `goTo(target[, options]): Promise`
- A number of diffent targets are supported: `[lon, lat]`, `Camera`, `Geometry`, `Geometry[]`, `Graphic`, `Graphic[]`
- Besides target, allows specifying desired `scale`, `center`, `position` (camera), `heading` and `tilt`
- Animates! (by default)

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

## [`SceneView.goTo`](https://developers.arcgis.com/javascript/beta/api-reference/esri-views-SceneView.html#goTo) &mdash; `heading`/`tilt`

<div class="twos">
  <div class="snippets">
    <div class="code-snippet">
      <pre><code class="lang-ts hljs typescript" style="padding: 20px;width: 100%;">
const currentHeading = view.camera.heading;
// Set the heading of the view to
// the closest multiple of 30 degrees
const heading = Math.floor((currentHeading + 1) / 30)
    \* 30 + 30;
// go to heading preserves view.center
view.goTo({
  heading
});
        </code>
    </pre>
      <svg data-play-frame="frame-go-to-heading-tilt" class="play-code" data-play-argument="heading" viewBox="0 0 24 24"><path fill="#999" d="M12,20.14C7.59,20.14 4,16.55 4,12.14C4,7.73 7.59,4.14 12,4.14C16.41,4.14 20,7.73 20,12.14C20,16.55 16.41,20.14 12,20.14M12,2.14A10,10 0 0,0 2,12.14A10,10 0 0,0 12,22.14A10,10 0 0,0 22,12.14C22,6.61 17.5,2.14 12,2.14M10,16.64L16,12.14L10,7.64V16.64Z" /></svg>
    </div>
    <div class="code-snippet">
      <pre><code class="lang-ts hljs typescript" style="padding: 20px;">
const currentTilt = view.camera.tilt;
// Cycle tilt of the view in 15 degree increments
const tilt = (Math.floor((currentTilt + 1) / 15) * 15 + 15) % 90;
// go to tilt preserves view.center
view.goTo({
  tilt
});</code></pre>
      <svg data-play-frame="frame-go-to-heading-tilt" class="play-code" data-play-argument="tilt" viewBox="0 0 24 24"><path fill="#999" d="M12,20.14C7.59,20.14 4,16.55 4,12.14C4,7.73 7.59,4.14 12,4.14C16.41,4.14 20,7.73 20,12.14C20,16.55 16.41,20.14 12,20.14M12,2.14A10,10 0 0,0 2,12.14A10,10 0 0,0 12,22.14A10,10 0 0,0 22,12.14C22,6.61 17.5,2.14 12,2.14M10,16.64L16,12.14L10,7.64V16.64Z" /></svg>
    </div>
  </div>

  <div class="snippet-preview">
    <iframe id="frame-go-to-heading-tilt" data-src="./samples/using-animations/04-go-to-heading-tilt.html" style="overflow: hidden;padding: 0; min-width: 400px;" frameborder="0"></iframe>
  </div>
</div>

<!---

## [`SceneView.goTo`](https://developers.arcgis.com/javascript/beta/api-reference/esri-views-SceneView.html#goTo) &mdash; Continuous updates

<div class="twos">
    <div class="code-snippet">
      <pre><code class="lang-ts hljs typescript" style="padding: 20px; float: none; ">
function animateLookAroundStep() {
  view.goTo({
    position: animationPosition,
    heading: view.camera.heading - 0.1
  }, { animate: false });
}
<br>
function animateRotateAroundStep() {
  view.goTo({
    center: animationCenter,
    scale: animationScale,
    heading: view.camera.heading + 0.1
  }, { animate: false });
}
<br>
function startAnimation() {
  // Store scale, center and position to
  // animate around
  animationScale = view.scale;
  animationCenter = view.center.clone();
  animationPosition = view.camera.position.clone();
  animate();
}
</code></pre>
      <svg data-play-frame="frame-go-to-heading-continuous" class="play-code" viewBox="0 0 24 24"><path fill="#999" d="M12,20.14C7.59,20.14 4,16.55 4,12.14C4,7.73 7.59,4.14 12,4.14C16.41,4.14 20,7.73 20,12.14C20,16.55 16.41,20.14 12,20.14M12,2.14A10,10 0 0,0 2,12.14A10,10 0 0,0 12,22.14A10,10 0 0,0 22,12.14C22,6.61 17.5,2.14 12,2.14M10,16.64L16,12.14L10,7.64V16.64Z" /></svg>
    </div>
  
  <div class="snippet-preview">
    <iframe id="frame-go-to-heading-continuous" data-src="./samples/using-animations/05-go-to-heading-continuous.html" style="overflow: hidden;padding: 0; min-width: 400px;" frameborder="0"></iframe>
  </div>
</div>

I realised that this already involves animation that we see in the next chapter, so this slide is irrelevant here.

-->
---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

## [`SceneView.goTo`](https://developers.arcgis.com/javascript/beta/api-reference/esri-views-SceneView.html#goTo) &mdash; Graphics, query

<div class="twos">
  <div class="code-snippet">
    <pre><code class="lang-ts hljs typescript" style="padding: 20px;width: 100%;">
const query = new Query({
  definitionExpression: "ELEVATION > 90",
  returnGeometry: false,
  geometry: view.extent.clone(),
  outFields: [layer.objectIdField]
});
// Query features from the service
layer.queryFeatures(query)
.then((featureSet: FeatureSet) => {
  // Get all the feature object ids
  const objectIds = featureSet.features.map(
    feature => feature.attributes[layer.objectIdField]
  );
<br>
  // Query the graphics from the layer view
  const query = new Query({ objectIds });
  return layerView.queryFeatures(query);
})
.then((graphics: Graphic[]) => {
  // Finally, frame the graphics using goTo
  view.goTo(graphics, { speedFactor: 0.2 });
});
</code></pre>
 <svg data-play-frame="frame-go-to-graphics" class="play-code" viewBox="0 0 24 24"><path fill="#999" d="M12,20.14C7.59,20.14 4,16.55 4,12.14C4,7.73 7.59,4.14 12,4.14C16.41,4.14 20,7.73 20,12.14C20,16.55 16.41,20.14 12,20.14M12,2.14A10,10 0 0,0 2,12.14A10,10 0 0,0 12,22.14A10,10 0 0,0 22,12.14C22,6.61 17.5,2.14 12,2.14M10,16.64L16,12.14L10,7.64V16.64Z" /></svg>
  </div>
  <div class="snippet-preview">
    <iframe id="frame-go-to-graphics" data-src="./samples/using-animations/06-go-to-graphics.html" style="overflow: hidden;padding: 0; min-width: 400px;" frameborder="0"></iframe>
  </div>
</div>

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

## [`SceneView.goTo`](https://developers.arcgis.com/javascript/beta/api-reference/esri-views-SceneView.html#goTo) &mdash; Graphics, hitTest

<div class="twos">
  <div class="code-snippet">
    <pre><code class="lang-ts hljs typescript" style="padding: 20px;width: 100%;">
<br><br><br>
view.on("double-click", (event: any) => {
  view.hitTest({ x: event.x, y: event. y})
      .then((hitResult: any) => {
        const graphic = (
          hitResult.results[0] &&
          hitResult.results[0].graphic
        );
<br>
        if (graphic) {
          const target = {
            target: graphic,
            scale: 1200,
            heading: view.camera.heading + 50
          };
<br>
          view.goTo(target, { speedFactor: 0.5 });
        }
      });
});
</code></pre>
</div>
  <div class="snippet-preview">
    <iframe id="frame-go-to-graphics-hit-test" data-src="./samples/using-animations/07-go-to-graphics-hit-test.html" style="overflow: hidden;padding: 0; min-width: 400px;" frameborder="0"></iframe>
  </div>
</div>

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

## [`Slides`](https://developers.arcgis.com/javascript/beta/api-reference/esri-views-SceneView.html#goTo)

- Convinient way to `goTo` point

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

## [`SceneView.apply`](https://developers.arcgis.com/javascript/beta/api-reference/esri-views-SceneView.html#goTo) &mdash; Animation options (since 4.2)

<div class="twos">
  <div class="code-snippet">
    <pre><code class="lang-ts hljs typescript" style="padding: 20px;width: 100%;">
function customEasing(t: number): number {
  return 1 - Math.abs(
    Math.sin(
      -1.7 + t \* 4.5 \* Math.PI
    )
  ) \* Math.pow(0.5, t \* 10);
}
<br>
// ...
<br>
slide.applyTo(view, {
  // Either well-known builtin name, or
  // custom function as above
  easing,
<br>
  // Speed the animation up or down
  speedFactor
});
</code></pre>
  </div>
  <div class="snippet-preview">
    <iframe id="frame-go-to-animation-options" data-src="./samples/using-animations/08-go-to-animation-options.html" style="padding: 0; min-width: 400px; width: 100%; max-width: 90%; height: 100%;" allowfullscreen="true" frameborder="0"></iframe>
  </div>
</div>

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

# 2. Custom animation

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

In this section, the idea is to...

### Update _some_ parameters at a defined time steps

using vanilla javascript or libraries.

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

<div class="twos">
    <div>
        Let's change the lighting manually... <br><br>
        <div class="code-snippet" style="max-width: 600px; font-size: 130%;float: none;">
            <pre>
            <code style="padding: 50px;" class="lang-js">
    const now = new Date();
    <br>
    function nextStep() {
        now.setHours(now.getHours() + 1);
        view.environment.lighting.date = now;
    }
            </code></pre>
            <svg data-play-frame="daylight-click" class="play-code" viewBox="0 0 24 24"><path fill="#999" d="M12,20.14C7.59,20.14 4,16.55 4,12.14C4,7.73 7.59,4.14 12,4.14C16.41,4.14 20,7.73 20,12.14C20,16.55 16.41,20.14 12,20.14M12,2.14A10,10 0 0,0 2,12.14A10,10 0 0,0 12,22.14A10,10 0 0,0 22,12.14C22,6.61 17.5,2.14 12,2.14M10,16.64L16,12.14L10,7.64V16.64Z" /></svg>
        </div>
    </div>
    <div class="snippet-preview">
      <iframe id="daylight-click" data-src="./samples/using-animations/01-snippet-daylight-click.html" scrolling="no" style="overflow: hidden;padding: 0; min-width: 400px;" frameborder="0"></iframe>
    </div>
</div>

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

### The Naive Approach

Using javascript built-in timing functions:

<div class="code-snippet" style="font-size: 130%;width: auto; margin: auto; float: none;">
        <pre>
            <code style="padding: 0px 50px;" class="lang-js">
...
setInterval(nextStep, 200);
            </code>
        </pre>
    </div>

Or...

<div class="code-snippet" style="font-size: 130%;float: none;">
        <pre>
            <code style="padding: 0px 50px;" class="lang-js">
function nextStep() {
    ...
    setTimeout(nextStep, 0);
}
setTimeout(nextStep, 0);
            </code>
        </pre>
    </div>


---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

### Why not?

<small><i>(explanation of the task queue and event loop here?)</i></small>

<!-- Inspiration: https://vimeo.com/254947206 -->

- `nextStep` can be called multiple time before the browser renders one frame
- It will waste CPU time
- Animation can look chopy if called less time than browser renders frames

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

### Solution: `requestAnimationFrame`

<!-- inspiration: https://flaviocopes.com/requestanimationframe/
    https://css-tricks.com/using-requestanimationframe/
    https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame -->

> It's a function provided by the browser for it to call your function (likeley to update an animation) before the next **repaint**.

<small>source: <a href="https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame">https://developer.mozilla.org</a></small>

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

### In code...
<div class="twos">
    <div>
        <div class="code-snippet" style="max-width: 600px; font-size: 130%;float: none;">
            <pre>
            <code style="padding: 0" class="lang-js">
                <br><br><br>
    ...
    const startTime = new Date().getTime();
    <br>
    function nextStep(timestamp) {
        // epoch time [ms] (floated precision):
        const elapsedTime = startTime - timestamp;
        ...
        // request next refresh:
        window.requestAnimationFrame(nextStep);
    }
    // start:
    window.requestAnimationFrame(nextStep);
    <br>
    <br>
    <br>
            </code></pre>
            <svg data-play-frame="daylight-animation" class="play-code" viewBox="0 0 24 24"><path fill="#999" d="M12,20.14C7.59,20.14 4,16.55 4,12.14C4,7.73 7.59,4.14 12,4.14C16.41,4.14 20,7.73 20,12.14C20,16.55 16.41,20.14 12,20.14M12,2.14A10,10 0 0,0 2,12.14A10,10 0 0,0 12,22.14A10,10 0 0,0 22,12.14C22,6.61 17.5,2.14 12,2.14M10,16.64L16,12.14L10,7.64V16.64Z" /></svg>
        </div>
    </div>
    <div class="snippet-preview">
      <iframe id="daylight-animation" data-src="./samples/using-animations/02-snippet-daylight-animation.html" scrolling="no" style="overflow: hidden;padding: 0; min-width: 600px;margin-top: 0;" frameborder="0"></iframe>
    </div>
</div>

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

### Tips & tricks

<div class="code-snippet" style="font-size: 130%;width: auto; margin: auto; float: none;">
        <pre>
            <code style="padding: 20px 50px;" class="lang-js">
const elapsedTime = startTime - currentTimestamp;
<br>
// project to your position coordinate:
const step = velocity * elapsedTime;
<br>
// update at position x:
updateAnimationAt(step);
            </code>
        </pre>
    </div>

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

### Libraries: Use anime.js for 3D Symbols

<div class="two-columns">
  <div class="left-column">

<div class="code-snippet">
<button class="play" id="addSceneLayerButton"></button>
<pre><code class="lang-ts">anime.timeline({
  autoplay: false,
  targets: point,
  loop: true,
  duration: 5000,
  update: function() {
    plane.geometry = point.clone();
  }
})
.add({
  ...pointB,
  easing: "linear",
})
.add({
  z: 0,
  easing: "easeOutSine",
}, 0)
.add({
  ...pointC,
  easing: "easeOutSine",
});
</code></pre>
</div>

  </div>
  <div class="right-column">
    <iframe id="go-to-demo" data-src="./samples/berlin-airport" ></iframe>
  </div>
</div>

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

## So, what can you animate with it?

- Renderer
- Visual variables
- ...


---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

# 3. External Renderers
=======
  * Camera paths (A, fly along Berlin wall, `geometryEngine.generalize`, splines, interpolate)
  * Line geometry (A, Cyclades sailing route, query geometry, add points to geometry)
* Three.js as an external renderer (Y)

