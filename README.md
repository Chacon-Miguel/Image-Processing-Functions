# Image-Processing-Functions
Group of functions to manipulate image files

## Contents of Each File:



## Digital Image Representation and Color Encoding
While digital images can be represented in myriad ways, the most common has endured the test of time: a rectangular mosaic of pixels -- colored dots, which together make up the image. An image, therefore, can be defined by specifying a _width_, a _height_, and an array of _pixels_, each of which is a color value. This representation emerged from the early days of analog television and has survived many technology changes. While individual file formats employ different encodings, compression, and other tricks, the pixel-array representation remains central to most digital images.

For this lab, we will simplify things a bit by focusing on grayscale images. Each pixel's brightness is encoded as a single integer in the range `[0,255]` (1 byte could contain 256 different values), `0` being the deepest black, and `255` being the brightest white we can represent. 

We'll represent an image using a Python dictionary with three keys:

`width`: the width of the image (in pixels),
`height`: the height of the image (in pixels), and
`pixels`: a Python list of pixel brightnesses stored in row-major order (listing the top row left-to right, then the next row, and so on)

For example, consider this $2 \times 3$ image (enlarged here for clarity):
DON'T FORGET TO ADD THE IMAGE

This image would be encoded as the following instance:
```
i = {'height': 3, 'width': 2, 'pixels': [0, 50, 50, 100, 100, 255]}
```

## Filters Implemented
### Inversion Filter
reflects pixels about the middle gray value (0 black becomes 255 white and vice versa).

### Correlation
Given an input image $I$ and a kernel $k$, applying $k$ to $I$ yields a new image $O$ (perhaps with non-integer, out-of-range pixels), equal in height and width to $I$, the pixels of which are calculated according to the rules described by $k$.

The process of applying a kernel $k$ to an image $I$ is performed as a correlation: the brightness of the pixel at position $(x, y)$ in the output image, which we'll denot as $O_{x,y}$ (with $O_{0,0}$ being the upper-left corner), is expressed as a linear combination of the brightness of the pixels around positions $(x, y)$ in the input image, where the weights are given by kernel k.

As an example, let's start by considering a $3 \times 3$ kernel:

IMAGEEEEEEEEEE

When we apply this kernel to an image $I$, the brightness of each output pixel $O_{x,y}$ is a linear combination of the brightness of the 9 pixels nearest to $(x,y)$ in $I$, where each input pixel's values is multiplied by the associated value in the kernel:

IMAGEESSSSSSSSSSSSSSSSSSSS


#### Edge Effects
When computing the pixels at the perimeter of $O$, fewer than 9 input pixels are available. For a specific example, consider the top left pixel at position $(0,0)$. In this case, all of the pixels to the top and left of $(0,0)$ are out-of-bounds. We have several options for dealing with these effects:

* One option is to treat every out-of-bounds pixel as having a value of 0.
* Another is to extend the input image beyond its boundaries, explained in the next paragraph.
* Yet another is to wrap the input image at its edges, explained in the paragraph after that.

