--======================================
-- SCRIPT UNIVERSAL PC
-- Título: Script Universal PC
-- Organizado em Abas com Cameraman Corrigido
--======================================

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local VirtualUser = game:GetService("VirtualUser")

local Player = Players.LocalPlayer
local Camera = workspace.CurrentCamera

--======================================
-- VARIÁVEIS GLOBAIS
--======================================
local flyOn = false
local flySpeed = 50
local flyY = 0
local bv, bg

local speedOn = false
local speedValue = 16
local speedOptions = {16, 30, 50, 75, 100, 150, 200}

local noclipOn = false
local spinOn = false

-- Variáveis para Cameraman (CORRIGIDO)
local camManOn = false
local camManConnection = nil
local mouseSensitivity = 0.5
local camYaw = 0
local camPitch = 0

local espOn = false
local invisOn = false
local espObjs = {}

local savedCF = CFrame.new()
local SelectedPlayer = nil

--======================================
-- CORES
--======================================
local COLOR_OFF = Color3.fromRGB(60,60,60)
local COLOR_ON = Color3.fromRGB(0,150,255)
local COLOR_HOVER = Color3.fromRGB(80,80,80)
local COLOR_BG = Color3.fromRGB(25,25,25)
local COLOR_BAR = Color3.fromRGB(35,35,35)

--======================================
-- FUNÇÕES ÚTEIS
--======================================
local function setMouseLocked(locked)
    if locked then
        UIS.MouseIconEnabled = false
        UIS.MouseBehavior = Enum.MouseBehavior.LockCurrentPosition
    else
        UIS.MouseIconEnabled = true
        UIS.MouseBehavior = Enum.MouseBehavior.Default
    end
end

local function TeamColor(p)
    if p.Team and p.Team.TeamColor then 
        return p.Team.TeamColor.Color 
    end
    return Color3.fromRGB(255,0,0)
end

local function PlayClick()
    -- Som de clique (opcional)
end

local function AnimateButton(btn, state)
    TweenService:Create(
        btn,
        TweenInfo.new(0.2),
        {BackgroundColor3 = state and COLOR_ON or COLOR_OFF}
    ):Play()
end

--======================================
-- ATUALIZAR CARACTER
--======================================
local Char = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Char:WaitForChild("Humanoid")
local HRP = Char:WaitForChild("HumanoidRootPart")
savedCF = HRP.CFrame

Player.CharacterAdded:Connect(function(c)
    Char = c
    Humanoid = c:WaitForChild("Humanoid")
    HRP = c:WaitForChild("HumanoidRootPart")
end)

--======================================
-- ANTI AFK
--======================================
Player.Idled:Connect(function()
    VirtualUser:Button2Down(Vector2.new(0,0), Camera.CFrame)
    task.wait(1)
    VirtualUser:Button2Up(Vector2.new(0,0), Camera.CFrame)
end)

--======================================
-- CRIAR GUI PRINCIPAL
--======================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "UniversalPC"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local success = pcall(function()
    ScreenGui.Parent = game:GetService("CoreGui")
end)

if not success then
    ScreenGui.Parent = Player:WaitForChild("PlayerGui")
end

--======================================
-- BOTÃO FLUTUANTE
--======================================
local ToggleBtn = Instance.new("TextButton", ScreenGui)
ToggleBtn.Size = UDim2.new(0, 55, 0, 55)
ToggleBtn.Position = UDim2.new(0, 15, 0.5, -27.5)
ToggleBtn.Text = "⚡"
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.TextSize = 28
ToggleBtn.TextColor3 = Color3.new(1,1,1)
ToggleBtn.BackgroundColor3 = COLOR_BG
ToggleBtn.BorderSizePixel = 0
ToggleBtn.Draggable = true

local btnCorner = Instance.new("UICorner", ToggleBtn)
btnCorner.CornerRadius = UDim.new(1,0)

-- Tooltip
local tooltip = Instance.new("TextLabel", ScreenGui)
tooltip.Size = UDim2.new(0, 140, 0, 22)
tooltip.Position = UDim2.new(0, 75, 0.5, -11)
tooltip.BackgroundColor3 = Color3.fromRGB(0,0,0)
tooltip.BackgroundTransparency = 0.3
tooltip.Text = "F4 | Shift+P"
tooltip.TextColor3 = Color3.new(1,1,1)
tooltip.Font = Enum.Font.Gotham
tooltip.TextSize = 12
Instance.new("UICorner", tooltip).CornerRadius = UDim.new(0, 4)

