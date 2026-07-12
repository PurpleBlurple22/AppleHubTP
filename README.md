local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local chatBotEvent = ReplicatedStorage:WaitForChild("ChatBotEvent")

-- 1. Core Top-Level UI Config
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PremiumChatBotGui"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true -- Ensures it displays perfectly over CoreGuis
screenGui.Parent = playerGui

-- Toggle UI Button (Sleek Round Button on Screen Corner)
local toggleBtn = Instance.new("TextButton")
toggleBtn.Name = "ToggleBot"
toggleBtn.Size = UDim2.new(0, 50, 0, 50)
toggleBtn.Position = UDim2.new(0.02, 0, 0.85, 0)
toggleBtn.BackgroundColor3 = Color3.fromRGB(30, 31, 34)
toggleBtn.Font = Enum.Font.FredokaOne
toggleBtn.Text = "🤖"
toggleBtn.TextSize = 24
toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleBtn.Parent = screenGui

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(1, 0) -- Circle
toggleCorner.Parent = toggleBtn

local toggleStroke = Instance.new("UIStroke")
toggleStroke.Color = Color3.fromRGB(88, 101, 242)
toggleStroke.Width = 2
toggleStroke.Parent = toggleBtn

-- Main Glow-Glass Frame Container
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 380, 0, 480)
mainFrame.Position = UDim2.new(0.02, 0, 0.82, -490) -- Nested beautifully over toggle
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 31, 34)
mainFrame.BorderSizePixel = 0
mainFrame.Visible = true
mainFrame.Parent = screenGui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 16)
mainCorner.Parent = mainFrame

local mainStroke = Instance.new("UIStroke")
mainStroke.Color = Color3.fromRGB(43, 45, 49)
mainStroke.Width = 2
mainStroke.Parent = mainFrame

-- Title Header Bar
local header = Instance.new("Frame")
header.Name = "Header"
header.Size = UDim2.new(1, 0, 0, 50)
header.BackgroundColor3 = Color3.fromRGB(43, 45, 49)
header.BorderSizePixel = 0
header.Parent = mainFrame

local headerCorner = Instance.new("UICorner")
headerCorner.CornerRadius = UDim.new(0, 16)
headerCorner.Parent = header

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -20, 1, 0)
title.Position = UDim2.new(0, 15, 0, 0)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.Text = "LUA SYSTEM BOT v2.0"
title.TextColor3 = Color3.fromRGB(242, 243, 245)
title.TextSize = 14
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = header

-- 2. Scroller Container
local scrollFrame = Instance.new("ScrollingFrame")
scrollFrame.Name = "ScrollFrame"
scrollFrame.Size = UDim2.new(1, -20, 1, -120)
scrollFrame.Position = UDim2.new(0, 10, 0, 60)
scrollFrame.BackgroundTransparency = 1
scrollFrame.BorderSizePixel = 0
scrollFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
scrollFrame.ScrollBarImageColor3 = Color3.fromRGB(88, 101, 242)
scrollFrame.ScrollBarThickness = 4
scrollFrame.Parent = mainFrame

local uiListLayout = Instance.new("UIListLayout")
uiListLayout.Padding = UDim.new(0, 12)
uiListLayout.SortOrder = Enum.SortOrder.LayoutOrder
uiListLayout.Parent = scrollFrame

uiListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	scrollFrame.CanvasSize = UDim2.new(0, 0, 0, uiListLayout.AbsoluteContentSize.Y + 20)
	scrollFrame.CanvasPosition = Vector2.new(0, uiListLayout.AbsoluteContentSize.Y)
end)

-- 3. Modern Neon Input Box
local inputFrame = Instance.new("Frame")
inputFrame.Name = "InputFrame"
inputFrame.Size = UDim2.new(1, -24, 0, 44)
inputFrame.Position = UDim2.new(0, 12, 1, -56)
inputFrame.BackgroundColor3 = Color3.fromRGB(43, 45, 49)
inputFrame.Parent = mainFrame

local inputCorner = Instance.new("UICorner")
inputCorner.CornerRadius = UDim.new(0, 10)
inputCorner.Parent = inputFrame

local textBox = Instance.new("TextBox")
textBox.Size = UDim2.new(1, -60, 1, 0)
textBox.Position = UDim2.new(0, 14, 0, 0)
textBox.BackgroundTransparency = 1
textBox.Font = Enum.Font.GothamMedium
textBox.TextSize = 14
textBox.TextColor3 = Color3.fromRGB(255, 255, 255)
textBox.TextXAlignment = Enum.TextXAlignment.Left
textBox.PlaceholderText = "Command AI (sword, clear, explode...)"
textBox.PlaceholderColor3 = Color3.fromRGB(148, 155, 164)
textBox.Text = ""
textBox.ClearTextOnFocus = false
textBox.Parent = inputFrame

