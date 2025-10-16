-- Load Ghost GUI
loadstring(game:HttpGet('https://raw.githubusercontent.com/GhostPlayer352/UI-Library/refs/heads/main/Ghost%20Gui'))()

-- Wait for GUI to load and change the title
local gui = game.CoreGui:WaitForChild("GhostGui")
gui.MainFrame.Title.Text = "Mini City Tycoon"

----------------------------------------------------------------
-- 🛑 Add Close (X) Button
----------------------------------------------------------------
local closeButton = Instance.new("TextButton")
closeButton.Parent = gui.MainFrame
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -30, 0, 5) -- top right corner
closeButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
closeButton.Text = "X"
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.TextScaled = true
closeButton.Font = Enum.Font.GothamBold
closeButton.BorderSizePixel = 0
closeButton.ZIndex = 10

closeButton.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

----------------------------------------------------------------
-- Button: Max Level
----------------------------------------------------------------
AddContent("TextButton", "Max Level", [[
    local currentFileValue = game:GetService("Players").LocalPlayer.CurrentFile.Value
    game:GetService("Players").LocalPlayer.Data.Files[tostring(currentFileValue)].Level.Value = 20
]])

----------------------------------------------------------------
-- Button: Free VIP
----------------------------------------------------------------
AddContent("TextButton", "Free VIP", [[
    game:GetService("Players").LocalPlayer.Data.Passes.Pass_VIP.Value = true
]])

----------------------------------------------------------------
-- Button: Get All Passes
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
-- 💰 Button: Add Money
----------------------------------------------------------------
AddContent("TextButton", "Add Money", [[
    local currentFileValue = game:GetService("Players").LocalPlayer.CurrentFile.Value
    local money = game:GetService("Players").LocalPlayer.Data.Files[tostring(currentFileValue)].Cash
    money.Value = money.Value + 1000000  -- Change amount here 💵
    game.StarterGui:SetCore("SendNotification", {
        Title = "Money Added";
        Text = "+1,000,000 Cash!";
        Duration = 5;
    })
]])

----------------------------------------------------------------
-- ☀️ Toggle: Always Day
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
-- Fixed Text at the Bottom
----------------------------------------------------------------
local TextLabel = AddContent("TextLabel")
TextLabel.Text = "FELIPEHUB"

----------------------------------------------------------------
-- 🛡️ Anti-AFK
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
