---
layout: post
mathjax: true
title: Creating Mosaics Sorted by Visual Similarity using CLIP Embeddings
--- 

Since the recent launch of [shaderpark](https://shaderpark.com), a lot of new artwork has been created on the site. Since we have relatively high resolution images available for each piece (1000+ at the time of writing), I think it would be fun to compile all of the shaders into a single mosaic. 

My first attempt looks like this:
<br>

![random_mosaic](https://i.imgur.com/9yHPcff.jpg)
  

<br>    
Pretty neat! The images are in no particular order however. It would be cool if we could group visually similar images together. Beyond grouping by simple features like mean color or brightness, we can generate more semantically meaningful features for an image using the embeddings from a vision model. [CLIP](https://github.com/openai/CLIP) is a solid choice for robust embeddings across a wide variety of data because it was trained from diverse data scraped from the internet. This is important because these shaders look very different than the real-life images found in a dataset like imagenet. 
<br>

If CLIP generates hundreds of features per image, we still need to reduce these down to just 2 in order to sort the images on a 2D grid. So as the simplest method possible, let's take the first 2 PCA components. These only capture less than 10% of the variance for the embeddings of this dataset of images, but hopefully still contain enough information to produce an interested sort. Now we have a 2D point for each image, but these will not be evenly distributed into a nice grid. To fix them onto a grid while maintaining some of their relative positioning information, we will sort them in two stages. First, sort them on their X coordinate (the first PCA component). Then we will divide the sorted images into buckets for each row, and sort them within each bucket/row on their Y coordinate (second PCA component). 
<br>

Here's the result:
### Sorted Mosaic
![mosaic_sorted](https://i.imgur.com/DT3Lwnm.jpg)
<br>
<br>
<br>
Cool! Without zooming in, it's hard to see the effects of the sorting other than that white spheres tend to be gathered in the lower right. By zooming in, we can see some interesting clusters:
<br>

### Round Blobs
![round_blobs](https://i.imgur.com/uI0Xs9F.png)
<br>

### Distorted Textures
![distorted_textures](https://i.imgur.com/FsS23vM.png)
<br>

### Characters and Faces
![characters_and_faces](https://i.imgur.com/5iOFi7M.png)
<br>

### Primitive Shapes
![primitive_shapes](https://i.imgur.com/DVqRXoP.png)
<br>

### Piano Keys
![piano_keys](https://i.imgur.com/4LTqGxp.png)
<br>

To be clear, if the goal was simply to extract clusters of shader types, clustering the embeddings using something like k-means would certainly produce much more well-defined clusters than the ones shown here. However we are also applying the additional constraint that all images must be placed onto a fixed 2D grid. I think the fact that clusters are still visible despite this much distortion of their original embeddings is pretty amazing!
<br>
<br>
The code for creating sorted mosaics like this has been released on github:

[https://github.com/PWhiddy/sp-mosaic](https://github.com/PWhiddy/sp-mosaic)

### UPDATE (6/13/22). 
I've swapped out PCA with UMAP, here's the result:  
  
![umap_grid](https://i.imgur.com/OHsMlpc.jpg)
