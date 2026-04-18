--[[
    Script: Dark Aura BR 🇧🇷 - GUI Premium
    Design: Moderno + Animado
    KEY: 5609
    Compatível: Delta Executor
--]]

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Variáveis do Sistema
local IsAuthenticated = false
local CORRECT_KEY = "5609"

local Settings = {
    FOVSize = 120,
    ShowFOV = true,
    PlayerHighlight = false,
    DirectionLines = false
}

local FOVCircle = nil
local Highlights = {}
local DirectionLinesObj = {}

-- ============================================
-- SISTEMA DE NOTIFICAÇÕES
-- ============================================

local function ShowNotification(title, message, type)
    local gui = Instance.new("ScreenGui")
    gui.Name = "Notification"
    gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    gui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    
    local notification = Instance.new("Frame")
    notification.Size = UDim2.new(0, 350, 0, 60)
    notification.Position = UDim2.new(1, 10, 0, 10)
    notification.BackgroundColor3 = type == "success" and Color3.fromRGB(0, 100, 0) or (type == "error" and Color3.fromRGB(100, 0, 0) or Color3.fromRGB(40, 40, 50))
    notification.BackgroundTransparency = 0.1
    notification.BorderSizePixel = 0
    notification.Parent = gui
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = notification
    
    local titleLabel = Instance.new("TextLabel")
    titleLabel.Text = title
    titleLabel.Size = UDim2.new(1, -20, 0, 25)
    titleLabel.Position = UDim2.new(0, 10, 0, 5)
    titleLabel.BackgroundTransparency = 1
    titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    titleLabel.TextSize = 16
    titleLabel.Font = Enum.Font.GothamBold
    titleLabel.TextXAlignment = Enum.TextXAlignment.Left
    titleLabel.Parent = notification
    
    local messageLabel = Instance.new("TextLabel")
    messageLabel.Text = message
    messageLabel.Size = UDim2.new(1, -20, 0, 25)
    messageLabel.Position = UDim2.new(0, 10, 0, 30)
    messageLabel.BackgroundTransparency = 1
    messageLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    messageLabel.TextSize = 12
    messageLabel.Font = Enum.Font.Gotham
    messageLabel.TextXAlignment = Enum.TextXAlignment.Left
    messageLabel.Parent = notification
    
    TweenService:Create(notification, TweenInfo.new(0.5, Enum.EasingStyle.Quad), {Position = UDim2.new(1, -360, 0, 10)}):Play()
    
    task.wait(3)
    TweenService:Create(notification, TweenInfo.new(0.5, Enum.EasingStyle.Quad), {Position = UDim2.new(1, 10, 0, 10)}):Play()
    task.wait(0.5)
    gui:Destroy()
end

-- ============================================
-- JANELA DE AUTENTICAÇÃO
-- ============================================

