-- Carrega a biblioteca Rayfield UI
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- Serviços do Roblox
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer

-- Janela Principal
local Window = Rayfield:CreateWindow({
   Name = "LEGENDARY HUB | SOCCER SYSTEM",
   LoadingTitle = "Carregando Estrutura Completa...",
   LoadingSubtitle = "by Legendary",
   ConfigurationSaving = { Enabled = false },
   Discord = { Enabled = false },
   KeySystem = false 
})

-- ==========================================
-- ABAS DA INTERFACE
-- ==========================================
local TabAtravessar = Window:CreateTab("Atravessar", 4483362458)
local TabPerformance = Window:CreateTab("Performance", 4483362458)
local TabVelocidade = Window:CreateTab("Velocidade", 4483362458)
local TabMuralha = Window:CreateTab("Muralha", 4483362458)

-- Variável de Controle Global para a Bola Branca
local whiteBallActive = false

-- Função auxiliar para achar a bola no mapa (usada em outros módulos)
local function ObterBola()
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and (obj.Name == "Ball" or obj.Name == "SoccerBall" or obj.Name:lower():find("bola")) then
            return obj
        end
    end
    return nil
end

-- Função interna que pinta a bola de branco e remove as texturas
local function makeBallWhite(p)
    if not p:IsA("BasePart") then return end
    
    p.Color = Color3.fromRGB(255, 255, 255) -- Cor Branca pura
    p.Material = Enum.Material.SmoothPlastic -- Textura Lisa
    p.CastShadow = false -- Otimiza o desempenho
    
    -- Remove texturas/adesivos normais
    for _, child in ipairs(p:GetChildren()) do
        if child:IsA("Texture") or child:IsA("Decal") then
            child:Destroy()
        end
    end
    
    -- Remove texturas de malhas especiais (SpecialMesh)
    local m = p:FindFirstChildOfClass("SpecialMesh")
    if m then 
        m.TextureId = "" 
    end
end

-- Função que checa se o objeto realmente é uma esfera/bola
local function checkAndApply(v)
    if v:IsA("BasePart") then
        if v.Shape == Enum.PartType.Ball or v.Name:lower():find("ball") or v.Name:lower():find("bola") then
            makeBallWhite(v)
        end
    end
end

-- ==========================================
-- ABA 1: ATRAVESSAR
-- ==========================================
TabAtravessar:CreateSection("Módulos Principais")

local AtravessarAtivado = false
TabAtravessar:CreateToggle({
   Name = "Atravessar",
   CurrentValue = false,
   Flag = "ToggleAtravessar",
   Callback = function(Value)
       AtravessarAtivado = Value
   end,
})
TabAtravessar:CreateLabel("INFO: Atravessa o oponente, mas na visão dele empurra.")

local BallChicleteAtivado = false
TabAtravessar:CreateToggle({
   Name = "Ball Chiclete",
   CurrentValue = false,
   Flag = "ToggleChiclete",
   Callback = function(Value)
       BallChicleteAtivado = Value
   end,
})
TabAtravessar:CreateLabel("INFO: A bola fica grudenta e gruda em você instantaneamente.")

local PainelZagAtivado = false
TabAtravessar:CreateToggle({
   Name = "Painel Zag",
   CurrentValue = false,
   Flag = "ToggleZag",
   Callback = function(Value)
       PainelZagAtivado = Value
   end,
})
TabAtravessar:CreateLabel("INFO: O oponente não atravessa você e a bola gruda.")

TabAtravessar:CreateSection("Módulos Adicionais")

local AntiPuloAtivado = false
TabAtravessar:CreateToggle({
   Name = "Anti Pulo",
   CurrentValue = false,
   Flag = "ToggleAntiPulo",
   Callback = function(Value)
       AntiPuloAtivado = Value
       if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
           if AntiPuloAtivado then
               LocalPlayer.Character:FindFirstChildOfClass("Humanoid").JumpPower = 0
           else
               LocalPlayer.Character:FindFirstChildOfClass("Humanoid").JumpPower = 50 
           end
       end
   end,
})
TabAtravessar:CreateLabel("INFO: Deixa o jogador incapaz de pular.")

