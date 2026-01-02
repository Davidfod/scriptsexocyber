local player = game.Players.LocalPlayer
local pGui = player:WaitForChild("PlayerGui")
local runService = game:GetService("RunService")

-- Limpeza de segurança
if pGui:FindFirstChild("CyberNoDry_CF") then
    pGui.CyberNoDry_CF:Destroy()
end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "CyberNoDry_CF"
screenGui.ResetOnSpawn = false
screenGui.Parent = pGui

local mainFrame = Instance.new("Frame")
mainFrame.Name = "Main"
mainFrame.Size = UDim2.new(0, 280, 0, 280) -- Aumentado para caber o novo botão
mainFrame.Position = UDim2.new(0.5, -140, 0.5, -140)
mainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
mainFrame.Active = true
mainFrame.Draggable = true 
mainFrame.Parent = screenGui

Instance.new("UICorner", mainFrame)
local stroke = Instance.new("UIStroke", mainFrame)
stroke.Color = Color3.fromRGB(0, 255, 255)
stroke.Thickness = 2

-- TEXTO QUANDO MINIMIZADO
local cfText = Instance.new("TextLabel")
cfText.Size = UDim2.new(1, -80, 0, 40)
cfText.Position = UDim2.new(0, 10, 0, 0)
cfText.Text = "CF SCRIPTS"
cfText.TextColor3 = Color3.fromRGB(0, 255, 255)
cfText.Font = Enum.Font.GothamBold
cfText.TextSize = 16
cfText.BackgroundTransparency = 1
cfText.Visible = false
cfText.Parent = mainFrame

-- CONTEÚDO
local content = Instance.new("Frame")
content.Name = "Content"
content.Size = UDim2.new(1, 0, 1, 0)
content.BackgroundTransparency = 1
content.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "CyberNoDry Hub"
title.TextColor3 = Color3.fromRGB(0, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.BackgroundTransparency = 1
title.Parent = content

local inputName = Instance.new("TextBox")
inputName.Size = UDim2.new(0, 220, 0, 40)
inputName.Position = UDim2.new(0.5, -110, 0.2, 0)
inputName.PlaceholderText = "Iniciais do Alvo..."
inputName.Text = ""
inputName.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
inputName.TextColor3 = Color3.fromRGB(255, 255, 255)
inputName.Parent = content
Instance.new("UICorner", inputName)

-- BOTÃO GRUDAR TRONCO
local toggleTorso = Instance.new("TextButton")
toggleTorso.Size = UDim2.new(0, 220, 0, 45)
toggleTorso.Position = UDim2.new(0.5, -110, 0.4, 0)
toggleTorso.Text = "GRUDAR TRONCO"
toggleTorso.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
toggleTorso.TextColor3 = Color3.fromRGB(0, 0, 0)
toggleTorso.Font = Enum.Font.GothamBold
toggleTorso.Parent = content
Instance.new("UICorner", toggleTorso)

-- BOTÃO GRUDAR CABEÇA (NOVO)
local toggleHead = Instance.new("TextButton")
toggleHead.Size = UDim2.new(0, 220, 0, 45)
toggleHead.Position = UDim2.new(0.5, -110, 0.6, 0)
toggleHead.Text = "GRUDAR CABEÇA"
toggleHead.BackgroundColor3 = Color3.fromRGB(0, 200, 255)
toggleHead.TextColor3 = Color3.fromRGB(0, 0, 0)
toggleHead.Font = Enum.Font.GothamBold
toggleHead.Parent = content
Instance.new("UICorner", toggleHead)

local footer = Instance.new("TextLabel")
footer.Size = UDim2.new(1, 0, 0, 20)
footer.Position = UDim2.new(0, 0, 1, -20)
footer.Text = "© CyberNoDry Autor"
footer.TextColor3 = Color3.fromRGB(100, 100, 100)
footer.BackgroundTransparency = 1
footer.Parent = content

-- BOTÕES DE CONTROLE
local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.Text = "X"
closeBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.ZIndex = 10
closeBtn.Parent = mainFrame
Instance.new("UICorner", closeBtn)

local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 30, 0, 30)
minBtn.Position = UDim2.new(1, -70, 0, 5)
minBtn.Text = "-"
minBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minBtn.ZIndex = 10
minBtn.Parent = mainFrame
Instance.new("UICorner", minBtn)

