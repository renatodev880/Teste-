-- LEAKED BY CRYPTIC SOURCES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")

local lp = Players.LocalPlayer

-- Clean up existing UI
if CoreGui:FindFirstChild("RyzzinHub_AP") then
    CoreGui.RyzzinHub_AP:Destroy()
end

-- Posicoes
local pos1 = Vector3.new(-476.48, -6.28, 92.73)
local pos2 = Vector3.new(-483.12, -4.95, 94.8)

local autoWalkEnabled = false
local batFollowEnabled = false
local walkSpeedValue = 30

-- Main UI Container
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "RyzzinHub_AP"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = CoreGui

-- Main Frame (Agora sem posição fixa, inicia no topo esquerdo)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.fromOffset(195, 140)
MainFrame.Position = UDim2.new(0, 50, 0, 50) -- Inicia no canto superior esquerdo
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BackgroundTransparency = 0.1
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true -- Habilita o arraste nativo do Roblox (facilita para mover a qualquer canto)
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 14)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(40, 40, 40)
MainStroke.Thickness = 2.5
MainStroke.Parent = MainFrame

-- Title
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.BackgroundTransparency = 1
Title.Text = "Ryzzin hub"
Title.Font = Enum.Font.GothamBlack
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 15
Title.Parent = MainFrame

------------------------------------------------------------------
-- BUTTONS
------------------------------------------------------------------

local function createMenuBtn(name, pos, text)
    local btn = Instance.new("TextButton")
    btn.Name = name
    btn.Size = UDim2.new(1, -20, 0, 30)
    btn.Position = pos
    btn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    btn.BorderSizePixel = 0
    btn.Text = text
    btn.Font = Enum.Font.GothamBlack
    btn.TextColor3 = Color3.fromRGB(240, 245, 255)
    btn.TextSize = 13
    btn.Parent = MainFrame
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    return btn
end

local AutoWalkBtn = createMenuBtn("AutoWalk", UDim2.new(0, 10, 0, 40), "Auto Walk")
local BatFollowBtn = createMenuBtn("BatFollow", UDim2.new(0, 10, 0, 75), "Bat Follow")

-- Speed Walk Input Box
local SpeedFrame = Instance.new("Frame")
SpeedFrame.Size = UDim2.new(1, -20, 0, 26)
SpeedFrame.Position = UDim2.new(0, 10, 0, 110)
SpeedFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
SpeedFrame.BorderSizePixel = 0
SpeedFrame.Parent = MainFrame
Instance.new("UICorner", SpeedFrame).CornerRadius = UDim.new(0, 8)

local SpeedTitle = Instance.new("TextLabel")
SpeedTitle.Size = UDim2.new(0.55, 0, 1, 0)
SpeedTitle.Position = UDim2.new(0, 8, 0, 0)
SpeedTitle.BackgroundTransparency = 1
SpeedTitle.Text = "Speed"
SpeedTitle.Font = Enum.Font.GothamBlack
SpeedTitle.TextColor3 = Color3.fromRGB(240, 245, 255)
SpeedTitle.TextSize = 12
SpeedTitle.TextXAlignment = Enum.TextXAlignment.Left
SpeedTitle.Parent = SpeedFrame

local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(0.38, 0, 0.8, 0)
SpeedInput.Position = UDim2.new(0.58, 0, 0.1, 0)
SpeedInput.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
SpeedInput.Text = "30"
SpeedInput.Font = Enum.Font.GothamBold
SpeedInput.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedInput.TextSize = 12
SpeedInput.BorderSizePixel = 0
SpeedInput.Parent = SpeedFrame
Instance.new("UICorner", SpeedInput).CornerRadius = UDim.new(0, 6)

------------------------------------------------------------------
-- FEATURES LOGIC (Mantida original)
------------------------------------------------------------------
SpeedInput.FocusLost:Connect(function()
    walkSpeedValue = tonumber(SpeedInput.Text) or 30
end)

RunService.Heartbeat:Connect(function()
    if lp.Character and lp.Character:FindFirstChild("Humanoid") then
        lp.Character.Humanoid.WalkSpeed = walkSpeedValue
    end
end)

AutoWalkBtn.MouseButton1Click:Connect(function()
    autoWalkEnabled = not autoWalkEnabled
    AutoWalkBtn.TextColor3 = autoWalkEnabled and Color3.fromRGB(0, 255, 150) or Color3.fromRGB(240, 245, 255)
end)

task.spawn(function()
    while task.wait() do
        if autoWalkEnabled and lp.Character and lp.Character:FindFirstChild("Humanoid") then
            lp.Character.Humanoid:MoveTo(pos1)
            lp.Character.Humanoid.MoveToFinished:Wait()
            lp.Character.Humanoid:MoveTo(pos2)
            lp.Character.Humanoid.MoveToFinished:Wait()
        end
    end
end)

BatFollowBtn.MouseButton1Click:Connect(function()
    batFollowEnabled = not batFollowEnabled
    BatFollowBtn.TextColor3 = batFollowEnabled and Color3.fromRGB(0, 255, 150) or Color3.fromRGB(240, 245, 255)
end)

task.spawn(function()
    while task.wait() do
        if batFollowEnabled and lp.Character then
            local bat = lp.Backpack:FindFirstChild("Bat") or lp.Character:FindFirstChild("Bat")
            if bat and bat.Parent ~= lp.Character then lp.Character.Humanoid:EquipTool(bat) end
            local closest, dist = nil, 50
            for _, v in pairs(Players:GetPlayers()) do
                if v ~= lp and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
                    local d = (lp.Character.HumanoidRootPart.Position - v.Character.HumanoidRootPart.Position).Magnitude
                    if d < dist then dist = d; closest = v end
                end
            end
            if closest then
                lp.Character.Humanoid:MoveTo(closest.Character.HumanoidRootPart.Position)
                if bat and dist < 5 then bat:Activate() end
            end
        end
    end
end)
