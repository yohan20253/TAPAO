local Tool = script.Parent
local Handle = Tool:WaitForChild("Handle")
local Players = game:GetService("Players")
local Debris = game:GetService("Debris")

local danoSlap = 20
local empurraoForca = 80
local tempoEntreTapaGlobal = 1 -- segundos entre cada tapa global

local podeBater = true

-- Função para aplicar tapa em todos (menos o atacante)
local function aplicarTapaGlobal(atacante)
	if not podeBater then return end
	podeBater = false

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= atacante and player.Character and player.Character:FindFirstChild("Humanoid") and player.Character:FindFirstChild("HumanoidRootPart") then
			local humanoid = player.Character.Humanoid
			humanoid:TakeDamage(danoSlap)

			-- Empurrão
			local bv = Instance.new("BodyVelocity")
			bv.Velocity = Vector3.new(0, 20, 0) + atacante.Character.HumanoidRootPart.CFrame.LookVector * empurraoForca
			bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
			bv.P = 1e5
			bv.Parent = player.Character.HumanoidRootPart
			Debris:AddItem(bv, 0.25)
		end
	end

	task.delay(tempoEntreTapaGlobal, function()
		podeBater = true
	end)
end

-- Detectar toque em jogador inimigo
Handle.Touched:Connect(function(hit)
	local vitimaChar = hit:FindFirstAncestorOfClass("Model")
	if not vitimaChar then return end

	local vitimaPlayer = Players:GetPlayerFromCharacter(vitimaChar)
	if not vitimaPlayer then return end

	local atacanteChar = Tool.Parent
	local atacantePlayer = Players:GetPlayerFromCharacter(atacanteChar)
	if not atacantePlayer then return end

	-- Verifica se não estamos batendo em nós mesmos
	if vitimaPlayer ~= atacantePlayer then
		aplicarTapaGlobal(atacantePlayer)
	end
end)
