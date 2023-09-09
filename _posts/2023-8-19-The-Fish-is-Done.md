---
layout: post
mathjax: false
title: The Fish is Done
---

Last fall, I helped create the design for a large art installation by writing a python program which converted an illustration of a fish into over 268 thousand tiny disks.  

&nbsp;
  
<video width=640 autoplay playsinline muted loop>
  <source src="https://i.imgur.com/7b8yEpP.mp4" type="video/mp4" />
</video>
*[Sagmeister's Instagram account](https://www.instagram.com/stefansagmeister/)*  

&nbsp;

Once the code was written, it took only 15 seconds to tile and generate the positions of all the disks. But it took quite some time to physically position these disks in the real world!   

&nbsp;  
 
<video width=640 autoplay playsinline muted loop>
  <source src="https://i.imgur.com/A0kbqHC.mp4" type="video/mp4" />
</video>
*[OZ Art NWA](https://www.instagram.com/ozartnwa/)*  

&nbsp;

![The original fish illustration](https://i.imgur.com/umVL90T.png)
*The original fish illustration*

After the mask had been generated and converted to the correct resolution, the core of the code was:
&nbsp;
```python
import numpy as np
from einops import rearrange
from skimage.morphology import flood_fill
import drawSvg as draw
from PIL import Image

tile_size = 10
disk_size = 1.125 # inches
cell_size = (11 + 13/16) / 10 # inches
wall_w = 98*12 + 8
wall_h = 85*12
padding = 1/16
thres = 20

img = Image.open("mask_v2.png")
im = np.array(img)[:, 1:]
ims = np.pad(np.array(im[:810, :1040, 3]), ((40, 40), (0,0)), 'edge') 
ims = flood_fill(ims, (600,600), 255)
ims = flood_fill(ims, (200,500), 255)

chunked = rearrange(ims, "(i x) (j y) -> i j x y ", x=tile_size, y=tile_size)
threshed_chunks = (chunked > thres).astype(np.uint8)
chunk_size_x = thresh_chunks_mask[0][0].shape[0] * cell_size + padding
chunk_size_y = thresh_chunks_mask[0][0].shape[1] * cell_size + padding

w = thresh_chunks_mask.shape[1]*chunk_size_x
h = thresh_chunks_mask.shape[0]*chunk_size_y
d = draw.Drawing(w, h, origin=(0,0), displayInline=False)
# add bounding box on fish
min_x = w
max_x = 0
min_y = h
max_y = 0
dot_count = 0

for j, col in enumerate(thresh_chunks_mask):
    for i, cell in enumerate(col):
        if cell.any():
            for y, c_col in enumerate(cell):
                for x, val in enumerate(c_col):
                    xp = i*chunk_size_x + x * cell_size
                    yp = h-(j*chunk_size_y) - y * cell_size
                    d.append(draw.Rectangle(
                        xp-0.5*cell_size, yp-0.5*cell_size, cell_size, cell_size,
                        fill="none", stroke_width=cell_size*0.008, stroke='black' ))
                    if val:
                        d.append(
                            draw.Circle(xp, yp, disk_size*0.5, fill="none",
                            stroke_width=cell_size*0.008, stroke='black'))
                        dot_count += 1
                        min_x = min(min_x, xp)
                        max_x = max(max_x, xp)
                        min_y = min(min_y, yp)
                        max_y = max(max_y, yp)
```
&nbsp;  
Full Code:  
[https://github.com/PWhiddy/fish-project](https://github.com/PWhiddy/fish-project)  


<video width=640 autoplay muted loop>
  <source src="https://i.imgur.com/fVG5n1Y.mp4" type="video/mp4" />
</video>
*The generated design*

Cumulatively I only spent a few days iterating on the script, spread across several months. I was not involved in the concept or production work in any way, and after submitting it heard almost nothing until today!  
&nbsp;  
The original illustration was created by [Duane Raver](https://www.ourstate.com/duane-raver/), a man who made a career out of illustrating fish. Truly inspiring!  

![fish build 1](https://i.imgur.com/TIcjsCa.png)
*[OZ Art NWA](https://www.instagram.com/ozartnwa/)* 
![fish build 2](https://i.imgur.com/qmD5cDT.jpg)
*[OZ Art NWA](https://www.instagram.com/ozartnwa/)* 
![fish build 3](https://i.imgur.com/aLsCKMo.png)
*[OZ Art NWA](https://www.instagram.com/ozartnwa/)* 
![fish build 4](https://i.imgur.com/c6cALLv.png)
*[OZ Art NWA](https://www.instagram.com/ozartnwa/)* 
