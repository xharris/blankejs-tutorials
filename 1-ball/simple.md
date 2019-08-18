# Bouncy Ball Survival Game

Keep a bouncy ball bouncing in the air with your paddle. Don't let your paddle get destroyed!

## Elements

* Entities: Ball, Missile, Paddle

* States: PlayState

* Images: ![ball.png](ball.png) ![missile.png](missile.png) ![paddle.png](paddle.png)

## Get Started

1. Click `New Project`

2. Select the folder that will contain ALL of your BlankE projects.

3. Rename it in the `new project name` dialog and press the checkmark.

## Accessing the searchbar and creating files

In the top left corner where it displays the project name, there is actually a hidden searchbar. :)

* To create a regular file, search for `Add a script`

* To create an Entity or Scene, search `Add an entity` or `Add a scene`

Now we'll go through each script/entity/scene that we'll need for this game

## main.js (Add a script)

You can rename a script by clicking the 3-bar 'hamburger' menu in the top left

> ### Setting the controls

__BlankE.load()__

```
Input.set("move_left", "left", "a")
Input.set("move_right", "right", "d")
Input.set("move_up","up", "w")
Input.set("move_down","down", "s")
Input.set("restart","r")
```

## Entity -> Ball (Add an entity)

> ### __look like a bouncy ball _(drawing the ball image)___

__init()__

1. add the image of a ball `this.addSprite("ball")`

2. change the image offset to the center of the image `this.sprite_align = "center"`

> ### __fall towards the bottom of the screen _(affected by gravity)___

__init()__

1. give Ball some gravity `this.gravity = 6`

_(by default, `gravity_direction` is set to 90)_

> ### __bounce on the player's paddle _(has a hitbox and collision event)___

__init()__

1. add a hitbox `this.addShape('main','circle')`

    NOTE: The _main_ hitbox is a circle with a radius equal to half the width of the ball image. It doesn't matter if you use the image's width or height here since the image's width and height are the same (32 x 32)

__update(dt)__

1. bounce when Ball hits a paddle

```
this.onCollision['main'] = (other, res) => {
    if (other.tag == "Paddle") {
        this.collisionBounce(Util.lerp(1,1.01,this.y/Game.height))
        Event.emit("ball_bounce")
    }
}
```

The `Util.lerp` part is some math that makes the ball bounce higher if the paddle is lower to the bottom of the screen.

NOTE: we'll use that Signal emission later for scoring

> ### __bounce off the screen wall (keep the ball inside the screen)__

__update(dt)__

1. if the ball gets too close to the screen edges, flip its horizontal speed to have it go in the opposite direction

```
    if (this.x < 0 || this.x > Game.width)
        this.hspeed = -this.hspeed;
```

That should be everything we need for Ball!

## Entity -> Paddle

Paddle behavior:

> ### Draw a paddle!

__init()__

1. add the image of a paddle `this.addSprite("paddle")`

2. change the image offset to the center of the image `this.sprite_align = "center"`

3. give the Paddle friction so it won't move in one direction forever `this.friction = 0.4`

4. add a hitbox `self:addShape("main","rect")`

> ### Move left and right when the player presses those keys

__update(dt)__

1. determine how fast it will move `let move_spd = 5`

2. watch for key presses and move the paddle in the direction the player presses

```
if (Input("move_left").pressed)
    this.hspeed = -move_spd;
if (Input("move_right").pressed)
    this.hspeed = move_spd;
if (Input("move_up").pressed)
    this.vspeed = -move_spd;
if (Input("move_down").pressed)
    this.vspeed = move_spd;
```

NOTE: Creating the __move_spd__ variable is good practice instead of typing __5__ every time. If we test the game and realize the paddle moves too quickly, we can easily change the value of __move_spd__ instead of changing everywhere we typed __5__.

> ### Wrap the paddle around the edges of the game

__update(dt)__

1. check if it is out of bounds

```
if (this.x > Game.width)
	;
    
if (this.x < 0)
    ;

if (this.y < 0)
    ;

if (this.y > Game.height)
    ;
```

2. 'teleport' it to the other side of the screen, if it goes out of bounds

```
if (this.x > Game.width)
    this.x = 0;
if (this.x < 0)
    this.x = Game.width;
if (this.y < 0)
    this.y = Game.height;
if (this.y > Game.height)
    this.y = 0;
```

> ### Blow up the paddle when a Missile hits it (we'll add missiles later)

__update(dt)__

1. explode on contact with missile (destroy the missile too)

```
this.onCollision['main'] = (other) => {
    if (other.tag == "Missile") {
        other.parent.destroy();
        this.explode();
    }
}
```

__explode()__

_We're going to add a new method to our Paddle class named `explode`_

```
update (dt) {
    ...stuff in update function
}
explode () {
    ...our Paddle's new explode function!
}
```

_The stuff below will go inside `explode()`_

