# ESP


```
-- Script to create a button and highlight all humanoid parts for all players

-- Function to highlight humanoid parts of a given character
local function highlightHumanoidParts(character)
    -- Check if character is valid and has a humanoid
    if character and character:FindFirstChild("Humanoid") then
        -- Loop through all parts in the character
        for _, part in pairs(character:GetChildren()) do
            -- Check if the part is a part of the humanoid (e.g. head, torso, limbs)
            if part:IsA("BasePart") then
                -- Create a Highlight instance if not already present
                if not part:FindFirstChild("Highlight") then
                    local highlight = Instance.new("Highlight")
                    highlight.Parent = part
                    highlight.Adornee = part
                    highlight.FillColor = Color3.fromRGB(0, 255, 0)  -- Green color
                    highlight.OutlineColor = Color3.fromRGB(0, 255, 0)  -- Green outline
                    highlight.FillTransparency = 0.5  -- Set transparency for the highlight
                    highlight.OutlineTransparency = 0.5  -- Set transparency for the outline
                end
            end
        end
    end
end

-- Function to remove highlights from all players
local function removeAllHighlights()
    for _, player in pairs(game.Players:GetPlayers()) do
        local character = player.Character
        if character then
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("BasePart") and part:FindFirstChild("Highlight") then
                    part.Highlight:Destroy()  -- Remove the highlight
                end
            end
        end
    end
end

-- Create a ScreenGui for the ESP Button
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")  -- Ensure the PlayerGui exists

-- Create the ESP Button
local espButton = Instance.new("TextButton")
espButton.Parent = screenGui
espButton.Size = UDim2.new(0, 200, 0, 50)  -- Size of the button
espButton.Position = UDim2.new(0, 10, 1, -60)  -- Positioned at the bottom-left of the screen
espButton.BackgroundColor3 = Color3.fromRGB(128, 128, 128)  -- Grey background
espButton.Text = "ESP"  -- Button text
espButton.TextColor3 = Color3.fromRGB(255, 255, 255)  -- White text
espButton.Font = Enum.Font.SourceSansBold  -- Font style
espButton.TextSize = 24  -- Text size
espButton.TextStrokeTransparency = 0.8  -- Stroke transparency to make the text clearer

-- Function to handle button click (toggle ESP)
local espActive = false
espButton.MouseButton1Click:Connect(function()
    if espActive then
        -- If ESP is active, remove highlights
        removeAllHighlights()
    else
        -- If ESP is inactive, highlight all players' humanoid parts
        for _, player in pairs(game.Players:GetPlayers()) do
            -- Wait for player character to be loaded
            local character = player.Character or player:WaitForChild("Character")
            highlightHumanoidParts(character)
            
            -- If the player dies and respawns, re-apply the highlight
            player.CharacterAdded:Connect(function(char)
                highlightHumanoidParts(char)
            end)
        end
    end
    
    -- Toggle ESP state
    espActive = not espActive
end)

-- Function to initialize the highlights for all players when they join
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        if espActive then
            highlightHumanoidParts(character)
        end
    end)
end)

-- Ensure that all existing players' humanoid parts are highlighted if ESP is enabled
for _, player in pairs(game.Players:GetPlayers()) do
    if player.Character then
        highlightHumanoidParts(player.Character)
    end
end
```
