---
title: Adding core game objects
slug: core-game-objects
---

For Sushi Neko you will be using the default portrait orientation. You will need to modify the *GameScene.sks* scene
size to a iPhone resolution. The artwork was designed for iPhone 6 or 7, and should work for most other screen sizes.

## Set the Game to Portrait

This game will run in protrait mode only.

> [action]
> Select your project file, should be the first item in the project navigator on the left side. Under the general
> tab uncheck everything except "Portrait" under Deployment Info > Device Orientation.
>

## Setting up GameScene.sks

> [action]
> Open *GameScene.sks*, delete any default objects. `Zoom Out` until you can see the yellow bounding box of the scene.
> Click on
> *Atrributes inspector* and set the *Size* to `(320,568)`
>
> Also, move the anchor point to the lower left by setting *Anchor* to `(0, 0)`.
>
> ![GameScene size](../Tutorial-Images/xcode_gamescene_size.png)
>

## Adding the backdrop

It's nice to work with a backdrop, so let's add this before working on the core objects.

> [action]
> Drag *background.png* onto the scene and snap to the center. Set the *Z Position* to `-1`.
>

# Creating Sushi

Sushi is a key ingredient in the game mechanic, you will use it to build a sushi tower. To create the tower you will
be randomly stacking sushi, before you can stack the tower you need to build a core piece of sushi.  This master piece
will contain two chopsticks, one on the left and one on the right.  

This will enable 3 possible sushi pieces:

- Sushi with no chopsticks
- Sushi with left hand side chopstick
- Sushi with right hand side chopstick

Let's setup this master sushi piece.

> [action]
>
> Drag in *roll.png* and place it in the center of the screen near the bottom. I would suggest around `(160,160)`,
> set the *Name* to `sushiBasePiece`.
>
> Drag in *chopstick.png* and move it to the top-left side of the *sushiBasePiece*.
> You want this *chopstick* to be part of our **Sushi Piece**, set *Parent* to `sushiBasePiece` and I would suggest a
> position of around `(-92,37)`.  Set the *Name* to `leftChopstick`.
>
> ![Left chopstick setup](../Tutorial-Images/xcode_spritekit_leftchopstick.png)
>

Now we need to add the right hand side chopstick. Can you do this?

> [solution]
> *Copy / Paste* the left chopstick, set the *Name* to `rightChopstick`.  
> There is a simple trick to flip the asset horizontally, set *Scale X* to `-1`.
>
> ![Flip horizontally](../Tutorial-Images/xcode_spritekit_flip_horizontal.png)
>

## Working with GameScene

The default project comes with a lot of extra code you don't need for this project. You will want to remove it
before continuing.

> [action]
> Open GameScene.swift. Delete everything and replace it with:
>
```
import SpriteKit
>
class GameScene: SKScene {
>
}
```
>

## Sushi type

You will want to keep track of the type of sushi piece.
An *Enumeration* would work well for the first job, knowing what *Side* the chopstick is on will come in handy later
when you need to test for cat vs chopstick collisions.

> [action]
> Add the following code to the top of *GameScene.swift* before the class definition.
>
```
/* Tracking enum for use with character and sushi side */
enum Side {
    case left, right, none
}
```

Next up you will create a custom sushi class called *SushiPiece*

# The SushiPiece class

> [action]
> Create a new *Swift* file (`File > New > File > Swift File`) and name it `SushiPiece.swift`.
> Replace the contents of this file with:
>
```
import SpriteKit
>
class SushiPiece: SKSpriteNode {
>    
    /* Chopsticks objects */
    var rightChopstick: SKSpriteNode!
    var leftChopstick: SKSpriteNode!
>    
    /* You are required to implement this for your subclass to work */
    override init(texture: SKTexture?, color: UIColor, size: CGSize) {
        super.init(texture: texture, color: color, size: size)
    }
>    
    /* You are required to implement this for your subclass to work */
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
    }
>    
}
```
>

This gives you a basic subclass of *SKSpriteNode*, with properties ready to connect to the chopsticks.
Before you do this, you need to set the **sushiBasePiece** class to *SushiPiece*, you can do this via the
*Custom Class Inspector*.

> [action]
> Open *GameScene.sks*, select the *sushiBasePiece* and set *Custom Class* to `SushiPiece`
>
> ![Sushi custom class](../Tutorial-Images/xcode_spritekit_sushi_custom_class.png)

Run the game, you should expect to see the scene as it is in the editor.

![Screenshot Sushi](../Tutorial-Images/screenshot_sushi_basic.png)

Looking good, time for you to connect the chopsticks.

## Connecting chopsticks

Let's setup a function to handle this.

> [action]
> Add the following method to your *SushiPiece* class.
>
```
func connectChopsticks() {
  /* Connect our child chopstick nodes */
>
  func connectChopsticks() {
    /* Connect our child chopstick nodes */
    rightChopstick = childNode(withName: "rightChopstick") as! SKSpriteNode
    leftChopstick = childNode(withName: "leftChopstick") as! SKSpriteNode
}
```
>

