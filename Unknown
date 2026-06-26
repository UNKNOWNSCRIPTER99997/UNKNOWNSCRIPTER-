local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local VirtualInputManager = game:GetService("VirtualInputManager")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MobileModMenu"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false

local MenuOpenButton = Instance.new("TextButton")
MenuOpenButton.Name = "MenuOpenButton"
MenuOpenButton.Parent = ScreenGui
MenuOpenButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
MenuOpenButton.Position = UDim2.new(0, 10, 0, 40)
MenuOpenButton.Size = UDim2.new(0, 110, 0, 40)
MenuOpenButton.Font = Enum.Font.SourceSansBold
MenuOpenButton.Text = "Toggle Menu"
MenuOpenButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MenuOpenButton.TextSize = 18
local UICornerBtn = Instance.new("UICorner")
UICornerBtn.CornerRadius = UDim.new(0, 8)
UICornerBtn.Parent = MenuOpenButton

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
MainFrame.Position = UDim2.new(0.3, 0, 0.2, 0)
MainFrame.Size = UDim2.new(0, 240, 0, 250)
MainFrame.Visible = true

local UICornerMain = Instance.new("UICorner")
UICornerMain.CornerRadius = UDim.new(0, 8)
UICornerMain.Parent = MainFrame

local TopBar = Instance.new("Frame")
TopBar.Name = "TopBar"
TopBar.Parent = MainFrame
TopBar.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
TopBar.Size = UDim2.new(1, 0, 0, 40)

local UICornerTop = Instance.new("UICorner")
UICornerTop.CornerRadius = UDim.new(0, 8)
UICornerTop.Parent = TopBar

local Title = Instance.new("TextLabel")
Title.Parent = TopBar
Title.BackgroundTransparency = 1
Title.Size = UDim2.new(1, 0, 1, 0)
Title.Font = Enum.Font.SourceSansBold
Title.Text = "  MENU"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 14
Title.TextXAlignment = Enum.TextXAlignment.Left

local ContentFrame = Instance.new("ScrollingFrame")
ContentFrame.Parent = MainFrame
ContentFrame.BackgroundTransparency = 1
ContentFrame.Position = UDim2.new(0, 0, 0, 50)
ContentFrame.Size = UDim2.new(1, 0, 1, -50)
ContentFrame.ScrollBarThickness = 4
ContentFrame.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 150)

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Parent = ContentFrame
UIListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
UIListLayout.Padding = UDim.new(0, 8)

local EspEnabled = false
local AimbotEnabled = false
local TeleportEnabled = false
local AutoShootEnabled = false
local IsTouching = false
local CurrentTarget = nil
local EspObjects = {}
local TeleportCooldown = 0
local ShootCooldown = 0

local function CreateToggle(name, defaultState, callback)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(0, 210, 0, 40)
    Button.Parent = ContentFrame
    Button.Font = Enum.Font.SourceSansBold
    Button.TextSize = 16
    
    local function updateVisuals(state)
        if state then
            Button.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
            Button.Text = name .. ": ON"
        else
            Button.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
            Button.Text = name .. ": OFF"
        end
        Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    end
    
    local state = defaultState
    updateVisuals(state)
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 6)
    UICorner.Parent = Button

    Button.MouseButton1Click:Connect(function()
        state = not state
        updateVisuals(state)
        callback(state)
    end)
end

MenuOpenButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

local dragging = false
local dragStart = Vector3.new()
local startPos = UDim2.new()

TopBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseBehavior) then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(
            startPos.X.Scale, 
            startPos.X.Offset + delta.X, 
            startPos.Y.Scale, 
            startPos.Y.Offset + delta.Y
        )
    end
end)

local function ClearESP()
    for _, obj in pairs(EspObjects) do
        if obj and obj.Parent then
            obj:Destroy()
        end
    end
    EspObjects = {}
end

local function CreateCleanESP(player)
    if player == LocalPlayer then return end
    if not player.Character then return end
    
    local char = player.Character
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    
    for _, obj in pairs(EspObjects) do
        if obj.Parent == char then
            return
        end
    end
    
    local highlight = Instance.new("Highlight")
    highlight.Parent = char
    highlight.FillColor = Color3.fromRGB(255, 0, 0)
    highlight.FillTransparency = 0.2
    highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
    highlight.OutlineTransparency = 0.1
    highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    table.insert(EspObjects, highlight)
    
    local nameGui = Instance.new("BillboardGui")
    nameGui.Parent = char
    nameGui.Size = UDim2.new(0, 200, 0, 40)
    nameGui.Position = UDim2.new(0, -100, 0, -80)
    nameGui.Adornee = root
    nameGui.AlwaysOnTop = true
    nameGui.StudsOffset = Vector3.new(0, 3.5, 0)
    
    local nameLabel = Instance.new("TextLabel")
    nameLabel.Parent = nameGui
    nameLabel.Size = UDim2.new(1, 0, 1, 0)
    nameLabel.BackgroundTransparency = 1
    nameLabel.Text = player.Name
    nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    nameLabel.TextScaled = true
    nameLabel.Font = Enum.Font.GothamBold
    nameLabel.TextStrokeTransparency = 0.2
    nameLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    
    table.insert(EspObjects, nameGui)
    table.insert(EspObjects, nameLabel)
