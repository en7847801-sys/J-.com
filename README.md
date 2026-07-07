--[[
    Jeep Sik Hub - Roblox Script Hub
    Bordas: Azuladas | Senha: 1234
    ESP: Linhas retas do centro até cabeça (atravessa paredes)
    Minimizar: Bolinha Vermelha "JEEP"
]]

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")
local TeleportService = game:GetService("TeleportService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- ============================================
-- LIMPAR DADOS ANTERIORES
-- ============================================
getgenv().ESPData = getgenv().ESPData or {}
getgenv().ESPData.Boxes = {}
getgenv().ESPData.Tracers = {}
getgenv().ESPData.Names = {}
getgenv().ESPData.HealthBars = {}
getgenv().ESPData.Connections = {}

getgenv().Settings = getgenv().Settings or {}
getgenv().Settings.ESPEnabled = false
getgenv().Settings.ShowBoxes = true
getgenv().Settings.ShowTracers = true
getgenv().Settings.ShowNames = true
getgenv().Settings.ShowHealth = true
getgenv().Settings.ShowDistance = true
getgenv().Settings.TracerOrigin = "Center" -- Center, Bottom
getgenv().Settings.ESPColor = Color3.fromRGB(60, 120, 255)
getgenv().Settings.FlyEnabled = false
getgenv().Settings.InfJump = false
getgenv().Settings.Noclip = false
getgenv().Settings.AntiAfk = false
getgenv().Settings.AutoFarm = false
getgenv().Settings.NightMode = false
getgenv().Settings.FullBright = false

-- ============================================
-- SISTEMA DE NOTIFICAÇÕES
-- ============================================
local NotificationFrame = Instance.new("Frame")
NotificationFrame.Name = "NotifFrame"
NotificationFrame.Parent = CoreGui
NotificationFrame.BackgroundTransparency = 1
NotificationFrame.Position = UDim2.new(0, 20, 0, -100)
NotificationFrame.Size = UDim2.new(0, 280, 0, 0)
NotificationFrame.ZIndex = 999

local NotifList = Instance.new("UIListLayout")
NotifList.Parent = NotificationFrame
NotifList.SortOrder = Enum.SortOrder.LayoutOrder
NotifList.VerticalAlignment = Enum.VerticalAlignment.Bottom
NotifList.Padding = UDim.new(0, 8)

function notify(title, msg, duration)
    local n = Instance.new("Frame")
    n.Parent = NotificationFrame
    n.BackgroundColor3 = Color3.fromRGB(20, 22, 30)
    n.BorderSizePixel = 2
    n.BorderColor3 = Color3.fromRGB(60, 120, 255)
    n.Size = UDim2.new(1, 0, 0, 60)
    n.BackgroundTransparency = 1
    n.Position = UDim2.new(0, -300, 0, 0)
    n.ZIndex = 999
    n.ClipsDescendants = true
    
    Instance.new("UICorner", n).CornerRadius = UDim.new(0, 10)
    
    local bar = Instance.new("Frame", n)
    bar.BackgroundColor3 = Color3.fromRGB(60, 120, 255)
    bar.BorderSizePixel = 0
    bar.Size = UDim2.new(0, 4, 1, 0)
    bar.ZIndex = 1000
    
    local t = Instance.new("TextLabel", n)
    t.BackgroundTransparency = 1
    t.Position = UDim2.new(0, 15, 0, 5)
    t.Size = UDim2.new(1, -20, 0, 20)
    t.Font = Enum.Font.GothamBold
    t.Text = title
    t.TextColor3 = Color3.fromRGB(255, 255, 255)
    t.TextSize = 13
    t.TextXAlignment = Enum.TextXAlignment.Left
    t.ZIndex = 1000
    
    local m = Instance.new("TextLabel", n)
    m.BackgroundTransparency = 1
    m.Position = UDim2.new(0, 15, 0, 28)
    m.Size = UDim2.new(1, -20, 0, 25)
    m.Font = Enum.Font.Gotham
    m.Text = msg
    m.TextColor3 = Color3.fromRGB(160, 180, 220)
    m.TextSize = 11
    m.TextXAlignment = Enum.TextXAlignment.Left
    m.ZIndex = 1000
    
    TweenService:Create(n, TweenInfo.new(0.4, Enum.EasingStyle.Back), {
        BackgroundTransparency = 0,
        Position = UDim2.new(0, 0, 0, 0)
    }):Play()
    
    delay(duration or 3, function()
        TweenService:Create(n, TweenInfo.new(0.4, Enum.EasingStyle.Quad), {
            BackgroundTransparency = 1,
            Position = UDim2.new(0, -300, 0, 0)
        }):Play()
        wait(0.5)
        n:Destroy()
    end)
end

-- ============================================
-- TELA DE SENHA
-- ============================================
local Hub = Instance.new("ScreenGui")
Hub.Name = "JeepSikHub"
Hub.Parent = CoreGui
Hub.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local Overlay = Instance.new("Frame", Hub)
Overlay.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Overlay.BackgroundTransparency = 1
Overlay.BorderSizePixel = 0
Overlay.Size = UDim2.new(1, 0, 1, 0)
Overlay.ZIndex = 10
TweenService:Create(Overlay, TweenInfo.new(0.5), {BackgroundTransparency = 0.6}):Play()

local PassFrame = Instance.new("Frame", Hub)
PassFrame.BackgroundColor3 = Color3.fromRGB(20, 22, 30)
PassFrame.BorderSizePixel = 2
PassFrame.BorderColor3 = Color3.fromRGB(60, 120, 255)
PassFrame.Position = UDim2.new(0.5, -155, 0.5, -200)
PassFrame.Size = UDim2.new(0, 310, 0, 280)
PassFrame.AnchorPoint = Vector2.new(0.5, 0.5)
PassFrame.ZIndex = 11
PassFrame.BackgroundTransparency = 1
Instance.new("UICorner", PassFrame).CornerRadius = UDim.new(0, 15)

TweenService:Create(PassFrame, TweenInfo.new(0.8, Enum.EasingStyle.Back), {
    Position = UDim2.new(0.5, -155, 0.5, -140),
    BackgroundTransparency = 0
}):Play()

local lockIcon = Instance.new("TextLabel", PassFrame)
lockIcon.BackgroundTransparency = 1
lockIcon.Position = UDim2.new(0, 0, 0, 10)
lockIcon.Size = UDim2.new(1, 0, 0, 40)
lockIcon.Font = Enum.Font.GothamBold
lockIcon.Text = "🔒"
lockIcon.TextColor3 = Color3.fromRGB(60, 120, 255)
lockIcon.TextSize = 35
lockIcon.ZIndex = 12

local passTitle = Instance.new("TextLabel", PassFrame)
passTitle.BackgroundTransparency = 1
passTitle.Position = UDim2.new(0, 0, 0, 50)
passTitle.Size = UDim2.new(1, 0, 0, 30)
passTitle.Font = Enum.Font.GothamBold
passTitle.Text = "JEEP SIK HUB"
passTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
passTitle.TextSize = 22
passTitle.ZIndex = 12

local passSub = Instance.new("TextLabel", PassFrame)
passSub.BackgroundTransparency = 1
passSub.Position = UDim2.new(0, 0, 0, 80)
passSub.Size = UDim2.new(1, 0, 0, 20)
passSub.Font = Enum.Font.Gotham
passSub.Text = "Digite a senha para acessar"
passSub.TextColor3 = Color3.fromRGB(160, 180, 220)
passSub.TextSize = 11
passSub.ZIndex = 12

local passInput = Instance.new("TextBox", PassFrame)
passInput.BackgroundColor3 = Color3.fromRGB(30, 32, 40)
passInput.BorderSizePixel = 2
passInput.BorderColor3 = Color3.fromRGB(60, 120, 255)
passInput.Position = UDim2.new(0.5, -110, 0, 115)
passInput.Size = UDim2.new(0, 220, 0, 42)
passInput.Font = Enum.Font.GothamBold
passInput.PlaceholderText = "Senha..."
passInput.PlaceholderColor3 = Color3.fromRGB(100, 120, 160)
passInput.Text = ""
passInput.TextColor3 = Color3.fromRGB(255, 255, 255)
passInput.TextSize = 18
passInput.ZIndex = 12
passInput.TextXAlignment = Enum.TextXAlignment.Center
Instance.new("UICorner", passInput).CornerRadius = UDim.new(0, 10)

local confirmBtn = Instance.new("TextButton", PassFrame)
confirmBtn.BackgroundColor3 = Color3.fromRGB(60, 120, 255)
confirmBtn.BorderSizePixel = 0
confirmBtn.Position = UDim2.new(0.5, -90, 0, 180)
confirmBtn.Size = UDim2.new(0, 180, 0, 40)
confirmBtn.Font = Enum.Font.GothamBold
confirmBtn.Text = "ENTRAR"
confirmBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
confirmBtn.TextSize = 15
confirmBtn.ZIndex = 12
Instance.new("UICorner", confirmBtn).CornerRadius = UDim.new(0, 10)

local errorLabel = Instance.new("TextLabel", PassFrame)
errorLabel.BackgroundTransparency = 1
errorLabel.Position = UDim2.new(0, 0, 0, 240)
errorLabel.Size = UDim2.new(1, 0, 0, 25)
errorLabel.Font = Enum.Font.Gotham
errorLabel.Text = ""
errorLabel.TextColor3 = Color3.fromRGB(255, 80, 80)
errorLabel.TextSize = 11
errorLabel.ZIndex = 12

-- ============================================
-- BOLINHA VERMELHA
-- ============================================
local MiniBall = Instance.new("TextButton", Hub)
MiniBall.BackgroundColor3 = Color3.fromRGB(200, 30, 30)
MiniBall.BorderSizePixel = 2
MiniBall.BorderColor3 = Color3.fromRGB(255, 255, 255)
MiniBall.Position = UDim2.new(0.92, -50, 0.85, -50)
MiniBall.Size = UDim2.new(0, 55, 0, 55)
MiniBall.Font = Enum.Font.GothamBold
MiniBall.Text = "JEEP"
MiniBall.TextColor3 = Color3.fromRGB(255, 255, 255)
MiniBall.TextSize = 10
MiniBall.Visible = false
MiniBall.ZIndex = 100
MiniBall.Active = true
MiniBall.Draggable = true
Instance.new("UICorner", MiniBall).CornerRadius = UDim.new(1, 0)

spawn(function()
    while MiniBall.Parent do
        if MiniBall.Visible then
            TweenService:Create(MiniBall, TweenInfo.new(0.5, Enum.EasingStyle.Sine), {Size = UDim2.new(0, 58, 0, 58)}):Play()
            wait(0.5)
            TweenService:Create(MiniBall, TweenInfo.new(0.5, Enum.EasingStyle.Sine), {Size = UDim2.new(0, 55, 0, 55)}):Play()
            wait(0.5)
        else
            wait(1)
        end
    end
end)

-- ============================================
-- PAINEL PRINCIPAL
-- ============================================
local MainFrame = Instance.new("Frame", Hub)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 22, 30)
MainFrame.BorderSizePixel = 2
MainFrame.BorderColor3 = Color3.fromRGB(60, 120, 255)
MainFrame.Position = UDim2.new(0.5, -300, 0.5, -250)
MainFrame.Size = UDim2.new(0, 600, 0, 500)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = false
MainFrame.BackgroundTransparency = 1
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 15)