local sendButton = Instance.new("TextButton")
sendButton.Size = UDim2.new(0, 40, 0, 32)
sendButton.Position = UDim2.new(1, -46, 0.5, -16)
sendButton.BackgroundColor3 = Color3.fromRGB(88, 101, 242) -- Discord Blurple
sendButton.Font = Enum.Font.GothamBold
sendButton.TextSize = 14
sendButton.TextColor3 = Color3.fromRGB(255, 255, 255)
sendButton.Text = "➔"
sendButton.Parent = inputFrame

local buttonCorner = Instance.new("UICorner")
buttonCorner.CornerRadius = UDim.new(0, 8)
buttonCorner.Parent = sendButton

-- 4. Premium Typewriter Animated Message Builder
local function addMessage(text, isUser)
	local bubbleFrame = Instance.new("Frame")
	bubbleFrame.BackgroundTransparency = 1
	bubbleFrame.Size = UDim2.new(1, 0, 0, 0)
	bubbleFrame.AutomaticSize = Enum.AutomaticSize.Y
	bubbleFrame.Parent = scrollFrame

	local bubble = Instance.new("Frame")
	bubble.BorderSizePixel = 0
	bubble.AutomaticSize = Enum.AutomaticSize.XY
	bubble.Parent = bubbleFrame

	local bubbleCorner = Instance.new("UICorner")
	bubbleCorner.CornerRadius = UDim.new(0, 12)
	bubbleCorner.Parent = bubble

	local textLabel = Instance.new("TextLabel")
	textLabel.BackgroundTransparency = 1
	textLabel.Font = Enum.Font.GothamMedium
	textLabel.TextSize = 14
	textLabel.TextWrapped = true
	textLabel.Size = UDim2.new(0, 0, 0, 0)
	textLabel.AutomaticSize = Enum.AutomaticSize.XY
	textLabel.Parent = bubble

	local sizeConstraint = Instance.new("UISizeConstraint")
	sizeConstraint.MaxWidth = 240
	sizeConstraint.Parent = textLabel

	local padding = Instance.new("UIPadding")
	padding.PaddingTop = UDim.new(0, 10)
	padding.PaddingBottom = UDim.new(0, 10)
	padding.PaddingLeft = UDim.new(0, 14)
	padding.PaddingRight = UDim.new(0, 14)
	padding.Parent = bubble

	-- Styling Distinctions
	if isUser then
		bubble.BackgroundColor3 = Color3.fromRGB(88, 101, 242)
		textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
		bubble.Position = UDim2.new(1, -5, 0, 0)
		bubble.AnchorPoint = Vector2.new(1, 0)
		textLabel.TextXAlignment = Enum.TextXAlignment.Left
		textLabel.Text = text
	else
		bubble.BackgroundColor3 = Color3.fromRGB(43, 45, 49)
		textLabel.TextColor3 = Color3.fromRGB(218, 222, 225)
		bubble.Position = UDim2.new(0, 5, 0, 0)
		bubble.AnchorPoint = Vector2.new(0, 0)
		textLabel.TextXAlignment = Enum.TextXAlignment.Left
		
		-- Animated Typewriter effect for Bot responses!
		task.spawn(function()
			for i = 1, #text do
				textLabel.Text = string.sub(text, 1, i)
				task.wait(0.01)
			end
		end)
	end
	
	-- Smooth Fade In Entry Animation
	bubble.GroupTransparency = 1
	local canvasGroup = Instance.new("CanvasGroup") -- Wrap frame transparency cleanly
	canvasGroup.Size = UDim2.new(1,0,1,0)
	canvasGroup.BackgroundTransparency = 1
	
	TweenService:Create(bubble, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position = UDim2.new(bubble.Position.X.Scale, bubble.Position.X.Offset, 0, 2)}):Play()
end

-- 5. Sending Actions
local function handleSend()
	local text = textBox.Text
	if text ~= "" then
		addMessage(text, true)
		textBox.Text = ""
		chatBotEvent:FireServer(text)
	end
end

sendButton.MouseButton1Click:Connect(handleSend)
textBox.FocusLost:Connect(function(enterPressed)
	if enterPressed then handleSend() end
end)

-- 6. Interactive Visibility Toggle Logic
local open = true
toggleBtn.MouseButton1Click:Connect(function()
	open = not open
	local targetPos = open and UDim2.new(0.02, 0, 0.82, -490) or UDim2.new(-1, 0, 0.82, -490)
	
	TweenService:Create(mainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
		Position = targetPos
	}):Play()
	
	toggleBtn.Text = open and "🤖" or "💬"
end)

-- Receive back from server
chatBotEvent.OnClientEvent:Connect(function(botResponse)
	addMessage(botResponse, false)
end)

-- Welcome Init Script
addMessage("Welcome Elite Admin! Access granted. Try these systems: 'sword', 'clear', 'speed', 'jump', 'explode', 'day', 'night'", false)
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local ServerStorage = game:GetService("ServerStorage")
local Lighting = game:GetService("Lighting")

local chatBotEvent = ReplicatedStorage:WaitForChild("ChatBotEvent")

