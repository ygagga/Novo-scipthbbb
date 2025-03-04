-- INÍCIO: Configuração Inicial
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

-- Função para começar a espectar o jogador
local function startSpectating(targetPlayer)
    -- Verifica se o personagem do jogador alvo está carregado
    if targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local targetCharacter = targetPlayer.Character
        local targetHumanoid = targetCharacter:FindFirstChild("Humanoid")

        -- Desativa a física do jogador local para que ele não se mova
        character.HumanoidRootPart.Anchored = true
        character.HumanoidRootPart.CanCollide = false

        -- Atravessa a câmera para o personagem do alvo
        game.Workspace.CurrentCamera.CameraSubject = targetHumanoid
        game.Workspace.CurrentCamera.CameraType = Enum.CameraType.Attach
        
        -- Exibe o nome do jogador na tela enquanto está assistindo
        local espLabel = Instance.new("BillboardGui")
        espLabel.Adornee = targetCharacter.HumanoidRootPart
        espLabel.Size = UDim2.new(0, 100, 0, 50)
        espLabel.StudsOffset = Vector3.new(0, 3, 0)
        espLabel.AlwaysOnTop = true
        espLabel.Parent = game.Players.LocalPlayer.PlayerGui

        local nameLabel = Instance.new("TextLabel")
        nameLabel.Text = targetPlayer.Name
        nameLabel.Size = UDim2.new(1, 0, 1, 0)
        nameLabel.BackgroundTransparency = 1
        nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        nameLabel.TextStrokeTransparency = 0.8
        nameLabel.Parent = espLabel

        -- Opcional: Adicionar uma forma de voltar para o modo normal
        -- Exemplo: Ao apertar a tecla "Q" para parar de espectar
        game:GetService("UserInputService").InputBegan:Connect(function(input)
            if input.KeyCode == Enum.KeyCode.Q then
                stopSpectating() -- Chamando a função para parar de espectar
            end
        end)
    end
end

-- Função para parar de espectar e voltar ao controle normal
local function stopSpectating()
    -- Restaura a física do personagem local
    character.HumanoidRootPart.Anchored = false
    character.HumanoidRootPart.CanCollide = true

    -- Restaura a câmera para o jogador
    game.Workspace.CurrentCamera.CameraSubject = character:FindFirstChild("Humanoid")
    game.Workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
end

-- Função para selecionar o jogador (pode ser através de comando ou interface personalizada)
local function onCommandReceived(command)
    local targetPlayerName = string.match(command, "^!espectar%s(.+)$") -- Comando para espectar um jogador, ex: !espectar NomeDoJogador
    if targetPlayerName then
        -- Encontrar o jogador com o nome especificado
        local targetPlayer = game.Players:FindFirstChild(targetPlayerName)
        if targetPlayer then
            startSpectating(targetPlayer)
        else
            print("Jogador não encontrado!")
        end
    end
end

-- Detectar entrada de comando no chat
game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.OnMessageDoneFiltering.OnClientEvent:Connect(function(message)
    -- Quando o jogador enviar um comando no chat, verifica se é o comando de espectar
    onCommandReceived(message)
end)

-- FIM: Finalização e Mensagem de Log
print("Script carregado com sucesso! Você pode usar o comando !espectar NomeDoJogador para começar a espectar.")