--======================================
-- MENU PRINCIPAL
--======================================
local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 500, 0, 500)
Frame.Position = UDim2.new(0.5, -250, 0.5, -250)
Frame.BackgroundColor3 = COLOR_BG
Frame.BackgroundTransparency = 0.05
Frame.BorderSizePixel = 0
Frame.Draggable = true
Frame.Visible = false
Frame.Active = true
Frame.ClipsDescendants = true

local frameCorner = Instance.new("UICorner", Frame)
frameCorner.CornerRadius = UDim.new(0, 10)

-- Sombra
local shadow = Instance.new("ImageLabel", Frame)
shadow.Size = UDim2.new(1, 20, 1, 20)
shadow.Position = UDim2.new(0, -10, 0, -10)
shadow.BackgroundTransparency = 1
shadow.Image = "rbxasset://textures/ui/GuiImagePlaceholder.png"
shadow.ImageColor3 = Color3.fromRGB(0,0,0)
shadow.ImageTransparency = 0.5
shadow.ScaleType = Enum.ScaleType.Slice
shadow.SliceCenter = Rect.new(10,10,10,10)
shadow.ZIndex = -1

--======================================
-- BARRA DE TÍTULO
--======================================
local TitleBar = Instance.new("Frame", Frame)
TitleBar.Size = UDim2.new(1,0,0,45)
TitleBar.BackgroundColor3 = COLOR_BAR
TitleBar.BorderSizePixel = 0

local titleCorner = Instance.new("UICorner", TitleBar)
titleCorner.CornerRadius = UDim.new(0, 10)

local Title = Instance.new("TextLabel", TitleBar)
Title.Size = UDim2.new(1, -80, 1, 0)
Title.Position = UDim2.new(0, 15, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "⚡ UNIVERSAL PC"
Title.TextColor3 = Color3.new(1,1,1)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Botão Minimizar
local MinBtn = Instance.new("TextButton", TitleBar)
MinBtn.Size = UDim2.new(0, 40, 0, 40)
MinBtn.Position = UDim2.new(1, -90, 0, 2.5)
MinBtn.Text = "−"
MinBtn.Font = Enum.Font.GothamBold
MinBtn.TextSize = 24
MinBtn.TextColor3 = Color3.new(1,1,1)
MinBtn.BackgroundTransparency = 1

-- Botão Fechar
local CloseBtn = Instance.new("TextButton", TitleBar)
CloseBtn.Size = UDim2.new(0, 40, 0, 40)
CloseBtn.Position = UDim2.new(1, -45, 0, 2.5)
CloseBtn.Text = "✕"
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 20
CloseBtn.TextColor3 = Color3.fromRGB(255,80,80)
CloseBtn.BackgroundTransparency = 1

local minimized = false
MinBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    Frame.Size = minimized and UDim2.new(0, 500, 0, 45) or UDim2.new(0, 500, 0, 500)
end)

CloseBtn.MouseButton1Click:Connect(function()
    Frame.Visible = false
end)

--======================================
-- ATALHOS TECLADO
--======================================
UIS.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.F4 then
        Frame.Visible = not Frame.Visible
        tooltip.Visible = false
    end
    
    if input.KeyCode == Enum.KeyCode.P and UIS:IsKeyDown(Enum.KeyCode.LeftShift) then
        toggleCameraman()
    end
end)

ToggleBtn.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
    tooltip.Visible = false
end)

--======================================
-- SISTEMA DE ABAS
--======================================
local TabFrame = Instance.new("Frame", Frame)
TabFrame.Size = UDim2.new(1, -20, 0, 45)
TabFrame.Position = UDim2.new(0, 10, 0, 50)
TabFrame.BackgroundTransparency = 1

local function CriarAba(nome, pos)
    local btn = Instance.new("TextButton", TabFrame)
    btn.Size = UDim2.new(0.25, -5, 1, -5)
    btn.Position = UDim2.new(pos, 0, 0, 0)
    btn.Text = nome
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 14
    btn.TextColor3 = Color3.new(1,1,1)
    btn.BackgroundColor3 = COLOR_BAR
    btn.BorderSizePixel = 0
    
    local btnCorner = Instance.new("UICorner", btn)
    btnCorner.CornerRadius = UDim.new(0, 6)
    
    return btn
end

local AbaMovimento = CriarAba("🏃 MOVIMENTO", 0)
local AbaCamera = CriarAba("📷 CÂMERA", 0.25)
local AbaVisual = CriarAba("👁️ VISUAL", 0.5)
local AbaTeleport = CriarAba("🚀 TELEPORT", 0.75)

