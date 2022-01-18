local GC = getconnections or get_signal_cons
if GC then
    for i,v in pairs(GC(game.Players.LocalPlayer.Idled)) do
        if v["Disable"] then
            v["Disable"](v)
        elseif v["Disconnect"] then
            v["Disconnect"](v)
        end
    end
else
    print("Unlucky.")
    local vu = game:GetService("VirtualUser")
    game:GetService("Players").LocalPlayer.Idled:connect(function()
        vu:Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
        wait(1)
        vu:Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
    end)
end

local Network = game:GetService("ReplicatedStorage").Framework.Modules["2 | Network"]:GetChildren()
local Run_Service = game:GetService("RunService")
local rs = Run_Service.RenderStepped
local CurrencyOrder = {"Gingerbread", "Tech Coins", "Fantasy Coins", "Coins", "Diamonds",}
 
    local IMightKillMyselfCauseOfThis = {
        --Misc
        ['VIP'] = {'VIP'};
        --Spawn
        ['Town'] = {'Town', 'Town FRONT'}; ['Forest'] = {'Forest', 'Forest FRONT'}; ['Beach'] = {'Beach', 'Beach FRONT'}; ['Mine'] = {'Mine', 'Mine FRONT'}; ['Winter'] = {'Winter', 'Winter FRONT'}; ['Glacier'] = {'Glacier', 'Glacier Lake'}; ['Desert'] = {'Desert', 'Desert FRONT'}; ['Volcano'] = {'Volcano', 'Volcano FRONT'};
        -- Fantasy init
        ['Enchanted Forest'] = {'Enchanted Forest', 'Enchanted Forest FRONT'}; ['Ancient'] = {'Ancient'}; ['Samurai'] = {'Samurai', 'Samurai FRONT'}; ['Candy'] = {'Candy'}; ['Haunted'] = {'Haunted', 'Haunted FRONT'}; ['Hell'] = {'Hell'}; ['Heaven'] = {'Heaven'};
        -- Tech
        ['Ice Tech'] = {'Ice Tech'}; ['Tech City'] = {'Tech City'; 'Tech City FRONT'}; ['Dark Tech'] = {'Dark Tech'; 'Dark Tech FRONT'}; ['Steampunk'] = {'Steampunk'; 'Steampunk FRONT'}, ['Alien Forest'] = {"Alien Forest"; "Alien Forest FRONT"}, ['Alien Lab'] = {"Alien lab"; "Alien Lab FRONT"};
    }
 
    local AreaList = { --These match the IMightKillMyselfCuaseOfThis table
        'All'; 'VIP';
        'Town'; 'Forest'; 'Beach'; 'Mine'; 'Winter'; 'Glacier'; 'Desert'; 'Volcano';
        'Enchanted Forest'; 'Ancient'; 'Samurai'; 'Candy'; 'Haunted'; 'Hell'; 'Heaven';
        'Ice Tech'; 'Tech City'; 'Dark Tech'; 'Steampunk'; 'Alien Lab'; 'Alien Forest';
    }
 
    local Chests = {
"All";
        -- Spawn
        "Magma Chest",
        -- Fantasy
        "Enchanted Chest", "Hell Chest", "Haunted Chest", "Angel Chest", "Grand Heaven Chest",
        -- Tech
        "Giant Tech Chest"; "Giant Steampunk Chest"; "Giant Alien Chest";
    }
workspace.__THINGS.__REMOTES.MAIN:FireServer("b", "buy egg")
workspace.__THINGS.__REMOTES.MAIN:FireServer("b", "join coin")
workspace.__THINGS.__REMOTES.MAIN:FireServer("a", "farm coin")
workspace.__THINGS.__REMOTES.MAIN:FireServer("a", "claim orbs")
workspace.__THINGS.__REMOTES.MAIN:FireServer("a", "collect lootbag")
workspace.__THINGS.__REMOTES.MAIN:FireServer("a", "change pet target")

local AreaWorldTable = {}
for _, v in pairs(game:GetService("ReplicatedStorage").Game.Coins:GetChildren()) do
    for _, b in pairs(v:GetChildren()) do
        table.insert(AreaWorldTable, b.Name)
    end
    table.insert(AreaWorldTable, v.Name)
