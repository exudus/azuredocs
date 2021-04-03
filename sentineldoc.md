# Unofficial Sentinel V2 Pre-release Documentation

**Important:** This documentation only depicts a **pre-release** version of Sentinel and may change at any time. Also, this documentation was written from the perspective of a scripter so some things may be inaccurate.

## Credits

* Greenman(#0001) - Most of the documentation
* Google Chrome(#6242) - Providing `setnamecallmethod`
* Stroketon(#1337) - Providing `secure_create` and `secure_load`
* bditt(#0931) - Providing `runscript` and `terminatescript`

## getsenv

`table getsenv(<LocalScript> script)`

Returns the global environment table of LocalScript `script`.

Example (execute in [Prison Life](https://www.roblox.com/games/155615604/Prison-Life-Cars-fixed)):
```lua
local env = getsenv(game.Players.LocalPlayer.Character.ClientInputHandler)
for k,v in pairs(env) do
  print(k,v)
end 
```
Keep in mind that every script's global environment contains a global called `script` which is the current script.

## getrawmetatable

`table getrawmetatable(<table, userdata> t)`

Gets the metatable of table or userdata `t` and returns it without invoking `__metatable`.

Example:
```lua
print(getmetatable(game)) -- The metatable is locked
print(getrawmetatable(game)) -- table: 0xADDR
```

## mouse1press

`void mouse1press()`

Simulates the left mouse button being held down. To stop simulating this, call `mouse1release()`.

Example:
```lua
-- Hold left mouse for 3 seconds
mouse1press()
wait(3)
mouse1release()
```

## newcclosure

`function newcclosure(<function> lclosure)`

Takes an lclosure (Lua function) and returns a new cclosure (C function). This is commonly used when setting the metamethods of a Roblox userdata such as `game`.

Example:
```lua
local oldf = hookfunction(getrawmetatable(game).__namecall,newcclosure(function(self,...)
  if getnamecallmethod() == "Kick" then
    return
  end
  return oldf(self,...)
end))
```

## request

`string request(<string> url, <string> type [,<string> data])`

Sends a `type` request to `url` with `data`. The `type` string can either be `get` or `post` and is case insensitive so you can also use `GET` and `POST`. `data` is an optional argument and it is recommended to format the data into JSON (using Roblox's HttpService). The returned string is the response of the request.

Example:
```
print(request("https://pastebin.com/raw/MVrUGHk5","get")) -- Hi
```

Keep in mind that the request will have the `User-Agent` header set to `sentinel` and the `Sentinel-Fingerprint` header because it's used internally for the HWID whitelist.

## isluau

`bool isluau()`

Returns a boolean that tells you whether a game has the new Luau VM enabled (true) or not (false). Now that all games use Luau, this function will always return `true` and just exists for legacy purposes.

Example:
```lua
print(isluau()) -- true
```

## loadstring

`function,<nil,string> loadstring(<string> source [,<string> chunkname])`

Compiles a Lua function from `string` and returns it. If any errors are produced, `nil` is returned plus the error message as a string. 

Example:
```lua
print(loadstring("print('hi')")) -- function
print(loadstring("lol")) -- nil [string "lol"]:1: `=` expected near `<eof>`
loadstring("print('hi'))")() -- hi
```

## hookfunction

Alias: `replaceclosure`

`function hookfunction(<function> target, <function> hook)`

Intercepts any calls to C closure`target` to C closure `hook`. `target` is returned and can be used within `hook` to call the original function.

Example:
```lua
square = newcclosure(function(n)
	print(n*n)
end)

square(2) -- 4

hookfunction(square,newcclosure(function(n)
	print(n*n*n)
end))

square(2) -- 8
```

Keep in mind that in order to make the `hook` argument a C closure, you should use `newcclosure(f)`.

## mouse2release

`void mouse2release()`

Simulates the right mouse button being released. This is commonly used after calling `mouse2press()`.

Example:
```lua
-- Hold right click for 3 seconds
mouse2press()
wait(3)
mouse2release()
```

## mouse1click

`void mouse1click()`

Simulates a left mouse button click.

Example:
```lua
mouse1click()
```

## getinstancecachekey

Sorry I don't know what this does.

## getnilinstances

`table getnilinstances()`

Returns a table of all instances in the game that are parented to `nil`.

Example:
```lua
local x = Instance.new("Part")
x.Name = "FINDME"
x.Parent = nil

for k,v in pairs(getnilinstances()) do
  if v.Name == "FINDME" then
    print("Found FINDME in nil!") -- this will get called
  end
end
```

Keep in mind that the returned table uses numeric indices as keys so you will have to use a conditional statement to search for certain instances.

## setclipboard

`void setclipboard(<string> data)`

Copies `data` to the clipboard.

Example:
```lua
-- copy your player's current position
local pos = game.Players.LocalPlayer.Character.HumanoidRootPart.Position
setclipboard("Vector3.new("..tostring(pos)..")") -- example: Vector3.new(0,0,0)
```

## mouse2click

`void mouse2click()`

Simulates a click of the right mouse button.

Example:
```lua
mouse2click()
```

## loadfile

`function,<nil,string> loadfile(<string> filename)`

Compiles a Lua function from the contents of `filename` and returns it. If any errors are produced, `nil` is returned plus the error message as a string. This function is restricted to reading files from the `workspace` folder.

workspace/test.txt:
```lua
print("Hello World!")
```
Example:
```lua
loadfile("test.txt")() -- Hello World!
```

## checkcaller

`bool checkcaller()`

Returns `true` if the current thread was made by Sentinel and returns `false` if the current thread was not made by Sentinel.

Example:
```lua
print(checkcaller()) -- true
```
If you were to hook a function which comes from a script inside the game and make your hook call checkcaller, you would get a false result.

## issentinelclosure

`bool issentinelclosure(<function> closure)`

Returns `true` if `closure` originates from Sentinel.

Example:
```lua
print(issentinelclosure(writefile)) -- true
print(issentinelclosure(print)) -- false
```
The second line returns `false` because `print()` originates from Lua's global environment.

## runscript

`void runscript(<LocalScript> s)`

Runs script `s` after it's been terminated. 

Example:
```
-- restart all running scripts
for k,v in pairs(getrunningscripts()) do
  terminatescript(v)
  runscript(v)
end
```

## getloadedmodules

`table getloadedmodules()`

Returns a table containing all of the loaded ModuleScripts in the game.

Example:
```lua
for k,v in pairs(getloadedmodules()) do
  print(k,v)
end
```
Keep in mind that the returned table uses numeric indices as keys so you will have to use a conditional statement to search for certain ModuleScripts.

## mouse1release

`void mouse1release()`

Simulates the left mouse button being released. Commonly used after calling `mouse1press()`.

Example:
```lua
-- Hold left click for 3 seconds
mouse1press()
wait()
mouse1release()
```

## setnamecallmethod

`void setnamecallmethod(<string> name)`

Sets the current method name to `name`.

Example:
```lua
-- Example provided by Google Chrome#6242
local mt = getrawmetatable(game)
local oldnamecall = mt.__namecall
setreadonly(mt,false)

mt.__namecall = newcclosure(function(self,...)
    if getnamecallmethod() == "FireServer" then
          setnamecallmethod("InvokeServer")
           print("A RemoteEvent was called with the name "..self.Name)
    end
    return oldnamecall(self,...)
end)
```

In the example above, any time `:FireServer` is called, `:InvokeServer` is called instead.

## replaceclosure

This is just an alias for `hookfunction`. Click [here](#hookfunction) to jump to hookfunction.

## getgenv

`table getgenv()`

Returns Sentinel's global environment table. This is **not** the same table as `_G`.

Example:
```lua
getgenv().square = function(n)
  print(n*n)
end

-- separate tab/script
square(2) -- 4
```

## getcallingscript

`userdata getcallingscript()`

Returns the script that is calling the current function. Returns `nil` when called within Sentinel.

Example:
```lua
print(getcallingscript()) -- nil
```
In order to use this properly, you must hook a function from a script and call this function within your hook.

## getnamecallmethod

`string getnamecallmethod()`

Returns the name of the method being called on an instance as a string. This only works when used within a `__namecall` metamethod hook.

Example:
```lua
local mt = getrawmetatable(game)
local oldnamecall = mt.__namecall
setreadonly(mt,false)

mt.__namecall = newcclosure(function(self,...)
	if getnamecallmethod() == "FireServer" then
     	print("A RemoteEvent was called with the name "..self.Name)
	end
	return oldnamecall(self,...)
end)
```

If you still don't get it, here's a breakdown:

Let's assume you have a hook on `mt.__namecall` and you print `getnamecallmethod()` every time anything is called with a `:`.
|  Method Call | `getnamecallmethod()`'s value |
|:---:|:---:|
|  game.Players.LocalPlayer:Kick() | Kick  |
|  game.Workspace:FindFirstChild("Part") | FindFirstChild  |
|                   game.ReplicatedStorage.GiveMoney:FireServer(100)                     |          FireServer       |


## secure_load

`void secure_load(<string> script)`

Takes in a script obfuscated by `secure_create` and runs it.

```
-- Provided by Stroketon#1337
secure_load("Sy0TjpZ3KylrexYHHldftzp3oZPSCJxdRprPPPpK5H+k3GCs1ONXUyAuiti636nFXkXkgfzKlV/JUDdMoBfYA32xSlC7HQBet9mcFz2N0q9uJokrl9UyONzL2vh0KTtJWnrITMaBJQcleutkEB6SYXyrbapdCj60AtdIypqSK4/M22JRodwdX2EbpTHaf714/QkPftcMn95/nMjJbBZ//meQROk/edNjOFUIQw0g1AYZYCU5v6VPTqAdpsG11I6ECJnmudrCcebnYuEyaKmTRZxwFJWWQv+dvnoJJWfZGCU7xOdeLyV+vBXdyskODWy1AqsrS6yl0z8rMSpO+CbY6+awXW0nAAuzigUuHy1wbJ+Ib2c5nuOq2S6iq69hRBn/J163EB3/j+SBBkFliUr3m86kZcWzyU4myd7at6st0S0wfZON7GtYgwO9ihbVCz94xw3nOfFErabrrOYYsjbRMEOS4ANon/nuY2pCJNGnnraOTJ4U9YXC5yW69cqSPKZ9KNrbk1GhQAzUKFs/VhkbYQYbaeiECF0a19BvcA5a+tmPftdImHn7peTaxQu/RIcFNlcIx5XyNoBwqyaAwFTQT0/Kjbd8oFP9VYQZ/4s8UKPepbAttumDTpHsuyj/ZdpstesbkFz3Tm25o3Oi2nvXAFiFam4NDybn6uTVOye5cmilLOYSJDA5XjA5Ib3q9aqPT4gFRgn4ByRoj7npu9n4yw79/Azjy+g/BH77dO8QGD4oNBQODuGyyEoBHxu2Bxy5Q+vIO/D8c+qZyvm/rUwNbl6HLpTYn9efbBCcxAMQ6q0UfTyN64oI1ocSCSEbyYxh7Tg4SUGQ1cZrhnpfF1fEz9bUB7pjIZpH1IZtCdK8IGWAEPcFffOrfiV9MB/rRdNnuYK9l6dqNwRdC2xnMdYnJ0MnngeYwHfvbBJYj1ItJteM/9XIOexSjOSqbBY1wDuLDnX8pEiBHvjetj+o2nAtecNqq/Lwf39ZLLJ5GG/SwZFXD2/qU/nzoNlWVqhtI91DqJMJafxFVa2PNk/2XdtEPKkW0vMAe/NDUfHKc34YOLzdhXro4W87DSSsE4IrnXpiD0kZG3oJZ384dBb6Zl/LbX7IUphKtUWWbYcW7xL3MaK3ZVy+H/dRXx8mBNn5hhbDETM6jYPzs/tlQ/kUteO4XA==") -- Hello World
```

## require

`any require(<ModuleScript> module)`

Runs the ModuleScript `module` and returns any values returned by the module.

Example:

game.Workspace.Module
```lua
local m = {
  health = 100,
  ammo = 200,
  stamina = 50
}

return m
```
Script:
```lua
local module = require(game.Workspace.Module)
print(module.health) -- 100
module.stamina = 100
print(module.stamina) -- 100
```

## mousemoverel

`void mousemoverel(<number> x, <number> y)`

Moves the mouse by `x` and `y` relative to the mouse's current position. Keep in mind that a positive y value moves the mouse down.

Example:
```lua
mousemoverel(100,100)
```
In the example above, we move the mouse 100 pixels right and 100 pixels down from the mouse's current position.

## writefile

`void writefile(<string> filename, <string> data)`

Writes `data` to a file called `filename` in the workspace folder. If the file already exists, the file is overwritten. This function is restricted to the workspace folder.

Example:
```lua
writefile("test.txt","print('Hi')")
loadfile("test.txt")() -- Hi
```

## getthreadcontext

`number getthreadcontext()`

Returns the current thread's security context level. 

Example:
```lua
print(getthreadcontext()) -- 6
```

## secure_create

Used to create obfuscated scripts which can be ran with `secure_load`. Only certain people have authorization to use this function and anyone else who tries to use it will get a `Not authorized!` error message.

**IMPORTANT: DO NOT ASK AUTHORIZED PEOPLE TO OBFUSCATE SCRIPTS FOR YOU**

## readfile

`string readfile(<string> filename)`

Returns the contents of `filename` from the workspace folder as a string.

Example:

test.txt
```
Hi
```
Script
```lua
print(readfile("test.txt"))
```

## islclosure

`bool islclosure(<function> closure)`

Returns a boolean that tells you if `closure` is a Lua closure (true) or a C closure (false).

Example:
```lua
function square(n)
  return n*n
end

print(islclosure(square)) -- true
print(islclosure(loadstring)) -- false
```

## setthreadcontext

`void setthreadcontext(<number> level)`

Sets the current thread's context level to `level`.

Example:
```lua
setthreadcontext(4)
print(getthreadcontext()) -- 4
```

## onteleport

## appendfile

`void appendfile(<string> filename, <string> data)`

Writes `data` to a file called `filename` in the workspace folder. If the file already exists, the data is written starting at the end of the file. This function is restricted to the workspace folder.

Example:
```lua
appendfile("test.txt","Hi")
appendfile("test.txt","Hello")
print(readfile("test.txt")) -- HiHello
```

## setreadonly

`void setreadonly(<table> t, <bool> b)`

Sets `t`'s readonly value to `b`.

Example:
```lua
local mt = getrawmetatable(game)
setreadonly(mt,game) -- allows us to modify game's metatable
```

## getgc

`table getgc()`

Returns a table containing Lua's garbage collector.

Example:
```lua
for k,v in pairs(getgc()) do
  print(k,v)
end
```

## iscclosure

`bool iscclosure(<function> closure)`

Returns a boolean that tells you if `closure` is a C closure (true) or a Lua closure (false).

Example:
```lua
function square(n)
  return n*n
end

print(iscclosure(square)) -- false
print(iscclosure(loadstring)) -- true
```

## getrenv

`table getrenv()`

Returns Roblox's global environment table.

Example:
```lua
for k,v in pairs(getrenv()) do
  print(k,v)
end
```

## mouse2press

`void mouse2press()`

Simulates the right mouse button being held down. To stop simulating this, call `mouse2release()`.

Example:
```lua
-- Hold right click for 3 seconds
mouse2press()
wait(3)
mouse2release()
```

## DebuggerManager

Please read [this](https://developer.roblox.com/en-us/api-reference/class/DebuggerManager).

## getreg

Alias: `debug.getregistry()`

`table getreg()`

Returns the Lua registry table.

Example:
```lua
for k,v in pairs(getreg()) do
  print(k,v)
end
```

## getrunningscripts

`table getrunningscripts()`

Returns a table contaning all of the scripts that are currently running.

Example:
```lua
for k,v in pairs(getrunningscripts()) do
  print(k,v)
end
```

## terminatescript

`void terminatescript(<LocalScript> s)`

Terminates script `s` if it's running.

Example:
```
-- terminate all running scripts
for k,v in pairs(getrunningscripts()) do
  terminatescript(v)
end
```


## fireclickdetector

`void fireclickdetector(<ClickDetector> d, <number> distance)`

Clicks click detector `d` at a distance of `distance`. In order for the click detector to accept the click, the `distance` must be less than the `MaxActivationDistance` property of the click detector.

Example:
```lua
-- this is just example code and doesn't apply to every situation
fireclickdetector(game.Workspace.Button.ClickDetector,10)
```

## Coming Soon

* debug.* functions
* Drawing library
* bit.* functions
