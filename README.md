	elseif string.match(filename, "tu_full_00_*") or string.match(filename, "tu_full_01_*") or string.match(filename, "tu_full_02_*") then				
		if fulltime_anthem == 0 then
			log("Base Loaded: " .. filename) 
			fulltime_anthem = 1
			kickoff_check = 1 	
			if stats then									
				if home_id == ctx.home_team and away_id == ctx.away_team and daynightid == ctx.timeofday and tid == ctx.tournament_id and cuproundid == ctx.match_info then
					log("tid: " .. tid)
					log("cuproundid: " .. cuproundid)
					-- HOME --	
					if stats.home_score > stats.away_score then
						if home_score == home_score and away_score == away_score then
							sound = audio.new(content_root .. horario[daynightid] .. "\\3_Falas_no_Encerramento\\" .. torneios[tid] .. "\\" .. torneios_round[cuproundid] .. "\\" .. times[home_id] .. "\\" .. times[home_id] .. "_X_" .. times[away_id] .. "\\" .. times[home_id] .. "_" .. home_score .. "_X_" .. away_score .. "_" .. times[away_id] .. ".mp3")
						end
					-- AWAY --	
					elseif stats.away_score > stats.home_score then
						if home_score == home_score and away_score == away_score then
							sound = audio.new(content_root .. horario[daynightid] .. "\\3_Falas_no_Encerramento\\" .. torneios[tid] .. "\\" .. torneios_round[cuproundid] .. "\\" .. times[home_id] .. "\\" .. times[home_id] .. "_X_" .. times[away_id] .. "\\" .. times[home_id] .. "_" .. home_score .. "_X_" .. away_score .. "_" .. times[away_id] .. ".mp3")
						end
					-- EMPATE --
					elseif stats.away_score == stats.home_score then
						if home_score == home_score and away_score == away_score then
							sound = audio.new(content_root .. horario[daynightid] .. "\\3_Falas_no_Encerramento\\" .. torneios[tid] .. "\\" .. torneios_round[cuproundid] .. "\\" .. times[home_id] .. "\\" .. times[home_id] .. "_X_" .. times[away_id] .. "\\" .. times[home_id] .. "_" .. home_score .. "_X_" .. away_score .. "_" .. times[away_id] .. ".mp3")	
						end
					end
				end
			end
						log(string.format("fulltime anthem starting: %s", sound:get_filename()))
						sound:set_volume(0.7)
						sound:play()					
		end		
	end		
