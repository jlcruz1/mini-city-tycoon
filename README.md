-- Load Ghost GUI Library
loadstring(game:HttpGet('https://raw.githubusercontent.com/GhostPlayer352/UI-Library/refs/heads/main/Ghost%20Gui'))()

-- Wait for GUI to load and change the title
local gui = game.CoreGui:WaitForChild("GhostGui")
gui.MainFrame.Title.Text = "Elizabeth Menu"

----------------------------------------------------------------
-- Small helper: beautify frames/buttons
----------------------------------------------------------------
local function beautify(frame)
    local ok, _ = pcall(function()
        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(0, 8)
        corner.Parent = frame
    end)
    pcall(function()
        local shadow = Instance.new("ImageLabel")
        shadow.Name = "Shadow"
        shadow.AnchorPoint = Vector2.new(0.5, 0.5)
        shadow.Position = UDim2.new(0.5, 0, 0.5, 0)
        shadow.Size = UDim2.new(1, 20, 1, 20)
        shadow.BackgroundTransparency = 1
        shadow.Image = "rbxassetid://1316045217"
        shadow.ImageColor3 = Color3.new(0, 0, 0)
        shadow.ImageTransparency = 0.55
        shadow.ScaleType = Enum.ScaleType.Slice
        shadow.SliceCenter = Rect.new(10, 10, 118, 118)
        shadow.ZIndex = -1
        shadow.Parent = frame
    end)
end
beautify(gui.MainFrame)

----------------------------------------------------------------
-- Close (X) Button - hides GUI
----------------------------------------------------------------
local closeButton = Instance.new("TextButton")
closeButton.Parent = gui.MainFrame
closeButton.Size = UDim2.new(0, 26, 0, 26)
closeButton.Position = UDim2.new(1, -36, 0, 8)
closeButton.BackgroundColor3 = Color3.fromRGB(220, 20, 60)
closeButton.Text = "✕"
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
-- Hotkey to Reopen GUI (Right Shift)
----------------------------------------------------------------
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.RightShift then
        gui.Enabled = not gui.Enabled
    end
end)

----------------------------------------------------------------
-- Keep a reference for last-captured remote calls
----------------------------------------------------------------
local Capture = {
    active = false,
    last = nil,       -- { remote = <Instance>, method = "FireServer"|"InvokeServer", args = { ... } }
    captured = {},    -- array of captured entries
}

----------------------------------------------------------------
-- Robust hooking: try to hook __namecall to capture FireServer / InvokeServer
----------------------------------------------------------------
local hooked = false
do
    local success, err = pcall(function()
        -- prefer hookmetamethod if available (exploit env)
        if type(hookmetamethod) == "function" then
            local old = hookmetamethod(game, "__namecall", function(self, ...)
                local method = getnamecallmethod()
                local args = {...}
                if (method == "FireServer" or method == "InvokeServer") and Capture.active then
                    -- record a shallow copy (weak refs not needed here)
                    table.insert(Capture.captured, {remote = self, method = method, args = args})
                    Capture.last = {remote = self, method = method, args = args}
                end
                return old(self, ...)
            end)
            hooked = true
        else
            -- fallback: try to use getrawmetatable hooking if available
            local mt = getrawmetatable(game)
            if mt and type(mt) == "table" and mt.__namecall then
                local oldNamecall = mt.__namecall
                setreadonly(mt, false)
                mt.__namecall = newcclosure(function(self, ...)
                    local method = getnamecallmethod()
                    local args = {...}
                    if (method == "FireServer" or method == "InvokeServer") and Capture.active then
                        table.insert(Capture.captured, {remote = self, method = method, args = args})
                        Capture.last = {remote = self, method = method, args = args}
                    end
                    return oldNamecall(self, ...)
                end)
                setreadonly(mt, true)
                hooked = true
            end
        end
    end)
    -- ignore error; if unable to hook, we'll still provide manual scanning and user-driven approach
end

----------------------------------------------------------------
-- UI Buttons (use AddContent from your GUI lib)
----------------------------------------------------------------
-- Max Level
AddContent("TextButton", "Max Level", [[
    local currentFileValue = game:GetService("Players").LocalPlayer.CurrentFile.Value
    game:GetService("Players").LocalPlayer.Data.Files[tostring(currentFileValue)].Level.Value = 20
]])

-- Free VIP
AddContent("TextButton", "Free VIP", [[
    game:GetService("Players").LocalPlayer.Data.Passes.Pass_VIP.Value = true
]])

