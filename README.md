# KurtKPZ
Just a script 
(please rate my script i made it)


local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "KurtKPZ Ultimate Troll V4",
   LoadingTitle = "Loading KurtKPZ v4...",
   LoadingSubtitle = "by Kurt ultimate",
   ConfigurationSaving = { Enabled = true, FolderName = "KurtKPZ_V4", FileName = "Config" },
   Discord = { Enabled = false, Invite = "", RememberJoins = true },
   KeySystem = false 
})

-- VARIABLES
local LP = game.Players.LocalPlayer
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

local InfJumpEnabled = false
local AimbotEnabled = false
local CurrentTarget = nil 
local ESPEnabled = false
local NoclipEnabled = false
local SpinEnabled = false
local KillAuraEnabled = false
local HitboxEnabled = false
local HitboxSizeValue = 20

-- TABS
local MainTab = Window:CreateTab("Movement", "zap")
local CombatTab = Window:CreateTab("Combat & Aim", "crosshair")
local VisualTab = Window:CreateTab("Visuals & FPS", "eye")
local MiscTab = Window:CreateTab("Misc & World", "settings")

--- [MOVEMENT TAB] ---

MainTab:CreateSlider({
   Name = "WalkSpeed",
   Range = {16, 500},
   Increment = 1,
   CurrentValue = 16,
   Callback = function(Value)
      if LP.Character and LP.Character:FindFirstChild("Humanoid") then
         LP.Character.Humanoid.WalkSpeed = Value
      end
   end,
})

MainTab:CreateSlider({
   Name = "Jump Power",
   Range = {50, 500},
   Increment = 1,
   CurrentValue = 50,
   Callback = function(Value)
      if LP.Character and LP.Character:FindFirstChild("Humanoid") then
         LP.Character.Humanoid.UseJumpPower = true
         LP.Character.Humanoid.JumpPower = Value
      end
   end,
})

MainTab:CreateSlider({
   Name = "Gravity Control",
   Range = {0, 196},
   Increment = 1,
   CurrentValue = 196,
   Callback = function(Value)
      workspace.Gravity = Value
   end,
})

MainTab:CreateToggle({
   Name = "Noclip (Walk Through Walls)",
   CurrentValue = false,
   Callback = function(Value) NoclipEnabled = Value end,
})

MainTab:CreateToggle({
   Name = "Spinbot",
   CurrentValue = false,
   Callback = function(Value) SpinEnabled = Value end,
})

MainTab:CreateToggle({
   Name = "Infinite Jump",
   CurrentValue = false,
   Callback = function(Value) InfJumpEnabled = Value end,
})