-- LÓGICA COMPARTILHADA
local ativo = false
local conexao = nil

local function getPlayer(name)
    name = name:lower()
    if name == "" then return nil end
    for _, v in pairs(game.Players:GetPlayers()) do
        if v ~= player and (v.Name:lower():sub(1, #name) == name or v.DisplayName:lower():sub(1, #name) == name) then
            return v
        end
    end
    return nil
end

local function parar()
    ativo = false
    if conexao then conexao:Disconnect() conexao = nil end
    toggleTorso.Text = "GRUDAR TRONCO"
    toggleTorso.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
    toggleHead.Text = "GRUDAR CABEÇA"
    toggleHead.BackgroundColor3 = Color3.fromRGB(0, 200, 255)
    local char = player.Character
    if char then
        for _, p in pairs(char:GetChildren()) do if p:IsA("BasePart") then p.CanCollide = true end end
    end
end

-- CLIQUE TRONCO
toggleTorso.MouseButton1Click:Connect(function()
    if ativo then parar() return end
    local target = getPlayer(inputName.Text)
    if target and target.Character then
        ativo = true
        toggleTorso.Text = "ATIVADO (TRONCO)"
        toggleTorso.BackgroundColor3 = Color3.fromRGB(255, 0, 100)
        conexao = runService.RenderStepped:Connect(function()
            local char = player.Character
            local root = char and char:FindFirstChild("HumanoidRootPart")
            local tTorso = target.Character and (target.Character:FindFirstChild("UpperTorso") or target.Character:FindFirstChild("Torso"))
            if root and tTorso then
                for _, p in pairs(char:GetChildren()) do if p:IsA("BasePart") then p.CanCollide = false end end
                root.Velocity = Vector3.new(0,0,0)
                root.CFrame = tTorso.CFrame * CFrame.new(0, 0, 0.7 + math.sin(tick()*60)*1.5)
            end
        end)
    end
end)

-- CLIQUE CABEÇA (NOVO)
toggleHead.MouseButton1Click:Connect(function()
    if ativo then parar() return end
    local target = getPlayer(inputName.Text)
    if target and target.Character then
        ativo = true
        toggleHead.Text = "ATIVADO (CABEÇA)"
        toggleHead.BackgroundColor3 = Color3.fromRGB(255, 0, 100)
        conexao = runService.RenderStepped:Connect(function()
            local char = player.Character
            local root = char and char:FindFirstChild("HumanoidRootPart")
            local tHead = target.Character and target.Character:FindFirstChild("Head")
            if root and tHead then
                for _, p in pairs(char:GetChildren()) do if p:IsA("BasePart") then p.CanCollide = false end end
                root.Velocity = Vector3.new(0,0,0)
                -- Posiciona na FRENTE da cabeça (0.5 no eixo Y e 1.2 no eixo Z)
                local osc = math.sin(tick() * 60) * 1.5
                root.CFrame = tHead.CFrame * CFrame.new(0, 0.5, -1.2 + osc) * CFrame.Angles(0, math.pi, 0)
            end
        end)
    end
end)

-- MINIMIZAR / FECHAR
minBtn.MouseButton1Click:Connect(function()
    content.Visible = not content.Visible
    if not content.Visible then
        mainFrame.Size = UDim2.new(0, 200, 0, 40)
        cfText.Visible = true
        minBtn.Text = "+"
    else
        mainFrame.Size = UDim2.new(0, 280, 0, 280)
        cfText.Visible = false
        minBtn.Text = "-"
    end
end)

closeBtn.MouseButton1Click:Connect(function()
    parar()
    screenGui:Destroy()
end)