-- Get Passes
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
-- Add Money: Auto-scan by name (fallback)
----------------------------------------------------------------
AddContent("TextButton", "Auto Fire Found", [[
    local Players = game:GetService("Players")
    local player = Players.LocalPlayer
    local found = 0
    for _, v in pairs(game:GetDescendants()) do
        if v:IsA("RemoteEvent") or v:IsA("RemoteFunction") then
            local name = string.lower(v.Name)
            if string.find(name, "cash") or string.find(name, "money") or string.find(name, "reward") or string.find(name, "give") then
                found = found + 1
                pcall(function()
                    if v:IsA("RemoteEvent") then
                        v:FireServer(1000000)
                    else
                        v:InvokeServer(1000000)
                    end
                end)
            end
        end
    end
    game.StarterGui:SetCore("SendNotification", {
        Title = "Auto Fire";
        Text = "Attempted "..found.." remote(s).";
        Duration = 5;
    })
]])

----------------------------------------------------------------
-- Remote Capture Controls
----------------------------------------------------------------
AddContent("TextButton", "Start Capture", [[
    getgenv()._EL_CAP_ACTIVE = true
    local Players = game:GetService("Players")
    game.StarterGui:SetCore("SendNotification", {
        Title = "Capture";
        Text = "Capture started. Now perform an action that gives money.";
        Duration = 5;
    })
]])

AddContent("TextButton", "Stop Capture", [[
    getgenv()._EL_CAP_ACTIVE = false
    game.StarterGui:SetCore("SendNotification", {
        Title = "Capture";
        Text = "Capture stopped.";
        Duration = 4;
    })
]])

-- Use Last Captured Remote with prompt for amount
AddContent("TextButton", "Use Last Remote", [[
    local Players = game:GetService("Players")
    local player = Players.LocalPlayer

    -- read capture from global (bridge)
    local cap = getgenv()._EL_CAPTURED_LAST
    if not cap then
        game.StarterGui:SetCore("SendNotification", {
            Title = "Use Last Remote";
            Text = "No captured remote found. Start Capture first and perform an earning action.";
            Duration = 5;
        })
        return
    end

    -- prompt for amount (simple InputDialog)
    local amount = 0
    local success, input = pcall(function()
        return game:GetService("Players").LocalPlayer:WaitForChild("PlayerGui") -- placeholder to avoid error
    end)
    -- Since we cannot rely on fancy prompts from all exploit envs, use a quick default amount and notify user
    amount = 1000000

    -- attempt to reuse the same remote and argument structure while replacing the first numeric argument (if present)
    local remote = cap.remote
    local method = cap.method
    local args = {}
    for i,v in ipairs(cap.args) do args[i] = v end

    -- try to find a numeric arg to modify; otherwise append
    local replaced = false
    for i,v in ipairs(args) do
        if type(v) == "number" then
            args[i] = amount
            replaced = true
            break
        end
    end
    if not replaced then
        table.insert(args, amount)
    end

    pcall(function()
        if method == "FireServer" then
            remote:FireServer(unpack(args))
        else
            remote:InvokeServer(unpack(args))
        end
    end)

    game.StarterGui:SetCore("SendNotification", {
        Title = "Use Last Remote";
        Text = "Tried firing captured remote with amount: "..tostring(amount);
        Duration = 5;
    })
]])

----------------------------------------------------------------
-- small background task: bridge capture globals between hooked code and AddContent closures
----------------------------------------------------------------
-- Hooking in a separate thread so we can set global states used by the UI buttons above
task.spawn(function()
    -- map global flags to local Capture table so AddContent closures can alter them
    while true do
        -- update Capture.active from global flag if present
        if getgenv()._EL_CAP_ACTIVE ~= nil then
            Capture.active = getgenv()._EL_CAP_ACTIVE == true
            if Capture.active then
                -- clear previously captured list to avoid confusion
                Capture.captured = {}
                Capture.last = nil
                getgenv()._EL_CAPTURED_LAST = nil
            end
            getgenv()._EL_CAP_ACTIVE = Capture.active
        end

        -- if a new capture entry is recorded by the hook, export it to global for the UI to use
        if #Capture.captured > 0 then
            local last = Capture.captured[#Capture.captured]
            -- store only minimal safe info: keep the instance and args
            getgenv()._EL_CAPTURED_LAST = { remote = last.remote, method = last.method, args = last.args }
            -- stop auto-collecting multiple to keep it simple
            -- (you can press Stop Capture manually)
            -- don't clear captured here; allow user to stop to preserve history if needed
        end

        task.wait(0.8)
    end
end)

----------------------------------------------------------------
-- Always Day Toggle
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
-- Footer Text -> Elizabeth
----------------------------------------------------------------
local TextLabel = AddContent("TextLabel")
TextLabel.Text = "Elizabeth"

----------------------------------------------------------------
-- Anti-AFK
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
