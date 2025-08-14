# Ataextends CharacterBody3D

@export var speed: float = 5.0
@export var mouse_sensitivity: float = 0.2
@export var jump_velocity: float = 4.5
@export var gravity: float = ProjectSettings.get_setting("physics/3d/default_gravity")

var camera
var rotation_x = 0.0

func _ready():
    camera = $Camera3D
    Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED)

func _process(delta):
    if Input.is_action_just_pressed("ui_cancel"):
        Input.set_mouse_mode(Input.MOUSE_MODE_VISIBLE)

func _input(event):
    if event is InputEventMouseMotion and Input.get_mouse_mode() == Input.MOUSE_MODE_CAPTURED:
        rotation_x -= event.relative.y * mouse_sensitivity
        rotation_x = clamp(rotation_x, -90, 90)
        camera.rotation_degrees.x = rotation_x
        rotation_degrees.y -= event.relative.x * mouse_sensitivity

func _physics_process(delta):
    var velocity = Vector3.ZERO

    # الحركة للأمام والخلف
    var forward = -transform.basis.z
    var right = transform.basis.x

    if Input.is_action_pressed("move_forward"):
        velocity += forward
    if Input.is_action_pressed("move_back"):
        velocity -= forward
    if Input.is_action_pressed("move_left"):
        velocity -= right
    if Input.is_action_pressed("move_right"):
        velocity += right

    velocity = velocity.normalized() * speed

    # الجاذبية
    if not is_on_floor():
        velocity.y = self.velocity.y - gravity * delta
    else:
        if Input.is_action_just_pressed("jump"):
            velocity.y = jump_velocity
        else:
            velocity.y = -0.1

    self.velocity = velocity
    move_and_slide()
re
extends CharacterBody3D

@export var speed: float = 5.0
@export var mouse_sensitivity: float = 0.2
@export var jump_velocity: float = 4.5
@export var gravity: float = ProjectSettings.get_setting("physics/3d/default_gravity")

var camera
var rotation_x = 0.0

func _ready():
    camera = $Camera3D
    Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED)

func _process(delta):
    if Input.is_action_just_pressed("ui_cancel"):
        Input.set_mouse_mode(Input.MOUSE_MODE_VISIBLE)

func _input(event):
    if event is InputEventMouseMotion and Input.get_mouse_mode() == Input.MOUSE_MODE_CAPTURED:
        rotation_x -= event.relative.y * mouse_sensitivity
        rotation_x = clamp(rotation_x, -90, 90)
        camera.rotation_degrees.x = rotation_x
        rotation_degrees.y -= event.relative.x * mouse_sensitivity

func _physics_process(delta):
    var velocity = Vector3.ZERO

    # الحركة للأمام والخلف
    var forward = -transform.basis.z
    var right = transform.basis.x

    if Input.is_action_pressed("move_forward"):
        velocity += forward
    if Input.is_action_pressed("move_back"):
        velocity -= forward
    if Input.is_action_pressed("move_left"):
        velocity -= right
    if Input.is_action_pressed("move_right"):
        velocity += right

    velocity = velocity.normalized() * speed

    # الجاذبية
    if not is_on_floor():
        velocity.y = self.velocity.y - gravity * delta
    else:
        if Input.is_action_just_pressed("jump"):
            velocity.y = jump_velocity
        else:
            velocity.y = -0.1

    self.velocity = velocity
    move_and_slide() extends CharacterBody3D

@export var speed: float = 5.0
@export var mouse_sensitivity: float = 0.2
@export var jump_velocity: float = 4.5
@export var gravity: float = ProjectSettings.get_setting("physics/3d/default_gravity")
@export var reach_distance: float = 5.0

var camera
var rotation_x = 0.0
var block_scene = preload("res://Block.tscn") # ملف البلوك

func _ready():
    camera = $Camera3D
    Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED)

func _process(delta):
    if Input.is_action_just_pressed("ui_cancel"):
        Input.set_mouse_mode(Input.MOUSE_MODE_VISIBLE)

    if Input.is_action_just_pressed("break_block"):
        break_block()
    if Input.is_action_just_pressed("place_block"):
        place_block()

func _input(event):
    if event is InputEventMouseMotion and Input.get_mouse_mode() == Input.MOUSE_MODE_CAPTURED:
        rotation_x -= event.relative.y * mouse_sensitivity
        rotation_x = clamp(rotation_x, -90, 90)
        camera.rotation_degrees.x = rotation_x
        rotation_degrees.y -= event.relative.x * mouse_sensitivity

func _physics_process(delta):
    var dir = Vector3.ZERO
    var forward = -transform.basis.z
    var right = transform.basis.x

    if Input.is_action_pressed("move_forward"):
        dir += forward
    if Input.is_action_pressed("move_back"):
        dir -= forward
    if Input.is_action_pressed("move_left"):
        dir -= right
    if Input.is_action_pressed("move_right"):
        dir += right

    dir = dir.normalized() * speed

    if not is_on_floor():
        dir.y = self.velocity.y - gravity * delta
    else:
        if Input.is_action_just_pressed("jump"):
            dir.y = jump_velocity
        else:
            dir.y = -0.1

    self.velocity = dir
    move_and_slide()

func break_block():
    var space_state = get_world_3d().direct_space_state
    var result = space_state.intersect_ray(camera.global_transform.origin, camera.global_transform.origin + camera.global_transform.basis.z * -reach_distance)
    if result and result.collider.is_in_group("blocks"):
        result.collider.queue_free()

func place_block():
    var space_state = get_world_3d().direct_space_state
    var result = space_state.intersect_ray(camera.global_transform.origin, camera.global_transform.origin + camera.global_transform.basis.z * -reach_distance)
    if result:
        var pos = result.position + result.normal * 0.5
        pos = Vector3(round(pos.x), round(pos.y), round(pos.z))
        var new_block = block_scene.instantiate()
        new_block.position = pos
        get_tree().root.add_child(new_block) extends StaticBody3D

func _ready():
    add_to_group("blocks") extends Node3D

var block_scene = preload("res://Block.tscn")

func _ready():
    for x in range(-10, 10):
        for z in range(-10, 10):
            var block = block_scene.instantiate()
            block.position = Vector3(x, 0, z)
            add_child(block) extends CharacterBody3D

@export var speed = 5.0
@export var mouse_sensitivity = 0.2
@export var jump_velocity = 4.5
@export var gravity = ProjectSettings.get_setting("physics/3d/default_gravity")
@export var reach_distance = 5.0

var camera
var rotation_x = 0.0
var block_scene = preload("res://Block.tscn")
var break_sound = preload("res://assets/sounds/break.wav")
var place_sound = preload("res://assets/sounds/place.wav")

func _ready():
    camera = $Camera3D
    Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED)

func _process(delta):
    if Input.is_action_just_pressed("ui_cancel"):
        Input.set_mouse_mode(Input.MOUSE_MODE_VISIBLE)

    if Input.is_action_just_pressed("break_block"):
        break_block()
    if Input.is_action_just_pressed("place_block"):
        place_block()

func _input(event):
    if event is InputEventMouseMotion and Input.get_mouse_mode() == Input.MOUSE_MODE_CAPTURED:
        rotation_x -= event.relative.y * mouse_sensitivity
        rotation_x = clamp(rotation_x, -90, 90)
        camera.rotation_degrees.x = rotation_x
        rotation_degrees.y -= event.relative.x * mouse_sensitivity

func _physics_process(delta):
    var dir = Vector3.ZERO
    var forward = -transform.basis.z
    var right = transform.basis.x

    if Input.is_action_pressed("move_forward"):
        dir += forward
    if Input.is_action_pressed("move_back"):
        dir -= forward
    if Input.is_action_pressed("move_left"):
        dir -= right
    if Input.is_action_pressed("move_right"):
        dir += right

    dir = dir.normalized() * speed

    if not is_on_floor():
        dir.y = self.velocity.y - gravity * delta
    else:
        if Input.is_action_just_pressed("jump"):
            dir.y = jump_velocity
        else:
            dir.y = -0.1

    self.velocity = dir
    move_and_slide()

func break_block():
    var space_state = get_world_3d().direct_space_state
    var result = space_state.intersect_ray(camera.global_transform.origin, camera.global_transform.origin + camera.global_transform.basis.z * -reach_distance)
    if result and result.collider.is_in_group("blocks"):
        var audio = AudioStreamPlayer3D.new()
        audio.stream = break_sound
        audio.position = result.position
        get_tree().root.add_child(audio)
        audio.play()
        result.collider.queue_free()

func place_block():
    var space_state = get_world_3d().direct_space_state
    var result = space_state.intersect_ray(camera.global_transform.origin, camera.global_transform.origin + camera.global_transform.basis.z * -reach_distance)
    if result:
        var pos = result.position + result.normal * 0.5
        pos = Vector3(round(pos.x), round(pos.y), round(pos.z))
        var new_block = block_scene.instantiate()
        new_block.position = pos
        get_tree().root.add_child(new_block)

        var audio = AudioStreamPlayer3D.new()
        audio.stream = place_sound
        audio.position = pos
        get_tree().root.add_child(audio)
        audio.play() res://
   Player.gd
   Player.tscn
   Block.gd
   Block.tscn
   World.gd
   World.tscn
   assets/
       textures/
           grass.png
           stone.png
           wood.png
           water.png
       sounds/
           break.wav
           place.wav
   SunSky.tres extends Node3D

