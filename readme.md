# Graphic filters (image_processor)
A console application that allows to apply various filters to images, similar to filters in popular graphic editors.


## Supported image format

The input and output image files must be in [BMP](http://en.wikipedia.org/wiki/BMP_file_format) format.

The BMP format supports quite a lot of variations, but in this task will be used
24-bit BMP without compression and without a color table. Type of used `DIB header` - `BITMAPINFOHEADER`.

An example of a file in the desired format is available in the Wikipedia article [in the "Example 1" section](https://en.wikipedia.org/wiki/BMP_file_format#Example_1)
and in the folder [examples](examples).

When testing, be sure to pay attention to the fact that the test image was saved in a 24-bit BMP.

## Format of command line arguments

Description of the format of command line arguments:

`{name} {path to a input file} {path to a output file}
[-{filter name 1} [filter parameter 1] [filter parameters 2] ...]
[-{filter name 2} [filter parameter 1] [filter parameter 2] ...] ...`

## Compilation

`cmake -B build -S . -D CMAKE_BUILD_TYPE=Release`

`cmake --build build `


### Example
`./image_processor input.bmp /tmp/output.bmp -crop 800 600 -gs -blur 0.5`

In this example
1. An image is loaded from a file `input.bmp`
2. It is cropped to an image with the beginning in the upper left corner and the size of 800x600 pixels
3. Transfers to shades of gray
4. A blur with sigma 0.5 is applied
5. The resulting image is saved to a file `/tmp/output.bmp`

The filter list may be empty, then the image must be saved unchanged.
Filters are applied in the order in which they are listed in the command line arguments.

## Filters

Crop

`-crop width height`

Grayscale

`-gs`

Negative

`-neg`

Sharpening

`-sharp`

EdgeDetection

`-edge threshold`

GaussianBlur

`-blur sigma [kernel_radius]`

Median
`-median kernel_radius`


#### Crop (-crop width height)
Crops the image to the specified width and height. The upper left part of the image is used.

If the requested width or height exceeds the dimensions of the original image, the available part of the image is given.

#### Grayscale (-gs)
Converts an image to grayscale using the formula

![encoding](https://latex.codecogs.com/svg.image?R'%20=%20G'%20=%20B'%20=0.299%20R%20&plus;%200%20.587%20G%20&plus;%200%20.%20114%20B)

#### Negative (-neg)
Converts an image to a negative using the formula

![encoding](https://latex.codecogs.com/svg.image?R'%20=%201%20-%20R,%20G'%20=%201%20-%20G,%20B'%20=%201%20-%20B)

#### Sharpening (-sharp)
Sharpening. It is achieved by using a matrix

![encoding](https://latex.codecogs.com/svg.image?%5Cbegin%7Bbmatrix%7D%20&%20-1%20&%20%20%5C%5C-1%20&%205%20&%20-1%20%5C%5C%20&%20-1%20&%20%20%5C%5C%5Cend%7Bbmatrix%7D)

#### Edge Detection (-edge threshold)
Border selection. The image is converted to grayscale and a matrix is applied

![encoding](https://latex.codecogs.com/svg.image?%5Cbegin%7Bbmatrix%7D%20&%20-1%20&%20%20%5C%5C-1%20&%204%20&%20-1%20%5C%5C%20&%20-1%20&%20%20%5C%5C%5Cend%7Bbmatrix%7D)

Pixels with a value greater than `threshold` are colored white, the rest are black.

#### Gaussian Blur (-blur sigma)
[Gaussian Blur](https://en.wikipedia.org/wiki/Gaussian_blur),
parameter – sigma.

The value of each of the pixel colors `C[x0][y0]` is determined by the formula

![encoding](https://latex.codecogs.com/svg.image?C%5Bx_0%5D%5By_0%5D%20%3D%20%5Csum_%7Bx%3D0%2Cy%3D0%7D%5E%7Bwidth-1%2C%20height-1%7DC%5Bx%5D%5By%5D%5Cfrac%7B1%7D%7B2%5Cpi%5Csigma%5E2%7De%5E%7B-%5Cfrac%7B%5Cleft%7Cx_o-x%5Cright%7C%5E2%20%26plus%3B%20%5Cleft%7Cy_o-y%5Cright%7C%5E2%7D%7B2%5Csigma%5E2%7D%7D)