local EmpurraForteAtivado = false
TabAtravessar:CreateToggle({
   Name = "Empurra Forte",
   CurrentValue = false,
   Flag = "ToggleEmpurra",
   Callback = function(Value)
       EmpurraForteAtivado = Value
   end,
})
TabAtravessar:CreateLabel("INFO: Faz os jogadores serem empurrados forte ao ficar na frente.")

local VencerDisputaAtivado = false
TabAtravessar:CreateToggle({
   Name = "Vencer Disputa",
   CurrentValue = false,
   Flag = "ToggleDisputa",
   Callback = function(Value)
       VencerDisputaAtivado = Value
   end,
})
TabAtravessar:CreateLabel("INFO: Tira a bola do adversário facilmente na frente do gol.")

local DesarmeAutoAtivado = false
TabAtravessar:CreateToggle({
   Name = "Desarme Automático",
   CurrentValue = false,
   Flag = "ToggleDesarme",
   Callback = function(Value)
       DesarmeAutoAtivado = Value
   end,
})
TabAtravessar:CreateLabel("INFO: Anti-atravessar bola do Brookhaven/Roblox quando rasteja.")


-- ==========================================
-- ABA 2: PERFORMANCE
-- ==========================================
TabPerformance:CreateSection("Aparência de Elementos")

TabPerformance:CreateToggle({
   Name = "Bola Branca",
   CurrentValue = false,
   Flag = "ToggleBolaBranca",
   Callback = function(Value)
       whiteBallActive = Value
       if whiteBallActive then
           -- Força uma varredura imediata ao ativar
           for _, v in ipairs(Workspace:GetDescendants()) do
               checkAndApply(v)
           end
       end
   end,
})
TabPerformance:CreateLabel("INFO: Deixa a bola toda branca tirando a textura dela.")


-- ==========================================
-- ABA 3: VELOCIDADE
-- ==========================================
TabVelocidade:CreateSection("Controle de WalkSpeed")

local function MudarVelocidade(v)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = v
    end
end

TabVelocidade:CreateToggle({
   Name = "Walkspeed | 23",
   CurrentValue = false,
   Callback = function(v) if v then MudarVelocidade(23) else MudarVelocidade(16) end end,
})
TabVelocidade:CreateToggle({
   Name = "Walkspeed | 24",
   CurrentValue = false,
   Callback = function(v) if v then MudarVelocidade(24) else MudarVelocidade(16) end end,
})
TabVelocidade:CreateToggle({
   Name = "Walkspeed | 25",
   CurrentValue = false,
   Callback = function(v) if v then MudarVelocidade(25) else MudarVelocidade(16) end end,
})
TabVelocidade:CreateToggle({
   Name = "Walkspeed | 26",
   CurrentValue = false,
   Callback = function(v) if v then MudarVelocidade(26) else MudarVelocidade(16) end end,
})
TabVelocidade:CreateToggle({
   Name = "Walkspeed | 27",
   CurrentValue = false,
   Callback = function(v) if v then MudarVelocidade(27) else MudarVelocidade(16) end end,
})
TabVelocidade:CreateToggle({
   Name = "Walkspeed | 28",
   CurrentValue = false,
   Callback = function(v) if v then MudarVelocidade(28) else MudarVelocidade(16) end end,
})
TabVelocidade:CreateToggle({
   Name = "Walkspeed | 29",
   CurrentValue = false,
   Callback = function(v) if v then MudarVelocidade(29) else MudarVelocidade(16) end end,
})
TabVelocidade:CreateToggle({
   Name = "Walkspeed | 30",
   CurrentValue = false,
   Callback = function(v) if v then MudarVelocidade(30) else MudarVelocidade(16) end end,
})
TabVelocidade:CreateLabel("Madki")


-- ==========================================
-- ABA 4: MURALHA
-- ==========================================
TabMuralha:CreateSection("Controle de Bloqueio")

