--[[
    🍡 Mochi UI Library v2.0 (Reusable UI Framework)
    
    วิธีนำไปใช้งาน (Example Usage):
    
    local Mochi = loadstring(game:HttpGet("YOUR_RAW_URL"))() -- หรือใส่ไว้ในสคริปต์เดียวกัน
    
    local Window = Mochi:CreateWindow({
        Title = "Mochi Hub",
        SubTitle = "v2.0.0",
        Keybind = Enum.KeyCode.RightControl
    })

    local MainTab = Window:CreateTab("Main", "🌾")
    
    MainTab:CreateLabel("ยินดีต้อนรับสู่ Mochi UI Library!")
    
    MainTab:CreateToggle({
        Name = "Auto Farm",
        Desc = "ฟาร์มมอนสเตอร์ให้อัตโนมัติ",
        Default = false,
        Callback = function(Value)
            print("Auto Farm:", Value)
        end
    })

    MainTab:CreateButton({
        Name = "Rejoin Server",
        Desc = "เชื่อมต่อเซิร์ฟเวอร์เดิมอีกครั้ง",
        Callback = function()
            print("Rejoining...")
        end
    })

    MainTab:CreateSlider({
        Name = "WalkSpeed",
        Min = 16,
        Max = 200,
        Default = 16,
        Callback = function(Value)
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value
        end
    })

    MainTab:CreateDropdown({
        Name = "Select Weapon",
        Options = {"Melee", "Sword", "Gun", "Blox Fruit"},
        Default = "Melee",
        Callback = function(Option)
            print("Selected Weapon:", Option)
        end
    })

    MainTab:CreateKeybind({
        Name = "Fly Keybind",
        Default = Enum.KeyCode.E,
        Callback = function(Key)
            print("Fly Key Pressed:", Key)
        end
    })

    MainTab:CreateColorpicker({
        Name = "ESP Color",
        Default = Color3.fromRGB(255, 163, 191),
        Callback = function(Color)
            print("Selected Color:", Color)
        end
    })
]]

local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Destroy existing UI
if PlayerGui:FindFirstChild("MochiMenuLibrary") then
	PlayerGui.MochiMenuLibrary:Destroy()
end

local MochiLib = {}

-- Theme Config
local C = {
	win = Color3.fromRGB(29, 28, 35),
	side = Color3.fromRGB(23, 22, 28),
	panel = Color3.fromRGB(37, 36, 44),
	row = Color3.fromRGB(45, 44, 53),
	line = Color3.fromRGB(54, 53, 64),
	text = Color3.fromRGB(239, 237, 245),
	muted = Color3.fromRGB(143, 141, 158),
	accent = Color3.fromRGB(255, 163, 191),
	accentSoft = Color3.fromRGB(74, 61, 72),
	ink = Color3.fromRGB(58, 31, 42),
	off = Color3.fromRGB(60, 59, 70),
	white = Color3.fromRGB(255, 255, 255),
}

local F = {
	title = Enum.Font.FredokaOne,
	body = Enum.Font.Nunito,
}

-- Helpers
local function make(class, props)
	local inst = Instance.new(class)
	for k, v in pairs(props) do
		if k ~= "Parent" then inst[k] = v end
	end
	inst.Parent = props.Parent
	return inst
end

local function round(inst, radius)
	make("UICorner", { CornerRadius = UDim.new(0, radius), Parent = inst })
end

local function pad(inst, v, h)
	make("UIPadding", {
		PaddingTop = UDim.new(0, v),
		PaddingBottom = UDim.new(0, v),
		PaddingLeft = UDim.new(0, h),
		PaddingRight = UDim.new(0, h),
		Parent = inst,
	})
end

local function tween(inst, props, duration)
	TweenService:Create(inst, TweenInfo.new(duration or 0.18, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), props):Play()
end

