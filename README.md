# Road-Traffic-Simulator

extends Navigation2D

const SPEED = 200.0

var begin = Vector2()
var end = Vector2()
var path = []

func _process(delta):
	if (path.size() > 1):
		var to_walk = delta*SPEED
		while(to_walk > 0 and path.size() >= 2):
			var pfrom = path[path.size() - 1]
			var pto = path[path.size() - 2]
			var d = pfrom.distance_to(pto)
			if (d <= to_walk):
				path.remove(path.size() - 1)
				to_walk -= d
			else:
				path[path.size() - 1] = pfrom.linear_interpolate(pto, to_walk/d)
				to_walk = 0
		
		var atpos = path[path.size() - 1]
		get_node("car").set_pos(atpos)
		get_node("car").look_at(get_node("destination").get_pos())
		if (path.size() < 2):
			path = []
			set_process(false)
	else:
		set_process(false)


func _update_path():
	var p = get_simple_path(begin, end, true)
	path = Array(p)
	path.invert()
	
	set_process(true)

func _setpos():
	get_node("car").set_pos(get_node("spawn").get_pos())
	begin = get_node("car").get_pos()

	end = get_node("destination").get_pos() - get_pos()
	_update_path()

func _ready():
	get_node("Button").connect("pressed", self, "_setpos")
