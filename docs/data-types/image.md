#MicroBitImage

## Overview

`MicroBitImage` represents a bitmap picture.

Images can be of any size, and each pixel on the image has an individual brightness value in the range 0 - 255.
l
Once created, this class also provides functions to undertake graphical operations on that image, including setting pixels, clearing the image, pasting one
image onto another at a given position, shifting the content of an image and comparing and copying images.

It is designed to work with the [`MicroBitDisplay`](../ubit/display.md) class to allow the creation of animations and visual effects.

!!! note
    This is a **managed type**. This means that it will automatically use and release memory as needed. There is no need for you to explicitly free or release memory when you're done - the memory will be freed as soon as the last piece of code stops using the data.

## Creating Images

Images are easy to create - just create them like a variable, but provide the details requested in one of the constructor function shown below. This may sound complex, but is quite simple when you get used to it. For example, to create
a blank, 2x2 image:

```cpp
MicroBitImage image(2,2);
```

You can also create one from a text string that represents the pixel values that you want. This is a really easy way to create icons and emojis in your code.

The string constructor for a `MicroBitImage` takes the form of a series of comma separate values. Each value is the brightness of a pixel, starting at the top left of your image and working
to the right. Whenever you put a newline character `\n` in your string, this moves onto a new line of pixels.

So to create a 3x3 image that is a picture of a cross, you might write:
```cpp
MicroBitImage cross("0,255,0\n255,255,255\n0,255,0\n");
```

## Manipulating Images

Once you have created an image, you can use any of the functions listed in the API below to change that image. For example, you can use `setPixelValue` to change an individual pixel.

In the example below, you can see how to change the centre pixel in our cross image
created earlier:
```cpp
cross.setPixelValue(1,1,0);
```
!!!note
    Co-ordinates are indexed from zero, with the origin (0,0) being at the top left of the image.


You can print characters onto an image...

```cpp
MicroBitImage image(5,5);
image.print('J');
```

You can also paste the content of one image onto another - either at the origin, or somewhere else:

```cpp
MicroBitImage image(5,5);
image.paste(cross);
```
```cpp
MicroBitImage image(5,5);
image.paste(cross, 1, 1);
```

and of course, you can display your image on the LEDs using the [`MicroBitDisplay`](../ubit/display.md) class:

```cpp
MicroBitImage image(5,5);
image.paste(cross);
uBit.display.print(image);
```

## Comparing and Assigning Images

MicroBitImage is a **managed type**, so you can pass images as parameters to functions, store then and assign them to other variables without having to worry about memory leaks.

The type will count the number of times it is used, and will delete itself as soon as your image is not used anymore.

You can assign images just like any other variable. So this is perfectly permitted, and memory safe:

```cpp
MicroBitImage cross("0,255,0\n255,255,255\n0,255,0\n");
MicroBitImage img;

img = cross;
uBit.display.print(img);
```

As is this:
```cpp
void doSomething(MicroBitImage i)
{
    uBit.display.print(img);
}

int main()
{
    MicroBitImage cross("0,255,0\n255,255,255\n0,255,0\n");

    doSomething(cross);
}
```

You can also compare images:
```cpp
MicroBitImage cross("0,255,0\n255,255,255\n0,255,0\n");
MicroBitImage img;

img = cross;

if (img == cross)
    uBit.display.scroll("SAME!");
```

## Storing Images in Flash Memory

The micro:bit is a **very** constrained device in terms of Random Access Memory (RAM). Unlike modern PCs that typically have over 4 Gigabytes of RAM (around four thousand million bytes!), the micro:bit has only 16 Kilobytes (16 thousand bytes!), and if you take a look at the memory map, you will see most of this is already committed to running the Bluetooth stack.

By default, any `MicroBitImage` you create will be stored in this precious RAM, so that you have the ability to change it. However, it is not uncommon to have read-only images. in this case, we can store the image in FLASH memory (the same place as your program), of which the micro:bit has 256 Kilobytes.  

