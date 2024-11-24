print("start")
print("next")
print("new13")

local Fluent = loadstring(game:HttpGet("https://raw.githubusercontent.com/Lucas-Lua/ui/main/m"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/barlossxi/barlossxi/main/ZAZA.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/barlossxi/barlossxi/main/InterfaceManager.lua.txt"))()

local ScreenGui1 = Instance.new("ScreenGui")
local ImageButton1 = Instance.new("ImageButton")
local UICorner = Instance.new("UICorner")

ScreenGui1.Name = "ImageButton"
ScreenGui1.Parent = game.CoreGui
ScreenGui1.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

ImageButton1.Parent = ScreenGui1
ImageButton1.BackgroundTransparency = 1
ImageButton1.BorderSizePixel = 0
ImageButton1.Position = UDim2.new(0.120833337, 0, 0.0952890813, 0)
ImageButton1.Size = UDim2.new(0, 50, 0, 50)
ImageButton1.Draggable = true
ImageButton1.Image = "rbxassetid://10723396107"
ImageButton1.MouseButton1Down:Connect(function()
	game:GetService("VirtualInputManager"):SendKeyEvent(true,305,false,game)
	game:GetService("VirtualInputManager"):SendKeyEvent(false,305,false,game)
end)
UICorner.Parent = ImageButton1

local Window = Fluent:CreateWindow({
	Title = "Rock Fruit",
	SubTitle = "Savage Hub",
	TabWidth = 130,
	Size = UDim2.fromOffset(480, 400),
	Acrylic = false, 
	Theme = "Dark",
	MinimizeKey = Enum.KeyCode.RightControl
})

local Tabs = {
	Main = Window:AddTab({ Title = "Main", Icon = "gamepad-2" }),
	Event = Window:AddTab({ Title = "Event/Boss", Icon = "ghost" }),
	Secretxd = Window:AddTab({ Title = "Secret", Icon = "file-box" }),
	Skill = Window:AddTab({ Title = "Skill", Icon = "sword" }),
	Misc = Window:AddTab({ Title = "Misc", Icon = "align-left" }),
	Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

Tabs.Main:AddDropdown("Select Method", {
	Title = "Select Method",
	Values = {"Upper","Behind","Below"},
	Multi = false,
	Default = 1,
	Callback = function(v)
		_G.Method = v
	end
})



spawn(function()
	while wait() do 
		pcall(function()
			if _G.Method == "Behind" then
				MethodFarm = CFrame.new(0,0,_G.DistanceMob)
			elseif _G.Method == "Below" then
				MethodFarm = CFrame.new(0,-_G.DistanceMob,0) * CFrame.Angles(math.rad(90),0,0)
			elseif _G.Method == "Upper" then
				MethodFarm = CFrame.new(0,_G.DistanceMob,0)  * CFrame.Angles(math.rad(-90),0,0)
			else
				MethodFarm = CFrame.new(0,_G.DistanceMob,0)  * CFrame.Angles(math.rad(-90),0,0)
			end
		end)
	end
end)


Tabs.Main:AddInput("Input", {
	Title = "Distance Mob",
	Default = 0,
	Placeholder = " ",
	Numeric = true,
	Finished = false,
	Callback = function(gay)
		_G.DistanceMob = gay
	end
})

function Attack()
	local VirtualUser = game:GetService('VirtualUser')
	VirtualUser:CaptureController()
	VirtualUser:ClickButton1(Vector2.new(851, 158), game:GetService("Workspace").Camera.CFrame)
end
local Wea = Tabs.Main:AddSection("| Weapon") 

local Weaponlist = {}

local SelectWeapon = Wea:AddDropdown("SelectWeapon", {
	Title = "Select Weapon",
	Values = Weaponlist,
	Multi = false,
	Default = false,
	Callback = function(selectedWeapon)
		_G.Weaponnn = selectedWeapon
		print(_G.Weaponnn)
	end
})

local function RefreshWeaponList()
	Weaponlist = {}
	for i,v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do  
		if v:IsA("Tool") then
			table.insert(Weaponlist ,v.Name)
		end
	end
	for i,v in pairs(game.Players.LocalPlayer.Character:GetChildren()) do  
		if v:IsA("Tool") then
			table.insert(Weaponlist, v.Name)
		end
	end
	SelectWeapon:SetValues(Weaponlist)
end

Wea:AddButton({
	Title = "Refresh Weapon",
	Description = "",
	Callback = RefreshWeaponList
})

RefreshWeaponList()


local Tool = nil

function EquipWeapon(ToolSe)
	if game.Players.LocalPlayer.Backpack:FindFirstChild(ToolSe) then
		if Tool == game.Players.LocalPlayer.Backpack:WaitForChild(ToolSe) then
			game.Players.LocalPlayer.Character.Humanoid:EquipTool(Tool)
			wait(0.10)
		else
			Tool = game.Players.LocalPlayer.Backpack:WaitForChild(ToolSe)
		end
	end
end

spawn(function()
	while task.wait() do
		xpcall(function()
			if _G.AutoStoreFruit or _G.AutoEvent or _G.AutoBoss or _G.AutoFarm1 or _G.Raid then
					repeat
						task.wait(1)
						EquipWeapon(_G.Weaponnn)
						wait(1)
				    until not _G.AutoStoreFruit or not _G.AutoEvent or not _G.AutoBoss or not _G.AutoFarm1 or not _G.Raid
				end
			end, function(err)
		end)
	end
end)


local Farm = Tabs.Main:AddSection("| Auto Farm")  
local Mon = {}
local MonSet = {}
for i, v in pairs(game:GetService("Workspace").Mob:GetChildren()) do
	if not MonSet[v.Name] then
		table.insert(Mon, v.Name)
		MonSet[v.Name] = true
	end
end
_G.SelectedStands = {}

local MultiDropdown = Farm:AddDropdown("Select Mon", {
	Title = "Select Mon",
	Description = "",
	Values = Mon,
	Multi = true,
	Default = {},
	Callback = function(selected)
		_G.SelectedStands = selected
	end
})

MultiDropdown:OnChanged(function(selected)
	_G.SelectedStands = {}
	for stand, isSelected in pairs(selected) do
		if isSelected then
			table.insert(_G.SelectedStands, stand)
		end
	end
end)

Farm:AddToggle("Auto Farm Mon", {
	Title = "Auto Farm Mon",
	Default = false,
	Callback = function(AutoFarm1)
		_G.AutoFarm1 = AutoFarm1
	end
})

spawn(function()
	while task.wait() do
		if _G.AutoFarm1 then
			pcall(function()
				for _, v in pairs(game:GetService("Workspace").Mob:GetChildren()) do
					if table.find(_G.SelectedStands, v.Name) and v:FindFirstChild("Humanoid") and v:FindFirstChild("HumanoidRootPart") and v.Humanoid.Health > 1 then
						v.Humanoid.WalkSpeed = 0
						v.Humanoid.JumpPower = 0
						repeat
							task.wait()
							Attack()
							game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = v.HumanoidRootPart.CFrame*MethodFarm
						until not _G.AutoFarm1 or v.Humanoid.Health <= 0
					end
				end
			end)
		end
	end
end)

--event/boss

local even = Tabs.Event:AddSection("| Event")  

even:AddToggle("Auto Farm Event", {
	Title = "Auto Farm Event",
	Default = false,
	Callback = function(AutoEvent)
		_G.AutoEvent = AutoEvent
	end
})

spawn(function()
	while task.wait() do
		if _G.AutoEvent then
			pcall(function()
				for _, v in pairs(game:GetService("Workspace").Mob:GetChildren()) do
					if v.Name == "Moon Demon" then
						v.Humanoid.WalkSpeed = 0
						v.Humanoid.JumpPower = 0
						repeat
							task.wait()
							Attack()
							game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = v.HumanoidRootPart.CFrame*MethodFarm
						until not _G.AutoEvent or v.Humanoid.Health <= 0
					end
				end
			end)
		end
	end
end)

local Boss = Tabs.Event:AddSection("| Boss") 

Boss:AddToggle("Auto Farm Boss [Fully]", {
	Title = "Auto Farm Boss [Fully]",
	Default = false,
	Callback = function(AutoBoss)
		_G.AutoBoss = AutoBoss
	end
})


spawn(function()
	while task.wait(0.3) do
		if _G.AutoBoss then
			xpcall(function()
				if workspace.Mob:FindFirstChild("Laios") then 
					local target = workspace.Mob:FindFirstChild("Laios")
					if target and target:FindFirstChild("HumanoidRootPart") and target:FindFirstChild("Humanoid") and target:FindFirstChild("Humanoid").Health > 0 then 
						target.Humanoid.WalkSpeed = 0
						target.Humanoid.JumpPower = 0
						repeat task.wait()
							game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = target:FindFirstChild("HumanoidRootPart").CFrame*MethodFarm
							Attack()
						until not target.Parent or not target:FindFirstChild("HumanoidRootPart") or not target:FindFirstChild("Humanoid") or target:FindFirstChild("Humanoid").Health <= 0 or not _G.AutoBoss
					else
						game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-1949.558349609375, 15.95364761352539, -423.34527587890625)
					end
				elseif workspace.NpcSpawnBoss:FindFirstChild("Laios") and workspace.NpcSpawnBoss:FindFirstChild("Laios"):FindFirstChild("Torso") and workspace.NpcSpawnBoss:FindFirstChild("Laios"):FindFirstChild("Torso"):FindFirstChild("ProximityPrompt") then 
					if game:GetService("Players").LocalPlayer.Character:FindFirstChild("Shiny Banana") or game:GetService("Players").LocalPlayer.Backpack:FindFirstChild("Shiny Banana") then
						game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-1949.558349609375, 15.95364761352539, -423.34527587890625)
						task.wait(0.3)
						local prompt = workspace.NpcSpawnBoss:FindFirstChild("Laios").Torso
						fireproximityprompt(prompt.ProximityPrompt)
						task.wait(0.3)     
					elseif game:GetService("Players").LocalPlayer.PlayerGui.HUD.Inventory.ScrollingFrameList:FindFirstChild("Shiny Banana") and not (game:GetService("Players").LocalPlayer.Character:FindFirstChild("Shiny Banana") or game:GetService("Players").LocalPlayer.Backpack:FindFirstChild("Shiny Banana")) then
						game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("Inventory"):FireServer("Shiny Banana")         
						task.wait(1)
					else
						local target = workspace.Mob:FindFirstChild("Gorilla Power")
						if target and target:FindFirstChild("HumanoidRootPart") and target:FindFirstChild("Humanoid") and target:FindFirstChild("Humanoid").Health > 0 then 
							target.Humanoid.WalkSpeed = 0
							target.Humanoid.JumpPower = 0
							repeat task.wait()
								game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = target:FindFirstChild("HumanoidRootPart").CFrame*MethodFarm
								Attack()
							until not target.Parent or not target:FindFirstChild("HumanoidRootPart") or not target:FindFirstChild("Humanoid") or target:FindFirstChild("Humanoid").Health <= 0 or not _G.AutoBoss
						else
							game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-2, 20, 2322)
						end
					end

				else
					game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-1949.558349609375, 15.95364761352539, -423.34527587890625)
					task.wait(0.1)     
				end
			end,print)
		end
	end