-- TitleBar
local TitleBar = Instance.new("Frame", MainFrame)
TitleBar.BackgroundColor3 = Color3.fromRGB(15, 17, 25)
TitleBar.BorderSizePixel = 0
TitleBar.Size = UDim2.new(1, 0, 0, 45)
Instance.new("UICorner", TitleBar).CornerRadius = UDim.new(0, 15)

local TitleLine = Instance.new("Frame", TitleBar)
TitleLine.BackgroundColor3 = Color3.fromRGB(60, 120, 255)
TitleLine.BorderSizePixel = 0
TitleLine.Position = UDim2.new(0, 0, 1, -2)
TitleLine.Size = UDim2.new(1, 0, 0, 2)

local Title = Instance.new("TextLabel", TitleBar)
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 15, 0, 0)
Title.Size = UDim2.new(0.6, 0, 1, 0)
Title.Font = Enum.Font.GothamBold
Title.Text = "JEEP SIK HUB"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 18
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Minimize Button
local MinBtn = Instance.new("TextButton", TitleBar)
MinBtn.BackgroundColor3 = Color3.fromRGB(200, 30, 30)
MinBtn.BorderSizePixel = 0
MinBtn.Position = UDim2.new(0.85, 0, 0, 12)
MinBtn.Size = UDim2.new(0, 22, 0, 22)
MinBtn.Text = "−"
MinBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinBtn.Font = Enum.Font.GothamBold
MinBtn.TextSize = 16
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(0, 6)