end

function AllChests()
    local returntable = {}
    local ListCoins = game.workspace['__THINGS']['__REMOTES']["get coins"]:InvokeServer({})[1]
    for i,v in pairs(ListCoins) do
        local shit = v
        shit["index"] = i
        for aa,bb in pairs(AreaWorldTable) do
            if string.find(v.n, bb) then
                local thing = string.gsub(v.n, bb.." ", "")
                if table.find(Chests, thing) then
                    shit.n = thing
                    table.insert(returntable, shit)
                end
            end
        end
    end
    return returntable
end

function FarmCoin(CoinID, PetID)
    game.workspace['__THINGS']['__REMOTES']["join coin"]:InvokeServer({[1] = CoinID, [2] = {[1] = PetID}})
    game.workspace['__THINGS']['__REMOTES']["farm coin"]:FireServer({[1] = CoinID, [2] = PetID})
end


local IDToName = {}
local NameToID = {}
for i,v in pairs(game:GetService("Players").LocalPlayer.PlayerGui.Inventory.Frame.Main.Pets:GetChildren()) do
    IDToName[i] = v.name
    NameToID[v.name] = i
end

function GetMyPets()
    local returntable = {}
    for i,v in pairs(game:GetService("Players").LocalPlayer.PlayerGui.Inventory.Frame.Main.Pets:GetChildren()) do
        if v.ClassName == 'TextButton' and v.Equipped.Visible then
            table.insert(returntable, v.Name)
        end
    end
    return returntable
end

function GetPets()
        local MyPets = {}
        for i,v in pairs(game:GetService("Players").LocalPlayer.PlayerGui.Inventory.Frame.Main.Pets:GetChildren()) do
            local ThingyThingyTempTypeThing = (v.g and 'Gold') or (v.r and 'Rainbow') or (v.dm and 'Dark Matter') or 'Normal'
            local TempString = ThingyThingyTempTypeThing .. IDToName[v.id]
            if MyPets[TempString] then
                    table.insert(MyPets[TempString], v.uid)
                else
                    MyPets[TempString] = {}
                    table.insert(MyPets[TempString], v.uid)
                end
            --end
        end
        return MyPets
end
            

function GetCoinsNormal(area)
    local returntable = {}
    local ListCoins = game.workspace['__THINGS']['__REMOTES']["get coins"]:InvokeServer({})[1]
    for i,v in pairs(ListCoins) do
        if string.lower(v.a) == string.lower(area) then
            table.insert(returntable, i)
        end
    end
    return returntable
end

function GetCoins(area, exclude)
    exclude = exclude or {}
    local Areas = (IMightKillMyselfCauseOfThis)
    local returntable = {}
    local ListCoins = game.workspace['__THINGS']['__REMOTES']["get coins"]:InvokeServer({})[1]
    for i,v in pairs(ListCoins) do
        if _G.FarmingArea == 'All' or table.find(Areas[_G.FarmingArea], v.a) and not table.find(exclude ,v.n) then
            local shit = v
            shit["index"] = i
            table.insert(returntable, shit)
         end
    end
    return returntable
end

function GetCoinTable(area, Type, exclude)
    exclude = exclude or {}
    local CoinTable = GetCoins(area, exclude)
    local function getKeysSortedByValue(tbl, sortFunction)
        local keys = {}
        for key in pairs(tbl) do
            table.insert(keys, key)
        end
        table.sort(
            keys,
            function(a, b)
                return sortFunction(tbl[a].h, tbl[b].h)
            end
        )
        return keys
    end
    local sortedKeys
    if Type == 'Highest' then
        sortedKeys = getKeysSortedByValue(CoinTable, function(a, b) return a > b end)
    elseif Type == 'Lowest' then
        sortedKeys = getKeysSortedByValue(CoinTable, function(a, b) return a < b end)
    end
    local newCoinTable = {}

    for i,v in pairs(sortedKeys) do
        table.insert(newCoinTable, CoinTable[v])
    end

    return newCoinTable
end