end)


local attack = function(args)
	if not game:GetService("Players").LocalPlayer.PlayerGui.HUD:FindFirstChild("attack") then
		local attack_ui = Instance.new("Frame")

		attack_ui.Name = "attack"
		attack_ui.Parent = game:GetService("Players").LocalPlayer.PlayerGui.HUD
		attack_ui.Active = true
		attack_ui.AnchorPoint = Vector2.new(0.5, 0.5)
		attack_ui.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
		attack_ui.BackgroundTransparency = 1.000
		attack_ui.BorderColor3 = Color3.fromRGB(0, 0, 0)
		attack_ui.BorderSizePixel = 0
		attack_ui.Position = UDim2.new(0.989629149, 0, 0.2359629149, 0)
		attack_ui.Rotation = 1.000
		attack_ui.Size = UDim2.new(0, 19, 0, 19)
	else
		local attacker = game:GetService("Players").LocalPlayer.PlayerGui.HUD:FindFirstChild("attack")
		local VirtualInputManager = game:GetService("VirtualInputManager")
		VirtualInputManager:SendMouseButtonEvent(attacker.AbsolutePosition.X, attacker.AbsolutePosition.Y, 0, true, game, 0)
		VirtualInputManager:SendMouseButtonEvent(attacker.AbsolutePosition.X, attacker.AbsolutePosition.Y, 0, false, game, 0)
	end