var block_scene = preload("res://Block.tscn")
var save_file = "user://world.save"

func _ready():
    if FileAccess.file_exists(save_file):
        load_world()
    else:
        generate_world()

func generate_world():
    for x in range(-20, 20):
        for z in range(-20, 20):
            var block = block_scene.instantiate()
            block.position = Vector3(x, 0, z)
            add_child(block)

func save_world():
    var data = []
    for block in get_tree().get_nodes_in_group("blocks"):
        data.append({"pos": block.position, "texture": block.texture_path})
    var file = FileAccess.open(save_file, FileAccess.WRITE)
    file.store_var(data)
    file.close()

func load_world():
    var file = FileAccess.open(save_file, FileAccess.READ)
    var data = file.get_var()
    file.close()
    for b in data:
        var block = block_scene.instantiate()
        block.position = b["pos"]
        block.texture_path = b["texture"]
        add_child(block)

func _exit_tree():
    save_world() var time_of_day = 0.0 # 0 = فجر، 0.5 = غروب، 1 = فجر جديد
var day_length = 300.0 # طول اليوم بالثواني

func _process(delta):
    time_of_day += delta / day_length
    if time_of_day > 1:
        time_of_day = 0
    $DirectionalLight3D.rotation_degrees.x = lerp(-45, 225, time_of_day) # حركة الشمس
    RenderServer.environment_set_background(Environment.BG_SKY)func start_rain():
    $RainParticles.emitting = true
    $RainSound.play()

func stop_rain():
    $RainParticles.emitting = false
    $RainSound.stop()var season = "spring"
var season_timer = 0.0
var season_length = 1200.0 # 20 دقيقة

func _process(delta):
    season_timer += delta
    if season_timer >= season_length:
        season_timer = 0
        change_season()

func change_season():
    match season:
        "spring": season = "summer"
        "summer": season = "autumn"
        "autumn": season = "winter"
        "winter": season = "spring" extends CharacterBody3D

@export var move_speed = 1.0
var target_position = Vector3.ZERO

func _ready():
    choose_new_target()

func _physics_process(delta):
    var direction = (target_position - global_position).normalized()
    velocity = direction * move_speed
    move_and_slide()
    if global_position.distance_to(target_position) < 1:
        choose_new_target()

func choose_new_target():
    target_position = global_position + Vector3(randf() * 10 - 5, 0, randf() * 10 - 5) # res://scripts/player.gd
extends CharacterBody3D

@onready var cam: Camera3D = $Cam
@onready var aim: RayCast3D = $Cam/AimRay

var mouse_sensitivity := 0.1
var speed := 8.0
var sprint_multiplier := 1.8
var jump_velocity := 4.5
var gravity := ProjectSettings.get_setting("physics/3d/default_gravity")

var yaw := 0.0
var pitch := 0.0

# هوت بار لاختيار نوع البلوك
var hotbar := [Globals.BlockId.GRASS, Globals.BlockId.DIRT, Globals.BlockId.STONE, Globals.BlockId.SAND, Globals.BlockId.WOOD]
var hot_index := 0

func _ready():
	Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED)

func _unhandled_input(event):
	if event is InputEventMouseMotion and Input.get_mouse_mode() == Input.MOUSE_MODE_CAPTURED:
		yaw -= event.relative.x * mouse_sensitivity * 0.01
		pitch -= event.relative.y * mouse_sensitivity * 0.01
		pitch = clamp(pitch, deg_to_rad(-89), deg_to_rad(89))
		rotation.y = yaw
		cam.rotation.x = pitch

	if event.is_action_pressed("ui_cancel"):
		# ESC
		var mode = Input.get_mouse_mode()
		Input.set_mouse_mode(mode == Input.MOUSE_MODE_CAPTURED ? Input.MOUSE_MODE_VISIBLE : Input.MOUSE_MODE_CAPTURED)

	if event.is_action_pressed("hot_next"):
		hot_index = (hot_index + 1) % hotbar.size()
	if event.is_action_pressed("hot_prev"):
		hot_index = (hot_index - 1 + hotbar.size()) % hotbar.size()

	if event.is_action_pressed("break_block"):
		_break_block()
	if event.is_action_pressed("place_block"):
		_place_block()

func _physics_process(delta):
	var input_dir = Vector3.ZERO
	var forward = -transform.basis.z
	var right = transform.basis.x

	if Input.is_action_pressed("move_forward"):
		input_dir += forward
	if Input.is_action_pressed("move_backward"):
		input_dir -= forward
	if Input.is_action_pressed("move_left"):
		input_dir -= right
	if Input.is_action_pressed("move_right"):
		input_dir += right

	input_dir.y = 0
	input_dir = input_dir.normalized()

	var current_speed = speed * (Input.is_action_pressed("sprint") ? sprint_multiplier : 1.0)
	velocity.x = input_dir.x * current_speed
	velocity.z = input_dir.z * current_speed

	if not is_on_floor():
		velocity.y -= gravity * delta
	elif Input.is_action_just_pressed("jump"):
		velocity.y = jump_velocity

	move_and_slide()

func _break_block():
	if not aim.is_colliding():
		return
	var pos = aim.get_collision_point() - aim.get_collision_normal() * 0.01
	Globals.call_deferred("set_block_world", pos.floor(), Globals.BlockId.AIR)

func _place_block():
	if not aim.is_colliding():
		return
	var place_pos = aim.get_collision_point() + aim.get_collision_normal() * 0.01
	var id = hotbar[hot_index]
	Globals.call_deferred("set_block_world", place_pos.floor(), id) # res://scripts/chunk.gd
extends Node3D
class_name Chunk

@export var chunk_x: int
@export var chunk_z: int

var blocks: PackedInt32Array # 3D مضغوط: x + z*SIZE + y*SIZE*SIZE
var dirty := true

static var SIZE := Globals.CHUNK_SIZE
static var HEIGHT := Globals.CHUNK_HEIGHT

func _ready():
	blocks = PackedInt32Array()
	blocks.resize(SIZE * SIZE * HEIGHT)

func index(lx:int, ly:int, lz:int) -> int:
	return lx + lz * SIZE + ly * SIZE * SIZE

func get_local(lx:int, ly:int, lz:int) -> int:
	if ly < 0 or ly >= HEIGHT: return Globals.BlockId.AIR
	return blocks[index(lx,ly,lz)]

func set_local(lx:int, ly:int, lz:int, id:int) -> void:
	if ly < 0 or ly >= HEIGHT: return
	blocks[index(lx,ly,lz)] = id
	dirty = true

func rebuild_mesh():
	if not dirty: return
	dirty = false
	# نبني Mesh بسطح واحد Vertex Colors
	var st := SurfaceTool.new()
	st.begin(Mesh.PRIMITIVE_TRIANGLES)

	for ly in HEIGHT:
		for lz in SIZE:
			for lx in SIZE:
				var id = get_local(lx,ly,lz)
				if id == Globals.BlockId.AIR:
					continue
				_add_block_faces(st, lx,ly,lz, id)

	var mesh := st.commit()
	if mesh:
		var mi = MeshInstance3D.new()
		mi.mesh = mesh
		mi.name = "Mesh"
		# احذف القديم
		for c in get_children():
			if c is MeshInstance3D:
				remove_child(c)
				c.queue_free()
		add_child(mi)

func _add_block_faces(st:SurfaceTool, lx:int, ly:int, lz:int, id:int) -> void:
	var c = BlockData.color_of(id)
	var wx = lx + chunk_x*SIZE
	var wy = ly
	var wz = lz + chunk_z*SIZE

	# لكل وجه: لو الجار غير مصمت، ارسم الوجه
	# Left (-X)
	if _is_air(lx-1,ly,lz):
		_face(st, Vector3(wx,wy,wz), Vector3(-1,0,0), c)
	# Right (+X)
	if _is_air(lx+1,ly,lz):
		_face(st, Vector3(wx+1,wy,wz), Vector3(1,0,0), c)
	# Bottom (-Y)
	if _is_air(lx,ly-1,lz):
		_face(st, Vector3(wx,wy,wz), Vector3(0,-1,0), c)
	# Top (+Y)
	if _is_air(lx,ly+1,lz):
		_face(st, Vector3(wx,wy+1,wz), Vector3(0,1,0), c)
	# Back (-Z)
	if _is_air(lx,ly,lz-1):
		_face(st, Vector3(wx,wy,wz), Vector3(0,0,-1), c)
	# Front (+Z)
	if _is_air(lx,ly,lz+1):
		_face(st, Vector3(wx,wy,wz+1), Vector3(0,0,1), c)

func _is_air(lx:int, ly:int, lz:int) -> bool:
	if ly < 0 or ly >= HEIGHT: return true
	if lx < 0 or lx >= SIZE: return true
	if lz < 0 or lz >= SIZE: return true
	return get_local(lx,ly,lz) == Globals.BlockId.AIR

