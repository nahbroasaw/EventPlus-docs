# Input

## Working Examples
#### on(eventName, fn)
{static}
```lua
event.on('teamChanged', function(player) -- Receives the broadcast and the function uses the table
	print(player.Name, 'changed teams!')
end)

event.broadcast('teamChanged', { Name = 'Andrea' }) -- Send's a event broadcast to teamChanged with a table to send to the event
```

----

#### once(eventName, fn)
{static}
```lua
event.once('onceEvent', function(ohwell) -- Same thing as event.on() but it will only broadcast once
    print('[EVENT-EXAMPLE] Once Event triggered')
end)

event.broadcast('onceEvent', true) -- This is the first and last broadcast of 'onceEvent' so it makes it not usable again
```

----

#### off(eventName)
{static}
```lua
local listener = event.once("PlayerJoined", function(player)
    print(player.Name .. " joined (once)")
end)

event.off(listener) -- Disconnects the event even if it's not broadcasted
```

----

#### broadcast(eventName, arguments(any))
{static}
```lua
event.on('announce', function(message) -- Receives the event from broadcast and the arguments: 'supper'
    if message then
        print('ANNOUNCED:', message)
    end
end)
event.broadcast('announce', 'supper') -- Sends the event to 'announce' with arguments 'supper'
```

----

#### debounce(eventName, cooldown, callback)
{recommended}
```lua
event.debounce('myEvent', 2, function() -- wraps your function so it only runs once per 2 seconds even if the event fires multiple times.
    console.log('yea')
end)

for i = 1, 5 do
    event.broadcast('myEvent', i)
    task.wait(0.5)
end
```

----

#### wait(eventName)
{static}
```lua
coroutine.wrap(function() -- So it doesn't make the other lines wait
    print("Waiting for myEvent...")
    local arg1, arg2 = event.wait("myEvent") -- Pauses the other lines, and wont run until it gets broadcasted
    print("myEvent fired with arguments:", arg1, arg2)
end)()

task.wait(3) -- Wait for 3 seconds
event.broadcast('myEvent', 'header', 'message') -- Inititate broadcast to myEvent
```

----

#### filter(eventName, fn)
{chainable}
```lua
local highScoreEvent = event.filter('scoreUpdated', function(score) -- if score is higher than 100, 
    return score >= 100
end)

event.on(highScoreEvent, function(score) -- if highScoreEvent returns true, execute function, otherwise it will ignore
    print("🎉 High score reached:", score)
end)

event.broadcast("scoreUpdated", 50)   -- Ignored
event.broadcast("scoreUpdated", 99)   -- Ignored
event.broadcast("scoreUpdated", 120)  -- Accepted and handled
```

----

#### map(eventName, fn)
{chainable}
```lua
local returnPlayerName = event.map('playerJoined', function(player) -- replaces the function with the returning argument
    return player.name -- Replaces the function argument with the name, 
end)

event.on(returnPlayerName, function(name) -- Since PlayerName Map returns 'player.name', it will give the function instead of player, the name of the player
    print("👋 Player joined:", name) -- What it should show in console: '👋 Player Joined: Zara'
end)

local fakePlayer = { name = "Zara", level = 42 }
event.broadcast("playerJoined", fakePlayer)
```

----

#### onceWithTimeout(eventName, timeoutDuration, fn)
{recommended}
```lua
event.onceWithTimeout('dataReady', 5, function(data, err) -- data is from the broadcast event, err is from the module itself.
    if err == "timeout" then
        warn("⏰ Data load timed out.")
    else
        print("✅ Data received:", data)
    end
end)

task.delay(3, function() -- Wait for 3 seconds, then initiate broadcast
    EventPlus.broadcast("dataReady", "PlayerStats") -- Since the timeout function is 5 seconds, it should
end)
```

----

#### throttle(eventName, interval, callback)
{recommended}
```lua
event.throttle('enemySpotted', 2, function(location) -- Only executes once every 2 seconds, no matter how often it is broadcasted, great so it prevents spam
    print("🔪 Enemy Spotted at:", location)
end)

event.broadcast("enemySpotted", "Bridge") -- now it makes it delay for 2 seconds, as it's the second argument in .throttle().
task.wait(0.5)
event.broadcast("enemySpotted", "Tower") -- Won't execute because the event it's calling is under cooldown as from the first one.
task.wait(2)
event.broadcast("enemySpotted", "Cave") -- Will execute as the cooldown was lifed
```

----

#### delay(eventName, delayTime, ...)
{recommended}
```lua
event.on('reminder', function(message) -- Creating a event
    print("Reminder:", message)
end)

print("Scheduling reminder in 3 seconds...")
event.delay('reminder', 3, "Time to take a break!") -- Wait's for 3 seconds, then broadcast event. Think of it as using task.wait() and .broadcast after it.
```

----

#### repeatEvent(eventName, interval, times, ...)
{recommended}
```lua
local count = 0

event.on('heartbeat', function()
    count +=1 -- Increase count each run
    print("Heartbeat Count: #", count) -- Print the amount of counts it has been ran
end)

event.repeatEvent('heartbeat', 1, 3) -- Each 1 second, send a event until it reaches 3
```

----

#### pipe(eventName, fn)
{chainable}
```lua
event.on("numberEvent", function(value) -- Create a new event listener, no longer uses the function local return
	print("Number event fired", value) -- Returns the value
end)

event.pipe("numberEvent", function(number) -- Create a new pipe system
	-- transform arguments here
	return number*5 -- Replace the argument with the returning argument
end)

event.broadcast('numberEvent', 5) -- Send a broadcast to pipe event
```

----

#### clear(eventName)
{toggleable}
```lua
local handle1 = event.on("testEvent", function() print("Listener 1") end)
local handle2 = event.on("testEvent", function() print("Listener 2") end)

event.broadcast('testEvent')
-- outputs
-- Listener 1
-- Listener 2

print('Clearing Listeners')
event.clear('testEvent')

event.broadcast('testEvent') -- No output would be found as we cleared the listener
```

----

#### getCallCount(eventName)
{read-only}
```lua
event.on('myEvent', function(num)
    if not num then num = 123 end
    print('Broadcasted', num)
end)
print('Count of calls before broadcast:', event.getCallCount('myEvent')) -- returns 0 as we didn't broadcast the event yet

event.broadcast('myEvent') -- we broadcasted it, so it becomes 1
event.broadcast('myEvent', 123) -- becomes 2

print('New Count:',event.getCallCount('myEvent'))
```

----

#### waitFor(eventName, timeout)
{static}
```lua
event.onceWithTimeout('dataReady', 5, function(data, err) -- if it doesn't receive the event within 5 seconds, timeout, otherwise return data unpacked
    if err == "timeout" then
        return { [1] = 'timeout' }
    else
        return table.unpack(data)
    end
end)

coroutine.wrap(function()
    local args = { event.waitFor("dataLoaded", 5) }
    if args[1] == "timeout" then
        print("⚠️ Data loading timed out.")
    else
        print("✅ Data loaded with args:", table.unpack(args))
    end
end)()

-- Delay broadcast by 3 seconds (simulate async event)
task.delay(3, function()
    event.broadcast("dataLoaded", "PlayerStats", 42)
end)
```

----

#### onceEvery(eventName, interval, callback)
{unstable}
```lua
-- Responds every 2 seconds prior to the interval duration
event.onceEvery('heartbeat', 2, function(pulse) 
    print('Received Heartbeat:', pulse)
end)

task.spawn(function()
    while true do
        event.broadcast('heartbeat', os.clock())
        task.wait(0.5)
    end
end)
```