end



_G.autost = {}
for _, v in pairs(game.ReplicatedStorage.Tools:GetDescendants()) do
	if v:IsA("Tool") and not v:IsDescendantOf(game.ReplicatedStorage.Tools.Weapon) then
		table.insert(_G.autost, v.Name)
	end
end

local Store = Tabs.Secretxd:AddSection("| Auto Store All")  

Store:AddToggle("Auto Store All", {
	Title = "Auto Store All", 
	Default = false, 
	Callback = function(AutoStoreFruit) 
		_G.AutoStoreFruit = AutoStoreFruit
	end})


local lastEquippedTool = nil
spawn(function()
	while true do
		wait(3)
		if _G.AutoStoreFruit or _G.Raid then
			pcall(function()
				local player = game.Players.LocalPlayer
				local backpack = player.Backpack
				local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
				local dialogueGui = game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Dialogue")
				local isVisible = dialogueGui and dialogueGui:FindFirstChild("Frame")
				if isVisible then return end
				if not humanoid then return end
				local currentTool = humanoid:FindFirstChildOfClass("Tool")
				if currentTool and table.find(_G.autost, currentTool.Name) then
					return
				end
				if currentTool and not table.find(_G.autost, currentTool.Name) then
					currentTool:Destroy()
				end
				for i, v in pairs(backpack:GetChildren()) do
					if table.find(_G.autost, v.Name) then
						humanoid:EquipTool(v)
						lastEquippedTool = v
						break
					end
				end
			end)
		end
	end
end)