-- Containers das abas
local function CriarContainer()
    local container = Instance.new("ScrollingFrame", Frame)
    container.Size = UDim2.new(1, -20, 1, -140)
    container.Position = UDim2.new(0, 10, 0, 100)
    container.BackgroundTransparency = 1
    container.BorderSizePixel = 0
    container.ScrollBarThickness = 8
    container.ScrollBarImageColor3 = Color3.fromRGB(100,100,100)
    container.AutomaticCanvasSize = Enum.AutomaticSize.Y
    container.Visible = false
    
    local layout = Instance.new("UIListLayout", container)
    layout.Padding = UDim.new(0, 8)
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    
    return container
end

local ContainerMov = CriarContainer()
local ContainerCam = CriarContainer()
local ContainerVis = CriarContainer()
local ContainerTP = CriarContainer()

-- Função para trocar de aba
local function TrocarAba(aba)
    ContainerMov.Visible = false
    ContainerCam.Visible = false
    ContainerVis.Visible = false
    ContainerTP.Visible = false
    
    -- Resetar cores das abas
    AbaMovimento.BackgroundColor3 = COLOR_BAR
    AbaCamera.BackgroundColor3 = COLOR_BAR
    AbaVisual.BackgroundColor3 = COLOR_BAR
    AbaTeleport.BackgroundColor3 = COLOR_BAR
    
    if aba == "mov" then
        ContainerMov.Visible = true
        AbaMovimento.BackgroundColor3 = COLOR_ON
    elseif aba == "cam" then
        ContainerCam.Visible = true
        AbaCamera.BackgroundColor3 = COLOR_ON
    elseif aba == "vis" then
        ContainerVis.Visible = true
        AbaVisual.BackgroundColor3 = COLOR_ON
    elseif aba == "tp" then
        ContainerTP.Visible = true
        AbaTeleport.BackgroundColor3 = COLOR_ON
    end
end

AbaMovimento.MouseButton1Click:Connect(function() TrocarAba("mov") end)
AbaCamera.MouseButton1Click:Connect(function() TrocarAba("cam") end)
AbaVisual.MouseButton1Click:Connect(function() TrocarAba("vis") end)
AbaTeleport.MouseButton1Click:Connect(function() TrocarAba("tp") end)

TrocarAba("mov") -- Aba inicial

--======================================
-- FUNÇÃO PARA CRIAR BOTÕES
--======================================
local function CriarBotao(container, texto, callback)
    local btn = Instance.new("TextButton", container)
    btn.Size = UDim2.new(1, -10, 0, 40)
    btn.Text = texto
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 14
    btn.TextColor3 = Color3.new(1,1,1)
    btn.BackgroundColor3 = COLOR_OFF
    btn.BorderSizePixel = 0
    
    local btnCorner = Instance.new("UICorner", btn)
    btnCorner.CornerRadius = UDim.new(0, 6)
    
    btn.MouseButton1Click:Connect(function()
        if callback then callback(btn) end
    end)
    
    btn.MouseEnter:Connect(function()
        if btn.BackgroundColor3 ~= COLOR_ON then
            TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = COLOR_HOVER}):Play()
        end
    end)
    
    btn.MouseLeave:Connect(function()
        if btn.BackgroundColor3 ~= COLOR_ON then
            TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = COLOR_OFF}):Play()
        end
    end)
    
    return btn
end

local function CriarLinha(container, botoes)
    local linha = Instance.new("Frame", container)
    linha.Size = UDim2.new(1, -10, 0, 40)
    linha.BackgroundTransparency = 1
    
    local qtd = #botoes
    local largura = 1 / qtd
    
    for i, dados in ipairs(botoes) do
        local btn = Instance.new("TextButton", linha)
        btn.Size = UDim2.new(largura, -4, 1, 0)
        btn.Position = UDim2.new((i-1) * largura, 2, 0, 0)
        btn.Text = dados.texto
        btn.Font = Enum.Font.GothamBold
        btn.TextSize = 13
        btn.TextColor3 = Color3.new(1,1,1)
        btn.BackgroundColor3 = dados.cor or COLOR_OFF
        btn.BorderSizePixel = 0
        
        local btnCorner = Instance.new("UICorner", btn)
        btnCorner.CornerRadius = UDim.new(0, 6)
        
        btn.MouseButton1Click:Connect(function()
            if dados.callback then dados.callback(btn) end
        end)
        
        btn.MouseEnter:Connect(function()
            if btn.BackgroundColor3 ~= COLOR_ON then
                TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = COLOR_HOVER}):Play()
            end
        end)
        
        btn.MouseLeave:Connect(function()
            if btn.BackgroundColor3 ~= COLOR_ON then
                TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = dados.cor or COLOR_OFF}):Play()
            end
        end)
    end
    
    return linha
