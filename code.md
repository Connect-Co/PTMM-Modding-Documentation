# Code documentation (client-side)
The API at your disposal.

Of course, you still have the entire game, and almost every GML function.

Refer to some decomps, the GameMaker docs and UTMT for those.

The usages are simple examples by the way.

# Essentials
## `mod_log(message)`
Logs a message inside of the `/log` menu for mods and such.
> Usage: `mod_log("I mean, that's kind of it")`<br>Returns: Nothing

## `mod_log_err(message)`
Logs an error message inside of the `/log` menu for mods and such.
> Usage: `mod_log_err("I'm angry now")`<br>Returns: Nothing

## `mod_hook_method(region_name, method)`
This is essential for any sort of actual modding.
Hooks are bits of PML code you can run in specific zones we've defined.

These are the current hook region names:
```
online_utilities:warp_to_room
scr_characterspr
scr_drawcontroller:draw_enemy:top
scr_drawcontroller:draw_enemy:bottom
scr_drawcontroller:draw_player:top
scr_drawcontroller:draw_player:bottom
scr_playerreset
player:state:onlinespectate
obj_player1:step:state_machine:before
obj_afterimagecontroller:draw:top
obj_afterimagecontroller:draw:bottom
obj_arcario_shot_dead_in_the_bronx:step:top
obj_baddie:alarm:1:bottom
obj_baddie:alarm:2:bottom
obj_baddie:alarm:3:bottom
obj_baddie:alarm:4:bottom
obj_baddie:destroy:top
obj_baddie:destroy:bottom
obj_baddie:room_start:bottom
obj_baddie:end_step:bottom
obj_camera:draw_gui:bottom
obj_camera:step:top
obj_camera:step:bottom
obj_lap2visual:alarm:0
obj_lap2visual:create:bottom
obj_lap2visual:draw_gui:top
obj_lap2visual:step:top
obj_lap2visual:step:bottom
obj_lapportal:create
obj_lapportal:room_start
obj_lapportal:step:top
obj_lapportal:step:enter_portal
obj_lapportal:step:bottom
obj_lapportalentrance:step:bottom
obj_otherplayer:postsync
obj_otherplayer:step
obj_player1:step:pre_state_machine
obj_player1:step:state_machine
obj_player1:step:top
obj_player1:step:bottom
obj_startgate:step:top
obj_tv:draw_gui:top
obj_tv:draw_gui:bottom
obj_tv:room_start:top
obj_tv:room_start:ranks_setup
obj_tv:room_start:bottom
obj_tv:step:top
obj_tv:step:bottom
```

> Usage: 
> ```js
> mod_hook_method("obj_somewhere:idk", function ()
> {
>   // Code here
> })
> ```
> Returns: Nothing

## `mod_create_object(obj_name, events)`
This creates an `obj_modobject` that acts as a sort of puppet. It runs the given functions inside of the specified events. The name is set inside of the `name` variable.

> mod_create_object: 
> ```js
> mod_create_object("object_ig", 
> {
>   step_begin: function () ...,
>   draw_begin: function () ...,
>   end_draw: function () ...,
> })
> ```
> Returns: Instance

By the way, it doesn't really matter if you mix up things a little, like `draw_gui_begin` and `gui_draw_begin`, I've handled those cases for you.

## `mod_object_get(obj_name)`
Gets the specified instance of `obj_modobject` based off of the name. Better than `with`, since it uses a lookup.

> Usage: `mod_object_get("object_ig")`<br>Returns: Instance

## `asset_get_index_mod(asset_name)`
Like `asset_get_index`, but factors in mod assets.
> Usage: `asset_get_index_mod("spr_something")`<br>Returns: Any or -1

## `mod_file_text_open_read(file)`, `mod_file_text_open_write(file)`, `mod_buffer_load(file)`, `mod_buffer_save(buf, file)`
Like their normal counterparts, but limited to the sandbox.

Not like you have a choice, the normal ones are blacklisted.

You still close the handles normally though.

# Networking functions
PT:MM's networking is built on top of our NetGM2 framework. Docs are [here](https://github.com/kenan238/netgm2-docs-client/blob/main/README.md).

Of course, you don't do any of the set-up stuff.

The most useful feature for mods though is **RPC**. Look into that, it covers a bunch of cases by itself.

