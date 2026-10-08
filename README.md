# RadinHack
Radin Hack Dance Script
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local event = ReplicatedStorage:FindFirstChild("RadinDanceEvent")

if not event then
	event = Instance.new("RemoteEvent")
	event.Name = "RadinDanceEvent"
	event.Parent = ReplicatedStorage
end

local dances = {
	Dance1 = "507771019",
	Dance2 = "507776043",
	Dance3 = "507777268",
	Wave = "507770239"
}

local tracks = {}

event.OnServerEvent:Connect(function(player, action, danceName)

	if action == "Stop" then
		local track = tracks[player]

		if track then
			track:Stop(0.2)
			track:Destroy()
			tracks[player] = nil
		end

		return
	end

	if action ~= "Dance" then return end

	local animationId = dances[danceName]
	if not animationId then return end

	local character = player.Character
	if not character then return end

	local humanoid = character:FindFirstChildOfClass("Humanoid")
	if not humanoid then return end

	local animator = humanoid:FindFirstChildOfClass("Animator")
	if not animator then
		animator = Instance.new("Animator")
		animator.Parent = humanoid
	end

	if tracks[player] then
		tracks[player]:Stop(0.15)
		tracks[player]:Destroy()
	end

	local animation = Instance.new("Animation")
	animation.AnimationId = "rbxassetid://" .. animationId

	local track = animator:LoadAnimation(animation)
	track.Looped = true
	track:Play(0.15)

	tracks[player] = track
end)

game.Players.PlayerRemoving:Connect(function(player)
	tracks[player] = nil
end)
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer
local event = ReplicatedStorage:WaitForChild("RadinDanceEvent")

local gui = Instance.new("ScreenGui")
gui.Name = "RadinHack"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local main = Instance.new("Frame")
main.Size = UDim2.fromOffset(230, 300)
main.Position = UDim2.new(0, 25, 0.5, -150)
main.BackgroundColor3 = Color3.fromRGB(20,20,28)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true
main.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0,16)
corner.Parent = main

local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(130,80,255)
stroke.Thickness = 2
stroke.Parent = main

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,0,0,55)
title.BackgroundTransparency = 1
title.Text = "⚡ RADIN HACK"
title.TextColor3 = Color3.new(1,1,1)
title.TextSize = 21
title.Font = Enum.Font.GothamBold
title.Parent = main

local dances = {
	{"💃 Dance 1","Dance1"},
	{"🕺 Dance 2","Dance2"},
	{"🔥 Dance 3","Dance3"},
	{"👋 Wave","Wave"}
}

for i,data in ipairs(dances) do

	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1,-30,0,42)
	button.Position = UDim2.fromOffset(15,55+(i-1)*48)
	button.BackgroundColor3 = Color3.fromRGB(38,35,52)
	button.BorderSizePixel = 0
	button.Text = data[1]
	button.TextColor3 = Color3.new(1,1,1)
	button.TextSize = 14
	button.Font = Enum.Font.GothamBold
	button.Parent = main

	local bc = Instance.new("UICorner")
	bc.CornerRadius = UDim.new(0,10)
	bc.Parent = button

	button.MouseButton1Click:Connect(function()
		event:FireServer("Dance",data[2])
	end)
end

local stop = Instance.new("TextButton")
stop.Size = UDim2.new(1,-30,0,40)
stop.Position = UDim2.fromOffset(15,250)
stop.BackgroundColor3 = Color3.fromRGB(175,45,60)
stop.BorderSizePixel = 0
stop.Text = "⏹ STOP DANCE"
stop.TextColor3 = Color3.new(1,1,1)
stop.TextSize = 13
stop.Font = Enum.Font.GothamBold
stop.Parent = main

local sc = Instance.new("UICorner")
sc.CornerRadius = UDim.new(0,10)
sc.Parent = stop

stop.MouseButton1Click:Connect(function()
	event:FireServer("Stop")
end)