spawn(function()
	while wait() do
		pcall(function()
			if _G.AutoStoreFruit or _G.Raid or _G.AutoStoreFruitone then
				for i2, v in pairs(game.Players.LocalPlayer.PlayerGui:GetChildren()) do
					if string.find(v.Name, "Dialogue") then
						v.Frame["3"].Position = UDim2.new(0, -800 ,0, -700)
						v.Frame["3"].Size = UDim2.new(10000, 10000, 10000, 10000)
						v.Frame["3"].BackgroundTransparency = 1
						v.Frame["3"].ImageTransparency = 1
					end
				end
			end
		end)
	end
end)

spawn(function()
	while task.wait() do
		pcall(function()
			if _G.AutoStoreFruit or _G.Raid or _G.AutoStoreFruitone then
				attack()
			end
		end)
	end
end)


local Storeone = Tabs.Secretxd:AddSection("| Auto Store Select")


local ez = Storeone:AddDropdown("Select Store", {
	Title = "Select Store",
	Description = "",
	Values = {"Orb Broken", "Finger Sukuna", "Orb Atomic", "Dragon Blade", "Cyber Militech",
		"Chair", "Shark", "Hat Megumin", "Straw Hat", "Smith mask", "Shadow Diary", "Shadow Manga", "Cloak Shadow",
		"Orb Shadow", "Shiny String", "Stick", "Orb Curse", "Sheath", "Rubber Band", "Cyber Chip", "Antler", "Black Glasses",
		"Orb Flame", "Scarf", "Book Heaven", "Rock", "Scrap Iron", "Crowbar", "Banana Stem", "Orb Dark", "Goku Manga",
		"Orb Dragon", "Cursed Womb", "Orb Demon", "Cat", "Train ticket", "Mask Robber", "Orb Water", "Naruto Manga",
	"Toilet", "Devil's Arm", "Tires", "Banana", "Crab", "Shiny Banana", "Orb Dungeon"},
	Multi = false,
	Default = _G.SelectedStore,
	Callback = function(ezs)
		_G.SelectedStore = ezs
	end
})




Storeone:AddToggle("Auto Store One", {
	Title = "Auto Store One", 
	Default = false, 
	Callback = function(AutoStoreFruit) 
		_G.AutoStoreFruitone = AutoStoreFruit
	end})


