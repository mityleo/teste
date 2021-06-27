-- Tournament Anthems (Entrance/Half-Time/Full-Time) for PES2021
-- Author: Bruno Braga and Mity Leo on 18/06/2021
-- Version: 1.5

local m = {}
local content_root = "\\content\\tournament_anth_tunnel\\"
local sound
local pretunnel_anthem = 0
local halftime_anthem = 0
local fulltime_anthem = 0
-- local stats_home_score = 0
-- local stats_away_score = 0
-- local home_score = 0
-- local away_score = 0

local function pause_check() 
	if pause_state_address then 
		pause_state = memory.unpack("u8", memory.read(pause_state_address, 1))
	else	
		addr1 = memory.unpack("u32", memory.read(base + 0x037FFB18,4))
		addr2 = addr1 + 0x478
		addr3 = memory.unpack("u32", memory.read(addr2,4))
		addr4 = addr3 + 0x110
		addr5 = memory.unpack("u32", memory.read(addr4,4))
		addr6 = addr5 + 0x10
		addr7 = memory.unpack("u32", memory.read(addr6,4))
		pause_state_address = addr7 + 0x64a
		pause_state = memory.unpack("u8", memory.read(pause_state_address, 1))
	end
end

	
    local times = {
	-- SELEÇÕES NACIONAIS -- 	
	 [37] = "Africa_do_Sul",            [1165] = "Albania",	        [14] = "Alemanha",	      [1166] = "Andorra",	      [56] = "Arabia_Saudita", [1040] = "Argelia",	          [50] = "Argentina",            [1167] = "Armenia",
     [57] = "Australia",	            [21] = "Austria",	        [1168] = "Azerbaijao",    [9] = "Belgica",            [1169] = "Bielorrussia", [1128] = "Bolivia",	          [1170] = "Bosnia_Herzegovina", [45] = "Brasil",
     [27] = "Bulgaria",	                [1044] = "Burkina_Fasso",   [36] = "Camaroes",	      [1026] = "Catar",	          [1176] = "Cazaquistao",  [47] = "Chile",	              [54] = "China",                [1171] = "Chipre",    
     [44] = "Colombia",	                [1012] = "Coreia_do_Norte", [53] = "Coreia_do_Sul",   [1051] = "Costa_do_Marfim", [42] = "Costa_Rica",	   [24] = "Croacia",	          [15] = "Dinamarca",			 [34] = "Egito",		
     [1032] = "Emirados_Arabes_Unidos", [51] = "Equador",	        [3] = "Escocia",	      [20] = "Eslovaquia",	      [23] = "Eslovenia",	   [7] = "Espanha",	              [39] = "Estados_Unidos",       [1172] = "Estonia",
     [18] = "Finlandia",	            [8] = "França",	            [1058] = "Gana",	      [1174] = "Georgia",	      [5439] = "Gibraltar",	   [28] = "Grecia",	              [1059] = "Guine",              [10] = "Holanda",
     [43] = "Honduras",                 [22] = "Hungria",	        [1173] = "Ilhas_Faroe",   [5] = "Inglaterra",	      [55] = "Ira",	           [1010] = "Iraque",	          [1] = "Irlanda",               [2] = "Irlanda_do_Norte",
	 [1175] = "Islandia",               [1164] = "Israel",          [12] = "Italia",	      [41] = "Jamaica",	          [52] = "Japao",	       [1011] = "Jordania",	          [5440] = "Kosovo",	         [1013] = "Kuwait",
	 [59] = "Letonia",	                [1015] = "Libano",	        [1177] = "Liechtenstein", [1178] = "Lituania",	      [1179] = "Luxemburgo",   [1180] = "Macedonia_do_Norte", [1067] = "Mali",	             [1181] = "Malta",   
	 [32] = "Marrocos",	                [40] = "Mexico",	        [1182] = "Moldavia",	  [1551] = "Montenegro",      [35] = "Nigeria",	       [16] = "Noruega",	          [1141] = "Nova_Zelandia",      [1022] = "Oma",
	 [4] = "Pais_de_Gales",             [1113] = "Panama",	        [48] = "Paraguai",	      [46] = "Peru",	          [19] = "Polonia",	       [6] = "Portugal",	          [13] = "Republica_Tcheca",     [26] = "Romenia", 
	 [31] = "Russia",	                [1183] = "Sao_Marinho",	    [38] = "Senegal",	      [1550] = "Servia",	      [17] = "Suecia",	       [11] = "Suiça",	              [1031] = "Tailandia",	         [33] = "Tunisia",     
	 [29] = "Turquia",	                [30] = "Ucrania",	        [49] = "Uruguai",	      [1184] = "Uzbequistao",     [1129] = "Venezuela",	   [1083] = "Zambia",		

	-- TIMES --
	-- Brasil --
	 [9042] = "4_de_Julho_EC",	        [5669] = "ABC",	                 [9017] = "Aimore",	          [5674] = "Altos",	       [2450] = "America_Mineiro",	    [5671] = "America_RN",	         [9024] = "Athletic_Club",	     [1930] = "Athletico_Paranaense",
	 [9010] = "Atletico_de_Alagoinhas", [2451] = "Atletico_Goianiense",  [1245] = "Atletico_Mineiro", [2452] = "Avai",	       [2453] = "Bahia",	            [9011] = "Bahia_de_Feira",	     [9007] = "Bangu",	             [2458] = "Boa_Esporte",
	 [9005] = "Boavista_RJ",	        [1246] = "Botafogo",	         [5650] = "Botafogo_PB",	  [5139] = "Botafogo_SP",  [9031] = "Brasiliense",	        [5047] = "Brasil_de_Pelotas_RS", [5660] = "Brusque",	         [9023] = "Caldense",
	 [9030] = "Campinense",	            [9016] = "Caxias",	             [2454] = "Ceara",	          [4108] = "Chapecoense",  [9028] = "Coimbra",	            [5140] = "Confiança",	         [1247] = "Corinthians",	     [1931] = "Coritiba",
	 [2506] = "CRB",	                [2460] = "Criciuma",	         [274] = "Cruzeiro",          [5141] = "CSA",	       [5142] = "Cuiaba",	            [9015] = "Doce_Mel",	         [9020] = "Esportivo",	         [9001] = "Ferroviaria",
	 [5647] = "Ferroviario",	        [1932] = "Figueirense",	         [1248] = "Flamengo",	      [5672] = "Floresta_CE",  [1249] = "Fluminense",	        [9014] = "Fluminense_de_Feira",  [5143] = "Fortaleza",	         [1933] = "Goias",
	 [1250] = "Gremio",	                [5677] = "Gremio_Novorizontino", [1251] = "Guarani",	      [5648] = "Imperatriz",   [5663] = "Inter_de_Limeira",	    [1252] = "Internacional",	     [2455] = "Ituano",	             [5649] = "Jacuipense",
	 [9029] = "Joinville",	            [9009] = "Juazeirense",	         [5137] = "Juventude",	      [5048] = "Londrina",	   [9008] = "Macae",	            [2505] = "Madureira",	         [4263] = "Manaus",	             [5627] = "Mirassol",
	 [1935] = "Nautico",	            [9004] = "Nova_Iguaçu",	         [9019] = "Novo_Hamburgo",	  [4110] = "Oeste",	       [5433] = "Operario_Ferroviario", [137] = "Palmeiras",	         [2464] = "Parana",	             [9026] = "Patrocinense",
	 [4959] = "Paysandu",	            [9021] = "Pelotas",	             [2465] = "Ponte_Preta",	  [1253] = "Portuguesa",   [9003] = "Portuguesa_RJ",        [9022] = "Pouso_Alegre",	     [2459] = "Red_Bull_Bragantino", [5145] = "Remo",
	 [9006] = "Resende",	            [9043] = "Salgueiro",	         [4111] = "Sampaio_Correa",   [4112] = "Santa_Cruz",   [9002] = "Santo_Andre",	        [1254] = "Santos",	             [5146] = "Sao_Bento",	         [9000] = "Sao_Caetano",
	 [5643] = "Sao_Jose_RS",            [9018] = "Sao_Luiz",	         [1255] = "Sao_Paulo",	      [1936] = "Sport_Recife", [5644] = "Tombense",	            [4326] = "Treze",	             [9027] = "Uberlandia",	         [9013] = "Unirb_FC",
	 [9025] = "URT",	                [136] = "Vasco_Da_Gama",	     [2468] = "Vila_Nova",	      [1937] = "Vitoria",	   [9012] = "Vitoria_da_Conquista", [5646] = "Volta_Redonda",	     [2218] = "Ypiranga_RS",	

	-- Argentina --
	 [1240] = "Indepediente",
	}

	
	local horario = {
	 [0] = "Dia",	    
	 [1] = "Noite",
	}

	local torneios = {
	-- TORNEIOS(ctx.tournament_id) --
	
	-- AMISTOSO -- 
	 [65535] = "Amistoso",
	 
	-- FLORIDA CUP -- 
	 [106] = "Florida_Cup",
	
	-- BRASIL --
	 [29] = "Brasil\\Brasileirao_Serie_A", 
	 [163] = "Brasil\\Brasileirao_Serie_B", 
	 [31] = "Brasil\\Copa_do_Brasil",
	 
	-- TURQUIA --
	 [118] = "Turquia\\Super_Lig",
	 [125] = "Turquia\\Turkish_Cup",		 
	 [130] = "Turquia\\Super_Cup",
	 
	-- ESCOCIA -- 
	 [133] = "Escocia\\Scottish_Premiership",
	 [134] = "Escocia\\First_Phase",
	 [135] = "Escocia\\Top_Six",
	 [136] = "Escocia\\Bottom_Six", 
	 [137] = "Escocia\\Scottish_Cup",
	 
	-- FRANÇA --
	 [20] = "França\\Ligue_1",
	 [81] = "França\\Ligue_2",
	 [26] = "França\\Coupe_de_France",
	 [88] = "França\\Super_Coupe_de_France", -- Trophee des Champions
	 
	-- BÉLGICA --
	 [115] = "Belgica\\Jupiler_Pro_League",
	 [155] = "Belgica\\Regular_Season", 
	 [156] = "Belgica\\Play_Offs_Group_A_1",
	 [157] = "Belgica\\Play_Offs_Group_A_2",
	 [158] = "Belgica\\Play_Offs_Group_B",
	 [159] = "Belgica\\European_Play_Offs",
	 [122] = "Belgica\\Super_Cup", -- Croky Cup
	 [128] = "Belgica\\Belgian_Super_Cup",
	 
	-- SUIÇA --
	 [117] = "Suiça\\Raiffeisen_League",
     [124] = "Suiça\\Coupe_de_Suisse",
	 
	-- ITÁLIA --
	 [18] = "Italia\\Serie_A",
	 [82] = "Italiz\\Serie_B",	
	 [24] = "Italia\\Coppa_Italia",
	 [85] = "Italia\\D2_Playoff_Knockout",
	 
	-- HOLANDA --
	 [21] = "Holanda\\Eredivisie",
	 [27] = "Holanda\\Netherlands_Cup",
	 [90] = "Holando\\Super_Cup_Knockout",
	 
	-- ESPANHA -- 
	 [19] = "Espanha\\D1_La_Liga",	
	 [80] = "Espanha\\D2_La_Liga_SmartBank",
	 [84] = "Espanha\\D2_Playoff_Knockout",	
	 [87] = "Espanha\\Spanish_Super_Cup",
	 [25] = "Espanhol\\Copa_del_Rey",
	 
	-- CHILE --	
	 [67] = "Chile\\Campeonato_PlanVital",
	 [68] = "Chile\\Chilean_Cup",
	 
	-- INGLATERRA --
	 [17] = "Inglaterra\\Premier_League",
	 [79] = "Inglaterra\\EFL_Championship",
	 [83] = "Inglaterra\\EFL_Championship_Playoff",
	 [23] = "Inglaterra\\FA_Cup",
	 [86] = "Inglaterra\\Super_Cup",
	 
	-- ALEMANHA --
	 [50] = "Alemanha\\Bundesliga",
	 [53] = "Alemanha\\DFB_Pokal",
	 [95] = "Alemanha\\DFL_Supercup",
	 
	-- COLÔMBIA --
	 [119] = "Colombia\\Liga_BetPlay_Dimayor",
	 [160] = "Colombia\\Liga_BetPlay_Dimayor_I_Playoffs_",
	 [161] = "Colombia\\Liga_BetPlay_Dimayor_II_Playoffs",
	 [168] = "Colombia\\Liga_BetPlay_Dimayor",
	 [169] = "Colombia\\Liga_BetPlay_Dimayor",
	 [126] = "Colombia\\Colombian_Cup",
	 [131] = "Colombia\\Super_Cup_Knockout",
	


	
	-- LIBERTADORES --
	 [8] = "Conmebol\\Libertadores\\Pre_Libertadores_1",
	 [1032] = "Conmebol\\Libertadores\\Pre_Libertadores_2",   -- Fase 1 Classificatória / cpuroundid = 53
	 [2056] = "Conmebol\\Libertadores\\Pre_Libertadores_3",   -- Fase 1 Classificatória / cpuroundid = 53
	 [3080] = "Conmebol\\Libertadores\\Pre_Libertadores_4",
	 [4104] = "Conmebol\\Libertadores\\Pre_Libertadores_5",
	 [9] = "Conmebol\\Libertadores\\Fase_de_Grupos_1",
	 [1033] = "Conmebol\\Libertadores\\Grupo_1",
	 [2057] = "Conmebol\\Libertadores\\Grupo_2",
	 [3081] = "Conmebol\\Libertadores\\Grupo_3",
	 [4105] = "Conmebol\\Libertadores\\Grupo_4",
	 [5129] = "Conmebol\\Libertadores\\Grupo_5",
	 [6153] = "Conmebol\\Libertadores\\Grupo_6",
	 [7177] = "Conmebol\\Libertadores\\Grupo_7",
	 [8201] = "Conmebol\\Libertadores\\Grupo_8",
	 [10] = "Conmebol\\Libertadores\\Finais", 
	 
	-- UEFA Champions League -- 
	 [2] = "UEFA\\UEFA_Champions_League\\Playoff_1",
	 [1026] = "UEFA\\UEFA_Champions_League\\Playoff_2",
	 [2050] = "UEFA\\UEFA_Champions_League\\Playoff_3",
	 [3074] = "UEFA\\UEFA_Champions_League\\Playoff_4",
	 [4098] = "UEFA\\UEFA_Champions_League\\Playoff_5",
	 [5122] = "UEFA\\UEFA_Champions_League\\Playoff_6",
	 [6146] = "UEFA\\UEFA_Champions_League\\Playoff_7",
	 [7170] = "UEFA\\UEFA_Champions_League\\Playoff_8",
	 [8194] = "UEFA\\UEFA_Champions_League\\Playoff_9",
	 [3] = "UEFA\\UEFA_Champions_League\\Fase_de_Grupos_1",
	 [1027] = "UEFA\\UEFA_Champions_League\\Fase_de_Grupos_2",
	 [2051] = "UEFA\\UEFA_Champions_League\\Fase_de_Grupos_3",
	 [3075] = "UEFA\\UEFA_Champions_League\\Fase_de_Grupos_4",
	 [4099] = "UEFA\\UEFA_Champions_League\\Fase_de_Grupos_5",
	 [5123] = "UEFA\\UEFA_Champions_League\\Fase_de_Grupos_6",
	 [6147] = "UEFA\\UEFA_Champions_League\\Oitavas_de_Final",
	 [7171] = "UEFA\\UEFA_Champions_League\\Quartas_de_Final",
	 [8195] = "UEFA\\UEFA_Champions_League\\Semi_Final",
	 [4] = "UEFA\\UEFA_Champions_League\\Final",
	 
	-- UEFA Europa League --
	 [5] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [1029] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [2053] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [3077] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [4101] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [5125] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [6149] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [7173] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [8197] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [9221] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [1024] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [11269] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [12293] = "UEFA\\UEFA_Europa_League\\Fase_de_Grupos_",
	 [6] = "UEFA\\UEFA_Europa_League\\Final", 
	 
	-- Uefa Super Cup --
	 [7] = "UEFA\\UEFA_Super_Cup", 

	-- Eliminatorias Europeias --
	 [41] = "UEFA\\UEFA_Euro_Qualifier\\Fase_de_Grupos_1",
	 [42] = "UEFA\\UEFA_Euro_Qualifier\\Fase_de_Grupos_2",
	 [1065] = "UEFA\\UEFA_Euro_Qualifier\\Fase_de_Grupos_3",
	 [2089] = "UEFA\\UEFA_Euro_Qualifier\\Fase_de_Grupos_4",
	 [3113] = "UEFA\\UEFA_Euro_Qualifier\\Fase_de_Grupos_5",
	 [4137] = "UEFA\\UEFA_Euro_Qualifier\\Playoff_1",
	 [5161] = "UEFA\\UEFA_Euro_Qualifier\\Playoff_2",
	 [6185] = "UEFA\\UEFA_Euro_Qualifier\\Playoff_3",
	
	-- Eliminatorias Sulamericanas -- 	
 	

	-- UEFA Nations League
	 [47] = "UEFA\\UEFA\\Nations_League",
	 [1071] = "UEFA\\UEFA_Nations_League",
	 [2095] = "UEFA\\UEFA_Nations_League",
	 [3119] = "UEFA\\UEFA_Nations_League",
	 [4143] = "UEFA\\UEFA_Nations_League",
	 [5167] = "UEFA\\UEFA_Nations_League",
	 [6191] = "UEFA\\UEFA_Nations_League",
	 [7215] = "UEFA\\UEFA_Nations_League",
	 [8239] = "UEFA\\UEFA_Nations_League",
	 [48] = "UEFA\\UEFA_Nations_League", 

	-- AFC Champions League
	 [15] = "AFC\\AFC_Champions_League",
	 [16] = "AFC\\AFC_Champions_League",
	 [1039] = "AFC\\AFC_Champions_League",
	 [2063] = "AFC\\AFC_Champions_League",
	 [3087] = "AFC\\AFC_Champions_League",
	 [4111] = "AFC\\AFC_Champions_League",
	 [5135] = "AFC\\AFC_Champions_League",
	 [6159] = "AFC\\AFC_Champions_League",
	 [7183] = "AFC\\AFC_Champions_League",
	 [8207] = "AFC\\AFC_Champions_League",

	-- Mundial de Clubes -- 
	 [1] = "Mundial_de_Clubes",

	-- Uefa Super Cup --
	 [7] = "UEFA\\Uefa_Super_Cup",
	
	-- FA Cup
	 [23] = "FA_Cup",

	-- Copa da França --
		
	-- Copa_America --
	 [104] = "Conmebol\\Copa_America",                   
	 [1128] = "Conmebol\\Copa_America",                   
	 [2152] = "Conmebol\\Copa_America",                  
	 [3176] = "Conmebol\\Copa_America",      
	 [43] = "Conmebol\\Copa_America",	
	}
	 
	local torneios_round = {
	-- TORNEIOS(ctx.match_info) --
	 [46] = "Oitavas_de_Final",    -- Libertadores
	 [47] = "1_Rodada_da_Repescagem",     -- 2ª Fase Copa 
	 [48] = "2_Rodada_da_Repescagem ",    -- 3ª Fase Copa (Oitavas_de_Final - Libertadores)
	 [51] = "Quartas_de_Final",	 
	 [52] = "Semi_Final", 
	 [53] = "Final",
	 [55] = "All_Clubs",
	 
	-- PARTIDAS -- 
     [0] = "1ª_Rodada",   [1] = "2ª_Rodada",   [2] = "3ª_Rodada",   [3] = "4ª_Rodada",	 [4] = "5ª_Rodada",
     [5] = "6ª_Rodada",   [6] = "7ª_Rodada",   [7] = "8ª_Rodada",   [8] = "9ª_Rodada",	 [9] = "10ª_Rodada",
     [10] = "11ª_Rodada", [11] = "12ª_Rodada", [12] = "13ª_Rodada", [13] = "14ª_Rodada", [14] = "15ª_Rodada",
     [15] = "16ª_Rodada", [16] = "17ª_Rodada", [17] = "18ª_Rodada", [18] = "19ª_Rodada", [19] = "20ª_Rodada",
     [20] = "21ª_Rodada", [21] = "22ª_Rodada", [22] = "23ª_Rodada", [23] = "24ª_Rodada", [24] = "25ª_Rodada",
     [25] = "26ª_Rodada", [26] = "27ª_Rodada", [27] = "28ª_Rodada", [28] = "29ª_Rodada", [29] = "30ª_Rodada",
     [30] = "31ª_Rodada", [31] = "32ª_Rodada", [32] = "33ª_Rodada", [33] = "34ª_Rodada", [34] = "35ª_Rodada",
     [35] = "36ª_Rodada", [36] = "37ª_Rodada", [37] = "38ª_Rodada", 		 
	}

	
     local goal_score = {
	[1] = home_score or away_score, [6] = home_score or away_score,
	[2] = home_score or away_score, [7] = home_score or away_score,
	[3] = home_score or away_score, [8] = home_score or away_score,
	[4] = home_score or away_score, [9] = home_score or away_score,
	[5] = home_score or away_score, [10] = home_score or away_score,
   }	


	
