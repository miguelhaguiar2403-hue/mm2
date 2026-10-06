--[[
    ═══════════════════════════════════════════════════════
    🔪 MURDER MYSTERY 2 HUB  •  PURPLE EDITION
    - ESP Players com ROLE (Murder/Sheriff/Innocent)
    - ESP de Armas (gun/knife)
    - TP pra arma mais próxima
    - Speed, Fly, Noclip, Infinite Jump
    - Hub no TOPO da tela
    ═══════════════════════════════════════════════════════
]]

-- ==================== SERVIÇOS ====================
local Players          = game:GetService("Players")
local RunService       = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService     = game:GetService("TweenService")
local Workspace        = game:GetService("Workspace")

local LP     = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

-- ==================== TEMA ====================
local T = {
    Bg=Color3.fromRGB(18,12,28), Panel=Color3.fromRGB(28,18,45),
    Btn=Color3.fromRGB(45,25,70), BtnHov=Color3.fromRGB(70,40,110),
    Acc=Color3.fromRGB(170,90,255), AccB=Color3.fromRGB(210,140,255),
    Text=Color3.fromRGB(235,220,255), Green=Color3.fromRGB(140,255,180),
    Red=Color3.fromRGB(255,90,120), Yellow=Color3.fromRGB(255,220,90),
}

local RoleColors = {
    Murder    = Color3.fromRGB(255, 60, 60),
    Sheriff   = Color3.fromRGB(80, 150, 255),
    Innocent  = Color3.fromRGB(140, 255, 140),
    Unknown   = Color3.fromRGB(180, 180, 180),
}

-- ==================== ESTADO ====================
local State = {
    Speed=false, SpeedVal=40,
    Jump=false, JumpVal=80,
    Noclip=false,
    Fly=false, FlySpeed=10,
    InfJump=false,
    ESPPlayers=false, ESPRoles=true, ESPNames=true, ESPDist=true,
    ESPWeapons=false,
    AntiAFK=true,
}

-- ==================== GUI ====================
local SG=Instance.new("ScreenGui")
SG.Name="MM2Hub"; SG.ResetOnSpawn=false; SG.IgnoreGuiInset=true
SG.ZIndexBehavior=Enum.ZIndexBehavior.Sibling
SG.Parent=LP:WaitForChild("PlayerGui")

-- Botão flutuante no TOPO
local OpenBtn=Instance.new("TextButton")
OpenBtn.Size=UDim2.new(0,55,0,55); OpenBtn.Position=UDim2.new(0,20,0,80)
OpenBtn.BackgroundColor3=T.Panel; OpenBtn.Text="🔪"; OpenBtn.TextSize=26
OpenBtn.TextColor3=T.AccB; OpenBtn.BorderSizePixel=0; OpenBtn.ZIndex=100
OpenBtn.Parent=SG
Instance.new("UICorner",OpenBtn).CornerRadius=UDim.new(1,0)
local OS=Instance.new("UIStroke",OpenBtn); OS.Color=T.Acc; OS.Thickness=2

-- Frame principal no TOPO CENTRO
local Frame=Instance.new("Frame")
Frame.Size=UDim2.new(0,440,0,500)
Frame.Position=UDim2.new(0.5,-220,0,80)
Frame.BackgroundColor3=T.Bg; Frame.BorderSizePixel=0; Frame.Visible=false
Frame.ZIndex=100; Frame.Parent=SG
Instance.new("UICorner",Frame).CornerRadius=UDim.new(0,12)
local FS=Instance.new("UIStroke",Frame); FS.Color=T.Acc; FS.Thickness=2

local TB=Instance.new("Frame")
TB.Size=UDim2.new(1,0,0,42); TB.BackgroundColor3=T.Panel; TB.BorderSizePixel=0
TB.ZIndex=101; TB.Parent=Frame
Instance.new("UICorner",TB).CornerRadius=UDim.new(0,12)
local Mask=Instance.new("Frame")
Mask.Size=UDim2.new(1,0,0,12); Mask.Position=UDim2.new(0,0,1,-12)
Mask.BackgroundColor3=T.Panel; Mask.BorderSizePixel=0; Mask.ZIndex=102; Mask.Parent=TB
local TL=Instance.new("TextLabel")
TL.Size=UDim2.new(1,-90,1,0); TL.Position=UDim2.new(0,15,0,0); TL.BackgroundTransparency=1
TL.Text="🔪 MM2 HUB"; TL.TextColor3=T.AccB; TL.TextSize=17
TL.Font=Enum.Font.GothamBold; TL.TextXAlignment=Enum.TextXAlignment.Left
TL.ZIndex=103; TL.Parent=TB
local CB=Instance.new("TextButton")
CB.Size=UDim2.new(0,28,0,28); CB.Position=UDim2.new(1,-36,0,7)
CB.BackgroundColor3=T.Red; CB.Text="✕"; CB.TextColor3=Color3.new(1,1,1)
CB.TextSize=16; CB.Font=Enum.Font.GothamBold; CB.BorderSizePixel=0
CB.ZIndex=103; CB.Parent=TB
Instance.new("UICorner",CB).CornerRadius=UDim.new(0,6)