spawn(function()
	while task.wait() do 
		if _G.AutoStoreFruitone then
			pcall(function()
				local Continuebro = false
				for i,v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do 
					if v:IsA("Tool") then
						if v.Name:lower():find(_G.SelectedStore) then 
							game.Players.LocalPlayer.Character.Humanoid:EquipTool(v)
							Continuebro = true
							break
						end
					end
				end
				task.wait(0.01)
				for i,v in pairs(game.Players.LocalPlayer.Character:GetChildren()) do 
					if v:IsA("Tool") then 
						if v.Name:lower():find(_G.SelectedStore) then 
							repeat task.wait()
								game:GetService("VirtualUser"):CaptureController()
								game:GetService("VirtualUser"):ClickButton1(Vector2.new(0,0),workspace.CurrentCamera.CFrame)

							until game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Dialogue") and game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Dialogue"):FindFirstChild("Frame") and game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("Dialogue"):FindFirstChild("Frame"):FindFirstChild("3")
						end
					end
				end
			end)
		end
	end
end)







local Grab = Tabs.Secretxd:AddSection("| Auto Grab ")  

Grab:AddToggle("Grab Fruit", {
	Title = "Grab Item", 
	Default = false, 
	Callback = function(Fruit) 
		_G.Grab = Fruit
	end})


spawn(function()
	while wait() do
		if _G.Grab then
			pcall(function()
				for i,v in pairs(game.Workspace.Fruits:GetChildren()) do
					if v:IsA("Tool") then
						v.Handle.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
					end
				end    
			end)
		end
	end
end)


local interact = function(v2)
	local events = {"Activated","MouseButton1Down","MouseButton1Click","MouseButton1Up","MouseButton1Down"}
	for i,v in pairs(events) do
		for i,v in pairs(getconnections(v2[v])) do
			v.Function()
		end
	end
end


local Raid = Tabs.Secretxd:AddSection("| Auto Raid ")  

Raid:AddToggle("Auto Raid", {
	Title = "Auto Raid [one click] ", 
	Default = false, 
	Callback = function(raid) 
		_G.Raid = raid
	end})


spawn(function()
	while task.wait() do
		if _G.Raid then
			xpcall(function()
				if workspace.Portal.Mid:GetChildren()[6].Enabled == false then
					if game:GetService("Players").LocalPlayer.PlayerGui.HUD.DungeonReward.Visible == false then
						if game:GetService("Players").LocalPlayer.Character:FindFirstChild("Orb Dungeon") or game:GetService("Players").LocalPlayer.Backpack:FindFirstChild("Orb Dungeon") then
							game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = workspace.Portal.CFrame
							wait(1)
							game:GetService("VirtualInputManager"):SendKeyEvent(true, Enum.KeyCode.E, false, game)
						else
							wait(1)
							game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("Inventory"):FireServer("Orb Dungeon")
							wait(3.5)
							print("out orb")
						end
					else
						wait(10)
						interact(game:GetService("Players").LocalPlayer.PlayerGui.HUD.DungeonReward.CloseFrame.CloseButton)
						wait(10)
						game:GetService("Players").LocalPlayer.PlayerGui.HUD.DungeonReward.Visible = false
						game:GetService("Players").LocalPlayer.PlayerGui.HUD.DungeonReward.Visible = false
					end
				else
					if not game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("WaveUI") then
						game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-125.230751, 34.3508415, -1133.29834, -0.796580017, -9.39225586e-10, 0.604533076, -1.5428554e-09, 1, -4.79348838e-10, -0.604533076, -1.3145468e-09, -0.796580017)
					else
						for i, v in pairs(workspace.DunMob:GetChildren()) do
							if #v:GetChildren() <= 0 then
								game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(18704.322265625, 98.0782470703125, -873.9330444335938)
							else
								if v.Humanoid.Health <= 0 then
									wait(1.5)
									repeat task.wait()
									game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(18704.322265625, 98.0782470703125, -873.9330444335938)
									until v and v:IsA("Model") and not v:FindFirstChild("HumanoidRootPart") and not v:FindFirstChild("Humanoid")
								else
									v.Humanoid.WalkSpeed = 0
									v.Humanoid.JumpPower = 0
									repeat
										task.wait()
										Attack()
										game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = v.HumanoidRootPart.CFrame * MethodFarm
									until not v or v.Humanoid.Health <= 0 or #v:GetChildren() <= 0
								end
							end
						end
					end
				end
			end, print)
		end
	end
end)




