# hello-world
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