-- Barra de status (SUA role)
local StatusBar=Instance.new("Frame")
StatusBar.Size=UDim2.new(1,-20,0,32); StatusBar.Position=UDim2.new(0,10,0,50)
StatusBar.BackgroundColor3=T.Panel; StatusBar.BorderSizePixel=0
StatusBar.ZIndex=102; StatusBar.Parent=Frame
Instance.new("UICorner",StatusBar).CornerRadius=UDim.new(0,6)
local StatusLabel=Instance.new("TextLabel")
StatusLabel.Size=UDim2.new(1,-16,1,0); StatusLabel.Position=UDim2.new(0,8,0,0)
StatusLabel.BackgroundTransparency=1
StatusLabel.Text="🎭 Role: Carregando..."
StatusLabel.TextColor3=T.AccB; StatusLabel.TextSize=13
StatusLabel.Font=Enum.Font.GothamBold
StatusLabel.TextXAlignment=Enum.TextXAlignment.Left
StatusLabel.ZIndex=103; StatusLabel.Parent=StatusBar

local Sc=Instance.new("ScrollingFrame")
Sc.Size=UDim2.new(1,-20,1,-90); Sc.Position=UDim2.new(0,10,0,88)
Sc.BackgroundTransparency=1; Sc.BorderSizePixel=0; Sc.ScrollBarThickness=4
Sc.ScrollBarImageColor3=T.Acc; Sc.CanvasSize=UDim2.new(0,0,0,0)
Sc.ZIndex=101; Sc.Parent=Frame
local LL=Instance.new("UIListLayout",Sc)
LL.Padding=UDim.new(0,6); LL.SortOrder=Enum.SortOrder.LayoutOrder

-- ==================== HELPERS UI ====================
local function MakeBtn(txt,cor,fn)
    local b=Instance.new("TextButton")
    b.Size=UDim2.new(1,-10,0,34); b.BackgroundColor3=cor or T.Btn
    b.Text="  "..txt; b.TextColor3=T.Text; b.TextSize=14
    b.Font=Enum.Font.GothamMedium; b.TextXAlignment=Enum.TextXAlignment.Left
    b.BorderSizePixel=0; b.ZIndex=102; b.Parent=Sc
    Instance.new("UICorner",b).CornerRadius=UDim.new(0,7)
    local s=Instance.new("UIStroke",b); s.Color=T.Acc; s.Thickness=1; s.Transparency=0.6
    b.MouseEnter:Connect(function()
        TweenService:Create(b,TweenInfo.new(0.15),{BackgroundColor3=T.BtnHov}):Play()
    end)
    b.MouseLeave:Connect(function()
        TweenService:Create(b,TweenInfo.new(0.15),{BackgroundColor3=cor or T.Btn}):Play()
    end)
    b.MouseButton1Click:Connect(function() fn(b) end)
    return b
end

local function MakeToggle(txt,key,callback)
    local b=MakeBtn("🔴 "..txt,nil,function(btn)
        State[key]=not State[key]
        local on=State[key]
        btn.Text=(on and "  🟢 " or "  🔴 ")..txt
        btn.TextColor3=on and T.Green or T.Text
        if callback then callback(on) end
    end)
    return b
end

local function Section(txt)
    local l=Instance.new("TextLabel")
    l.Size=UDim2.new(1,-10,0,26); l.BackgroundTransparency=1
    l.Text="  ── "..txt.." ──"; l.TextColor3=T.Acc; l.TextSize=13
    l.Font=Enum.Font.GothamBold; l.TextXAlignment=Enum.TextXAlignment.Left
    l.ZIndex=102; l.Parent=Sc
end

