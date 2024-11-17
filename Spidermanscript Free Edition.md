--[[ # SpiderMan Script By ._.zyx._. on discord or crownyy_321567

```
-- Create UI
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "WebInstructionsGui"
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Create the frame
local frame = Instance.new("Frame")
frame.Name = "InstructionFrame"
frame.Size = UDim2.new(0.3, 0, 0.1, 0) -- Adjust size
frame.Position = UDim2.new(0.35, 0, 0.9, 0) -- Center-bottom of the screen
frame.BackgroundColor3 = Color3.fromRGB(128, 128, 128) -- Grey color
frame.BackgroundTransparency = 0.2 -- 20% transparency
frame.BorderSizePixel = 0 -- No border
frame.Parent = screenGui

-- Round the edges
local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0.1, 0) -- Rounded corners
uiCorner.Parent = frame

-- Create the text label
local textLabel = Instance.new("TextLabel")
textLabel.Name = "InstructionLabel"
textLabel.Size = UDim2.new(1, 0, 1, 0) -- Full size of the frame
textLabel.Position = UDim2.new(0, 0, 0, 0)
textLabel.BackgroundTransparency = 1 -- Fully transparent background
textLabel.Text = "Press E for Web. Hover mouse on the part you want to go to."
textLabel.TextColor3 = Color3.fromRGB(255, 255, 255) -- White text
textLabel.Font = Enum.Font.Gotham -- Modern font
textLabel.TextScaled = true -- Auto-scale text
textLabel.Parent = frame

local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local player = Players.LocalPlayer

local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")
local mouse = player:GetMouse()

-- Web Configuration
local webSpeed = 100
local webLifetime = 5

-- State variables
local isWebActive = false
local webPart = nil
local ropeConstraint = nil
local heartbeatConnection = nil
local targetPart = nil

-- Function to clean up the web
local function cleanupWeb()
    if webPart then
        webPart:Destroy()
        webPart = nil
    end
    if ropeConstraint then
        ropeConstraint:Destroy()
        ropeConstraint = nil
    end
    if heartbeatConnection then
        heartbeatConnection:Disconnect()
        heartbeatConnection = nil
    end
    rootPart.Velocity = Vector3.zero -- Stop the player's movement
    isWebActive = false
    targetPart = nil
end

-- Function to create an attachment
local function createAttachment(part)
    local attachment = Instance.new("Attachment")
    attachment.Parent = part
    return attachment
end

-- Function to fire the web
local function fireWeb()
    if isWebActive then return end
    isWebActive = true

    -- Get the mouse target position
    local mouseHit = mouse.Hit.Position
    local direction = (mouseHit - rootPart.Position).Unit

    -- Create the web projectile
    webPart = Instance.new("Part")
    webPart.Size = Vector3.new(0.2, 0.2, 0.2)
    webPart.BrickColor = BrickColor.new("Bright red")
    webPart.Material = Enum.Material.Neon
    webPart.Shape = Enum.PartType.Ball
    webPart.CanCollide = false
    webPart.Anchored = false
    webPart.Parent = workspace

    -- Set initial position and velocity
    webPart.CFrame = rootPart.CFrame + (direction * 2)
    local bodyVelocity = Instance.new("BodyVelocity", webPart)
    bodyVelocity.Velocity = direction * webSpeed
    bodyVelocity.MaxForce = Vector3.new(1e6, 1e6, 1e6)

    -- Detect collision with the target part
    local connection
    connection = webPart.Touched:Connect(function(hit)
        if hit:IsDescendantOf(character) then return end

        -- Stop the web and attach it
        bodyVelocity:Destroy()

        -- Create attachments for the rope
        local rootAttachment = rootPart:FindFirstChild("RootRigAttachment") or createAttachment(rootPart)
        local webAttachment = createAttachment(webPart)

        -- Create a rope to pull the character
        ropeConstraint = Instance.new("RopeConstraint")
        ropeConstraint.Attachment0 = rootAttachment
        ropeConstraint.Attachment1 = webAttachment
        ropeConstraint.Visible = true
        ropeConstraint.Color = BrickColor.new("Bright blue") -- Customize rope color
        ropeConstraint.Length = (rootPart.Position - webPart.Position).Magnitude
        ropeConstraint.Parent = workspace

        -- Pull the character towards the web
        heartbeatConnection = RunService.Heartbeat:Connect(function()
            local moveDirection = (webPart.Position - rootPart.Position).Unit
            local distance = (webPart.Position - rootPart.Position).Magnitude
            if distance > 2 then
                rootPart.Velocity = moveDirection * webSpeed
            else
                rootPart.Velocity = Vector3.zero -- Stop movement when close
            end
        end)

        -- Listen for the player touching the target part
        targetPart = hit
        targetPart.Touched:Connect(function(touchedHit)
            if touchedHit:IsDescendantOf(character) then
                cleanupWeb()
                connection:Disconnect()
            end
        end)
    end)

    -- Destroy web after lifetime if it doesn't hit anything
    task.delay(webLifetime, function()
        if isWebActive then
            cleanupWeb()
            connection:Disconnect()
        end
    end)
end

-- Detect keypress
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.E then
        fireWeb()
    end
end)
```

