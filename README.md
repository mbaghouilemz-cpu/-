# -
لعبة أكشن ومغامرات في جزيرة مهجورة برسم أنمي مرعب. اختر اسمك، بلدك، ومظهرك، وشكّل فريقك للقتال أونلاين أو أوفلاين. استخدم كافة الأسلحة، ونظام التنكر المخادع لتجاوز التحديات والمخاطر في "جزيرة الموت"، حيث البقاء للأقوى وسط الأسرار والأماكن المرعبة.
extends Node

# ==========================================
# 1. فئة بيانات اللاعب (PLAYER DATA CLASS)
# ==========================================
class PlayerData:
	var player_name: String = "محارب الأنمي"
	var country: String = "الجزائر"
	var skin_style: String = "Anime_Default"
	var clothing_id: int = 1
	var team_id: int = 1
	var is_disguised: bool = false
	var disguised_as: String = ""
	var current_weapon: String = "AK-47"
	var health: float = 100.0

# ==========================================
# 2. المتغيرات العامة للعبة (GLOBAL VARIABLES)
# ==========================================
var local_player: PlayerData = PlayerData.new()
var is_online_mode: bool = false
var teams: Dictionary = {1: [], 2: []}
var available_weapons: Array[String] = [
	"سيف الأنمي الأسطوري", 
	"رشاش AK-47", 
	"قناصة مهجورة", 
	"مسدس تنكر"
]

# ==========================================
# 3. نظام الإعداد والبدء (INITIALIZATION)
# ==========================================
func _ready():
	print("---------------------------------------")
	print("مرحباً بك في لعبة: جزيرة الموت / جحيم مرعب")
	print("---------------------------------------")
	setup_game_ui()
	setup_abandoned_island()

# ==========================================
# 4. واجهة المستخدم والتخصيص (UI & CUSTOMIZATION)
# ==========================================
func setup_game_ui():
	print("[واجهة التحكم]: تم إعداد قائمة اختيار الاسم، البلد، المظهر، والملابس.")

func customize_player(p_name: String, p_country: String, p_skin: String, p_clothes: int):
	local_player.player_name = p_name
	local_player.country = p_country
	local_player.skin_style = p_skin
	local_player.clothing_id = p_clothes
	print("[تخصيص]: تم حفظ بيانات اللاعب:")
	print("  - الاسم: ", local_player.player_name)
	print("  - البلد: ", local_player.country)
	print("  - مظهر الأنمي: ", local_player.skin_style)
	print("  - رقم الملابس: ", local_player.clothing_id)

func select_team(team_number: int):
	if team_number == 1 or team_number == 2:
		local_player.team_id = team_number
		if not teams[team_number].has(local_player):
			teams[team_number].append(local_player)
		print("[الفرق]: تم الانضمام إلى الفريق رقم: ", team_number)
	else:
		print("[خطأ]: رقم الفريق غير صحيح!")

func set_network_mode(online: bool):
	is_online_mode = online
	if is_online_mode:
		print("[الشبكة]: تم تفعيل وضع اللعب عبر الإنترنت (Online MultiPlayer).")
		initialize_online_multiplayer()
	else:
		print("[الشبكة]: تم تفعيل وضع اللعب بدون إنترنت (Offline SinglePlayer).")

func initialize_online_multiplayer():
	var peer = ENetMultiplayerPeer.new()
	peer.create_client("127.0.0.1", 8915)
	multiplayer.multiplayer_peer = peer
	print("[الشبكة]: تم الاتصال بالسيرفر الرئيسي للجزيرة.")

# ==========================================
# 5. نظام الأسلحة والتنكر (WEAPONS & DISGUISE)
# ==========================================
func equip_weapon(weapon_index: int):
	if weapon_index >= 0 and weapon_index < available_weapons.size():
		local_player.current_weapon = available_weapons[weapon_index]
		print("[الأسلحة]: تم تجهيز السلاح: ", local_player.current_weapon)
	else:
		print("[خطأ]: السلاح غير موجود!")

func toggle_disguise(disguise_target_name: String):
	local_player.is_disguised = !local_player.is_disguised
	if local_player.is_disguised:
		local_player.disguised_as = disguise_target_name
		print("[التنكر]: تم تفعيل التنكر! أنت الآن تبدو كـ: ", disguise_target_name)
		print("[التنكر]: تم تغيير شيدر الشخصية (Anime Cel-Shader) لإخفاء هويتك.")
	else:
		local_player.disguised_as = ""
		print("[التنكر]: تم إلغاء التنكر والعودة لشكل الأنمي الأصلي.")

# ==========================================
# 6. بيئة الجزيرة والقرى المهجورة (ISLAND ENVIRONMENT)
# ==========================================
func setup_abandoned_island():
	print("[الجزيرة]: تم تحميل خريطة جزيرة الموت المهجورة.")
	create_abandoned_zone("القرية الغارقة المهجورة")
	create_abandoned_zone("المختبر السري للأنمي")
	create_abandoned_zone("ميناء الأشباح")

func create_abandoned_zone(zone_name: String):
	print("[الخريطة]: إضافة منطقة مهجورة ومظلمة: ", zone_name)

# ==========================================
# 7. نظام المغامرات والتحديات (QUESTS & ADVENTURES)
# ==========================================
func start_adventure_challenge(challenge_name: String):
	print("---------------------------------------")
	print("[بدء تحدي جديد]: ", challenge_name)
	print("المهمة: ابحث عن الأسلحة النادرة وابقَ على قيد الحياة!")
	print("---------------------------------------")

# ==========================================
# 8. تجربة واختبار النظام (TEST SCENARIO)
# ==========================================
func _input(event):
	# اضغط زِر ENTER في اللعبة لتشغيل تجربة شاملة للأنظمة
	if Input.is_action_just_pressed("ui_accept"):
		run_full_game_test()

func run_full_game_test():
	print("\n--- بدء تجربة أنظمة اللعبة بالكامل ---")
	
	# 1. إدخال بيانات الشخصية
	customize_player("البطل_الغامض", "مصر", "Anime_Hero_Style", 3)
	
	# 2. تحديد الوضع والفرقة
	set_network_mode(false) # يمكن تغييرها إلى true للأونلاين
	select_team(1)
	
	# 3. اختيار الأسلحة والتنكر
	equip_weapon(0) # اختيار سيف الأنمي
	toggle_disguise("جندي حراسة مهجور")
	
	# 4. بدء التحديات
	start_adventure_challenge("تحدي الهروب من جحيم جزيرة الموت")
