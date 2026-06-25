-- BY UNKNOWNSCRIPTER

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "UI"
screenGui.DisplayOrder = 999
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 90, 0, 32)
toggleButton.Position = UDim2.new(0, 20, 0.5, -150)
toggleButton.BackgroundColor3 = Color3.fromRGB(12, 13, 15)
toggleButton.TextColor3 = Color3.fromRGB(0, 220, 255)
toggleButton.Text = "[ MENU ]"
toggleButton.Parent = screenGui
Instance.new("UICorner", toggleButton).CornerRadius = UDim.new(0, 4)

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 240, 0, 220)
mainFrame.Position = UDim2.new(0, 20, 0.5, -100)
mainFrame.BackgroundColor3 = Color3.fromRGB(15, 17, 20)
mainFrame.Active = true
mainFrame.Parent = screenGui
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 8)

local title = Instance.new("TextLabel", mainFrame)
title.Size = UDim2.new(1, -40, 0, 35)
title.BackgroundTransparency = 1
title.Text = "BY UNKNOWNSCRIPTER"
title.TextColor3 = Color3.new(1, 1, 1)
title.Parent = mainFrame

local closeBtn = Instance.new("TextButton", mainFrame)
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.BackgroundTransparency = 1
closeBtn.Text = "✕"
closeBtn.TextColor3 = Color3.fromRGB(255, 70, 70)
closeBtn.Parent = mainFrame

local list = Instance.new("UIListLayout", mainFrame)
list.Padding = UDim.new(0, 8)
list.HorizontalAlignment = Enum.HorizontalAlignment.Center
list.VerticalAlignment = Enum.VerticalAlignment.Center

local function createBtn(text)
	local b = Instance.new("TextButton", mainFrame)
	b.Size = UDim2.new(0, 210, 0, 35)
	b.BackgroundColor3 = Color3.fromRGB(25, 27, 30)
	b.TextColor3 = Color3.new(1, 1, 1)
	b.Text = text
	Instance.new("UICorner", b).CornerRadius = UDim.new(0, 4)
	return b
end

local saveBtn = createBtn("RECORD POSITION")
local warpBtn = createBtn("WARP TO POSITION")
local dragBtn = createBtn("DRAG: ENABLED")

local pos = nil
local dragging = true

toggleButton.MouseButton1Click:Connect(function() mainFrame.Visible = true end)
closeBtn.MouseButton1Click:Connect(function() mainFrame.Visible = false end)

dragBtn.MouseButton1Click:Connect(function()
	dragging = not dragging
	dragBtn.Text = dragging and "DRAG: ENABLED" or "DRAG: LOCKED"
end)

mainFrame.InputBegan:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
		local startPos = input.Position
		local framePos = mainFrame.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.Change then
				local delta = input.Position - startPos
				mainFrame.Position = UDim2.new(framePos.X.Scale, framePos.X.Offset + delta.X, framePos.Y.Scale, framePos.Y.Offset + delta.Y)
			end
		end)
	end
end)

saveBtn.MouseButton1Click:Connect(function()
	local char = Players.LocalPlayer.Character
	if char and char:FindFirstChild("HumanoidRootPart") then
		pos = char.HumanoidRootPart.Position
		saveBtn.Text = "SAVED!"
		task.wait(1)
		saveBtn.Text = "RECORD POSITION"
	end
end)

warpBtn.MouseButton1Click:Connect(function()
	local char = Players.LocalPlayer.Character
	if pos and char and char:FindFirstChild("HumanoidRootPart") then
		char.HumanoidRootPart.CFrame = CFrame.new(pos + Vector3.new(0, 2, 0))
	end
end)