Should you want to create an store a constant image in flash, you can do so using the following constructor, it is a little more complicated, but can save you memory:

 - The array of bytes should always start with 0xff, 0xff - which tell the runtime that this image is stored in FLASH memory.
 - The next number should be the width in pixels, followed by a zero.
 - The next number should be the height in pixels, followed by a zero.
 - The following bytes are then individual pixel brightness values, starting at the top left, then working left to right, top to bottom until the bottom right corner is reached.


!!! note
    If you create an image this way, none of the functions to change the content will work on that image (e.g. `setPixelValue`, `paste`, etc).

```cpp
    const uint8_t heart[] __attribute__ ((aligned (4))) = { 0xff, 0xff, 10, 0, 5, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0};

    MicroBitImage i((ImageData*)heart);
    uBit.display.animate(i,5);
}
```

#API
[comment]: <> ({"className":"MicroBitImage"})
##leakData
<br/>
####<div style='color:#a71d5d; display:inline-block'>ImageData</div> <div style='color:#795da3; display:inline-block'>leakData</div>()
#####Description
Get current ptr, do not decr() it, and set the current instance to empty image.  

 This is to be used by specialized runtimes which pass  ImageData  around.           


##getBitmap
<br/>
####<div style='color:#a71d5d; display:inline-block'>uint8_t \*</div> <div style='color:#795da3; display:inline-block'>getBitmap</div>()
#####Description
Return a 2D array representing the bitmap image.           


##Constructor
<br/>
####MicroBitImage( <div style='color:#a71d5d; display:inline-block'>ImageData  \*</div> ptr)
#####Description
Constructor. Create an image from a specially prepared constant array, with no copying. Will call ptr->incr().  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>ImageData  \*</div> ptr - The literal - first two bytes should be 0xff, then width, 0, height, 0, and the bitmap. Width and height are 16 bit. The literal has to be 4-byte aligned.
#####Example
```cpp
 static const uint8_t heart[] __attribute__ ((aligned (4))) = { 0xff, 0xff, 10, 0, 5, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i((ImageData*)(void*)heart); 
```
<br/>
####MicroBitImage()
#####Description
Default Constructor. Creates a new reference to the empty  MicroBitImage  bitmap  

 


#####Example
```cpp
 MicroBitImage i(); //an empty image instance 
```
<br/>
####MicroBitImage( <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> image)
#####Description
Copy Constructor. Add ourselves as a reference to an existing  MicroBitImage .  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> image - The  MicroBitImage  to reference.
#####Example
```cpp
 MicroBitImage i("0,1,0,1,0\n"); 
 MicroBitImage i2(i); //points to i 
```
<br/>
####MicroBitImage( <div style='color:#a71d5d; display:inline-block'>const char \*</div> s)
#####Description
Constructor. Create a blank bitmap representation of a given size.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>const char \*</div> s - A text based representation of the image given whitespace delimited numeric values.
#####Example
```cpp
 MicroBitImage i("0,1,0,1,0\n1,0,1,0,1\n0,1,0,1,0\n1,0,1,0,1\n0,1,0,1,0\n"); // 5x5 image 
```
<br/>
####MicroBitImage( <div style='color:#a71d5d; display:inline-block'>const int16_t</div> x,  <div style='color:#a71d5d; display:inline-block'>const int16_t</div> y)
#####Description
Constructor. Create a blank bitmap representation of a given size.  

   
 
 x 
 
 
 the width of the image.  
 
 
 
 y 
 
 
 the height of the image.  
 
 