1. set a flag and check it so that the paddle can't explode again

```
explode () {
    if (this.exploded) return;
    this.exploded = true;
```

2. break the paddle image into pieces `let paddle_bits = this.sprites['paddle'].chop(5,8)`

3. throw them in random directions

```
paddle_bits.forEach(b => {
    let direction = Util.rand_range(45,135)
    b.hspeed = Util.direction_x(direction, 10)
    b.vspeed = Util.direction_y(direction, 10)
})
```

4. destroy the original paddle `this.destroy()`

Our final `explode()` code

```
if (this.exploded) return;
this.exploded = true;
		
let paddle_bits = this.sprites['paddle'].chop(5,8)
paddle_bits.forEach(b => {
    let direction = Util.rand_range(45,135)
    b.hspeed = Util.direction_x(direction, 10)
    b.vspeed = Util.direction_y(direction, 10)
})
this.destroy();
```

## Entity -> Missile

Missile behavior:

> ### Draw a missile

__init()__ 

1. add missile image `this.addSprite("missile")`

2. center it `this.sprite_align = "center"`

__update(dt)__

1. rotate the image to match the direction it's moving in `this.sprite_angle = this.direction`

> ### Make it a homing missile that follows the Paddle

__update(dt)__

1. if there is a paddle, move towards it

```
let paddle = Paddle.instances[0];
if (this.homing) {
    this.moveTowards(paddle.x, paddle.y, 1)
}
```

__init()__

1. after 10 seconds, stop homing in on the Paddle

```
this.homing = true;
Timer.after(10, ()=>{
    this.homing = false;
})
```

> ### ...and wait 3 seconds before moving

We want to give the player a chance to respond to these missiles before they become deadly (having a hitbox). 

For this we'll add a 'tween' to make the missile gradually appear. At the end of the tween, the missile will start moving.

To enable the Tween plugin, click the puzzle piece icon ('view plugins') and check Tween.js

__init()__

1. make the missile non-homing when it spawns `this.homing = false` (change the line where we put `this.homing = true`)

2. have the missile fade in while it's "charging up"

```
this.sprite_alpha = 0
let twn = new TWEEN.Tween(this)
    .to({ sprite_alpha:1 },3000)
    .easing(TWEEN.Easing.Quadratic.Out)
    .onComplete(()=>{
        this.homing = true;
		this.addShape('main','circle')
    })
    .start()
```

NOTE: We're putting `this.addShape` in the onComplete part of the tween. That means we need to remove the line we previously added (`this.addShape('main','circle')`). The reason for this is because we don't want the paddle to be able to hit the missile while it's fading in, but rather when the missile is completely visible and moving towards the paddle.

## Scene -> ScenePlay

What will happen in this Scene? This is where the game will be played. We'll spawn a paddle and a ball above it. Every few seconds we'll spawn a missile. Every time the ball hits the paddle, the player gains a point. If the ball falls off the bottom of the screen, the player loses.

> ### Set up the game

__onStart()__

1. Spawn a Paddle `scene.paddle = new Paddle()`

2. Spawn a Ball `scene.ball = new Ball()`

3. Move the Ball to the top-middle of the screen

```
scene.ball.x = Game.width / 2
scene.ball.y = 50
```

4. Move the Paddle to the center of the screen

```
scene.paddle.x = Game.width / 2
scene.paddle.y = Game.height / 2
```

5. Set some variables to track how the player is doing

```
scene.player_alive = true
scene.score = 0;
```

6. Spawn a missile every 5 seconds in a random spot

```
Timer.every(5000, ()=>{
    if (scene.player_alive) {
        let rand_missile = new Missile()
        rand_missile.x = Util.rand_range(50, Game.width-50)
        rand_missile.y = Util.rand_range(50, Game.height-50)
    }
});
```

> ### Handle the scoring

__onStart()__

1. Make a Text object

```
scene.txt_score = new Text({
        fontSize:16,
        align:"center",
        x: 30,
        y: 30,
        text:"SCORE: 0"
    });
```

__onUpdate()__

1. Every time the ball hits the paddle, increment the score. This will use the Signal we emitted earlier

```
Event.on('ball_bounce',()=>{
    scene.score += 1
    scene.txt_score.text = "SCORE: "+scene.score;
});
```

> ### End the game when the paddle breaks or the ball drops

__onStart()__

1. end game if paddle explodes 

```
Event.on('paddle_explode',()=>{
    scene.player_alive = false;
});
```

2. draw some game over text

```

```

__update(dt)__

1. end game if ball drops off screen

```
if (scene.ball.y > Game.height) {
    scene.paddle.explode();	
}
```

__update(dt)__

2. check if the player wants to restart

```
if (!scene.player_alive && Input("restart").released) {
    Scene.switch('ScenePlay')	
}
```

__There are plenty of other small touch-ups we could problably add to this, but at least we have a somewhat complete game!__