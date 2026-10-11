local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Lighting = game:GetService("Lighting")

-- 1. إنشاء واجهة الدخول المرعبة (مع الدوائر فقط فوق على اليمين بدون نصوص)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ScaryIntroGui"
ScreenGui.IgnoreGuiInset = true
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

if syn and syn.protect_gui then
    syn.protect_gui(ScreenGui)
    ScreenGui.Parent = CoreGui
elseif gethui then
    ScreenGui.Parent = gethui()
else
    ScreenGui.Parent = CoreGui
end

-- خلفية سوداء تغطي الشاشة بالكامل
local Background = Instance.new("Frame")
Background.Size = UDim2.new(1, 0, 1, 0)
Background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Background.BorderSizePixel = 0
Background.Parent = ScreenGui

-- حاوية الدوائر فوق على اليمين
local StatusContainer = Instance.new("Frame")
StatusContainer.Size = UDim2.new(0, 30, 0, 50)
StatusContainer.Position = UDim2.new(1, -45, 0, 15)
StatusContainer.BackgroundTransparency = 1
StatusContainer.Parent = Background

-- الدائرة الخضراء (التسجيل)
local RecDot = Instance.new("Frame")
RecDot.Size = UDim2.new(0, 14, 0, 14)
RecDot.Position = UDim2.new(0, 0, 0, 2)
RecDot.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
RecDot.BorderSizePixel = 0
RecDot.Parent = StatusContainer

local RecCorner = Instance.new("UICorner")
RecCorner.CornerRadius = UDim.new(1, 0)
RecCorner.Parent = RecDot

-- الدائرة الصفراء (المايك) تحتها
local MicDot = Instance.new("Frame")
MicDot.Size = UDim2.new(0, 14, 0, 14)
MicDot.Position = UDim2.new(0, 0, 0, 26)
MicDot.BackgroundColor3 = Color3.fromRGB(255, 220, 0)
MicDot.BorderSizePixel = 0
MicDot.Parent = StatusContainer

local MicCorner = Instance.new("UICorner")
MicCorner.CornerRadius = UDim.new(1, 0)
MicCorner.Parent = MicDot

-- صورة جماجم مرعبة
local SkullImage = Instance.new("ImageLabel")
SkullImage.Size = UDim2.new(0, 320, 0, 320)
SkullImage.Position = UDim2.new(0.5, -160, 0.28, -160)
SkullImage.BackgroundTransparency = 1
SkullImage.Image = "rbxassetid://6031094678" 
SkullImage.ImageTransparency = 0.1
SkullImage.Parent = Background

-- حاوية النصوص (مرفوعة للأعلى ومتباعدة)
local TextContainer = Instance.new("Frame")
TextContainer.Size = UDim2.new(1, 0, 0, 200)
TextContainer.Position = UDim2.new(0, 0, 0.52, 0)
TextContainer.BackgroundTransparency = 1
TextContainer.Parent = Background

-- نص "من نحن ؟"
local TitleText = Instance.new("TextLabel")
TitleText.Size = UDim2.new(1, 0, 0, 45)
TitleText.Position = UDim2.new(0, 0, 0, 0)
TitleText.BackgroundTransparency = 1
TitleText.Text = "من نحن ؟"
TitleText.TextColor3 = Color3.fromRGB(220, 20, 20)
TitleText.TextSize = 38
TitleText.Font = Enum.Font.GothamBold
TitleText.Parent = TextContainer

-- نص "نحن نراقبك !"
local SubText = Instance.new("TextLabel")
SubText.Size = UDim2.new(1, 0, 0, 45)
SubText.Position = UDim2.new(0, 0, 0, 55)
SubText.BackgroundTransparency = 1
SubText.Text = "نحن نراقبك !"
SubText.TextColor3 = Color3.fromRGB(255, 255, 255)
SubText.TextSize = 26
SubText.Font = Enum.Font.GothamMedium
SubText.Parent = TextContainer

