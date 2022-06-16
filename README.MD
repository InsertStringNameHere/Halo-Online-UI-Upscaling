#
# ElDewrito UI and Font Scaling

This is a repository for information relating to the scaling and modification of UI Elements and fonts within both Halo Online, and to a lesser extent, ElDewrito.
This respository only contains information relating to the modification of in engine UI elements and fonts, and does not contain information realting to the 
modification of any CEF related UI, although I may include that in the future.

#
## What are some of the limitations of the font system used in Halo Online?

Halo Online, and ElDewrito use Halo 3's font package system, which has various limitations imposed on it, 
mainly due to the memory and file size limitations of the Xbox 360. Halo 3's font package system is limited to a maxmimum of 16 fonts, or character sets and 16 font orders.
It has a maxmimum character resolution of 256x64, with each character being limited to maxmimum compressed file size of around 0x8000 bytes (32768 bytes when converted to decimal).

This limits both how large each character can be in terms of overall resolution, as well as how much data each character can contain, both in terms of metadata, and colour data, though
thats outside the scope of basic font manipulation, and doesn't really effect it all that much, outside of very specific scenarios.

##
## What tools are required to manipulate the UI Elements and Fonts within Halo Online?

### TagTool

TagTool is the tool that will allow us to make changes to the UI tags with Halo Online's cache files, including scaling them to the required size.
TagTool can also be used to edit other tags within the cache, which can prove useful when modifying other aspects of the UI.

TagTool Link: https://github.com/TheGuardians/TagTool

##
### Font Packager

Font Packager is the tool that allows us to read and write data to Halo Online's font package. This inlcudes the addition of extra characters,
the modification of existing ones, or the removal of characters.

Font Packager Link: https://github.com/Lord-Zedd/FontPackager

##
### Image Upscaler

You will of course need a tool to scale and resize the characters once they've been extracted from the font package. In my case, I'll be using Waifu2x,
which is an open source tool, however you can use which ever tool you feel most comfortable with.

Waifu2x Link: https://github.com/AaronFeng753/Waifu2x-Extension-GUI

##
# Selecting a Scaling Value
Because of the limitation of Halo 3's font package system, you can't just scale the characters within the font package by any random value. The maximum character resolution
is 256 pixels in width and 64 pixels in height. Width isn't really an issue, until you want to start upscaling the weapon characters, but I'll delve into that a little later.
Because the maximum height possible is 64 pixels, we first need to determine the maximum height of any single character within each font.

Within Halo Online's font package, the maximum height of any one character is:

> fixedsys-9: 16 pixels
> 
> conduit-16: 24 pixels
> 
> conduit-32: 46 pixels
> 
> conduit-23: 33 pixels
> 
> conduit-18: 45 pixels
> 
> larabie-10: 15 pixels
> 
> pragmata-14: 27 pixels

Using these values, we can approximate what the maximum possible scaling value is for each character across every single font within the font package
This can be doen by taking the highest value from the list above, which in our case is 46, and determining what is the highest possible value that we can multiply it by,
and still have it stay under 64, which is the maximum character height. I've already done the math for you, and the approximate value is:

> 1.399999....

Now, this isn't an optimal value, as this scaling value only results in around a 40% increase in overall resolution, which isn't a lot, and given the fairly low resolution
of the characters within the font package, it doesn't result in the best upscaling. This is why I decided to use multiple values when upscaling the characters within
the font package.

After some more research, and a lot of testing, I decided to use these scaling values for each font within the font package. That way it ensures that each font is upscaled
by values that are fairly close together, ensuirng that there won't be too many alignment issues when the rest of the UI elemets are scaled up accordingly

The scaling values for each font are as follows:

> fixedsys-9: 1.6666667 or 5/3
> 
> conduit-16: 1.6666667 or 5/3
> 
> conduit-32: 1.35 or 27/20
> 
> conduit-23: 1.6666667 or 5/3
> 
> conduit-18: 1.6666667 or 5/3 and 1.35 or 27/20
> 
> larabie-10: 1.6666667 or 5/3
> 
> pragmata-14: 1.6666667 or 5/3

I was forced to use multiple values for one of the fonts, in this case "conduit-18" as some of the characters when scaled with the standard value of 5/3 would exceed
the maximum character height of 64, so I was forced to use a smaller scaling value of 27/20, which kept each character under the height limit, as well as giving them some kind
of meaningful upscale

#
# Scaling and Resizing the Fonts
Now that we have decided upon a standard scaling value for each of the fonts and the characters within the font package, we can begin to scale each font and character
within the Halo Online Package.