-- Movement Loops
RunService.Stepped:Connect(function()
    if NoclipEnabled and LP.Character then
        for _, v in pairs(LP.Character:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
    end
    if SpinEnabled and LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then
        LP.Character.HumanoidRootPart.CFrame = LP.Character.HumanoidRootPart.CFrame * CFrame.Angles(0, math.rad(50), 0)
    end
end)

UIS.JumpRequest:Connect(function()
    if InfJumpEnabled and LP.Character and LP.Character:FindFirstChildOfClass("Humanoid") then
        LP.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

--- [COMBAT & AIM] ---

CombatTab:CreateToggle({
   Name = "Aimbot (Lock Camera)",
   CurrentValue = false,
   Callback = function(Value) 
      AimbotEnabled = Value 
      if not Value then CurrentTarget = nil end
   end,
})

CombatTab:CreateToggle({
   Name = "Kill Aura (15 Studs)",
   CurrentValue = false,
   Callback = function(Value) KillAuraEnabled = Value end,
})

CombatTab:CreateToggle({
   Name = "Hitbox Extender",
   CurrentValue = false,
   Callback = function(Value) HitboxEnabled = Value end,
})

CombatTab:CreateSlider({
   Name = "Hitbox Size",
   Range = {2, 50},
   Increment = 1,
   CurrentValue = 20,
   Callback = function(Value) HitboxSizeValue = Value end,
})

-- Smart Aimbot Loop
RunService.RenderStepped:Connect(function()
    if AimbotEnabled then
        if CurrentTarget and CurrentTarget.Character and CurrentTarget.Character:FindFirstChild("Head") then
            local _, onScreen = Camera:WorldToViewportPoint(CurrentTarget.Character.Head.Position)
            if onScreen then
                Camera.CFrame = CFrame.new(Camera.CFrame.Position, CurrentTarget.Character.Head.Position)
                return
            end
        end

        local closestDist = math.huge
        local newTarget = nil
        for _, v in pairs(Players:GetPlayers()) do
            if v ~= LP and v.Character and v.Character:FindFirstChild("Head") then
                local pos, onScreen = Camera:WorldToViewportPoint(v.Character.Head.Position)
                if onScreen then
                    local mousePos = UIS:GetMouseLocation()
                    local dist = (Vector2.new(pos.X, pos.Y) - mousePos).Magnitude
                    if dist < closestDist then
                        closestDist = dist
                        newTarget = v
                    end
                end
            end
        end
        CurrentTarget = newTarget
    end
end)

--- [VISUALS & FPS] ---

VisualTab:CreateButton({
    Name = "Adidas Community Animations (R15 Only)",
    Callback = function()
        local function StopAnimations(character)
            local animateScript = character:WaitForChild("Animate")
            local humanoid = character:WaitForChild("Humanoid")
            local animator = humanoid:WaitForChild("Animator")
            animateScript.Disabled = true
            for _, track in pairs(animator:GetPlayingAnimationTracks()) do
                track:Stop()
            end
            animateScript.Disabled = false
        end

        local function ApplyCustomAnimations(character)
            local humanoid = character:WaitForChild("Humanoid")
            if humanoid.RigType ~= Enum.HumanoidRigType.R15 then
                Rayfield:Notify({Title = "Error", Content = "R15 Only!", Duration = 3})
                return
            end

            local animateScript = character:WaitForChild("Animate")
            StopAnimations(character)

            animateScript.idle.Animation1.AnimationId = "rbxassetid://122257458498464"
            animateScript.idle.Animation2.AnimationId = "rbxassetid://98173568987992"
            animateScript.walk.WalkAnim.AnimationId = "rbxassetid://122150855457006"
            animateScript.run.RunAnim.AnimationId = "rbxassetid://82598234841035"
            animateScript.jump.JumpAnim.AnimationId = "rbxassetid://75290611992385"
            animateScript.climb.ClimbAnim.AnimationId = "rbxassetid://88763136693023"
            animateScript.fall.FallAnim.AnimationId = "rbxassetid://18537367238"

            humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
            Rayfield:Notify({Title = "Animations", Content = "Adidas Animations Applied!", Duration = 3})
        end

        if LP.Character then ApplyCustomAnimations(LP.Character) end
        LP.CharacterAdded:Connect(ApplyCustomAnimations)
    end,
})

VisualTab:CreateToggle({
   Name = "Enable ESP (Chams)",
   CurrentValue = false,
   Callback = function(Value) ESPEnabled = Value end,
})

VisualTab:CreateButton({
   Name = "Enable Infinite Zoom",
   Callback = function()
      LP.CameraMaxZoomDistance = 10000
      Rayfield:Notify({Title = "Zoom", Content = "Max Zoom set to 10,000!", Duration = 3})
   end,
})

--- [MISC & WORLD] ---

MiscTab:CreateButton({
   Name = "Touch to Fling",
   Callback = function()
      loadstring(game:HttpGet("https://pastebin.com/raw/LgZwZ7ZB",true))()
      Rayfield:Notify({Title = "Execution", Content = "Touch to Fling Activated!", Duration = 3})
   end,
})

MiscTab:CreateButton({
   Name = "Fly GUI V3",
   Callback = function()
      loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Fly-V3-X-132770"))()
      Rayfield:Notify({Title = "Execution", Content = "New Fly GUI V3 Loaded!", Duration = 3})
   end,
})

MiscTab:CreateButton({
   Name = "Invisible (FE)",
   Callback = function() loadstring(game:HttpGet('https://pastebin.com/raw/3Rnd9rHf'))() end,
})

MiscTab:CreateButton({
   Name = "Get KurtKPZ TP Tool",
   Callback = function()
      local mouse = LP:GetMouse()
      local tool = Instance.new("Tool")
      tool.RequiresHandle = false; tool.Name = "KurtKPZ TP Tool"
      tool.Activated:Connect(function() LP.Character:MoveTo(mouse.Hit.p + Vector3.new(0, 3, 0)) end)
      tool.Parent = LP.Backpack
      Rayfield:Notify({Title = "Inventory", Content = "TP Tool Added!", Duration = 3})
   end,
})

Rayfield:LoadConfiguration()
