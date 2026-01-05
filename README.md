# Pizza Tower: Multi Mayhem Modding Documentation
> This is the documentation for PT:MM's mod loader.
#### By the way, for questions, you can join the discord and ask me questions directly. Just make it manageable, please, and try reading this atleast.
## Essential information
- Mods are not written in GML, they use _PML_, which has some differences (arguably for the better).
- Mods can't modify anything outside online sessions.
- Mods are sandboxed and can't access outsider data/functionalities.
- Some functions may be blacklisted for security purposes.
## What does a mod contain?
> A mod may contain **Sprite information**, **Code**, **Rooms**, and its own **External files**.
## How does a mod look?
Mods are centered around their configuration file, called the `config.dev.json`. The file structure from this point onwards is ambiguous, and doesn't really matter, **as long as the paths specified in the config file match.**
## How do I make a mod?
> You'd use an essential tool: the ***PT:MM Compiler***.
This is required for anything modding-wise.

### The PT:MM Compiler
> The compiler is what turns your _mod's source code_ into the _actual deal_.

#### Basic usage
> To load a mod, you'd drag the `config.dev.json` file into the compiler window. You can then press the compile button to actually compile it.
#### Errors and compiler warnings
> They're shown in the console (the big glaring text box), if that wasn't obvious already.
### The final output
> Once you've compiled your mod, **you'll get a final file with the `.ptmm` format, usually called `output.ptmm`.** 

*This is the entirety of your mod packaged into a single file.*

It contains all of the code, sprites and the rest of everything.

##### This is what you put inside of the server's `mods/` folder.

**In addition,** if your mod contains any server code, an additional file appended with the `_server` suffix will appear, this is your server mod file, **which you also drag into the `mods` folder**. **This contains your mod's server-side code.**

## And to begin?
# Before jumping in, read the actual text before this, you won't regret it.
> [PML guide](pml.md)

> [Code (client side)](code.md)

> [Code (server side)](code_server.md)
