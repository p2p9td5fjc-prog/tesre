# --// ============================================
--// CDT DRIFTER
--// Créditos: @kazz4xn44
--// ============================================

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

--// VARIÁVEIS
local savedCFrame = nil
local minimized = false

--// FUNÇÃO PEGAR CARRO
local function getCar()
	local char = player.Character
	if not char then return nil end
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then return nil end
	local seat = hum.SeatPart
	if not seat then return nil end
	local root = seat.AssemblyRootPart
	if not root then return nil end
	return root:FindFirstAncestorWhichIsA("Model")
end

--// CONGELAR CARRO
local function freezeCar(car, state)
	for _, v in ipairs(car:GetDescendants()) do
		if v:IsA("BasePart") then
			v.Anchored = state
			v.AssemblyLinearVelocity = Vector3.zero
			v.AssemblyAngularVelocity = Vector3.zero
		end
	end
end

--// NOTIFICAÇÃO
local function notify(text)
	local n = Instance.new("TextLabel")
	n.Parent = PlayerGui
	n.Size = UDim2.new(0, 220, 0, 34)
	n.Position = UDim2.new(1, 300, 0.85, 0)
	n.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
	n.TextColor3 = Color3.new(1, 1, 1)
	n.Text = text
	n.Font = Enum.Font.GothamBold
	n.TextSize = 12
	n.BackgroundTransparency = 0.1
	n.ZIndex = 10

	local corner = Instance.new("UICorner", n)
	corner.CornerRadius = UDim.new(0, 8)

	local inTween = TweenService:Create(n,
		TweenInfo.new(0.4, Enum.EasingStyle.Quint),
		{ Position = UDim2.new(1, -240, 0.85, 0) })
	local outTween = TweenService:Create(n,
		TweenInfo.new(0.4, Enum.EasingStyle.Quint),
		{ Position = UDim2.new(1, 300, 0.85, 0) })

	inTween:Play()
	task.wait(2)
	outTween:Play()
	outTween.Completed:Wait()
	n:Destroy()
end

--// GUI
local gui = Instance.new("ScreenGui")
gui.Name = "CDTDrifter"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = PlayerGui

--// PANEL (menor)
local panel = Instance.new("Frame", gui)
panel.Name = "MainPanel"
panel.Size = UDim2.new(0, 160, 0, 130) -- 🔽 antes 220x180
panel.Position = UDim2.new(1, -180, 0.15, 0) -- 🔽 antes -250
panel.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
panel.BackgroundTransparency = 0.1
panel.Active = true
panel.Draggable = true

local panelCorner = Instance.new("UICorner", panel)
panelCorner.CornerRadius = UDim.new(0, 8)

local panelStroke = Instance.new("UIStroke", panel)
panelStroke.Color = Color3.fromRGB(255, 30, 30)
panelStroke.Thickness = 1
panelStroke.Transparency = 0.5

--// TÍTULO (menor)
local title = Instance.new("TextLabel", panel)
title.Name = "Title"
title.Size = UDim2.new(1, 0, 0, 20) -- 🔽 antes 26
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(140, 15, 15)
title.BackgroundTransparency = 0.0
title.BorderSizePixel = 0
title.Text = "CDT DRIFTER"
title.TextColor3 = Color3.fromRGB(255, 60, 60)
title.Font = Enum.Font.GothamBold
title.TextSize = 11 -- 🔽 antes 13
title.Active = true
title.Draggable = true

local titleCorner = Instance.new("UICorner", title)
titleCorner.CornerRadius = UDim.new(0, 8)

--// BOTÃO MINIMIZAR (menor)
local minimizeBtn = Instance.new("TextButton", title)
minimizeBtn.Name = "MinimizeBtn"
minimizeBtn.Size = UDim2.new(0, 18, 0, 16) -- 🔽 antes 22x20
minimizeBtn.Position = UDim2.new(1, -22, 0, 2) -- 🔽 antes -26
minimizeBtn.BackgroundColor3 = Color3.fromRGB(60, 10, 10)
minimizeBtn.Text = "–"
minimizeBtn.TextColor3 = Color3.new(1, 1, 1)
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.TextSize = 13 -- 🔽 antes 16
minimizeBtn.AutoButtonColor = true
minimizeBtn.ZIndex = 2