# يرسم وجهاً لمحور dir، عند origin كتلة 1x1x1
func _face(st:SurfaceTool, o:Vector3, dir:Vector3, col:Color) -> void:
	var a:Vector3; var b:Vector3; var c:Vector3; var d:Vector3
	if dir.x != 0:
		# X face
		var x = o.x
		var y = o.y
		var z = o.z
		if dir.x > 0:
			a = Vector3(x, y, z)
			b = Vector3(x, y+1, z)
			c = Vector3(x, y+1, z+1)
			d = Vector3(x, y, z+1)
		else:
			a = Vector3(x, y, z+1)
			b = Vector3(x, y+1, z+1)
			c = Vector3(x, y+1, z)
			d = Vector3(x, y, z)
	elif dir.y != 0:
		# Y face
		var x = o.x
		var y = o.y
		var z = o.z
		if dir.y > 0:
			a = Vector3(x, y, z)
			b = Vector3(x+1, y, z)
			c = Vector3(x+1, y, z+1)
			d = Vector3(x, y, z+1)
		else:
			a = Vector3(x, y, z+1)
			b = Vector3(x+1, y, z+1)
			c = Vector3(x+1, y, z)
			d = Vector3(x, y, z)
	else:
		# Z face
		var x = o.x
		var y = o.y
		var z = o.z
		if dir.z > 0:
			a = Vector3(x, y, z)
			b = Vector3(x, y+1, z)
			c = Vector3(x+1, y+1, z)
			d = Vector3(x+1, y, z)
		else:
			a = Vector3(x+1, y, z)
			b = Vector3(x+1, y+1, z)
			c = Vector3(x, y+1, z)
			d = Vector3(x, y, z)
	
	var n = (b - a).cross(d - a).normalized()
	# مثلثان
	st.add_color(col); st.add_normal(n); st.add_vertex(a)
	st.add_color(col); st.add_normal(n); st.add_vertex(b)
	st.add_color(col); st.add_normal(n); st.add_vertex(c)
	st.add_color(col); st.add_normal(n); st.add_vertex(a)
	st.add_color(col); st.add_normal(n); st.add_vertex(c)
	st.add_color(col); st.add_normal(n); st.add_vertex(d) خ # res://scripts/world.gd
extends Node3D
class_name World

@onready var player: CharacterBody3D = get_node("../Player") if has_node("../Player") else null

var chunks := {} # مفتاحه Vector2i(cx,cz) -> Chunk
var noise := FastNoiseLite.new()

func _ready():
	# ربط دوال عامة لـ Globals ليستعملها اللاعب في الكسر/الوضع
	Globals.set_block_world = set_block_world
	Globals.get_block_world = get_block_world

	# إعداد الضوضاء
	noise.noise_type = FastNoiseLite.TYPE_PERLIN
	noise.frequency = 0.015

	# تحميل أولي حول (0,0)
	for cz in range(-Globals.VIEW_DISTANCE_CHUNKS, Globals.VIEW_DISTANCE_CHUNKS+1):
		for cx in range(-Globals.VIEW_DISTANCE_CHUNKS, Globals.VIEW_DISTANCE_CHUNKS+1):
			_load_chunk(cx, cz)

	# ضع اللاعب فوق سطح مناسب
	if player:
		player.position = Vector3(0, terrain_height(0,0) + 5, 0)

func _process(_delta):
	# تحديث تحميل/تفريغ التشانكات حول اللاعب
	if not player:
		return
	var p = player.global_transform.origin
	var pcx = floori(p.x / Globals.CHUNK_SIZE)
	var pcz = floori(p.z / Globals.CHUNK_SIZE)
	for cz in range(pcz-Globals.VIEW_DISTANCE_CHUNKS, pcz+Globals.VIEW_DISTANCE_CHUNKS+1):
		for cx in range(pcx-Globals.VIEW_DISTANCE_CHUNKS, pcx+Globals.VIEW_DISTANCE_CHUNKS+1):
			if Vector2i(cx,cz) not in chunks:
				_load_chunk(cx, cz)

func _load_chunk(cx:int, cz:int) -> void:
	var key = Vector2i(cx,cz)
	if key in chunks:
		return
	var ch := Chunk.new()
	ch.chunk_x = cx
	ch.chunk_z = cz
	add_child(ch)
	ch.position = Vector3(cx * Globals.CHUNK_SIZE, 0, cz * Globals.CHUNK_SIZE)
	chunks[key] = ch
	_generate_chunk(ch)
	ch.rebuild_mesh()

func _generate_chunk(ch:Chunk) -> void:
	var SIZE = Globals.CHUNK_SIZE
	var HEIGHT = Globals.CHUNK_HEIGHT
	for z in SIZE:
		for x in SIZE:
			var wx = x + ch.chunk_x * SIZE
			var wz = z + ch.chunk_z * SIZE
			var h = terrain_height(wx, wz)
			for y in HEIGHT:
				var id = Globals.BlockId.AIR
				if y <= h:
					if y == h:
						id = Globals.BlockId.GRASS
					elif y > h - 3:
						id = Globals.BlockId.DIRT
					else:
						id = Globals.BlockId.STONE
				ch.set_local(x,y,z,id)

func terrain_height(wx:int, wz:int) -> int:
	var n = noise.get_noise_2d(float(wx), float(wz)) # -1..1
	var base = 50
	var amp = 20
	return clampi(int(base + n * amp), Globals.MIN_Y+1, Globals.MAX_Y-1)

# واجهة عامة لوضع/كسر بلوك بإحداثيات عالم
func set_block_world(wpos: Vector3, id:int) -> void:
	var wx = int(floor(wpos.x))
	var wy = int(floor(wpos.y))
	var wz = int(floor(wpos.z))
	if wy < Globals.MIN_Y or wy > Globals.MAX_Y:
		return
	var cc = Globals.world_to_chunk_coords(wx, wy, wz)
	var lc = Globals.world_to_local_in_chunk(wx, wy, wz)
	var key = Vector2i(cc.x, cc.z)
	if key not in chunks:
		_load_chunk(cc.x, cc.z)
	var ch:Chunk = chunks[key]
	ch.set_local(lc.x, lc.y, lc.z, id)
	ch.rebuild_mesh()

func get_block_world(wpos: Vector3) -> int:
	var wx = int(floor(wpos.x))
	var wy = int(floor(wpos.y))
	var wz = int(floor(wpos.z))
	var cc = Globals.world_to_chunk_coords(wx, wy, wz)
	var lc = Globals.world_to_local_in_chunk(wx, wy, wz)
	var key = Vector2i(cc.x, cc.z)
	if key not in chunks:
		return Globals.BlockId.AIR
	return (chunks[key] as Chunk).get_local(lc.x, lc.y, lc.z) scripts/biome_data.gd
scripts/animal_spawner.gd
scenes/animals/(Cow.tscn, Sheep.tscn, Wolf.tscn, Horse.tscn, Camel.tscn, Rabbit.tscn, PolarBear.tscn, Chicken.tscn) # ... أعلى الملف كما هو
static var BLOCKS := {
    Globals.BlockId.AIR:   {"solid": false, "color": Color(0,0,0,0)},
    Globals.BlockId.DIRT:  {"solid": true,  "color": Color(0.36, 0.22, 0.10)},
    Globals.BlockId.GRASS: {"solid": true,  "color": Color(0.25, 0.6, 0.2)},
    Globals.BlockId.STONE: {"solid": true,  "color": Color(0.5, 0.5, 0.5)},
    Globals.BlockId.SAND:  {"solid": true,  "color": Color(0.9, 0.85, 0.6)},
    Globals.BlockId.WOOD:  {"solid": true,  "color": Color(0.45, 0.28, 0.12)},
    Globals.BlockId.SNOW:  {"solid": true,  "color": Color(0.95, 0.97, 1.0)},
    Globals.BlockId.LEAVES:{"solid": true,  "color": Color(0.2, 0.6, 0.25)},
    Globals.BlockId.WATER: {"solid": false, "color": Color(0.2, 0.35, 0.9, 0.7)}
} enum BlockId {
    AIR = 0, DIRT = 1, GRASS = 2, STONE = 3, SAND = 4, WOOD = 5,
    SNOW = 6, LEAVES = 7, WATER = 8
} extends Resource
class_name BiomeData

# وصف بيوم: اسم + كتل السطح + قائمة حيوانات + خصائص مناخية
class Biome:
    var name: String
    var surface: int
    var subsoil: int
    var stone: int
    var animals: Array[String]
    var temp_min: float
    var temp_max: float
    var humid_min: float
    var humid_max: float
    var height_bias: int = 0 # يرفع/يخفض الارتفاع المتوسط
    func _init(_name:String, _surface:int, _subsoil:int, _stone:int, _animals:Array[String],
        _tmin:float, _tmax:float, _hmin:float, _hmax:float, _bias:int=0):
        name=_name; surface=_surface; subsoil=_subsoil; stone=_stone; animals=_animals
        temp_min=_tmin; temp_max=_tmax; humid_min=_hmin; humid_max=_hmax; height_bias=_bias

