--[[
    Cheat Menu de Teste (Aimbot 50%, ESP, God, Silent Aim)
    Menu abre/fecha ao clicar uma bolinha no canto.
    Use APENAS para teste de anti-cheat!
--]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")

-- CONFIGS
local RGB = Color3.fromRGB(255, 0, 0)
local SilentPercent = 50
local AimbotPercent = 50 -- Agora o aimbot só ativa 50% das vezes
local UI_TOGGLE_POS = UDim2.new(0, 10, 0, 200)

local function getClosestPlayer()
    local mouse = LocalPlayer:GetMouse()
    local closest, dist = nil, math.huge
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
            local pos = workspace.CurrentCamera:WorldToViewportPoint(p.Character.Head.Position)
            local magnitude = (Vector2.new(pos.X, pos.Y) - Vector2.new(mouse.X, mouse.Y)).Magnitude
            if magnitude < dist then
                closest = p
                dist = magnitude
            end
        end
    end
    return closest
end

local AimbotActive = false
local aimbotConn
local function enableAimbot()
    AimbotActive = true
    if aimbotConn then aimbotConn:Disconnect() end
    aimbotConn = RunService.RenderStepped:Connect(function()
        if AimbotActive and math.random(1,100) <= AimbotPercent then
            local p = getClosestPlayer()
            if p and p.Character and p.Character:FindFirstChild("Head") then
                workspace.CurrentCamera.CFrame = CFrame.new(
                    workspace.CurrentCamera.CFrame.Position,
                    p.Character.Head.Position
                )
            end
        end
    end)
end
local function disableAimbot()
    AimbotActive = false
    if aimbotConn then aimbotConn:Disconnect() end
end

-- ESP
local espEnabled = false
local function enableESP()
    espEnabled = true
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
            if not p.Character.Head:FindFirstChild("ESP_GUI") then
                local esp = Instance.new("BillboardGui")
                esp.Name = "ESP_GUI"
                esp.Adornee = p.Character.Head
                esp.Size = UDim2.new(0, 100, 0, 40)
                esp.AlwaysOnTop = true
                esp.Parent = p.Character.Head

                local tag = Instance.new("TextLabel")
                tag.Size = UDim2.new(1, 0, 1, 0)
                tag.BackgroundTransparency = 1
                tag.Text = p.Name
                tag.TextColor3 = RGB
                tag.TextStrokeTransparency = 0
                tag.Font = Enum.Font.SourceSansBold
                tag.TextScaled = true
                tag.Parent = esp
            end
        end
    end
end
local function disableESP()
    espEnabled = false
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
            local head = p.Character.Head
            if head:FindFirstChild("ESP_GUI") then
                head.ESP_GUI:Destroy()
            end
        end
    end
end

-- God mode
local godConn
local function enableGodMode()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        if godConn then godConn:Disconnect() end
        godConn = LocalPlayer.Character.Humanoid:GetPropertyChangedSignal("Health"):Connect(function()
            LocalPlayer.Character.Humanoid.Health = LocalPlayer.Character.Humanoid.MaxHealth
        end)
        LocalPlayer.Character.Humanoid.Health = LocalPlayer.Character.Humanoid.MaxHealth
    end
end
local function disableGodMode()
    if godConn then godConn:Disconnect() end
end

-- Silent Aim (simulação)
local silentAimOn = false
local function enableSilentAim(percent)
    silentAimOn = true
    print("Silent Aim ativado em " .. percent .. "% (simulado para testes)")
end
local function disableSilentAim()
    silentAimOn = false
end

-- GUI
local sg = Instance.new("ScreenGui")
sg.Name = "BrainrotCheatMenu"
sg.Parent = game.CoreGui

-- Bolinha para abrir/fechar
local openBtn = Instance.new("TextButton")
openBtn.Size = UDim2.new(0, 36, 0, 36)
openBtn.Position = UI_TOGGLE_POS
openBtn.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
openBtn.Text = ""
openBtn.Parent = sg
openBtn.AnchorPoint = Vector2.new(0, 0)
openBtn.BorderSizePixel = 0
openBtn.AutoButtonColor = true
openBtn.Name = "OpenBtn"

local circle = Instance.new("UICorner")
circle.CornerRadius = UDim.new(1,0)
circle.Parent = openBtn

local icon = Instance.new("TextLabel")
icon.Size = UDim2.new(1,0,1,0)
icon.BackgroundTransparency = 1
icon.Text = "≡"
icon.TextColor3 = Color3.new(1,1,1)
icon.Font = Enum.Font.SourceSansBold
icon.TextSize = 22
icon.Parent = openBtn

-- Menu principal
local menuFrame = Instance.new("Frame")
menuFrame.Size = UDim2.new(0, 220, 0, 240)
menuFrame.Position = UDim2.new(0, openBtn.Position.X.Offset + 40, 0, openBtn.Position.Y.Offset - 20)
menuFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
menuFrame.BorderSizePixel = 0
menuFrame.Visible = false
menuFrame.Parent = sg

local menuCorner = Instance.new("UICorner")
menuCorner.CornerRadius = UDim.new(0.12, 0)
menuCorner.Parent = menuFrame

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 40, 0, 30)
closeBtn.Position = UDim2.new(1, -45, 0, 5)
closeBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.new(1,1,1)
closeBtn.Font = Enum.Font.SourceSansBold
closeBtn.TextSize = 22
closeBtn.Parent = menuFrame
closeBtn.AutoButtonColor = true

local function createButton(y, text, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 40)
    btn.Position = UDim2.new(0, 10, 0, 40 + (y*50))
    btn.BackgroundColor3 = Color3.fromRGB(50,50,50)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 20
    btn.Text = text.." [OFF]"
    btn.Parent = menuFrame

    local isOn = false
    btn.MouseButton1Click:Connect(function()
        isOn = not isOn
        btn.Text = text.." ["..(isOn and "ON" or "OFF").."]"
        callback(isOn, btn)
    end)
    return btn
end

-- Botões
local aimBtn = createButton(0, "Aimbot Head (50%)", function(on)
    if on then enableAimbot() else disableAimbot() end
end)
local espBtn = createButton(1, "ESP Vermelho", function(on)
    if on then enableESP() else disableESP() end
end)
local godBtn = createButton(2, "God Mode", function(on)
    if on then enableGodMode() else disableGodMode() end
end)
local silentBtn = createButton(3, "Silent Aim ("..SilentPercent.."%)", function(on, btn)
    if on then
        enableSilentAim(SilentPercent)
        btn.Text = "Silent Aim ("..SilentPercent.."%) [ON]"
    else
        disableSilentAim()
        btn.Text = "Silent Aim ("..SilentPercent.."%) [OFF]"
    end
end)

-- Abrir/fechar menu
openBtn.MouseButton1Click:Connect(function()
    menuFrame.Visible = true
    openBtn.Visible = false
end)
closeBtn.MouseButton1Click:Connect(function()
    menuFrame.Visible = false
    openBtn.Visible = true
end)

print("Menu de cheats carregado para TESTE. Use só em ambiente controlado!")
