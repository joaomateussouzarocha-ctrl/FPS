-- [[ SERVIÇOS ]] --
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local Lighting = game:GetService("Lighting")

-- [[ VARIÁVEIS ]] --
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local ESP_Table = {}
local OriginalMapData = {}
local MenuState = {
    ESP = false,
    Aim = false,
    VisibleOnly = false,
    FOVEnabled = false,
    FPSMode = false,
    FOVSize = 100
}

-- [[ GUI PRINCIPAL ]] --
local ScreenGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
ScreenGui.Name = "XJ_Tabbed_System"
ScreenGui.ResetOnSpawn = false

-- Função Arrastar
local function MakeDraggable(frame)
    local dragging, dragInput, dragStart, startPos
    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging, dragStart, startPos = true, input.Position, frame.Position
            input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragging = false end end)
        end
    end)
    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

-- [[ LOGIN ]] --
local LoginFrame = Instance.new("Frame", ScreenGui)
LoginFrame.Size = UDim2.new(0, 260, 0, 220)
LoginFrame.Position = UDim2.new(0.5, -130, 0.3, 0)
LoginFrame.BackgroundColor3 = Color3.new(0, 0, 0)
LoginFrame.BackgroundTransparency = 0.2
Instance.new("UICorner", LoginFrame).CornerRadius = UDim.new(0, 20)
local LoginStroke = Instance.new("UIStroke", LoginFrame)
LoginStroke.Color, LoginStroke.Thickness, LoginStroke.Transparency = Color3.new(0.5, 0.5, 0.5), 10, 0.5
MakeDraggable(LoginFrame)

local StatusLabel = Instance.new("TextLabel", LoginFrame)
StatusLabel.Size = UDim2.new(1, 0, 0.3, 0)
StatusLabel.Position = UDim2.new(0, 0, 0.1, 0)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text, StatusLabel.TextColor3, StatusLabel.Font, StatusLabel.TextSize = "Loading System...", Color3.new(1,1,1), Enum.Font.GothamBold, 18

local BarContainer = Instance.new("Frame", LoginFrame)
BarContainer.Size, BarContainer.Position, BarContainer.BackgroundColor3 = UDim2.new(0, 180, 0, 8), UDim2.new(0.5, -90, 0.45, 0), Color3.new(0.1, 0.1, 0.1)
Instance.new("UICorner", BarContainer)
local BarStroke = Instance.new("UIStroke", BarContainer)
BarStroke.Color, BarStroke.Thickness = Color3.new(0, 0, 0), 2

local ProgressBar = Instance.new("Frame", BarContainer)
ProgressBar.Size, ProgressBar.BackgroundColor3 = UDim2.new(0, 0, 1, 0), Color3.new(1, 1, 1)
Instance.new("UICorner", ProgressBar)

task.spawn(function()
    ProgressBar:TweenSize(UDim2.new(1, 0, 1, 0), "Out", "Linear", 2)
    task.wait(2.2)
    StatusLabel.Text = "Enter Password"
    BarContainer:Destroy()
    local PassInput = Instance.new("TextBox", LoginFrame)
    PassInput.Size, PassInput.Position, PassInput.BackgroundColor3, PassInput.Text, PassInput.PlaceholderText, PassInput.TextColor3, PassInput.Font, PassInput.TextSize = UDim2.new(0.8, 0, 0.2, 0), UDim2.new(0.1, 0, 0.55, 0), Color3.new(0.1, 0.1, 0.1), "", "Password Here", Color3.new(1, 1, 1), Enum.Font.GothamBold, 14
    Instance.new("UICorner", PassInput)
    PassInput.FocusLost:Connect(function(e)
        if e and PassInput.Text == "xjscripts" then
            StatusLabel.Text = "Access Granted"
            task.wait(0.5)
            LoginFrame:Destroy()
            InitCheat()
        else
            StatusLabel.Text, task.wait(1), StatusLabel.Text = "Wrong Password", nil, "Enter Password"
        end
    end)
end)