static var BIOMES: Array = [
    Biome.new("plains", Globals.BlockId.GRASS, Globals.BlockId.DIRT, Globals.BlockId.STONE,
        ["Cow","Sheep","Horse","Chicken"], 0.4, 0.8, 0.3, 0.8, 0),
    Biome.new("forest", Globals.BlockId.GRASS, Globals.BlockId.DIRT, Globals.BlockId.STONE,
        ["Deer","Wolf","Rabbit"], 0.4, 0.7, 0.5, 1.0, 3),
    Biome.new("desert", Globals.BlockId.SAND, Globals.BlockId.SAND, Globals.BlockId.STONE,
        ["Camel","Rabbit"], 0.8, 1.0, 0.0, 0.3, -2),
    Biome.new("taiga", Globals.BlockId.SNOW, Globals.BlockId.DIRT, Globals.BlockId.STONE,
        ["Wolf","Rabbit"], 0.2, 0.5, 0.3, 0.8, 2),
    Biome.new("tundra", Globals.BlockId.SNOW, Globals.BlockId.STONE, Globals.BlockId.STONE,
        ["PolarBear"], 0.0, 0.3, 0.2, 0.7, -1),
    Biome.new("savanna", Globals.BlockId.GRASS, Globals.BlockId.DIRT, Globals.BlockId.STONE,
        ["Horse","Cow"], 0.7, 0.95, 0.2, 0.6, 1),
    Biome.new("swamp", Globals.BlockId.GRASS, Globals.BlockId.DIRT, Globals.BlockId.STONE,
        ["Frog","Cow"], 0.6, 0.9, 0.7, 1.0, -1)
]

static func pick_biome(temp: float, humid: float) -> Biome:
    for b in BIOMES:
        if temp >= b.temp_min and temp <= b.temp_max and humid >= b.humid_min and humid <= b.humid_max:
            return b
    return BIOMES[0] # افتراضي: سهول # أعلى الملف
const SEA_LEVEL := 52
var temp_noise := FastNoiseLite.new()
var humid_noise := FastNoiseLite.new()
var spawn_timer := 0.0
var spawn_interval := 5.0 # كل 5 ثواني نحاول توليد حيوانات قريبة

func _ready():
    Globals.set_block_world = set_block_world
    Globals.get_block_world = get_block_world

    noise.noise_type = FastNoiseLite.TYPE_PERLIN
    noise.frequency = 0.015

    temp_noise.noise_type = FastNoiseLite.TYPE_SIMPLEX
    temp_noise.frequency = 0.008
    humid_noise.noise_type = FastNoiseLite.TYPE_SIMPLEX
    humid_noise.frequency = 0.008

    for cz in range(-Globals.VIEW_DISTANCE_CHUNKS, Globals.VIEW_DISTANCE_CHUNKS+1):
        for cx in range(-Globals.VIEW_DISTANCE_CHUNKS, Globals.VIEW_DISTANCE_CHUNKS+1):
            _load_chunk(cx, cz)

    if player:
        player.position = Vector3(0, terrain_height(0,0) + 5, 0)

func _process(delta):
    if not player: return
    var p = player.global_transform.origin
    var pcx = floori(p.x / Globals.CHUNK_SIZE)
    var pcz = floori(p.z / Globals.CHUNK_SIZE)
    for cz in range(pcz-Globals.VIEW_DISTANCE_CHUNKS, pcz+Globals.VIEW_DISTANCE_CHUNKS+1):
        for cx in range(pcx-Globals.VIEW_DISTANCE_CHUNKS, pcx+Globals.VIEW_DISTANCE_CHUNKS+1):
            if Vector2i(cx,cz) not in chunks:
                _load_chunk(cx, cz)

    spawn_timer += delta
    if spawn_timer >= spawn_interval:
        spawn_timer = 0
        _try_spawn_animals_near(player.global_position)

func terrain_height(wx:int, wz:int) -> int:
    var n = noise.get_noise_2d(float(wx), float(wz)) # -1..1
    var base = 50
    var amp = 20
    # تأثير البيوم على الارتفاع
    var t = remap(temp_noise.get_noise_2d(wx, wz), -1, 1, 0, 1)
    var h = remap(humid_noise.get_noise_2d(wx, wz), -1, 1, 0, 1)
    var biome = BiomeData.pick_biome(t, h)
    return clampi(int(base + n*amp + biome.height_bias), Globals.MIN_Y+1, Globals.MAX_Y-1)

func get_biome(wx:int, wz:int) -> BiomeData.Biome:
    var t = remap(temp_noise.get_noise_2d(wx, wz), -1, 1, 0, 1)
    var h = remap(humid_noise.get_noise_2d(wx, wz), -1, 1, 0, 1)
    return BiomeData.pick_biome(t, h)

func _generate_chunk(ch:Chunk) -> void:
    var SIZE = Globals.CHUNK_SIZE
    var HEIGHT = Globals.CHUNK_HEIGHT
    for z in SIZE:
        for x in SIZE:
            var wx = x + ch.chunk_x * SIZE
            var wz = z + ch.chunk_z * SIZE
            var biome = get_biome(wx, wz)
            var h = terrain_height(wx, wz)
            for y in HEIGHT:
                var id = Globals.BlockId.AIR
                if y <= h:
                    if y == h:
                        id = biome.surface
                    elif y > h - 3:
                        id = biome.subsoil
                    else:
                        id = biome.stone
                # مياه البحر
                if y <= SEA_LEVEL and y > h:
                    id = Globals.BlockId.WATER
                ch.set_local(x,y,z,id)
            # زراعة أشجار/صبار/ثلج خفيف حسب البيوم
            if randi() % 32 == 0 and h > SEA_LEVEL: # لا نزرع تحت الماء
                _maybe_place_feature(wx, h+1, wz, biome)

func _maybe_place_feature(wx:int, wy:int, wz:int, biome:BiomeData.Biome) -> void:
    match biome.name:
        "forest":
            _place_tree(wx, wy, wz, 4, 2)
        "taiga":
            _place_tree(wx, wy, wz, 6, 2, pine=true)
        "desert":
            _place_cactus(wx, wy, wz, 3)
        "tundra":
            Globals.set_block_world(Vector3(wx,wy,wz), Globals.BlockId.SNOW)
        "swamp":
            if randi()%2==0: Globals.set_block_world(Vector3(wx,wy,wz), Globals.BlockId.LEAVES)

func _place_tree(wx:int, wy:int, wz:int, height:int, crown:int, pine:bool=false) -> void:
    for i in range(height):
        Globals.set_block_world(Vector3(wx, wy+i, wz), Globals.BlockId.WOOD)
    for x in range(-crown, crown+1):
        for y in range(-crown, crown+1):
            for z in range(-crown, crown+1):
                if pine and abs(x)+abs(z)+abs(y) > crown+1: continue
                if x==0 and y<0 and z==0: continue
                Globals.set_block_world(Vector3(wx+x, wy+height-1+y, wz+z), Globals.BlockId.LEAVES)

func _place_cactus(wx:int, wy:int, wz:int, height:int) -> void:
    for i in range(height):
        Globals.set_block_world(Vector3(wx, wy+i, wz), Globals.BlockId.WOOD) # مؤقتًا كخشب كمجسم عمودي

func _try_spawn_animals_near(center:Vector3):
    var wx = int(round(center.x))
    var wz = int(round(center.z))
    var biome = get_biome(wx, wz)
    if biome.animals.is_empty():
        return
    var count = 1 + randi()%3
    for i in count:
        var name = biome.animals[randi()%biome.animals.size()]
        _spawn_animal(name, Vector3(wx + randi()%16-8, terrain_height(wx,wz)+2, wz + randi()%16-8))

func _spawn_animal(aname:String, pos:Vector3):
    var path = "res://scenes/animals/%s.tscn" % aname
    if not ResourceLoader.exists(path):
        return
    var scene = load(path) as PackedScene
    var inst = scene.instantiate()
    inst.position = pos
    add_child(inst) extends CharacterBody3D
@export var move_speed: float = 1.2
var target: Vector3

func _ready():
    _pick_new_target()

func _physics_process(delta):
    if global_position.distance_to(target) < 0.5:
        _pick_new_target()
    var dir = (target - global_position).normalized()
    velocity = dir * move_speed
    move_and_slide()

func _pick_new_target():
    target = global_position + Vector3(randf()*10.0-5.0, 0, randf()*10.0-5.0) func change_season():
    match season:
        "spring": season = "summer"; rain_chance = 0.2
        "summer": season = "autumn"; rain_chance = 0.05
        "autumn": season = "winter"; rain_chance = 0.25
        "winter": season = "spring"; snow_chance = 0.3 extends Node3D

var launch_speed = 0.0
var launched = false

func _process(delta):
    if launched:
        translate(Vector3.UP * launch_speed * delta)
        launch_speed += delta * 5 # تسارع تدريجي
        if global_position.y > 1000: # ارتفاع الدخول للفضاء
            get_tree().change_scene_to_file("res://Scenes/Space.tscn")