MinBtn.MouseEnter:Connect(function()
    TweenService:Create(MinBtn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(230, 50, 50)}):Play()
end)
MinBtn.MouseLeave:Connect(function()
    TweenService:Create(MinBtn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(200, 30, 30)}):Play()
end)

MinBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    MiniBall.Visible = true
    MiniBall.BackgroundTransparency = 0
    notify("Minimizado", "Clique na bolinha JEEP para voltar!", 2)
end)

MiniBall.MouseButton1Click:Connect(function()
    MiniBall.Visible = false
    MainFrame.Visible = true
    TweenService:Create(MainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back), {BackgroundTransparency = 0}):Play()
    notify("Painel Restaurado", "Jeep Sik Hub voltou!", 1.5)
end)

-- Close Button
local CloseBtn = Instance.new("TextButton", TitleBar)
CloseBtn.BackgroundColor3 = Color3.fromRGB(200, 30, 30)
CloseBtn.BorderSizePixel = 0
CloseBtn.Position = UDim2.new(0.93, 0, 0, 12)
CloseBtn.Size = UDim2.new(0, 22, 0, 22)
CloseBtn.Text = "✕"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 14
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 6)

CloseBtn.MouseButton1Click:Connect(function()
    -- Desliga tudo
    getgenv().Settings.ESPEnabled = false
    getgenv().Settings.InfJump = false
    getgenv().Settings.Noclip = false
    getgenv().Settings.FlyEnabled = false
    getgenv().Settings.AntiAfk = false
    getgenv().Settings.AutoFarm = false
    
    for _, box in pairs(getgenv().ESPData.Boxes) do pcall(function() box:Remove() end) end
    for _, t in pairs(getgenv().ESPData.Tracers) do pcall(function() t:Remove() end) end
    for _, n in pairs(getgenv().ESPData.Names) do pcall(function() n:Remove() end) end
    for _, h in pairs(getgenv().ESPData.HealthBars) do pcall(function() h:Remove() end) end
    
    local char = LocalPlayer.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        local root = char:FindFirstChild("HumanoidRootPart")
        if hum then hum.PlatformStand = false end
        if root then
            for _, v in pairs(root:GetChildren()) do
                if v:IsA("BodyGyro") or v:IsA("BodyVelocity") then v:Destroy() end
            end
        end
    end
    
    MiniBall:Destroy()
    Hub:Destroy()
end)