end

local function CriarTitulo(container, texto)
    local titulo = Instance.new("TextLabel", container)
    titulo.Size = UDim2.new(1, -10, 0, 20)
    titulo.BackgroundTransparency = 1
    titulo.Text = texto
    titulo.TextColor3 = Color3.fromRGB(200,200,200)
    titulo.Font = Enum.Font.GothamBold
    titulo.TextSize = 12
    titulo.TextXAlignment = Enum.TextXAlignment.Left
    return titulo
end

--======================================
-- ABA MOVIMENTO
--======================================
CriarTitulo(ContainerMov, "🕊️ FLY")

CriarLinha(ContainerMov, {
    {
        texto = "🕊️ FLY",
        callback = function(btn)
            flyOn = not flyOn
            AnimateButton(btn, flyOn)
            if flyOn then
                bv = Instance.new("BodyVelocity", HRP)
                bv.MaxForce = Vector3.new(1e9,1e9,1e9)
                
                bg = Instance.new("BodyGyro", HRP)
                bg.MaxTorque = Vector3.new(1e9,1e9,1e9)
                bg.P = 1e4
            else
                if bv then bv:Destroy() end
                if bg then bg:Destroy() end
                flyY = 0
            end
        end
    },
    {
        texto = "⬆ SUBIR",
        callback = function(btn)
            flyY = 1
            AnimateButton(btn, true)
            task.wait(0.2)
            AnimateButton(btn, false)
        end
    },
    {
        texto = "⬇ DESCER",
        callback = function(btn)
            flyY = -1
            AnimateButton(btn, true)
            task.wait(0.2)
            AnimateButton(btn, false)
        end
    }
})

CriarTitulo(ContainerMov, "⚡ SPEED")

CriarLinha(ContainerMov, {
    {
        texto = "⚪ SPEED OFF",
        callback = function(btn)
            speedOn = not speedOn
            AnimateButton(btn, speedOn)
            btn.Text = speedOn and "🔴 SPEED ON" or "⚪ SPEED OFF"
            Humanoid.WalkSpeed = speedOn and speedValue or 16
        end
    },
    {
        texto = "⚡ " .. speedValue,
        callback = function(btn)
            local idx = 1
            for i, v in ipairs(speedOptions) do
                if v == speedValue then idx = i break end
            end
            idx = idx + 1
            if idx > #speedOptions then idx = 1 end
            speedValue = speedOptions[idx]
            btn.Text = "⚡ " .. speedValue
            if speedOn then Humanoid.WalkSpeed = speedValue end
        end
    }
})

CriarTitulo(ContainerMov, "🎮 OUTROS")

CriarLinha(ContainerMov, {
    {
        texto = "🚪 NOCLIP",
        callback = function(btn)
            noclipOn = not noclipOn
            AnimateButton(btn, noclipOn)
        end
    },
    {
        texto = "🌀 SPIN",
        callback = function(btn)
            spinOn = not spinOn
            AnimateButton(btn, spinOn)
        end
    }
})

--======================================
-- ABA CÂMERA (CAMERAMAN CORRIGIDO)
--======================================
CriarTitulo(ContainerCam, "📷 CAMERAMAN")

-- Botão principal do Cameraman
local camBtn = CriarBotao(ContainerCam, "🎥 ATIVAR CAMERAMAN (Shift+P)", function(btn)
    toggleCameraman()
end)

-- Botão Sensibilidade
CriarLinha(ContainerCam, {
    {
        texto = "🐭 Sensibilidade: Média",
        callback = function(btn)
            if mouseSensitivity == 0.3 then
                mouseSensitivity = 0.5
                btn.Text = "🐭 Sensibilidade: Média"
            elseif mouseSensitivity == 0.5 then
                mouseSensitivity = 1.0
                btn.Text = "🐭 Sensibilidade: Alta"
            else
                mouseSensitivity = 0.3
                btn.Text = "🐭 Sensibilidade: Baixa"
            end
        end
    }
})

