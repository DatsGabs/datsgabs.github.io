---
title: "Create a custom cursor that follows you and inverts colors"
date: 2021-07-15
draft: false
description: "In this post I'm going to explain how to make a custom cursor that inverts the color of the content it's covering."
tags: ['css', 'js']
---

Yeah I know the title is not very clear for what I'm trying to explain in this post. So just see the results by yourself. 

{{< sandbox sandbox="allow-scripts">}}
<html>
  <head>
    <style>
      * {
        padding: 0;
        margin: 0;
        box-sizing: border-box;
      }

      body {
        overflow: hidden;
      }

      .container {
        width: 100vw;
        height: 100vh;
        background-color: white;
        position: relative;
      }

      .circle {
          width: 60px;
          height: 60px;
          background: white;
          mix-blend-mode: difference;
          position: absolute;
          left: 0;
          top: 0;
          border-radius: 50%;
          pointer-events: none;
        }

    </style>
  </head>
  <body>
    <div class="container">
      <p> Lorem ipsum dolor sit amet consectetur, adipisicing elit. Odio enim natus, modi aperiam, aspernatur rerum, molestias corporis fuga nihil dicta fugit in sapiente eveniet eius eos soluta suscipit sunt molestiae. </p>
      <p> Lorem ipsum dolor sit amet consectetur, adipisicing elit. Odio enim natus, modi aperiam, aspernatur rerum, molestias corporis fuga nihil dicta fugit in sapiente eveniet eius eos soluta suscipit sunt molestiae. </p>
      <p> Lorem ipsum dolor sit amet consectetur, adipisicing elit. Odio enim natus, modi aperiam, aspernatur rerum, molestias corporis fuga nihil dicta fugit in sapiente eveniet eius eos soluta suscipit sunt molestiae. </p>
      <p> Lorem ipsum dolor sit amet consectetur, adipisicing elit. Odio enim natus, modi aperiam, aspernatur rerum, molestias corporis fuga nihil dicta fugit in sapiente eveniet eius eos soluta suscipit sunt molestiae. </p>
    <div class="circle"></div>
    </div>
    <script>
      const cursor = document.querySelector(".circle");
      const delay = 250;

      function throttle(callback, limit) {
        let wait = false;
        return function () {
          if (!wait) {
            callback.apply(null, arguments);
            wait = true;
            setTimeout(function () {
              wait = false;
            }, limit);
          }
        };
      }

      // window.resize callback function
      function getDimensions(e) {
        cursor.style.top = `${e.clientY - 25}px`;
        cursor.style.left = `${e.clientX - 25}px`;
      }

      // window.resize event listener
      window.addEventListener("mousemove", (e) => {
        throttle(getDimensions(e), delay);
      });
    </script>
  </body>
</html>
{{</ sandbox >}}

To achieve this we are using the `mix-blend-mode` CSS property with the value `difference` which basically inverts the color of the content it has below it. 

{{< highlight css >}}
.container { 
    width: 100vw; 
    height: 100vh; 
    background-color: white; 
    position: relative; 
}

.circle {
    position: absolute;
    left: 0;
    top: 0;
    width: 60px;
    height: 60px;
    background: white;
    border-radius: 50%;
    pointer-events: none;
    mix-blend-mode: difference;
}
{{< /highlight >}}

Here we are positioning the circle absolute to the container, giving it a size and with border-radius we are making the div a circle. I disable the `pointer-events` or we are not going to be able to select any text or really doing anything on the site, we would always be clicking on the cursor div. 

> Setting the `background-color` to white in both the `.container` and the `.circle` is needed for this to work. In this tutorial I'm just going to use white but it can be done with any color. It just gets the inverted colors and then css just gets the difference. 

And we start to see that it blends already. We just have to make the circle to move.

{{< sandbox >}}
<html>
  <head>
    <style>
      * {
        padding: 0;
        margin: 0;
        box-sizing: border-box;
      }

      body {
        overflow: hidden;
      }

      .container {
        width: 100vw;
        height: 100vh;
        background-color: white;
        position: relative;
      }

      .circle {
          width: 60px;
          height: 60px;
          background: white;
          mix-blend-mode: difference;
          position: absolute;
          left: 0;
          top: 0;
          border-radius: 50%;
          pointer-events: none;
        }

    </style>
  </head>
  <body>
    <div class="container">
      <p> Lorem ipsum dolor sit amet consectetur, adipisicing elit. Odio enim natus, modi aperiam, aspernatur rerum, molestias corporis fuga nihil dicta fugit in sapiente eveniet eius eos soluta suscipit sunt molestiae. </p>
      <p> Lorem ipsum dolor sit amet consectetur, adipisicing elit. Odio enim natus, modi aperiam, aspernatur rerum, molestias corporis fuga nihil dicta fugit in sapiente eveniet eius eos soluta suscipit sunt molestiae. </p>
      <p> Lorem ipsum dolor sit amet consectetur, adipisicing elit. Odio enim natus, modi aperiam, aspernatur rerum, molestias corporis fuga nihil dicta fugit in sapiente eveniet eius eos soluta suscipit sunt molestiae. </p>
      <p> Lorem ipsum dolor sit amet consectetur, adipisicing elit. Odio enim natus, modi aperiam, aspernatur rerum, molestias corporis fuga nihil dicta fugit in sapiente eveniet eius eos soluta suscipit sunt molestiae. </p>
    <div class="circle"></div>
    </div>
  </body>
</html>
{{</ sandbox >}}

{{< highlight js >}}
const cursor = document.querySelector(".circle")

function getDimensions(e) {
  cursor.style.top = `${e.clientY - 25}px` // -25px for the size of the circle
  cursor.style.left = `${e.clientX - 25}px`
}

window.addEventListener("mousemove", (e) => {
  getDimensions(e)
});
{{< /highlight >}}

**And it's working**

## Bonus: MouseMove Optimization

While it's working, if you add a debug `getDimensions` function you might see that there are lots of calls to the function. And this could impact performance.

There is a really known way of solving this problem. By throttling the function calls only firing it once the `mousemove` event idle for time, in this example 250ms.

{{< highlight js >}}
const delay = 250

// ...

function throttle(callback, limit) {
  let wait = false
  return function () {
    if (!wait) {
      callback.apply(null, arguments)
      wait = true
      setTimeout(function () {
        wait = false
      }, limit)
    }
  }
}

window.addEventListener("mousemove", (e) => {
  throttle(getDimensions(e), delay)
});
{{< /highlight >}}

You can get the complete implementation [in this link](https://gist.github.com/DatsGabs/e8da259163413861720384d432e79bfb)

## Related Posts
1. [How to auto adjust font-size to fit div](https://gabriellazcano.com/blog/how-to-auto-adjust-font-size-to-fit-div)
