local ui = loadstring(game:HttpGet("https://raw.githubusercontent.com/bloodball/-back-ups-for-libs/main/turtle"))()

local Players = game:GetService("Players")
local RepStorage = game:GetService("ReplicatedStorage")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")
local remotes = RepStorage:WaitForChild("Remotes")
local remoteFunction = remotes:FindFirstChild("CommF_")
local remoteEvent = remotes:FindFirstChild("CommE_")

local autoFarmActive = false
local autoBountyActive = false
local addingStats = false

local window = ui:Window("LilBro Hub")

remoteFunction:InvokeServer("Buso")

local function findTool(name)
    return character:FindFirstChild(name) or player.Backpack:FindFirstChild(name)
end

local function equipTool(name)
    local tool = findTool(name)
    if tool and tool.Parent ~= character then
        humanoid:EquipTool(tool)
        task.wait()
        return findTool(name)
    elseif tool and tool.Parent == character then
        return tool
    end
end

local function Attack(target)
    if not target or not target:FindFirstChild("HumanoidRootPart") then return end
    local tool = equipTool("Dragon-Dragon")
    local attackRemote = tool and tool:FindFirstChild("LeftClickRemote")
    local startTime = tick()
    while (autoFarmActive or autoBountyActive) and target.Parent and target.Humanoid.Health > 0 and humanoid.Health > 0 and tick() - startTime < 10 do
        rootPart.CFrame = target.HumanoidRootPart.CFrame
        attackRemote:FireServer(Vector3.new(-0.3, 0, -1), 1)
        task.wait()
    end
end

local function AttackMob(name)
    local tool = equipTool("Dragon-Dragon")
    if not tool then return end
    local attackRemote = tool:FindFirstChild("LeftClickRemote")
    sethiddenproperty(player, "SimulationRadius", math.huge)
    local startTime = tick()
    while (autoFarmActive or autoBountyActive) and humanoid.Health > 0 and tick() - startTime < 10 do
        local enemies = workspace:FindFirstChild("Enemies")
        if enemies then
            for _, mob in pairs(enemies:GetChildren()) do
                if mob.Name == name then
                    local hrp = mob:FindFirstChild("HumanoidRootPart")
                    local h = mob:FindFirstChild("Humanoid")
                    if hrp and h and h.Health > 0 then
                        hrp.CFrame = rootPart.CFrame * CFrame.new(math.random(0,5), 0, math.random(-5,0))
                        hrp.CanCollide = false
                        attackRemote:FireServer(Vector3.new(-0.3, 0, -1), 1)
                        task.wait()
                    end
                end
            end
        end
        task.wait()
    end
end

window:Toggle("Auto Farm (1-700)", false, function(state)
    autoFarmActive = state
    if state then
        remoteFunction:InvokeServer("SwitchFruit", "Dragon-Dragon", "West")
        rootPart.CFrame = CFrame.new(5313, 44, 4757)
        task.spawn(function()
            while autoFarmActive do
                if player.Data.Level.Value >= 700 then
                    game:GetService("TeleportService"):Teleport(85997647791174,player)
                    autoFarmActive = false
                    break
                end
                local enemies = workspace:FindFirstChild("Enemies")
                if enemies then
                    for _, e in pairs(enemies:GetChildren()) do
                        if e.Name == "Galley Captain" and e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 and e:FindFirstChild("HumanoidRootPart") then
                            AttackMob("Galley Captain")
                        end
                    end
                end
                task.wait()
            end
        end)

        if not addingStats then
            addingStats = true
            task.spawn(function()
                while autoFarmActive do
                    remoteFunction:InvokeServer("AddPoint", "Demon Fruit", 9999)
                    task.wait()
                end
                addingStats = false
            end)
        end
    end
end)

window:Toggle("Auto Farm (700-1500)", false, function(state)
    autoFarmActive = state
    if state then
        remoteFunction:InvokeServer("SwitchFruit", "Dragon-Dragon", "West")
        rootPart.CFrame = CFrame.new(-3174, 299, -10568)
        task.spawn(function()
            while autoFarmActive do
                if player.Data.Level.Value >= 1500 then
                    autoFarmActive = false
                    break
                end
                local enemies = workspace:FindFirstChild("Enemies")
                if enemies then
                    for _, e in pairs(enemies:GetChildren()) do
                        if e.Name == "Water Fighter" and e:FindFirstChild("Humanoid") and e.Humanoid.Health > 0 and e:FindFirstChild("HumanoidRootPart") then
                            AttackMob("Water Fighter")
                        end
                    end
                end
                task.wait()
            end
        end)

        if not addingStats then
            addingStats = true
            task.spawn(function()
                while autoFarmActive do
                    remoteFunction:InvokeServer("AddPoint", "Demon Fruit", 1)
                    task.wait()
                end
                addingStats = false
            end)
        end
    end
end)

window:Toggle("Auto Bounty", false, function(state)
    autoBountyActive = state
    if state then
        remoteFunction:InvokeServer("SwitchFruit", "Dragon-Dragon", "West")
        task.spawn(function()
            while autoBountyActive do
                local targetPlayer, minDist = nil, math.huge
                for _, pl in ipairs(Players:GetPlayers()) do
                    if pl ~= player and pl.Character and pl.Character:FindFirstChild("Humanoid") and pl.Character.Humanoid.Health > 0 and pl.Character:FindFirstChild("HumanoidRootPart") then
                        local d = (rootPart.Position - pl.Character.HumanoidRootPart.Position).Magnitude
                        if d < minDist then
                            minDist, targetPlayer = d, pl
                        end
                    end
                end
                if targetPlayer and targetPlayer.Character then
                    Attack(targetPlayer.Character)
                else
                    task.wait()
                end
                task.wait()
            end
        end)
    end
end)

window:Toggle("Modo Leve", false, function(state)
    if state then
        game:GetService("Lighting").GlobalShadows = false
        game:GetService("Lighting").FogEnd = 100000
        game:GetService("Lighting").Brightness = 1
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") or v:IsA("Fire") then
                v.Enabled = false
            elseif v:IsA("Decal") then
                v.Transparency = 1
            end
        end
        settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
    else
        game:GetService("Lighting").GlobalShadows = true
        game:GetService("Lighting").FogEnd = 1000
        game:GetService("Lighting").Brightness = 2
        settings().Rendering.QualityLevel = Enum.QualityLevel.Automatic
    end
end)

window:Label("By : LilBro", Color3.fromRGB(255, 0, 0))
