--[[
    Script: Dark Aura BR 🇧🇷 - RAYFIELD
    KEY: 5609
    Compatível com: Delta Executor
--]]

-- Carregar Rayfield (URLs compatíveis com Delta)
local Rayfield = nil

local RayfieldURLs = {
    "https://raw.githubusercontent.com/shlexware/Rayfield/main/source.lua",
    "https://raw.githubusercontent.com/Versaio/Rayfield/main/source.lua",
    "https://raw.githubusercontent.com/EpixFork/Rayfield/main/source.lua"
}

for _, url in ipairs(RayfieldURLs) do
    local success, result = pcall(function()
        return loadstring(game:HttpGet(url))()
    end)
    if success and result then
        Rayfield = result
        break
    end
end

if not Rayfield then
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Dark Aura BR",
        Text = "Erro ao carregar Rayfield! Tentando método alternativo...",
        Duration = 3
    })
    task.wait(1)
end

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Variáveis
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

-- Função para verificar jogador válido
local function IsValidPlayer(Player)
    if not Player then return false end
    if Player == LocalPlayer then return false end
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
    if OldCircle then
        OldCircle:Destroy()
    end
    
    if not Settings.ShowFOV then return nil end
    
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "FOVCircleGUI"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.Parent = PlayerGui
    
    local CircleFrame = Instance.new("Frame")
    CircleFrame.Name = "FOVCircle"
    CircleFrame.AnchorPoint = Vector2.new(0.5, 0.5)
    CircleFrame.BackgroundTransparency = 1
    CircleFrame.BorderSizePixel = 0
    CircleFrame.Size = UDim2.new(0, Settings.FOVSize * 2, 0, Settings.FOVSize * 2)
    CircleFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
    CircleFrame.Parent = ScreenGui
    
    local UIStroke = Instance.new("UIStroke")
    UIStroke.Thickness = 3
    UIStroke.Color = Color3.fromRGB(138, 43, 226)
    UIStroke.Transparency = 0.3
    UIStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    UIStroke.Parent = CircleFrame
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(1, 0)
    UICorner.Parent = CircleFrame
    
    return CircleFrame
end

local function UpdateFOVCircle()
    if FOVCircle then
        FOVCircle.Size = UDim2.new(0, Settings.FOVSize * 2, 0, Settings.FOVSize * 2)
    end
end

local function ToggleFOVCircle()
    if FOVCircle then
        local parent = FOVCircle.Parent
        if parent then
            parent:Destroy()
        end
        FOVCircle = nil
    end
    if Settings.ShowFOV then
        FOVCircle = CreateFOVCircle()
    end
end

-- Sistema de Highlight
local function UpdateHighlights()
    if not Settings.PlayerHighlight then
        for _, highlight in pairs(Highlights) do
            if highlight then
                highlight:Destroy()
            end
        end
        Highlights = {}
        return
    end
    
    for _, Player in ipairs(Players:GetPlayers()) do
        if IsValidPlayer(Player) and Player.Character then
            if not Highlights[Player.Name] then
                local highlight = Instance.new("Highlight")
                highlight.FillColor = Color3.fromRGB(138, 43, 226)
                highlight.FillTransparency = 0.5
                highlight.OutlineColor = Color3.fromRGB(255, 0, 255)
                highlight.OutlineTransparency = 0.3
                highlight.Parent = Player.Character
                Highlights[Player.Name] = highlight
            end
        elseif Highlights[Player.Name] then
            Highlights[Player.Name]:Destroy()
            Highlights[Player.Name] = nil
        end
    end
end

-- Sistema de Linhas
local function UpdateDirectionLines()
    if not Settings.DirectionLines then
        for _, line in pairs(DirectionLinesObj) do
            if line then
                line:Destroy()
            end
        end
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
                local line = DirectionLinesObj[Player.Name]
                if line then
                    line.PointB = TargetRoot.Position - LocalRoot.Position
                end
            end
        elseif DirectionLinesObj[Player.Name] then
            DirectionLinesObj[Player.Name]:Destroy()
            DirectionLinesObj[Player.Name] = nil
        end
    end
end

-- JANELA DE AUTENTICAÇÃO (Rayfield)
local function CreateAuthWindow()
    local AuthWindow = Rayfield:CreateWindow({
        Name = "Dark Aura BR - Autenticação",
        Icon = 0,
        LoadingTitle = "Verificando Acesso...",
        LoadingSubtitle = "Insira sua key de acesso",
        ConfigurationSaving = {
            Enabled = false
        }
    })
    
    local AuthTab = AuthWindow:CreateTab("Autenticação", nil)
    AuthTab:CreateSection("Login")
    
    AuthTab:CreateParagraph({
        Name = "KeyInfo",
        Content = "KEY CORRETA: 5609\n\nDigite a key abaixo para acessar o sistema."
    })
    
    local KeyInput = AuthTab:CreateInput({
        Name = "Chave de Acesso",
        PlaceholderText = "Digite sua key aqui...",
        RemoveTextAfterFocusLoss = false,
        CurrentValue = "",
        Flag = "KeyInput"
    })
    
    local alreadyAuthenticated = false
    
    AuthTab:CreateButton({
        Name = "Confirmar Acesso",
        Callback = function()
            if alreadyAuthenticated then
                Rayfield:Notify({
                    Title = "Atenção",
                    Content = "Você já está autenticado!",
                    Duration = 2
                })
                return
            end
            
            local EnteredKey = KeyInput.CurrentValue
            
            if EnteredKey == CORRECT_KEY then
                IsAuthenticated = true
                alreadyAuthenticated = true
                
                Rayfield:Notify({
                    Title = "Acesso Liberado!",
                    Content = "Carregando sistema Dark Aura BR...",
                    Duration = 2
                })
                
                task.wait(1)
                AuthWindow:Destroy()
                LoadMainInterface()
            else
                Rayfield:Notify({
                    Title = "Acesso Negado!",
                    Content = "Key inválida! A key correta é: 5609",
                    Duration = 4
                })
                KeyInput:SetValue("")
            end
        end
    })
    
    AuthTab:CreateButton({
        Name = "Esqueci a Key",
        Callback = function()
            Rayfield:Notify({
                Title = "Key Correta",
                Content = "A key de acesso é: 5609",
                Duration = 3
            })
        end
    })
