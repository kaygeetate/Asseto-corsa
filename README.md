extends RigidBody3D

var steering = 0
var acceleration = 0

const MAX_STEER = 30
const MAX_SPEED = 100
const ENGINE_FORCE = 30
const BRAKE_FORCE = 40

func _physics_process(delta):
    var forward = -transform.basis.z
    var velocity = linear_velocity

    if acceleration != 0:
        apply_central_force(forward * acceleration * ENGINE_FORCE)
    else:
        apply_central_force(-velocity * BRAKE_FORCE * delta)

    rotate_y(deg2rad(steering * delta * 0.5))

func set_input(steer: float, accel: float):
    steering = clamp(steer, -MAX_STEER, MAX_STEER)
    acceleration = clamp(accel, -1, 1)
extends Node

@onready var car = $"../Car"

func _process(delta):
    var steer = 0
    var accel = 0

    if Input.is_action_pressed("ui_left"):
        steer -= 1
    if Input.is_action_pressed("ui_right"):
        steer += 1
    if Input.is_action_pressed("ui_up"):
        accel = 1
    if Input.is_action_pressed("ui_down"):
        accel = -1

    car.set_input(steer * 30, accel)
extends Camera3D

@onready var car = $"../Car"

func _process(delta):
    global_transform.origin = car.global_transform.origin + Vector3(0, 5, 10)
    look_at(car.global_transform.origin, Vector3.UP)