chatBotEvent.OnServerEvent:Connect(function(player, userMessage)
	local msg = string.lower(userMessage)
	local character = player.Character
	local humanoid = character and character:FindFirstChildOfClass("Humanoid")
	local backpack = player:FindFirstChildOfClass("Backpack")
	
	local response = ""

	-- 🌟 COMMAND 1: Give Customized Inventory Sword/Tool
	if string.find(msg, "sword") or string.find(msg, "tool") or string.find(msg, "weapon") then
		local originalTool = ServerStorage:FindFirstChild("BotSword")
		
		if originalTool and backpack then
			local hasInBackpack = backpack:FindFirstChild("BotSword")
			local hasEquipped = character and character:FindFirstChild("BotSword")
			
			if hasInBackpack or hasEquipped then
				response = "⚠️ Diagnostics indicate you already possess the BotSword!"
			else
				local toolClone = originalTool:Clone()
				toolClone.Parent = backpack
				response = "⚔️ Request processed! The BotSword weapon has materialized in your backpack."
			end
		else
			-- Ultimate Quality Fallback: Dynamic Tool Creation if user hasn't made a weapon model!
			local newTool = Instance.new("Tool")
			newTool.Name = "EnergyBlade"
			
			local handle = Instance.new("Part")
			handle.Name = "Handle"
			handle.Size = Vector3.new(1, 4, 1)
			handle.BrickColor = BrickColor.new("Bright blue")
			handle.Material = Enum.Material.Neon
			handle.Parent = newTool
			
			newTool.Parent = backpack
			response = "✨ Couldn't find a template model in ServerStorage, so I dynamically forged a custom Neon EnergyBlade for your backpack!"
		end

	-- 🌟 COMMAND 2: Super Speed Modification
	elseif string.find(msg, "speed") or string.find(msg, "run") then
		if humanoid then
			humanoid.WalkSpeed = 100 -- Hyper Drive Speed
			response = "⚡ Matrix altered. Locomotive velocity boosted to Hyper-Drive 100!"
		else
			response = "❌ Override failed: Target system character instance is absent."
		end

	-- 🌟 COMMAND 3: Super Jump Alteration
	elseif string.find(msg, "jump") or string.find(msg, "fly") then
		if humanoid then
			humanoid.UseJumpPower = true
			humanoid.JumpPower = 120
			response = "🚀 Gravitational dampeners overridden! Jump structural output expanded to 120."
		else
			response = "❌ Target system lacks structural Jump Parameters."
		end

	-- 🌟 COMMAND 4: Self Explode Emergency System
	elseif string.find(msg, "explode") or string.find(msg, "boom") then
		local rootPart = character and character:FindFirstChild("HumanoidRootPart")
		if rootPart then
			local explosion = Instance.new("Explosion")
			explosion.Position = rootPart.Position
			explosion.BlastRadius = 15
			explosion.Parent = game.Workspace
			response = "💥 Tactical detonation initiated at your coordinates!"
		else
			response = "❌ Localization parameters not found. Cannot detonate base layer."
		end

	-- 🌟 COMMAND 5: Purge/Wipe Backpack Inventory Clean
	elseif string.find(msg, "clear") or string.find(msg, "empty") then
		if backpack then
			backpack:ClearAllChildren()
			-- Force drop anything currently gripped in hands too
			for _, item in pairs(character:GetChildren()) do
				if item:IsA("Tool") then item:Destroy() end
			end
			response = "🧹 Inventory completely vaporized. Your backpack is now completely clean."
		else
			response = "❌ System Error: Vault backpack record inaccessible."
		end

	-- 🌟 COMMAND 6: Instant Absolute Healing Max
	elseif string.find(msg, "heal") or string.find(msg, "hp") then
		if humanoid then
			humanoid.Health = humanoid.MaxHealth
			response = "❤️ Biometrics restored! All physical cell lines safely normalized to 100% capacity."
		else
			response = "❌ Medical protocols aborted: Biological lifeforms absent."
		end

	-- 🌟 COMMAND 7 & 8: Dynamic Planetary Space Lighting Override
	elseif string.find(msg, "night") then
		Lighting.TimeOfDay = "00:00:00"
		response = "🌌 Planetary vector adjusted: Commencing Dark Protocol Night Mode."

	elseif string.find(msg, "day") then
		Lighting.TimeOfDay = "12:00:00"
		response = "☀️ Planetary vector adjusted: Atmospheric illumination locked to Max Noon Day Mode."

	-- System Communication Response Fallbacks
	elseif string.find(msg, "hello") or string.find(msg, "hi") then
		response = "📡 System Greetings, operator " .. player.Name .. ". Database online. Ready to alter game states."
	else
		response = "🤖 Command unrecognized. Input parameters like 'sword', 'clear', 'speed', 'jump', 'explode', 'day', or 'night'."
	end

	-- Safe response callback loop
	chatBotEvent:FireClient(player, response)
end)
