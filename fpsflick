-- FerHacks for [FPS] Flick
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "FerHacks - [FPS] Flick",
    LoadingTitle = "FerHacks Initializing...",
    LoadingSubtitle = "Strong Proximity Aimbot + Wallhack",
    ConfigurationSaving = { Enabled = true, FolderName = "FerHacks", FileName = "FlickConfig" },
    Discord = { Enabled = false },
    KeySystem = false
})

local MainTab = Window:CreateTab("Main", 4483362458)
local CombatSection = MainTab:CreateSection("Combat")
local VisualsSection = MainTab:CreateSection("Visuals")

-- Variables
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")

local AimbotEnabled = false
local ESPEnabled = false
local ProximityRange = 50
local AimSmoothness = 0.2
local AimPart = "Head"

local ESPObjects = {}

-- Anti-Detection Utilities
local function GetClosestPlayer()
    local closest, dist = nil, math.huge
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
            local root = player.Character:FindFirstChild("HumanoidRootPart")
            local head = player.Character:FindFirstChild("Head")
            if root and head then
                local distance = (LocalPlayer.Character.HumanoidRootPart.Position - root.Position).Magnitude
                if distance < ProximityRange and distance < dist then
                    local origin = Camera.CFrame.Position + Vector3.new(math.random(-1,1)*0.1, math.random(-1,1)*0.1, 0)
                    local ray = Ray.new(origin, (head.Position - origin).Unit * 500)
                    local hit, _ = workspace:FindPartOnRayWithIgnoreList(ray, {LocalPlayer.Character})
                    if not hit or hit:IsDescendantOf(player.Character) then
                        dist = distance
                        closest = player
                    end
                end
            end
        end
    end
    return closest
end

local aimConnection
MainTab:CreateToggle({
    Name = "Strong Proximity Aimbot",
    CurrentValue = false,
    Flag = "AimbotToggle",
    Callback = function(Value)
        AimbotEnabled = Value
        if Value then
            aimConnection = RunService.RenderStepped:Connect(function()
                local target = GetClosestPlayer()
                if target and target.Character and target.Character:FindFirstChild(AimPart) then
                    local targetPos = target.Character[AimPart].Position
                    local currentCFrame = Camera.CFrame
                    local lerpFactor = AimSmoothness + math.random(-5,5)/1000
                    Camera.CFrame = currentCFrame:Lerp(CFrame.lookAt(currentCFrame.Position, targetPos), lerpFactor)
                end
            end)
        else
            if aimConnection then aimConnection:Disconnect() end
        end
    end
})

MainTab:CreateSlider({
    Name = "Proximity Range (Studs)",
    Range = {10, 100},
    Increment = 5,
    CurrentValue = ProximityRange,
    Flag = "ProximitySlider",
    Callback = function(Value) ProximityRange = Value end
})

MainTab:CreateSlider({
    Name = "Aim Smoothness",
    Range = {0.05, 0.5},
    Increment = 0.05,
    CurrentValue = AimSmoothness,
    Flag = "SmoothSlider",
    Callback = function(Value) AimSmoothness = Value end
})

-- ESP / Wallhack
local function CreateESP(player)
    if player == LocalPlayer or ESPObjects[player] then return end
    local char = player.Character or player.CharacterAdded:Wait()
    
    local box = Drawing.new("Square")
    box.Thickness = 2
    box.Filled = false
    box.Color = Color3.fromRGB(255, 0, 0)
    box.Transparency = 1
    
    local name = Drawing.new("Text")
    name.Size = 14
    name.Color = Color3.fromRGB(255, 255, 255)
    name.Outline = true
    name.Center = true
    
    ESPObjects[player] = {Box = box, Name = name}
end

local function UpdateESP()
    for player, drawings in pairs(ESPObjects) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character.Humanoid.Health > 0 then
            local root = player.Character.HumanoidRootPart
            local rootPos, onScreen = Camera:WorldToViewportPoint(root.Position)
            
            if onScreen then
                local headPos = Camera:WorldToViewportPoint(root.Position + Vector3.new(0, 2.5, 0))
                local legPos = Camera:WorldToViewportPoint(root.Position - Vector3.new(0, 3, 0))
                
                local boxHeight = math.abs(headPos.Y - legPos.Y)
                local boxWidth = boxHeight * 0.6
                
                drawings.Box.Size = Vector2.new(boxWidth, boxHeight)
                drawings.Box.Position = Vector2.new(rootPos.X - boxWidth/2, rootPos.Y - boxHeight/2)
                drawings.Box.Visible = true
                
                drawings.Name.Text = player.Name .. " [" .. math.floor((LocalPlayer.Character.HumanoidRootPart.Position - root.Position).Magnitude) .. "m]"
                drawings.Name.Position = Vector2.new(rootPos.X, rootPos.Y - boxHeight/2 - 15)
                drawings.Name.Visible = true
            else
                drawings.Box.Visible = false
                drawings.Name.Visible = false
            end
        else
            drawings.Box.Visible = false
            drawings.Name.Visible = false
        end
    end
end

MainTab:CreateToggle({
    Name = "Wallhack ESP (See Through Walls)",
    CurrentValue = false,
    Flag = "ESPToggle",
    Callback = function(Value)
        ESPEnabled = Value
        if Value then
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LocalPlayer then CreateESP(p) end
            end
            Players.PlayerAdded:Connect(CreateESP)
            RunService.RenderStepped:Connect(UpdateESP)
        else
            for _, drawings in pairs(ESPObjects) do
                drawings.Box:Remove()
                drawings.Name:Remove()
            end
            ESPObjects = {}
        end
    end
})

MainTab:CreateButton({
    Name = "Unload FerHacks",
    Callback = function()
        if aimConnection then aimConnection:Disconnect() end
        for _, drawings in pairs(ESPObjects) do
            pcall(function() drawings.Box:Remove() drawings.Name:Remove() end)
        end
        Rayfield:Destroy()
    end
})

Rayfield:Notify({
    Title = "FerHacks Loaded",
    Content = "Proximity Aimbot + Wall ESP Active.",
    Duration = 5,
    Image = 4483362458
})

Players.PlayerAdded:Connect(function(plr)
    if ESPEnabled then CreateESP(plr) end
end)
