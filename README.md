The Click Ninja
===================

A coding instructional implementation based on the [Predigame Platform](http://predigame.com/). Many of the features have been curated by ~75 aspiring innovators (ages 10+) who love to flex their STEM prowess by putting ideas to code!

Click Ninja was inspired by the app [fruit ninja](https://fruitninja.com/), but a little different since we have a mouse. As coders, we write the code and control how the game operates.

[![IMAGE ALT TEXT](http://img.youtube.com/vi/jKYfLYLctHE/0.jpg)](https://youtu.be/jKYfLYLctHE "Click Ninja")

## Instructional Coverage

We're working on some videos to describe Predigame concepts in more detail, but this game illustrates quite a few pretty cool features of the platform.

**Predigame Concepts Covered:**

- Callbacks (timing and mouse)
- Levels
- Image Sprite Effects
- Sound Effects

The game consists of three python (only one is needed to run the game):

-   `clickninja.py`  - You'll create this file as part of this tutorial.
-   `clickninja-final.py` - A "camera ready" version of the game.
-   `clickninja-levels.py` - An extension of `clickninja-final.py` but with level support.

## Installation (TODO)

You'll need the Predigame platform installed (visit  [http://predigame.com](http://predigame.com/)  if you need help).

-   Download a release:
-   Clone the repo:

## Running the Game

We recommend running all predigames from the command prompt/console/terminal. Be sure to  `cd`  into your game directory run:

```
my_machine$ pred clickninja.py
```
If you want to give one of the complete versions a spin, run one of these commands.
```
my_machine$ pred clickninja-final.py
my_machine$ pred clickninja-levels.py
```


# Basic Game

The fundamentals for click ninja are pretty basic. Open a text editor and copy in the follow code. This will create a window of 20x14 blocks and a title of 'Click Ninja'
```python
WIDTH = 20
HEIGHT = 14
TITLE = 'Click Ninja'
```
Save your changes. Let's call the file `clickninja.py`.  Try running the game from the terminal using the `pred` command (you'll want to run this command from the directory where you saved the file).

    my_machine$ pred clickninja.py

This game doesn't do much just yet. Just an empty window titled "Click Ninja". So boring. Let's add some more code. We added a bunch of comments to describe the purpose of each line.

```python
WIDTH = 20
HEIGHT = 14
TITLE = 'Click Ninja'

# the "main" part of our game
def spawn():

    # pick a random color
    target = choice([BLACK, ORANGE, AQUA, NAVY])

    # a virual "arc" -- three positions where
    # the object will move
    # arc[0]  bottom/off screen
    # arc[1] top of the arc
    # arc[2] bottom/off screen
    arc = rand_arc()

    # draw our sprite
    s = shape(CIRCLE, target, arc[0])

    # move to second and third points of arc
    # destroy if not hit
    s.move_to(arc[1], arc[2], callback = s.destroy)

    #tell this code to run again -- sometime between 100ms to 3secs
    callback(spawn, rand(0.1, 3))

# keep score (top left)
score(color = PURPLE)

# start the game in 1 second
callback(spawn, 1)

# register the 'r' key to reset the game
keydown('r', reset)  
```
Let's try our game now. We'll start to see a little more action. It looks a little like juggling, but we can't click on any of the circles. Notice that the last line of code will cause our game to reset if the 'r' key is pressed - this will be important a few steps later.

### Callback Functions
When we code a game we sometimes need to create actions that will *eventually occur*. Think of a mouse trap. It doesn't do much until, you know, the mouse comes by and eats the cheese. In code we define callback functions -- they don't do anything until a certain event, like a mouse click, occurs in the game.

The Python program language requires that we specify our **functions** before registering them in the code. Let's look at a simple callback example.

```python
def destroy(s):
    s.destroy()
```
This function will simply destroy a circle anytime one is clicked. Functions don't work until they are called, or in the case of callbacks, until they are registered.

```python
s = shape(CIRCLE, target, arc[0]).clicked(destroy)
```

That's right.. We can basically add a `.clicked(destroy)` to the end of our shape definition and *register* a callback function that won't get called until the player clicks on the circle.

Here's the full code now (we took out all the comments to make it a little easier to read). Let's give it a quick test.

```python
WIDTH = 20
HEIGHT = 14
TITLE = 'Click Ninja'

def destroy(s):
    s.destroy()

def spawn():
    target = choice([BLACK, ORANGE, AQUA, NAVY])

    arc = rand_arc()

    s = shape(CIRCLE, target, arc[0]).clicked(destroy)
    s.move_to(arc[1], arc[2], callback = s.destroy)
    callback(spawn, rand(0.1, 3))

score(color = PURPLE)
callback(spawn, 1)
keydown('r', reset)    
```
### Keeping Score
We can modify our destroy callback function to **score** the number of things we destroy. It's just requires adding a line of code.
```python
def destroy(s):
    # vvvv - add the line below - vvvv
    score(1)
    # ^^^^ - add the line above - ^^^^    
    s.destroy()
```
----------
# Version 1: Keep Alive

At this point we almost have a fully functional game. We just need to add a keep alive function. That is, we want to make sure we stop the game if we don't click on a circle. Look for this line of code in our game:

```python
    s.move_to(arc[1], arc[2], callback= s.destroy)
```
The way this code is written, the callback function `s.destroy` will be called if nothing else happens to the shape. Let's create a new callback function `failure(s)` that will pause the game. Notice we'll use something called a lambda. We'll be sure to discuss what that means at a later point.
```python
    s.move_to(arc[1], arc[2], callback = lambda: failure(s))
```
Let's make sure we create the failure function. We'll put that right under our `destroy` callback function.
```python
def failure(s):
    text('You Survived %s seconds' % time(), MAROON)
    callback(gameover, 0.01)
```
That's it! Here's the complete code if you need it. Go ahead and enjoy Version 1. How many circles can you click?
```python
WIDTH = 20
HEIGHT = 14
TITLE = 'Click Ninja'

def destroy(s):
    score(1)
    s.destroy()

def failure(s):
    text('You Survived %s seconds' % time(), MAROON)
    gameover()

def spawn():
    target = choice([BLACK, ORANGE, AQUA, NAVY])

    arc = rand_arc()

    s = shape(CIRCLE, target, arc[0]).clicked(destroy)
    s.move_to(arc[1], arc[2], callback = lambda: failure(s))
    callback(spawn, rand(0.1, 3))

score(color = PURPLE)
callback(spawn, 1)
keydown('r', reset)   
```
# Version 2: Throwing Food
Let's swap out circles for pictures of food. The predigame platform makes it easy to load pictures in your game. Just copy them to an `images` subdirectory. The click ninja includes a few food images to get started. Let's see what we have.

    my_machine$ ls images
    bananas.png  cherries.png ham.png      icee.png     pizza.png    taco.png
    bomb.png     fries.png    hotdog.png   olives.png   redsplat.png    

Let's say we want to load the hotdog image. We can do that with a single line of code.

```python
image('hotdog', (x, y), size=size)
```
We'll see that to load the image we need the first part of the file, the initial x and y coordinates, and the size (default is `1.0`). Notice that we don't need to include the `images` directory or the `.png` file extension. Predigame takes care of that for us.

So, now let's replace the circles with images. To do that we're going to rewrite our `spawn()` function.
```python
def spawn():

    speed = randint(2, 10)
    size = randint(1,4)

    target = choice(['bananas', 'cherries',
                     'olives', 'ham', 'hotdog',
                     'fries','icee', 'pizza'])

    arc = rand_arc()

    s = image(target, arc[0], size=size)
    s.speed(speed).clicked(destroy)
    s.move_to(arc[1], arc[2], callback = lambda: failure(s))

    callback(spawn, rand(0.1, 3))
```
In addition to drawing images, notice that we also have variables for `speed` and `size`. As our code runs anytime we'll draw a *random* target with a *random* speed and *random* size.  Notice the `randint` functions, such as `randint(2, 10)`. This will randomly pick a number between 2 and 10.

Let's try running our code. Here's the complete version.
```python
WIDTH = 20
HEIGHT = 14
TITLE = 'Click Ninja'

def destroy(s):
    score(1)
    s.destroy()

def failure(s):
    text('You Survived %s seconds' % time(), MAROON)
    callback(gameover, 0.01)

def spawn():

    speed = randint(2, 10)
    size = randint(1,4)

    target = choice(['bananas', 'cherries',
                     'olives', 'ham', 'hotdog',
                     'fries','icee', 'pizza'])

    arc = rand_arc()

    s = image(target, arc[0], size=size)
    s.speed(speed).clicked(destroy)
    s.move_to(arc[1], arc[2], callback = lambda: failure(s))

    callback(spawn, rand(0.1, 3))

score(color = PURPLE)
callback(spawn, 1)
keydown('r', reset)   
```

# Version 3: Bombs Away
Instead of just drawing food, let's throw some bombs too! Unlike food, our players can't click on a bomb or else.. well, game over! For starters, let's assume there is a 25% (1 out of 4) chance a bomb will be thrown. In code we'll want to add two lines in our `spawn()` function.

```python
    target = choice(['bananas', 'cherries',
                     'olives', 'ham', 'hotdog',
                     'fries','icee', 'pizza'])
    # vvv - add this code - vvv
    if randint(1, 4) == 2:
        target = 'bomb'
    # ^^^ - add this code - ^^^
```
The two lines we just added will replace the `target` variable with bomb with a 25% probability. Now, we said the player can't click on a bomb (game over otherwise) and, unlike food, if they don't click, we don't want to stop the game (because not clicking the bomb is what we want). This means we'll need to change the following lines:
```python
    # old code
    s = image(target, arc[0], size=size)
    s.speed(speed).clicked(destroy)
    s.move_to(arc[1], arc[2], callback = lambda: failure(s))
```
And check to see if target is a bomb.
```python
    # new code
    s = image(target, arc[0], size=size)
    if target == 'bomb':
       s.speed(speed).clicked(failure)
       s.move_to(arc[1], arc[2], callback = s.destroy)
    else:
       s.speed(speed).clicked(destroy)    
       s.move_to(arc[1], arc[2], callback = lambda: failure(s))

```
Let's try running our code. Here's the complete version.
```python
WIDTH = 20
HEIGHT = 14
TITLE = 'Click Ninja'

def destroy(s):
    score(1)
    s.destroy()

def failure(s):
    text('You Survived %s seconds' % time(), MAROON)
    callback(gameover, 0.01)

def spawn():

    speed = randint(2, 10)
    size = randint(1,4)

    target = choice(['bananas', 'cherries',
                     'olives', 'ham', 'hotdog',
                     'fries','icee', 'pizza'])

    if randint(1, 4) == 2:
        target = 'bomb'

    arc = rand_arc()

    s = image(target, arc[0], size=size)
    if target == 'bomb':
       s.speed(speed).clicked(failure)
       s.move_to(arc[1], arc[2], callback = s.destroy)
    else:
       s.speed(speed).clicked(destroy)    
       s.move_to(arc[1], arc[2], callback = lambda: failure(s))

    callback(spawn, rand(0.1, 3))

score(color = PURPLE)
callback(spawn, 1)
keydown('r', reset)   
```
# Version 4: Better Score

Our game ends quickly when we make a single mistake and that can make for a frustrating experience for even the best player. Let's improve our scoring with a few basic rules:

- Reward the player **five points** for clicking food
- Penalize the player  **twenty points** for missing a food item
- Halt the game when the number of points falls below zero
- Halt the game if the player clicks on a bomb

Here's our improved `destroy` and `failure` functions. Change your functions so they look like these:
```python
def destroy(s):
    score(5)
    s.destroy()

def failure(s):
    score(-20)
    if s.name == 'bomb' or score() < 0:
        text('You Survived %s seconds' % time(), MAROON)
        gameover()
```
Notice that in `failure` we added the line `if s.name == 'bomb' or score() < 0:`? Since our failure function is called if we don't click on a food or if we click on a bomb, we need to make sure that we end the game **immediately** if the player clicks on the bomb. It's a simple adjustment that will allow our code to be general enough for multiple cases.

Let's try running our code. Here's the complete version.
```python
WIDTH = 20
HEIGHT = 14
TITLE = 'Click Ninja'

def destroy(s):
    score(5)
    s.destroy()

def failure(s):
    score(-20)
    if s.name == 'bomb' or score() < 0:
        text('You Survived %s seconds' % time(), MAROON)
        callback(gameover, 0.01)

def spawn():
    speed = randint(2, 10)
    size = randint(1,4)

    target = choice(['bananas', 'cherries',
                     'olives', 'ham', 'hotdog',
                     'fries','icee', 'pizza'])

    if randint(1, 4) == 2:
        target = 'bomb'

    arc = rand_arc()

    s = image(target, arc[0], size=size)
    if target == 'bomb':
       s.speed(speed).clicked(failure)
       s.move_to(arc[1], arc[2], callback = s.destroy)
    else:
       s.speed(speed).clicked(destroy)    
       s.move_to(arc[1], arc[2], callback = lambda: failure(s))

    callback(spawn, rand(0.1, 3))

score(color = PURPLE)
callback(spawn, 1)
keydown('r', reset)   
```

# Version 5: The Bonus Taco
Just like a bomb, we can add a bonus taco that flies across the screen. We don't want to award our player too much, so we'll give the taco a 10% (1 out of 10) probability of showing on the screen. Here's what we'll need to change:

```python
    if randint(1, 4) == 2:
        target = 'bomb'
    if randint(1, 10) == 5:
        target = 'taco'
```
And if we pick a taco for a target, we'll need to change how the taco moves as we want it to fly across - not in an arc formation.

```python
    if target == 'bomb':
       s.speed(speed).clicked(failure)
       s.move_to(arc[1], arc[2], callback = s.destroy)
    elif target == 'taco':
       s.speed(5).clicked(destroy)
       s.move_to((-10, -2), (-5, HEIGHT/2), (WIDTH+1, HEIGHT/2), callback = s.destroy)
    else:
       s.speed(speed).clicked(destroy)    
       s.move_to(arc[1], arc[2], callback = lambda: failure(s))
```
Now when a user clicks on a taco, we want to reward some bonus points, so we'll need to tweak the `destroy` function.
```python
def destroy(s):
    if s.name == 'taco':
       score(50)
    else:
       score(5)
    s.destroy()
```
Let's try running our code. Here's the complete version.
```python
WIDTH = 20
HEIGHT = 14
TITLE = 'Click Ninja'

def destroy(s):
    if s.name == 'taco':
       score(50)
    else:
       score(5)
    s.destroy()

def failure(s):
    score(-20)
    if s.name == 'bomb' or score() < 0:
        text('You Survived %s seconds' % time(), MAROON)
        callback(gameover, 0.01)

def spawn():
    speed = randint(2, 10)
    size = randint(1,4)

    target = choice(['bananas', 'cherries',
                     'olives', 'ham', 'hotdog',
                     'fries','icee', 'pizza'])

    if randint(1, 4) == 2:
        target = 'bomb'
    if randint(1, 10) == 5:
        target = 'taco'

    arc = rand_arc()

    s = image(target, arc[0], size=size)
    if target == 'bomb':
       s.speed(speed).clicked(failure)
       s.move_to(arc[1], arc[2], callback = s.destroy)
    elif target == 'taco':
       s.speed(5).clicked(destroy)
       s.move_to((-10, -2), (-5, HEIGHT/2), (WIDTH+1, HEIGHT/2), callback = s.destroy)
    else:
       s.speed(speed).clicked(destroy)    
       s.move_to(arc[1], arc[2], callback = lambda: failure(s))

    callback(spawn, rand(0.1, 3))

score(color = PURPLE)
callback(spawn, 1)
keydown('r', reset)   
```
# Other Fun Things

## Spinning Sprites
We can code any image object to spin...
```python
s.speed(speed).spin().clicked(failure)
```
And the `spin` function can be given a number to control the rate
```python
# fast spinning
s.speed(speed).spin(0.1).clicked(failure)

# or

# slow spinning
s.speed(speed).spin(2.0).clicked(failure)


```

## Background Wallpaper
A white background can get a little boring. Try adding this line to the top of your code:

```python
TITLE = 'Click Ninja'

# load 'background/board.jpg' as the wallpaper
BACKGROUND = 'board'
```
`BACKGROUND` files are stored in the **background** directory.

## Sound Effects
Just like images, predigame supports sound effects. We've preloaded a few for the game. For instance, you can add a launching sound to the `spawn()` function:
```python
    target = choice(['bananas', 'cherries',
                     'olives', 'ham', 'hotdog',
                     'fries','icee', 'pizza'])

    if randint(1, 4) == 2:
        target = 'bomb'
    if randint(1, 10) == 5:
        target = 'taco'

    sound('launch')
```
You can add effects to when a user clicks on a food item or even looses.
```python
def destroy(s):
    sound('swoosh')
    if s.name == 'taco':
       score(50)
    else:
       score(5)
    s.destroy()

def failure(s):
    score(-20)
    if s.name == 'bomb' or score() < 0:
        sound('scream')
        text('You Survived %s seconds' % time(), MAROON)
        gameover()
```
## Effects
While it's cool to click away on our food. We can also introduce a fading effect that makes it slowly dissolve on contact. To do this, simply change the last line in the `destroy` function.
```python
def destroy(s):
    sound('swoosh')
    if s.name == 'taco':
       score(50)
    else:
       score(5)
    # s.destroy()
    # have the sprite fade out in 750 ms
    s.fade(0.75)
```

## Red Splat on Contact
Another fun thing is to leave a splat mark behind for each food item the player strikes. To do this, it's a simple extension to the `destroy` callback function.

```python
def destroy(s):
    sound('swoosh')
    if s.name == 'taco':
       score(50)
    else:
       score(5)

    # draw a splatting image at the position of the strike
    # make it fade away after 10 seconds
    image('redsplat', s.event_pos, 2).fade(1.0)

    s.destroy()
```
Here the `s` variable (which is short for sprite) has a position. So we want to draw the red splat image at the location of the strike.

## Exploding Bombs
Accidents happen! If the player accidentally clicks on a bomb, we should have them explode. It's a simple modification to the `failure` function.
```python
def failure(s):
    score(-20)
    # make the bomb explode
    if s.name == 'bomb':
        s.destroy()
        image('explode', s.center, 10).pulse(0.05)

    if s.name == 'bomb' or score() < 0:
        sound('scream')
        text('You Survived %s seconds' % time(), MAROON)
        callback(gameover, 0.01)
```
The key to the explosion is the `.pulse(0.05)` function call. The game will quickly pause, but the pulse is fast enough to provide an explosion effect.

# Levels!
Click Ninja is 1000x better when you add levels! We won't get into the coding specifics for levels here - that'll be covered in other [Predigame](http://predigame.com) examples, but here is a leveled version of the code, with plenty of inline comments:

```python
WIDTH = 20
HEIGHT = 14
TITLE = 'Click Ninja (Leveled Edition)'

# handle to current level (used by generic functions)
current_level = None

def destroy(s):
    sound('swoosh')
    current_level.hit()
    if s.name == 'taco':
       score(50)
    else:
       score(5)

    # draw a splatting image at the center position of the image
    image('redsplat', center=s.event_pos, size=2).fade(1.0)

    # add a fade out effect
    s.fade(0.25)

def failure(s):
    score(-20)
    if s.name == 'bomb':
        s.destroy()
        image('explode', center=s.center, size=10).pulse(0.05)

    if s.name == 'bomb' or score() < 0:
        sound('scream')
        text('You Survived %s seconds' % current_level.get_duration(), MAROON)
        callback(gameover, 0.01)

def spawn(min_size=4, max_size=4, min_speed=1, max_speed=1, min_rate=1, max_rate=1):
    """ leveled version includes min/max parameters """
    size = rand(min_size, max_size)
    speed = rand(min_speed,max_speed)

    target = choice(['bananas', 'cherries',
                     'olives', 'ham', 'hotdog',
                     'fries','icee', 'pizza'])

    if randint(1, 4) == 2:
        target = 'bomb'
    if randint(1, 10) == 5:
        target = 'taco'

    sound('launch')

    arc = rand_arc()

    s = image(target, arc[0], size=size)
    if target == 'bomb':
       s.speed(speed).spin(1).clicked(failure)
       s.move_to(arc[1], arc[2], callback = s.destroy)
    elif target == 'taco':
       s.speed(5).spin().clicked(destroy)
       s.move_to((-10, -2), (-5, HEIGHT/2), (WIDTH+1, HEIGHT/2), callback = s.destroy)
    else:
       s.speed(speed).clicked(destroy)
       s.move_to(arc[1], arc[2], callback = lambda: failure(s))

    callback(partial(spawn, min_speed=min_speed, max_speed=max_speed,
                     min_rate=min_rate, max_rate=max_rate,
                     min_size=min_size, max_size=max_size), rand(min_rate, max_rate))

class NinjaLevel(Level):
    """ defines the behavior of a single level """
    def __init__(self, level=1, total_hits=0, duration=0, min_size=4, max_size=4, min_speed=1, max_speed=1, min_rate=3, max_rate=3):
        self.level = level

        self.min_size = min_size
        # prevent sizes from getting too small
        if self.min_size < 0.1:
            self.min_size = 0.1

        self.min_speed = min_speed
        # prevent speed from getting too small
        if self.min_speed < 0.1:
            self.min_speed = 0.1

        self.min_rate = min_rate
        # prevent launch rates from getting too small
        if self.min_rate < 0:
            self.min_rate = 0

        self.max_size = max_size
        self.max_speed = max_speed
        self.max_rate = max_rate
        self.hits = 0
        self.total_hits = total_hits
        self.duration = duration

    def hit(self):
        self.hits += 1
        self.total_hits += 1
        score(self.total_hits, pos=LOWER_LEFT)

    def get_duration(self):
        return score(pos=LOWER_RIGHT)

    def setup(self):
        """ setup the level """
        global current_level
        current_level = self

        # BACKGROUND (randomly selected)
        background()

        # SCORE BOARD
        score(0, prefix='Score: ')
        score(self.total_hits, pos=LOWER_LEFT, color=BLACK, method=VALUE, prefix='Hits: ')
        score(pos=LOWER_RIGHT, color=BLACK, value=self.duration, method=TIMER,
              step=1, goal=1000, prefix='Duration: ')
        score(self.level, pos=UPPER_RIGHT, color=BLACK, method=VALUE, prefix='Level: ')

        # START LEVEL
        callback(partial(spawn, min_speed=self.min_speed, max_speed=self.max_speed,
                         min_rate=self.min_rate, max_rate=self.max_rate,
                         min_size=self.min_size, max_size=self.max_size), 1)

        # KEYBOARD EVENTS
        keydown('r', reset)

    def completed(self):
        """ 10 hits are required to complete a level """
        if self.hits == 10:
            return True
        else:
            return False

    def next(self):
        """ load the next level """
        return NinjaLevel(level=self.level+1, total_hits=self.total_hits,
                          duration=score(pos=LOWER_RIGHT),
                          min_speed=self.min_speed-0.1, max_speed=self.max_speed+0.5,
                          min_rate=self.min_rate-0.5, max_rate=self.max_rate-0.1,
                          min_size=self.min_size-0.5, max_size=self.max_size)

level(NinjaLevel(1))
```
