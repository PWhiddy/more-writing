---
layout: post
mathjax: true
title: Porting a Particle Sim from Jax to Pyscript (First impressions)
--- 

<html>
    <head>
      <link rel="stylesheet" href="https://pyscript.net/alpha/pyscript.css" />
      <script defer src="https://pyscript.net/alpha/pyscript.js"></script>
      <py-env>
        - numpy
        - matplotlib
      </py-env>
    </head>

  <body>
    <center>
    
    <div>

      <h1>Let's simulate some particles!</h1>
      <br>
      <div id="nbody">
        
        <div>
          <div class="loading"></div>
          <canvas></canvas>
        </div>
      </div>
    </div>
  </center>

    <py-script>

# interactive setup adapted from https://github.com/pyscript/pyscript/blob/main/pyscriptjs/examples/numpy_canvas_fractals.html

from pyodide import to_js, create_proxy

import numpy as np
from numpy import random
      
from js import (
    console,
    document,
    devicePixelRatio,
    ImageData,
    Uint8ClampedArray,
    CanvasRenderingContext2D as Context2d,
    requestAnimationFrame,
)

###### boilerplate from https://github.com/PWhiddy/tensor-canvas/blob/master/tensorcanvas/BaseShapes.py
def make_uv(t):
  uvx = np.tile(np.expand_dims(np.arange(0.0, t.shape[1], 1), axis=0), (t.shape[0],1))
  uvy = np.tile(np.expand_dims(np.arange(0.0, t.shape[0], 1), axis=0), (t.shape[1],1)).transpose()
  return uvx, uvy

def dist_to_col(dist, color, blend, t):
  msk = np.clip((dist+blend) / (2.0*blend), 0.0, 1.0)
  msk = msk * msk * (3.0 - 2.0 * msk)
  msk = np.tile(np.expand_dims(msk, axis=2), (1,1,3))
  col_t = np.tile(np.expand_dims(np.expand_dims(np.array(color), axis=0), axis=0), ((t.shape[0],t.shape[1],1)))
  return msk*t + (1.0-msk)*col_t

def draw_circle(xp, yp, radius, color, t, blend=0.75):
  uvx, uvy = make_uv(t)
  dist = np.sqrt((xp-uvx)**2.0 + (yp-uvy)**2.0 + 1.0)-radius
  t = dist_to_col(dist, color, blend, t)
  return t
######

##### from https://github.com/PWhiddy/jax-experiments/blob/main/nbody.ipynb

def make_init_state(p_count):
  return random.rand(p_count, 2), random.rand(p_count, 2)-0.5

def compute_forces(pos, scale, eps=0.1):
  a, b = np.expand_dims(pos, 1), np.expand_dims(pos, 0)
  diff = a - b
  dist = (diff * diff).sum(axis=-1) ** 0.5
  dist = np.expand_dims(dist, 2)
  force = diff / ((dist * scale) ** 3 + eps)
  return force.sum(0)

def sim_update_force(parts_pos, parts_vel, t_delta=0.05, scale=5, repel_mag=0.1, center_mag=2.5, steps=10, damp=0.99):
  p_p = np.array(parts_pos)
  p_v = np.array(parts_vel)
  for _ in range(steps):
    p_p = p_p + t_delta * p_v
    force = compute_forces(p_p, scale)
    center_diff = p_p-0.5
    centering_force = center_diff / ((center_diff ** 2).sum() ** 0.5)
    p_v = damp * p_v - t_delta * (force * repel_mag + centering_force * center_mag)
  return p_p, p_v

def draw_sim(parts_pos, parts_vel, grid_r, opacity=1.0, p_size=4.0):
  canvas = np.zeros((grid_r, grid_r, 3)) + 253/255
  # would be interesting to use jax.experimental.loops for these
  for part_p, part_v in zip(parts_pos, parts_vel):
    sp = part_p*grid_r
    # snipe only the pixels we're going to update (numpy version only)
    lx = (sp[0]-5)
    ux = (sp[0]+5)
    ly = (sp[1]-5)
    uy = (sp[1]+5)
    mag = 10*(np.abs(part_v[0]) + np.abs(part_v[1]))
    canvas[int(lx):int(ux), int(ly):int(uy), :] = draw_circle(
      sp[0]-lx, sp[1]-ly, p_size, np.array([mag,0.0,1-mag]), canvas[int(lx):int(ux), int(ly):int(uy), :]
    )
  return np.clip(canvas, 0, 1)