Bitmap buffer is linear, with 8 bits per pixel, row by row, top to bottom with no word alignment. Stride is therefore the image width in pixels. in where w and h are width and height respectively, the layout is therefore:  

 |[0,0]...[w,o][1,0]...[w,1] ... [[w,h]  

 A copy of the image is made in RAM, as images are mutable.           


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>const int16_t</div> x - the width of the image.

>  <div style='color:#a71d5d; display:inline-block'>const int16_t</div> y - the height of the image.
<br/>
####MicroBitImage( <div style='color:#a71d5d; display:inline-block'>const int16_t</div> x,  <div style='color:#a71d5d; display:inline-block'>const int16_t</div> y,  <div style='color:#a71d5d; display:inline-block'>const uint8_t \*</div> bitmap)
#####Description
Constructor. Create a bitmap representation of a given size, based on a given buffer.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>const int16_t</div> x - the width of the image.

>  <div style='color:#a71d5d; display:inline-block'>const int16_t</div> y - the height of the image.

>  <div style='color:#a71d5d; display:inline-block'>const uint8_t \*</div> bitmap - a 2D array representing the image.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); 
```
##operator=
<br/>
####<div style='color:#a71d5d; display:inline-block'>MicroBitImage</div> <div style='color:#795da3; display:inline-block'>operator=</div>( <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> i)
#####Description
Copy assign operation.  

 Called when one  MicroBitImage  is assigned the value of another using the '=' operator.  

 Decrement our reference count and free up the buffer as necessary.  

 Then, update our buffer to refer to that of the supplied  MicroBitImage , and increase its reference count.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> i
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); 
 MicroBitImage i1(); 
 i1 = i; // i1 now references i 
```
##operator==
<br/>
####<div style='color:#a71d5d; display:inline-block'>bool</div> <div style='color:#795da3; display:inline-block'>operator==</div>( <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> i)
#####Description
Equality operation.  

 Called when one  MicroBitImage  is tested to be equal to another using the '==' operator.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> i - The  MicroBitImage  to test ourselves against.
#####Returns
true if this  MicroBitImage  is identical to the one supplied, false otherwise.
#####Example
```cpp
 MicroBitDisplay display; 
 MicroBitImage i(); 
 MicroBitImage i1(); 
 
 if(i == i1) //will be true 
 display.scroll("true"); 
```
##clear
<br/>
####<div style='color:#a71d5d; display:inline-block'>void</div> <div style='color:#795da3; display:inline-block'>clear</div>()
#####Description
Resets all pixels in this image to 0.  

 


#####Example
```cpp
 MicroBitImage i("0,1,0,1,0\n1,0,1,0,1\n0,1,0,1,0\n1,0,1,0,1\n0,1,0,1,0\n"); // 5x5 image 
 i.clear(); 
```
##setPixelValue
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>setPixelValue</div>( <div style='color:#a71d5d; display:inline-block'>int16_t</div> x,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y,  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> value)
#####Description
Sets the pixel at the given co-ordinates to a given value.  

 

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x - The co-ordinate of the pixel to change.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y - The co-ordinate of the pixel to change.

>  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> value - The new value of the pixel (the brightness level 0-255)
#####Returns
MICROBIT_OK, or MICROBIT_INVALID_PARAMETER.
#####Example
```cpp
 MicroBitImage i("0,1,0,1,0\n1,0,1,0,1\n0,1,0,1,0\n1,0,1,0,1\n0,1,0,1,0\n"); // 5x5 image 
 i.setPixelValue(0,0,255); 
```

!!! note
    all coordinates originate from the top left of an image. 

##getPixelValue
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>getPixelValue</div>( <div style='color:#a71d5d; display:inline-block'>int16_t</div> x,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y)
#####Description
Retrieves the value of a given pixel.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x - The x co-ordinate of the pixel to read. Must be within the dimensions of the image.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y - The y co-ordinate of the pixel to read. Must be within the dimensions of the image.
#####Returns
The value assigned to the given pixel location (the brightness level 0-255), or MICROBIT_INVALID_PARAMETER.
#####Example
```cpp
 MicroBitImage i("0,1,0,1,0\n1,0,1,0,1\n0,1,0,1,0\n1,0,1,0,1\n0,1,0,1,0\n"); // 5x5 image 
 i.getPixelValue(0,0); //should be 0; 
```
##printImage
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>printImage</div>( <div style='color:#a71d5d; display:inline-block'>int16_t</div> x,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y,  <div style='color:#a71d5d; display:inline-block'>const uint8_t \*</div> bitmap)
#####Description
Replaces the content of this image with that of a given 2D array representing the image.  

 

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x - the width of the image. Must be within the dimensions of the image.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y - the width of the image. Must be within the dimensions of the image.

