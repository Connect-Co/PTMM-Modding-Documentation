# Code documentation (server-side)

Writing server-side code is a bit of a different story.

First of all, you can't simply use `with(obj_something)` to access data, that's not how it works.

Server mods don't run in-engine, which means that there aren't any built-in variables, or any sort of notion of rooms, or instances, or anything (although the server still has some form of room data, and knows whats in them, which player is where, etc).

You interact with the players through network requests.

---

# PML on the server

The language runs on a completely different environment, so there are to be some expected changes.

> Most of the language stays intact, however `with` statements become more rudimentary due to the absence of an engine.

You can no longer use `with (object_asset)` since there isn't any asset system, we're no longer running in-engine, however it still works for struct instances:

```js
var struct = { a: 1 }
with (struct)
{
  trace(a) // 1
  b = 2;
}
trace(struct) // { a: 1, b: 2 }
```

> `undefined` and `null` are also now synonymous.

This means that `undefined == null` and that they can be used interchangeably.

> There's also the addition of <b>type coercion</b>. Basically, PML will try to convert some values to `strings` if it finds that they can't operate together directly.

This is kind of akin to JavaScript.
Stuff such as `1 + "1"` now works fine without any explicit conversion.

---

# Base functions

## Logging & utilities

### `trace(....)`

Converts every parameter to string and concatenates them together to finally log them into the console.

> Usage: `trace("Hello!", 1, 2)`
> Returns: Nothing

### `string(a)`

Converts the parameter to string.

> Usage: `string(1)`
> Returns: string

### `typeof(value)`

Returns a string describing the type of the given value.

Possible return values:
`"number"`, `"int32"`, `"array"`, `"bool"`, `"string"`, `"struct"`, `"method"`, `"null"`, `"unknown"`

> Usage: `typeof(1)`
> Returns: string

---

## Randomness

### `random(a = 1)`

Generates a random number from 0 to $a$. (Inclusive)

> Usage: `random(2)`
> Returns: number

### `random_range(a, b)`

Generates a random number from $a$ to $b$. (Inclusive)

> Usage: `random_range(1, 4)`
> Returns: number

---

# Arrays

### `array_length(a)`

Gets the length of an array.

> Usage: `array_length([1, 2])`
> Returns: number

### `array_push(arr, element)`

Pushes the element to the array.

> Usage: `array_push(arr, 1)`
> Returns: number (new array length)

### `array_pop(arr)`

Pops the last element from the array and returns it.

> Usage: `array_pop(arr)`
> Returns: Any or null

### `array_shift(arr)`

Pops the first element from the array and returns it.

> Usage: `array_shift(arr)`
> Returns: Any or null

### `array_delete(arr, index, count)`

Deletes $count$ amounts of elements from $arr$ starting at $index$.

> Usage: `array_delete(arr, 1, 2)`
> Returns: Array of deleted elements

### `array_sort(arr, sort_decider = default)`
Sorts the array based off of a decider function's return value.

By default, the default ascending sorting function is used if nothing is provided for the `sort_decider` argument:
```js
array_sort(arr)
// Equates to..
array_sort(arr, function (a, b) return a - b)
```

If a decider were to be provided, it must take 2 arguments, and should return any of the following values to affect the sorting:
- `0`: if $a$ and $b$ are equal
- `<= -1` if $a$ goes before $b$
- `>= 1` if $a$ goes after $b$

> Usage: `array_sort(arr)`<br>Returns: Nothing

---

# Strings

### `string_length(str)`

Gets the length (number of characters) in the string.

> Usage: `string_length("hello")`
> Returns: `5`

### `string_contains(str, substring)`

Checks if `substring` exists anywhere inside `str`.

> Usage: `string_contains("pizza tower", "tower")`
> Returns: `true`

### `string_set(str, index, value)`

Sets the character at position `index` of `str` to `value`
(returns a new string because strings are immutable).

> Usage:
>
> ```js
> string_set("cat", 1, ord("u"))
> ```
>
> Returns: string

---

# Structs

### `struct_exists(struct, key)`

Checks if the following $key$ is present inside of the struct.

> Usage: `struct_exists({ a: 1 }, "a")`
> Returns: boolean

### `struct_get_names(struct)`

Returns an array containing all keys (field names) inside the struct.

> Usage: `struct_get_names({ a: 1, b: 2 })`
> Returns: Array of strings

### `struct_remove(struct, key)`

Removes the field with name `key` from the struct.

> Usage:
>
> ```js
> var s = { a: 1, b: 2 }
> struct_remove(s, "a")
> ```
>
> Returns: null

---

# Room Management

### `mod_room_add(room_id, room_name)`

Adds a custom room to the project.
Useful for mods that dynamically define new rooms.

### `mod_room_add_instance(room_id, name, asset, x, y, static_id)`

Adds an instance to a custom room at the given coordinates.

### `room_get_info_id(room_id)`

Returns a struct describing the room: name, layers, and all instances in it. **Only works for vanilla game rooms**

---

# Networking — Players

### `net_get_players()`

Returns an array of all connected players as structs.

### `net_player_get(player_id)`

Returns a single player as a struct, or null if not found.

### `net_send_chat(message, player_id = -1)`

Sends a chat message to a player or to everyone if `player_id` is `-1`.

---

# Networking — RPC

### `net_rpc_call(name, recipient, reliable, arg1, arg2, ...)`

Calls a registered RPC function on one player or all players.
`recipient = -1` broadcasts to everyone.

### `net_register_rpc(name, function)`

Registers a new RPC callable from the network.
The function receives `(caller_id, ...parameters)`.

### `net_clear_rpc(name)`

Unregisters a previously registered RPC.

---

# Networking — Synced Objects

### `net_create_sync(object_asset, room_id, custom_instance = -4)`

Creates a new synced object owned by the server and returns its sync ID.

### `net_sync_vars(sync_id, struct_of_vars, udp = false)`

Updates variables on a synced object and broadcasts the change.
Can optionally use UDP.

### `net_sync_set_setup_vars(sync_id, array_of_varnames)`

Defines which variables must be sent to clients on initial sync.

### `net_sync_destroy(sync_id)`

Destroys a synced object.
Returns `true` on success, `false` if not found.

### `net_get_syncs()`

Returns an array of all synced objects as structs.

### `net_get_syncs_of(player_id)`

Returns all synced objects owned by a specific player.

---

# Networking — GameState

### `net_gamestate_set(key, value)`

Sets a GameState value (server-side persistent state).

### `net_gamestate_commit()`

Commits all pending GameState changes and syncs them to clients.

### `net_gamestate_get(key)`

Gets a GameState value and converts it back to a PML object.

---

# Networking — Events

### `net_add_event_listener(name, function)`

Registers a listener for a server event.
Your function is called each time the event fires.

---