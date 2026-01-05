# Code documentation
## P<sub>TMM</sub> M<sub>odding</sub> L<sub>anguage</sub>
PML is very similar to GML, it however introduces some key differences.
### Major scoping changes
The way variables are resolved is very different from GML and more akin to actual languages such as JavaScript or such.
#### 🗺 Scopes
> A scope is a sort of zone that contains its own variables, and even some of it's own sub-scopes inside of itself.

A scope is essentially a block of code that can "see" only what's inside of, and above it.

**A good rule of thumb is that a scope's boundaries are formed by `{` and `}`.**
##### Example
```js
// This is the highest visible scope, the mod scope.
cool_variable = "..."

if (condition)
{ /* ⬅ Scope 1 begin */

  // This is a scope inside of the mod scope.
  trace(cool_variable) // ✅ We can access cool_variable, because it's above this current one.
  
  if (another_one)
  { /* ⬅ Scope 2 begin */

    var my_own_var = "Can't see me"
    trace(my_own_var) // ✅ This also works, it's inside the current scope.

  } /* ⬅ Scope 2 end */


  trace(my_own_var) // ❌ This doesn't work, this variable isn't accessible, it's inside of a scope that's already ended.

  var a = 2;
  trace(a * 5)

} /* ⬅ Scope 1 end */

if (condition)
{ /* ⬅ Scope begin */

  var a = 3 // ❗❗ You can create **ANOTHER** variable called a, because it's in a separate scope.
  trace(a) // ⚠ This refers to the current a, since remember: Variables are only resolved from the current-scope upwards, the other a is **adjacent** to it.


} /* ⬅ Scope end */

// ❗ You can even make scopes that aren't inherent statements!
{ /* ⬅ Scope begin */
  

  // ❗ You can do this if you want to do some one-off computation without polluting the current scope with some useless variables.
  var a = 1, b = 2, c = 3
  trace(a * b + c)


} /* ⬅ Scope end */
```

Now this seems a bit convoluted, isn't it? Don't worry, if you've used other languages, this could make intuitive sense.

**In fact, this may come to your service,** in PML, ****there is no `other`****, this is because PML can figure out where your variable comes from, thanks to the scoping system!

```js
with (obj_player)
{ // This is a scope! The with (...) starts the current objects' scope.
  with (obj_somethingelse)
  { // Another scope
    vsp += 1 // This `vsp` variable can be seen without issue. It is above this current scope, it is by definition reachable.
  }
}
```

⏩ It is important to note that **defining a variable without var immediately puts it in the highest scope**, so if you want to make variables visible everywhere I guess you could do that. For functions **that would put them at the highest scope inside the function, not the actual highest one** though.

#### Closures
This also has to do with scopes, but trust me, it's actually pretty useful.
##### A common annoyance
Over in GML, I've encountered this issue a lot...
```js
////// IN GML LAND ////// 
var important = "..." // This variable contains important data!
function func()
{
  trace(important) // the variable can't be seen
}
func(); // ❌❌ This doesn't work
```
This is because, well, GML doesn't have this scoping system, it can only "scope" to a single zone, either an entire script, or an instance.

To bypass this, you have to use a `method(...)`.
##### The solution
**The way this is solved in PML,** is that the function **"remembers" the scope it has been created from.**

A function can see variables from its current scope, and all ones _above it, visually_.


#### This is the case in real programming languages also!

##### **Example**
```js
var data = [1, 2]
function my_cool_func()
{
  for (var i = 0; i < array_length(data); ++i)
    trace(data[i])
}

my_cool_func(); // ✅ PML is amazing, isn't it? This just works.
```
#### However, it is essential to note that it _literally_ remembers the scope it has been **created from**, in a **literal sense**! This means...
```js
function func()
{
  trace(vsp)
}

with (obj_player)
  func(); // ❌ This won't work, func() only sees the mod-scope, containing the `data` variable, and its own.
```
#### It doesn't adapt to the current scope it is inside of.
##### **More examples..**
```js
var arr = []

for (var i = 0; i < 3; i++)
{
  array_push(arr, function ()
  {
    trace(i)
  })
}

arr[0](); // traces 0
arr[1](); // traces 1
arr[2](); // traces 2

// Every iteration is a separate scope that the functions remember.
```

#### Small changes...
```js
// Say goodbye to those weird symbols...
arr[0]
struct["key"] // ... not struct[$ "key"]
dslist[0] // ... not dslist[| 0]
dsmap["key"] // ... dsmap[? "key"]

// There also isn't repeat. Uhh.. My fault.. :( I'm sorry

// You can also skip brackets with **everything** to make one-statement blocks.
function oneliner(a, b)
  return a * b

// You can represent \n by literally pressing enter in the middle of a string..
var hello = "hello
there";

// macros don't exist, however, you can split your code into multiple files
// and then you can "paste" those files by doing
`include "init.pml"
`include "library.pml"

// This replaces the `includes with the entire file, literally pasting it
// This makes it so you can clean up your code a bit..

// By literally pasting by the way, I kind of literally mean it.
// They can kind of be used like macros.

// And static is more literal:
function counter()
{
  static c = 0;
  return c++;
}
trace(counter())

// Which means it behaves literally in constructors
function Person() constructor
{
  static Func = function ()
    trace("Don't do that :/ Just define it normally") // Won't work
}
```
#### More flexible syntax
The PML parser is more literal with how code is understood by it. I also kind of just made it more flexible.

This allows for:
```js
// Assignments are operations also
var a = 0;
b = (a = 3) + 2; // yields 5

var c = 4;
b = (c += 6) - (c += 5); // c = 15 and b = -5

if (c = (b--) * 5) > 5 // false
{
  // Won't run.
  trace("aww")
}

// Multi-step for loops
for (var i = 0; i < 10; {
  i++;
  trace("Did a step!")
})
{
  trace(i)
}

// Exponents
trace(5**2) // 25
```
# ⚠ THIS MEANS THAT USING SINGLE EQUALS `=` FOR COMPARISONS WILL NOT WORK AS INTENDED!!!
```js
a = 2
if (a = 1) // ❌❌❌❌❌❌❌❌❌ Bad
{
  // You will burn in hell if you do this
  trace("THIS RUNS BECAUSE a = 1 EVALUATES TO 1 (assignment) WHICH IS A TRUTHY VALUE!!")
}
```
#### Missing features
- Do-while loops (or more like do-until in GML).
- Try-catch and throw.
#### Notes
PML was still made by me, for a pizza tower mod, excuse some brevity or a bit of weird-ness here and there, I essentially made a whole programming language for fun. Cut me some slack :(