func launch():
    launched = true
    $CountdownSound.play()extends Node3D

func _ready():
    spawn_planets()
    spawn_stars()

func spawn_planets():
    var planet_data = [
        {"name": "Moon", "pos": Vector3(100, 0, 0), "biome": "lunar"},
        {"name": "Mars", "pos": Vector3(-150, 20, 50), "biome": "desert"},
        {"name": "IceWorld", "pos": Vector3(200, -50, -100), "biome": "ice"}
    ]
    for planet in planet_data:
        var planet_mesh = MeshInstance3D.new()
        planet_mesh.mesh = SphereMesh.new()
        planet_mesh.scale = Vector3(20, 20, 20)
        planet_mesh.position = planet["pos"]
        add_child(planet_mesh)

func spawn_stars():
    for i in range(200):
        var star = MeshInstance3D.new()
        star.mesh = SphereMesh.new()
        star.scale = Vector3(0.5, 0.5, 0.5)
        star.position = Vector3(randi()%1000 - 500, randi()%1000 - 500, randi()%1000 - 500)
        add_child(star) extends Node3D

var launch_speed = 0.0
var launched = false
var target_scene = "res://Scenes/Space.tscn"

func _process(delta):
    if launched:
        translate(Vector3.UP * launch_speed * delta)
        launch_speed += delta * 5
        if global_position.y > 1000: # ارتفاع الدخول للفضاء
            get_tree().change_scene_to_file(target_scene)

func launch():
    launched = true
    if has_node("CountdownSound"):
        $CountdownSound.play() extends Node3D

var aliens = {
    "lunar": ["res://Aliens/RobotAlien.tscn"],
    "desert": ["res://Aliens/BugAlien.tscn"],
    "ice": ["res://Aliens/IceAlien.tscn"]
}

func spawn(biome):
    if biome in aliens:
        var alien_scene = load(aliens[biome][0])
        var alien = alien_scene.instantiate()
        alien.position = Vector3(randf()*20, 0, randf()*20)
        add_child(alien) Scenes/
 ├── Main.tscn         ← العالم العادي
 ├── Rocket.tscn       ← الصاروخ
 ├── Space.tscn        ← الفضاء والكواكب
 ├── Planets/
 │    ├── Moon.tscn
 │    ├── Mars.tscn
 │    └── IceWorld.tscn
 ├── Aliens/
 │    ├── RobotAlien.tscn
 │    ├── BugAlien.tscn
 │    └── IceAlien.tscn
Scripts/
 ├── Rocket.gd
 ├── Space.gd
 ├── AlienSpawner.gd
 ├── PlanetBiome.gd func _ready():
    var rocket_scene = load("res://Scenes/Rocket.tscn")
    var rocket = rocket_scene.instantiate()
    rocket.position = Vector3(50, 0, 50) # مكان الصاروخ في الأرض
    add_child(rocket) extends Node3D

var launch_speed = 0.0
var launched = false
var target_scene = "res://Scenes/Space.tscn"

func _process(delta):
    if launched:
        translate(Vector3.UP * launch_speed * delta)
        launch_speed += delta * 5
        if global_position.y > 1000:
            get_tree().change_scene_to_file(target_scene)

func launch():
    if not launched:
        launched = true
        if has_node("LaunchSound"):
            $LaunchSound.play() extends Node3D

var aliens = {
    "lunar": ["res://Scenes/Aliens/RobotAlien.tscn"],
    "desert": ["res://Scenes/Aliens/BugAlien.tscn"],
    "ice": ["res://Scenes/Aliens/IceAlien.tscn"]
}

func spawn(biome):
    if biome in aliens:
        var alien_scene = load(aliens[biome][0])
        var alien = alien_scene.instantiate()
        alien.position = Vector3(randf()*50, 0, randf()*50)
        add_child(alien) extends Node3D
@export var biome_type = "lunar"

func _ready():
    var spawner = load("res://Scripts/AlienSpawner.gd").new()
    add_child(spawner)
    for i in range(5):
        spawner.spawn(biome_type) # res://autoloads/Globals.gd
extends Node

# أحجام عامة
const CHUNK_SIZE: int = 16
const CHUNK_HEIGHT: int = 128
const VIEW_DISTANCE_CHUNKS: int = 3  # نصف قطر التحميل حول اللاعب

# أنواع البلوكات
enum BlockId {
	AIR = 0,
	DIRT = 1,
	GRASS = 2,
	STONE = 3,
	SAND = 4,
	WOOD = 5
}

# حدود العالم الرأسية
const MIN_Y: int = 0
const MAX_Y: int = CHUNK_HEIGHT - 1

# أدوات تحويل إحداثيات بين عالم-عام وداخل-تشانك
static func world_to_chunk_coords(wx: int, wy: int, wz: int) -> Vector3i:
	var cx = floori(wx / CHUNK_SIZE)
	var cz = floori(wz / CHUNK_SIZE)
	return Vector3i(cx, 0, cz)

static func world_to_local_in_chunk(wx: int, wy: int, wz: int) -> Vector3i:
	var lx = posmod(wx, CHUNK_SIZE)
	var lz = posmod(wz, CHUNK_SIZE)
	return Vector3i(lx, wy, lz)

static func chunk_to_world_origin(cx: int, cz: int) -> Vector3i:
	return Vector3i(cx * CHUNK_SIZE, 0, cz * CHUNK_SIZE) # مثال داخل سكربت زر UI أو Trigger
func _on_launch_button_pressed():
    var rocket = get_node("/root/Main/Rocket")
    rocket.launch() extends Node3D

var aliens := {
    "lunar": ["res://Scenes/Aliens/RobotAlien.tscn"],
    "desert": ["res://Scenes/Aliens/BugAlien.tscn"],
    "ice":   ["res://Scenes/Aliens/IceAlien.tscn"],
    "savanna": ["res://Scenes/Aliens/BugAlien.tscn"],
    "forest": ["res://Scenes/Aliens/RobotAlien.tscn"]
}

@export var count_min := 3
@export var count_max := 6

func spawn(biome:String) -> void:
    if not aliens.has(biome): return
    var scenes: Array = aliens[biome]
    var count := randi_range(count_min, count_max)
    for i in count:
        var path: String = scenes[randi() % scenes.size()]
        if ResourceLoader.exists(path):
            var ps := load(path) as PackedScene
            var inst := ps.instantiate()
            if inst is Node3D:
                inst.position = Vector3(randf()*40.0 - 20.0, 2.0, randf()*40.0 - 20.0)
                add_child(inst) extends CharacterBody3D

@export var move_speed: float = 2.0
@export var roam_radius: float = 20.0
var origin: Vector3
var target: Vector3

func _ready():
    origin = global_position
    _pick_new_target()

func _physics_process(_delta):
    if global_position.distance_to(target) < 1.0:
        _pick_new_target()
    var dir = (target - global_position).normalized()
    velocity = dir * move_speed
    move_and_slide()

func _pick_new_target():
    target = origin + Vector3(randf()*roam_radius - roam_radius/2.0, 0, randf()*roam_radius - roam_radius/2.0) extends Node3D

@export var biome_type: String = "lunar"
@export var planet_name: String = "Unnamed"
@onready var terrain_root: Node3D = $TerrainRoot

var block_scene := preload("res://Block.tscn") # استخدم البلوك الحالي لتكوين تضاريس مكعبة بسيطة

func _ready():
    _apply_gravity()
    _generate_surface()
    _spawn_aliens()

func _apply_gravity():
    match biome_type:
        "lunar": PhysicsServer3D.area_set_param(get_world_3d().space, PhysicsServer3D.AREA_PARAM_GRAVITY, 3.0)
        "desert": PhysicsServer3D.area_set_param(get_world_3d().space, PhysicsServer3D.AREA_PARAM_GRAVITY, 9.8)
        "ice": PhysicsServer3D.area_set_param(get_world_3d().space, PhysicsServer3D.AREA_PARAM_GRAVITY, 11.0)
        _:
            PhysicsServer3D.area_set_param(get_world_3d().space, PhysicsServer3D.AREA_PARAM_GRAVITY, 9.8)

func _generate_surface():
    # مولد أرضية مكعبة بسيطة حسب البيوم
    var size = 64
    var height = 4
    for x in range(-size/2, size/2):
        for z in range(-size/2, size/2):
            var h := height
            # اختلاف بسيط حسب البيوم
            match biome_type:
                "lunar": h += int((abs(x)+abs(z)) % 2)
                "desert": h += int((x*z) & 1)
                "ice": h += 0
            for y in range(h):
                var b = block_scene.instantiate()
                b.position = Vector3(x, y, z)
                if b.has_method("set_texture"):
                    # لو عندك دالة لتغيير خامة البلوك
                    pass
                terrain_root.add_child(b)

func _spawn_aliens():
    var spawner := preload("res://Scripts/alien_spawner.gd").new()
    add_child(spawner)
    spawner.spawn(biome_type) extends Node3D