function m.data_ready(ctx, filename)
local stats = match.stats()
local tid = ctx.tournament_id
local home_id = ctx.home_team
local away_id = ctx.away_team
local daynightid = ctx.timeofday
local cuproundid = ctx.match_info
local stadiumid = ctx.stadium

--local nome_do_pais = "Brasil"


	-- INTRO ANTHEM 1 - STADIUM TUNNEL ANTHEM LEADING TO STADIUM ANTHEM (NATIONAL TEAMS)		
	if string.match(filename, "common\\demo\\fixdemoobj\\passage\\passage_%d+\\#Win\\light_config%.fpkd") or 
	   string.match(filename, "common\\demo\\fixdemoobj\\passage\\passage_st%d+\\#Win\\light_config%.fpkd") then			
		if pretunnel_anthem == 0 then
			log("Base Loaded: " .. filename)		  			
			pretunnel_anthem = 1
			kickoff_check = 1											
			if home_id == ctx.home_team and away_id == ctx.away_team and daynightid == ctx.timeofday and tid == ctx.tournament_id and cuproundid == ctx.match_info then
				log("tid: " .. tid)
				log("cuproundid: " .. cuproundid)					
				sound = audio.new(content_root .. horario[daynightid] .. "\\1_Falas_na_Abertura\\" .. torneios[tid] .. "\\" .. torneios_round[cuproundid] .. "\\" .. times[home_id] .. "\\" .. times[home_id] .. "_X_" .. times[away_id] .. ".mp3")	
			end												
				log(string.format("pretunnel anthem starting: %s", sound:get_filename()))			               		
				sound:set_volume(0.7)
				sound:play()	
		end	

	-- ANTHEM 2 - HALF TIME WALK OFF TOURNAMENT TV ANTHEM
	elseif string.match(filename, "tu_half_cmn_%d+_cam_st%d+.fdc") then 			
		if halftime_anthem == 0 then	
			log("Base Loaded: " .. filename)
			halftime_anthem = 1
			kickoff_check = 1						
			if home_id == ctx.home_team and away_id == ctx.away_team and daynightid == ctx.timeofday and tid == ctx.tournament_id and cuproundid == ctx.match_info then	
				log("tid: " .. tid)
				log("cuproundid: " .. cuproundid)							
				sound = audio.new(content_root .. horario[daynightid] .. "\\2_Falas_no_Intervalo\\" .. torneios[tid] .. "\\" .. torneios_round[cuproundid] .. "\\" .. times[home_id] .. "\\" .. times[home_id] .. "_X_" .. times[away_id] .. ".mp3")																
			end	
				log(string.format("halftime anthem starting: %s", sound:get_filename()))       
				sound:set_volume(0.7)
				sound:play()	
		end		

	-- ANTHEM 3 - FULL TIME WALK OFF TOURNAMENT TV ANTHEM
	-- elseif string.match(filename, "tu_full_00_*") or string.match(filename, "tu_full_01_*") or string.match(filename, "tu_full_02_*") then				
		-- if fulltime_anthem == 0 then	
			-- log("Base Loaded: " .. filename) 
			-- fulltime_anthem = 1
			-- kickoff_check = 1  		
			-- if home_id == ctx.home_team and away_id == ctx.away_team and daynightid == ctx.timeofday and tid == ctx.tournament_id and cuproundid == ctx.match_info then
				-- log("tid: " .. tid)
				-- log("cuproundid: " .. cuproundid)									
				-- sound = audio.new(content_root .. horario[daynightid] .. "\\3_Falas_no_Encerramento\\" .. torneios[tid] .. "\\" .. torneios_round[cuproundid] .. "\\" .. times[home_id] .. "\\" .. times[home_id] .. "_X_" .. times[away_id] .. ".mp3")																									
			-- end	
				-- log(string.format("fulltime anthem starting: %s", sound:get_filename()))
				-- sound:set_volume(0.7)
				-- sound:play()					
		-- end	
	-- end
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
	
	
	if kickoff_check == 1 then
		pause_check()
		if pause_state == 0 then
			kickoff_check = 0
			if sound then								
				log(string.format("sound finishing: %s", sound:get_filename()))
				sound:fade_to(0, 1)
				sound:finish()
				sound = nil				
			end
		end	
	end	
	

	-- if filename == "common\\script\\flow\\Match\\MatchSetupRematch.json" then
	-- if filename == "common\\script\\flow\\Match\\MatchEnd.json" then	  
	if string.match(filename, "common\\script\\flow\\Match\\MatchResult.json") then		
		log("Base Loaded: " .. filename) 
		pretunnel_anthem = 0
		halftime_anthem = 0
		fulltime_anthem = 0
		if sound then 
			log(string.format("sound finishing: %s", sound:get_filename()))
			sound:fade_to(0, 2) 
			sound:finish() 
			sound = nil 
		end	
	end	

end	
	

function m.init(ctx)
	base = memory.get_process_info().base
	content_root = ctx.sider_dir .. content_root
	if not audio then
        error("audio library not found. Upgrade your sider")
    end
    ctx.register("livecpk_data_ready", m.data_ready)	
end

return m
