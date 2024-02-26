# svitlyna

A "full-color" image viewer for the Uxn/Varvara 8-bit computer. This is mostly an experiment and an excuse for me to play around with [dithering](https://en.wikipedia.org/wiki/Dither) and [color quantization](https://en.wikipedia.org/wiki/Color_quantization).

> The word "svitlyna" is a Ukrainian word for "picture" or "photograph". The "i" is pronounced like "free", "y" is pronounced like in "system".

Note that the Varvara computer specification only supports a palette of 4 colors, however those colors can be selected by the program. This is not enough to cover the whole RGB color space no matter how hard you try, but Svitlyna still tries her best.

See the [showcase](showcase/README.md) for example images.

## Features
* [QOI](qoi.txt) file format support
* [Farbfeld](https://tools.suckless.org/farbfeld/) image format support
* Current size limit is **4096 pixels maximum width**
* **Color quantization** using the median-cut algorithm
  * Choice of preset color palettes in addition to the algorithm above
* **Dithering** (Sierra Lite algorithm)
* Can read an image piped through console/standard input (however only in grayscale)

## Usage
There is currently no friendly graphical UI. There is also no file picker from within the program.

To display an image, provide a filename as an argument through the command-line:
```
uxn11 svitlyna.rom image.ff
```

Press [UP] or [DOWN] to browse color palettes. 

Press [A], aka Ctrl on desktop keyboard, to automatically pick a color palette based on the image, but note that this is currently slow. 

Press [B] aka Alt to switch render modes. You can enable or disable dithering this way. For the auto-color mode, there are extra modes with slightly different rendering.

## To-do / ideas

* TGA file support
* Proper color -> grayscale convert
* Downscaling
  * currently the image happily renders huge images, however performance is terrible
* File picker, UI
* Custom palette picker
  * Magic wand tool: click on 4 points of the image to say "these are the colors I want"
* Export to TGA, CHR, theme files
  * this could make this genuinely useful within the Uxn ecosystem rather than just a toy
* Other color picking algorithms: popularity sort?
* Other color picking algorithms, see below

## Some notes on color

If you look at the [showcase images](showcase/README.md) you might notice that the median cut algorithm can produce somewhat washed-out colors. This is because this algorithm attempts to find the *"average"* colors, which can approximate a majority of the pixels. 

This is good when you don't do any dithering, however, with dithering, this may not be necessarily the best approach. A simple color preset like Red-Green-DarkBlue-White can achieve arguably better results.

Think of the RGB color space as a 3D cube. If you have one color, that's a point within the cube. If you have two colors, that's two points, however with dithering we can interpolate between the two colors, thus we get a line segment. With three colors we form a triangle, and with the 4 colors of Varvara we get a tetrahedron of colors.

However, you cannot "fill" the color cube with a tetrahedron. The closest you can get is by picking "extreme" colors that cover a large volume, and letting dithering take care of the colors within it. The optimal [simplex](https://en.wikipedia.org/wiki/Simplex) is dependant on the pixel color values within a given image.

So mathematically this becomes a problem of finding a tetrahedron, embedded within a cube, which covers the maximum number of color value points from the given image. But, I don't know how to solve this yet.
