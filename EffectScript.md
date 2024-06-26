# EffectScript

EffectScript is a [LocalScript](https://create.roblox.com/docs/reference/engine/classes/LocalScript) inside of the TRIA.os Map Making Kit, it should be used as an alternative to the LocalMapScript if you want your code to be replicated to other spectators.

In order for the EffectScript to communicate we use [RemoteEvents](https://create.roblox.com/docs/reference/engine/classes/RemoteEvent) which are fired from the MapScript to the EffectScript.

When a round starts all the old EffectScripts are deleted and if a new EffectScript is present in the map model, the script will be inserted into `game.Players.LocalPlayer.PlayerGui`, this is to prevent the EffectScript from being deleted if the player dies.

If you wish to manually edit the EffectScript the location of it is `game.Players.LocalPlayer.PlayerGui`

Keep in mind that reparenting the EffectScript to anywhere else will cause the EffectScript not to be deleted on the next round and can potentially cause memory leaks.

## Example of usage

The EffectScript uses the same methods as the MapScript to get the MapLib, being
`local MapLib = game.GetMapLib:Invoke()()`

Below is a simple example of how to use the EffectScript, in this example we will make a laser effect similar to Dystopia.

MapScript code:

```lua

local MapLib = game.GetMapLib:Invoke()()
local map = MapLib.map

task.wait(10)
map.StartLaser:FireAllClients()

task.wait(10)
map.StopLaser:FireAllClients()
```

EffectScript code:

```lua

local MapLib = game.GetMapLib:Invoke()()
local map = MapLib.map

local RunService = game:GetService("RunService")

local connection

map.StartLaser.OnClientEvent:Connect(function()
    connection = RunService.Heartbeat:Connnect(function()
        laser.CFrame *= CFrame.Angles(math.rad(1), 0, 0)
    end)
end)

map.StopLaser.OnClientEvent:Connect(function()
   if connection then
        connection:Disconnect()
     end
end)
```
