-- Blox Fruits Multi-Feature Script by Grok 3 (xAI)
local player = game.Players.LocalPlayer
local root = player.Character:WaitForChild("HumanoidRootPart")
local humanoid = player.Character:WaitForChild("Humanoid")
local autoFarm, menuVisible = false, true

-- GUI
local gui = Instance.new("ScreenGui", player.PlayerGui)
local frame = Instance.new("Frame", gui)
frame.Size, frame.Position, frame.BackgroundColor3, frame.Draggable = UDim2.new(0, 220, 0, 300), UDim2.new(0.5, -110, 0.5, -150), Color3.fromRGB(30, 30, 30), true

-- Logo
local logo = Instance.new("TextLabel", frame)
logo.Size, logo.Position, logo.Text, logo.TextColor3, logo.TextSize = UDim2.new(0, 200, 0, 40), UDim2.new(0, 10, 0, 10), "Grok Hub", Color3.fromRGB(0, 255, 0), 20

-- Button function
local function btn(pos, text, color, callback)
    local b = Instance.new("TextButton", frame)
    b.Size, b.Position, b.Text, b.BackgroundColor3, b.TextColor3 = UDim2.new(0, 200, 0, 40), pos, text, color, Color3.fromRGB(255, 255, 255)
    b.MouseButton1Click:Connect(callback)
    return b
end

-- Auto Farm
local farmBtn = btn(UDim2.new(0, 10, 0, 60), "Auto Farm: OFF", Color3.fromRGB(255, 0, 0), function()
    autoFarm = not autoFarm
    farmBtn.Text = autoFarm and "Auto Farm: ON" or "Auto Farm: OFF"
    farmBtn.BackgroundColor3 = autoFarm and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
    if autoFarm then task.spawn(function()
        while autoFarm and root and humanoid.Health > 0 do
            for _, e in pairs(game.Workspace.Enemies:GetChildren()) do
                if e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 then
                    root.CFrame = e.HumanoidRootPart.CFrame * CFrame.new(0, 5, 0)
                    game:GetService("VirtualUser"):ClickButton1(Vector2.new())
                    task.wait(0.5)
                end
            end
            game.ReplicatedStorage.Remotes.CommF_:InvokeServer("AddPoint", "Melee", 1) -- Auto Stats
            task.wait(1)
        end
    end) end
end)

-- Teleport
local tpBtn = btn(UDim2.new(0, 10, 0, 110), "Teleport Middle Town", Color3.fromRGB(0, 255, 0), function()
    root.CFrame = CFrame.new(-650, 10, 1370)
end)

-- Auto Quest
local questBtn = btn(UDim2.new(0, 10, 0, 160), "Auto Quest", Color3.fromRGB(0, 255, 0), function()
    for _, npc in pairs(game.Workspace.NPCs:GetChildren()) do
        if npc:FindFirstChild("HumanoidRootPart") and (npc.HumanoidRootPart.Position - root.Position).Magnitude < 20 then
            game.ReplicatedStorage.Remotes.CommF_:InvokeServer("StartQuest", npc.Name, 1)
            break
        end
    end
end)

-- Toggle Menu
local toggleBtn = btn(UDim2.new(0, 10, 0, 250), "Close", Color3.fromRGB(255, 165, 0), function()
    menuVisible = not menuVisible
    frame.Visible = menuVisible
    toggleBtn.Text = menuVisible and "Close" or "Open"
end)

-- Respawn handler
player.CharacterAdded:Connect(function(newChar)
    root = newChar:WaitForChild("HumanoidRootPart")
    humanoid = newChar:WaitForChild("Humanoid")
end)

print("Grok Hub Multi-Feature Loaded!")
