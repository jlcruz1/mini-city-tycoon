-- Load Ghost GUI
loadstring(game:HttpGet('https://raw.githubusercontent.com/GhostPlayer352/UI-Library/refs/heads/main/Ghost%20Gui'))()

-- Wait for GUI to load and change the title
local gui = game.CoreGui:WaitForChild("GhostGui")
gui.MainFrame.Title.Text = "Mini City Tycoon"

-- Button: Max Level
AddContent("TextButton", "Max Level", [[
    local currentFileValue = game:GetService("Players").LocalPlayer.CurrentFile.Value
    game:GetService("Players").LocalPlayer.Data.Files[tostring(currentFileValue)].Level.Value = 20
]])

-- Button: Free VIP
AddContent("TextButton", "Free VIP", [[
    game:GetService("Players").LocalPlayer.Data.Passes.Pass_VIP.Value = true
]])

-- Button: Get All Passes
AddContent("TextButton", "Get Passes", [[
    local passes = game:GetService("Players").LocalPlayer.Data.Passes
    passes.Pass_X2Level.Value = true
    passes.Pass_X2Cash.Value = true
    passes.Pass_Paint.Value = true
    passes.Pass_MoreHeight.Value = true
    passes.Pass_DisableGrid.Value = true
    passes.Pass_DisableCollision.Value = true
]])

-- Toggle: Always Day
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

-- Fixed text at the bottom
local TextLabel = AddContent("TextLabel")
TextLabel.Text = "FELIPEHUB"

----------------------------------------------------------------
-- ✅ Anti-AFK Script
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