Tabs.Skill:AddToggle("Z", {
	Title = "Z", 
	Default = false, 
	Callback = function(z) 
		_G.Z = z
	end})

spawn(function()
	pcall(function()
		while wait() do
			if _G.Z then
				if _G.Raid then
					for i, v in pairs(workspace.DunMob:GetChildren()) do
						if v and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "z"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				elseif _G.AutoBoss then
					local target = workspace.Mob:FindFirstChild("Laios")
					if target and target:FindFirstChild("HumanoidRootPart") and target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
						local args = {
							[1] = _G.Weaponnn,
							[2] = "z"
						}
						game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
					end
				elseif _G.AutoEvent then
					for _, v in pairs(workspace.Mob:GetChildren()) do
						if v and v.Name == "Moon Demon" and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "z"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				elseif _G.AutoFarm1 then
					for _, v in pairs(workspace.Mob:GetChildren()) do
						if v and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "z"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				end
			end
		end
	end)
end)





Tabs.Skill:AddToggle("X", {
	Title = "X", 
	Default = false, 
	Callback = function(X) 
		_G.X = X
	end})


spawn(function()
	pcall(function()
		while wait() do
			if _G.X then
				if _G.Raid then
					for i, v in pairs(workspace.DunMob:GetChildren()) do
						if v and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "x"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				elseif _G.AutoBoss then
					local target = workspace.Mob:FindFirstChild("Laios")
					if target and target:FindFirstChild("HumanoidRootPart") and target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
						local args = {
							[1] = _G.Weaponnn,
							[2] = "x"
						}
						game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
					end
				elseif _G.AutoEvent then
					for _, v in pairs(workspace.Mob:GetChildren()) do
						if v and v.Name == "Moon Demon" and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "x"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				elseif _G.AutoFarm1 then
					for _, v in pairs(workspace.Mob:GetChildren()) do
						if v and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "x"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				end
			end
		end
	end)
end)



Tabs.Skill:AddToggle("C", {
	Title = "C", 
	Default = false, 
	Callback = function(C) 
		_G.C = C
	end})



spawn(function()
	pcall(function()
		while wait() do
			if _G.C then
				if _G.Raid then
					for i, v in pairs(workspace.DunMob:GetChildren()) do
						if v and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "c"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				elseif _G.AutoBoss then
					local target = workspace.Mob:FindFirstChild("Laios")
					if target and target:FindFirstChild("HumanoidRootPart") and target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
						local args = {
							[1] = _G.Weaponnn,
							[2] = "c"
						}
						game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
					end
				elseif _G.AutoEvent then
					for _, v in pairs(workspace.Mob:GetChildren()) do
						if v and v.Name == "Moon Demon" and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "c"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				elseif _G.AutoFarm1 then
					for _, v in pairs(workspace.Mob:GetChildren()) do
						if v and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "c"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				end
			end
		end
	end)
end)



Tabs.Skill:AddToggle("V", {
	Title = "V", 
	Default = false, 
	Callback = function(V) 
		_G.V = V
	end})

spawn(function()
	pcall(function()
		while wait() do
			if _G.V then
				if _G.Raid then
					for i, v in pairs(workspace.DunMob:GetChildren()) do
						if v and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "v"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				elseif _G.AutoBoss then
					local target = workspace.Mob:FindFirstChild("Laios")
					if target and target:FindFirstChild("HumanoidRootPart") and target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 then
						local args = {
							[1] = _G.Weaponnn,
							[2] = "v"
						}
						game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
					end
				elseif _G.AutoEvent then
					for _, v in pairs(workspace.Mob:GetChildren()) do
						if v and v.Name == "Moon Demon" and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "v"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				elseif _G.AutoFarm1 then
					for _, v in pairs(workspace.Mob:GetChildren()) do
						if v and v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
							local args = {
								[1] = _G.Weaponnn,
								[2] = "v"
							}
							game:GetService("ReplicatedStorage"):WaitForChild("Remote"):WaitForChild("Action"):FireServer(unpack(args))
						end
					end
				end
			end
		end
	end)
end)


nocooldown = hookfunction(wait, function(seconds)
	return nocooldown(0.1)
end)