local function GetChar()
    local c = LP.Character
    if not c then return nil, nil, nil end
    return c, c:FindFirstChild("HumanoidRootPart"), c:FindFirstChildOfClass("Humanoid")
end

-- ==================== DETECTAR ROLE ====================
local function GetPlayerRole(plr)
    -- StringValue no Player
    for _, obj in pairs(plr:GetChildren()) do
        if obj:IsA("StringValue") then
            local n = obj.Name:lower()
            if n:find("role") or n:find("team") then
                local v = obj.Value:lower()
                if v:find("murder") then return "Murder" end
                if v:find("sheriff") then return "Sheriff" end
                if v:find("innocent") then return "Innocent" end
            end
        end
    end

    local c = plr.Character
    if c then
        -- Tools equipadas
        for _, tool in pairs(c:GetChildren()) do
            if tool:IsA("Tool") then
                local tn = tool.Name:lower()
                if tn:find("knife") then return "Murder" end
                if tn:find("gun") or tn:find("revolver") or tn:find("pistol") then return "Sheriff" end
            end
        end
        -- Objetos com role no character
        for _, obj in pairs(c:GetChildren()) do
            if obj:IsA("StringValue") or obj:IsA("ObjectValue") then
                local n = obj.Name:lower()
                if n:find("role") or n:find("team") then
                    local v = tostring(obj.Value):lower()
                    if v:find("murder") then return "Murder" end
                    if v:find("sheriff") then return "Sheriff" end
                    if v:find("innocent") then return "Innocent" end
                end
            end
        end
    end

    -- Backpack
    local bp = plr:FindFirstChild("Backpack")
    if bp then
        for _, tool in pairs(bp:GetChildren()) do
            if tool:IsA("Tool") then
                local tn = tool.Name:lower()
                if tn:find("knife") then return "Murder" end
                if tn:find("gun") or tn:find("revolver") or tn:find("pistol") then return "Sheriff" end
            end
        end
    end

    return "Unknown"
end

-- ==================== ESP PLAYERS ====================
local ESPCache = {}

local function BuildESP(plr)
    if plr == LP or ESPCache[plr] then return end
    local d = {}

    d.hl = Instance.new("Highlight")
    d.hl.Name = "MM2ESP"
    d.hl.Adornee = plr.Character
    d.hl.FillColor = RoleColors.Unknown
    d.hl.OutlineColor = Color3.new(1,1,1)
    d.hl.FillTransparency = 0.5
    d.hl.OutlineTransparency = 0
    d.hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    d.hl.Enabled = false
    d.hl.Parent = SG

    d.bg = Instance.new("BillboardGui")
    d.bg.Name = "MM2Name"
    d.bg.Size = UDim2.new(0,200,0,44)
    d.bg.StudsOffset = Vector3.new(0, 3, 0)
    d.bg.AlwaysOnTop = true
    d.bg.Enabled = false
    d.bg.Parent = SG

    d.lbl = Instance.new("TextLabel")
    d.lbl.Size = UDim2.new(1,0,1,0)
    d.lbl.BackgroundTransparency = 1
    d.lbl.TextColor3 = RoleColors.Unknown
    d.lbl.TextStrokeTransparency = 0
    d.lbl.TextStrokeColor3 = Color3.new(0,0,0)
    d.lbl.TextSize = 14
    d.lbl.Font = Enum.Font.GothamBold
    d.lbl.Text = plr.Name
    d.lbl.Parent = d.bg

    ESPCache[plr] = d
end

local function KillESP(plr)
    if not ESPCache[plr] then return end
    for _, obj in pairs(ESPCache[plr]) do
        if obj and obj.Destroy then obj:Destroy() end
    end
    ESPCache[plr] = nil
end

Players.PlayerAdded:Connect(function(p)
    BuildESP(p)
    p.CharacterAdded:Connect(function(c)
        task.wait(0.5)
        local d = ESPCache[p]
        if d then
            d.hl.Adornee = c
            d.bg.Adornee = c:FindFirstChild("Head") or c
        end
    end)
end)
Players.PlayerRemoving:Connect(KillESP)
for _, p in pairs(Players:GetPlayers()) do BuildESP(p) end

