---
title: "Blend modes"
date: 2020-06-01T21:29:50+02:00
draft: true
---
A blend mode is a procedure based on a mathematical formula to create a new color from two other colors. Much like an old-fashioned painter who fabricates a new tint by mixing or blending two paints together according to a secret recipe. Or to the analog photographer, who burn and dodges a film negative to develop a new picture.

The blending procedure can be described by the following general formula (see  https://www.w3.org/TR/compositing-1/).

Cs = (1 - αb).Cs + αb.B(Cb, Cs)

* Cs: is the source color
* Cb: is the backdrop color
* αs: is the source alpha
* αb: is the backdrop alpha
* B(Cb, Cs): is the blending function

Different names circulate about the two colors in the literature: base vs blend, background vs foreground, destination vs source. The source (blend, foreground) is rendered on top of the existing backdrop (base, background) color. Sometimes, it's easier to visualize the colors as layers. The source layer (blend, foreground layer) is blended with the layer beneath (base, background layer), replacing the original source layer.

αs and αb are the alpha-values of both colors. α=0 is a completely transparent color. α=1 is a full opaque color. The history of compositing runs parallel with the discovery of the alpha-channel in the 1980's.

Let's look at an example with the multiply blend mode: B(Cb, Cs) = Cb . Cs or multiplying the two colors. So, the genarl formula becomes: Cs = (1 - αb).Cs + αb.Cb.Cs

There are about 23 different blend modes, sometimes grouped in 5 unnamed groups, apparently following the Photoshop tradition. Blend modes appear in several places in Blender.

{{< figure src="blend-modes-10.png" title="MixRGB shader node" caption= "MixRGB shader node">}}
The Shader Editor has a mixRGB node (see figure 1). This node has two input color sockets and one output color socket. With the dropdown, you can choose between 18 blend modes: mix, darken, lighten, ... It's a little strange that the title indicates "Mix" while the DD menu talks about mixRGB (see further)


The Compositor (mix node), Grease Pencil (layers), texture & vertex painting (brush) and the Video Sequence Editor (strips).

Because colors are numerical codes, describing the intensities of the primary colors Red, Green and Blue (RGB), they can be altered by mathematical formulas, for example a specific amount can be added or subtracted.  These formulas form the essence of the blend modes. Each blend formula contains two colors. They can be called the foreground (F) and background (B) color. Sometimes they are also named the base and blend color. You can also think about them in terms of layers or images, e.g. the layers in Grease Pencil or the strips in the VSE.

In Grease Pencil and VSE,  the foreground color (F) is represented by the top layer and the background color (B) by the layer beneath it. But contra-intuitively, in a MIX node of the compositor or shade editor, the foreground is plugged in the second socket of a node and the background color in the first socket! 

Blend modes involve the mixing of the colors of these two layers, strips or images. Your intention is probably to alter the look-and-feel of the background image in terms of its colors. This background image is most likely also the more complex image. The second image, the foreground image, can be a simple image; e.g. a logo or a uniformly colored image. The second one is often used to darken of lighten an image. The first one is imposed upon the background. Generally speaking, you want to change the color of the background layer by blending it with a foreground layer.

![](blend-modes-20.png)

In the example above we will use images of 5 x 5 pixels. The background layer image is randomly filled with colors. Each pixel is formed by a combination of the three primary colors. Cell (1,1) has a RED-value of 1, a GREEN value of 0.5 and a BLUE-value of 0.3; together resembling an orange color. Foreground 1 contains a simple figure with black (R=0,G=0,B=0) and white (R=1,G=1,B=1) pixels. Foreground 2 is a uniform gray of 0.30. Gray shades are created by equal amounts of red, green and blue.

Both results are created by the Multiply blend mode. Result 1 is the multiplication of the background with F1.  Multiplying a value with one returns the same value; multiplying with zero returns zero. So, cell (1,1) of Result 1 is black because (1.00,0.50,0.30) * 0 returns zero or black for the three color channels. Cell (1,3) has the same color as the background because multiplying by 1 returns the same value.



Result 2 is a darkened version of the background. For example, take cell (1,5) with value (R=0.70, G=0.60, B=0.40). Multiplying these values with 0.30 of the gray F2 results in (0.21, 0.18; 0.12), a slightly darker color.

CAUTION
Blend mode formulas are originally developed in a display referred environment, with lower and upper intensity thresholds for a specific display. A typical LED display for example can produce a maximum intensity of about 250 - 300 candelas/m². Traditionally, these thresholds are standardized to zero and 1. Therefore, color intensities could only vary between 0  and 1 in a display referred environment. This limit -the number 1- appear in some formulas. Blender however, operates in a scene referred environment, where color intensities can vary between 0 and infinity, depending upon the amount of lighting.  For example, if you render the default cube with a sun lamp of strenght 1000, and send this render output to the compositor, the lightened faces of the cube  will have intensity values of about 200. Applying one of the display referred blend formulas, which assume a maximum intensity of 1, can then give unpredictable results. Using PNG's or movie clips pose no problems because they are coded for a display referred environment.

The blend modes are grouped into 4 groups: darken, lighten, contrast, compare and component. The examples below assume two images of 5 x 5 pixels. The background layer image is randomly filled with 5  tones of grey: RGB = 0,0,0 (pure black), 0.25, 0.50, 0.75 and 1.00 (pure white). The foreground image has a uniform tone of gray. The corresponding RGB values are given below the figure; abbreviated to one number. We use gray tones because it's easier to demonstrate the math. Remember however that the blend modes work on each RGB channel independently; see the figure above for an example.

GROUP 1: DARKEN
																			
![](blend-modes-30.png)


As the name implies, this group of blend modes will result in a overall darker color.  As you can see the average intensity (or Value) is  0.5  for the back- and  foreground and 0.37, 0.25 and 0 for the darken, multiply and color burn blend mode.  Remark that there are some negative values for the color burn mode, lowering the average artificially. The three blend modes also differ in the look-and-feel (see below). For all three modes: a  pure black foreground will always result into pure black background. A white foreground returns the original background.

DARKEN: min (F, B)
This blend mode compares the two color codes and keeps the minimum or lowest intensity value. Remember, that this is done for each channel (R, G and B) separately. The result contains the darkest color of either the foreground or the background.  Because our foreground has a uniform gray-tone of 0.50, each pixel in the background image that is > 0.50 is replaced with the 50% gray, returning islands of grey. This also implicates that a white foreground (F = 1) will return the original background (in all its colors). A pure black foreground (F = 0) will result in a black image,  effectively masking out the background.

MULTIPLY: F * B
The color codes of the foreground and background layer are multiplied. Because each color code (in a display referred environment!) is a number between 0 and 1 ; the result is always less (darker) than each of its constituent color codes. A property of the multiply operation is that the effect is larger with bigger numbers. So, the darkening effect will be more pronounced with in the highligths than with in the shadows of the background image.  In the example above, the white cell (1,2) with B = 1 is darkened to B = 0.5; a reduction of 50%, while the 25% gray cell (1,3) is darkened to B = 0.13; a reduction of 12.5%. The global effect of the multiply blend mode is less outspoken than the two other methods, especially when the foreground color is chosen from the midtones (0.3 < F < 0.7).  The result resembles more of the heterogenity of the background image than the other blend modes.

Again, there are two special cases: 0 and 1. Multiplying with a black foreground (B = 0) gives a pure black result, because multiplying by zero returns zero. Multiplying with a white foreground color (F = 1) results in the original background color.

COLOR BURN: 1 - (1 - B)/F
This formula contains the 'magical' number 1. 'One' signifies in a display referred application the maximum allowed intensity. As such, this blend mode is not really usuable in the Blender compositor where the maximum intensity can be much higher.  The formula also contains some other potential problems. A white foreground (F=0) will result in a division by zero error. The formula can also return negative values.  Blender solves both problems  by setting the result to zero in those cases.

But, what exactly does this formula do? The name comes from the photographers old dark-room technique to darken a film negative by burning or shining light  on it.  A little contra-intuitively however, the burn light is simulated by a 'dark' foreground color. As F is approaching zero (black), the result of the divison will become larger and larger, so that the final formula 1 - (1-B)/F gets negative and thus pure black. If F is approaching one, the formula becomes equal to B (1 - (1 - B/1) or the original background.

The essence of the formula is a division: (1 - B)/F.  A property of a division is that if the numerator > denominator, the result of the division will be a number  > 1. This means that the ultimate result of the formula (1 minus division) will be a negative number, which is translated to a pure black color. In this case, the background image is burnt to black.

When will the burning occur? When  will (1 - B) > F? Setting F to a very low value certainly helps; e.g. F = 0 will burn the complete background image. Also, because of the inversion (1 - B), the shadows will be burnt more rapidly than the highlights. In the example, all cells < 0.5 (=shadows) become through the inversion > 0.5 and thus > F.

The color burn blend mode has indeed as the name suggest a burnt look-and-feel with a lot of pure black colors.
[The darker the background layer, the more its color is used. White will make no changes; white doesn't burn. . Everything is darker or the same intensit as the background layer
 
GROUP 2: LIGHTEN




LIGHTEN: max (F,B)
This blend mode compares the two color codes (for each channel R, G and B) and keeps the maximum or highest intensity value. The result contains the lightest color of the foreground or background. Because the foreground is 50% grey, this blend operation will give quite a few islands of 50% grey. This also implicates that a black foreground (F = 0) will return the original background (in all its colors!). A pure white foreground (F = 1) will result in a white  image, removing the background completely.

SCREEN: 1 - (1 - F)*(1 - B)
Both the foreground and background colors are inverted (shadows become highlights and vice versa) and then multiplied. So, the darkening effect of the multiplication applies also here but because of the inversion, the original shadows are darkened more than the original highlights.  Because the foreground is also inverted, the darkening will be strengthened by a dark foreground. So, the multiplication of (1 - F) * (1 - B) will result in a darker image, where the shadows are more darkened than the highlights. Through the final inversion (1 - multiplication) the result will be a lightening of the image where the shadows are more ligthened than the highlights. For example, a dark cell (5,3) of 0.25 is lightned to 0.63 (increase of 38%), while cell (2.1) of 0.50 is lightend up to 0.75, an increase of 25%.

COLOR DODGE: B/(1-F)
The Color Dodge mode is the opposite of the Color Burn mode. If the numerator B >= denominator (1 - F) then the result of the division will be greater or equal than 1. And this translates to a white color as the result of the blending process.  So, in the example above where F = 0.5 and thus also 1 - F = 0.5, every cell in the background layer where B >= 0.5 will become white, such as cells (2.1) and (3,1).

By manipulating the foreground color, one can whiten or dodge the background layer.

ADD: F + B
The two color codes are simply added, resulting in a higher intensity color code and thus a lighter image. Because the addition can easily result in numbers > 1, pure white is much more possible than pure black.

GROUP 3: CONTRAST


OVERLAY: if B < 0.5 then 2*B*F  else 1 - 2*(1-F)*(1-B) or if B < 0.5 then 2 * multiply mode else 1 - 2 * screen mode

The Overlay blend mode is a combination of the Multiply and Screen mode. Remember that multiply will darken and screen will lighten the background layer.  By selectively applying both modes, higher contrast in the background layer can be created.

The shadows (B < 0.5) will be multiplied or darkened. The highlights (B >= 0.5) will be lightened. So, the light parts of the image will become lighter and the dark parts become darker.


SOFT LIGHT:  (1 - B) * Multiply(F,B) + B * Screen(F,B)

This mode is very similar to the Overlay blend mode and is always a combination of both the Screen and the Multiply blend mode. The result has however a more softer effect
 
LINEAR LIGHT: if F > 0.5 then B + 2 * (F - 0.5) else B + (2 * F) - 1
This blend mode also increases the contrast because, if F > 0.5, the background color is increased with 2 * a positive number. Otherwise, if F <= 0.5, then the background color is decreased because 2 * F cannot be greater than 1, which is subtracted.

GROUP 4: COMPARE



DIFFERENCE: if F > B then F - B else B - F
This blend mode gives the absolute intensity difference between foreground and background. This mode can be used to check the alignment of two images. For example, if foreground and background are the same image then the difference mode returns a pure black color, because F - B = B - F = 0. If the  foreground however has shifted a few pixels, then a grey pattern will appear.

SUBTRACT:  B - F
The color codes are subtracted, resulting in a darkened image. If the result is negative, then pure black (0) is returned.

DIVIDE: B + B/F
This blend mode can be used to lighten the background without clipping, as far as B <= F. The divide blend mode is also used to remove a color cast (an unwanted tint) in your image. Create a foreground color with the same colorcode as the unwanted tint. Dividing the color code of each pixel by this foreground color will give a result of B/F = 1 (white) if the pixel color is the same as the foreground color.

GROUP 5: COMPONENT

The previous blend modes are based on the RGB color model. Group 5 assumes another color model: the HSV or Hue, Saturation and Value model. A hue is a combination of the three primary colors, where one of primary colors is at full intensity, the other is at variable intensity and the third is at zero intensity. For example, Red (1,0,0), orange (1, 0.5,0), yellow (1,1,0), green (0,1,0), … are all hues. Saturation refers to the "pureness" of the hue or the amount of grey added to the hue: (1,0.5,0), (1,0.625,0.25), (1,0.75, 0.5), …, (1,1,1) are all less saturated versions of orange. Lastly, value refers to the global intensity of the color: (1,0.5,0), (0.75,0.375,0), (0.5,0.25,0), … are all less intense versions of the same orange hue. The nice thing about the HSV model is that you can easily change one aspect (e.g. saturation) without changing the other. And this is exactly what the following blend modes are doing.

HUE: 
This blend mode keeps the hue of the foreground  layer, and blends the value and saturation of the background layer. The result is the background layer with the hue of the foreground layer. If the saturation of the foreground layer is zero, which means that all three primary colors have the same intensity and there is no dominant color,  then the hue is taken from the background layer.

SATURATION:
The saturation of the foreground layer is used. The value and hue come from the background  layer. You can use this blend mode to quickly create a grey scale image of the background by creating a foreground layer with saturation = 0.

COLOR:
Uses the hue and saturation of the foreground color and the value of the background color. 

VALUE:  Keeps the value (global intensity) of the foreground layer, and blends it with the hue and saturation of the background layer.

