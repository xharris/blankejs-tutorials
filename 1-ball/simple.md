# Bouncy Ball Survival

Keep a bouncy ball bouncing in the air with your paddle. Don't let your paddle get destroyed!

## Ingredients

* Entities

    * Ball

    * Missile

    * Paddle

* States

    * PlayState

* Images

    ![ball.png](ball.png)

    ![missile.png](missile.png)

    ![paddle.png](paddle.png)

## main.lua

> ### Setting the controls and the first state

__BlankE.load()__

```
Input.set("move_left", "left")
Input.set("move_right", "right")

BlankE.options.state = "PlayState"
```

## Entity -> Ball

> ### __look like a bouncy ball _(drawing the ball image)___

__init()__

1. add the image of a ball `self.img_ball = Image("ball")`

__update(dt)__

1. move the ball image to the Ball object's position

```
self.img_ball.x = self.x
self.img_ball.y = self.y
```

__draw()__

1. We can't forget to actually draw the ball image `self.img_ball:draw()`


> ### __fall towards the bottom of the screen _(affected by gravity)___

__init()__

1. give Ball some gravity `self.gravity = 0.5`

> ### __bounce on the player's paddle _(has a hitbox and collision event)___

__init()__

1. add a hitbox `self:addShape('main','circle',{0,0,self.img_ball.width/2})`

    NOTE: The _main_ hitbox is a circle with a radius equal to half the width of the ball image. It doesn't matter if you use the image's width or height here since the image's width and height are the same (32 x 32)

__update(dt)__

1. bounce when Ball hits a paddle

```
self.onCollision['main'] = function(other)
    if other.parent.classname == "Paddle" then
        self:collisionBounce()
    end
end
```

That should be everything we need for Ball!

## Entity -> Paddle

Paddle behavior:

> ### Draw a paddle!

__init()__

1. add the image of a paddle `self.img_paddle = Image("paddle")`

__update(dt)__

1. move the image

```
self.img_paddle.x = self.x - (self.img_paddle.width / 2)
self.img_paddle.y = self.y - (self.img_paddle.height / 2)
```

NOTE: subtracting half of the images height and width will make it so the image is in the center of it's actual position.

> ### Move left and right when the player presses those keys

__update(dt)__

1. watch for key presses



* Wrap the paddle around the edges of the game

__init()__


1. 

## State -> PlayState

What will happen in this State?

* The game will start with a Paddle.

* A Ball will then drop from the top of the screen.

* If the Ball falls off the screen, the game ends and the score is shown.

__enter()__

1. 
