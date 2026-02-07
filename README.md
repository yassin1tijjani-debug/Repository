-- DRAGON ZONE HUB V30 🐉 - الـنـسـخـة الـشـامـلـة الـنـهـائـيـة
-- [ تم دمج: ESP كامل + مراقب أحداث + أزرار الطوابق + تخصيص أزرار التحكم ]

local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local Title = Instance.new("TextLabel")

local EventMenu = Instance.new("Frame")
local TeleportMenu = Instance.new("Frame")
local ESPMenu = Instance.new("Frame")
local SettingsMenu = Instance.new("Frame")

local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local LocalPlayer = game.Players.LocalPlayer

-- [ المتغيرات الرئيسية ]
local AirOn = false 
local P = nil
local AirWalkKey = Enum.KeyCode.J -- حرف المشي الافتراضي
local MenuKey = Enum.KeyCode.RightControl -- زر المنيو الافتراضي
local WaitingForAirKey = false
local WaitingForMenuKey = false

-- [1] الواجهة الرئيسية
ScreenGui.Parent = game.CoreGui
MainFrame.Name = "DragonZoneMain"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.Position = UDim2.new(0.05, 0, 0.2, 0)
MainFrame.Size = UDim2.new(0, 230, 0, 440)
MainFrame.Active = true
MainFrame.Draggable = true

Title.Text = "DRAGON ZONE 🐉 V30"
Title.Size = UDim2.new(1, 0, 0, 50)
Title.BackgroundColor3 = Color3.fromRGB(170, 0, 0)
Title.TextColor3 = Color3.new(1,1,1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 20
Title.Parent = MainFrame

local function CreateButton(name, pos, color, parent)
    local btn = Instance.new("TextButton", parent)
    btn.Text = name
    btn.Size = UDim2.new(0.9, 0, 0, 32)
    btn.Position = pos
    btn.BackgroundColor3 = color
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    btn.BorderSizePixel = 0
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    return btn
end

-- [ وظيفة المشي على الهواء ]
local function ToggleAirWalk()
    AirOn = not AirOn
    local btn = MainFrame:FindFirstChild("AirWalkBtn")
    if btn then
        btn.Text = AirOn and "المشي: ON" or "المشي: OFF"
        btn.BackgroundColor3 = AirOn and Color3.new(0, 0.7, 0) or Color3.new(0.7, 0, 0)
    end
    if AirOn then 
        P = Instance.new("Part", LocalPlayer.Character); P.Size = Vector3.new(15,1,15); P.Anchored = true; P.Transparency = 0.5
        task.spawn(function() while AirOn and P.Parent do P.CFrame = LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0,-3.5,0); task.wait() end end)
    else if P then P:Destroy() end end
end

-- [ أزرار القائمة الرئيسية ]
local AirWalkBtn = CreateButton("المشي على الهواء: OFF", UDim2.new(0.05, 0, 0.13, 0), Color3.fromRGB(180, 0, 0), MainFrame)
AirWalkBtn.Name = "AirWalkBtn"
local FastBtn = CreateButton("أخذ سريع (Instant E)", UDim2.new(0.05, 0, 0.22, 0), Color3.fromRGB(0, 140, 0), MainFrame)
local VIPBtn = CreateButton("فتح أبواب VIP", UDim2.new(0.05, 0, 0.31, 0), Color3.fromRGB(200, 140, 0), MainFrame)
local OpenEvtBtn = CreateButton("مراقب الأحداث ⏰ >", UDim2.new(0.05, 0, 0.45, 0), Color3.fromRGB(0, 150, 150), MainFrame)
local OpenTPBtn = CreateButton("قسم التنقل 🚀 >", UDim2.new(0.05, 0, 0.58, 0), Color3.fromRGB(0, 100, 200), MainFrame)
local OpenESPBtn = CreateButton("إعدادات الكشف ESP 👁️ >", UDim2.new(0.05, 0, 0.71, 0), Color3.fromRGB(120, 0, 120), MainFrame)
local OpenSetBtn = CreateButton("إعدادات الماب ⚙️ >", UDim2.new(0.05, 0, 0.84, 0), Color3.fromRGB(60, 60, 60), MainFrame)

local function StyleMenu(menu)
    menu.Parent = MainFrame; menu.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    menu.Position = UDim2.new(1.05, 0, 0.1, 0); menu.Size = UDim2.new(0, 220, 0, 320)
    menu.Visible = false; Instance.new("UICorner", menu).CornerRadius = UDim.new(0, 8)
end
StyleMenu(EventMenu); StyleMenu(TeleportMenu); StyleMenu(ESPMenu); StyleMenu(SettingsMenu)