RunService.RenderStepped:Connect(function()
    for plr, d in pairs(ESPCache) do
        local c = plr.Character
        local hrp = c and c:FindFirstChild("HumanoidRootPart")
        local head = c and c:FindFirstChild("Head")
        local hum = c and c:FindFirstChildOfClass("Humanoid")

        if c and hrp and head and hum and hum.Health > 0 then
            if not d.hl.Adornee then
                d.hl.Adornee = c
                d.bg.Adornee = head
            end

            local role = GetPlayerRole(plr)
            local color = RoleColors[role] or RoleColors.Unknown

            d.hl.Enabled = State.ESPPlayers
            d.hl.FillColor = color

            d.bg.Enabled = State.ESPPlayers
            d.lbl.TextColor3 = color

            local parts = {}
            if State.ESPNames then table.insert(parts, plr.Name) end
            if State.ESPRoles and role ~= "Unknown" then
                table.insert(parts, "["..role.."]")
            end
            if State.ESPDist then
                local dist = math.floor((hrp.Position - Camera.CFrame.Position).Magnitude)
                table.insert(parts, dist.."m")
            end
            d.lbl.Text = table.concat(parts, " ")

            if plr == LP then
                StatusLabel.Text = "🎭 SUA ROLE: "..role
                StatusLabel.TextColor3 = color
            end
        else
            d.hl.Enabled = false
            d.bg.Enabled = false
        end
    end
end)

-- ==================== ESP ARMAS ====================
local WeaponESPCache = {}

local function ClearWeaponESP()
    for _, obj in pairs(WeaponESPCache) do
        if obj and obj.Destroy then obj:Destroy() end
    end
    WeaponESPCache = {}
end

local function IsWeapon(obj)
    if not obj or not obj:IsA("BasePart") then return false end
    local n = obj.Name:lower()
    return n:find("gun") or n:find("knife") or n:find("revolver") or n:find("pistol") or n:find("weapon")
end

local function RefreshWeaponESP()
    ClearWeaponESP()
    if not State.ESPWeapons then return end
    for _, obj in pairs(Workspace:GetDescendants()) do
        if IsWeapon(obj) then
            local hl = Instance.new("Highlight")
            hl.Name = "WeaponESP"
            hl.Adornee = obj
            hl.FillColor = Color3.fromRGB(255, 220, 90)
            hl.OutlineColor = Color3.new(1,1,1)
            hl.FillTransparency = 0.3
            hl.OutlineTransparency = 0
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.Parent = SG
            table.insert(WeaponESPCache, hl)
        end
    end
end

spawn(function()
    while task.wait(2) do
        if State.ESPWeapons then RefreshWeaponESP() end
    end
end)

-- ==================== SEÇÕES ====================
Section("🎭 ESP PLAYERS")
MakeToggle("ESP Players", "ESPPlayers")
MakeToggle("Mostrar Role", "ESPRoles")
MakeToggle("Mostrar Nome", "ESPNames")
MakeToggle("Mostrar Distância", "ESPDist")

Section("🔫 ARMAS")
MakeToggle("ESP Armas (gun/knife)", "ESPWeapons", function(on)
    RefreshWeaponESP()
end)
MakeBtn("🎯 TP pra Arma Mais Próxima", T.Panel, function()
    local _, hrp = GetChar()
    if not hrp then return end
    local nearest, dist = nil, math.huge
    for _, obj in pairs(Workspace:GetDescendants()) do
        if IsWeapon(obj) then
            local d = (obj.Position - hrp.Position).Magnitude
            if d < dist then dist = d; nearest = obj end
        end
    end
    if nearest then
        hrp.CFrame = CFrame.new(nearest.Position + Vector3.new(0, 3, 0))
        print("🔫 TP pra arma:", nearest.Name)
    else
        print("❌ Nenhuma arma no mapa")
    end
end)
MakeBtn("📋 Listar Armas no Mapa", T.Panel, function()
    print("════════ ARMAS NO MAPA ════════")
    for _, obj in pairs(Workspace:GetDescendants()) do
        if IsWeapon(obj) then
            print("🔫 "..obj.Name.." em "..tostring(obj.Position))
        end
    end
    print("═══════════════════════════════")
end)