local function CreateAuthWindow()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "DarkAuraAuth"
    screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    
    -- Fundo escuro com blur
    local background = Instance.new("Frame")
    background.Size = UDim2.new(1, 0, 1, 0)
    background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    background.BackgroundTransparency = 0.5
    background.Parent = screenGui
    
    -- Janela principal
    local window = Instance.new("Frame")
    window.Size = UDim2.new(0, 450, 0, 400)
    window.Position = UDim2.new(0.5, -225, 0.5, -200)
    window.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
    window.BorderSizePixel = 0
    window.ClipsDescendants = true
    window.Parent = screenGui
    
    local windowCorner = Instance.new("UICorner")
    windowCorner.CornerRadius = UDim.new(0, 15)
    windowCorner.Parent = window
    
    -- Efeito de glow na borda
    local glow = Instance.new("Frame")
    glow.Size = UDim2.new(1, 20, 1, 20)
    glow.Position = UDim2.new(-0.02, 0, -0.02, 0)
    glow.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    glow.BackgroundTransparency = 0.8
    glow.BorderSizePixel = 0
    glow.ZIndex = 0
    glow.Parent = window
    
    local glowCorner = Instance.new("UICorner")
    glowCorner.CornerRadius = UDim.new(0, 20)
    glowCorner.Parent = glow
    
    -- Header com gradiente
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 80)
    header.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    header.BorderSizePixel = 0
    header.Parent = window
    
    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 15)
    headerCorner.Parent = header
    
    local title = Instance.new("TextLabel")
    title.Text = "Dark Aura BR 🇧🇷"
    title.Size = UDim2.new(1, 0, 1, 0)
    title.Position = UDim2.new(0, 0, 0, 10)
    title.BackgroundTransparency = 1
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextSize = 28
    title.Font = Enum.Font.GothamBold
    title.Parent = header
    
    local subtitle = Instance.new("TextLabel")
    subtitle.Text = "Sistema de Autenticação"
    subtitle.Size = UDim2.new(1, 0, 1, 0)
    subtitle.Position = UDim2.new(0, 0, 0, 45)
    subtitle.BackgroundTransparency = 1
    subtitle.TextColor3 = Color3.fromRGB(200, 200, 255)
    subtitle.TextSize = 14
    subtitle.Font = Enum.Font.Gotham
    subtitle.Parent = header
    
    -- Container do formulário
    local form = Instance.new("Frame")
    form.Size = UDim2.new(1, -40, 1, -120)
    form.Position = UDim2.new(0, 20, 0, 100)
    form.BackgroundTransparency = 1
    form.Parent = window
    
    -- Label da key
    local keyLabel = Instance.new("TextLabel")
    keyLabel.Text = "🔑 CHAVE DE ACESSO"
    keyLabel.Size = UDim2.new(1, 0, 0, 30)
    keyLabel.Position = UDim2.new(0, 0, 0, 20)
    keyLabel.BackgroundTransparency = 1
    keyLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    keyLabel.TextSize = 14
    keyLabel.Font = Enum.Font.GothamBold
    keyLabel.Parent = form
    
    -- Campo de texto
    local keyInput = Instance.new("TextBox")
    keyInput.Size = UDim2.new(1, 0, 0, 50)
    keyInput.Position = UDim2.new(0, 0, 0, 55)
    keyInput.PlaceholderText = "Digite sua key aqui..."
    keyInput.PlaceholderColor3 = Color3.fromRGB(100, 100, 100)
    keyInput.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    keyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
    keyInput.TextSize = 16
    keyInput.Font = Enum.Font.Gotham
    keyInput.ClearTextOnFocus = false
    keyInput.Parent = form
    
    local inputCorner = Instance.new("UICorner")
    inputCorner.CornerRadius = UDim.new(0, 10)
    inputCorner.Parent = keyInput
    
    local inputStroke = Instance.new("UIStroke")
    inputStroke.Thickness = 1
    inputStroke.Color = Color3.fromRGB(138, 43, 226)
    inputStroke.Parent = keyInput
    
    -- Botão confirmar
    local confirmBtn = Instance.new("TextButton")
    confirmBtn.Text = "🔓 CONFIRMAR ACESSO"
    confirmBtn.Size = UDim2.new(1, 0, 0, 50)
    confirmBtn.Position = UDim2.new(0, 0, 0, 130)
    confirmBtn.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    confirmBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    confirmBtn.TextSize = 16
    confirmBtn.Font = Enum.Font.GothamBold
    confirmBtn.BorderSizePixel = 0
    confirmBtn.Parent = form
    
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 10)
    btnCorner.Parent = confirmBtn
    
    -- Info da key
    local infoText = Instance.new("TextLabel")
    infoText.Text = "💡 Key correta: 5609"
    infoText.Size = UDim2.new(1, 0, 0, 30)
    infoText.Position = UDim2.new(0, 0, 0, 200)
    infoText.BackgroundTransparency = 1
    infoText.TextColor3 = Color3.fromRGB(100, 100, 150)
    infoText.TextSize = 12
    infoText.Font = Enum.Font.Gotham
    infoText.Parent = form
    
    -- Animação de entrada
    window.BackgroundTransparency = 1
    TweenService:Create(window, TweenInfo.new(0.5, Enum.EasingStyle.Quad), {BackgroundTransparency = 0}):Play()
    
    -- Função do botão
    confirmBtn.MouseButton1Click:Connect(function()
        local enteredKey = keyInput.Text
        
        if enteredKey == CORRECT_KEY then
            ShowNotification("✅ ACESSO LIBERADO", "Bem-vindo ao Dark Aura BR!", "success")
            IsAuthenticated = true
            
            TweenService:Create(window, TweenInfo.new(0.5, Enum.EasingStyle.Quad), {Position = UDim2.new(0.5, -225, 0.5, -250)}):Play()
            TweenService:Create(window, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {BackgroundTransparency = 1}):Play()
            
            task.wait(0.8)
            screenGui:Destroy()
            CreateMainInterface()
        else
            ShowNotification("❌ ACESSO NEGADO", "Key inválida! A key correta é: 5609", "error")
            keyInput.Text = ""
            
            -- Efeito de tremor
            local originalPos = window.Position
            for i = 1, 3 do
                TweenService:Create(window, TweenInfo.new(0.05), {Position = UDim2.new(0.5, -225 + (i % 2 == 0 and -5 or 5), 0.5, -200)}):Play()
                task.wait(0.05)
            end
            TweenService:Create(window, TweenInfo.new(0.05), {Position = originalPos}):Play()
        end
    end)
    
    -- Foco no input
    keyInput:CaptureFocus()
