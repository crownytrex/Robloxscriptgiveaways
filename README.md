# Robloxscriptgiveaways

```
-- Initialize the GUI
local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "AdminGui"

-- Create Frame
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 200)
frame.Position = UDim2.new(0.5, -150, 0.5, -100)
frame.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
frame.BorderSizePixel = 0

-- Add a title label
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = "Admin Panel"
title.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.SourceSans
title.TextSize = 24

-- Create TextBox for Username Input
local usernameBox = Instance.new("TextBox", frame)
usernameBox.Size = UDim2.new(0.8, 0, 0, 30)
usernameBox.Position = UDim2.new(0.1, 0, 0.3, 0)
usernameBox.PlaceholderText = "Enter username"
usernameBox.Text = ""
usernameBox.Font = Enum.Font.SourceSans
usernameBox.TextSize = 18
usernameBox.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
usernameBox.TextColor3 = Color3.new(1, 1, 1)

-- Create Fly Button
local flyButton = Instance.new("TextButton", frame)
flyButton.Size = UDim2.new(0.8, 0, 0, 30)
flyButton.Position = UDim2.new(0.1, 0, 0.5, 0)
flyButton.Text = "Fly"
flyButton.Font = Enum.Font.SourceSans
flyButton.TextSize = 18
flyButton.BackgroundColor3 = Color3.new(0.2, 0.5, 0.2)
flyButton.TextColor3 = Color3.new(1, 1, 1)

-- Create Kick Button
local kickButton = Instance.new("TextButton", frame)
kickButton.Size = UDim2.new(0.8, 0, 0, 30)
kickButton.Position = UDim2.new(0.1, 0, 0.7, 0)
kickButton.Text = "Kick"
kickButton.Font = Enum.Font.SourceSans
kickButton.TextSize = 18
kickButton.BackgroundColor3 = Color3.new(0.5, 0.2, 0.2)
kickButton.TextColor3 = Color3.new(1, 1, 1)

-- Flying Functionality
local flying = false
local bodyVelocity, bodyGyro

local function toggleFly()
    flying = not flying
    local character = player.Character
    local humanoid = character and character:FindFirstChild("HumanoidRootPart")

    if flying then
        if humanoid then
            -- Add BodyVelocity and BodyGyro to enable flight
            bodyVelocity = Instance.new("BodyVelocity", humanoid)
            bodyGyro = Instance.new("BodyGyro", humanoid)
            bodyVelocity.MaxForce = Vector3.new(1e4, 1e4, 1e4)
            bodyGyro.MaxTorque = Vector3.new(1e4, 1e4, 1e4)
            bodyVelocity.Velocity = Vector3.new(0, 50, 0) -- Fly upwards
        end
    else
        -- Stop flying and clean up
        if bodyVelocity then bodyVelocity:Destroy() end
        if bodyGyro then bodyGyro:Destroy() end
    end
end

flyButton.MouseButton1Click:Connect(toggleFly)

-- Kick Functionality
local function kickPlayer()
    local targetUsername = usernameBox.Text
    local targetPlayer = game.Players:FindFirstChild(targetUsername)

    if targetPlayer then
        targetPlayer:Kick("You have been kicked by an admin.")
    else
        warn("Player not found: " .. targetUsername)
    end
end

kickButton.MouseButton1Click:Connect(kickPlayer)

```
