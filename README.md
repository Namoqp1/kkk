local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
	Title = "paischooldailaw",
	SubTitle = "kuy",
	TabWidth = 160,
	Size = UDim2.fromOffset(580, 460),
	Acrylic = false,
	Theme = "Dark",
	MinimizeKey = Enum.KeyCode.LeftControl
})

local Tabs = {
	Main = Window:AddTab({ Title = "Main", Icon = "home" }),
	Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

-- ตั้งค่าค่าเริ่มต้นให้กับ _G.MapSelect และ _G.SelectedStands
_G.MapSelect = _G.MapSelect or "Leaf Village"
_G.SelectedStands = _G.SelectedStands or {}

local Farm = Tabs.Main:AddSection("| Select") 
local Mon = {}
local MonSet = {}

-- ฟังก์ชันเพื่ออัปเดตมอนสเตอร์ตามแผนที่
local function UpdateMonsters()
	Mon = {}
	MonSet = {}

	if workspace.Server.Enemies[_G.MapSelect] then
		for i, v in pairs(workspace.Server.Enemies[_G.MapSelect]:GetChildren()) do
			if not MonSet[v.Name] then
				table.insert(Mon, v.Name)
				MonSet[v.Name] = true
			end
		end
	end
end

-- เริ่มต้นอัปเดตมอนสเตอร์เมื่อโหลด
UpdateMonsters()

-- สร้าง MultiDropdown ก่อน MapSelect
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

local MapSelect = Farm:AddDropdown("MapSelect", {
	Title = "Select Map",
	Values = {"Leaf Village", "Piece Town", "Bizarre City", "Dragon World"},
	Multi = false,
	Default = _G.MapSelect,
	Callback = function(selected)
		_G.MapSelect = selected
		-- อัปเดตมอนสเตอร์เมื่อเลือกแผนที่ใหม่
		UpdateMonsters()
		MultiDropdown:SetValues(Mon)
	end
})

local AutoFarm = Tabs.Main:AddSection("| Auto Farm")

local toggleAttack = AutoFarm:AddToggle("toggleAttack", {Title = "Auto Attack", Default = _G.Attack })
toggleAttack:OnChanged(function(bool)
	_G.Attack = bool
end)

spawn(function()
	while task.wait() do
		if _G.Attack then
			for i, v in pairs(workspace.Server.Enemies[_G.MapSelect]:GetChildren()) do
				local args = {
					[1] = "General",
					[2] = "Pets",
					[3] = "Attack",
					[4] = v:GetAttribute("ID"),
					[5] = workspace:WaitForChild("Server"):WaitForChild("Enemies"):WaitForChild(_G.MapSelect):WaitForChild(_G.SelectedStands)
				}

				game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("Bridge"):FireServer(unpack(args))
			end
		end
	end
end)