-- Tab Frame
local TabFrame = Instance.new("Frame", MainFrame)
TabFrame.BackgroundColor3 = Color3.fromRGB(15, 17, 25)
TabFrame.BorderSizePixel = 0
TabFrame.Position = UDim2.new(0, 0, 0, 45)
TabFrame.Size = UDim2.new(0, 130, 1, -45)

local TabLine = Instance.new("Frame", TabFrame)
TabLine.BackgroundColor3 = Color3.fromRGB(60, 120, 255)
TabLine.BorderSizePixel = 0
TabLine.Position = UDim2.new(1, -1, 0, 0)
TabLine.Size = UDim2.new(0, 2, 1, 0)

local ContentFrame = Instance.new("Frame", MainFrame)
ContentFrame.BackgroundColor3 = Color3.fromRGB(20, 22, 30)
ContentFrame.BorderSizePixel = 0
ContentFrame.Position = UDim2.new(0, 130, 0, 45)
ContentFrame.Size = UDim2.new(1, -130, 1, -45)

-- ============================================
-- FUNÇÕES DE UI
-- ============================================
local function createTab(name, icon, pos)
    local tab = Instance.new("TextButton", TabFrame)
    tab.BackgroundColor3 = Color3.fromRGB(35, 38, 50)
    tab.BorderSizePixel = 1
    tab.BorderColor3 = Color3.fromRGB(60, 120, 255)
    tab.Position = UDim2.new(0, 8, 0, pos)
    tab.Size = UDim2.new(1, -16, 0, 40)
    tab.Font = Enum.Font.GothamSemibold
    tab.Text = icon .. " " .. name
    tab.TextColor3 = Color3.fromRGB(255, 255, 255)
    tab.TextSize = 13
    Instance.new("UICorner", tab).CornerRadius = UDim.new(0, 8)
    return tab