Great, this would be a good time to add a property to track the type of sushi piece.

> [action]
> Add the following to the start of the *SushiPiece* class:
>
```
/* Sushi type */
var side: Side = .none {
    didSet {
        switch side {
        case .left:
            /* Show left chopstick */
            leftChopstick.isHidden = false
        case .right:
            /* Show right chopstick */
            rightChopstick.isHidden = false
        case .none:
            /* Hide all chopsticks */
            leftChopstick.isHidden = true
            rightChopstick.isHidden = true
        }
    }
}
```
>

## Property observation

You now have a *Side* property to keep track of the sushi type, using your previously defined Enumeration type. You can
make use of the *didSet* property observer to ensure the chopsticks are correctly setup as shown.

> [action]
> Add the following to the end of the *connectChopsticks* method:
>
```
/* Set the default side */
side = .none
```
>

The default *side* will be set to `.none`, you're not quite ready to run the game yet. You need to connect the **sushiBasePiece** into our *GameScene.swift*

# Connecting the sushi

Time to connect the **sushiBasePiece**.

> [action]
> Open *GameScene.swift* and add the following property to the class.
>
```
/* Game objects */
var sushiBasePiece: SushiPiece!
```
>

## Initializing the Scene with didMove(to view:)

The `didMove(to View:)` method is called when the scene appears on the screen. This is a great place to initialize
your game.

> Next add `didMove(to view:)`. Tip! If you start typing "didMove" Xcode will show a menu with code options.
> When you see "didMove(to view: SKView)" press the Return key and Xcode will type the rest of the function
> for you.
>
```
override func didMove(to view: SKView) {
    super.didMove(to: view)
>
}
```
>
>
> Next next add th following to `didMove(to view:)`. This creates a reference to the `sushioBasePiece` you created in
> *GameScene.sks*.
>
```
/* Connect game objects */
sushiBasePiece = childNode(withName: "sushiBasePiece") as! SushiPiece
```
>

## Connecting the chopsticks

You need to ensure the *connectChopsticks* method is called. A good place to add this would be after the
**sushiBasePiece** code connection.

> [action]
> Open *GameScene.swift* and add a call to this method anywhere after the **sushiBasePiece** code connection
> in `didMove(to view:)`
>
```
/* Setup chopstick connections */
sushiBasePiece.connectChopsticks()
```
>

Run the game, it should now look like this:

![Screenshot Sushi](../Tutorial-Images/screenshot_sushi_connected.png)

Awesome, let's add the heroic feline.

# Adding the cat

Time for your feline friend to enter the fray.

> [action]
> Open *GameScene.sks*, drag in *character.png* and place it in on the left side of the sushi, set the *Z Position* to `1`.  
> A *Position* of `(70,165)` looks pretty good.  Set *Name* to `character` and *Custom Class* to `Character`.

You may have noticed you don't have a *Character* class yet, let's create one. It's similar to the *SushiPiece* in
that you will want to track the *side* of the cat.

## The Character class

> [action]
> Create a new *Swift* file (`File > New > File > Swift File`) and name it `Character.swift`.
> Replace the contents of this file with:
>
```
import SpriteKit
>
class Character: SKSpriteNode {
>
    /* Character side */
    var side: Side = .left {
        didSet {
            if side == .left {
                xScale = 1
                position.x = 70
            } else {
                /* An easy way to flip an asset horizontally is to invert the X-axis scale */
                xScale = -1
                position.x = 252
            }
        }
    }
>    
    /* You are required to implement this for your subclass to work */
    override init(texture: SKTexture?, color: UIColor, size: CGSize) {
        super.init(texture: texture, color: color, size: size)
    }
>
    /* You are required to implement this for your subclass to work */
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
    }
}
```
>

After the *SushiPiece* setup, this code should be fairly clear. There is no need to implement a handler for the
`.none` enum case as the cat can only ever be on the `.left` or the `.right`.

Next you need to code connect the cat, see if you can do this yourself. The process is the same as *sushiBasePiece*,
just don't forget the name of this class :]

> [solution]
> Open *GameScene.swift* and add the following property to the class.
>
```
/* Cat Character */
var character: Character!
```
>

> Next create the code connection in `didMove(to view:)`
>
```
/* Connect game objects */
character = childNode(withName: "character") as! Character
```
>

Run the game, always good to frequently check everything is working after adding something new.

![Screenshot Sushi Cat](../Tutorial-Images/screenshot_cat_sushi_basic.png)

# Summary

Great, you've learnt to:

- Setup the two core objects of this game
- Create custom subclasses of *SKSpriteNode*
- Add a custom *Enumeration* type for use in tracking your game objects
- Implement property observation with *didSet*

Next you will get started building the Sushi Tower!
