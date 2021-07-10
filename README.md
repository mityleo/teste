
--goalscreams mod by dazzaa (v1.01 24/05/21)
--for PES2021 (1.04 and 1.05 and 1.06)
--credits
--Sider by juce

--settings

-- goalscream volume (goalscream_x_vol>=0)
local goalscream_h_vol = 0.2
local goalscream_a_vol = 0.1

--probabilidade de um gol entrar no modo de seleção de gols (0<=probability<=1)
local probability = 1

--redefinir o áudio reproduzido no intervalo. (verdadeiro ou falso) ou após cada meta (verdadeiro ou falso)
local reset_played_audio_every_goal = true


--apenas cantem os gols em casa (verdadeiro ou falso)
local only_home = true

--apenas toque cânticos no replay, não após o pontapé inicial (verdadeiro ou falso)
local only_replay = false
local only_restart = true

--fade replay chants at kickoff
local fade_at_kickoff = false






local m = {}
local h = {}
local a = {}
local initialised = 0
local blacklist = {}
local whitelist = {}
local bigtable = {}

local function check_goals()
	if goal_address then
		home_score = memory.unpack("u8", memory.read(home_goal_addr, 0))
		away_score = memory.unpack("u8", memory.read(away_goal_addr, 0))
	else
		goal_address = memory.unpack("u32", memory.read(base + 0x037FDF90,4))
		home_goal_addr = goal_address + 0x5158
		away_goal_addr = goal_address + 0x5159
		home_score = 0
		away_score = 0
		last_home_score = 0
		last_away_score = 0
	end
end

local function superleeds()
	if magic_address then
		match_state = memory.unpack("u8", memory.read(magic_address, 1))
	else
		addr1 = memory.unpack("u32", memory.read(base + 0x038018E8,4))
		addr2 = addr1 + 0x58
		addr3 = memory.unpack("u32", memory.read(addr2,4))
		addr4 = addr3 + 0x270
		addr5 = memory.unpack("u32", memory.read(addr4,4))
		magic_address = addr5 + 0xb4
		last_match_state = 0
	end
end

local function vardys_party_check()
	if vardys_party_address then
		vardys_party = memory.unpack("u32", memory.read(vardys_party_address,4))
	else
		addr1 = memory.unpack("u32", memory.read(base + 0x036FC178,4))
--		log("1: " .. memory.hex(addr1))
		addr2 = addr1 + 0x7a8
		addr3 = memory.unpack("u32", memory.read(addr2,4))
--		log("3: " .. memory.hex(addr3))
		addr4 = addr3 + 0x4e8
		addr5 = memory.unpack("u32", memory.read(addr4,4))
--		log("5: " .. memory.hex(addr5))
		addr6 = addr5 + 0x28
		addr7 = memory.unpack("u32", memory.read(addr6,4))
--		log("7: " .. memory.hex(addr7))
		vardys_party_address = addr7 + 0x1c4
--		log("vpa: " .. memory.hex(vardys_party_address))
	end
end	
	--[[

function m.key_down(ctx, vkey)
	if only_home == true then
		if vkey == 0x35 then
			if menu > 0 then
				menu = menu - 1
			end
		elseif vkey == 0x36
			if menu <  #menu then
				menu = menu + 1
			end
		elseif vkey == 0x37
		end
			
function m.overlay_on(ctx)
	if only_home == true then
		return ("only home "..only_home.." home vol. "..goalscream_h_vol.." away vol. "..goalscream_a_vol..reset_played_audio_every_goal..only_home..only_replay)
	else
		return ("only home "..only_home.." home vol. "..goalscream_h_vol..reset_played_audio_every_goal..only_home..only_replay)
end
--]]