end

-- ============================================
-- FUNÇÕES DO SISTEMA
-- ============================================

local function IsValidPlayer(Player)
    if not Player or Player == LocalPlayer then return false end
    local Character = Player.Character
    if not Character then return false end
    local Humanoid = Character:FindFirstChild("Humanoid")
    if not Humanoid or Humanoid.Health <= 0 then return false end
    return true
end

-- FOV Circle
local function CreateFOVCircle()
    local PlayerGui = LocalPlayer:FindFirstChild("PlayerGui")
    if not PlayerGui then
        PlayerGui = Instance.new("ScreenGui")
        PlayerGui.Name = "PlayerGui"
        PlayerGui.Parent = LocalPlayer
    end
    
    local OldCircle = PlayerGui:FindFirstChild("FOVCircleGUI")
    if OldCircle then OldCircle:Destroy() end
    if not Settings.ShowFOV then return nil end
    
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "FOVCircleGUI"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.Parent = PlayerGui
    
    local CircleFrame = Instance.new("Frame")
    CircleFrame.Name = "FOVCircle"
    CircleFrame.AnchorPoint = Vector2.new(0.5, 0.5)
    CircleFrame.BackgroundTransparency = 1
    CircleFrame.Size = UDim2.new(0, Settings.FOVSize * 2, 0, Settings.FOVSize * 2)
    CircleFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
    CircleFrame.Parent = ScreenGui
    
    local UIStroke = Instance.new("UIStroke")
    UIStroke.Thickness = 3
    UIStroke.Color = Color3.fromRGB(138, 43, 226)
    UIStroke.Transparency = 0.4
    UIStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    UIStroke.Parent = CircleFrame
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(1, 0)
    UICorner.Parent = CircleFrame
    
    -- Efeito pulsante
    task.spawn(function()
        while CircleFrame and CircleFrame.Parent do
            for i = 0, 1, 0.05 do
                if not CircleFrame or not CircleFrame.Parent then break end
                local transparency = 0.3 + math.sin(tick() * 3) * 0.1
                UIStroke.Transparency = transparency
                task.wait(0.05)
            end
        end
    end)
    
    return CircleFrame
end

local function UpdateFOVCircle()
    if FOVCircle then
        FOVCircle.Size = UDim2.new(0, Settings.FOVSize * 2, 0, Settings.FOVSize * 2)
    end
end