# Additional functions
## `online_get_player_obj(player_id)`
Returns the NetGM2 sync object for the current player id. If you want to access the instance directly, just access the `inst` property. It may not exist though (not in the same room).

> Usage: `online_get_player_obj(0)`<br>Returns: NetGM2 Sync

## `pvp_get_grabbedid(grabber_pid)`
Returns the id of the person that's being grabbed by the id passed in. -1 if none.

> Usage: `pvp_get_grabbedid(0)`<br>Returns: Number (player id)
## `pvp_get_grabberid(grabber_pid)`
Returns the id of the person that's grabbing the specified player. -1 if none.

> Usage: `pvp_get_grabberid(0)`<br>Returns: Number (player id)

## `online_get_level_info(level_name)`
Returns a level information object for the specified, containing 3 booleans: `keyget` (if the key has been gotten), `keydoor` (if the door has been opened), `gerome` (if gerome has been gotten).

> Usage: `online_get_level_info("entrance")`<br>Returns: OnlineLevelInfo

## `room_get_level(roomname)`
Returns the level name, not a simple string split, scans until the first underscore and returns it.

> Usage: `room_get_level("entrance_1")`<br>Returns: string

## `ellipse_truncate(txt, size)`
Utility function, truncates a piece of text and add a triple dot if the $textlength >= size - 3$

> Usage: `ellipse_truncate("something long", 6)`<br>Returns: string

## `online_is_privileged_id(p_id)`
Informs on if the current player has any sort of administrator permissions (lobby owner and such)

> Usage: `online_is_privileged_id(0)`<br>Returns: boolean

## `create_notif(txt)`
Creates a little notification bubble.

> Usage: `create_notif("Hi hello :D")`<br>Returns: Nothing

## `mod_chat_info(txt)`
Sends an `INFO` chat message, locally visible only.

> Usage: `mod_chat_info("Did you know? Your game will blow up instantly")`<br>Returns: Nothing

## `draw_sprite_hover(sprite, subimg, xx, yy, dodraw = true)`
Draws a sprite, and returns `true` if it is being hovered (relative to screen coordinates).
If `dodraw` is disabled, only the check will be performed, and no actual sprite will be drawn.

> Usage: `draw_sprite_hover(spr_button, 0, 0, 0)`<br>Returns: Nothing

# Rooms
You can always create rooms via the `config.dev.json`, but if you want to make them procedurally, or through code, I can't blame you.

## `mod_room_add(room_id, room_name, width = 960, height = 540)`
Creates a new room, you must provide a unique id, or an error will be thrown.

> Usage: `mod_room_add(29182, "pits of hell")`<br>Returns: Nothing

## `mod_room_get(room_id, error_handle = true)`
Returns a room's information struct. If `error_handle` is set, a room that doesn't exist will throw an error, so I recommend setting it to `false`.

> Usage: `mod_room_get(29182)`<br>Returns: Custom room struct

## `mod_room_instance_add(room_id, object_asset)`
Add a new instance to a room. Note that you can't pass in some sorts of `var` argument, because you're given the struct reference itself, you can just add fields to the struct.

The instance's id is stored inside of the `id` field.

> Usage: `mod_room_instance_add(29182, "obj_solid")`<br>Returns: Custom room instance struct

## `mod_room_instance_set_static_id(room_id, inst_id, static_id)`
Assigns a static id to said instance. A **static id is a unique network id, that lets NetGM2 reference and identify this object**. It must be constant, and it's essential for network elements (such as enemies) to work.

> Usage: `mod_room_instance_set_static_id(29182, 45, 381)`<br>Returns: Nothing

## `room_goto_mod(room)`
`room_goto` but accounts for custom rooms.

> Usage: `room_goto_mod(rm_awesome)`<br>Returns: Nothing

# Input

## `online_ui_scroll()`
Returns a scroll factor. If using a controller, it returns a raw value from the right stick from -1 to 1. 

> Usage: `online_ui_scroll()`<br>Returns: Range from -1 to 1

## `online_ui_left_button(hold = false)`
Checks if the left mouse button is being pressed. If `held` is true, will check if it's being held.

> Usage: `online_ui_left_button()`<br>Returns: boolean

## `online_ui_right_button()`
Checks if the left mouse button is being pressed.

> Usage: `online_ui_right_button()`<br>Returns: boolean