>  <div style='color:#a71d5d; display:inline-block'>const uint8_t \*</div> bitmap - a 2D array representing the image.
#####Returns
MICROBIT_OK on success, or MICROBIT_INVALID_PARAMETER.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(); 
 i.printImage(0,0,heart); 
```

!!! note
    all coordinates originate from the top left of an image. 

##paste
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>paste</div>( <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> image)
#####Description
Pastes a given bitmap at the given co-ordinates.  

 Any pixels in the relevant area of this image are replaced.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> image - The  MicroBitImage  to paste.
#####Returns
The number of pixels written.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); // a big heart 
 i.paste(i, -5, 0); // a small heart 
```
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>paste</div>( <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> image,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x)
#####Description
Pastes a given bitmap at the given co-ordinates.  

 Any pixels in the relevant area of this image are replaced.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> image - The  MicroBitImage  to paste.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x - The leftmost X co-ordinate in this image where the given image should be pasted. Defaults to 0.
#####Returns
The number of pixels written.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); // a big heart 
 i.paste(i, -5, 0); // a small heart 
```
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>paste</div>( <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> image,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y)
#####Description
Pastes a given bitmap at the given co-ordinates.  

 Any pixels in the relevant area of this image are replaced.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> image - The  MicroBitImage  to paste.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x - The leftmost X co-ordinate in this image where the given image should be pasted. Defaults to 0.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y - The uppermost Y co-ordinate in this image where the given image should be pasted. Defaults to 0.
#####Returns
The number of pixels written.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); // a big heart 
 i.paste(i, -5, 0); // a small heart 
```
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>paste</div>( <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> image,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y,  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> alpha)
#####Description
Pastes a given bitmap at the given co-ordinates.  

 Any pixels in the relevant area of this image are replaced.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>const  MicroBitImage  &</div> image - The  MicroBitImage  to paste.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x - The leftmost X co-ordinate in this image where the given image should be pasted. Defaults to 0.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y - The uppermost Y co-ordinate in this image where the given image should be pasted. Defaults to 0.

>  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> alpha - set to 1 if transparency clear pixels in given image should be treated as transparent. Set to 0 otherwise. Defaults to 0.
#####Returns
The number of pixels written.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); // a big heart 
 i.paste(i, -5, 0); // a small heart 
```
##print
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>print</div>( <div style='color:#a71d5d; display:inline-block'>char</div> c)
#####Description
Prints a character to the display at the given location  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>char</div> c - The character to display.
#####Returns
MICROBIT_OK on success, or MICROBIT_INVALID_PARAMETER.
#####Example
```cpp
 MicroBitImage i(5,5); 
 i.print('a'); 
```
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>print</div>( <div style='color:#a71d5d; display:inline-block'>char</div> c,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x)
#####Description
Prints a character to the display at the given location  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>char</div> c - The character to display.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x - The x co-ordinate of on the image to place the top left of the character. Defaults to 0.
#####Returns
MICROBIT_OK on success, or MICROBIT_INVALID_PARAMETER.
#####Example
```cpp
 MicroBitImage i(5,5); 
 i.print('a'); 
```
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>print</div>( <div style='color:#a71d5d; display:inline-block'>char</div> c,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x,  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y)
#####Description
Prints a character to the display at the given location  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>char</div> c - The character to display.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> x - The x co-ordinate of on the image to place the top left of the character. Defaults to 0.

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> y - The y co-ordinate of on the image to place the top left of the character. Defaults to 0.
#####Returns
MICROBIT_OK on success, or MICROBIT_INVALID_PARAMETER.
#####Example
```cpp
 MicroBitImage i(5,5); 
 i.print('a'); 