@onready var planets_root: Node3D = $PlanetsRoot
@onready var stars: MultiMeshInstance3D = $Stars

class PlanetSpec:
    var name: String
    var biome: String
    var radius: float
    var pos: Vector3
    func _init(n:String, b:String, r:float, p:Vector3): name=n; biome=b; radius=r; pos=p

var planet_biomes := ["lunar", "desert", "ice", "savanna", "forest"]
var rng := RandomNumberGenerator.new()

func _ready():
    rng.randomize()
    _spawn_stars(800)
    _spawn_random_planets(5)

func _spawn_random_planets(count:int) -> void:
    for i in count:
        var name = "Planet_%d" % i
        var biome = planet_biomes[rng.randi_range(0, planet_biomes.size()-1)]
        var radius = rng.randf_range(12.0, 40.0)
        var pos = Vector3(rng.randf_range(-600, 600), rng.randf_range(-300, 300), rng.randf_range(-800, 800))
        _create_planet(PlanetSpec.new(name, biome, radius, pos))

func _create_planet(spec:PlanetSpec) -> void:
    # جسم مرئي للكوكب
    var mesh := MeshInstance3D.new()
    var sphere := SphereMesh.new()
    sphere.radial_segments = 32
    sphere.rings = 16
    sphere.radius = spec.radius
    mesh.mesh = sphere
    mesh.position = spec.pos

    # مادة بسيطة بلون حسب البيوم
    var mat := StandardMaterial3D.new()
    match spec.biome:
        "lunar": mat.albedo_color = Color(0.6,0.6,0.65)
        "desert": mat.albedo_color = Color(0.8,0.55,0.35)
        "ice": mat.albedo_color = Color(0.85,0.9,1.0)
        "savanna": mat.albedo_color = Color(0.6,0.75,0.35)
        "forest": mat.albedo_color = Color(0.3,0.6,0.35)
        _:
            mat.albedo_color = Color(0.8,0.8,0.8)
    mesh.material_override = mat

    # منطقة تفاعل للنقر/الهبوط
    var area := Area3D.new()
    var shape := CollisionShape3D.new()
    var sph := SphereShape3D.new(); sph.radius = spec.radius
    shape.shape = sph
    area.add_child(shape)
    area.position = spec.pos
    area.set_meta("biome", spec.biome)
    area.set_meta("name", spec.name)
    area.connect("input_event", Callable(self, "_on_planet_clicked"))

    planets_root.add_child(mesh)
    planets_root.add_child(area)

func _on_planet_clicked(_camera, event: InputEvent, _pos: Vector3, _normal: Vector3, _shape_idx: int) -> void:
    if event is InputEventMouseButton and event.pressed and event.button_index == MOUSE_BUTTON_LEFT:
        var area := event.target as Area3D
        if area == null: return
        var biome := String(area.get_meta("biome"))
        var name := String(area.get_meta("name"))
        _enter_planet(name, biome)

func _enter_planet(pname:String, biome:String) -> void:
    # حمل قالب الكوكب ومرر نوع البيوم
    var scene := load("res://Scenes/Planets/PlanetTemplate.tscn") as PackedScene
    var inst := scene.instantiate()
    get_tree().current_scene.add_child(inst)
    inst.set("planet_name", pname)
    inst.set("biome_type", biome)
    # بدّل المشهد: أو بدل الكاميرا فقط
    get_tree().change_scene_to_packed(scene)

func _spawn_stars(count:int) -> void:
    stars.multimesh = MultiMesh.new()
    stars.multimesh.transform_format = MultiMesh.TRANSFORM_3D
    stars.multimesh.color_format = MultiMesh.COLOR_8BIT
    stars.multimesh.instance_count = count
    for i in count:
        var t := Transform3D()
        var pos := Vector3(rng.randf_range(-2000,2000), rng.randf_range(-2000,2000), rng.randf_range(-2000,2000))
        t.origin = pos
        stars.multimesh.set_instance_transform(i, t) Scenes/
  Main.tscn            ← مشهدك الأساسي (الأرض)
  Rocket.tscn          ← الصاروخ
  Space.tscn           ← مشهد الفضاء
  Planets/
    PlanetTemplate.tscn
  Aliens/
    RobotAlien.tscn
    BugAlien.tscn
    IceAlien.tscn
Scripts/
  rocket.gd
  space.gd
  planet_biome.gd
  alien_ai.gd
  alien_spawner.gd func change_season():
    match season:
        "spring": season = "summer"; rain_chance = 0.2
        "summer": season = "autumn"; rain_chance = 0.05
        "autumn": season = "winter"; rain_chance = 0.25
        "winter": season = "spring"; snow_chance = 0.3 extends CharacterBody3D
@export var move_speed: float = 1.2
var target: Vector3

func _ready():
    _pick_new_target()

func _physics_process(delta):
    if global_position.distance_to(target) < 0.5:
        _pick_new_target()
    var dir = (target - global_position).normalized()
    velocity = dir * move_speed
    move_and_slide()

func _pick_new_target():
    target = global_position + Vector3(randf()*10.0-5.0, 0, randf()*10.0-5.0) # أعلى الملف
const SEA_LEVEL := 52
var temp_noise := FastNoiseLite.new()
var humid_noise := FastNoiseLite.new()
var spawn_timer := 0.0
var spawn_interval := 5.0 # كل 5 ثواني نحاول توليد حيوانات قريبة

func _ready():
    Globals.set_block_world = set_block_world
    Globals.get_block_world = get_block_world

    noise.noise_type = FastNoiseLite.TYPE_PERLIN
    noise.frequency = 0.015

    temp_noise.noise_type = FastNoiseLite.TYPE_SIMPLEX
    temp_noise.frequency = 0.008
    humid_noise.noise_type = FastNoiseLite.TYPE_SIMPLEX
    humid_noise.frequency = 0.008

    for cz in range(-Globals.VIEW_DISTANCE_CHUNKS, Globals.VIEW_DISTANCE_CHUNKS+1):
        for cx in range(-Globals.VIEW_DISTANCE_CHUNKS, Globals.VIEW_DISTANCE_CHUNKS+1):
            _load_chunk(cx, cz)

    if player:
        player.position = Vector3(0, terrain_height(0,0) + 5, 0)

func _process(delta):
    if not player: return
    var p = player.global_transform.origin
    var pcx = floori(p.x / Globals.CHUNK_SIZE)
    var pcz = floori(p.z / Globals.CHUNK_SIZE)
    for cz in range(pcz-Globals.VIEW_DISTANCE_CHUNKS, pcz+Globals.VIEW_DISTANCE_CHUNKS+1):
        for cx in range(pcx-Globals.VIEW_DISTANCE_CHUNKS, pcx+Globals.VIEW_DISTANCE_CHUNKS+1):
            if Vector2i(cx,cz) not in chunks:
                _load_chunk(cx, cz)

    spawn_timer += delta
    if spawn_timer >= spawn_interval:
        spawn_timer = 0
        _try_spawn_animals_near(player.global_position)

func terrain_height(wx:int, wz:int) -> int:
    var n = noise.get_noise_2d(float(wx), float(wz)) # -1..1
    var base = 50
    var amp = 20
    # تأثير البيوم على الارتفاع
    var t = remap(temp_noise.get_noise_2d(wx, wz), -1, 1, 0, 1)
    var h = remap(humid_noise.get_noise_2d(wx, wz), -1, 1, 0, 1)
    var biome = BiomeData.pick_biome(t, h)
    return clampi(int(base + n*amp + biome.height_bias), Globals.MIN_Y+1, Globals.MAX_Y-1)

func get_biome(wx:int, wz:int) -> BiomeData.Biome:
    var t = remap(temp_noise.get_noise_2d(wx, wz), -1, 1, 0, 1)
    var h = remap(humid_noise.get_noise_2d(wx, wz), -1, 1, 0, 1)
    return BiomeData.pick_biome(t, h)

func _generate_chunk(ch:Chunk) -> void:
    var SIZE = Globals.CHUNK_SIZE
    var HEIGHT = Globals.CHUNK_HEIGHT
    for z in SIZE:
        for x in SIZE:
            var wx = x + ch.chunk_x * SIZE
            var wz = z + ch.chunk_z * SIZE
            var biome = get_biome(wx, wz)
            var h = terrain_height(wx, wz)
            for y in HEIGHT:
                var id = Globals.BlockId.AIR
                if y <= h:
                    if y == h:
                        id = biome.surface
                    elif y > h - 3:
                        id = biome.subsoil
                    else:
                        id = biome.stone
                # مياه البحر
                if y <= SEA_LEVEL and y > h:
                    id = Globals.BlockId.WATER
                ch.set_local(x,y,z,id)
            # زراعة أشجار/صبار/ثلج خفيف حسب البيوم
            if randi() % 32 == 0 and h > SEA_LEVEL: # لا نزرع تحت الماء
                _maybe_place_feature(wx, h+1, wz, biome)