-- يوزر المستهدف
local UserText = Instance.new("TextLabel")
UserText.Size = UDim2.new(1, 0, 0, 40)
UserText.Position = UDim2.new(0, 0, 0, 110)
UserText.BackgroundTransparency = 1
UserText.Text = "يا " .. LocalPlayer.Name
UserText.TextColor3 = Color3.fromRGB(180, 180, 180)
UserText.TextSize = 22
UserText.Font = Enum.Font.Gotham
UserText.Parent = TextContainer

-- اختفاء الواجهة بالكامل تدريجياً بعد 4.5 ثانية
task.delay(4.5, function()
    for i = 0, 1, 0.1 do
        Background.Transparency = i
        SkullImage.ImageTransparency = i
        TitleText.TextTransparency = i
        SubText.TextTransparency = i
        UserText.TextTransparency = i
        RecDot.BackgroundTransparency = i
        MicDot.BackgroundTransparency = i
        task.wait(0.05)
    end
    ScreenGui:Destroy()
end)

-- 2. دالة تبييض السكن وتشغيل Korblox & Headless بشكل دائم لا يختفي أبداً
local function applyScriptsAndWhite()
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    char:WaitForChild("Humanoid")
    
    -- تبييض السكن بالكامل
    for _, v in pairs(char:GetChildren()) do
        if v:IsA("Clothing") or v:IsA("Accessory") or v:IsA("ShirtGraphic") or v:IsA("BodyColors") then
            v:Destroy()
        elseif v:IsA("BasePart") then
            v.Color = Color3.fromRGB(255, 255, 255)
            v.Material = Enum.Material.SmoothPlastic
            for _, child in pairs(v:GetChildren()) do
                if child:IsA("Texture") or child:IsA("Decal") then
                    child:Destroy()
                end
            end
        end
    end

    -- تشغيل الكوربلوكس والهيدليس وإعادة استدعائهما فوراً عند الموت أو الرسباون
    task.spawn(function()
        pcall(function()
            loadstring(game:HttpGet("https://scriptblox.com/raw/Universal-Script-Korblox-All-145374"))()
        end)
    end)

    task.spawn(function()
        pcall(function()
            loadstring(game:HttpGet("https://scriptblox.com/raw/Universal-Script-Headless-(R6R15)-224493"))()
        end)
    end)
end

-- تطبيقها لأول مرة
pcall(applyScriptsAndWhite)

-- إعادة تطبيقها تلقائياً عند كل موت أو رسباون
LocalPlayer.CharacterAdded:Connect(function()
    task.wait(0.6)
    pcall(applyScriptsAndWhite)
end)

-- 3. تفعيل منظور PC و FOV واسع وواضح
Camera.FieldOfView = 95
LocalPlayer.CameraMaxZoomDistance = 500
LocalPlayer.CameraMinZoomDistance = 0.5

-- 4. إجبار اللعبة على أعلى دقة وجودة رسوميات خرافية (جودة PC)
pcall(function()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level21
    UserSettings():GetService("UserGameSettings").SavedQualityLevel = Enum.SavedQualityLevel.Level21
end)

-- 5. إضاءة وتأثيرات بصرية فخمة وجبارة
pcall(function()
    Lighting.GlobalShadows = true
    Lighting.FogEnd = 999999
    Lighting.Brightness = 2.2
    Lighting.ClockTime = 14
    
    if not Lighting:FindFirstChild("LuxuryBloom") then
        local bloom = Instance.new("BloomEffect")
        bloom.Name = "LuxuryBloom"
        bloom.Intensity = 0.15
        bloom.Size = 24
        bloom.Threshold = 0.8
        bloom.Parent = Lighting
    end

    if not Lighting:FindFirstChild("LuxuryColorCorrection") then
        local cc = Instance.new("ColorCorrectionEffect")
        cc.Name = "LuxuryColorCorrection"
        cc.Contrast = 0.2
        cc.Saturation = 0.15
        cc.Parent = Lighting
    end

    if not Lighting:FindFirstChild("LuxuryDOF") then
        local dof = Instance.new("DepthOfFieldEffect")
        dof.Name = "LuxuryDOF"
        dof.FarIntensity = 0.1
        dof.FocusDistance = 50
        dof.InFocusRadius = 30
        dof.NearIntensity = 0
        dof.Parent = Lighting
    end
end)

print("تم تفعيل السكربت بنجاح!")