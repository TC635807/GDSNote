# GDScript语言教程

GDScript是Godot游戏引擎的专用脚本语言，专为游戏开发而设计，语法类似Python但针对Godot进行了优化。本教程将带你从基础到进阶掌握GDScript。

## 目录
1. [基础语法](#基础语法)
2. [变量与数据类型](#变量与数据类型)
3. [运算符](#运算符)
4. [控制流](#控制流)
5. [函数](#函数)
6. [面向对象编程](#面向对象编程)
7. [Godot特有特性](#godot特有特性)
8. [信号系统](#信号系统)
9. [协程](#协程)
10. [最佳实践](#最佳实践)

## 基础语法

GDScript文件以`.gd`为扩展名，每个文件通常对应Godot中的一个节点或场景。

```gdscript
# 这是单行注释

"""
这是多行注释
可以跨越多行
"""

# 脚本结构示例
extends Node  # 指定继承的类

# 成员变量
var player_name = "Hero"

# 内置函数
func _ready():
    print("游戏准备好了!")
```

## 变量与数据类型

### 变量声明

```gdscript
# 基本变量声明
var health = 100       # 整数
var player_name = "Alex" # 字符串
var velocity = 50.5    # 浮点数
var is_alive = true    # 布尔值

# 类型注解（可选）
var score: int = 0
var position: Vector2 = Vector2(10, 20)

# 常量
const MAX_SPEED = 200
const GRAVITY := 9.8  # 使用:=可以自动推断类型
```

### 数据类型

- **基本类型**: `int`, `float`, `bool`, `String`
  变量之间可以直接通过赋值相互转换，但是在出现特定类型变量时，不能直接使用赋值符号
- **字符串**无法进行减法，只能进行加法把两个字符串前后组合在一起
- **基本类型的转化**
```gdscript
#错误做法
var a:int = 10
var b:String = "20"
func _ready():
    b=a#此时会报错
#正确做法
var a:int = 10
var c:float = 2.6
var b:String = "20"
func _ready():
    b = String.num(c)#float转字符串
    b = String.num_64(a)#int转字符串
    #float和int之间可以相互转换(float会丢失小数部分)
```

- **Godot特有类型**:
  - `Vector2`, `Vector3` - 2D/3D向量
    - Vector变量中的两个数据均为float
    - 对Vector操作时要标明.x/.y
  - `Rect2` - 矩形
  - `Color` - RGBA颜色
  - `Array` - 动态数组
  - `Dictionary` - 键值对集合

```gdscript
# 向量示例

var direction = Vector2(1, 0)
var position_3d = Vector3(0, 10, -5)

# 颜色示例
var red = Color(1, 0, 0)
var transparent_blue = Color(0, 0, 1, 0.5)

# 数组和字典
var items = ["sword", "shield", "potion"]
var player_stats = {"health": 100, "attack": 15}
```

## 运算符

### 算术运算符

```gdscript
var a = 10
var b = 3

print(a + b)  # 13
print(a - b)  # 7
print(a * b)  # 30
print(a / b)  # 3.333...
print(a % b)  # 1
print(a ** b) # 1000 (幂运算)
```

### 比较运算符

```gdscript
print(10 == 10)  # true
print(10 != 5)   # true
print(5 > 10)    # false
print(5 <= 5)    # true
```

### 逻辑运算符

```gdscript
var x = true
var y = false

print(x and y)   # false
print(x or y)    # true
print(not x)     # false
```

## 控制流

### 条件语句

```gdscript
# if语句
if health <= 0:
    print("玩家死亡!")
elif health < 20:
    print("警告: 生命值低!")
else:
    print("生命值正常")

# 三元运算符
var message = "胜利!" if score >= 100 else "继续努力"
```

### 循环语句

```gdscript
# while循环
var i = 0
while i < 5:
    print(i)
    i += 1

# for循环
for i in range(5):  # 0到4
    print(i)

# 遍历数组
var weapons = ["sword", "bow", "axe"]
for weapon in weapons:
    print("武器: " + weapon)

# 遍历字典
var stats = {"hp": 100, "mp": 50}
for key in stats:
    print(key + ": " + str(stats[key]))
```

## 函数

### 基本函数

```gdscript
# 定义函数
func greet(name):
    return "Hello, " + name

# 调用函数
var greeting = greet("Alex")
print(greeting)  # 输出: Hello, Alex

# 带默认参数的函数
func attack(damage, multiplier := 1.0):
    return damage * multiplier

print(attack(10))     # 10.0
print(attack(10, 2.5)) # 25.0
```

### Godot内置函数

```gdscript
# _ready() - 节点进入场景树时调用
func _ready():
    print("节点已准备好")

# _process(delta) - 每帧调用
func _process(delta):
    # delta是帧间隔时间(秒)
    position.x += speed * delta

# _physics_process(delta) - 物理帧调用(固定时间步长)
func _physics_process(delta):
    velocity.y += GRAVITY * delta
```

## 面向对象编程

### 类与继承

```gdscript
# 定义一个类
extends Node

class_name Character  # 可选: 注册为全局可访问的类

var health := 100

func take_damage(amount):
    health -= amount
    print("受到伤害! 剩余生命值: ", health)

# 继承
class Player extends Character:
    var experience := 0
    
    func gain_exp(amount):
        experience += amount
        print("获得经验: ", amount)
```

### 访问控制

```gdscript
var public_var = "可公开访问"  # 默认是公开的
var _private_var = "建议私有"  # 下划线前缀表示私有(约定)

# 导出变量(在Godot编辑器中可见)
export var speed = 100
export(int, 0, 100) var health = 50  # 带范围限制
```

## Godot特有特性

### 节点访问

```gdscript
# 获取节点引用
onready var sprite = $Sprite  # $是get_node()的简写
onready var anim_player = get_node("AnimationPlayer")

func _ready():
    # 修改节点属性
    sprite.position = Vector2(100, 200)
    anim_player.play("walk")
```

### 场景与实例化

```gdscript
# 加载场景
var enemy_scene = preload("res://enemy.tscn")

func spawn_enemy():
    # 实例化场景
    var enemy = enemy_scene.instance()
    add_child(enemy)
    enemy.position = Vector2(rand_range(0, 100), 0)
```

## 信号系统

Godot使用信号系统实现节点间通信。

```gdscript
# 定义信号
signal player_died
signal health_changed(new_health)

# 发射信号
func take_damage(amount):
    health -= amount
    emit_signal("health_changed", health)
    if health <= 0:
        emit_signal("player_died")

# 连接信号(通常在_ready中)
func _ready():
    connect("player_died", self, "_on_player_died")
    $Button.connect("pressed", self, "_on_button_pressed")

func _on_player_died():
    print("游戏结束!")

func _on_button_pressed():
    print("按钮被点击!")
```

## 协程

GDScript支持使用`yield`实现协程。

```gdscript
# 简单协程示例
func blink_sprite():
    for i in range(5):
        $Sprite.visible = !$Sprite.visible
        yield(get_tree().create_timer(0.2), "timeout")
    $Sprite.visible = true

# 调用协程
func _ready():
    blink_sprite()
```

# 最佳实践

## GDScript 综合教程：代码与理论深度融合（注释版）

## 1. 基础语法与实战结合

### 1.1 脚本结构与实际应用

```gdscript
# 玩家角色脚本
extends CharacterBody2D  # 继承自Godot的2D物理角色节点基类，提供物理移动功能

# 工具模式：添加此指令后脚本在编辑器中也会执行（用于工具开发）
tool
# 类名定义：将此脚本注册为全局可访问的类
class_name Player  

"""
玩家控制脚本文档字符串（可通过编辑器查看）
功能：
- 处理玩家移动输入
- 管理角色动画状态
- 实现基础物理碰撞
"""

# @export 导出变量：使变量在Godot编辑器面板中可见并可编辑
@export var speed := 300.0          # 玩家移动速度（像素/秒）
@export var jump_force := 500.0     # 玩家跳跃力度
@export var texture: Texture2D      # 角色纹理（可在编辑器拖拽指定）

# _ready(): 当节点加入场景树时自动调用的内置函数
func _ready():
    $Sprite.texture = texture       # $是get_node()的简写，获取子节点Sprite
    print("玩家角色初始化完成")     # 调试输出
```

## 2. 数据类型与游戏开发

### 2.1 向量运算实战

```gdscript
# 子弹运动系统
extends Area2D  # 继承Area2D用于碰撞检测

# 成员变量定义
var direction := Vector2.RIGHT  # 初始方向向量（1,0）
var speed := 600.0              # 子弹速度（像素/秒）
var damage := 25                # 伤害值
var max_distance := 1000.0      # 最大飞行距离
var traveled_distance := 0.0    # 已飞行距离

# _physics_process(): 物理帧回调函数（固定时间步长）
func _physics_process(delta: float):
    # 计算本帧移动向量 = 方向 * 速度 * 时间增量
    var movement = direction * speed * delta
    position += movement               # 更新位置
    traveled_distance += movement.length()  # 累加移动距离
    
    # 超出射程后自动销毁
    if traveled_distance > max_distance:
        queue_free()  # 从场景树中移除并删除对象

# 碰撞检测回调函数
func _on_Bullet_body_entered(body: Node):
    # 检查碰撞体是否有take_damage方法
    if body.has_method("take_damage"):
        body.take_damage(damage)  # 调用伤害方法
    create_impact_effect()        # 创建击中特效
    queue_free()                  # 碰撞后销毁子弹

# 创建击中特效
func create_impact_effect():
    var effect = preload("res://effects/impact.tscn").instantiate()  # 加载并实例化特效场景
    get_parent().add_child(effect)  # 添加到父节点
    effect.global_position = global_position  # 设置特效位置为子弹当前位置
```

## 3. 控制流与游戏逻辑

### 3.1 状态模式实现角色AI

```gdscript
# 敌人AI状态机
extends KinematicBody2D  # 继承2D物理体节点

# 枚举定义状态类型
enum State {
    IDLE,     # 闲置状态
    PATROL,   # 巡逻状态
    CHASE,    # 追逐玩家状态
    ATTACK    # 攻击状态
}

# 成员变量
var current_state := State.IDLE  # 当前状态（默认为闲置）
var player: Node2D = null        # 玩家引用
var health := 100                # 生命值
var speed := 200.0               # 移动速度
var attack_range := 50.0         # 攻击范围

# _physics_process(): 物理帧处理
func _physics_process(delta: float):
    match current_state:  # 状态匹配
        State.IDLE:
            $AnimationPlayer.play("idle")  # 播放闲置动画
            if can_see_player():          # 条件检测
                current_state = State.CHASE  # 状态转移
            
        State.PATROL:
            $AnimationPlayer.play("walk")
            patrol_move()  # 执行巡逻移动逻辑
            if can_see_player():
                current_state = State.CHASE
            
        State.CHASE:
            $AnimationPlayer.play("run")
            var dir = (player.position - position).normalized()  # 计算朝向玩家的方向向量
            move_and_slide(dir * speed)  # 物理移动
            if position.distance_to(player.position) < attack_range:
                current_state = State.ATTACK  # 进入攻击范围后切换状态
            
        State.ATTACK:
            $AnimationPlayer.play("attack")
            if not $AttackCooldown.is_stopped():  # 检查攻击冷却
                current_state = State.CHASE

# 受伤处理
func take_damage(amount: int):
    health -= amount
    if health <= 0:
        die()  # 死亡处理
    elif current_state != State.ATTACK:
        current_state = State.CHASE  # 受伤后进入追逐状态

# 辅助方法：检测是否可见玩家
func can_see_player() -> bool:
    # 实现视线检测逻辑...
    return false
```

## 4. 函数与游戏机制

### 4.1 技能系统实现

```gdscript
# 技能管理系统
extends Node

# 冷却时间字典（记录各技能剩余冷却时间）
var cooldowns := {}
# 技能数据表（配置所有技能属性）
var skills := {
    "fireball": {
        "damage": 30,
        "cooldown": 2.0,
        "mana_cost": 15,
        "texture": preload("res://skills/fireball.png")
    },
    "heal": {
        "amount": 50,
        "cooldown": 5.0,
        "mana_cost": 20,
        "texture": null
    }
}

# 施放技能主函数
func cast_skill(skill_name: String, target = null) -> bool:
    # 检查是否可施放（冷却和资源）
    if not can_cast(skill_name):
        return false
    
    # 根据技能类型执行不同逻辑
    match skill_name:
        "fireball":
            # 实例化火球场景并初始化
            var fireball = preload("res://skills/fireball.tscn").instantiate()
            fireball.setup(skills[skill_name]["damage"], target)
            get_parent().add_child(fireball)
            
        "heal":
            # 治疗玩家
            get_parent().health += skills[skill_name]["amount"]
            show_heal_effect()  # 显示治疗特效
    
    start_cooldown(skill_name)  # 触发冷却
    return true  # 返回施放结果

# 检查技能是否可用
func can_cast(skill_name: String) -> bool:
    var skill = skills.get(skill_name, {})
    return (cooldowns.get(skill_name, 0) <= 0 and 
           get_parent().mana >= skill.get("mana_cost", 0))

# 开始技能冷却
func start_cooldown(skill_name: String):
    var cooldown_time = skills[skill_name]["cooldown"]
    cooldowns[skill_name] = cooldown_time  # 记录冷却时间
    
    # 创建计时器处理冷却
    var timer = get_tree().create_timer(cooldown_time)
    timer.timeout.connect(_on_cooldown_end.bind(skill_name))

# 冷却结束回调
func _on_cooldown_end(skill_name: String):
    cooldowns[skill_name] = 0  # 重置冷却时间
    print("技能 %s 冷却完毕" % skill_name)
```

## 5. 面向对象与游戏架构

### 5.1 物品系统设计

```gdscript
# 基础物品类（继承Resource便于资源化）
class_name Item
extends Resource

# 导出属性（可在编辑器设置）
@export var name := "物品"      # 物品名称
@export var icon: Texture2D    # 图标纹理
@export var max_stack := 1     # 最大堆叠数

# 使用物品虚方法（子类需实现）
func use(user: Node) -> bool:
    print("使用物品: ", name)
    return true  # 返回使用是否成功

# ===== 消耗品类 =====
class_name Consumable
extends Item

# 特有属性
@export var heal_amount := 0  # 治疗量
@export var mana_amount := 0  # 恢复魔法值

# 实现使用逻辑
func use(user: Node) -> bool:
    if not super.use(user):  # 调用父类方法
        return false
    
    # 治疗效果
    if heal_amount > 0:
        user.health = min(user.health + heal_amount, user.max_health)
    
    # 魔法恢复效果
    if mana_amount > 0:
        user.mana = min(user.mana + mana_amount, user.max_mana)
    
    return true

# ===== 武器类 =====
class_name Weapon
extends Item

# 武器属性
@export var damage := 10         # 基础伤害
@export var attack_speed := 1.0  # 攻击速度系数
var durability := 100            # 当前耐久度

# 使用武器（攻击）
func use(user: Node) -> bool:
    if not super.use(user):
        return false
    
    durability -= 1  # 减少耐久度
    return durability > 0  # 返回是否还能使用
```

## 6. 信号系统与游戏事件

### 6.1 成就系统实现

```gdscript
# 成就系统管理器
extends Node

# 已解锁成就列表
var unlocked_achievements := []

# 初始化连接信号
func _ready():
    # 连接全局事件总线信号
    EventBus.player_level_up.connect(_on_player_level_up)
    EventBus.enemy_defeated.connect(_on_enemy_defeated)
    EventBus.item_collected.connect(_on_item_collected)

# 玩家升级事件处理
func _on_player_level_up(new_level: int):
    # 检查是否达成"达到10级"成就
    if new_level >= 10 and not "reached_level_10" in unlocked_achievements:
        unlock_achievement("reached_level_10")

# 击败敌人事件处理
func _on_enemy_defeated(enemy_type: String):
    # 检查是否击败Boss
    if enemy_type == "boss" and not "boss_defeater" in unlocked_achievements:
        unlock_achievement("boss_defeater")

# 解锁成就主逻辑
func unlock_achievement(achievement_id: String):
    unlocked_achievements.append(achievement_id)  # 添加到已解锁列表
    show_achievement_popup(achievement_id)        # 显示解锁UI
    save_achievements()                          # 持久化存储
    
    print("成就解锁: ", achievement_id)  # 调试输出

# 显示成就弹窗
func show_achievement_popup(achievement_id: String):
    var popup = preload("res://ui/achievement_popup.tscn").instantiate()
    popup.setup(achievement_id)  # 初始化弹窗
    get_tree().root.add_child(popup)  # 添加到场景树

# 示例：在其他脚本中触发成就事件
func _on_boss_defeated():
    EventBus.emit_signal("enemy_defeated", "boss")  # 发射全局信号
```

## 7. 协程与游戏动画

### 7.1 过场动画序列

```gdscript
# 过场动画控制器
extends Node

# 播放完整过场动画序列
func play_cutscene():
    # 使用yield等待每个阶段完成
    yield(intro_sequence(), "completed")      # 等待开场序列
    yield(show_dialog("Welcome!"), "completed") # 等待对话显示
    yield(focus_on_objective(), "completed")  # 等待目标标记
    end_cutscene()  # 结束处理

# 开场动画序列
func intro_sequence():
    # 1. 背景音乐淡入（2秒）
    $MusicPlayer.fade_in(2.0)
    yield($MusicPlayer, "fade_finished")  # 等待淡入完成
    
    # 2. 镜头移动（3秒）
    $Camera.move_to(target_position, 3.0)
    yield($Camera, "movement_completed")
    
    # 3. 角色入场动画
    $Hero.play_entrance_animation()
    yield($Hero, "animation_finished")
    
    emit_signal("completed")  # 通知序列完成

# 显示对话
func show_dialog(text: String):
    $DialogBox.display_text(text)  # 调用对话框显示文字
    yield($DialogBox, "text_shown")  # 等待文字显示完成
    yield(get_tree().create_timer(1.0), "timeout")  # 额外停留1秒
    emit_signal("completed")

# 示例调用
func start_game():
    if should_play_intro:
        await play_cutscene()  # Godot 4.0+可以使用await语法
    start_level()
```

## 8. 最佳实践总结

### 8.1 游戏管理器实现

```gdscript
# 游戏全局管理器（建议设为自动加载单例）
extends Node

# 自定义信号定义
signal game_paused
signal game_resumed
signal player_died
signal level_loaded(level_num: int)

# 游戏状态变量
var current_level := 1       # 当前关卡编号
var player_lives := 3        # 玩家剩余生命
var is_game_paused := false  # 暂停状态
var score := 0               # 游戏分数

# 加载指定关卡
func load_level(level_num: int):
    current_level = level_num
    var path = "res://levels/level_%d.tscn" % level_num
    if ResourceLoader.exists(path):
        get_tree().change_scene_to_file(path)  # Godot 4.0+场景加载方式
        emit_signal("level_loaded", level_num)
    else:
        print("错误：关卡文件不存在 ", path)

# 切换暂停状态
func toggle_pause():
    is_game_paused = !is_game_paused
    get_tree().paused = is_game_paused  # 暂停游戏树
    
    if is_game_paused:
        emit_signal("game_paused")  # 通知暂停
    else:
        emit_signal("game_resumed") # 通知恢复

# 玩家死亡处理
func player_death():
    player_lives -= 1
    emit_signal("player_died")
    
    if player_lives <= 0:
        game_over()  # 游戏结束
    else:
        respawn_player()  # 玩家重生

# 示例用法
func _on_player_hit():
    GameManager.player_death()  # 通过单例访问

func _on_pause_button_pressed():
    GameManager.toggle_pause()
```

### 关键设计原则：
1. **单一职责**：每个脚本/类只负责一个明确的功能
2. **低耦合**：通过信号系统减少直接引用
3. **高内聚**：相关功能集中管理
4. **可扩展性**：使用继承和组合方便扩展
5. **编辑器友好**：合理使用@export和工具模式