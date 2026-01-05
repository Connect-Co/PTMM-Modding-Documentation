# Palettes
PT:MM provides its own paletting system allowing for more advanced custom palettes for mods.
This will be split into 3 categories:
- [Basic](#basic) The stuff that will be all that is needed for basic clothing addition and replacement.
- [Drawing](#drawing) Functions for drawing clothing made in the palette API.
- [Advanced](#advanced) Functions for if the other ones are not enough.
- [Effects](#effects) Functions for creating special transformation effects like pepper pizza.
## Basic
```js
// An individual clothing item! set pattern to undefined or "none" for no pattern.
new clothing(id, index, default, pattern, name, description);
// For animation through multiple palette indices.
new clothing_animate(id, index1, index2, speed, default, pattern, name, description);

new clothing_ext(id, function(drawer_id, profile, varstruct)
{
  return get_pep_color_arr();
}, default, pattern, name, description);
// For clothing color that needs live updating. The profile argument will be better explained in
// the drawing section, but just know that you can use profile_cloth_wearing() to get the clothing
// drawn and profile_get_char() for the character. The varstruct contains other details such as:
// spr, img, x, y, xscale, yscale, rot, col, a, isgus, and fx. Finally, the return value must be an
// array of color values of rows like so: [r1,g1,b1,a1,r2...]

get_pep_color_arr(raw=false);
// Returns the array of color values for peppinos default palette. raw is first column, not is second.
// Also gus and noise versions. Makes clothing_ext use more convenient.

convert_shd_to_nice(arr);
// --> Think you want to manipulate the array with [col1,a1,col2...] instead? Just use this. But be sure to use:
convert_nice_to_shd(arr); // When finally returning the array.
```
> The clothing constructor automatically adds it to the palette manager.
## Drawing
```js
// Patterns! Sprites with multiple frames are animated automatically. Mix is a special variable:
// When it is zero, patterns will be drawn behind every row, but if not, then the column selected will be used
// as a mask for the pattern. White means it gets drawn on the row, and transparent, not.
new custom_pattern(id, spr, speed=0, mix=0);

// Allows you to manipulate the pattern live. Not all return arguments have to be provided. Scale is applied 
// first, then position, then rotation is applied at the origin. Default origin is center of sprite drawn.
new effect_pattern(id, spr, function(drawer_id, profile, varstruct)
{
  return [x, y, rot] or [x, y, xscale, yscale, rot, origx, origy]
}, speed=0, mix=0);

new stack_pattern(id, array_of_ids);
//Stacks multiple patterns on top. Mix gets really useful here. Order is important.
```