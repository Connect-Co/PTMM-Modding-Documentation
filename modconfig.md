# Mod Configuration API
Here you can find the documentation for the Mod Configuration API, otherwise known as Flingo Computer (FPC for short).

Players can access FPC in two ways: the `/config` command, and the physical computer in the tower's bottom floor.

### Additional Info
When a player opens the Flingo Computer, they will be greeted with three options: `Mod Config`, `Mod Credits`, and `Games`. `Mod Config` and `Mod Credits` are separated into different tabs for each mod running on the server. The `Mod Credits` page is pretty lackluster right now, but I hope to improve it.

Each mod can create options to customize their mod to each player's content. The three types are toggles, sliders, and lists. All options are clientside as of now. They are saved to the player's `saveData.ini` file under `PTMMModConfig`.

# Functions

### Adding Options
`mod_config_add_toggle(name, default, function)`
- `name` (string): The name of the option.
- `default` (boolean): The default value of the option.
- `function` (function(value)): A function called when the option's value is changed. `value` is the current value of the option. This function can be used to change other variables or call other functions based on the new value.
- **Returns: undefined**

`mod_config_add_slider(name, default, function)`
- `name` (string): The name of the option.
- `default` (real): The default value of the option, from 0 to 1.
- `function` (function(value)): A function called when the option's value is changed. `value` is the current value of the option. This function can be used to change other variables or call other functions based on the new value.
- **Returns: undefined**

`mod_config_add_list(name, options, default, function)`
- `name` (string): The name of the option.
- `options` (array\[string\]): An array of strings for each option name.
- `default` (integer): The default value, an index in `options`, of the option.
- `function` (function(value)): A function called when the option's value is changed. `value` is the current value of the option as an index in `options`. This function can be used to change other variables or call other functions based on the new value.
- **Returns: undefined**

`mod_config_add_input(name, default, softlock = 0)`
- `name` (string): The name of the option.
- `default` (array\[tdp_input_action\]): The default tdp_input_action array of the option.
- `softlock` (integer): Whether it could cause a softlock if the input isn't properly bound. This will give the same binding protections as menu inputs. Inputs will only notice inputs of the same softlock integer.
- **Returns: The tdp_input_key for this option**

### Mod Credits
These functions basically work the same as the mod config functions but it's in its own category because

`mod_config_add_credit(name, subtitle)`
- `name` (string): The name of the contributor.
- `subtitle` (string): The subtitle for the credit. Preferably used for what the job they did was (i.e. spriter).
- **Returns: undefined**
