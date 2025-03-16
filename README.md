local tycoonLists = {
    [5142372758] = { -- Jogo 1 (Ordem de prioridade)
        "Robotic", "Magic", "Storm", "Stone", "Spike", "Strong", "Web", "Dark", "Insanity", "Giant"
    }
}

local allowedTycoons = tycoonLists[game.PlaceId] or {}

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local function PlayerHasBase()
    for _, tycoon in pairs(workspace.Tycoons:GetChildren()) do
        local ownerObject = tycoon:FindFirstChild("isim")
        if ownerObject and ownerObject.Value == LocalPlayer.Name then
            return true
        end
    end
    return false
end

local function ClaimTycoon(name)
    local tycoon = workspace.Tycoons:FindFirstChild(name)
    local claimDoor = tycoon and tycoon:FindFirstChild("Door")
    local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")

    if claimDoor and root then
        for _, v in pairs(claimDoor:GetChildren()) do
            if v:IsA("BasePart") then
                firetouchinterest(root, v, 0)
                firetouchinterest(root, v, 1)
            end
        end
    end
end

local function FindAndClaimTycoon()
    while not PlayerHasBase() do -- Loop contínuo até pegar um tycoon
        for _, name in ipairs(allowedTycoons) do
            local tycoon = workspace.Tycoons:FindFirstChild(name)
            if tycoon then
                local ownerObject = tycoon:FindFirstChild("isim")
                if ownerObject and not Players:FindFirstChild(ownerObject.Value) then
                    ClaimTycoon(name)

                    -- Verifica se conseguiu pegar
                    wait(0.5) -- Pequeno delay para garantir que a verificação ocorra corretamente
                    if PlayerHasBase() then
                        return
                    end
                end
            end
        end
        wait(0.5) -- Evita sobrecarga no loop
    end
end

-- Monitora o renascimento do personagem e sempre tenta pegar um tycoon se necessário
LocalPlayer.CharacterAdded:Connect(function()
    repeat wait() until LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    FindAndClaimTycoon()
end)

-- Se o personagem já estiver carregado, tenta pegar o tycoon imediatamente
if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
    FindAndClaimTycoon()
end