-- [[ SISTEMA DE ABAS ]] --
function InitCheat()
    local MainFrame = Instance.new("Frame", ScreenGui)
    MainFrame.Size, MainFrame.Position, MainFrame.BackgroundColor3, MainFrame.BackgroundTransparency = UDim2.new(0, 350, 0, 250), UDim2.new(0.5, -175, 0.2, 0), Color3.new(0, 0, 0), 0.2
    Instance.new("UICorner", MainFrame)
    local MainStroke = Instance.new("UIStroke", MainFrame)
    MainStroke.Color, MainStroke.Thickness, MainStroke.Transparency = Color3.new(0.5, 0.5, 0.5), 6, 0.5
    MakeDraggable(MainFrame)

    -- Barra Lateral (Tabs)
    local TabSidebar = Instance.new("Frame", MainFrame)
    TabSidebar.Size, TabSidebar.BackgroundColor3, TabSidebar.BackgroundTransparency = UDim2.new(0, 80, 1, 0), Color3.new(0.05, 0.05, 0.05), 0.3
    Instance.new("UICorner", TabSidebar)

    -- Container de Conteúdo
    local ContentFrame = Instance.new("Frame", MainFrame)
    ContentFrame.Size, ContentFrame.Position, ContentFrame.BackgroundTransparency = UDim2.new(1, -90, 1, -20), UDim2.new(0, 85, 0, 10), 1

    local TabFrames = {}
    local function CreateTab(name, id)
        local frame = Instance.new("ScrollingFrame", ContentFrame)
        frame.Size, frame.BackgroundTransparency, frame.Visible, frame.CanvasSize, frame.ScrollBarThickness = UDim2.new(1, 0, 1, 0), 1, false, UDim2.new(0,0,1.5,0), 0
        TabFrames[id] = frame
        
        local btn = Instance.new("TextButton", TabSidebar)
        btn.Size, btn.Position, btn.Text, btn.BackgroundColor3, btn.TextColor3, btn.Font, btn.TextSize = UDim2.new(1, -10, 0, 35), UDim2.new(0, 5, 0, 10 + (id-1)*45), name, Color3.new(0.1, 0.1, 0.1), Color3.new(1, 1, 1), Enum.Font.GothamBold, 10
        Instance.new("UICorner", btn)
        
        btn.MouseButton1Click:Connect(function()
            for _, f in pairs(TabFrames) do f.Visible = false end
            for _, b in pairs(TabSidebar:GetChildren()) do if b:IsA("TextButton") then b.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1) end end
            frame.Visible = true
            btn.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
        end)
        return frame
    end

    local VisualsTab = CreateTab("VISUALS", 1)
    local CombatTab = CreateTab("COMBAT", 2)
    local MiscTab = CreateTab("MISC", 3)

    -- Função Criar Botão Switch
    local function CreateSwitch(txt, y, parent, callback)
        local btn = Instance.new("TextButton", parent)
        btn.Size, btn.Position, btn.Text, btn.BackgroundColor3, btn.TextColor3, btn.Font, btn.TextSize = UDim2.new(1, -10, 0, 35), UDim2.new(0, 5, 0, y), txt..": OFF", Color3.new(0.1, 0.1, 0.1), Color3.new(1, 1, 1), Enum.Font.GothamBold, 12
        Instance.new("UICorner", btn)
        local active = false
        btn.MouseButton1Click:Connect(function()
            active = not active
            btn.Text = txt..": "..(active and "ON" or "OFF")
            btn.BackgroundColor3 = active and Color3.new(1, 1, 1) or Color3.new(0.1, 0.1, 0.1)
            btn.TextColor3 = active and Color3.new(0, 0, 0) or Color3.new(1, 1, 1)
            callback(active)
        end)
        return btn
    end

    -- [ CONTEÚDO VISUALS ] --
    CreateSwitch("ESP", 0, VisualsTab, function(v) MenuState.ESP = v end)

    -- [ CONTEÚDO COMBAT ] --
    CreateSwitch("AIMBOT", 0, CombatTab, function(v) MenuState.Aim = v end)
    CreateSwitch("ONLY VISIBLE", 40, CombatTab, function(v) MenuState.VisibleOnly = v end)
    CreateSwitch("SHOW FOV", 80, CombatTab, function(v) 
        MenuState.FOVEnabled = v 
        ScreenGui:FindFirstChild("FOV_Visual").Visible = v
    end)
    
    local fovInp = Instance.new("TextBox", CombatTab)
    fovInp.Size, fovInp.Position, fovInp.Text, fovInp.BackgroundColor3, fovInp.TextColor3, fovInp.Font, fovInp.TextSize = UDim2.new(1, -10, 0, 35), UDim2.new(0, 5, 0, 120), "100", Color3.new(0.1,0.1,0.1), Color3.new(1,1,1), Enum.Font.GothamBold, 12
    Instance.new("UICorner", fovInp)
    fovInp:GetPropertyChangedSignal("Text"):Connect(function()
        local n = tonumber(fovInp.Text) or 0
        MenuState.FOVSize = n
        ScreenGui.FOV_Visual.Size = UDim2.new(0, n*2, 0, n*2)
    end)

    -- [ CONTEÚDO MISC ] --
    CreateSwitch("FPS BOOST", 0, MiscTab, function(v)
        MenuState.FPSMode = v
        if v then
            OriginalMapData.Ambient = Lighting.OutdoorAmbient
            Lighting.OutdoorAmbient = Color3.new(1, 1, 1)
            for _, x in pairs(Lighting:GetChildren()) do if x:IsA("Sky") or x:IsA("Atmosphere") then x:Destroy() end end
            local s = Instance.new("Sky", Lighting)
            local id = "rbxassetid://6039289839"
            s.SkyboxBk, s.SkyboxDn, s.SkyboxFt, s.SkyboxLf, s.SkyboxRt, s.SkyboxUp = id, id, id, id, id, id
            for _, p in pairs(workspace:GetDescendants()) do
                if p:IsA("BasePart") and not p:IsDescendantOf(LocalPlayer.Character) then
                    OriginalMapData[p] = {p.Color, p.Material}
                    p.Color, p.Material = Color3.fromRGB(35,35,35), Enum.Material.SmoothPlastic
                end
            end
        else
            Lighting.OutdoorAmbient = OriginalMapData.Ambient or Color3.new(0.5,0.5,0.5)
            for p, d in pairs(OriginalMapData) do if p ~= "Ambient" and p.Parent then p.Color, p.Material = d[1], d[2] end end
            OriginalMapData = {}
        end
    end)
    
    local rej = Instance.new("TextButton", MiscTab)
    rej.Size, rej.Position, rej.Text, rej.BackgroundColor3, rej.TextColor3, rej.Font, rej.TextSize = UDim2.new(1, -10, 0, 35), UDim2.new(0, 5, 0, 40), "REJOIN GAME", Color3.fromRGB(80, 30, 30), Color3.new(1,1,1), Enum.Font.GothamBold, 12
    Instance.new("UICorner", rej)
    rej.MouseButton1Click:Connect(function() TeleportService:Teleport(game.PlaceId, LocalPlayer) end)

    local cls = Instance.new("TextButton", MiscTab)
    cls.Size, cls.Position, cls.Text, cls.BackgroundColor3, cls.TextColor3, cls.Font, cls.TextSize = UDim2.new(1, -10, 0, 35), UDim2.new(0, 5, 0, 80), "CLOSE MENU", Color3.fromRGB(40, 40, 40), Color3.new(1,1,1), Enum.Font.GothamBold, 12
    Instance.new("UICorner", cls)

    -- FOV Visual
    local fv = Instance.new("Frame", ScreenGui)
    fv.Name, fv.AnchorPoint, fv.Position, fv.BackgroundTransparency, fv.Visible = "FOV_Visual", Vector2.new(0.5, 0.5), UDim2.new(0.5, 0, 0.5, 0), 1, false
    local fvSt = Instance.new("UIStroke", fv)
    fvSt.Color, fvSt.Thickness = Color3.new(1, 1, 1), 1
    Instance.new("UICorner", fv).CornerRadius = UDim.new(1, 0)

    -- Botão Minimizar
    local mB = Instance.new("TextButton", ScreenGui)
    mB.Size, mB.Position, mB.BackgroundColor3, mB.Text, mB.TextColor3, mB.Font, mB.TextSize, mB.Visible = UDim2.new(0, 45, 0, 45), UDim2.new(0.5, -22, 0.05, 0), Color3.new(1, 1, 1), "XJ", Color3.new(0, 0, 0), Enum.Font.GothamBold, 14, false
    Instance.new("UICorner", mB).CornerRadius = UDim.new(1, 0)
    local mSt = Instance.new("UIStroke", mB)
    mSt.Color, mSt.Thickness = Color3.new(0, 0, 0), 2
    MakeDraggable(mB)

    -- Toggle Logic
    local function Toggle()
        MainFrame.Visible = not MainFrame.Visible
        mB.Visible = not MainFrame.Visible
        if MainFrame.Visible then MainFrame.Position = UDim2.new(mB.Position.X.Scale, mB.Position.X.Offset-150, mB.Position.Y.Scale, mB.Position.Y.Offset) end
    end
    cls.MouseButton1Click:Connect(Toggle)
    mB.MouseButton1Click:Connect(Toggle)
    UserInputService.InputBegan:Connect(function(i, p) if not p and i.KeyCode == Enum.KeyCode.RightControl then Toggle() end end)

    -- Iniciar na aba Visuals
    VisualsTab.Visible = true

    -- Loop de Render (ESP/AIM)
    RunService.RenderStepped:Connect(function()
        local target, dist = nil, 9e9
        local center = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                local root = plr.Character.HumanoidRootPart
                if MenuState.ESP then
                    if not ESP_Table[plr] or not ESP_Table[plr].Parent then
                        local bb = Instance.new("BillboardGui", ScreenGui)
                        bb.AlwaysOnTop, bb.Size, bb.Adornee = true, UDim2.new(4, 0, 5, 0), root
                        local f = Instance.new("Frame", bb)
                        f.Size, f.BackgroundColor3, f.BackgroundTransparency = UDim2.new(1, 0, 1, 0), Color3.new(1, 0, 0), 0.7
                        Instance.new("UICorner", f).CornerRadius = UDim.new(0, 4)
                        local st = Instance.new("UIStroke", f)
                        st.Color, st.Thickness = Color3.new(0, 0, 0), 1.5
                        ESP_Table[plr] = bb
                    end
                    ESP_Table[plr].Enabled = true
                elseif ESP_Table[plr] then ESP_Table[plr].Enabled = false end

                if MenuState.Aim then
                    local pos, s = Camera:WorldToViewportPoint(root.Position)
                    if s then
                        local m = (center - Vector2.new(pos.X, pos.Y)).Magnitude
                        if (not MenuState.FOVEnabled or m < MenuState.FOVSize) and m < dist then
                            local vis = true
                            if MenuState.VisibleOnly then
                                local r = Camera:GetPartsObscuringTarget({root.Position}, {LocalPlayer.Character, plr.Character})
                                if #r > 0 then vis = false end
                            end
                            if vis then dist, target = m, root end
                        end
                    end
                end
            end
        end
        if target then Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, target.Position + Vector3.new(0, 1.5, 0)), 0.15) end
    end)
end
