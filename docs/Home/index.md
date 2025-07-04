[EventPlus]: (https://create.roblox.com/store/asset/104918093574132/EventPlus)

# Introduction

EventPlus is a lightweight, flexible module for managing custom events entirely in memory—no BindableEvents or RemoteEvents required.  
It simplifies communication between systems in Roblox by offering an expressive and powerful event API.

---

## 🔍 Overview

[EventPlus] lets you define and manage named events (`"PlayerJoined"`, `"InputReceived"`, etc.) within your Lua scripts.  
It provides rich utility functions for transforming, timing, and reacting to events, helping you write modular, decoupled game logic.

---

## 🚀 Key Features

- ✅ **Reusable Listeners** via `on`, `once`, and `off`
- 🧠 **Timing Utilities**: `debounce`, `throttle`, `delay`, and `repeatEvent`
- 🔁 **Event Shaping**: `filter`, `map`, `pipe`, and `all/any` composition
- ⏱ **Timeout & Coroutine-Friendly**: `wait`, `waitFor`, `onceWithTimeout`
- 🧼 **No Roblox Dependencies**: No need for `BindableEvent` or `RemoteEvent`

---

## ⚡ Quick Start

```lua
local EventPlus = require(game.ReplicatedStorage.EventPlus)

-- Listen for an event
EventPlus.on("PlayerJoined", function(playerName)
	print(playerName .. " has joined the game.")
end)

-- Fire the event
EventPlus.broadcast("PlayerJoined", "Alex")

```

----

## How It Works

- Events are referenced by string names, like ``"PlayerDied``" or ``"InputReceived"``.
- Listeners are stored internally and removed automatically if you use ``once()`` or manually via ``off()``.
- You can broadcast any number of arguments using ``broadcast()``.

----

## Example: Combining Events

!!!! info "Info"
This example shows how EventPlus.all waits for multiple events before executing the callback.

!!! example "Example"
    ```lua
    EventPlus.all({ "PlayerReady", "MapLoaded" }, function(results)
        print("Game ready with:", results.PlayerReady, results.MapLoaded)
    end)

    EventPlus.broadcast("PlayerReady", true)
    EventPlus.broadcast("MapLoaded", "Forest")
    ```


----

## Notes
- ``EventPlus`` is fully local unless integrated with networking layers.
- Ideal for server-only or client-only systems.
- Works great in systems where events need to be "shaped" or chained.

!!! info "Important"
    EventPlus does not use Roblox's `BindableEvent` or `RemoteEvent` objects. All events are purely in-memory and local to your script environment.

!!! warning "Warning"
    Event names are global within EventPlus. Avoid using the same event name across unrelated systems unless intentional, as this may cause unexpected side effects.