---------------- [ قسم مراقب الأحداث - كامل ] ----------------
local SkyText = Instance.new("TextLabel", EventMenu)
SkyText.TextSize = 22; SkyText.Font = Enum.Font.SourceSansBold; SkyText.TextColor3 = Color3.new(0,1,1); SkyText.Size = UDim2.new(1,0,0.5,0); SkyText.BackgroundTransparency = 1
local MoneyText = Instance.new("TextLabel", EventMenu)
MoneyText.TextSize = 22; MoneyText.Font = Enum.Font.SourceSansBold; MoneyText.TextColor3 = Color3.new(1,1,0); MoneyText.Size = UDim2.new(1,0,0.5,0); MoneyText.Position = UDim2.new(0,0,0.5,0); MoneyText.BackgroundTransparency = 1

task.spawn(function()
    while task.wait(1) do
        local f = {}
        for _,v in pairs(workspace:GetDescendants()) do if v:IsA("TextLabel") and v.Visible and v.Text:match("%d+:%d+") then table.insert(f, v.Text:match("%d+:%d+")) end end
        table.sort(f) if #f >= 2 then SkyText.Text = "السماوي:\n"..f[1] MoneyText.Text = "المال:\n"..f[2] end
    end
end)

---------------- [ قسم إعدادات ESP - كامل ] ----------------
local ESP_Color = Color3.fromRGB(255, 0, 0)
local NameESP_Active = false
local BoxESP_Active = false

local NameToggle = CreateButton("كشف الأسماء: OFF", UDim2.new(0.05, 0, 0.05, 0), Color3.fromRGB(100, 0, 100), ESPMenu)
local BoxToggle = CreateButton("كشف الأماكن (Box): OFF", UDim2.new(0.05, 0, 0.25, 0), Color3.fromRGB(100, 0, 100), ESPMenu)
local ColorPicker = CreateButton("تغيير لون الكشف", UDim2.new(0.05, 0, 0.45, 0), Color3.fromRGB(50, 50, 50), ESPMenu)
local ClearESP = CreateButton("إزالة جميع الكشوفات", UDim2.new(0.05, 0, 0.75, 0), Color3.fromRGB(150, 0, 0), ESPMenu)

ColorPicker.MouseButton1Click:Connect(function() ESP_Color = Color3.new(math.random(), math.random(), math.random()); ColorPicker.BackgroundColor3 = ESP_Color end)

local function ApplyESP(plr)
    if plr == LocalPlayer or not plr.Character then return end
    if NameESP_Active and not plr.Character.Head:FindFirstChild("DragonName") then
        local bg = Instance.new("BillboardGui", plr.Character.Head); bg.Name = "DragonName"; bg.Size = UDim2.new(0, 100, 0, 50); bg.AlwaysOnTop = true; bg.ExtentsOffset = Vector3.new(0, 3, 0)
        local tl = Instance.new("TextLabel", bg); tl.Text = plr.Name; tl.Size = UDim2.new(1, 0, 1, 0); tl.BackgroundTransparency = 1; tl.TextColor3 = ESP_Color; tl.Font = Enum.Font.SourceSansBold; tl.TextSize = 16
    end
    if BoxESP_Active and not plr.Character:FindFirstChild("DragonBox") then
        local box = Instance.new("BoxHandleAdornment", plr.Character); box.Name = "DragonBox"; box.Size = plr.Character:GetExtentsSize(); box.Adornee = plr.Character; box.AlwaysOnTop = true; box.ZIndex = 5; box.Transparency = 0.6; box.Color3 = ESP_Color
    end
end

NameToggle.MouseButton1Click:Connect(function() NameESP_Active = not NameESP_Active; NameToggle.Text = NameESP_Active and "كشف الأسماء: ON" or "كشف الأسماء: OFF"; NameToggle.BackgroundColor3 = NameESP_Active and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(100, 0, 100) end)
BoxToggle.MouseButton1Click:Connect(function() BoxESP_Active = not BoxESP_Active; BoxToggle.Text = BoxESP_Active and "كشف الأماكن: ON" or "كشف الأماكن: OFF"; BoxToggle.BackgroundColor3 = BoxESP_Active and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(100, 0, 100) end)
ClearESP.MouseButton1Click:Connect(function() for _, p in pairs(game.Players:GetPlayers()) do if p.Character then if p.Character.Head:FindFirstChild("DragonName") then p.Character.Head.DragonName:Destroy() end if p.Character:FindFirstChild("DragonBox") then p.Character.DragonBox:Destroy() end end end end)
RunService.RenderStepped:Connect(function() for _, p in pairs(game.Players:GetPlayers()) do ApplyESP(p) end end)

---------------- [ قسم التنقل - طوابق المنزل ] ----------------
local function TPTo(offset) LocalPlayer.Character.HumanoidRootPart.CFrame *= CFrame.new(0, offset, 0) end