function m.data_ready(ctx, filename)
	if initialised == 0 then
		if string.match(filename, "common\\sound\\match\\awb\\Chant\\CHANT_Base.awb") then
			log("goalscreams mod started")
			vardys_party_check()
			superleeds()
			check_goals()
			do_once = 0
			initialised = 1
			if bigtable then
				bigtable = {}
			end
		end
	end
	if initialised == 1 then
		superleeds()
		--começar no matchstate 0
		if do_once == 0 and match_state == 0 then
			if last_match_state == 1 then
				log("initialised")
				start_me_up = 1
				do_once = 1
			end
		end
		if start_me_up == 1 then
			superleeds()
			if match_state ~= last_match_state then
				-- jogar o goalcream se o rnd100 decidir não jogar durante o replay
				if match_state == 0 then
					if reset_played_audio_every_goal == true then
						if bigtable then
							bigtable = {}
						end
					end
					if goalscream then
						if play_later == true then
							log("rnd: " .. rnd .. " loaded")
							goalscream:when_done(function(ctx)
								goalscream = nil
							end)
							goalscream:play()
							play_later = false
						elseif fade_at_kickoff == true then
							goalscream:fade_to(0,1)
						end
					end
				--objetivo, iniciar verificações
				elseif match_state == 1 then
					comm_play_once = 1
					vardy_check_once = 1
				--resultados, fade goalscream e modo de pausa
				elseif match_state == 7 then
				if goalscream then
					goalscream:fade_to(0,3)
				end
					initialised = 0
					start_me_up = 0
					do_once = 0
				end
			end
			if comm_play_once == 1 then
				--verificar a id do jogador
				vardys_party_check()
				if vardy_check_once == 1 then
					log("goal....scored by id. " .. vardys_party)
					if vardys_party ~= 9042 and vardys_party < 200000 then
						if vardys_party == 0 then
							log("rechecking player id.")
						else
							vardy_check_once = 0
							--verificar qual lado marcou e marcar gols / id do jogador em casa e fora
							check_goals()
							if home_score > last_home_score then
								goal_h = true
								if h[vardys_party] == nil then
									h[vardys_party]={}
									h[vardys_party] = 1
								else
									h[vardys_party] = h[vardys_party] + 1
									if h[vardys_party] == 3 then
										log("player id. " .. vardys_party .. " has a hat-trick at home")
										h[vardys_party] = 0
									end 
								end
							elseif away_score > last_away_score then
								goal_a = true
								if a[vardys_party] == nil then
									a[vardys_party]={}
									a[vardys_party] = 1
								else
									a[vardys_party] = a[vardys_party] + 1
									if a[vardys_party] == 3 then
										log("player id. " .. vardys_party .. " has a hat-trick away from home")
										a[vardys_party] = 0
									end 
								end
							end							
                          --sem áudio último objetivo, id na lista negra? se não, vá em frente
							if blacklist[vardys_party] then
								comm_play_once = 0
							else
								if whitelist[vardys_party] then
									rnd10 = math.random (1, whitelist[vardys_party])
									max_xx = whitelist[vardys_party]
								else
									rnd10 = math.random (1, 3)
									max_xx = 3
								end
								--tente encontrar um mp3 e max rnd10
								goalscream = audio.new(ctx.sider_dir .. "content\\audio-demo\\goal\\goalscreams\\" .. vardys_party .. "_0" .. rnd10 .. ".mp3")
								rnd = rnd10
								if goalscream == nil and rnd10 == 1 then
									max_xx = nil
									blacklist[vardys_party] = true
									comm_play_once = 0
								elseif goalscream == nil and rnd10 > 1 then
									goalscream = audio.new(ctx.sider_dir .. "content\\audio-demo\\goal\\goalscreams\\" .. vardys_party .. "_0" .. rnd10 - 1 .. ".mp3")
									rnd = rnd10 - 1
									if goalscream then
										if rnd10 == 2 then
											max_xx = 1
										else
											max_xx = 2
										end
									elseif goalscream == nil then
										if rnd10 == 2 then
											max_xx = nil
											blacklist[vardys_party] = true
											comm_play_once = 0
										elseif rnd10 == 3 then
											max_xx = 1
											goalscream = audio.new(ctx.sider_dir .. "content\\audio-demo\\goal\\goalscreams\\" .. vardys_party .. "_0" .. rnd10 - 2 .. ".mp3")
											rnd = rnd10 - 2
											if goalscream == nil then
												max_xx = nil
												blacklist[vardys_party] = true
												comm_play_once = 0
											end
										end
									end
								end
								if max_xx then
									--make a table of played mp3s and check new goals against it, add mp3 to table
									whitelist[vardys_party] = max_xx
									if bigtable[vardys_party]  then
										for index, value in ipairs(bigtable[vardys_party]) do
											if index == #bigtable[vardys_party] and value ~= rnd then
												not_matched = 1
											elseif value == rnd then
												goalscream = nil
												comm_play_once = 0
											end
										end
										if not_matched == 1 then
											table.insert(bigtable[vardys_party],rnd)
											not_matched = 0
										end
									else
										bigtable[vardys_party] = {}
										table.insert(bigtable[vardys_party],rnd)
									end
								end
							end
						end
					end
					--play mp3 once either now or when the game goes back to kickoff screen
					if goalscream then
						comm_play_once = 0
						if goal_h == true then
							goalscream:set_volume(goalscream_h_vol)
						else
							goalscream:set_volume(goalscream_a_vol)
						end
						if only_replay == true then
							rnd100 = 1
						elseif only_restart == true then
							rnd100 = 2
						else
							rnd100 = math.random (1, 2)					
						end
						if only_home == true then
							if goal_a == true then
								goalscream = nil
							else
								if rnd100 == 1 then
									goalscream:when_done(function(ctx)
										goalscream = nil
									end)
									goalscream:play()
								else
									log("will play on a kickoff")
									play_later = true
								end
							end
						else
							if rnd100 == 1 then
								goalscream:when_done(function(ctx)
									goalscream = nil
								end)
								goalscream:play()
							else
								log("will play on a kickoff")
								play_later = true
							end
						end
						goal_h = nil
						goal_a = nil

					end
				end
			end
		end
	end
	last_match_state = match_state
	last_home_score = home_score
	last_away_score = away_score
	if filename == "common\\script\\flow\\Match\\MatchSetupRematch.json" or filename == "common\\script\\flow\\Match\\MatchDiscontinue.json" 
	or filename == "common\\script\\flow\\Match\\MatchDiscontinueTeam.json" or filename == "common\\script\\flow\\Match\\MatchEnd.json" then
		initialised = 0
		start_me_up = 0
		magic_address = nil
		vardys_party_address = nil
		game_over = false
		a = {}
		h = {}
		log("goalscreams mod stopped")
	end
end

function m.init(ctx)
	ctx.register("livecpk_data_ready", m.data_ready)
	base = memory.get_process_info().base
end

return m