------------------------------------------------------------------
-- MAIN LIBRARY
------------------------------------------------------------------
function MochiLib:CreateWindow(config)
	config = config or {}
	local winTitle = config.Title or "Mochi UI"
	local winSub = config.SubTitle or "v2.0.0"
	local toggleKey = config.Keybind or Enum.KeyCode.RightControl

	local gui = make("ScreenGui", {
		Name = "MochiMenuLibrary",
		ResetOnSpawn = false,
		IgnoreGuiInset = true,
		ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
		Parent = PlayerGui,
	})

	local window = make("Frame", {
		Name = "Window",
		Size = UDim2.fromOffset(580, 360),
		AnchorPoint = Vector2.new(0.5, 0.5),
		Position = UDim2.fromScale(0.5, 0.5),
		BackgroundColor3 = C.win,
		BorderSizePixel = 0,
		Parent = gui,
	})
	round(window, 18)

	local uiScale = make("UIScale", { Parent = window })
	local camera = workspace.CurrentCamera
	local function fit()
		local v = camera.ViewportSize
		uiScale.Scale = math.clamp(math.min(v.X / 620, v.Y / 400), 0.5, 1)
	end
	fit()
	camera:GetPropertyChangedSignal("ViewportSize"):Connect(fit)

	-- Sidebar
	local side = make("Frame", {
		Name = "Sidebar",
		Size = UDim2.new(0, 160, 1, 0),
		BackgroundColor3 = C.side,
		BorderSizePixel = 0,
		Parent = window,
	})
	round(side, 18)
	make("Frame", {
		Size = UDim2.new(0, 24, 1, 0),
		Position = UDim2.new(1, -24, 0, 0),
		BackgroundColor3 = C.side,
		BorderSizePixel = 0,
		Parent = side,
	})

	local dotColors = { Color3.fromRGB(255, 143, 163), Color3.fromRGB(255, 216, 138), Color3.fromRGB(155, 232, 184) }
	for i, color in ipairs(dotColors) do
		local dot = make("Frame", {
			Size = UDim2.fromOffset(10, 10),
			Position = UDim2.fromOffset(16 + (i - 1) * 16, 16),
			BackgroundColor3 = color,
			BorderSizePixel = 0,
			Parent = side,
		})
		round(dot, 5)
	end

	make("TextLabel", {
		Text = winTitle,
		Font = F.title,
		TextSize = 18,
		TextColor3 = C.accent,
		TextXAlignment = Enum.TextXAlignment.Left,
		BackgroundTransparency = 1,
		Size = UDim2.new(1, -32, 0, 24),
		Position = UDim2.fromOffset(16, 38),
		Parent = side,
	})
	make("TextLabel", {
		Text = winSub,
		Font = F.body,
		TextSize = 12,
		TextColor3 = C.muted,
		TextXAlignment = Enum.TextXAlignment.Left,
		BackgroundTransparency = 1,
		Size = UDim2.new(1, -32, 0, 16),
		Position = UDim2.fromOffset(16, 60),
		Parent = side,
	})
	make("Frame", {
		Size = UDim2.new(1, -32, 0, 1),
		Position = UDim2.fromOffset(16, 85),
		BackgroundColor3 = C.line,
		BorderSizePixel = 0,
		Parent = side,
	})

	local tabList = make("Frame", {
		Size = UDim2.new(1, -20, 1, -100),
		Position = UDim2.fromOffset(10, 95),
		BackgroundTransparency = 1,
		Parent = side,
	})
	make("UIListLayout", { Padding = UDim.new(0, 6), SortOrder = Enum.SortOrder.LayoutOrder, Parent = tabList })

	-- Content Area
	local content = make("Frame", {
		Name = "Content",
		Size = UDim2.new(1, -160, 1, 0),
		Position = UDim2.fromOffset(160, 0),
		BackgroundTransparency = 1,
		Parent = window,
	})

	local header = make("Frame", {
		Size = UDim2.new(1, 0, 0, 50),
		BackgroundTransparency = 1,
		Active = true,
		Parent = content,
	})
	local titleLabel = make("TextLabel", {
		Text = "",
		Font = F.title,
		TextSize = 20,
		TextColor3 = C.text,
		TextXAlignment = Enum.TextXAlignment.Left,
		BackgroundTransparency = 1,
		Size = UDim2.new(1, -44, 1, 0),
		Position = UDim2.fromOffset(20, 0),
		Parent = header,
	})

	local body = make("Frame", {
		Size = UDim2.new(1, -30, 1, -65),
		Position = UDim2.fromOffset(15, 50),
		BackgroundTransparency = 1,
		Parent = content,
	})

	-- Drag Window
	local dragging, dragStart, startPos
	header.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = window.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then dragging = false end
			end)
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			local delta = input.Position - dragStart
			window.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
		end
	end)

	-- Toggle FAB Button
	local fab = make("TextButton", {
		Text = "🍡",
		Font = F.body,
		TextSize = 22,
		AutoButtonColor = false,
		Size = UDim2.fromOffset(44, 44),
		Position = UDim2.new(0, 15, 0.5, -22),
		BackgroundColor3 = C.accent,
		BorderSizePixel = 0,
		Parent = gui,
	})
	round(fab, 22)
	fab.Activated:Connect(function() window.Visible = not window.Visible end)
	UserInputService.InputBegan:Connect(function(input, processed)
		if not processed and input.KeyCode == toggleKey then
			window.Visible = not window.Visible
		end
	end)

	local WindowObject = { Tabs = {}, CurrentTab = nil }

	------------------------------------------------------------------
	-- TAB CREATION
	------------------------------------------------------------------
	function WindowObject:CreateTab(tabName, icon)
		icon = icon or "📌"
		local tabIndex = #WindowObject.Tabs + 1

		local page = make("ScrollingFrame", {
			Name = tabName .. "Page",
			Size = UDim2.fromScale(1, 1),
			BackgroundColor3 = C.panel,
			BorderSizePixel = 0,
			CanvasSize = UDim2.new(0, 0, 0, 0),
			AutomaticCanvasSize = Enum.AutomaticSize.Y,
			ScrollBarThickness = 3,
			ScrollBarImageColor3 = C.muted,
			Visible = false,
			Parent = body,
		})
		round(page, 14)
		pad(page, 10, 10)
		make("UIListLayout", { Padding = UDim.new(0, 8), SortOrder = Enum.SortOrder.LayoutOrder, Parent = page })

		local tabBtn = make("TextButton", {
			Text = "",
			AutoButtonColor = false,
			Size = UDim2.new(1, 0, 0, 36),
			BackgroundColor3 = C.row,
			BackgroundTransparency = 1,
			BorderSizePixel = 0,
			LayoutOrder = tabIndex,
			Parent = tabList,
		})
		round(tabBtn, 10)

		local tabIcon = make("TextLabel", {
			Text = icon,
			Font = F.body,
			TextSize = 16,
			TextColor3 = C.text,
			TextTransparency = 0.45,
			BackgroundTransparency = 1,
			Size = UDim2.fromOffset(28, 36),
			Position = UDim2.fromOffset(6, 0),
			Parent = tabBtn,
		})

		local tabLabel = make("TextLabel", {
			Text = tabName,
			Font = F.title,
			TextSize = 14,
			TextColor3 = C.muted,
			TextXAlignment = Enum.TextXAlignment.Left,
			BackgroundTransparency = 1,
			Size = UDim2.new(1, -40, 1, 0),
			Position = UDim2.fromOffset(36, 0),
			Parent = tabBtn,
		})

		local TabObject = { Page = page, Elements = {} }

		local function selectThisTab()
			for _, t in ipairs(WindowObject.Tabs) do
				t.Page.Visible = false
				tween(t.Btn, { BackgroundTransparency = 1 })
				tween(t.Label, { TextColor3 = C.muted })
				tween(t.Icon, { TextTransparency = 0.45 })
			end
			page.Visible = true
			titleLabel.Text = tabName
			tween(tabBtn, { BackgroundTransparency = 0 })
			tween(tabLabel, { TextColor3 = C.text })
			tween(tabIcon, { TextTransparency = 0 })
		end

		tabBtn.Activated:Connect(selectThisTab)

		table.insert(WindowObject.Tabs, { Page = page, Btn = tabBtn, Label = tabLabel, Icon = tabIcon })
		if #WindowObject.Tabs == 1 then selectThisTab() end

		-- Component Builders
		local function makeRow()
			local row = make("Frame", {
				Size = UDim2.new(1, 0, 0, 0),
				AutomaticSize = Enum.AutomaticSize.Y,
				BackgroundColor3 = C.row,
				BorderSizePixel = 0,
				Parent = page,
			})
			round(row, 10)
			pad(row, 10, 12)
			make("UIListLayout", { Padding = UDim.new(0, 6), SortOrder = Enum.SortOrder.LayoutOrder, Parent = row })
			return row
		end

		local function addDesc(row, text)
			if text and text ~= "" then
				make("TextLabel", {
					Text = text,
					Font = F.body,
					TextSize = 12,
					TextColor3 = C.muted,
					TextXAlignment = Enum.TextXAlignment.Left,
					TextWrapped = true,
					BackgroundTransparency = 1,
					Size = UDim2.new(1, 0, 0, 0),
					AutomaticSize = Enum.AutomaticSize.Y,
					LayoutOrder = 10,
					Parent = row,
				})
			end
		end

		------------------------------------------------------------------
		-- ELEMENTS
		------------------------------------------------------------------

		-- 1. Label
		function TabObject:CreateLabel(text)
			local row = makeRow()
			local lbl = make("TextLabel", {
				Text = text,
				Font = F.body,
				TextSize = 14,
				TextColor3 = C.text,
				TextXAlignment = Enum.TextXAlignment.Left,
				TextWrapped = true,
				BackgroundTransparency = 1,
				Size = UDim2.new(1, 0, 0, 0),
				AutomaticSize = Enum.AutomaticSize.Y,
				Parent = row,
			})
			return {
				Set = function(_, newText) lbl.Text = newText end
			}
		end

		-- 2. Button
		function TabObject:CreateButton(opts)
			local row = makeRow()
			local btn = make("TextButton", {
				Text = opts.Name or "Button",
				Font = F.title,
				TextSize = 14,
				TextColor3 = C.accent,
				AutoButtonColor = false,
				BackgroundColor3 = C.accentSoft,
				BorderSizePixel = 0,
				Size = UDim2.new(1, 0, 0, 32),
				Parent = row,
			})
			round(btn, 8)
			addDesc(row, opts.Desc)

			btn.Activated:Connect(function()
				tween(btn, { BackgroundColor3 = C.accent, TextColor3 = C.ink }, 0.08)
				task.delay(0.15, function()
					tween(btn, { BackgroundColor3 = C.accentSoft, TextColor3 = C.accent }, 0.2)
				end)
				if opts.Callback then opts.Callback() end
			end)
		end

		-- 3. Toggle
		function TabObject:CreateToggle(opts)
			local row = makeRow()
			local top = make("Frame", { Size = UDim2.new(1, 0, 0, 24), BackgroundTransparency = 1, Parent = row })
			make("TextLabel", {
				Text = opts.Name or "Toggle",
				Font = F.title,
				TextSize = 14,
				TextColor3 = C.text,
				TextXAlignment = Enum.TextXAlignment.Left,
				BackgroundTransparency = 1,
				Size = UDim2.new(1, -50, 1, 0),
				Parent = top,
			})

			local switch = make("TextButton", {
				Text = "",
				AutoButtonColor = false,
				AnchorPoint = Vector2.new(1, 0.5),
				Position = UDim2.new(1, 0, 0.5, 0),
				Size = UDim2.fromOffset(40, 22),
				BackgroundColor3 = C.off,
				BorderSizePixel = 0,
				Parent = top,
			})
			round(switch, 11)
			local knob = make("Frame", {
				Size = UDim2.fromOffset(16, 16),
				Position = UDim2.fromOffset(3, 3),
				BackgroundColor3 = C.white,
				BorderSizePixel = 0,
				Parent = switch,
			})
			round(knob, 8)

			addDesc(row, opts.Desc)

			local state = opts.Default or false
			local function set(val)
				state = val
				tween(switch, { BackgroundColor3 = val and C.accent or C.off })
				tween(knob, { Position = val and UDim2.fromOffset(21, 3) or UDim2.fromOffset(3, 3) })
				if opts.Callback then opts.Callback(state) end
			end
			set(state)

			switch.Activated:Connect(function() set(not state) end)
			return { Set = function(_, val) set(val) end }
		end

		-- 4. Slider
		function TabObject:CreateSlider(opts)
			local row = makeRow()
			local min = opts.Min or 0
			local max = opts.Max or 100
			local current = opts.Default or min

			local top = make("Frame", { Size = UDim2.new(1, 0, 0, 20), BackgroundTransparency = 1, Parent = row })
			make("TextLabel", {
				Text = opts.Name or "Slider",
				Font = F.title,
				TextSize = 14,
				TextColor3 = C.text,
				TextXAlignment = Enum.TextXAlignment.Left,
				BackgroundTransparency = 1,
				Size = UDim2.new(0.7, 0, 1, 0),
				Parent = top,
			})
			local valLabel = make("TextLabel", {
				Text = tostring(current),
				Font = F.body,
				TextSize = 13,
				TextColor3 = C.accent,
				TextXAlignment = Enum.TextXAlignment.Right,
				BackgroundTransparency = 1,
				Size = UDim2.new(0.3, 0, 1, 0),
				Position = UDim2.new(0.7, 0, 0, 0),
				Parent = top,
			})

			local sliderBar = make("TextButton", {
				Text = "",
				AutoButtonColor = false,
				Size = UDim2.new(1, 0, 0, 8),
				BackgroundColor3 = C.off,
				BorderSizePixel = 0,
				Parent = row,
			})
			round(sliderBar, 4)

			local fill = make("Frame", {
				Size = UDim2.new((current - min)/(max - min), 0, 1, 0),
				BackgroundColor3 = C.accent,
				BorderSizePixel = 0,
				Parent = sliderBar,
			})
			round(fill, 4)

			addDesc(row, opts.Desc)

			local sliding = false
			local function update(input)
				local pos = math.clamp((input.Position.X - sliderBar.AbsolutePosition.X) / sliderBar.AbsoluteSize.X, 0, 1)
				local val = math.floor(min + (max - min) * pos)
				valLabel.Text = tostring(val)
				fill.Size = UDim2.new(pos, 0, 1, 0)
				if opts.Callback then opts.Callback(val) end
			end

			sliderBar.InputBegan:Connect(function(input)
				if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
					sliding = true
					update(input)
				end
			end)
			UserInputService.InputEnded:Connect(function(input)
				if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
					sliding = false
				end
			end)
			UserInputService.InputChanged:Connect(function(input)
				if sliding and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
					update(input)
				end
			end)
		end

		-- 5. Dropdown
		function TabObject:CreateDropdown(opts)
			local row = makeRow()
			local options = opts.Options or {}
			local selected = opts.Default or options[1] or "Select..."

			local top = make("Frame", { Size = UDim2.new(1, 0, 0, 24), BackgroundTransparency = 1, Parent = row })
			make("TextLabel", {
				Text = opts.Name or "Dropdown",
				Font = F.title,
				TextSize = 14,
				TextColor3 = C.text,
				TextXAlignment = Enum.TextXAlignment.Left,
				BackgroundTransparency = 1,
				Size = UDim2.new(0.5, 0, 1, 0),
				Parent = top,
			})

			local dropBtn = make("TextButton", {
				Text = selected .. " ▾",
				Font = F.body,
				TextSize = 12,
				TextColor3 = C.text,
				BackgroundColor3 = C.panel,
				BorderSizePixel = 0,
				Size = UDim2.new(0.5, 0, 1, 0),
				Position = UDim2.new(0.5, 0, 0, 0),
				Parent = top,
			})
			round(dropBtn, 6)

			local listFrame = make("Frame", {
				Size = UDim2.new(1, 0, 0, 0),
				AutomaticSize = Enum.AutomaticSize.Y,
				BackgroundTransparency = 1,
				Visible = false,
				LayoutOrder = 2,
				Parent = row,
			})
			make("UIListLayout", { Padding = UDim.new(0, 4), Parent = listFrame })

			addDesc(row, opts.Desc)

			for _, opt in ipairs(options) do
				local optBtn = make("TextButton", {
					Text = opt,
					Font = F.body,
					TextSize = 12,
					TextColor3 = C.muted,
					BackgroundColor3 = C.panel,
					BorderSizePixel = 0,
					Size = UDim2.new(1, 0, 0, 24),
					Parent = listFrame,
				})
				round(optBtn, 4)
				optBtn.Activated:Connect(function()
					selected = opt
					dropBtn.Text = selected .. " ▾"
					listFrame.Visible = false
					if opts.Callback then opts.Callback(selected) end
				end)
			end

			dropBtn.Activated:Connect(function()
				listFrame.Visible = not listFrame.Visible
			end)
		end

		-- 6. Keybind
		function TabObject:CreateKeybind(opts)
			local row = makeRow()
			local currentKey = opts.Default or Enum.KeyCode.E

			local top = make("Frame", { Size = UDim2.new(1, 0, 0, 24), BackgroundTransparency = 1, Parent = row })
			make("TextLabel", {
				Text = opts.Name or "Keybind",
				Font = F.title,
				TextSize = 14,
				TextColor3 = C.text,
				TextXAlignment = Enum.TextXAlignment.Left,
				BackgroundTransparency = 1,
				Size = UDim2.new(0.6, 0, 1, 0),
				Parent = top,
			})

			local keyBtn = make("TextButton", {
				Text = currentKey.Name,
				Font = F.body,
				TextSize = 12,
				TextColor3 = C.accent,
				BackgroundColor3 = C.accentSoft,
				BorderSizePixel = 0,
				Size = UDim2.new(0.35, 0, 1, 0),
				Position = UDim2.new(0.65, 0, 0, 0),
				Parent = top,
			})
			round(keyBtn, 6)

			addDesc(row, opts.Desc)

			local listening = false
			keyBtn.Activated:Connect(function()
				listening = true
				keyBtn.Text = "..."
			end)

			UserInputService.InputBegan:Connect(function(input, processed)
				if listening and input.UserInputType == Enum.UserInputType.Keyboard then
					listening = false
					currentKey = input.KeyCode
					keyBtn.Text = currentKey.Name
				elseif not processed and input.KeyCode == currentKey then
					if opts.Callback then opts.Callback(currentKey) end
				end
			end)
		end

		-- 7. Colorpicker
		function TabObject:CreateColorpicker(opts)
			local row = makeRow()
			local color = opts.Default or Color3.fromRGB(255, 255, 255)

			local top = make("Frame", { Size = UDim2.new(1, 0, 0, 24), BackgroundTransparency = 1, Parent = row })
			make("TextLabel", {
				Text = opts.Name or "Colorpicker",
				Font = F.title,
				TextSize = 14,
				TextColor3 = C.text,
				TextXAlignment = Enum.TextXAlignment.Left,
				BackgroundTransparency = 1,
				Size = UDim2.new(0.7, 0, 1, 0),
				Parent = top,
			})

			local colorBox = make("TextButton", {
				Text = "",
				AutoButtonColor = false,
				BackgroundColor3 = color,
				BorderSizePixel = 0,
				Size = UDim2.fromOffset(36, 20),
				Position = UDim2.new(1, -36, 0.5, -10),
				Parent = top,
			})
			round(colorBox, 6)

			addDesc(row, opts.Desc)

			-- Preset Quick Colors
			local pickerFrame = make("Frame", {
				Size = UDim2.new(1, 0, 0, 24),
				BackgroundTransparency = 1,
				Visible = false,
				LayoutOrder = 2,
				Parent = row,
			})
			make("UIListLayout", { FillDirection = Enum.FillDirection.Horizontal, Padding = UDim.new(0, 6), Parent = pickerFrame })

			local presetColors = {
				Color3.fromRGB(255, 99, 132), Color3.fromRGB(54, 162, 235),
				Color3.fromRGB(255, 206, 86), Color3.fromRGB(75, 192, 192),
				Color3.fromRGB(153, 102, 255), Color3.fromRGB(255, 255, 255)
			}

			for _, c in ipairs(presetColors) do
				local pBtn = make("TextButton", {
					Text = "",
					AutoButtonColor = false,
					BackgroundColor3 = c,
					BorderSizePixel = 0,
					Size = UDim2.fromOffset(20, 20),
					Parent = pickerFrame,
				})
				round(pBtn, 10)
				pBtn.Activated:Connect(function()
					color = c
					colorBox.BackgroundColor3 = color
					if opts.Callback then opts.Callback(color) end
				end)
			end

			colorBox.Activated:Connect(function()
				pickerFrame.Visible = not pickerFrame.Visible
			end)
		end

		return TabObject
	end

	return WindowObject
end

return MochiLib