end

local function createScroll(parent)
    local sf = Instance.new("ScrollingFrame", parent)
    sf.BackgroundTransparency = 1
    sf.BorderSizePixel = 0
    sf.Size = UDim2.new(1, -10, 1, -10)
    sf.Position = UDim2.new(0, 5, 0, 5)
    sf.CanvasSize = UDim2.new(0, 0, 0, 900)
    sf.ScrollBarThickness = 5
    sf.ScrollBarImageColor3 = Color3.fromRGB(60, 120, 255)
    Instance.new("UIListLayout", sf).Padding = UDim.new(0, 10)
    return sf
end

local function createSection(parent, text)
    local lbl = Instance.new("TextLabel", parent)
    lbl.BackgroundColor3 = Color3.fromRGB(60, 120, 255)
    lbl.BorderSizePixel = 0
    lbl.Size = UDim2.new(1, -4, 0, 28)
    lbl.Font = Enum.Font.GothamBold
    lbl.Text = "  " .. text
    lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    Instance.new("UICorner", lbl).CornerRadius = UDim.new(0, 6)
    return lbl
end

local function createToggle(parent, name, desc, default, callback)
    local frame = Instance.new("Frame", parent)
    frame.BackgroundColor3 = Color3.fromRGB(28, 30, 40)
    frame.BorderSizePixel = 1
    frame.BorderColor3 = Color3.fromRGB(60, 120, 255)
    frame.Size = UDim2.new(1, -4, 0, 68)
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)
    
    local lbl = Instance.new("TextLabel", frame)
    lbl.BackgroundTransparency = 1
    lbl.Position = UDim2.new(0, 12, 0, 8)
    lbl.Size = UDim2.new(0.65, 0, 0, 20)
    lbl.Font = Enum.Font.GothamBold
    lbl.Text = name
    lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    
    local dlbl = Instance.new("TextLabel", frame)
    dlbl.BackgroundTransparency = 1
    dlbl.Position = UDim2.new(0, 12, 0, 30)
    dlbl.Size = UDim2.new(0.65, 0, 0, 30)
    dlbl.Font = Enum.Font.Gotham
    dlbl.Text = desc
    dlbl.TextColor3 = Color3.fromRGB(160, 180, 220)
    dlbl.TextSize = 10
    dlbl.TextXAlignment = Enum.TextXAlignment.Left
    dlbl.TextWrapped = true
    
    local btn = Instance.new("TextButton", frame)
    btn.BackgroundColor3 = default and Color3.fromRGB(60, 120, 255) or Color3.fromRGB(50, 52, 60)
    btn.BorderSizePixel = 1
    btn.BorderColor3 = Color3.fromRGB(60, 120, 255)
    btn.Position = UDim2.new(0.82, 0, 0, 20)
    btn.Size = UDim2.new(0, 55, 0, 25)
    btn.Font = Enum.Font.GothamBold
    btn.Text = default and "ON" or "OFF"
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextSize = 11
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 12)
    
    local toggled = default
    btn.MouseButton1Click:Connect(function()
        toggled = not toggled
        btn.BackgroundColor3 = toggled and Color3.fromRGB(60, 120, 255) or Color3.fromRGB(50, 52, 60)
        btn.Text = toggled and "ON" or "OFF"
        notify(name, toggled and "✅ Ativado!" or "❌ Desativado!", 1.5)
        callback(toggled)
    end)
    return frame
