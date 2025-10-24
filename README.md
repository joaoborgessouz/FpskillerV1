-- ⚫ JAO HUB - Painel compacto com funções completas
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local gui = Instance.new("ScreenGui")
gui.Name = "JAOHUB"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- 🔹 Remover roupa e acessórios ao abrir o script
local function removeAllAccessoriesFromCharacter()
	local char = player.Character
	if not char then return end
	for _, item in ipairs(char:GetChildren()) do
		if item:IsA("Accessory") or item:IsA("LayeredClothing") or item:IsA("Shirt")
		or item:IsA("ShirtGraphic") or item:IsA("Pants") or item:IsA("BodyColors")
		or item:IsA("CharacterMesh") then
			pcall(function() item:Destroy() end)
		end
	end
end

player.CharacterAdded:Connect(function()
	task.wait(0.2)
	removeAllAccessoriesFromCharacter()
end)
if player.Character then
	task.defer(removeAllAccessoriesFromCharacter)
end

-- 🪟 Painel principal
local painelLargura = 160
local painelAltura = 200
local panel = Instance.new("Frame")
panel.Name = "MainPanel"
panel.Size = UDim2.new(0, painelLargura, 0, painelAltura)
panel.Position = UDim2.new(0, 80, 0.5, -painelAltura/2)
panel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
panel.BackgroundTransparency = 0.35
panel.BorderSizePixel = 0
panel.Active = true
panel.Draggable = true
panel.Visible = false
panel.Parent = gui
Instance.new("UICorner", panel).CornerRadius = UDim.new(0, 15)

-- 🟢 Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 25)
title.BackgroundTransparency = 1
title.Text = "JAO HUB"
title.Font = Enum.Font.Arcade
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextSize = 18
title.Parent = panel

-- ⚫ Botão principal (abrir/minimizar) - móvel
local mainButton = Instance.new("TextButton")
mainButton.Size = UDim2.new(0, 70, 0, 70)
mainButton.Position = UDim2.new(0, 10, 0.5, -35)
mainButton.Text = "JAO"
mainButton.Font = Enum.Font.Arcade
mainButton.TextScaled = true
mainButton.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
mainButton.BackgroundTransparency = 0.4
mainButton.TextColor3 = Color3.fromRGB(255, 255, 255)
mainButton.BorderSizePixel = 0
mainButton.Draggable = true
mainButton.Parent = gui
Instance.new("UICorner", mainButton).CornerRadius = UDim.new(1, 0)

local painelAberto = false
mainButton.MouseButton1Click:Connect(function()
	painelAberto = not painelAberto
	panel.Visible = painelAberto
end)

-- 🧩 Criador de botões empilhados
local botaoPosY = 35
local botaoEspaco = 45
local function criarBotao(nome, callback)
	local botao = Instance.new("TextButton")
	botao.Size = UDim2.new(0.9, 0, 0, 40)
	botao.Position = UDim2.new(0.05, 0, 0, botaoPosY)
	botao.BackgroundColor3 = Color3.fromRGB(180, 0, 0)
	botao.Text = nome
	botao.Font = Enum.Font.Arcade
	botao.TextColor3 = Color3.fromRGB(255, 255, 255)
	botao.TextSize = 14
	botao.BorderSizePixel = 0
	botao.Parent = panel
	Instance.new("UICorner", botao).CornerRadius = UDim.new(0, 10)

	local ativo = false
	botao.MouseButton1Click:Connect(function()
		ativo = not ativo
		botao.BackgroundColor3 = ativo and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(180, 0, 0)
		callback(ativo)
	end)

	botaoPosY = botaoPosY + botaoEspaco
end

-- 🟩 Jump Boost
criarBotao("JUMP BOOST", function(ativo)
	local char = player.Character or player.CharacterAdded:Wait()
	local humanoid = char:FindFirstChildOfClass("Humanoid")
	if humanoid then
		humanoid.UseJumpPower = true
		humanoid.JumpPower = ativo and 70 or 50
	end
end)

-- ⚔️ FPS DEVOUVER (Dark Matter + Yin Yang + Alien + Defective + Detective + Defective Beat Slap)
local FPSDevourer = {}
FPSDevourer.running = false
local TOOLS = {
    "Dark Matter Slap",
    "Yin Yang Strike",
    "Alien Slap",
    "Defective Beat Slap",
    "Detective Beat Slap"
}

local function equipTool(name)
    local char = player.Character
    local backpack = player:FindFirstChild("Backpack")
    if not char or not backpack then return false end
    if char:FindFirstChild(name) then return true end
    local tool = backpack:FindFirstChild(name)
    if tool then
        tool.Parent = char
        return true
    end
    return false
end

local function ensureAllEquipped()
    local char = player.Character
    if not char then return end
    for _, name in ipairs(TOOLS) do
        if not char:FindFirstChild(name) then
            equipTool(name)
        end
    end
end

function FPSDevourer:Start()
    if FPSDevourer.running then return end
    FPSDevourer.running = true
    task.spawn(function()
        while FPSDevourer.running do
            ensureAllEquipped()
            task.wait(0.4)
        end
    end)
end

function FPSDevourer:Stop()
    FPSDevourer.running = false
end

criarBotao("FPS DEVOUVER", function(ativo)
	if ativo then
		removeAllAccessoriesFromCharacter()
		FPSDevourer:Start()
	else
		FPSDevourer:Stop()
	end
end)

-- 🛡️ ANTI-LAG (remove skins de todos os jogadores)
local antiLagActive = false
local function antiLagFunction()
	while antiLagActive do
		for _, plr in ipairs(Players:GetPlayers()) do
			local char = plr.Character
			if char then
				for _, item in ipairs(char:GetChildren()) do
					if item:IsA("Accessory") or item:IsA("LayeredClothing") or item:IsA("Shirt")
					or item:IsA("ShirtGraphic") or item:IsA("Pants") or item:IsA("BodyColors")
					or item:IsA("CharacterMesh") then
						pcall(function() item:Destroy() end)
					end
				end
			end
		end
		task.wait(0.3)
	end
end

criarBotao("ANTI-LAG", function(ativo)
	antiLagActive = ativo
	if ativo then
		task.spawn(antiLagFunction)
	end
end)