-- Instruções
local instrucoes = Instance.new("TextLabel", ContainerCam)
instrucoes.Size = UDim2.new(1, -10, 0, 60)
instrucoes.BackgroundTransparency = 1
instrucoes.Text = "🎥 CAMERAMAN (TERCEIRA PESSOA):\n\nMouse: Rotacionar câmera ao redor do personagem\nWASD: Mover o personagem"
instrucoes.TextColor3 = Color3.fromRGB(150,150,150)
instrucoes.Font = Enum.Font.Gotham
instrucoes.TextSize = 12
instrucoes.TextWrapped = true
instrucoes.LineHeight = 1.5

--======================================
-- FUNÇÃO TOGGLE CAMERAMAN (CORRIGIDA)
--======================================
function toggleCameraman()
    camManOn = not camManOn
    
    if camBtn and camBtn.Parent then
        camBtn.BackgroundColor3 = camManOn and COLOR_ON or COLOR_OFF
        camBtn.Text = camManOn and "🎥 CAMERAMAN ATIVO (Shift+P)" or "🎥 ATIVAR CAMERAMAN (Shift+P)"
    end
    
    if camManOn then
        -- Configurar câmera
        Camera.CameraType = Enum.CameraType.Scriptable
        setMouseLocked(true)
        
        -- Resetar ângulos da câmera
        camYaw = 0
        camPitch = 0
        
        if camManConnection then camManConnection:Disconnect() end
        
        camManConnection = RunService.RenderStepped:Connect(function()
            if camManOn and HRP and HRP.Parent then
                -- Pegar movimento do mouse
                local delta = UIS:GetMouseDelta()
                
                -- Atualizar ângulos baseado no mouse
                camYaw = camYaw - delta.X * 0.003 * mouseSensitivity
                camPitch = camPitch - delta.Y * 0.003 * mouseSensitivity
                camPitch = math.clamp(camPitch, -math.rad(70), math.rad(70)) -- Limitar para não virar de cabeça pra baixo
                
                -- Distância da câmera em relação ao personagem
                local distance = 8
                local height = 2.5
                
                -- Posição do personagem
                local charPos = HRP.Position
                
                -- Calcular offset baseado na rotação
                local offset = Vector3.new(
                    -math.sin(camYaw) * math.cos(camPitch) * distance,
                    height + math.sin(camPitch) * distance,
                    -math.cos(camYaw) * math.cos(camPitch) * distance
                )
                
                -- Posição da câmera
                local camPos = charPos + offset
                
                -- Ponto para onde a câmera olha (personagem)
                local lookAt = charPos + Vector3.new(0, height, 0)
                
                -- Aplicar à câmera
                Camera.CFrame = CFrame.new(camPos, lookAt)
            end
        end)
    else
        -- Restaurar câmera normal
        Camera.CameraType = Enum.CameraType.Custom
        setMouseLocked(false)
        
        if camManConnection then
            camManConnection:Disconnect()
            camManConnection = nil
        end
    end
end

--======================================
-- ABA VISUAL
--======================================
CriarTitulo(ContainerVis, "👁️ EFEITOS")

CriarLinha(ContainerVis, {
    {
        texto = "👁️ ESP",
        callback = function(btn)
            espOn = not espOn
            AnimateButton(btn, espOn)
            
            for _, h in pairs(espObjs) do
                if h then pcall(function() h:Destroy() end) end
            end
            espObjs = {}
            
            if espOn then
                for _, p in pairs(Players:GetPlayers()) do
                    if p ~= Player and p.Character then
                        local h = Instance.new("Highlight", p.Character)
                        h.FillColor = TeamColor(p)
                        h.FillTransparency = 0.5
                        h.OutlineColor = Color3.new(1,1,1)
                        espObjs[p] = h
                    end
                end
            end
        end
    },
    {
        texto = "👻 INVISÍVEL",
        callback = function(btn)
            invisOn = not invisOn
            AnimateButton(btn, invisOn)
            for _, v in pairs(Char:GetDescendants()) do
                if v:IsA("BasePart") or v:IsA("Decal") or v:IsA("MeshPart") then
                    v.Transparency = invisOn and 1 or 0
                end
            end
        end
    }
})

--======================================
-- ABA TELEPORT
--======================================
CriarTitulo(ContainerTP, "📍 TELEPORT LOCAL")