end

-- INTERFACE PRINCIPAL (Rayfield)
local function LoadMainInterface()
    local MainWindow = Rayfield:CreateWindow({
        Name = "Dark Aura BR",
        Icon = 0,
        LoadingTitle = "Carregando Dark Aura...",
        LoadingSubtitle = "Sistema de Visualização Avançado",
        ConfigurationSaving = {
            Enabled = true,
            FolderName = "DarkAuraBR",
            FileName = "Config"
        },
        Discord = {
            Enabled = false
        },
        KeySystem = false
    })
    
    -- Aba FOV
    local FOVTab = MainWindow:CreateTab("Visual FOV", nil)
    FOVTab:CreateSection("Configuração do Círculo FOV")
    
    FOVTab:CreateSlider({
        Name = "Tamanho do Círculo FOV",
        Range = {120, 1000},
        Increment = 10,
        Suffix = "px",
        CurrentValue = 120,
        Flag = "FOVSize",
        Callback = function(Value)
            Settings.FOVSize = Value
            UpdateFOVCircle()
        end
    })
    
    FOVTab:CreateToggle({
        Name = "Mostrar Círculo FOV",
        CurrentValue = true,
        Flag = "ShowFOV",
        Callback = function(Value)
            Settings.ShowFOV = Value
            ToggleFOVCircle()
        end
    })
    
    -- Aba Visual Players
    local VisualTab = MainWindow:CreateTab("Visual Players", nil)
    VisualTab:CreateSection("Destaque de Jogadores")
    
    VisualTab:CreateToggle({
        Name = "Highlight em Jogadores",
        CurrentValue = false,
        Flag = "PlayerHighlight",
        Callback = function(Value)
            Settings.PlayerHighlight = Value
            UpdateHighlights()
        end
    })
    
    VisualTab:CreateParagraph({
        Name = "HighlightInfo",
        Content = "Aplica um contorno roxo nos jogadores visíveis."
    })
    
    -- Aba Linhas
    local DebugTab = MainWindow:CreateTab("Linhas Debug", nil)
    DebugTab:CreateSection("Linhas de Direção")
    
    DebugTab:CreateToggle({
        Name = "Linhas de Direção",
        CurrentValue = false,
        Flag = "DirectionLines",
        Callback = function(Value)
            Settings.DirectionLines = Value
            if not Value then
                for _, line in pairs(DirectionLinesObj) do
                    if line then
                        line:Destroy()
                    end
                end
                DirectionLinesObj = {}
            end
        end
    })
    
    DebugTab:CreateParagraph({
        Name = "LinesInfo",
        Content = "Mostra linhas do seu personagem até os outros jogadores."
    })
    
    -- Aba Informações
    local InfoTab = MainWindow:CreateTab("Informações", nil)
    InfoTab:CreateSection("Sobre")
    
    InfoTab:CreateParagraph({
        Name = "Credits",
        Content = "Dark Aura BR\nVersão: 4.0\n\nSistema Autenticado com Sucesso!\nKey: 5609\n\nDesenvolvido para Delta Executor"
    })
    
    -- Inicializar FOV
    FOVCircle = CreateFOVCircle()
    
    -- Loop principal
    RunService.RenderStepped:Connect(function()
        if IsAuthenticated then
            if Settings.PlayerHighlight then
                UpdateHighlights()
            end
            if Settings.DirectionLines then
                UpdateDirectionLines()
            end
        end
    end)
    
    LocalPlayer.CharacterAdded:Connect(function()
        if IsAuthenticated then
            if Settings.ShowFOV then
                if FOVCircle then
                    local parent = FOVCircle.Parent
                    if parent then
                        parent:Destroy()
                    end
                end
                FOVCircle = CreateFOVCircle()
            end
            for _, line in pairs(DirectionLinesObj) do
                if line then
                    line:Destroy()
                end
            end
            DirectionLinesObj = {}
        end
    end)
    
    Rayfield:Notify({
        Title = "Dark Aura BR",
        Content = "Sistema carregado com sucesso!",
        Duration = 3
    })
    
    print("[Dark Aura BR] Sistema Carregado - Key: 5609")
end

-- Iniciar
local function Start()
    local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
    if Rayfield then
        CreateAuthWindow()
        print("[Dark Aura BR] Aguardando key: 5609")
    else
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "Dark Aura BR",
            Text = "Erro: Rayfield não carregou. Tente executar novamente.",
            Duration = 5
        })
    end
end

Start()