```
##shiftLeft
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>shiftLeft</div>( <div style='color:#a71d5d; display:inline-block'>int16_t</div> n)
#####Description
Shifts the pixels in this Image a given number of pixels to the left.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> n - The number of pixels to shift.
#####Returns
MICROBIT_OK on success, or MICROBIT_INVALID_PARAMETER.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); // a big heart 
 i.shiftLeft(5); // a small heart 
```
##shiftRight
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>shiftRight</div>( <div style='color:#a71d5d; display:inline-block'>int16_t</div> n)
#####Description
Shifts the pixels in this Image a given number of pixels to the right.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> n - The number of pixels to shift.
#####Returns
MICROBIT_OK on success, or MICROBIT_INVALID_PARAMETER.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); // a big heart 
 i.shiftLeft(5); // a small heart 
 i.shiftRight(5); // a big heart 
```
##shiftUp
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>shiftUp</div>( <div style='color:#a71d5d; display:inline-block'>int16_t</div> n)
#####Description
Shifts the pixels in this Image a given number of pixels to upward.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> n - The number of pixels to shift.
#####Returns
MICROBIT_OK on success, or MICROBIT_INVALID_PARAMETER.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); 
 i.shiftUp(1); 
```
##shiftDown
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>shiftDown</div>( <div style='color:#a71d5d; display:inline-block'>int16_t</div> n)
#####Description
Shifts the pixels in this Image a given number of pixels to downward.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int16_t</div> n - The number of pixels to shift.
#####Returns
MICROBIT_OK on success, or MICROBIT_INVALID_PARAMETER.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); 
 i.shiftDown(1); 
```
##getWidth
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>getWidth</div>()
#####Description
Gets the width of this image.  

 


#####Returns
The width of this image.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); 
 i.getWidth(); // equals 10... 
```
##getHeight
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>getHeight</div>()
#####Description
Gets the height of this image.  

 


#####Returns
The height of this image.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); 
 i.getHeight(); // equals 5... 
```
##getSize
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>getSize</div>()
#####Description
Gets number of bytes in the bitmap, ie., width * height.  

 


#####Returns
The size of the bitmap.
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); 
 i.getSize(); // equals 50... 
```
##toString
<br/>
####<div style='color:#a71d5d; display:inline-block'>ManagedString</div> <div style='color:#795da3; display:inline-block'>toString</div>()
#####Description
Converts the bitmap to a csv  ManagedString .  

 


#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); 
 uBit.serial.printString(i.toString()); // "0,1,0,1,0,0,0,0,0,0\n..." 
```
##crop
<br/>
####<div style='color:#a71d5d; display:inline-block'>MicroBitImage</div> <div style='color:#795da3; display:inline-block'>crop</div>( <div style='color:#a71d5d; display:inline-block'>int</div> startx,  <div style='color:#a71d5d; display:inline-block'>int</div> starty,  <div style='color:#a71d5d; display:inline-block'>int</div> finx,  <div style='color:#a71d5d; display:inline-block'>int</div> finy)
#####Description
Crops the image to the given dimensions.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int</div> startx - the location to start the crop in the x-axis

>  <div style='color:#a71d5d; display:inline-block'>int</div> starty - the location to start the crop in the y-axis

>  <div style='color:#a71d5d; display:inline-block'>int</div> finx

>  <div style='color:#a71d5d; display:inline-block'>int</div> finy
#####Example
```cpp
 const uint8_t heart[] = { 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, }; // a cute heart 
 MicroBitImage i(10,5,heart); 
 i.crop(0,0,2,2).toString() // "0,1\n1,1\n" 
```
##isReadOnly
<br/>
####<div style='color:#a71d5d; display:inline-block'>bool</div> <div style='color:#795da3; display:inline-block'>isReadOnly</div>()
#####Description
Check if image is read-only (i.e., residing in flash).           


##clone
<br/>
####<div style='color:#a71d5d; display:inline-block'>MicroBitImage</div> <div style='color:#795da3; display:inline-block'>clone</div>()
#####Description
Create a copy of the image bitmap. Used particularly, when  isReadOnly()  is true.  

 


#####Returns
an instance of  MicroBitImage  which can be modified independently of the current instance 
____
[comment]: <> ({"end":"MicroBitImage"})
