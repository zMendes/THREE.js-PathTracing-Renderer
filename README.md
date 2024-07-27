# THREE.js-PathTracing-Renderer

Real-time PathTracing with global illumination and progressive rendering, all on top of the Three.js WebGL framework.

<h4>Quick Controls Notes</h4>

* *Desktop*: Mouse click anywhere to capture mouse, then the usual Mouse-move and WASD/QZ keys control 1st person camera. Mousewheel to zoom in and out. O and P keys toggle Orthographic and Perspective camera modes. Left/Right arrow keys control camera's aperture size (depth of field blur effect), while Up/Down arrow keys control the Focal point distance from the camera. ESC key to exit and return the mouse pointer.
* *Mobile*: Swipe to rotate 1st person camera. The 4 Large Arrow buttons control camera movement. Horizontal pinch to zoom in and out.  Vertical Pinch controls camera's aperture size (depth of field blur effect).  The 2 smaller Up/Down Arrow buttons control the Focal point distance from the camera.  Orthographic camera mode can be turned on/off through the GUI checkbox.

<h2>LIVE DEMOS</h2>

* [Geometry Showcase Demo](https://erichlof.github.io/THREE.js-PathTracing-Renderer/Geometry_Showcase.html) demonstrates some primitive shapes for ray tracing, while showing off the renderer's capabilities - global illumination, true reflections/refractions, bounce lighting, soft shadows, caustics, and near-instant image convergence at 30-60fps, on any device!

<img src="https://github.com/erichlof/THREE.js-PathTracing-Renderer/blob/gh-pages/readme-Images/1982_December_Arthur%20Appel_IBM%20Research%20Magazine.png" width=20% height=20%> <br>
Arthur Appel, from the IBM Research Employee Gallery, ca. 1982
Reprint Courtesy of IBM Corporation © <br>

<img src="https://github.com/erichlof/THREE.js-PathTracing-Renderer/blob/gh-pages/readme-Images/1983_December_Arthur%20Appel_IBM%20Research%20Magazine.png" width=20% height=20%> <br>
Arthur Appel demonstrating display architecture, from IBM Research Magazine ca. 1983
Reprint Courtesy of IBM Corporation © <br>

Many thanks to Max Campbell at IBM Research Archives for locating these rare photos and helping me to obtain permission to share them with everyone who is interested in ray tracing!  It is so nice to be able to finally put a face with the name of one of my ray tracing heroes.  Thank you Arthur Appel for your historic contributions to the field of Computer Graphics! `<br>`
`<br>`

![](readme-Images/Whitted_1979.jpg)

While working at Bell Labs and writing his now-famous paper [An Improved Illumination Model for Shaded Display](http://artis.imag.fr/Members/David.Roger/whitted.pdf), J. Turner Whitted created an iconic ray traced scene which showcased his novel methods for producing more realistic images with a computer. Beginning work in 1978, he rendered a handful of scenes featuring spheres and planes with various materials and reflectivity, so that these images would be included in his paper (which would be published in June 1980).  Then for an upcoming SIGGRAPH conference submission, Whitted decided to create an animated sequence of individual rendered images.  Thus the first ever ray traced animation was born!  This style of putting together single frames of pre-rendered images would continue through a great lineage of movies such as Tron, Toy Story, Cars, all the way to current animated feature films.

[Vintage 1979 Video: &#39;The Compleat Angler&#39; by J. Turner Whitted](https://youtu.be/0KrCh5qD9Ho)

Although this movie appears as a smooth animation, it took around 45 minutes to render each individual frame back in 1979!  Fast forward to today and using WebGL 2.0 and the parallel processing power of GPUs, here is the same iconic scene rendered at 60 times a second in your browser! : `<br>`

* [The Compleat Angler demo](https://erichlof.github.io/THREE.js-PathTracing-Renderer/Classic_Scene_Whitted_TheCompleatAngler.html) `<br>`

Thank you Dr. Whitted for your pioneering computer graphics work and for helping to start the rendered animation industry!  `<br>`
`<br>`

In 1986 James T. Kajiya published his famous paper [The Rendering Equation](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.63.1402), in which he presented an elegant and profound unifying integral equation for rendering.  Since the equation is infinitely recursive and hopelessly multidimensional, he suggests using Monte Carlo integration (sampling and averaging) in order to converge on a solution.  Thus Monte Carlo path tracing was born, which this repo follows very closely.  At the end of his paper he included a sample rendered image that demonstrates global illumination through Monte Carlo path tracing:

![](readme-Images/kajiya.jpg)

And here is the same scene from 1986, rendered in real-time: `<br>`

* [The Rendering Equation Demo](https://erichlof.github.io/THREE.js-PathTracing-Renderer/Classic_Scene_Kajiya_TheRenderingEquation.html) `<br>`
  `<br>`

The next classic ray traced scene comes from Eric Haines.  In 1987 for the SIGGRAPH Art Show, Haines presented an image of several thousand spheres arranged in his custom 3D fractal pattern, which he called 'Sphereflake'.  The fractal is generated by first placing the large root parent sphere in the center.  Then 9 smaller child spheres are placed at equidistant angles on the parent sphere's surface.  On the next iteration, those 9 child spheres become parents themselves, spawning 9 even smaller child spheres on their surfaces.  The process continues in fractal fashion, leading to an exponential increase in the amount of spheres on each iteration.

![](readme-Images/Sphereflake_Haines_1987.png)

For this demo, I chose 5 iterations of Haines' fractal pattern, which means that in the end we have: 1 root parent sphere + (9) + (9x9) + (9x9x9) + (9x9x9x9) + (9x9x9x9x9) = 66,430 spheres total!  This dense fractal structure relies on my new custom Shape_BVH builder, which instead of handling typical triangles of a triangular model, handles quadric shapes (spheres, boxes, cylinders, cones, paraboloids, etc.) for fractal and CSG models.  These simple math shape primitives are ray-tracing friendly and with the help of my BVH tree builder, it accelerates the rendering to real-time, even on your cell phone! Also, this demo allows you to change the entire Sphereflake material to common material presets, like metal, clearCoat, glass, etc. Just for fun, I included a 'Random' material option which assigns a randomized unique material type to each of the 66,430 spheres!

Here is Haines' Sphereflake fractal, path traced in real-time: `<br>`

* [Sphereflake Demo](https://erichlof.github.io/THREE.js-PathTracing-Renderer/Sphereflake.html) `<br>`
  `<br>`

<h4>Bi-Directional Path Tracing</h4>
In December of 1997, Eric Veach wrote a seminal PhD thesis paper on methods for light transport http://graphics.stanford.edu/papers/veach_thesis/  In Chapter 10, entitled Bi-Directional Path Tracing, Veach outlines a novel way to deal with difficult path tracing scenarios with hidden light sources (i.e. cove lighting, recessed lighting, spotlights, etc.).  Instead of just shooting rays from the camera like we normally do, we also shoot rays from the light sources, and then later join the camera paths to the light paths.  Although his full method is difficult to implement on GPUs because of memory storage requirements, I took the basic idea and applied it to real-time path tracing of his classic test scene with hidden light sources.  For reference, here is a rendering made by Veach for his 1997 paper:

![](readme-Images/Veach-BiDirectional.jpg)

And here is the same room rendered in real-time by the three.js path tracer: `<br>`

* [Bi-Directional PathTracing Demo](https://erichlof.github.io/THREE.js-PathTracing-Renderer/Bi-Directional_PathTracing.html) `<br>`

The following classic scene rendering comes from later in the same paper by Veach.  This scene is intentionally difficult to converge because there is no direct light, only indirect light hitting the walls and ceiling from a crack in the doorway.  Further complicating things is the fact that caustics must be captured by the glass teapot on the coffee table, without being able to directly connect with the light source.

![](readme-Images/Veach-DifficultLighting.jpg)

And here is that scene rendered in real-time by the three.js path tracer: Try moving the GUI slider to open and close the door! `<br>`

* [Difficult Lighting Classic Test Scene Demo](https://erichlof.github.io/THREE.js-PathTracing-Renderer/Bi-Directional_Difficult_Lighting.html) `<br>`

I only had the above images to go on - there are no scene dimensions specifications that I am aware of.  However, I feel that I have captured the essence and purpose of his test scene rooms.  I think Veach would be interested to know that his scenes, which probably took several minutes if not hours to render back in the 1990's, are now rendering real-time in a web browser! :-D

For more intuition and a direct comparison between regular path tracing and bi-directional path tracing, here is the old Cornell Box scene again but this time there is a blocker panel that blocks almost all of the light source in the ceiling.  The naive approach is just to path trace normally and hope that the camera rays will be lucky enough to find the light source:

* [Naive Approach to Blocked Light Source](https://erichlof.github.io/THREE.js-PathTracing-Renderer/Compare_Uni-Directional_Approach.html) As we can painfully see, we will have to wait a long time to get a decent image!
  Enter Bi-Directional path tracing to the rescue!:
* [Bi-Directional Approach to Blocked Light Source](https://erichlof.github.io/THREE.js-PathTracing-Renderer/Compare_Bi-Directional_Approach.html) Like magic, the difficult scene comes into focus - in real-time! `<br>` `<br>` `<br>`

<h3>Game Engine path tracer for Desktop and Mobile</h3>

Before I got into this world of path tracing, I was a 3D game programmer (and still am, although path tracing is consuming most of my coding time!).  My first game was way back in 1998, using OpenGL 1 and the C language, back when these new things called graphics cards were all the rage! [my old Binary Brotherz page](https://web.archive.org/web/20010405004141/http://www.binarybrotherz.com/games.html)  Although using OpenGL back then and WebGL today was/is cool, I always wanted more in terms of lighting, shadows, reflections, diffuse color sharing, etc., in my game engines that I just couldn't get from rasterizing graphics APIs.  Well, fast forward to 2019 and NVidia is releasing graphics cards dedicated to real-time ray tracing!  I couldn't have imagined this back in the 90's! However, at the time I'm writing this, NVidia is only doing specular ray tracing as a bonus feature on top of the old rasterization technique. I wanted to see if I could 'overclock' my full path tracer's convergence so that you could see the beautiful light effects in real time, being able to possibly move a game character or 1st-person camera through a path-traced dynamic game environment at 30-60 fps, even on mobile.  If you're willing to sacrifice some ultimate physical reality (like perfect converged reflected/refracted caustics), then you can have this!: `<br>`

* [Future Game Engine PathTracer Demo](https://erichlof.github.io/THREE.js-PathTracing-Renderer/GameEngine_PathTracer.html)`<br>`
  To my knowledge, this is just about as fast as I can push the path tracing engine and WebGL in general, and still retain good lighting, accurate reflections, and almost instant image convergence.  As computers get faster, this will be the heart of future game rendering engines - a simple path tracer that is just around 500 to 1000 lines of code, is easy to maintain and debug, and which gives photo-realistic real-time results! I already have some ideas for some simple 3d games that can use this technology.  I'll keep you posted! `<br>`
  `<br>`

<h2>PATH TRACED GAMES</h2>

I am pleased to announce the first ever path traced game for desktop and mobile: AntiGravity Pool!  If you've ever played American 8-ball before, then you already know how to play - except that gravity has been shut off! LOL. I tried to imagaine how our distant future descendants would enjoy the game of billiards while in the HoloDeck.  Warping the 2D classic pool table into a 3D cube presents some unique and interesting challenges for the player.  AntiGravity Pool features real-time raytraced reflections, soft shadows, and path traced global illumination from 8 light sources (which is challenging for path tracers).  Since it uses a physics engine and various custom components, I decided to create a dedicated repository for just this new game. Be sure to check it out!`<br>`

* [AntiGravity Pool game](https://erichlof.github.io/AntiGravity-Pool/AntiGravityPool.html) on Desktop, press SPACEBAR to shoot! :)
* [AntiGravity Pool project](https://github.com/erichlof/AntiGravity-Pool) `<br><br>`

Continuing my series of path traced games for desktop and mobile, I happily present: Path Traced Pong!  The iconic game of Pong holds a special place in my heart as it was my first computer game experience as a 6 year old in 1979, played on my brand new Atari 2600!  My version of Pong brings the classic into 3D, and is played inside the CG-famous 'Cornell Box'.  Path Traced Pong features real time raytraced reflections, soft shadows, transparency, dynamic light sources, and path traced global illumination.  As with AntiGravity Pool, I made a dedicated repository for just this new game. I must say, once you start playing, it's hard to stop!  I didn't realize how addictive it would become!`<br><br>`

* [Path Traced Pong game](https://erichlof.github.io/PathTracedPong/Path_Traced_Pong.html) on Desktop, click to capture Mouse
* [Path Traced Pong project](https://github.com/erichlof/PathTracedPong) `<br><br>`

In 1986 when I was 13 years old and on my Commodore 64 (I know, I'm old), Geoff Crammond released his masterpiece, The Sentinel.  This iconic game featured true 3D filled polygons (an amazing feat running on underpowered 80's hardware!) and had a haunting look and atmosphere like no other before it (or after).  This was the first game that I played that truly immersed me, surrounding the player from all angles with its sterile, other-worldly environment.  I've always wanted to pay homage to my favorite game of all time, while maybe adding some of my personal path tracing touch to it.  So it is with much joy that I present, The Sentinel: 2nd Look.  This fully path traced remake contains a random landscape generator (which I had to figure out from looking at the classic over several months), an added day cycle, pixel-perfect raytraced shadows on the terrain and game objects, object self-shadowing, and true raytraced reflections on the white/black connector panels of the landscape.  `<br>`
`<br>`
Creating this remake forced me to figure out how to make a dynamic top-level BVH over many moving, rotating game objects/models, each with their own unique BVHs for their own triangle geometry.  I'm happy to report that not only does my new system work, it can completely rebuild and update the whole top-level BVH in a split second, allowing for more complex, path traced dynamic game environments!  As of now, this project is a W.I.P. (gameplay and game logic to be added soon), but I just wanted to share this passion project of mine, as well as the technical major step forward (in BVH technology) that will allow a wider range of real time games and applications to be path traced right inside your browser!
`<br><br>`

* [The Sentinel: 2nd Look (W.I.P.) game](https://erichlof.github.io/TheSentinel-2nd-Look/TheSentinel_2nd_Look.html) on Desktop, click to capture Mouse
* [The Sentinel: 2nd Look project](https://github.com/erichlof/TheSentinel-2nd-Look) `<br>`

<br>
<br>

<h2> My New YouTube series! The <em>Joy</em> of Ray Tracing </h2>
You may be interested to know that I have started my own video tutorial series on YouTube all about ray tracing!  It's called <strong> The <em>Joy</em> of Ray Tracing </strong> and together we will make several different kinds of ray tracers - <strong><em> from scratch </em></strong>!

<br>
<br>

![JoyOfRayTracing_thumbnail7](https://user-images.githubusercontent.com/3434843/217344929-bf083956-242d-446f-90b2-0bc2d33b579c.png)
![JoyOfRayTracing_thumbnail](https://user-images.githubusercontent.com/3434843/217345576-d1225757-5bf8-4987-856f-f597de98f48d.png)
![JoyOfRayTracing_thumbnail4](https://user-images.githubusercontent.com/3434843/217345617-3910befe-23a5-4849-a3b4-beae6a1011f3.png)

<br>

[My YouTube Channel intro](https://www.youtube.com/watch?v=bI9MzhqBcH4)

<br>

[The Joy of Ray Tracing Video Series](https://www.youtube.com/playlist?list=PL3NuKUKozjGTJRKB4duG2dxpyUu_Pj7jV)

<br>

If you want to know how all of my ray tracing and path tracing demos work under the hood, then look no further.  And when I say 'from scratch', I really mean it!  Chapter 0 of the video series is all about installing and setting up a productive coding environment for the web (mainly VS Code and some helpful plugins), the basics of HTML/CSS/JavaScript, GitHub basics plus how to use GitHub's integration with VS Code to easily create your 1st GitHub repo, how to use VS Code's awesome Live Server plugin to automatically run and test your code on any device on your WiFi network and to instantly see the visual results of your code changes, and finally an introduction to the web's Canvas element that allows us to draw our 1st pixels to the screen! `<br>`

Therefore, if you're an experienced web developer who already has a working coding environment and who is comfortable with HTML/CSS/JavaScript basics, then you can safely skip Chapter 0 and meet us in Chapter 1.  In this chapter we start talking about concepts and techniques that are specific to Ray Tracing.  As of this Readme update today (2/7/2023), I am still in the process of filming the rest of the videos for Chapter 1.  But by the end of this chapter, you will have created your first basic ray tracer that runs on any device!  The following chapters will explore more intermediate techniques and our ray tracers will get more sophisticated (and awesome!).  Along the way we'll also learn about the fascinating history of Ray Tracing, which is over 55 years old by now! (wow, something that's actually older than I am, lol) `<br>`

As we go through the series making our ray tracers, I code every single line on-screen, and then all of the code (and live demos!) will be placed in a dedicated GitHub repository with the same name (Joy of Ray Tracing):

[The Joy of Ray Tracing companion code repository](https://github.com/erichlof/Joy-of-Ray-Tracing)
`<br>`

I hope to see you over on YouTube! I'm really excited to have you along for the ride! `<br>`
So...

<h3> Let's get started on our journey to discover... The <em> Joy </em> of Ray Tracing!
</h3>

<br>
<br>
<br>
A random sample rendering from the three.js pathtracing renderer as it was back in 2015!
<br>

![](readme-Images/threejsPathTracing.png)

<h2>FEATURES</h2>

* Real-time interactive Path Tracing at 30-60 FPS in your browser - even on your smartphone! ( What?! )
* First-Person camera navigation through the 3D scene.
* When camera is still, switches to progressive rendering mode and converges on a highest quality photo-realistic result!
* The accumulated render image will converge at around 500-3,000 samples (lower for simple scenes, higher for complex scenes).
* My custom randomized Direct Light targeting now makes images render/converge almost instantly!
* Both Uni-Directional (normal) and Bi-Directional path tracing approaches available for different lighting situations.
* Support for: Spheres, Planes, Discs, Quads, Triangles, and quadrics such as Cylinders, Cones, Ellipsoids, Paraboloids, Hyperboloids, Capsules, and Rings/Torii. Parametric/procedural surfaces (i.e. terrain, clouds, waves, etc.) are handled through Raymarching.
* Constructive Solid Geometry(CSG) allows you to combine 2 shapes using operations like addition, subtraction, and overlap.
* Support for loading models in .gltf and .glb formats
* BVH (Bounding Volume Hierarchy) greatly speeds up rendering of triangle models in gltf/glb format (tested up to 800,000 triangles!)
* Current material options: Metallic (mirrors, gold, etc.), Transparent (glass, water, etc.), Diffuse(matte, chalk, etc), ClearCoat(cars, plastic, polished wood, billiard balls, etc.), Translucent (skin, leaves, cloth, etc.), Subsurface w/ shiny coat (jelly beans, cherries, teeth, polished Jade, etc.)
* Solid transparent objects (i.e. glass tables, glass sculptures, tanks filled with water or other fluid, etc) now obey the Beer-Lambert law for ray color/energy attenuation.
* Support for PBR materials on models in gltf format (albedo diffuse, emissive, metallicRoughness, and normal maps)
* Diffuse/Matte objects use Monte Carlo integration (a random process, hence the visual noise) to sample the unit-hemisphere oriented around the normal of the ray-object hitpoint and collects any light that is being received.  This is the key-difference between path tracing and simple old-fashioned ray tracing.  This is what produces realistic global illumination effects such as color bleeding/sharing between diffuse objects and refractive caustics from specular/glass/water objects.
* Camera has Depth of Field with real-time adjustable Focal Distance and Aperture Size settings for a still-photography or cinematic look.
* SuperSampling gives beautiful, clean Anti-Aliasing (no jagged edges!) `<br>`
  `<br>`

<h3>Experimental Works in Progress (W.I.P.)</h3>

The following demos show what I have been experimenting with most recently.  They might not work 100% and might have small visual artifacts that I am trying to fix.  I just wanted to share some more possible areas in the world of path tracing! :-) `<br>`

Some pretty interesting shapes can be obtained by deforming objects and/or warping the ray space (position and direction) around these objects.  This demo applies a twist warp to the spheres and mirror box and randomizes the positional space of the top purple sphere, creating an acceptable representation of a little cloud. `<br>`

* [Ray/Object Warping Demo](https://erichlof.github.io/THREE.js-PathTracing-Renderer/Ray_Warping.html)`<br>`
  `<br>`

Normally, all of my demos on this repo use a single pixel sample per frame, which keeps the framerate high (30-60 fps) for all devices, even cell phones. But a naive implementation of just 1 pixel sample per frame results in large amounts of distracting noise. I use my custom-built denoiser to smooth out and quiet down the noise, giving much better picture quality for all devices at interactive framerates. However, several users have requested demos of more than 1 sample per animation frame, because they have more powerful systems with the latest dedicated GPUs, and they want to utilize these resources to the max. So here are a couple of demo examples that allow multiple pixel samples per frame. The first demo is the Geometry Showcase Demo, but with a slider that lets you crank up the pixel samples anywhere from 1 to 100. The second demo is a similar scene, but with dynamic, moving objects like you might have in a game, and also lets you adjust the number of samples per frame. The reason why these multiSPF demos are here in the experimental section is because they do not have the denoiser in place yet. My denoiser relies on the number of samples (which has previously been 1) in its calculations over time to smooth out and converge the image. I will have to convert the denoiser so that it will work properly with multi-sample scenes like these:

* [MultiSamples-per-Frame Demo](https://erichlof.github.io/THREE.js-PathTracing-Renderer/MultiSamples_Per_Frame.html)`<br>`
* [MultiSPF Dynamic Scene Demo](https://erichlof.github.io/THREE.js-PathTracing-Renderer/MultiSPF_Dynamic_Scene.html)`<br>`
  `<br>`

When rendering/raytracing Terrain, you can either raymarch a Perlin noise texture (as I have demonstrated in the above Terrain_Rendering and Planet_Rendering demos), or you can just load in a large pre-existing triangle terrain mesh and raytrace it in the traditional way.  Both have their advantages and disadvantages. However, if you want to go the classical raytracing route, to make the land contours a little more convincing, there needs to be a *lot* of triangles!  The following WIP preview demo uses the BVH acceleration structure to load in and quickly render a huge terrain mesh consisting of no less than 734,464 triangles!  It really pushes my BVH code to the max - we're pretty near a million triangles here, pathtracing in WebGL!  For now I just stuck a checker texture across the terrain and the environment is simply a large skylight dome.  But the good news is that it doesn't crash the browser, and it runs slightly above 20 fps even on my humble laptop - it's amazing that all of this is happening inside a browser webpage!  Note: because of the large BVH data set that needs to be built at startup, this demo might take a few seconds to compile - please be patient, it's worth the wait! ;-) `<br>`

* [BVH Large Terrain Demo (W.I.P.)](https://erichlof.github.io/THREE.js-PathTracing-Renderer/BVH_Terrain.html) *Note: due to the large data set, it might take a few seconds or more to load and compile*`<br>`
  `<br>`

Inspired by an older Shadertoy demo by user koiava that I came across - https://www.shadertoy.com/view/MtBSzd - I noticed that my mobile device didn't have any problems when trying that particular demo with 1000 triangles.  I copied / edited / optimized the traversal code and then, I did the unthinkable (for me anyway) - I sent down over 2 million triangles to the engine to be raytraced, then raytraced yet again for the reflection/shadow ray pass (so effectively 4,200,000 triangles in a single frame, and .... my Samsung 9 still runs at nearly 60 fps!  It didn't even blink an eye.  Compilation takes maybe 1 second.  I couldn't believe what I was seeing at first. `<br>`
`<br>`
![](readme-Images/Grid_on_Mobile.png)

* [Grid Acceleration Demo (W.I.P.)](https://erichlof.github.io/THREE.js-PathTracing-Renderer/Grid_Acceleration.html) `<br>`

A technical note about what you are seeing: The data arrives to the fragment shader through a 1024x1024 heightmap texture (I randomly chose a DinoIsland.png heightmap, but it can be anything, even a realtime video texture).  The acceleration structure handles sampling the texture and stepping the ray through each sample cell.  The number of cells is up to you.  At first I tried 32x32 cells, and each cell is a square, and each of the 4 corners of that square is a vertex that is used by 2 triangles sandwiched together back-to-back.  So to get the number of triangles that you must raytrace, you take 32 cells width times 32 cells height and remember that each square cell contains 2 triangles, so multiply all that times 2, so 32Wx32Hx2t  which is 2048 triangles representing the polygon heightmap.  Now 2048 triangles sounds like a lot, and it is for raytracing, but the result mesh looks like an old-school low-poly terrain - it is not detailed enough.  On a whim, I tried a resolution of 1024, so each little texel of the 1024x1024 source texture image has its own quad cell, and 2 triangles for every one of those quad cells.  So now we have 1024x1024x2, or 2,097,152 triangles every frame!  And since the grid looks up the texture to get the triangle vertices every frame, you can animate the height/depth of the displacement as well as even play an HD video (saved as textures) with an embossed-effect on the terrain in real time!
    Oddly, it turns out that my Mobile device (a Samsung S9) trounces my laptop at this demo.  The humble old laptop achieves maybe 20fps, whereas the newer smartphone rocks at 60fps. It may have to do with the cheap integrated graphics of my laptop, but in any case, this is a true testament to the power of modern smartphone GPUs!

<h2>Updates</h2>

* March 8, 2024: Over the last year on and off, I have been working on an efficient method to ray trace a Torus.  At long last, I present the Cheap Torus - my custom approximation to the beautiful Torus shape.  Historically, the Torus has always been difficult and expensive to ray trace, because it is a quartic (degree 4) shape which requires you to solve for the roots of a quartic equation.  I've tried many different quartic solvers out there (I even tried ray marching a torus signed distance field, or sdf) and they are either too finnicky to use and get looking right, or too expensive to efficiently run on normal hardware and mobile.  And then after doing all that work, you still get rendering artifacts like gaps and thin cracks at larger scales, due to limited GPU shader float precision.  So, determined to come up with a better solution, I set out on a long journey to efficiently render a Torus that would be air-tight at all scales.  Along this rabbit hole (ha), I discovered a cool trick of combining 2 simple quadric shapes (only degree 2), namely the Hyperboloid (hourglass) and the truncated Sphere (both top and bottom removed).  When these 2 simple shapes are combined in just the right way, we get a very close approximation to the Torus, free of gap artifacts at all scales and very fast to render.  The Hyperboloid (hourglass) is used for the inner part of the Torus, while the top-and-bottom-truncated Sphere is used for the outside of the Torus.  Where these 2 shapes meet, there was an annoying seam, so I hid this seam by slightly bending the surface normals reported back from the 2 different shapes.  This results in a smooth, rounded-looking surface on the very top and bottom of the Torus.  Now if you fly the camera up really close along the side edge of my Cheap Torus, the illusion might be broken, but at most viewing angles, it looks perfect. You can even fly your camera through the inside of a large glass Torus!  The Cheap Torus demo allows you to change the Torus' scale, roatation, hole size, and material.  Now we can finally have multiple, air-tight Torii all over our scenes without worrying about precision artifacts or performance costs!
* February 15, 2023: I developed a new custom system for handling large amounts of geometry.  For now I’m simply calling it the ‘Shapes BVH’.  Rather than dealing with the usual glTF models with their various triangle primitives and vertices, my new system instead builds a BVH (which is an axis-aligned bounding box binary tree) around large amounts of simple primitive shapes like spheres, boxes, cylinders, etc.  Using these ray tracing-friendly shapes, we can build up scenes of almost any complexity.  Just like traditional triangular models, each small building-shape (primitive) can have its own unique transform (scale, rotation, position) as well as its own material properties.  Unlike triangular models, which are, in the end, just an approximation to the original shape/surface, the Shapes BVH scenes are rendered with pixel-perfect accuracy (because they are composed of smaller mathematical, analytic shapes).  Certain scenes call for the traditional triangle model/BVH system (like the Stanford Bunny, Stanford Dragon and Damaged Helmet glTF scenes). But other types of scenes requiring a lot of simple primitives don’t really work well with the older triangle BVH system – especially on mobile, where triangle intersection is a performance bottleneck.  By using the Shapes BVH instead, now we can have arbitrarily complex and densely populated scenes that can be path traced in real time at 30-60 fps, even on your phone!  Better yet, since these scenes are made from simple quadric shapes, you can just call familiar three.js JavaScript commands (like ‘new THREE.SphereGeometry’, ‘new THREE.BoxGeometry’, ‘new THREE.PhysicalMaterial’, and ‘new THREE.Mesh’) to construct any scene you can imagine. To see this new system in action, check out the ‘Invisible Date’, ‘Sphereflake’, and ‘Cube Fractal 3D’ demos. The Sphereflake and Cube Fractal demos both showcase the sheer amount of geometry you can have in a scene, and still have it run smoothly on any device, (even your cell phone!).  The Invisible Date scene showcases the different kind of shapes you can use, the various transformations you can do on those shapes, and the various physical material properties you can assign to each and every small shape that is used to build up the final scene.
* January 17th, 2023: New Year - New rendering technique!  Major update across the entire repo (and also my path traced games) to how transparent (glass, water, etc) and clearCoat (billiard balls, polished wood, car paint, etc) materials are handled.  If you've followed my project from the beginning, you might remember that I have gone back and forth on the technique used to efficiently render objects with these transparent/semi-transparent materials. One way is to use Monte Carlo integration and essentially 'flip a coin' every time a ray encounters a transparent surface.  If 'heads', the ray reflects or bounces off of the surface - if 'tails', the ray refracts or transmits through the surface.  Using a progressive renderer, this method will eventually converge to the correct result, which is a double-image on the surface.  Half of it is the reflection we see, and the other half is the world beyond the surface that we can also see at the same time (think of the double-image you get when looking at a shallow angle through a window).  Although this technique works well, the major drawback is that you get 2 noisy images on the transparent surface, due to the Monte Carlo randomization of the rays' path choices.  On the other hand, we can do away with the random decisions (and noise), and instead spawn 2 rays when we encounter these transparent surfaces.   Although this creates slighty more shader complexity (inside the tight 'bounces' loop), the reward is definitely worth the cost because now we have a smooth, non-noisy, steady double-image appearing on all transparent surfaces.  The effect is most noticeable when moving the camera around and looking at these surfaces from different angles.  Now you see a reflection perfectly blended with a transmission (or refraction), which more accurately resembles the real world transparent objects that we see every day (and take for granted!). The reason I have returned to this double ray spawning method (1 reflection / 1 transmission ray) after having it the other Monte Carlo way for a year, is that *this* time around, I figured out how to implement it better.  Not only does it look smoother and more correct than my previous attempts at this technique, but it is also more performant.  Enjoy looking at (and through!) all of the transparent and clearCoat materials! :)
* March 4th, 2022: Added a new technique to my bag of tricks - Many Model Instances! Check out the new BVH_Model_Instancing demo which loads a glTF model, but instead of rendering its triangles as triangles, we replace each triangle with a brand new smaller instance of that model! For instance, the demo opens with the famous Utah Teapot model, containing ~1,000 triangle faces, and each of those faces is replaced with another small teapot with its own 1,000 triangles. So that means 1,000 tris * 1,000 meshes = ~1 Million polygons, each fully path traced in real time in the browser! The heavier Stanford Dragon model has 100,000 triangles - so 100,000 tris * 100,000 meshes = 10 Billion polygons in your browser, even on a cell phone! To add to the craziness, each of the smaller instances has its own unique transform matrix within its small AABB bounding box. That means that we can have thousands of rotated, positioned, and scaled models, all doing their own thing in real time! I could see this new technique being used for a grass/foilage system, each instance having its own transform and lighting in real time. In other news, I did a fairly large refactor and clean-up for my entire repo.  All duplicated JS code has been removed (thousands of lines of code between demos), without removing any functionality. In fact, *more* functionality has been added to all demos in terms of helpful GUI elements (on both desktop and mobile devices) as well as a more unified/streamlined codebase between the JS init files for each demo - a win-win situation all around! ;-)
* October 28th, 2021: Major breakthrough! If you have been following my project here on GitHub, you were probably painfully aware that my heavier BVH demos (glTF triangle models) simply would not work at all on mobile devices like tablets and cell phones. The BVH demos wouldn’t even compile, or would just crash after a few seconds, even on my (2021) Samsung Galaxy S21 - and worse yet, there were no errors in the console. I had no idea why these particular demos would not work. This bug has been haunting me for years, literally. Well, a recent Twitter post by Garrett Johnson caught my eye because he was having similar problems when developing his BVH [system](https://github.com/gkjohnson/three-mesh-bvh) for three.js. We got into a discussion about it, chalked it up to lack of mobile precision, and I was ready to give up. Not Garrett however; he is a robotics expert at NASA and he knows all about low level stuff and machine precision. He couldn’t let this go, because mobile devices *should* have as much floating point precision as desktops, as reported. Yet there was still a discrepancy. Many posts later and a multiple-expert discussion later (Ken Russell from Khronos, Romain Guy from Google, someone who knows mobile chips, etc…) it was discovered that for some reason (which I can’t quite fathom), the designers of Adreno mobile chips treat all members of structs{} as mediump or lowp precision, even if I specifically put 'highp' precision at the beginning of my shaders! I had structs all over the place, stuff like Ray{vec3 origin; vec3 direction;} Intersection{ vec3 hitPos, etc.}, BVHNode{vec4 data, etc.}. So the problem was that when traversing the very precise BVH, my mobile devices were unable to even start because floating point round-off errors were flying all over the place! All this was happening unbeknownst to me because I thought everything was in highp precision, like I had instructed on every shader - not so! As soon as I got rid of all my structs, and unpacked them into seperate variables, like vec3 rayOrigin instead of ray.origin for example, everything magically started to work! It was such a joy to be able to see the Stanford Bunny (30,000+ triangles) path traced at 60 fps,… on my phone!  Many thanks to Garrett Johnson (@garrettkjohnson on Twitter) for helping me solve this multi-years-long bug!

<h2>TODO</h2>

* For simple scenes without gltf models, instead of scene description hard-coded in the path tracing shader, let the scene be defined using familiar Three.js mesh creation commands (2/6/23 made progress in this area with my new 'Shapes BVH' system - the 'Invisible Date' demo above shows how you can use simple Three.js commands to build up this classic scene. Each small object has a unique THREE.Geometry, THREE.PhysicalMaterial, and THREE.Mesh/THREE.Object3D for placing, scaling, and rotating the object).  Next step is to be able to create and load any arbritrary scene that uses the standard, simple Three.js library calls for scene construction.
* Figure out how to save pathtraced results into texture maps to be used for lightmaps (optimized baked lighting for static geometry)
* Dynamic Scene description/BVH rigged model animation streamed real-time to the GPU path tracer (1/21/21 made progress in this area by working on my new game The Sentinel: 2nd Look.  Featues a dynamic top-level BVH that can change and update itself every animation frame)`<br>`

<h2>ABOUT</h2>

* This began as a port of Kevin Beason's brilliant 'smallPT' ("small PathTracer") over to the Three.js WebGL framework.  http://www.kevinbeason.com/smallpt/  Kevin's original 'smallPT' only supports spheres of various sizes and is meant to render offline, saving the image to a PPM text file (not real-time). I have so far added features such as real-time progressive rendering on any device with a browser, FirstPerson Camera controls with Depth of Field, more Ray-Primitive object intersection support (such as planes, triangles, quadrics, CSG shapes, etc.), loading and rendering glTF triangle models, static and dynamic GPU BVH acceleration structures, and support for additional materials like ClearCoat and SubSurface. `<br>`

More examples, features, and content to come!