-- Highlight
local function UpdateHighlights()
    if not Settings.PlayerHighlight then
        for _, h in pairs(Highlights) do if h then h:Destroy() end end
        Highlights = {}
        return
    end
    
    for _, Player in ipairs(Players:GetPlayers()) do
        if IsValidPlayer(Player) and Player.Character then
            if not Highlights[Player.Name] then
                local h = Instance.new("Highlight")
                h.FillColor = Color3.fromRGB(138, 43, 226)
                h.FillTransparency = 0.5
                h.OutlineColor = Color3.fromRGB(255, 0, 255)
                h.OutlineTransparency = 0.3
                h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                h.Parent = Player.Character
                Highlights[Player.Name] = h
            end
        elseif Highlights[Player.Name] then
            Highlights[Player.Name]:Destroy()
            Highlights[Player.Name] = nil
        end
    end
end

-- Linhas
local function UpdateDirectionLines()
    if not Settings.DirectionLines then
        for _, l in pairs(DirectionLinesObj) do if l then l:Destroy() end end
        DirectionLinesObj = {}
        return
    end
    
    local LocalChar = LocalPlayer.Character
    if not LocalChar then return end
    local LocalRoot = LocalChar:FindFirstChild("HumanoidRootPart")
    if not LocalRoot then return end
    
    for _, Player in ipairs(Players:GetPlayers()) do
        if IsValidPlayer(Player) and Player.Character then
            local TargetRoot = Player.Character:FindFirstChild("HumanoidRootPart")
            if TargetRoot then
                if not DirectionLinesObj[Player.Name] then
                    local line = Instance.new("LineHandleAdornment")
                    line.Adornee = LocalRoot
                    line.PointA = Vector3.new(0, 1, 0)
                    line.Color3 = Color3.fromRGB(0, 255, 255)
                    line.Thickness = 2
                    line.Transparency = 0.5
                    line.AlwaysOnTop = true
                    line.Parent = LocalChar
                    DirectionLinesObj[Player.Name] = line
                end
                if DirectionLinesObj[Player.Name] then
                    DirectionLinesObj[Player.Name].PointB = TargetRoot.Position - LocalRoot.Position
                end
            end
        elseif DirectionLinesObj[Player.Name] then
            DirectionLinesObj[Player.Name]:Destroy()
            DirectionLinesObj[Player.Name] = nil
        end
    end
end

-- ============================================
-- INTERFACE PRINCIPAL (MAIS BONITA QUE RAYFIELD)
-- ============================================

