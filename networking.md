# Networking
That's the multiplayer part of the multiplayer mod.

You'll meet the concepts you need to know in order to properly add networking features to your mod.

# What you get for free
PT:MM already handles asset management for you, downloading the mods, running them, and the networking capabilities.

# Networking philosophy
A good rule of thumb is:
> **The server should be the main source of truth**, which means that any "important" task should be done by the server.

That is mainly **to prevent cheating** by *not trusting the client too much*, since if one player modifies their client, and you trust the client too much, hackers'll have a field day.

This is why PT:MM is mostly *server-authoritative*. Stuff such as grabbing an enemy or killing it requires approval from the server. Clients merely send a request to do so, the server actually achieves it and replies back.

---

# Reliability
> There are 2 types of packets: **TCP/reliable packets** and **UDP/unreliable packets**.

Each one has their own pros and cons, for their own use-cases.

### Reliable packets:
> They're packets that always arrive, and in-order. They're sent carefully and managed properly to ensure stability in the connection by TCP.

**However, that comes at the cost of latency.**

Ensuring these packets arrive takes time, which in turn slows things down.

#### Picture this:
We want to send _reliable packets_ $A$, $B$ and $C$.

Even if $B$ and $C$ arrive successfully, the receiver still has to wait for $A$ to arrive to make sure that everything is processed in order.

Even worse, if $A$ ever gets lost (due to lag, or due to network hiccups), it needs to be resent, **that's extra latency, and lag.**

### Unreliable packets:
> They're catapulted with no care in the world and do not guarantee any order. They may get lost and won't get resent forever.

**However, they're extremely fast.**

The fact that they're fired-and-forgotten is what makes them so unbelievably quick, the network doesn't wait for anything and fires them off without a single care in the world.

#### Picture this:
We want to send _unreliable packets_ $A$, $B$ and $C$.

$A$ gets lost and is shot dead in the Bronx.
$C$ arrives before $B$ and get processed out of order.

However, no waiting occurs, they get sent at the speed of the network.

## When to use them?

> TCP packets must be used **sparingly**, reserved only for events that <u>_must_</u> be received by everyone. Player life-cycle events can't be ignored, same for chat messages, or an enemy getting killed.

> UDP packets **can be used for mass-information that can be lost, or arrive out of order**. Useful for sending player position packets that arrive in bulk, if one of them gets lost, another one will arrive soon enough to replace it.

# RPC
> **They're functions that can be called through the network**, which subsequently makes them extremely useful and gives them a wide range of use-cases, this is why it's useful to know them.
---
### Different parties call RPCs their own way:
- Clients call server RPCs by **sending an RPC request to the server**, since there's only one endpoint to call RPCs from (the server)
- The server calls client RPCs by **sending a request to the specific client** it wants to target.

#### Because of this one-sidedness, server RPCs and client RPCs can share the same name.
---
#### Example:
Given a server RPC function called `sum` that adds 2 numbers, and a reply rpc called `sumReply`:
```js
// On the client...
net_rpc_register("sumReply", function (data)
{
  trace(data) // Server reply
})

// "true" since we want to call this RPC reliably.
net_rpc_call("sum", true, 1, 1) // we send 2 arguments: 1, and 1

// On the server...
net_register_rpc("sum", function (caller_id, a, b)
{
  // The id of the caller is also passed in now
  net_rpc_call("sumReply", caller_id, true, a + b) // We send back our reply with one parameter passed: a + b
})
```
> NOTE: **sumReply can also just be named sum**, but I've given it a different name to make a clear **distinction between client -> server and server -> client**.

The following networking requests will happen:
```
Client 0 (RPC Call "sum") ➡ Server
-- RPC runs on server --
Server (RPC Call)         ➡ Client 0
-- RPC runs on client --
```
