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
  * 3D Symbols using [anime.js](https://animejs.com/) (A, plane airport approach)
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
        Let's change the lighting using a Ph&eacute;nakisticope... <br><br>
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
        </div>
    </div>
    <div class="snippet-preview">
      <iframe id="code-snippet-3" data-src="./samples/using-animations/01-snippet-daylight-click.html" scrolling="no" style="overflow: hidden;padding: 0; min-width: 400px;" frameborder="0"></iframe>
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
        </div>
    </div>
    <div class="snippet-preview">
      <iframe id="code-snippet-2" data-src="./samples/using-animations/02-snippet-daylight-animation.html" scrolling="no" style="overflow: hidden;padding: 0; min-width: 600px;margin-top: 0;" frameborder="0"></iframe>
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

### Libraries

...

---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

## So, what can you animate with it?

- Renderer
- Visual variables
- ...


---

<!-- .slide: data-background="images/bg-3.png" data-title="add-scene-layer" -->

# 3. External Renderers
