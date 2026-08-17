```lua
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local ESP = {
    Enabled = true,
    Boxes = true,
    Names = true,
    Distance = true,
    Tracers = true,
    TeamCheck = false,
    MaxDistance = 2000,
    Color = Color3.fromRGB(255, 0, 0),
    TextSize = 14,
    BoxThickness = 2
}

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "UniversalESP"
ScreenGui.Parent = CoreGui

local function CreateBox()
    local box = Drawing.new("Square")
    box.Visible = false
    box.Thickness = ESP.BoxThickness
    box.Color = ESP.Color
    box.Filled = false
    box.Transparency = 1
    return box
end

local function CreateText()
    local text = Drawing.new("Text")
    text.Visible = false
    text.Size = ESP.TextSize
    text.Color = ESP.Color
    text.Center = true
    text.Outline = true
    text.OutlineColor = Color3.new(0, 0, 0)
    return text
end

local function CreateTracer()
    local tracer = Drawing.new("Line")
    tracer.Visible = false
    tracer.Thickness = 1
    tracer.Color = ESP.Color
    tracer.Transparency = 1
    return tracer
end

local ESPObjects = {}

local function CreateESP(target)
    local espData = {
        Box = CreateBox(),
        Name = CreateText(),
        Distance = CreateText(),
        Tracer = CreateTracer(),
        Target = target
    }
    table.insert(ESPObjects, espData)
    return espData
end

local function RemoveESP(espData)
    if espData.Box then espData.Box:Remove() end
    if espData.Name then espData.Name:Remove() end
    if espData.Distance then espData.Distance:Remove() end
    if espData.Tracer then espData.Tracer:Remove() end
    table.remove(ESPObjects, table.find(ESPObjects, espData))
end

local function GetPartPosition(part)
    local position, onScreen = Camera:WorldToViewportPoint(part.Position)
    return Vector2.new(position.X, position.Y), onScreen, position.Z
end

local function UpdateESP()
    for _, espData in pairs(ESPObjects) do
        local target = espData.Target
        if target and target.Parent then
            local humanoidRootPart = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChild("Head") or target
            local humanoid = target:FindFirstChildOfClass("Humanoid")
            
            if humanoidRootPart and humanoid then
                local position, onScreen, depth = GetPartPosition(humanoidRootPart)
                local distance = (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and (LocalPlayer.Character.HumanoidRootPart.Position - humanoidRootPart.Position).Magnitude) or 0
                
                if onScreen and depth > 0 and distance <= ESP.MaxDistance then
                    local scale = 1000 / depth
                    local size = humanoidRootPart.Size
                    local boxWidth = size.X * scale * 2
                    local boxHeight = size.Y * scale * 2.5
                    
                    if ESP.Boxes then
                        espData.Box.Visible = ESP.Enabled
                        espData.Box.Position = Vector2.new(position.X - boxWidth/2, position.Y - boxHeight/2)
                        espData.Box.Size = Vector2.new(boxWidth, boxHeight)
                    else
                        espData.Box.Visible = false
                    end
                    
                    if ESP.Names then
                        espData.Name.Visible = ESP.Enabled
                        espData.Name.Text = target.Name
                        espData.Name.Position = Vector2.new(position.X, position.Y - boxHeight/2 - 20)
                    else
                        espData.Name.Visible = false
                    end
                    
                    if ESP.Distance then
                        espData.Distance.Visible = ESP.Enabled
                        espData.Distance.Text = string.format("%.0f m", distance)
                        espData.Distance.Position = Vector2.new(position.X, position.Y + boxHeight/2 + 5)
                    else
                        espData.Distance.Visible = false
                    end
                    
                    if ESP.Tracers then
                        espData.Tracer.Visible = ESP.Enabled
                        espData.Tracer.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                        espData.Tracer.To = Vector2.new(position.X, position.Y + boxHeight/2)
                    else
                        espData.Tracer.Visible = false
                    end
                else
                    espData.Box.Visible = false
                    espData.Name.Visible = false
                    espData.Distance.Visible = false
                    espData.Tracer.Visible = false
                end
            else
                espData.Box.Visible = false
                espData.Name.Visible = false
                espData.Distance.Visible = false
                espData.Tracer.Visible = false
            end
        else
            RemoveESP(espData)
        end
    end
end

local function ScanObjects()
    local existingTargets = {}
    for _, espData in pairs(ESPObjects) do
        existingTargets[espData.Target] = true
    end
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if not ESP.TeamCheck or (player.Team ~= LocalPlayer.Team) then
                if player.Character and not existingTargets[player.Character] then
                    CreateESP(player.Character)
                end
            end
        end
    end
    
    for _, object in pairs(workspace:GetDescendants()) do
        if object:IsA("Model") and object:FindFirstChildOfClass("Humanoid") then
            if not existingTargets[object] then
                local isPlayer = false
                for _, player in pairs(Players:GetPlayers()) do
                    if player.Character == object then
                        isPlayer = true
                        break
                    end
                end
                if not isPlayer then
                    CreateESP(object)
                end
            end
        end
    end
end

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        wait(1)
        ScanObjects()
    end)
end)

Players.PlayerRemoving:Connect(function(player)
    for _, espData in pairs(ESPObjects) do
        if espData.Target == player.Character then
            RemoveESP(espData)
        end
    end
end)

game:GetService("RunService").RenderStepped:Connect(function()
    if ESP.Enabled then
        UpdateESP()
    end
end)

spawn(function()
    while true do
        if ESP.Enabled then
            ScanObjects()
        end
        wait(2)
    end
end)

local function CreateMenu()
    local MenuGui = Instance.new("ScreenGui")
    MenuGui.Name = "ESPMenu"
    MenuGui.Parent = CoreGui
    
    local Frame = Instance.new("Frame")
    Frame.Size = UDim2.new(0, 200, 0, 250)
    Frame.Position = UDim2.new(0, 10, 0.3, 0)
    Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    Frame.BorderSizePixel = 0
    Frame.Parent = MenuGui
    
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, 0, 0, 30)
    Title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.Text = "Universal ESP"
    Title.Font = Enum.Font.SourceSansBold
    Title.TextSize = 16
    Title.Parent = Frame
    
    local function CreateToggle(name, yPos, settingName)
        local Button = Instance.new("TextButton")
        Button.Size = UDim2.new(1, -20, 0, 25)
        Button.Position = UDim2.new(0, 10, 0, yPos)
        Button.BackgroundColor3 = ESP[settingName] and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(150, 0, 0)
        Button.TextColor3 = Color3.fromRGB(255, 255, 255)
        Button.Text = name .. ": " .. (ESP[settingName] and "ON" or "OFF")
        Button.Font = Enum.Font.SourceSans
        Button.TextSize = 14
        Button.Parent = Frame
        
        Button.MouseButton1Click:Connect(function()
            ESP[settingName] = not ESP[settingName]
            Button.BackgroundColor3 = ESP[settingName] and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(150, 0, 0)
            Button.Text = name .. ": " .. (ESP[settingName] and "ON" or "OFF")
        end)
    end
    
    CreateToggle("ESP Enabled", 35, "Enabled")
    CreateToggle("Boxes", 65, "Boxes")
    CreateToggle("Names", 95, "Names")
    CreateToggle("Distance", 125, "Distance")
    CreateToggle("Tracers", 155, "Tracers")
    CreateToggle("Team Check", 185, "TeamCheck")
    
    local DeleteButton = Instance.new("TextButton")
    DeleteButton.Size = UDim2.new(1, -20, 0, 25)
    DeleteButton.Position = UDim2.new(0, 10, 0, 215)
    DeleteButton.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    DeleteButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    DeleteButton.Text = "Delete ESP"
    DeleteButton.Font = Enum.Font.SourceSans
    DeleteButton.TextSize = 14
    DeleteButton.Parent = Frame
    
    DeleteButton.MouseButton1Click:Connect(function()
        for _, espData in pairs(ESPObjects) do
            RemoveESP(espData)
        end
        MenuGui:Destroy()
    end)
end

ScanObjects()
CreateMenu()
```
