# BATMAN SCRIPT
# PRESS E FOR BATARANG
```
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local inputService = game:GetService("UserInputService")
local runService = game:GetService("RunService")
local debris = game:GetService("Debris")

-- Function to give the player infinite health
local function giveInfiniteHealth()
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:FindFirstChildOfClass("Humanoid")

    if humanoid then
        humanoid.MaxHealth = math.huge -- Set maximum health to infinity
        humanoid.Health = math.huge -- Set current health to infinity
        print("Infinite health granted!")
    end
end

-- Function to create a black splash effect
local function createSplash(position)
    for i = 1, 10 do
        local splashPart = Instance.new("Part")
        splashPart.Size = Vector3.new(0.5, 0.5, 0.5)
        splashPart.Color = Color3.new(0, 0, 0) -- Black
        splashPart.Material = Enum.Material.SmoothPlastic
        splashPart.Anchored = false
        splashPart.CanCollide = false
        splashPart.Position = position + Vector3.new(math.random(-2, 2), 0, math.random(-2, 2))
        splashPart.Velocity = Vector3.new(math.random(-10, 10), math.random(5, 15), math.random(-10, 10))
        splashPart.Parent = workspace

        debris:AddItem(splashPart, 2) -- Clean up splash parts after 2 seconds
    end
end

-- Function to handle batarang behavior
local function createBatarang(origin, direction)
    -- Create the batarang part
    local batarang = Instance.new("Part")
    batarang.Size = Vector3.new(1, 0.2, 2)
    batarang.Color = Color3.new(0, 0, 0) -- Black
    batarang.Material = Enum.Material.SmoothPlastic
    batarang.Anchored = false
    batarang.CanCollide = false
    batarang.CFrame = CFrame.new(origin, direction)

    -- Add a neon circle to simulate the yellow glow
    local circle = Instance.new("SurfaceGui", batarang)
    circle.Face = Enum.NormalId.Top
    circle.CanvasSize = Vector2.new(200, 200)
    
    local yellowCircle = Instance.new("Frame", circle)
    yellowCircle.AnchorPoint = Vector2.new(0.5, 0.5)
    yellowCircle.Position = UDim2.new(0.5, 0, 0.5, 0)
    yellowCircle.Size = UDim2.new(0.5, 0, 0.5, 0)
    yellowCircle.BackgroundColor3 = Color3.new(1, 1, 0) -- Yellow
    yellowCircle.BackgroundTransparency = 0.3
    yellowCircle.BorderSizePixel = 0

    -- Parent the batarang to the workspace
    batarang.Parent = workspace

    -- Velocity to move the batarang
    local velocity = Instance.new("BodyVelocity")
    velocity.Velocity = (direction - origin).Unit * 50 -- Speed of the batarang
    velocity.MaxForce = Vector3.new(10000, 10000, 10000)
    velocity.P = 1000
    velocity.Parent = batarang

    -- Detect collision
    batarang.Touched:Connect(function(hit)
        local hitParent = hit.Parent

        -- Ignore collisions with the thrower's character
        if hitParent == player.Character then
            return
        end

        -- Check if it hits a humanoid
        local humanoid = hitParent:FindFirstChild("Humanoid")
        if humanoid then
            local hitPlayer = game.Players:GetPlayerFromCharacter(hitParent)

            -- Only damage other players or NPCs
            if hitPlayer and hitPlayer ~= player then
                humanoid:TakeDamage(10) -- Deal 10 damage
                batarang:Destroy() -- Destroy the batarang upon hit
                return
            elseif not hitPlayer then -- For NPCs
                humanoid:TakeDamage(10)
                batarang:Destroy()
                return
            end
        end

        -- Create splash effect if it hits the ground
        if hit:IsA("Part") and hit.Anchored then
            createSplash(batarang.Position)
            batarang:Destroy() -- Destroy the batarang upon impact
        end
    end)

    -- Cleanup after a short time
    debris:AddItem(batarang, 5) -- Remove after 5 seconds if no collision occurs
end

-- Detect keypress
inputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end -- Ignore input if interacting with UI

    if input.KeyCode == Enum.KeyCode.E then
        -- Grant infinite health
        giveInfiniteHealth()

        -- Create a batarang
        local character = player.Character or player.CharacterAdded:Wait()
        local rightHand = character:FindFirstChild("RightHand")

        if rightHand then
            local origin = rightHand.Position -- Spawn from the player's right hand
            local direction = mouse.Hit.p
            createBatarang(origin, direction)
        end
    end
end)
```