local function CreateMainInterface()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "DarkAuraMain"
    screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    
    -- Janela principal
    local window = Instance.new("Frame")
    window.Size = UDim2.new(0, 500, 0, 600)
    window.Position = UDim2.new(0.5, -250, 0.5, -300)
    window.BackgroundColor3 = Color3.fromRGB(18, 18, 25)
    window.BorderSizePixel = 0
    window.ClipsDescendants = true
    window.Parent = screenGui
    
    local windowCorner = Instance.new("UICorner")
    windowCorner.CornerRadius = UDim.new(0, 15)
    windowCorner.Parent = window
    
    -- Header
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 70)
    header.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    header.BorderSizePixel = 0
    header.Parent = window
    
    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 15)
    headerCorner.Parent = header
    
    -- Título
    local title = Instance.new("TextLabel")
    title.Text = "Dark Aura BR 🇧🇷"
    title.Size = UDim2.new(1, -100, 1, 0)
    title.Position = UDim2.new(0, 20, 0, 0)
    title.BackgroundTransparency = 1
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextSize = 22
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.Font = Enum.Font.GothamBold
    title.Parent = header
    
    -- Botão fechar
    local closeBtn = Instance.new("TextButton")
    closeBtn.Text = "✕"
    closeBtn.Size = UDim2.new(0, 35, 0, 35)
    closeBtn.Position = UDim2.new(1, -45, 0, 17)
    closeBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
    closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeBtn.TextSize = 20
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.BorderSizePixel = 0
    closeBtn.Parent = header
    
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = closeBtn
    
    closeBtn.MouseButton1Click:Connect(function()
        TweenService:Create(window, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {Position = UDim2.new(0.5, -250, 0.5, -350)}):Play()
        TweenService:Create(window, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {BackgroundTransparency = 1}):Play()
        task.wait(0.3)
        screenGui:Destroy()
    end)
    
    -- Botão minimizar
    local minBtn = Instance.new("TextButton")
    minBtn.Text = "−"
    minBtn.Size = UDim2.new(0, 35, 0, 35)
    minBtn.Position = UDim2.new(1, -90, 0, 17)
    minBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 90)
    minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    minBtn.TextSize = 24
    minBtn.Font = Enum.Font.GothamBold
    minBtn.BorderSizePixel = 0
    minBtn.Parent = header
    
    local minCorner = Instance.new("UICorner")
    minCorner.CornerRadius = UDim.new(0, 8)
    minCorner.Parent = minBtn
    
    local minimized = false
    local originalHeight = 600
    minBtn.MouseButton1Click:Connect(function()
        minimized = not minimized
        TweenService:Create(window, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {Size = minimized and UDim2.new(0, 500, 0, 70) or UDim2.new(0, 500, 0, originalHeight)}):Play()
    end)
    
    -- Container de abas
    local tabContainer = Instance.new("Frame")
    tabContainer.Size = UDim2.new(1, 0, 0, 50)
    tabContainer.Position = UDim2.new(0, 0, 0, 70)
    tabContainer.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    tabContainer.BackgroundTransparency = 0.5
    tabContainer.Parent = window
    
    -- Conteúdo das abas
    local contentContainer = Instance.new("Frame")
    contentContainer.Size = UDim2.new(1, -40, 1, -140)
    contentContainer.Position = UDim2.new(0, 20, 0, 130)
    contentContainer.BackgroundTransparency = 1
    contentContainer.Parent = window
    
    -- ScrollFrame para conteúdo
    local scrollFrame = Instance.new("ScrollingFrame")
    scrollFrame.Size = UDim2.new(1, 0, 1, 0)
    scrollFrame.BackgroundTransparency = 1
    scrollFrame.CanvasSize = UDim2.new(0, 0, 0, 400)
    scrollFrame.ScrollBarThickness = 5
    scrollFrame.ScrollBarImageColor3 = Color3.fromRGB(138, 43, 226)
    scrollFrame.Parent = contentContainer
    
    local scrollCorner = Instance.new("UICorner")
    scrollCorner.CornerRadius = UDim.new(0, 10)
    scrollCorner.Parent = scrollFrame
    
    -- Criar abas
    local tabs = {}
    local currentTab = nil
    
    local function CreateTab(name, icon)
        local tabBtn = Instance.new("TextButton")
        tabBtn.Text = icon .. " " .. name
        tabBtn.Size = UDim2.new(0.25, 0, 1, 0)
        tabBtn.Position = UDim2.new((#tabs) * 0.25, 0, 0, 0)
        tabBtn.BackgroundColor3 = #tabs == 0 and Color3.fromRGB(138, 43, 226) or Color3.fromRGB(30, 30, 40)
        tabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
        tabBtn.TextSize = 14
        tabBtn.Font = Enum.Font.GothamBold
        tabBtn.BorderSizePixel = 0
        tabBtn.Parent = tabContainer
        
        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 8)
        btnCorner.Parent = tabBtn
        
        local content = Instance.new("Frame")
        content.Size = UDim2.new(1, 0, 1, 0)
        content.BackgroundTransparency = 1
        content.Visible = #tabs == 0
        content.Parent = scrollFrame
        
        tabs[#tabs + 1] = {btn = tabBtn, content = content}
        
        tabBtn.MouseButton1Click:Connect(function()
            for _, tab in pairs(tabs) do
                tab.btn.BackgroundCo