local AntiAtravessarBola = false
TabMuralha:CreateToggle({
   Name = "Anti-Atravessar",
   CurrentValue = false,
   Flag = "ToggleAntiAtravessar",
   Callback = function(Value)
       AntiAtravessarBola = Value
   end,
})
TabMuralha:CreateLabel("INFO: Impede do jogador ser atravessado pela bola de futebol.")

local ForcaColisaoAtivada = false
TabMuralha:CreateToggle({
   Name = "Força Colisão",
   CurrentValue = false,
   Flag = "ToggleForcaColisao",
   Callback = function(Value)
       ForcaColisaoAtivada = Value
       local bola = ObterBola()
       if bola and ForcaColisaoAtivada then
           bola.CanCollide = true
       end
   end,
})
TabMuralha:CreateLabel("INFO: Força a bola a ficar sempre com colisão ligada (Anti-Atravessar).")

local PesoDaBolaAtivado = false
TabMuralha:CreateToggle({
   Name = "Peso da Bola",
   CurrentValue = false,
   Flag = "TogglePesoBola",
   Callback = function(Value)
       PesoDaBolaAtivado = Value
       local bola = ObterBola()
       if bola and bola:IsA("BasePart") then
           if PesoDaBolaAtivado then
               bola.CustomPhysicalProperties = PhysicalProperties.new(100, 0.3, 0.5, 1, 1)
           else
               bola.CustomPhysicalProperties = nil
           end
       end
   end,
})
TabMuralha:CreateLabel("INFO: Deixa a bola mais pesada e fácil de retirar do oponente.")


-- ==========================================
-- THREADS ATIVAS / LOOPS DE SEGUNDO PLANO
-- ==========================================

RunService.Stepped:Connect(function()
    -- 1. Lógica do Atravessar Otimizado
    if AtravessarAtivado then
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                for _, part in pairs(player.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end
    end
    
    -- 2. Lógica da Ball Chiclete
    if BallChicleteAtivado and LocalPlayer.Character then
        local rootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        local bola = ObterBola()
        if rootPart and bola then
            local distancia = (bola.Position - rootPart.Position).Magnitude
            if distancia < 25 then
                local posicaoAlvo = (rootPart.CFrame * CFrame.new(0, -2, -3)).Position
                bola.Velocity = Vector3.new(0, 0, 0)
                bola.RotVelocity = Vector3.new(0, 0, 0)
                bola.CFrame = CFrame.new(posicaoAlvo)
            end
        end
    end

    -- 3. Lógica do Desarme Automático
    if DesarmeAutoAtivado and LocalPlayer.Character then
        local rootPart = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        local bola = ObterBola()
        if rootPart and bola then
            local distancia = (bola.Position - rootPart.Position).Magnitude
            if distancia < 12 then 
                bola.CanCollide = true
                local direcaoPé = (rootPart.CFrame * CFrame.new(0, -2, -2.5)).Position
                bola.CFrame = CFrame.new(direcaoPé)
            end
        end
    end
end)

-- 4. Monitoramento em tempo real para novas bolas (Sua Lógica Avançada Corrigida para Branco)
Workspace.DescendantAdded:Connect(function(v)
    if whiteBallActive then
        task.wait(0.1) -- Tempo de segurança para carregar na memória
        checkAndApply(v)
    end
end)

-- 5. Loop de Varredura Avançado de 3 segundos (O segredo para o Brookhaven)
task.spawn(function()
    while true do
        if whiteBallActive then
            for _, v in ipairs(Workspace:GetDescendants()) do
                checkAndApply(v)
            end
        end
        task.wait(3) -- Intervalo para evitar travamentos
    end
end)

-- Loop do renderizador para travar o Anti-Pulo
RunService.RenderStepped:Connect(function()
    if AntiPuloAtivado and LocalPlayer.Character then
        local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if hum then hum.JumpPower = 0 end
    end
end)

Rayfield:Notify({
   Title = "Painel Pronto",
   Content = "Todas as abas de futebol foram carregadas com sucesso e o modificador de bola branca foi injetado!",
   Duration = 5,
   Image = 4483362458,
})

print("⚪ Modificador de Bolas Brancas Executado com Sucesso!")