end

local function createSlider(parent, name, desc, min, max, default, callback)
    local frame = Instance.new("Frame", parent)
    frame.BackgroundColor3 = Color3.fromRGB(28, 30, 40)
    frame.BorderSizePixel = 1
    frame.BorderColor3 = Color3.fromRGB(60, 120, 255)
    frame.Size = UDim2.new(1, -4, 0, 88)
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)
    
    local lbl = Instance.new("TextLabel", frame)
    lbl.BackgroundTransparency = 1
    lbl.Position = UDim2.new(0, 12, 0, 8)
    lbl.Size = UDim2.new(0.8, 0, 0, 20)
    lbl.Font = Enum.Font.GothamBold
    lbl.Text = name .. ": " .. default
    lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    
    local dlbl = Instance.new("TextLabel", frame)
    dlbl.BackgroundTransparency = 1
    dlbl.Position = UDim2.new(0, 12, 0, 28)
    dlbl.Size = UDim2.new(0.8, 0, 0, 16)
    dlbl.Font = Enum.Font.Gotham
    dlbl.Text = desc
    dlbl.TextColor3 = Color3.fromRGB(160, 180, 220)
    dlbl.TextSize = 9
    dlbl.TextXAlignment = Enum.TextXAlignment.Left
    
    local sliderFrame = Instance.new("Frame", frame)
    sliderFrame.BackgroundColor3 = Color3.fromRGB(40, 42, 55)
    sliderFrame.BorderSizePixel = 1
    sliderFrame.BorderColor3 = Color3.fromRGB(60, 120, 255)
    sliderFrame.Position = UDim2.new(0, 12, 0, 52)
    sliderFrame.Size = UDim2.new(1, -80, 0, 18)
    Instance.new("UICorner", sliderFrame).CornerRadius = UDim.new(0, 9)
    
    local fill = Instance.new("Frame", sliderFrame)
    fill.BackgroundColor3 = Color3.fromRGB(60, 120, 255)
    fill.BorderSizePixel = 0
    fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    Instance.new("UICorner", fill).CornerRadius = UDim.new(0, 9)
    
    local sldBtn = Instance.new("TextButton", sliderFrame)
    sldBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    sldBtn.BorderSizePixel = 2
    sldBtn.BorderColor3 = Color3.fromRGB(60, 120, 255)
    sldBtn.Size = UDim2.new(0, 18, 1, 4)
    sldBtn.Position = UDim2.new((default - min) / (max - min), -9, 0, -2)
    sldBtn.Text = ""
    Instance.new("UICorner", sldBtn).CornerRadius = UDim.new(0, 9)
    
    local valBox = Instance.new("TextBox", frame)
    valBox.BackgroundColor3 = Color3.fromRGB(40, 42, 55)
    valBox.BorderSizePixel = 1
    valBox.BorderColor3 = Color3.fromRGB(60, 120, 255)
    valBox.Position = UDim2.new(0.86, 0, 0, 51)
    valBox.Size = UDim2.new(0, 45, 0, 20)
    valBox.Font = Enum.Font.Gotham
    valBox.Text = tostring(default)
    valBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    valBox.TextSize = 12
    
   
