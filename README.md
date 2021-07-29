-- ServerScript
local tweenService = game:GetService("TweenService")
game.ReplicatedStorage.Shoot.OnServerEvent:Connect(function(player, target, mousepos, gunpos, tool, shootAnim)
	if game.Workspace:FindFirstChild(player.Name .."BulletTrail") then
		game.Workspace[player.Name .."BulletTrail"]:Destroy()
	end
	if tool.Bullets.Value > 0 then
		part = Instance.new("Part", workspace)
		part.Name = player.Name .."BulletTrail"
		part.CanCollide = false
		part.Anchored = true
		part.Transparency = 0.5
		if (gunpos - mousepos).magnitude >= 2048 then
			part.Size = Vector3.new(0.2, 0.2, 2048)
			part.CFrame = CFrame.new(gunpos, mousepos) * CFrame.new(0,0,-2048/2)		
		else
			part.Size = Vector3.new(0.2, 0.2, (gunpos - mousepos).magnitude)
			part.CFrame = CFrame.new(gunpos, mousepos) * CFrame.new(0,0,-(gunpos - mousepos).magnitude/2)
		end
		spawn(function()
			local tween = tweenService:Create(part, TweenInfo.new(0.5), {Transparency = 1})
			tween:Play()
			tween.Completed:Connect(function()
				part:Destroy()
			end)
		end)
		if target.Parent:FindFirstChild("Humanoid") then
			target.Parent.Humanoid:TakeDamage(10)
		end
	end
end)
-- Local Script
local canShoot = true
local mouse = game.Players.LocalPlayer:GetMouse()
game.Players.LocalPlayer.PlayerGui:WaitForChild("ScreenGui").Bullets.Text = "Bullets: ".. script.Parent.Bullets.Value .."/"..script.Parent.MaxBullets.Value
game.Players.LocalPlayer.PlayerGui.ScreenGui.Bullets.Visible = true
local humanoid = game.Players.LocalPlayer.Character:WaitForChild("Humanoid")
local shootAnimTrack = humanoid:LoadAnimation(script.ShootAnim)
shootAnimTrack.Looped = false
shootAnimTrack.Priority = Enum.AnimationPriority.Action
local reloadAnimTrack = humanoid:LoadAnimation(script.ReloadAnim)
reloadAnimTrack.Looped = false
reloadAnimTrack.Priority = Enum.AnimationPriority.Action
script.Parent.Equipped:Connect(function()
	mouse.KeyDown:Connect(function(key)
		if key == "r" and script.Parent.Bullets.Value ~= script.Parent.MaxBullets.Value then
			reloadAnimTrack:Play()
			reloadAnimTrack.Stopped:Connect(function()
				script.Parent.Bullets.Value = script.Parent.MaxBullets.Value
				game.Players.LocalPlayer.PlayerGui.ScreenGui.Bullets.Text = "Bullets: ".. script.Parent.Bullets.Value .."/"..script.Parent.MaxBullets.Value
			end)
		end
	end)
	script.Parent.Activated:Connect(function()
		if canShoot == true and script.Parent.Bullets.Value ~= 0 then
			shootAnimTrack:Play()
			script.Parent.Bullets.Value = script.Parent.Bullets.Value - 1
			game.Players.LocalPlayer.PlayerGui.ScreenGui.Bullets.Text = "Bullets: ".. script.Parent.Bullets.Value .."/"..script.Parent.MaxBullets.Value
			game.ReplicatedStorage.Shoot:FireServer(mouse.Target, mouse.Hit.p, script.Parent.Part.Position, script.Parent, script.ShootAnim)
		else
			reloadAnimTrack:Play()
			reloadAnimTrack.Stopped:Connect(function()
				script.Parent.Bullets.Value = script.Parent.MaxBullets.Value
				game.Players.LocalPlayer.PlayerGui.ScreenGui.Bullets.Text = "Bullets: ".. script.Parent.Bullets.Value .."/"..script.Parent.MaxBullets.Value
			end)
		end
	end)
	script.Parent.Deactivated:Connect(function()
		if canShoot == false then
			shootAnimTrack.Stopped:Wait()
			canShoot = true
		end
	end)
end)
script.Parent.Unequipped:Connect(function()
	game.Players.LocalPlayer.PlayerGui.ScreenGui.Bullets.Visible = false
end)
