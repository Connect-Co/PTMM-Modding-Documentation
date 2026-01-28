# Flingo Computer Gaming
You may have noticed the `Games` menu in FPC. The API for this is finally finished, so you can straight up make different games.

### Initializing a Game
To initialize a game, you must run `mod_config_game_init()` in your mod's create code. The `create`, `step`, and `draw` arguments are formatted like this.
```js
function (s) { /*code goes here!*/ }

//I find it best to format it like this:
function (s) { with (s) {
    var myvar = 0;
    x += myvar;
    y = 0;
}}
//As opposed to this:
function (s) {
    var myvar = 0;
    s.x += myvar;
    s.y = 0;
}
//Having to spam s. on every variable gets annoying, so it's easiest to do with (s)
//You don't have to write it like this, but that's just my recommendation.
```
# Functions

`mod_config_game_init(name, create, step, draw)`
- `name` (string): The name of your game.
- `create` (function(s)): A function that runs the very frame your game was opened. The Flingo Computer object is passed in as `s`.
- `step` (function(s)): A function that runs every frame your game is open for. Works like GameMaker's step event. The Flingo Computer object is passed in as `s`.
- `draw` (function(s)): A function that runs every frame your game is open for, intended for drawing. Works like GameMaker's draw event. The Flingo Computer object is passed in as `s`.
- **Returns: undefined**

`mod_config_game_end()`
- **Returns: undefined**

# Example

Here is the Flingo Computer Gaming API in context with a fun snake game:
```js
mod_config_game_init("Snake", 
function(s) { with (s) {
    snake_x = [0]
    snake_y = [0]
    sdir = 0
    gwidth = obj_screensizer.actual_width/8
    gheight = obj_screensizer.actual_height/8
    ax = irandom(gwidth-1)
    ay = irandom(gheight-1)
    time = 0
    up = 0
    down = 0
    left = 0
    right = 0
}},
function(s) { with (s) {
    scr_menu_getinput()
    if (time%4==0)
    {
        if (!up) up = key_up2
        if (!down) down = key_down2
        if (!left) left = abs(key_left2)
        if (!right) right = abs(key_right2)
        
        if (right && sdir != 2)
            sdir = 0 
        else if (left && sdir != 0)
            sdir = 2
        else if (down && sdir != 3)
            sdir = 1
        else if (up && sdir != 1)
            sdir = 3
        
        if (snake_x[0] == ax && snake_y[0] == ay)
        {
            array_push(snake_x, -1)
            array_push(snake_y, -1)
            while (array_contains(snake_x, ax) && array_contains(snake_y, ay))
            {
                ax = irandom(gwidth-1)
                ay = irandom(gheight-1)
            }
        }
        var _snx = []
        array_copy(_snx, 0, snake_x, 0, array_length(snake_x))
        var _sny = []
        array_copy(_sny, 0, snake_y, 0, array_length(snake_y))
        var i;
        for (i = 1; i < array_length(snake_x); i++)
        {
            snake_x[i] = _snx[i-1]
            snake_y[i] = _sny[i-1]
        }
        
        
        snake_x[0] += (sdir==0)-(sdir==2)
        snake_y[0] += (sdir==1)-(sdir==3)
        if (snake_x[0] > gwidth || snake_x[0] < 0 || snake_y[0] > gheight || snake_y[0] < 0) mod_config_game_end()
        else
        {
            for (var i = 1; i < array_length(snake_x); i++)
            {
                if snake_x[0] == snake_x[i] && snake_y[0] == snake_y[i]
                {
                    mod_config_game_end();
                    break;
                }
            }
        }
        up = false
        down = false
        left = false
        right = false
        
    } else {
        if (!up) up = key_up2
        if (!down) down = key_down2
        if (!left) left = abs(key_left2)
        if (!right) right = abs(key_right2)
    }
    time++
}}, 
function(s) { with (s) {
    draw_set_color(c_lime)
    for (var i = 0; i < array_length(snake_x); i++)
    {
        draw_rectangle(8*snake_x[i], 8*snake_y[i], 8*(snake_x[i]+1), 8*(snake_y[i]+1), false)
    }
    draw_set_color(c_red)
    draw_rectangle(8*ax, 8*ay, 8*(ax+1), 8*(ay+1), false)
}})
```