end

local function UpdateESP()
    if not EspEnabled then
        ClearESP()
        return
    end
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local hasEsp = false
            for _, obj in pairs(EspObjects) do
                if obj.Parent == player.Character then
                    hasEsp = true
                    break
                end
            end
            
            if not hasEsp then
                CreateCleanESP(player)
            end
        end
    end
end

CreateToggle("ESP", false, function(state)
    EspEnabled = state
    if state then
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer then
                CreateCleanESP(player)
            end
        end
    else
        ClearESP()
    end
end)

local function GetClosestPlayerToMe()
    local closestPlayer = nil
    local shortestDistance = math.huge
    
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then 
        return nil 
    end
    
    local myPos = LocalPlayer.Character.HumanoidRootPart.Position

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") and player.Character:FindFirstChildOfClass("Humanoid") and player.Character:FindFirstChildOfClass("Humanoid").Health > 0 then
            
            local enemyPos = player.Character.Head.Position
            local distance = (enemyPos - myPos).Magnitude
            
            if distance < shortestDistance then
                closestPlayer = player
                shortestDistance = distance
            end
        end
    end
    return closestPlayer
end

CreateToggle("Aimbot", false, function(state)
    AimbotEnabled = state
end)

local function AutoShoot()
    if not AutoShootEnabled then return end
    if tick() - ShootCooldown < 0.1 then return end
    
    local tool = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Tool")
    if not tool then return end
    
    local mouse = LocalPlayer:GetMouse()
    if mouse then
        VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 0)
        task.wait(0.05)
        VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, 0)
        ShootCooldown = tick()
    end
end

local function GetTargetHealth(target)
    if not target or not target.Character then return 0 end
    local humanoid = target.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        return humanoid.Health
    end
    return 0
end

local function TeleportToTopOfHead()
    if not TeleportEnabled then return end
    if tick() - TeleportCooldown < 0.05 then return end
    
    if CurrentTarget then
        local health = GetTargetHealth(CurrentTarget)
        if health <= 0 then
            CurrentTarget = nil
        end
    end
    
    if not CurrentTarget then
        CurrentTarget = GetClosestPlayerToMe()
        if not CurrentTarget then
            return
        end
    end
    
    if not CurrentTarget or not CurrentTarget.Character then
        CurrentTarget = nil
        return
    end
    
    local targetChar = CurrentTarget.Character
    local targetHead = targetChar:FindFirstChild("Head")
    if not targetHead then return end
    
    local myChar = LocalPlayer.Character
    if not myChar then return end
    
    local myRoot = myChar:FindFirstChild("HumanoidRootPart")
    if not myRoot then return end
    
    local teleportPos = targetHead.Position + Vector3.new(0, 3.5, 0)
    myRoot.CFrame = CFrame.new(teleportPos)
    Camera.CFrame = CFrame.new(Camera.CFrame.Position, targetHead.Position)
    
    TeleportCooldown = tick()
    
    if AutoShootEnabled then
        AutoShoot()
    end
end

CreateToggle("TP on Head", false, function(state)
    TeleportEnabled = state
    if not state then
        CurrentTarget = nil
    end
end)

CreateToggle("Auto Shoot", false, function(state)
    AutoShootEnabled = state
end)

UserInputService.InputBegan:Connect(function(input, processed)
    if not processed and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1) then
        IsTouching = true
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        IsTouching = false
    end
end)

RunService.Heartbeat:Connect(function()
    UpdateESP()
end)

RunService.RenderStepped:Connect(function()
    if AimbotEnabled and IsTouching then
        local target = GetClosestPlayerToMe()
        if target and target.Character and target.Character:FindFirstChild("Head") then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character.Head.Position)
        end
    end
end)

RunService.Heartbeat:Connect(function()
    TeleportToTopOfHead()
end)

Players.PlayerRemoving:Connect(function(player)
    for i = #EspObjects, 1, -1 do
        if EspObjects[i] and EspObjects[i].Parent == player.Character then
            EspObjects[i]:Destroy()
            table.remove(EspObjects, i)
        end
    end
end)

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        if EspEnabled then
            task.wait(0.5)
            CreateCleanESP(player)
        end
    end)
end)
