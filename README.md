--[[ 
    ESP Hub por River0_0 | Interface by ChatGPT
    Para testes e análise de comportamento de ESP
]]

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- UI
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "ESP_Hub"
gui.ResetOnSpawn = false

-- Janela Principal
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 130)
frame.Position = UDim2.new(0, 20, 0, 20)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BackgroundTransparency = 0.1
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true -- ← Janela arrastável!

-- Canto Arredondado
local corner = Instance.new("UICorner", frame)
corner.CornerRadius = UDim.new(0, 10)

-- Borda Branca
local stroke = Instance.new("UIStroke", frame)
stroke.Thickness = 1
stroke.Color = Color3.new(1, 1, 1)

-- Título
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "ESP HUB"
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.TextColor3 = Color3.fromRGB(255, 255, 255)

-- Botão Toggle ESP
local button = Instance.new("TextButton", frame)
button.Size = UDim2.new(1, -20, 0, 40)
button.Position = UDim2.new(0, 10, 0, 40)
button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
button.TextColor3 = Color3.new(1, 1, 1)
button.Font = Enum.Font.GothamBold
button.TextSize = 14
button.Text = "Ativar ESP"
button.Name = "ToggleESP"

Instance.new("UICorner", button)

-- Créditos
local credit = Instance.new("TextLabel", frame)
credit.Size = UDim2.new(1, 0, 0, 20)
credit.Position = UDim2.new(0, 0, 1, -20)
credit.BackgroundTransparency = 1
credit.Text = "by River0_0"
credit.Font = Enum.Font.Gotham
credit.TextSize = 13
credit.TextColor3 = Color3.fromRGB(150, 150, 150)

-- Variáveis
local ESPEnabled = false
local highlights = {}

-- Função para aplicar ESP
local function applyESP(player)
	if player == LocalPlayer then return end
	if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then return end
	if highlights[player] then return end

	local highlight = Instance.new("Highlight")
	highlight.Name = "ESPHighlight"
	highlight.FillTransparency = 1
	highlight.OutlineTransparency = 0
	highlight.OutlineColor = Color3.new(1, 0, 0)
	highlight.Adornee = player.Character
	highlight.Parent = player.Character

	highlights[player] = highlight
end

-- Função para remover ESP
local function removeESP(player)
	if highlights[player] then
		highlights[player]:Destroy()
		highlights[player] = nil
	end
end

-- Atualização contínua do ESP
local function updateESP()
	if not ESPEnabled then return end

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
			applyESP(player)
		end
	end
end

-- Conexões de evento
Players.PlayerAdded:Connect(function(player)
	player.CharacterAdded:Connect(function()
		wait(1)
		if ESPEnabled then applyESP(player) end
	end)
end)

Players.PlayerRemoving:Connect(function(player)
	removeESP(player)
end)

-- Botão de Toggle
button.MouseButton1Click:Connect(function()
	ESPEnabled = not ESPEnabled
	button.Text = ESPEnabled and "Desativar ESP" or "Ativar ESP"

	if not ESPEnabled then
		for player, highlight in pairs(highlights) do
			removeESP(player)
		end
	end
end)

-- Loop contínuo
RunService.RenderStepped:Connect(function()
	if ESPEnabled then
		updateESP()
	end
end)