If we want to consider these out-of-bounds pixels in terms of an extended version of the input image, values to the left of the image should be considered to have the values from column 0, values to the top of the image should be considered to have the values from row 0, etc., as illustrated in the following diagram, where the bolded pixels in the center represent the original image, and the pixels outside that region represent the logical values in the "extended" version of the image (and note that this process continues infinitely in all directions, even though a finite number of pixels are shown below:

IMAGEEEEEEEEEEEEEEE

If we want to wrap the input image at its edges, that means trying to get values beyond the left edge of the image should return values from the right edge, but from the same row. Similarly, trying to get values beyond the top edge of the image should return values from the bottom edge, but from the same column. We can think of it as the image being 'tiled' in all four directions:

IMAGEEEEEEEEEEEEEEE

The thick black square above indicates the original image, and the copies are an illustration of the wrapping behavior (but they are not part of the actual image). Note that this only shows one copy of the image in each direction but we would like the wrapping to extend infinitely in all directions.

To accomplish the goal of switching between these different edge behaviors, you may wish to implement an alternative to get_pixel with an additional parameter for the out-of-bounds behavior, which expects one of the strings "zero", "wrap", or "extend". The function would return pixel values from within the image normally but handle out-of-bounds pixels by returning appropriate values accordingly as discussed above rather than raising an exception. If you do this, other pieces of your code can make use of that function and not have to worry themselves about whether pixels are in-bounds or not.

### Blurring Filter
we will implement a box blur, which can be implemented via correlation using the 'extend' behavior for out-of-bounds pixels 3. For a box blur, the kernel is an $n\times n$ square of identical values that sum to 1.

### Sharpening Filter
Next, we'll implement the opposite operation, known as a sharpen filter. The "sharpen" operation often goes by another name which is more suggestive of what it means: it is often called an unsharp mask because it results from subtracting an "unsharp" (blurred) version of the image from a scaled version of the original image.

More specifically, if we have an image $(I)$ and a blurred version of that same image $(B)$, the value of the sharpened image $S$ at a particular location is:

$$
S_{x,y} = 2I_{x,y}-B_{x,y}
$$

### Edge Detection
This edge detector is a bit more complicated than the filters above because it involves two correlations4. In particular, it involves kernels $K_{x}$ and $K_{y}$, which are shown below:
#### Kx:
```
-1 0 1
-2 0 2
-1 0 1
```

#### Ky:
```
-1 -2 -1
 0  0  0
 1  2  1
```

After computing $Ox$ and $Oy$ by correlating the input with $K_{x}$ and $K_{y}$ respectively (using the 'extend' behavior for each correlation), each pixel of the output $O$ is the square root of the sum of squares of corresponding pixels in $Ox$ and $Oy$:

$$
O_{x,y} = \left\lfloor \sqrt{Ox_{x,y}^{2} + Oy_{x,y}^{2}} \right\rfloor
$$

## Representing Color
It turns out that the representation of digital images and colors as data is a rich and interesting topic. And although there are many ways we could choose to represent color images, we will use a tried-and-true representation, the RGB color model, which is used in a lot of common image formats. In this representation, rather than a single integer, we will represent the color of each pixel as a tuple of three integers, representing the amount of red, green, and blue in the pixel, respectively. By combining red, green, and blue, we can represent a wide range of colors.

We'll represent a color image using a Python dictionary with three keys:
* width: the width of the image (pixels)
* heigth: the height of the image (in pixels)
* pixels: a Python list of pixels values, each represented as a tuple of three integers, `(r, g, b)`, where `r`,`g`,`b` are all in the range $[0, 255]$. As with the greyscale images, these values are stored in row-major order (listing the top row left-to-right, then the next row, and so on).

For example, consider this $2 \times 3$ image (enlarged here for clarity):
IMAGEEEEEEEEEEEEEEEEEEEEE

This image would be encoded as the following dictionary:
```
i = {
    'height': 3,
    'width': 2,
    'pixels': [(255, 0, 0), (39, 143, 230), (255, 191, 0),
               (0, 200, 0), (100, 100, 100), (179, 0, 199)],
}
```

We used Python function objects to represent filters: we represented a filter as a function that took an image as input and produced a related image as output. We will continue with this representation: a filter for color images is a Python function that takes a color image as its input and produces a related color image as its output.

While there are certainly other things we can do with color images, it turns out that we can think about implementing color versions of many of the filters from previous sections of the lab by separating our color image into three separate greyscale images (one for each channel: red, green, and blue), applying the same 'greyscale' filter to each one, and then recombining the results together into a color image.

For example, we can think about a color version of our inversion filter as working in the way illustrated in the diagram below:

IMAGEEEEEEEEEE OF DIAGRAM

We can think about blurring or sharpening in similar ways: separate an image into its three color components, apply a filter to each, and recombine them together.

Given this common structure, it was useful to have a common way to create these kinds of color image filters from their greyscale counterparts. Thus, we implemented the `color_filter_from_greyscale_filter` function. This function should take as input a Python function representing a greyscale filter (i.e., a function that takes a greyscale image as input and produces a greyscale image as its output). Its return value should be a _new function_ representing a color image filter (i.e., a function that takes a color image as input and produces a color image as its output).

This new filter should follow the pattern above: it should split the given color image into its three components, apply the greyscale filter to each, and recombine them into a new color image.

An example of its usage is given below:
```
# if we have a greyscale filter called inverted that inverts a greyscale
# image...
inverted_grey_frog = inverted(load_greyscale_image('grey_frog.png'))

# then the following will create a color version of that filter
color_inverted = color_filter_from_greyscale_filter(inverted)

# that can then be applied to color images to invert them (note that this
# should make a new color image, rather than mutating its input)
inverted_color_frog = color_inverted(load_color_image('color_frog.png'))
```

## Other Kinds of Filters
Note that the structure of the `color_filter_from_greyscale_filter` function assumes a particular form for our greyscale filters: in particular, it expects that the filters we pass to it as inputs are functions of a single argument. However, our `blurred` and `sharpened` filters took both an image and an additional parameter. 

One strategy is to define, for example, a function `make_blur_filter` that takes the parameter n and returns a blur filter (which takes a single image as argument). In this way, we can make a blur filter that is consistent with the form expected by `color_filter_from_greyscale_filter`, for example:

```
# from section 5, we could create a blurred greyscale image like follows:
blurry = blurred(load_greyscale_image('cat.png'), 3)

# we would like to make a function make_blur_filter that takes a single
# parameter and returns a filter.  the use of this function is illustrated
# below:
blur_filter = make_blur_filter(3)
blurry2 = blur_filter(load_greyscale_image('cat.png'))

# note that make_blur_filter(3), for example, produces a filter of the
# appropriate form for use with color_filter_from_greyscale_filter, but
# that blurry and blurry2 are equivalent images.

# it is also not necessary to store the output of make_blur_filter in a
# variable before calling it.  for example, the following also produces an
# equivalent image:
blurry3 = make_blur_filter(3)(load_greyscale_image('cat.png'))
```

For the sake of avoiding repetitious code, we made use of blurred and sharpened rather than reimplementing that behavior.

## Cascade of Filters
We then added support for chaining filters together in cascade (so that the output of one filter is used as the input to another, and so on). You should implement a function called `filter_cascade` in your lab file to this end. This function should take a list of filters (Python functions) as input, and its output should be a function that, when called on an input image, produces the equivalent of applying each of the given filters, in turn, to the input image.

For example, if we have three filters `f1`, `f2`, and `f3`, then the following two images should be equivalent:

```
out1 = f3(f2(f1(image)))

c = filter_cascade([f1, f2, f3])
out2 = c(image)
```