CreateButton("الطابق 1 (صعود)", UDim2.new(0.05,0,0.05,0), Color3.fromRGB(0,80,150), TeleportMenu).MouseButton1Click:Connect(function() TPTo(18) end)
CreateButton("الطابق 2 (صعود)", UDim2.new(0.05,0,0.18,0), Color3.fromRGB(0,80,150), TeleportMenu).MouseButton1Click:Connect(function() TPTo(36) end)
CreateButton("الطابق 3 (صعود)", UDim2.new(0.05,0,0.31,0), Color3.fromRGB(0,80,150), TeleportMenu).MouseButton1Click:Connect(function() TPTo(54) end)
CreateButton("نزول للطابق الأسفل", UDim2.new(0.05,0,0.48,0), Color3.fromRGB(150,0,0), TeleportMenu).MouseButton1Click:Connect(function() TPTo(-18) end)
local SavePosBtn = CreateButton("حفظ المكان الحالي", UDim2.new(0.05,0,0.70,0), Color3.fromRGB(0,120,80), TeleportMenu)
local GoPosBtn = CreateButton("العودة للمحفوظ", UDim2.new(0.05,0,0.83,0), Color3.fromRGB(80,0,120), TeleportMenu)

local SavedLocation = nil
SavePosBtn.MouseButton1Click:Connect(function() SavedLocation = LocalPlayer.Character.HumanoidRootPart.CFrame; SavePosBtn.Text = "✅ تم الحفظ" end)
GoPosBtn.MouseButton1Click:Connect(function() if SavedLocation then LocalPlayer.Character.HumanoidRootPart.CFrame = SavedLocation end end)

---------------- [ قسم الإعدادات - تخصيص الأزرار ] ----------------
local MenuKeyBtn = CreateButton("زر المنيو: [R-Ctrl]", UDim2.new(0.05, 0, 0.05, 0), Color3.fromRGB(45, 45, 45), SettingsMenu)
local AirKeyBtn = CreateButton("زر المشي: [J]", UDim2.new(0.05, 0, 0.20, 0), Color3.fromRGB(45, 45, 45), SettingsMenu)
CreateButton("إعادة اتصال (Rejoin)", UDim2.new(0.05, 0, 0.50, 0), Color3.fromRGB(0, 100, 150), SettingsMenu).MouseButton1Click:Connect(function() game:GetService("TeleportService"):Teleport(game.PlaceId, LocalPlayer) end)
CreateButton("إغلاق السكريبت", UDim2.new(0.05, 0, 0.80, 0), Color3.fromRGB(150, 0, 0), SettingsMenu).MouseButton1Click:Connect(function() ScreenGui:Destroy() end)

MenuKeyBtn.MouseButton1Click:Connect(function() MenuKeyBtn.Text = "...اضغط زر"; WaitingForMenuKey = true end)
AirKeyBtn.MouseButton1Click:Connect(function() AirKeyBtn.Text = "...اضغط زر"; WaitingForAirKey = true end)

UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if WaitingForMenuKey then
        MenuKey = input.KeyCode; MenuKeyBtn.Text = "زر المنيو: ["..input.KeyCode.Name.."]"; WaitingForMenuKey = false
    elseif WaitingForAirKey then
        AirWalkKey = input.KeyCode; AirKeyBtn.Text = "زر المشي: ["..input.KeyCode.Name.."]"; WaitingForAirKey = false
    elseif input.KeyCode == MenuKey then
        MainFrame.Visible = not MainFrame.Visible
    elseif input.KeyCode == AirWalkKey then
        ToggleAirWalk()
    end
end)

---------------- [ نظام فتح القوائم ] ----------------
local function CloseAll() EventMenu.Visible = false; TeleportMenu.Visible = false; ESPMenu.Visible = false; SettingsMenu.Visible = false end
OpenEvtBtn.MouseButton1Click:Connect(function() local s = not EventMenu.Visible; CloseAll(); EventMenu.Visible = s end)
OpenTPBtn.MouseButton1Click:Connect(function() local s = not TeleportMenu.Visible; CloseAll(); TeleportMenu.Visible = s end)
OpenESPBtn.MouseButton1Click:Connect(function() local s = not ESPMenu.Visible; CloseAll(); ESPMenu.Visible = s end)
OpenSetBtn.MouseButton1Click:Connect(function() local s = not SettingsMenu.Visible; CloseAll(); SettingsMenu.Visible = s end)

VIPBtn.MouseButton1Click:Connect(function() for _,v in pairs(game:GetDescendants()) do if v:IsA("BasePart") and (v.Name:find("VIP") or v.Name:find("Gate")) then v.CanCollide = false; v.Transparency = 0.5 end end end)
FastBtn.MouseButton1Click:Connect(function() for _,v in pairs(game:GetDescendants()) do if v:IsA("ProximityPrompt") then v.HoldDuration = 0 end end end)
AirWalkBtn.MouseButton1Click:Connect(ToggleAirWalk)

print("DRAGON ZONE V30 FULL & FIXED READY")