CriarBotao(ContainerTP, "📍 SALVAR/TELEPORTAR", function(btn)
    if not HRP then return end
    
    if (HRP.Position - savedCF.Position).Magnitude > 5 then
        savedCF = HRP.CFrame
        btn.Text = "📍 Salvo!"
        btn.BackgroundColor3 = COLOR_ON
        task.wait(1)
        btn.Text = "📍 SALVAR/TELEPORTAR"
        btn.BackgroundColor3 = COLOR_OFF
    else
        HRP.CFrame = savedCF
        btn.Text = "✅ Teleportado!"
        btn.BackgroundColor3 = COLOR_ON
        task.wait(1)
        btn.Text = "📍 SALVAR/TELEPORTAR"
        btn.BackgroundColor3 = COLOR_OFF
    end
end)

CriarTitulo(ContainerTP, "👥 JOGADORES")

-- Lista de jogadores
local PlayerList = Instance.new("ScrollingFrame", ContainerTP)
PlayerList.Size = UDim2.new(1, -10, 0, 150)
PlayerList.BackgroundColor3 = Color3.fromRGB(40,40,40)
PlayerList.BackgroundTransparency = 0.2
PlayerList.BorderSizePixel = 0
PlayerList.ScrollBarThickness = 6
Instance.new("UICorner", PlayerList).CornerRadius = UDim.new(0, 6)

local PlayerLayout = Instance.new("UIListLayout", PlayerList)
PlayerLayout.Padding = UDim.new(0, 2)
PlayerLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center

local function AtualizarLista()
    for _, c in pairs(PlayerList:GetChildren()) do
        if c:IsA("TextButton") then c:Destroy() end
    end
    
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= Player then
            local btn = Instance.new("TextButton", PlayerList)
            btn.Size = UDim2.new(1, -10, 0, 30)
            btn.Text = p.DisplayName .. " [" .. p.Name .. "]"
            btn.Font = Enum.Font.Gotham
            btn.TextSize = 12
            btn.TextColor3 = Color3.new(1,1,1)
            btn.BackgroundColor3 = Color3.fromRGB(60,60,60)
            btn.BorderSizePixel = 0
            Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 4)
            
            btn.MouseButton1Click:Connect(function()
                SelectedPlayer = p
                for _, b in pairs(PlayerList:GetChildren()) do
                    if b:IsA("TextButton") then b.BackgroundColor3 = Color3.fromRGB(60,60,60) end
                end
                btn.BackgroundColor3 = COLOR_ON
            end)
        end
    end
    
    PlayerList.CanvasSize = UDim2.new(0, 0, 0, PlayerLayout.AbsoluteContentSize.Y + 10)
end

AtualizarLista()
Players.PlayerAdded:Connect(AtualizarLista)
Players.PlayerRemoving:Connect(AtualizarLista)

CriarBotao(ContainerTP, "🚀 TELEPORT PARA JOGADOR", function(btn)
    if SelectedPlayer and SelectedPlayer.Character and SelectedPlayer.Character:FindFirstChild("HumanoidRootPart") then
        HRP.CFrame = SelectedPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0,0,-3)
        btn.Text = "✅ Teleportado!"
        btn.BackgroundColor3 = COLOR_ON
        task.wait(1)
        btn.Text = "🚀 TELEPORT PARA JOGADOR"
        btn.BackgroundColor3 = COLOR_OFF
    else
        btn.Text = "❌ Selecione um jogador!"
        btn.BackgroundColor3 = COLOR_ON
        task.wait(1)
        btn.Text = "🚀 TELEPORT PARA JOGADOR"
        btn.BackgroundColor3 = COLOR_OFF
    end
end)

--======================================
-- LOOPS GLOBAIS
--======================================
RunService.Stepped:Connect(function()
    if noclipOn and Char then
        for _, v in pairs(Char:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
    end
end)

RunService.RenderStepped:Connect(function()
    if spinOn and HRP and HRP.Parent then
        HRP.CFrame = HRP.CFrame * CFrame.Angles(0, math.rad(80), 0)
    end
end)

RunService.RenderStepped:Connect(function()
    if flyOn and HRP and HRP.Parent and bv and bg then
        local move = Humanoid.MoveDirection
        bv.Velocity = Vector3.new(move.X * flySpeed, flyY * flySpeed, move.Z * flySpeed)
        if move.Magnitude > 0 then
            bg.CFrame = CFrame.lookAt(HRP.Position, HRP.Position + move)
        end
    end
end)

--======================================
-- FINALIZAÇÃO
--======================================
game:BindToClose(function()
    setMouseLocked(false)
end)

print("✅ Script Universal PC carregado!")
print("📌 F4: Abrir/Fechar menu")
print("🔑 Shift+P: Ativar/Desativar Cameraman (Terceira Pessoa)")
print("📱 Abas: Movimento | Câmera | Visual | Teleport")