Tabs.Misc:AddToggle("Anti AFK", {
	Title = "Anti AFK", 
	Default = true, 
	Callback = function(cbx) 
		_G.AntiAFKEnabled = cbx
		local vu = game:GetService("VirtualUser")
		game.Players.LocalPlayer.Idled:connect(function()
			if _G.AntiAFKEnabled then
				vu:Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
				wait(1)
				vu:Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
			end
		end)
	end})


Tabs.Misc:AddToggle("White Screen", {
	Title = "White Screen", 
	Default = false, 
	Callback = function(cb) 
		_G.White_Screen = cb
	end})


spawn(function()
	while true do
		wait()

		if _G.White_Screen then
			game:GetService("RunService"):Set3dRenderingEnabled(false)
		else
			game:GetService("RunService"):Set3dRenderingEnabled(true)
		end
	end
end)

Tabs.Misc:AddButton({
	Title = "Boost FPS",
	Description = "",
	Callback = function()
		local decalsyeeted = true
		local g, w, l = game, game.Workspace, game.Lighting
		local t = w.Terrain
		sethiddenproperty(l, "Technology", 2)
		sethiddenproperty(t, "Decoration", false)
		t.WaterWaveSize, t.WaterWaveSpeed, t.WaterReflectance, t.WaterTransparency = 0, 0, 0, 0
		l.GlobalShadows, l.FogEnd, l.Brightness = false, 9e9, 0
		Misc().Rendering.QualityLevel = "Level01"

		local function handleDescendant(v)
			if v:IsA("Accessory") or v:IsA("Shirt") or v:IsA("Pants") or v:IsA("ShirtGraphic") then
				v:Remove()
			elseif v:IsA("BasePart") and not v:IsA("MeshPart") then
				v.Material, v.Reflectance = "Plastic", 0
			elseif (v:IsA("Decal") or v:IsA("Texture")) and decalsyeeted then
				v.Transparency = 1
			elseif v:IsA("ParticleEmitter") or v:IsA("Trail") then
				v.Lifetime = NumberRange.new(0)
			elseif v:IsA("Explosion") then
				v.BlastPressure, v.BlastRadius = 1, 1
			elseif (v:IsA("Fire") or v:IsA("SpotLight") or v:IsA("Smoke") or v:IsA("Sparkles")) then
				v.Enabled = false
			elseif v:IsA("MeshPart") and decalsyeeted then
				v.Material, v.Reflectance, v.TextureID = "Plastic", 0, 10385902758728957
			elseif v:IsA("SpecialMesh") and decalsyeeted then
				v.TextureId = 0
			end
		end

		for _, v in pairs(w:GetDescendants()) do
			handleDescendant(v)
		end

		w.DescendantAdded:Connect(function(v)
			wait()
			handleDescendant(v)
		end)

		for _, e in ipairs(l:GetChildren()) do
			if e:IsA("BlurEffect") or e:IsA("SunRaysEffect") or e:IsA("ColorCorrectionEffect") or e:IsA("BloomEffect") or e:IsA("DepthOfFieldEffect") then
				e.Enabled = false
			end
		end
	end
})


InterfaceManager:SetLibrary(Fluent)
InterfaceManager:SetFolder("Savage_hub")
InterfaceManager:BuildInterfaceSection(Tabs.Settings)

spawn(function()
	pcall(function()
		game:GetService("RunService").Stepped:Connect(function()
			if _G.AutoFarmOrb or _G.Aizen or _G.AutoFarm1 or _G.AutoStoreOrbgg or _G.ShadowM or _G.AutoBossspawnCid or _G.AutoBossspawnAizen then
				if not game.Players.LocalPlayer.Character.HumanoidRootPart:FindFirstChild("BodyClip") then
					local Noclip = Instance.new("BodyVelocity")
					Noclip.Name = "BodyClip"
					Noclip.Parent = game.Players.LocalPlayer.Character.HumanoidRootPart
					Noclip.MaxForce = Vector3.new(100000, 100000, 100000)
					Noclip.Velocity = Vector3.new(0, 0, 0)
				end
			else    
				if game.Players.LocalPlayer.Character.HumanoidRootPart:FindFirstChild("BodyClip") then
					game.Players.LocalPlayer.Character.HumanoidRootPart:FindFirstChild("BodyClip"):Destroy()
				end
			end
		end)
	end)
end)