Section("🏃 MOVIMENTO")
MakeToggle("Speed 40", "Speed", function(on)
    local _, _, hum = GetChar()
    if hum then hum.WalkSpeed = on and State.SpeedVal or 16 end
end)
MakeToggle("Jump 80", "Jump", function(on)
    local _, _, hum = GetChar()
    if hum then
        hum.UseJumpPower = true
        hum.JumpPower = on and State.JumpVal or 50
    end
end)
MakeToggle("Noclip", "Noclip")
MakeToggle("Fly", "Fly", function(on)
    local _, hrp, hum = GetChar()
    if not hrp or not hum then return end
    if on then
        local bv = hrp:FindFirstChild("MM2FlyVel") or Instance.new("BodyVelocity", hrp)
        bv.Name = "MM2FlyVel"
        bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
        bv.Velocity = Vector3.zero
        hum.PlatformStand = true
    else
        local bv = hrp:FindFirstChild("MM2FlyVel")
        if bv then bv:Destroy() end
        hum.PlatformStand = false
    end
end)
MakeToggle("Infinite Jump", "InfJump")

Section("🛡️ UTILIDADES")
MakeToggle("Anti-AFK", "AntiAFK")

Section("⚙️ SISTEMA")
MakeBtn("🔄 Resetar Tudo", T.Red, function()
    for k, v in pairs(State) do
        if type(v) == "boolean" then State[k] = false end
    end
    ClearWeaponESP()
    local _, _, hum = GetChar()
    if hum then hum.WalkSpeed = 16; hum.JumpPower = 50 end
    print("🔄 Tudo resetado")
end)

-- ==================== LOOPS ====================
RunService.Stepped:Connect(function()
    if not State.Noclip then return end
    local c = LP.Character
    if not c then return end
    for _, p in pairs(c:GetDescendants()) do
        if p:IsA("BasePart") then p.CanCollide = false end
    end
end)

RunService.RenderStepped:Connect(function()
    if not State.Fly then return end
    local _, hrp = GetChar()
    local bv = hrp and hrp:FindFirstChild("MM2FlyVel")
    if bv then
        local dir = Vector3.zero
        local cam = Camera.CFrame
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then dir += cam.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then dir -= cam.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then dir -= cam.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then dir += cam.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then dir += Vector3.new(0,1,0) end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then dir -= Vector3.new(0,1,0) end
        bv.Velocity = dir * State.FlySpeed * 10
    end
end)

UserInputService.JumpRequest:Connect(function()
    if not State.InfJump then return end
    local _, _, hum = GetChar()
    if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
end)

LP.Idled:Connect(function()
    if State.AntiAFK then
        local vu = game:GetService("VirtualUser")
        vu:CaptureController()
        vu:ClickButton2(Vector2.new())
    end
end)

LP.CharacterAdded:Connect(function()
    task.wait(1)
    local _, _, hum = GetChar()
    if hum then
        if State.Speed then hum.WalkSpeed = State.SpeedVal end
        if State.Jump then
            hum.UseJumpPower = true
            hum.JumpPower = State.JumpVal
        end
    end
end)

-- ==================== DRAG / ABRIR / FECHAR ====================
local drag,dStart,fStart
TB.InputBegan:Connect(function(i)
    if i.UserInputType==Enum.UserInputType.MouseButton1 then
        drag=true; dStart=i.Position; fStart=Frame.Position
    end
end)
TB.InputChanged:Connect(function(i)
    if drag and i.UserInputType==Enum.UserInputType.MouseMovement then
        local d=i.Position-dStart
        Frame.Position=UDim2.new(fStart.X.Scale,fStart.X.Offset+d.X,fStart.Y.Scale,fStart.Y.Offset+d.Y)
    end
end)
UserInputService.InputEnded:Connect(function(i)
    if i.UserInputType==Enum.UserInputType.MouseButton1 then drag=false end
end)

OpenBtn.MouseButton1Click:Connect(function() Frame.Visible=not Frame.Visible end)
CB.MouseButton1Click:Connect(function() Frame.Visible=false end)

LL:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    Sc.CanvasSize=UDim2.new(0,0,0,LL.AbsoluteContentSize.Y+10)
end)

-- ==================== NOTIFY ====================
local N=Instance.new("TextLabel",SG)
N.Size=UDim2.new(0,460,0,40); N.Position=UDim2.new(0.5,-230,0,20)
N.BackgroundColor3=T.Panel; N.TextColor3=T.AccB
N.Text="🔪 MM2 HUB carregado! 🍂"
N.TextSize=15; N.Font=Enum.Font.GothamBold; N.BorderSizePixel=0; N.ZIndex=200
Instance.new("UICorner",N).CornerRadius=UDim.new(0,8)
task.wait(3.5)
TweenService:Create(N,TweenInfo.new(0.5),{BackgroundTransparency=1,TextTransparency=1}):Play()
task.wait(0.6); N:Destroy()

print("🔪 MM2 HUB carregado!")
