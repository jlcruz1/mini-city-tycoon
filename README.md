-- Load Ghost GUI Library
loadstring(game:HttpGet('https://raw.githubusercontent.com/GhostPlayer352/UI-Library/refs/heads/main/Ghost%20Gui'))()

-- Wait for GUI to load and change the title
local gui = game.CoreGui:WaitForChild("GhostGui")
gui.MainFrame.Title.Text = "Elizabeth Menu"

----------------------------------------------------------------
-- 🪄 UI Enhancement: Rounded Corners + Shadow
----------------------------------------------------------------
local function beautify(frame)
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = frame

    local shadow = Instance.new("ImageLabel")
    shadow.Name = "Shadow"
    shadow.AnchorPoint = Vector2.new(0.5, 0.5)
    shadow.Position = UDim2.new(0.5, 0, 0.5, 0)
    shadow.Size = UDim2.new(1, 20, 1, 20)
    shadow.BackgroundTransparency = 1
    shadow.Image = "rbxassetid://1316045217"
    shadow.ImageColor3 = Color3.new(0, 0, 0)
    shadow.ImageTransparency = 0.5
    shadow.ScaleType = Enum.ScaleType.Slice
    shadow.SliceCenter = Rect.new(10, 10, 118, 118)
    shadow.ZIndex = -1
    shadow.Parent = frame
end

-- Apply style to main frame
beautify(gui.MainFrame)

----------------------------------------------------------------
-- ❌ Close (X) Button with Better Style
----------------------------------------------------------------
local closeButton = Instance.new("TextButton")
closeButton.Parent = gui.MainFrame
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -35, 0, 8)
closeButton.BackgroundColor3 = Color3.fromRGB(220, 20, 60)
closeButton.Text = "X"
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.TextScaled = true
closeButton.Font = Enum.Font.GothamBold
closeButton.BorderSizePixel = 0
closeButton.ZIndex = 10
beautify(closeButton)

closeButton.MouseEnter:Connect(function()
    closeButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
end)
closeButton.MouseLeave:Connect(function()
    closeButton.BackgroundColor3 = Color3.fromRGB(220, 20, 60)
end)

closeButton.MouseButton1Click:Connect(function()
    gui.Enabled = false
end)

----------------------------------------------------------------
-- ⌨️ Right Shift Hotkey to Reopen GUI
----------------------------------------------------------------
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.RightShift then
        gui.Enabled = not gui.Enabled
    end
end)

----------------------------------------------------------------
-- 🧭 Max Level Button
----------------------------------------------------------------
AddContent("TextButton", "Max Level", [[
    local currentFileValue = game:GetService("Players").LocalPlayer.CurrentFile.Value
    game:GetService("Players").LocalPlayer.Data.Files[tostring(currentFileValue)].Level.Value = 20
]])

----------------------------------------------------------------
-- 🏅 Free VIP Button
----------------------------------------------------------------
AddContent("TextButton", "Free VIP", [[
    game:GetService("Players").LocalPlayer.Data.Passes.Pass_VIP.Value = true
]])

----------------------------------------------------------------
-- 🪙 Get All Passes Button
----------------------------------------------------------------
AddContent("TextButton", "Get Passes", [[
    local passes = game:GetService("Players").LocalPlayer.Data.Passes
    passes.Pass_X2Level.Value = true
    passes.Pass_X2Cash.Value = true
    passes.Pass_Paint.Value = true
    passes.Pass_MoreHeight.Value = true
    passes.Pass_DisableGrid.Value = true
    passes.Pass_DisableCollision.Value = true
]])

----------------------------------------------------------------
-- 💰 Realistic Add Money Button (Tries to Fire Remotes)
----------------------------------------------------------------
AddContent("TextButton", "Add Money", [[
    local Players = game:GetService("Players")
    local player = Players.LocalPlayer
    local found = 0

    for _, v in pairs(game:GetDescendants()) do
        if v:IsA("RemoteEvent") or v:IsA("RemoteFunction") then
            local name = string.lower(v.Name)
            if string.find(name, "cash") or string.find(name, "money") or string.find(name, "reward") then
                found = found + 1
                pcall(function()
                    if v:IsA("RemoteEvent") then
                        v:FireServer(1000000) -- 💵 amount
                    else
                        v:InvokeServer(1000000)
                    end
                end)
            end
        end
    end

    game.StarterGui:SetCore("SendNotification", {
        Title = "Add Money";
        Text = "Fired "..found.." remote(s) for +1,000,000 cash!";
        Duration = 5;
    })
]])

----------------------------------------------------------------
-- ☀️ Always Day Toggle
----------------------------------------------------------------
AddContent("Toogle", "Always Day", [[
    getgenv().AlwaysDay = true
    while getgenv().AlwaysDay do
        game.Lighting.ClockTime = 6
        task.wait(1)
    end
]], [[
    getgenv().AlwaysDay = false
    game.Lighting.ClockTime = 20
]])

----------------------------------------------------------------
-- 📌 Fixed Text at the Bottom (Changed to Elizabeth)
----------------------------------------------------------------
local TextLabel = AddContent("TextLabel")
TextLabel.Text = "Elizabeth"

----------------------------------------------------------------
-- 🛡️ Anti-AFK Button
----------------------------------------------------------------
AddContent("TextButton", "Anti-AFK", [[
    local VirtualUser = game:GetService("VirtualUser")
    game:GetService("Players").LocalPlayer.Idled:Connect(function()
        VirtualUser:Button2Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
        task.wait(1)
        VirtualUser:Button2Up(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
    end)
    game.StarterGui:SetCore("SendNotification", {
        Title = "Anti-AFK";
        Text = "Anti-AFK activated successfully!";
        Duration = 5;
    })
]])
