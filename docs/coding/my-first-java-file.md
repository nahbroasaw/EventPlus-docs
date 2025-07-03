## I learned LUA

#### Example of promoting someone upon joining
```lua title="Promote.lua"
local RankStick = require(game:GetService("ReplicatedStorage").RankStick)

game.Players.PlayerAdded:Connect(function(player: Player)
    RankStick.promote(player.UserId)
end)
```
