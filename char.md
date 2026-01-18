# Character API
The character API allows you to easily add custom characters that can be accessed with the /char command. I'd like to note that this API is not fully complete in the sense that it's always open to improvements and changes in the future.

## General
You add new characters with the 'define_character' constructor. A custom character requires some assets such as sprites for the portrait, title, icon and the clothing. These assets are utilized in the afformentioned character select menu, alongside some additional information.

## How to use
### `define_character(id, portrait, title, icon, cloth, start_func, [color], [end_func])`
#### Arguments
- id - Unique identifier for a character, expects a **string**. **[REQUIRED]**
- portrait - The portrait that appears on the left side of the screen, expects a **sprite**. **[REQUIRED]**
- title - The title that appears next to the portrait, expects a **sprite**. **[REQUIRED]**
- icon - The icon that appears on bottom left, expects a **sprite**. **[REQUIRED]**
- clothing - The clothes that get shown while you're changing your outfit, expects a **sprite**. **[REQUIRED]**
- start_func - The function that gets called when you choose a specific custom character, expects a **function**. **[REQUIRED]**
- color - Color of the arrow next to the icon, expects a **GameMaker color constant** or a **hex value**. **[OPTIONAL]**
- end_func - The function that gets called when you stop playing as a specific custom character, expects a **function**. **[OPTIONAL]**
#### Example use
mrstick = define_character("mrstick", spr_dresser_stick, spr_dresser_stick_title, spr_dresser_stick_icon, spr_dresser_stick_clothing, ms_choose, #f87018, ms_back)

## Adding new clothes
- The procedure of adding new clothing to a custom character will require you to follow the steps seen in the [Palette API](kenanyazbeck.com) documentation, however there's some small adjustments needed.
- When you're using the `dresser_add` function, you're going to need to supply your character's unique ID as the first argument, and custom characters are going to be limited to their own assortment of clothes unless you decide to add all of either Peppino or The Noise's clothes.

## Adding modded states
- You're able to add **modded states** for your character/mod, this first requires creating the state enum that's going to be used. If you're unsure on how to do this, refer to [The Basics](kenanyazbeck.com) section of the documentation.
- To make use of these newly added states, you're going to need to use the `mod_state` function with the only argument being the state you're referring to, this function will return an offset value based on Pizza Tower's base **states** enum so no existing states are overriden or overlap. Make sure to always use this for good practice and maintainability.
- The returned state values are now open to use with a state machine, or just regular checks just as if you'd added a new entry to the base enum. Make sure that anything involving strings is kept inside a check that assures you're playing as the desired character, so it doesn't mess up any other characters.

### Tips and tricks
#### FMOD related
- You're able to replace FMOD instances relating to the player simply by overwritting the variables that contain the fmod instances, with `fmod_event_create_instance`. (make sure to manage these properly as to not cause a memory leak!)
- Alongside that, you're able to replace base game events with the  `fmod_event_replace` function which simply requires you to supply the original and the new event path, and once you're done with it, you can simply just use `fmod_event_restore` with the original path to render any changes obsolete.
#### Sprite related (in case you need it, would recommend using this information)
- The base portrait sprite is 400x400.
- The base title sprite is 400x200.
- The base icon sprite is 79x63.
- The base clothing sprite is 200x200.
- ^ All the sprites above use **middle centre** as their origin.
#### PTMM-Specific character vars
- update_mask_index - bool, decides whether the mask index will be updated every frame.
- jump_height - int, allows you to change the jumping height of the character. (make sure to reset back to 11!)
- uppercut_enabled - bool, changes whether the player can perform the "uppercut" move.
- standing_superjump - bool, toggles **The Noise's standing superjump**, performed by pressing **up + jump**.
- do_ledge_bump - bool, allows for an easier way to add ledge bumping for certain characters, they'll be snapped to the ground if they're close enough.
- use_tv_palettes - bool, for characters to have custom TV palettes (not the sprites displayed but the TV itself) like The Noise's yellow TV.