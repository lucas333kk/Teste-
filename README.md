local main = {
    p = game.Players.LocalPlayer,
    c = nil,
    cam = workspace.CurrentCamera,
    rs = game:GetService("RunService"),
    uis = game:GetService("UserInputService")
}
main.c = main.p.Character or main.p.CharacterAdded:Wait()

local steals = {
    jc = nil,
    fc = nil,
    sc = nil,
    skyPlat = nil,
    skyMoveConn = nil,
    ascDescConn = nil,
    skyActive = false,
}

-- GUI redesenhado
local sg = Instance.new("ScreenGui", game.CoreGui)

-- Botão circular com letra K brilhante e fundo roxo
local o = Instance.new("TextButton", sg)
o.Size = UDim2.new(0, 60, 0, 60)
o.Position = UDim2.new(0, 20, 0, 20)
o.Text = "K"
o.BackgroundColor3 = Color3.fromRGB(128, 0, 128) -- roxo
o.TextColor3 = Color3.new(1, 1, 1)
o.Font = Enum.Font.GothamBlack
o.TextSize = 36
o.TextStrokeTransparency = 0 -- brilho total
o.TextStrokeColor3 = Color3.new(1,1,1) -- contorno branco
local oc = Instance.new("UICorner", o)
oc.CornerRadius = UDim.new(1, 0) -- botão circular

-- Frame principal
local f = Instance.new("Frame", sg)
f.Size = UDim2.new(0, 300, 0, 220)
f.Position = UDim2.new(0.5, -150, 0.5, -110)
f.BackgroundColor3 = Color3.fromRGB(20,20,20)
f.Visible = false
Instance.new("UICorner", f).CornerRadius = UDim.new(0, 12)

local t = Instance.new("TextLabel", f)
t.Size = UDim2.new(1, 0, 0, 30)
t.Text = "kl script hub"
t.TextColor3 = Color3.new(1, 1, 1)
t.BackgroundTransparency = 1
t.Font = Enum.Font.SourceSansBold
t.TextSize = 24

local x = Instance.new("TextButton", f)
x.Size = UDim2.new(0, 50, 0, 30)
x.Position = UDim2.new(1, -55, 0, 5)
x.Text = "X"
x.BackgroundColor3 = Color3.fromRGB(30,30,30)
x.TextColor3 = Color3.new(1, 1, 1)
Instance.new("UICorner", x).CornerRadius = UDim.new(0, 12)

o.MouseButton1Click:Connect(function()
    f.Visible = true
    o.Visible = false
end)
x.MouseButton1Click:Connect(function()
    f.Visible = false
    o.Visible = true
end)

-- Scroll
local scroll = Instance.new("ScrollingFrame", f)
scroll.Size = UDim2.new(1, 0, 1, -35)
scroll.Position = UDim2.new(0, 0, 0, 35)
scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
scroll.ScrollBarThickness = 5
scroll.BackgroundTransparency = 1
scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
local layout = Instance.new("UIListLayout", scroll)
layout.Padding = UDim.new(0, 5)
layout.HorizontalAlignment = Enum.HorizontalAlignment.Center

-- Botão helper
local function btn(name, func)
    local b = Instance.new("TextButton", scroll)
    b.Size = UDim2.new(0, 200, 0, 35)
    b.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
    b.TextColor3 = Color3.new(1, 1, 1)
    b.Text = name .. ": OFF"
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 12)
    local active = false
    b.MouseButton1Click:Connect(function()
        active = not active
        b.Text = name .. ": " .. (active and "ON" or "OFF")
        func(active)
    end)
end

-- InfJump
btn("InfJump", function(a)
    if a then
        steals.jc = main.uis.JumpRequest:Connect(function()
            local h = main.c:WaitForChild("HumanoidRootPart")
            if h then
                h.AssemblyLinearVelocity = Vector3.new(h.AssemblyLinearVelocity.X, 50, h.AssemblyLinearVelocity.Z)
            end
        end)
    else
        if steals.jc then steals.jc:Disconnect() steals.jc = nil end
    end
end)

-- Fly
btn("Fly", function(a)
    if a then
        steals.bv = Instance.new("BodyVelocity", main.c:WaitForChild("HumanoidRootPart"))
        steals.bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        steals.fc = main.rs.Heartbeat:Connect(function()
            steals.bv.Velocity = main.cam.CFrame.LookVector * 50
        end)
    else
        if steals.fc then steals.fc:Disconnect() steals.fc = nil end
        if steals.bv then steals.bv:Destroy() steals.bv = nil end
    end
end)

-- ESP
btn("ESP", function(a)
    for _, plr in pairs(game.Players:GetPlayers()) do
        if plr ~= main.p and plr.Character then
            local h = plr.Character:FindFirstChild("ESPHighlight")
            if a then
                if not h then
                    h = Instance.new("Highlight")
                    h.Name = "ESPHighlight"
                    h.FillColor = Color3.new(1, 0, 0)
                    h.OutlineColor = Color3.new(1, 1, 1)
                    h.Parent = plr.Character
                end
            else
                if h then h:Destroy() end
            end
        end
    end
end)

-- Speed turbo
btn("Speed", function(a)
    if a then
        steals.sc = main.rs.Heartbeat:Connect(function()
            local h = main.c:FindFirstChildOfClass("Humanoid")
            if h then h.WalkSpeed = 1000 end
        end)
    else
        if steals.sc then steals.sc:Disconnect() steals.sc = nil end
        local h = main.c:FindFirstChildOfClass("Humanoid")
        if h then h.WalkSpeed = 16 end
    end
end)

-- SkyTP sem travar
btn("SkyTP", function(a)
    local hrp = main.c:WaitForChild("HumanoidRootPart")
    if a then
        if not steals.skyActive then
            steals.skyPlat = Instance.new("Part", workspace)
            steals.skyPlat.Size = Vector3.new(100, 1, 100)
            steals.skyPlat.Anchored = true
            steals.skyPlat.Transparency = 1
            steals.skyPlat.CanCollide = true
            steals.skyPlat.Position = Vector3.new(hrp.Position.X, 100, hrp.Position.Z)
            hrp.CFrame = CFrame.new(hrp.Position.X, 102, hrp.Position.Z)

            steals.ascDescConn = main.uis.InputBegan:Connect(function(input, gpe)
                if gpe then return end
                if input.KeyCode == Enum.KeyCode.W then
                    steals.skyPlat.Position += Vector3.new(0, 5, 0)
                    hrp.CFrame = CFrame.new(hrp.Position.X, steals.skyPlat.Position.Y + 2, hrp.Position.Z)
                elseif input.KeyCode == Enum.KeyCode.S then
                    steals.skyPlat.Position -= Vector3.new(0, 5, 0)
                    hrp.CFrame = CFrame.new(hrp.Position.X, steals.skyPlat.Position.Y + 2, hrp.Position.Z)
                end
            end)

            steals.skyMoveConn = main.rs.Heartbeat:Connect(function()
                steals.skyPlat.Position = Vector3.new(hrp.Position.X, steals.skyPlat.Position.Y, hrp.Position.Z)
            end)

            steals.skyActive = true
        end
    else
        if steals.skyPlat then steals.skyPlat:Destroy() steals.skyPlat = nil end
        if steals.ascDescConn then steals.ascDescConn:Disconnect() steals.ascDescConn = nil end
        if steals.skyMoveConn then steals.skyMoveConn:Disconnect() steals.skyMoveConn = nil end
        steals.skyActive = false
    end
end)