local minCorner = Instance.new("UICorner", minimizeBtn)
minCorner.CornerRadius = UDim.new(1, 0)

--// CONTEÚDO
local content = Instance.new("Frame", panel)
content.Name = "Content"
content.Size = UDim2.new(1, 0, 1, -20) -- 🔽 antes -26
content.Position = UDim2.new(0, 0, 0, 20) -- 🔽 antes 26
content.BackgroundTransparency = 1
content.Active = true
content.Draggable = true

--// BOTÃO SALVAR (menor)
local saveBtn = Instance.new("TextButton", content)
saveBtn.Size = UDim2.new(1, -16, 0, 30) -- 🔽 antes 40
saveBtn.Position = UDim2.new(0, 8, 0, 4) -- 🔽 antes 10,5
saveBtn.Text = "Salvar posição"
saveBtn.Font = Enum.Font.GothamBold
saveBtn.TextSize = 11 -- 🔽 antes 14
saveBtn.TextColor3 = Color3.new(1, 1, 1)
saveBtn.BackgroundColor3 = Color3.fromRGB(200, 25, 25)
saveBtn.AutoButtonColor = true

local saveCorner = Instance.new("UICorner", saveBtn)
saveCorner.CornerRadius = UDim.new(0, 6)

--// BOTÃO VOLTAR (menor)
local backBtn = Instance.new("TextButton", content)
backBtn.Size = UDim2.new(1, -16, 0, 30) -- 🔽 antes 40
backBtn.Position = UDim2.new(0, 8, 0, 38) -- 🔽 antes 10,50
backBtn.Text = "Voltar posição"
backBtn.Font = Enum.Font.GothamBold
backBtn.TextSize = 11 -- 🔽 antes 14
backBtn.TextColor3 = Color3.new(1, 1, 1)
backBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
backBtn.AutoButtonColor = true

local backCorner = Instance.new("UICorner", backBtn)
backCorner.CornerRadius = UDim.new(0, 6)

--// CRÉDITOS (menor)
local credits = Instance.new("TextLabel", content)
credits.Size = UDim2.new(1, 0, 0, 16) -- 🔽 antes 20
credits.Position = UDim2.new(0, 0, 1, -16) -- 🔽 antes -22
credits.BackgroundTransparency = 1
credits.Text = "@kazz4xn44"
credits.TextColor3 = Color3.fromRGB(150, 150, 150)
credits.Font = Enum.Font.Gotham
credits.TextSize = 10 -- 🔽 antes 12

--// MINIMIZAR / EXPANDIR
minimizeBtn.MouseButton1Click:Connect(function()
	minimized = not minimized

	if minimized then
		content.Visible = false
		TweenService:Create(panel, TweenInfo.new(0.25, Enum.EasingStyle.Quint), {
			Size = UDim2.new(0, 160, 0, 20) -- 🔽 antes 220x26
		}):Play()
		minimizeBtn.Text = "+"
	else
		TweenService:Create(panel, TweenInfo.new(0.25, Enum.EasingStyle.Quint), {
			Size = UDim2.new(0, 160, 0, 130) -- 🔽 antes 220x180
		}):Play()
		task.wait(0.2)
		content.Visible = true
		minimizeBtn.Text = "–"
	end
end)

--// SALVAR POSIÇÃO
saveBtn.MouseButton1Click:Connect(function()
	local car = getCar()
	if not car or not car.PrimaryPart then
		notify("Entre em um carro")
		return
	end
	savedCFrame = car.PrimaryPart.CFrame
	notify("Posição salva")
end)

--// VOLTAR POSIÇÃO
backBtn.MouseButton1Click:Connect(function()
	if not savedCFrame then
		notify("Nenhuma posição salva")
		return
	end

	local car = getCar()
	if not car or not car.PrimaryPart then
		notify("Entre em um carro")
		return
	end

	freezeCar(car, true)
	car:PivotTo(savedCFrame)
	task.wait(0.45)
	freezeCar(car, false)

	notify("Posição restaurada")
end)