func _maybe_place_feature(wx:int, wy:int, wz:int, biome:BiomeData.Biome) -> void:
    match biome.name:
        "forest":
            _place_tree(wx, wy, wz, 4, 2)
        "taiga":
            _place_tree(wx, wy, wz, 6, 2, pine=true)
        "desert":
            _place_cactus(wx, wy, wz, 3)
        "tundra":
            Globals.set_block_world(Vector3(wx,wy,wz), Globals.BlockId.SNOW)
        "swamp":
            if randi()%2==0: Globals.set_block_world(Vector3(wx,wy,wz), Globals.BlockId.LEAVES)

func _place_tree(wx:int, wy:int, wz:int, height:int, crown:int, pine:bool=false) -> void:
    for i in range(height):
        Globals.set_block_world(Vector3(wx, wy+i, wz), Globals.BlockId.WOOD)
    for x in range(-crown, crown+1):
        for y in range(-crown, crown+1):
            for z in range(-crown, crown+1):
                if pine and abs(x)+abs(z)+abs(y) > crown+1: continue
                if x==0 and y<0 and z==0: continue
                Globals.set_block_world(Vector3(wx+x, wy+height-1+y, wz+z), Globals.BlockId.LEAVES)

func _place_cactus(wx:int, wy:int, wz:int, height:int) -> void:
    for i in range(height):
        Globals.set_block_world(Vector3(wx, wy+i, wz), Globals.BlockId.WOOD) # مؤقتًا كخشب كمجسم عمودي

func _try_spawn_animals_near(center:Vector3):
    var wx = int(round(center.x))
    var wz = int(round(center.z))
    var biome = get_biome(wx, wz)
    if biome.animals.is_empty():
        return
    var count = 1 + randi()%3
    for i in count:
        var name = biome.animals[randi()%biome.animals.size()]
        _spawn_animal(name, Vector3(wx + randi()%16-8, terrain_height(wx,wz)+2, wz + randi()%16-8))

func _spawn_animal(aname:String, pos:Vector3):
    var path = "res://scenes/animals/%s.tscn" % aname
    if not ResourceLoader.exists(path):
        return
    var scene = load(path) as PackedScene
    var inst = scene.instantiate()
    inst.position = pos
    add_child(inst) # res://scripts/chunk.gd
extends Node3D
class_name Chunk

@export var chunk_x: int
@export var chunk_z: int

var blocks: PackedInt32Array # 3D مضغوط: x + z*SIZE + y*SIZE*SIZE
var dirty := true

static var SIZE := Globals.CHUNK_SIZE
static var HEIGHT := Globals.CHUNK_HEIGHT

func _ready():
	blocks = PackedInt32Array()
	blocks.resize(SIZE * SIZE * HEIGHT)

func index(lx:int, ly:int, lz:int) -> int:
	return lx + lz * SIZE + ly * SIZE * SIZE

func get_local(lx:int, ly:int, lz:int) -> int:
	if ly < 0 or ly >= HEIGHT: return Globals.BlockId.AIR
	return blocks[index(lx,ly,lz)]

func set_local(lx:int, ly:int, lz:int, id:int) -> void:
	if ly < 0 or ly >= HEIGHT: return
	blocks[index(lx,ly,lz)] = id
	dirty = true

func rebuild_mesh():
	if not dirty: return
	dirty = false
	# نبني Mesh بسطح واحد Vertex Colors
	var st := SurfaceTool.new()
	st.begin(Mesh.PRIMITIVE_TRIANGLES)

	for ly in HEIGHT:
		for lz in SIZE:
			for lx in SIZE:
				var id = get_local(lx,ly,lz)
				if id == Globals.BlockId.AIR:
					continue
				_add_block_faces(st, lx,ly,lz, id)

	var mesh := st.commit()
	if mesh:
		var mi = MeshInstance3D.new()
		mi.mesh = mesh
		mi.name = "Mesh"
		# احذف القديم
		for c in get_children():
			if c is MeshInstance3D:
				remove_child(c)
				c.queue_free()
		add_child(mi)

func _add_block_faces(st:SurfaceTool, lx:int, ly:int, lz:int, id:int) -> void:
	var c = BlockData.color_of(id)
	var wx = lx + chunk_x*SIZE
	var wy = ly
	var wz = lz + chunk_z*SIZE

	# لكل وجه: لو الجار غير مصمت، ارسم الوجه
	# Left (-X)
	if _is_air(lx-1,ly,lz):
		_face(st, Vector3(wx,wy,wz), Vector3(-1,0,0), c)
	# Right (+X)
	if _is_air(lx+1,ly,lz):
		_face(st, Vector3(wx+1,wy,wz), Vector3(1,0,0), c)
	# Bottom (-Y)
	if _is_air(lx,ly-1,lz):
		_face(st, Vector3(wx,wy,wz), Vector3(0,-1,0), c)
	# Top (+Y)
	if _is_air(lx,ly+1,lz):
		_face(st, Vector3(wx,wy+1,wz), Vector3(0,1,0), c)
	# Back (-Z)
	if _is_air(lx,ly,lz-1):
		_face(st, Vector3(wx,wy,wz), Vector3(0,0,-1), c)
	# Front (+Z)
	if _is_air(lx,ly,lz+1):
		_face(st, Vector3(wx,wy,wz+1), Vector3(0,0,1), c)

func _is_air(lx:int, ly:int, lz:int) -> bool:
	if ly < 0 or ly >= HEIGHT: return true
	if lx < 0 or lx >= SIZE: return true
	if lz < 0 or lz >= SIZE: return true
	return get_local(lx,ly,lz) == Globals.BlockId.AIR

# يرسم وجهاً لمحور dir، عند origin كتلة 1x1x1
func _face(st:SurfaceTool, o:Vector3, dir:Vector3, col:Color) -> void:
	var a:Vector3; var b:Vector3; var c:Vector3; var d:Vector3
	if dir.x != 0:
		# X face
		var x = o.x
		var y = o.y
		var z = o.z
		if dir.x > 0:
			a = Vector3(x, y, z)
			b = Vector3(x, y+1, z)
			c = Vector3(x, y+1, z+1)
			d = Vector3(x, y, z+1)
		else:
			a = Vector3(x, y, z+1)
			b = Vector3(x, y+1, z+1)
			c = Vector3(x, y+1, z)
			d = Vector3(x, y, z)
	elif dir.y != 0:
		# Y face
		var x = o.x
		var y = o.y
		var z = o.z
		if dir.y > 0:
			a = Vector3(x, y, z)
			b = Vector3(x+1, y, z)
			c = Vector3(x+1, y, z+1)
			d = Vector3(x, y, z+1)
		else:
			a = Vector3(x, y, z+1)
			b = Vector3(x+1, y, z+1)
			c = Vector3(x+1, y, z)
			d = Vector3(x, y, z)
	else:
		# Z face
		var x = o.x
		var y = o.y
		var z = o.z
		if dir.z > 0:
			a = Vector3(x, y, z)
			b = Vector3(x, y+1, z)
			c = Vector3(x+1, y+1, z)
			d = Vector3(x+1, y, z)
		else:
			a = Vector3(x+1, y, z)
			b = Vector3(x+1, y+1, z)
			c = Vector3(x, y+1, z)
			d = Vector3(x, y, z)
	
	var n = (b - a).cross(d - a).normalized()
	# مثلثان
	st.add_color(col); st.add_normal(n); st.add_vertex(a)
	st.add_color(col); st.add_normal(n); st.add_vertex(b)
	st.add_color(col); st.add_normal(n); st.add_vertex(c)
	st.add_color(col); st.add_normal(n); st.add_vertex(a)
	st.add_color(col); st.add_normal(n); st.add_vertex(c)
	st.add_color(col); st.add_normal(n); st.add_vertex(d) # res://Scripts/damageable.gd
extends Node3D
class_name Damageable

@export var max_health: float = 100.0
var health: float
var equipped_armor: Node = null # يمكن أن يكون Armor instance

signal died
signal damaged(amount, by)

func _ready():
    health = max_health

func apply_damage(amount: float, attacker: Node = null) -> void:
    var final = amount
    if equipped_armor and equipped_armor.has_method("absorb_damage"):
        final = equipped_armor.absorb_damage(final)
    health -= final
    emit_signal("damaged", final, attacker)
    if health <= 0:
        die(attacker)

func heal(amount: float) -> void:
    health = clamp(health + amount, 0, max_health)

func die(killer: Node = null) -> void:
    emit_signal("died", killer)
    queue_free() # أو أي سلوك موت مخصص # res://Scripts/armor.gd
extends Node
class_name Armor

# نوع الدرع: helmet / chest / legs / boots / full (optional)
@export var slot: String = "chest"
@export var damage_reduction_percent: float = 0.25 # تقلل 25% من الضرر الوارد
@export var durability: float = 200.0
@export var durability_loss_per_hit: float = 5.0

func absorb_damage(incoming: float) -> float:
    # تقلل الضرر بنسبة محددة، ثم تفقد متانة
    var reduced = incoming * (1.0 - damage_reduction_percent)
    durability -= durability_loss_per_hit
    if durability <= 0:
        queue_free() # الدرع انكسر
        return incoming # إذا انكسر، يرجع الضرر الأصلي (أو 0 حسب التصميم)
    return reduced # res://Scripts/armor.gd