function CollectOrbs()
    local ohTable1 = {[1] = {}}
    for i,v in pairs(game.workspace['__THINGS'].Orbs:GetChildren()) do
        ohTable1[1][i] = v.Name
    end
        game.workspace['__THINGS']['__REMOTES']["claim orbs"]:FireServer(ohTable1)
end

local TypeList = {'Normal', 'Player Location', 'Chest', 'Multi Target Normal', 'Multi Target Faster', 'Lowest Value (Multi)', 'Highest Value', 'Highest Value (Multi)', 'Nearest'}

local CurrentFarmingPets = {}
    while _G.FarmingEnable do
        local pethingy = GetMyPets()
        local cointhiny = nil
        
        if _G.FarmingType == 'Normal' then
                local cointhiny = GetCoins(_G.FarmingArea)
                for i = 1, #cointhiny do
                    if _G.FarmingEnable and game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(cointhiny[i].index) then
                        for _, bb in pairs(pethingy) do
                            coroutine.wrap(function()
                                FarmCoin(cointhiny[i].index, bb)
                            end)()
                        end
                        repeat task.wait(0.001) until not game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(cointhiny[i].index) or not _G.FarmingEnable or _G.FarmingType ~= 'Normal'
                    end
                end
        elseif _G.FarmingType == 'Chest' then
                for i,v in pairs(AllChests()) do
                    if (v.n == _G.FarmingSingleChest) or (_G.FarmingSingleChest == 'All') then
                        local starttick = tick()
                        for a, b in pairs(pethingy) do
                            coroutine.wrap(function()
                                FarmCoin(v.index, b)
                            end)()
                        end
                        repeat task.wait(0.001) until not game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(v.index) or #game:GetService("Workspace")["__THINGS"].Coins[v.index].Pets:GetChildren() == 0 or not _G.FarmingEnable or _G.FarmingType ~= 'Chest'
                        --warn(v.n .. " has been broken in", tick()-starttick)
                    end
                end
                
        elseif _G.FarmingType == 'Multi Target Normal' then
            local cointhiny = GetCoinsNormal(_G.FarmingArea)
            for i = 1, #cointhiny do
                pcall(function() FarmCoin(cointhiny[i], pethingy[i%#pethingy+1]) end)
            end
        elseif _G.FarmingType == 'Multi Target Faster' then
                local cointhiny = GetCoins(_G.FarmingArea)
                for i = 1, #cointhiny do
                    if not CurrentFarmingPets[pethingy[i%#pethingy+1]] or CurrentFarmingPets[pethingy[i%#pethingy+1]] == nil then
                        wait(0.1)
                        coroutine.wrap(function()
                            CurrentFarmingPets[pethingy[i%#pethingy+1]] = 'Farming'
                            FarmCoin(cointhiny[i].index, pethingy[i%#pethingy+1])
                            repeat task.wait() until not game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(cointhiny[i].index) or #game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(cointhiny[i].index).Pets:GetChildren() == 0 or not _G.FarmingEnable or _G.FarmingType ~= 'Multi Target Faster'
                            CurrentFarmingPets[pethingy[i%#pethingy+1]] = nil
                        end)()
                    end
                end
        elseif _G.FarmingType == 'Lowest Value (Multi)' then
                local cointhiny = GetCoinTable(_G.FarmingArea, "Lowest")
                for i = 1, (#cointhiny >= #pethingy and #pethingy or #cointhiny) do
                    if not CurrentFarmingPets[pethingy[i%#pethingy+1]] or CurrentFarmingPets[pethingy[i%#pethingy+1]] == nil then
                        coroutine.wrap(function()
                            CurrentFarmingPets[pethingy[i%#pethingy+1]] = 'Farming'
                            FarmCoin(cointhiny[i].index, pethingy[i%#pethingy+1])
                            repeat task.wait(0.0001) until not game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(cointhiny[i].index) or #game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(cointhiny[i].index).Pets:GetChildren() == 0 or not _G.FarmingEnable or _G.FarmingType ~= 'Lowest Value (Multi)'
                            CurrentFarmingPets[pethingy[i%#pethingy+1]] = nil
                        end)()
                    end
                end
        elseif _G.FarmingType == 'Highest Value' then
                local cointhiny = GetCoinTable(_G.FarmingArea, "Highest")
                if #cointhiny >= 1 then
                    for a,b in pairs(pethingy) do
                        coroutine.wrap(function()
                            FarmCoin(cointhiny[1].index, b) 
                        end)()
                    end
                    repeat task.wait(0.001) until not game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(cointhiny[1].index) or #game:GetService("Workspace")["__THINGS"].Coins[cointhiny[1].index].Pets:GetChildren() == 0 or not _G.FarmingEnable or _G.FarmingType ~= 'Highest Value'
                else
                    task.wait()
                end
        elseif _G.FarmingType == 'Highest Value (Multi)' then
                local cointhiny = GetCoinTable(_G.FarmingArea, "Highest")
                if #cointhiny >= 3 then
                    local CurrentFarmingPets = {}
                    for a = 1, 3 do
                        coroutine.wrap(function()
                            if CurrentFarmingPets[a] == nil then
                                for petnum = a, #pethingy, 3 do
                                    coroutine.wrap(function()
                                        if not CurrentFarmingPets[a] then CurrentFarmingPets[a] = {} end
                                        if CurrentFarmingPets[a] ~= nil and game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(cointhiny[a].index) and not table.find(CurrentFarmingPets[a], pethingy[petnum]) then
                                            table.insert(CurrentFarmingPets[a], pethingy[petnum])
                                            FarmCoin(cointhiny[a].index, pethingy[petnum]) 
                                        end
                                    end)()
                                end
                                repeat task.wait(0.001) until not game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(cointhiny[a].index) or #game:GetService("Workspace")["__THINGS"].Coins[cointhiny[a].index].Pets:GetChildren() == 0  or not _G.FarmingEnable or _G.FarmingType ~= 'Highest Value (Multi)'
                                CurrentFarmingPets[a] = nil
                            end
                        end)()
                    end
                else
                    task.wait()
                end
        elseif _G.FarmingType == 'Nearest' then
                local cointable = game:GetService("Workspace")["__THINGS"].Coins:GetChildren()
                table.sort(cointable, function(i, v)
                    return (game.Players.LocalPlayer.Character.HumanoidRootPart.Position - i.POS.Position).magnitude < (game.Players.LocalPlayer.Character.HumanoidRootPart.Position - v.POS.Position).magnitude
                end)
                local NearestOne = cointable[1]
                if NearestOne ~= nil then
                    for a,b in pairs(pethingy) do
                        coroutine.wrap(function()
                            if NearestOne ~= nil then
                                FarmCoin(NearestOne.Name, b)
                            end
                        end)()
                    end
                    repeat task.wait(0.001) until not game:GetService("Workspace")["__THINGS"].Coins:FindFirstChild(NearestOne.Name) or #game:GetService("Workspace")["__THINGS"].Coins[NearestOne.Name].Pets:GetChildren() == 0
                else
                    task.wait()
                end
        elseif _G.FarmingType == 'Player Location' then
            local dist = math.huge
			local target = nil
			for i,v1 in pairs(game:GetService("Players").LocalPlayer.PlayerGui.Inventory.Frame.Main.Pets:GetChildren()) do
				for i,v in pairs(game:GetService("Workspace")["__THINGS"].Coins:GetChildren()) do
				    if v1.ClassName == "TextButton" then
						if v.ClassName == "Folder" then
							if v:FindFirstChild("Coin") then
								if v.Coin.Transparency == 0 then
									local magnitude = (v.Coin.Position - game:GetService("Players").LocalPlayer.Character.Head.Position).magnitude
								    if magnitude < dist then
							    		dist = magnitude
							    		target = v.Name
						    			workspace.__THINGS.__REMOTES["join coin"]:InvokeServer({tostring(target),{tostring(v1.Name)}})
							    		workspace.__THINGS.__REMOTES["farm coin"]:FireServer({tostring(target),tostring(v1.Name)})
								    end
								end
							end
						end
				    end
				end
			end
        end
    end

if _G.MyConnection then _G.MyConnection:Disconnect() end
_G.MyConnection = game.Workspace.__THINGS.Orbs.ChildAdded:Connect(function(Orb)
    game.Workspace.__THINGS.__REMOTES["claim orbs"]:FireServer({{Orb.Name}})
end)