Now, normally we would just extract the existing characters from within the font package and use an upscaler to scale them according to the specified scaling value.
However, there are several issues with doing that. The main issue is that the resolution of the characters within the font package are so low that it is pretty hard
for even a decent image processing algorithim to upscale them correctly, and they are also so low detail, that even upscaling cannot correct them.

This is why I decided to look into using MCC's fonts. MCC has 3 different font packages, one which is the same resolution as Halo Online's font package, one that is
2x the resolution, and one that is 3x the resolution. After some experimenting with downscaling from the 2x and the 3x font packages, I found that I got the best results when
downscaling from the 2x font packages. I have feeling this is down to the image scaler built into Waifu2x, which probably can't properly downscale the amount of image data
within the 3x characters, however your mileage may vary depending on which application you use. The characters can now be extracted from the font packages, and are ready for
in this case downscaling.

Now that we have our extracted fonts, we can now downscale them to our desired scale. This was accomplished by first determinign the original resolution fo each font
within Halo Online's font package, and then multiplying by the decided upon scaling value. 

This can be approximated through the following formula:

> (Original Resolution) x (Scaling Value)

Once we have obtain this value, this value can then be used to scale the extracted characters to the desired resolution. Waifu2x has a feature tha allows me to scale an image
based on a given height or width value, whilst keeping the original aspect ratio, which definitely made things easier. Alternatively, you can apply the same formula,
but to both the width and the height of the image, in order to determine what resolution to scale it to.

Once each character has been scaled, it can then imported into the font package, replacing the existing characters. You can also add addition characters if need be.

#
# Importing the Fonts into the Engine
Assuming that all the characters don't surpass any of the resolution or size limits, then the font package should load perfectly fine. However, you may notice that
the fonts are too large for the UI elements that they are placed in, and that some of the characters are too close together.

This is because the UI tags and bitmaps have not been scaled with the fonts, so they appear too small, and the egine itself isn't rendering at the correct resolution, so
even if the UI elements were at the correct scale, they would be too big to render on screen.

The first issue can be remedied by the use of TagTool. TagTool has a command which will allows to scale all the UI tags by a given scaling value, which in out case
is the same value that we used to upscale the characters within the font package. In our case we'll be using the value of 5/3. The command is to be input as shown:

> rescalegui (Scaling Value)

or in our case

> rescalegui 1.6666667

This will fix the issue of the characters being too big to fit within their specified UI elements, but now you'll run into the latter issue of the UI elements being too big
to render on the screen, so it 'll render some of them offscreen. In order to remedy this, we'll need to make some change to ElDewrito's source code.

In order to remedy this issue properly we'll need to make some specific changes to ElDewrito's source code, more specifically, we'll need to make changes to the rendering
resolution of the UI, or the value that the game uses when determining the rendering resolution.

This is within the "ApplyUIResolution" method within Ui.cpp, and more specifically involves the following lines of code:

> UIResolution[0] = (int)(((float)gameResolution[0] / (float)gameResolution[1]) * 640);
>
> UIResolution[1] = 640;

and

> UIResolution[0] = 1152;
>
> UIResolution[1] = (int)(((float)gameResolution[1] / (float)gameResolution[0]) * 1152);

1152 x 640 is the default rendering resolution for the engine, and determines what the overall rendering resoltuion will be given the resolution specified within the game's settings. These are the values that we need to multiplay by our given scaling resolution, which in out case is 5/3, with the following output being:

> 1152 x 5/3 = 1920

and

> 640 x 5/3 = 1067

These values can the substitued into the code from above, which should in turn look like:

> UIResolution[0] = (int)(((float)gameResolution[0] / (float)gameResolution[1]) * 1067);
>
> UIResolution[1] = 1067;

and

> UIResolution[0] = 1920;
>
> UIResolution[1] = (int)(((float)gameResolution[1] / (float)gameResolution[0]) * 1920);

This should fix the issue of the UI elements being too big to display on screen, now that the game rendering at the appropriate resolution. There is still the issue of the characters being too close together, and that is related to there display resolution. Each character has an image resolution which is the actual resolution of the bitmap that said character is using, and a display resolution which is the amount of space that said character actually takes up within the engine.

The method I used to fix this was basic trial and error. I did however follow a basic rule that the display resolution should be no more than 3 or 4 pixels greater than the bitmap resolution. This ensured that each character was both aligned correctly, and that each character was evenly spaced apart from one another, however the amount of space between characters is purely subjective.