extends Node
class_name Armor

# نوع الدرع: helmet / chest / legs / boots / full (optional)
@export var slot: String = "chest"
@export var damage_reduction_percent: float = 0.25 # تقلل 25% من الضرر الوارد
@export var durability: float = 200.0
@export var durability_loss_per_hit: float = 5.0

func absorb_damage(incoming: float) -> float:
    # تقلل الضرر بنسبة محددة، ثم تفقد متانة
    var reduced = incoming * (1.0 - damage_reduction_percent)
    durability -= durability_loss_per_hit
    if durability <= 0:
        queue_free() # الدرع انكسر
        return incoming # إذا انكسر، يرجع الضرر الأصلي (أو 0 حسب التصميم)
    return reduced # res://Scripts/armor.gd
extends Node
class_name Armor

# نوع الدرع: helmet / chest / legs / boots / full (optional)
@export var slot: String = "chest"
@export var damage_reduction_percent: float = 0.25 # تقلل 25% من الضرر الوارد
@export var durability: float = 200.0
@export var durability_loss_per_hit: float = 5.0

func absorb_damage(incoming: float) -> float:
    # تقلل الضرر بنسبة محددة، ثم تفقد متانة
    var reduced = incoming * (1.0 - damage_reduction_percent)
    durability -= durability_loss_per_hit
    if durability <= 0:
        queue_free() # الدرع انكسر
        return incoming # إذا انكسر، يرجع الضرر الأصلي (أو 0 حسب التصميم)
    return reduced # res://Scripts/weapon.gd
extends Node3D
class_name Weapon

@export var name: String = "Weapon"
@export var damage: float = 10.0
@export var cooldown: float = 0.5
var can_fire: bool = true

func _ready():
    can_fire = true

func try_fire(owner: Node) -> void:
    # يرثها melee/ranged ويطبق السلوك
    if not can_fire:
        return
    can_fire = false
    fire(owner)
    yield(get_tree().create_timer(cooldown), "timeout")
    can_fire = true

func fire(owner: Node) -> void:
    # لازم يُعاد تعريفها في الأبناء
    pass # res://Scripts/melee_weapon.gd
extends Weapon
class_name MeleeWeapon

@export var range: float = 2.0
@export var arc_degrees: float = 90.0
@export var force: float = 0.0 # دفع (اختياري)

# يمكن ربط Mesh/Animation هنا
func fire(owner: Node) -> void:
    var cam = owner.get_node_or_null("Camera3D")
    var origin = owner.global_transform.origin
    var forward = -owner.global_transform.basis.z.normalized()
    # نستخدم intersect_shape لكشف الأجسام في مروحة
    var space = get_world_3d().direct_space_state
    var results = space.intersect_shape(BoxShape3D.new(), Transform3D(Basis(), origin + forward*range*0.5), 32, [], 0.1)
    # أبسط طريقة: نستخدم intersect_ray على عدة أشعة داخل القوس
    var steps = 7
    for i in range(steps):
        var angle = lerp(-arc_degrees*0.5, arc_degrees*0.5, float(i)/float(steps-1))
        var dir = forward.rotated(Vector3.UP, deg_to_rad(angle))
        var res = space.intersect_ray(origin, origin + dir * range, [owner], collision_mask=0xFFFFFFFF)
        if res:
            var target = res.collider
            if target and target.has_method("apply_damage"):
                target.apply_damage(damage, owner)
            # apply force if wanted (إذا الجسم Rigid)
            if target and target is RigidBody3D and force > 0:
                target.apply_impulse(res.position - target.global_transform.origin, dir * force) # res://Scripts/ranged_weapon.gd
extends Weapon
class_name RangedWeapon

@export var range: float = 200.0
@export var spread_degrees: float = 1.5
@export var ammo: int = 12
@export var max_ammo: int = 12
@export var reload_time: float = 1.5
@export var is_automatic: bool = false

var reloading: bool = false

func fire(owner: Node) -> void:
    if ammo <= 0:
        _start_reload()
        return
    ammo -= 1
    var origin = owner.global_transform.origin
    # تحدد نقطة الإطلاق من الكاميرا لو موجودة
    var cam = owner.get_node_or_null("Camera3D")
    var from = origin
    var dir: Vector3
    if cam:
        from = cam.global_transform.origin
        dir = -cam.global_transform.basis.z
    else:
        dir = -owner.global_transform.basis.z
    # أضف انحراف
    var rng = RandomNumberGenerator.new(); rng.randomize()
    var yaw = deg_to_rad(rng.randf_range(-spread_degrees, spread_degrees))
    var pitch = deg_to_rad(rng.randf_range(-spread_degrees, spread_degrees))
    dir = dir.rotated(Vector3.UP, yaw).rotated(Vector3.RIGHT, pitch).normalized()
    # Raycast فوري (البولت)
    var space = get_world_3d().direct_space_state
    var res = space.intersect_ray(from, from + dir * range, [owner], collision_mask=0xFFFFFFFF)
    if res:
        var target = res.collider
        if target and target.has_method("apply_damage"):
            target.apply_damage(damage, owner)
        # تأثير صغير في المكان
        _on_bullet_hit(res.position, res.normal, target)
    else:
        _on_bullet_miss(from + dir * range)
    # صوت و ارتداد بإمكانك ربطه هنا

func _start_reload() -> void:
    if reloading: return
    reloading = true
    Task.new().start(self, "_reload_task") # أو استخدم timer
    # بديل بدون Task:
    # yield(get_tree().create_timer(reload_time), "timeout")
    # ammo = max_ammo
    # reloading = false

func _reload_task() -> void:
    yield(get_tree().create_timer(reload_time), "timeout")
    ammo = max_ammo
    reloading = false

func _on_bullet_hit(pos:Vector3, normal:Vector3, target:Node) -> void:
    # Particle / Decal / Sound
    pass

func _on_bullet_miss(pos:Vector3) -> void:
    pass # res://Scripts/projectile.gd
extends Node3D
class_name Projectile

@export var speed: float = 60.0
@export var damage: float = 50.0
@export var life_time: float = 8.0
var dir: Vector3 = Vector3.FORWARD
var owner: Node = null
var travel: float = 0.0

func _physics_process(delta: float) -> void:
    var from = global_transform.origin
    var to = from + dir.normalized() * speed * delta
    var space = get_world_3d().direct_space_state
    var res = space.intersect_ray(from, to, [owner], collision_mask=0xFFFFFFFF)
    if res:
        var target = res.collider
        if target and target.has_method("apply_damage"):
            target.apply_damage(damage, owner)
        _explode(res.position)
        queue_free()
        return
    global_translate(dir.normalized() * speed * delta)
    travel += delta
    if travel > life_time:
        queue_free()

func _explode(pos:Vector3) -> void:
    # Particle / Area Damage
    pass # res://Scripts/inventory.gd
extends Node
class_name Inventory

var items := [] # array of nodes أو resource descriptors
var equipped_weapon: Weapon = null
var equipped_armor: Armor = null

func add_item(item: Node) -> void:
    items.append(item)

func equip_weapon(w: Weapon) -> void:
    equipped_weapon = w

func equip_armor(a: Armor) -> void:
    equipped_armor = a # داخل Player.gd أعلى الملف
var inventory: Inventory = Inventory.new()
var current_weapon: Weapon = null
var equip_cooldown = 0.2

func _ready():
    # ثبت الـInventory كـ child أو autoload لو حابب
    add_child(inventory)
    # مثال: إضافة سيف ومسدس ودرع للتجربة
    var sword = preload("res://Scripts/melee_weapon.gd").new()
    sword.name = "Iron Sword"
    sword.damage = 20
    sword.cooldown = 0.6
    inventory.add_item(sword)

    var pistol = preload("res://Scripts/ranged_weapon.gd").new()
    pistol.name = "9mm Pistol"
    pistol.damage = 12
    pistol.cooldown = 0.2
    pistol.ammo = 12
    inventory.add_item(pistol)

    var chest = preload("res://Scripts/armor.gd").new()
    chest.slot = "chest"
    chest.damage_reduction_percent = 0.30
    chest.durability = 250
    inventory.add_item(chest)

    # جهز السيف افتراضياً
    equip_weapon(sword)
    equip_armor(chest)

func equip_weapon(w: Weapon) -> void:
    if w == null: return
    current_weapon = w
    # لو في تمثيل مرئي (Mesh) ضيفه لليد أو مكان مناسب
    if not is_a_parent_of(w):
        add_child(w)

func equip_armor(a: Armor) -> void:
    if a == null: return
    inventory.equip_armor(a)
    # ضعها في اللاعب حتى يتم استخدامها من damageable
    var dmg = get_node_or_null(".") as Damageable
    if dmg:
        dmg.equipped_armor = a # داخل _input أو _process
if Input.is_action_just_pressed("attack"):
    if current_weapon:
        current_weapon.try_fire(self) # داخل _input أو _process
if Input.is_action_just_pressed("attack"):
    if current_weapon:
        current_weapon.try_fire(self) 