width, height = 384, 384

grid_res = 384
should_push = False

def generate_step(width, height, state):
    global should_push
    if state == None:
      p_state, v_state = make_init_state(128)
      v_state *= 0
      base_canvas = np.ones((width, height, 4), dtype=np.uint8) * 255
    else:
      base_canvas, p_state, v_state = state
    
    center_mag = 0.0 if should_push else 0.5
    for _ in range(5):
      p_state, v_state = sim_update_force(p_state, v_state, t_delta=0.05, scale=10, center_mag=center_mag, repel_mag=0.05, damp=0.996, steps=2)
    base_canvas[:, :, :3] = 255*draw_sim(p_state, v_state, grid_res, p_size=4.0)
    #base_canvas[:, :, 3] = 1
    #base_canvas[:, :, :3] = np.random.randint(0,255,size=(width,height, 3), dtype=np.uint8)

    return base_canvas, p_state, v_state
    #return np.random.randint(0,255,size=(width,height, 4), dtype=np.uint8)

#####

def prepare_canvas(width: int, height: int, canvas: Element) -> Context2d:
    ctx = canvas.getContext("2d")

    canvas.style.width = f"{width}px"
    canvas.style.height = f"{height}px"

    canvas.width = width
    canvas.height = height

    ctx.clearRect(0, 0, width, height)

    return ctx

def draw_image(ctx: Context2d, image: np.array) -> None:
  data = Uint8ClampedArray.new(to_js(image.tobytes()))
  width, height, _ = image.shape
  image_data = ImageData.new(data, width, height)
  ctx.putImageData(image_data, 0, 0)

current_image = None
async def draw_particles(state) -> None:
  spinner = document.querySelector("#nbody .loading")
  canvas = document.querySelector("#nbody canvas")

  spinner.style.display = ""
  canvas.style.display = "none"

  ctx = prepare_canvas(width, height, canvas)

  console.log("Computing Nbody interactions ...")

  poly_in = document.querySelector("#poly")
  coef_in = document.querySelector("#coef")
  conv_in = document.querySelector("#conv")
  iter_in = document.querySelector("#iter")

  image, p_state, v_state = generate_step(width, height, state)

  global current_image
  current_image = image
  draw_image(ctx, image)

  spinner.style.display = "none"
  canvas.style.display = "block"
  requestAnimationFrame(create_proxy(lambda _event: draw_particles((image, p_state, v_state))))

canvas = document.querySelector("#nbody canvas")

async def mousedown(event):
  global should_push
  should_push = True 

async def mouseup(event):
  global should_push
  should_push = False 


canvas.addEventListener("mousedown", create_proxy(mousedown))
canvas.addEventListener("mouseup", create_proxy(mouseup))

import asyncio

_ = await asyncio.gather(
  draw_particles(None)
)
      </py-script>
  <br>
  </body>
</html>

    
I've ported my simple [jax particle sim experiment](https://github.com/PWhiddy/jax-experiments/blob/main/nbody.ipynb) to pyscript/numpy! The [code](https://github.com/PWhiddy/more-writing/blob/main/_posts/2022-05-5-Pyscript-Test.md) ended up quite ugly and inefficent, but it works! Overall I'm impressed with performance and usability of pyodide and pyscript. Python libraries like numpy that are implemented in C seem to be able to run at full speed via wasm, so out of the box many numerical operations will probably be faster than naive JS. The integration with the web environment is very slick, DOM manipulation and JS interaction is pretty straightforward and this feels like a great option for interactive and graphical python applications. Because it's running the regular CPython interpreter, you get  error messages just like you'd normally expect. This means debugging works right away and doesn't require any extra tooling as one might want using compiled languages in wasm. In the case of this particular code however giving up jax's jit and GPU acceleration has made this simulation run quite slow. It will be interesting to see if ML frameworks add support for pyodide or new frameworks entirely pop up to take advantage of this. This setup won't be useful to anyone working with large amounts of data or compute obviously, but is a much better fit for lightweight scripts than something like google colab. Exciting!