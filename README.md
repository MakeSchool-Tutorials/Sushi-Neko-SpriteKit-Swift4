# Sushi Neko SpriteKit Swift 3

Use SpriteKit and Swift 3 to build a game like [Timberman](http://www.digitalmelody.eu/games/Timberman). This version of the
tutorial has been updated for Swift 3 _using Xcode beta 6_.

<!-- screenshots and gifs from licecap (http://www.cockos.com/licecap/), include them in a screenshots folder and reference with relative links -->

## What you will learn

The tutorial covers concepts like: 

- Working with Scene files (.sks)
- Enums 
- SpriteKit's coordinate system
- Creating action in the scene editor 
- Use comments to label and organize your code

<!-- list of concepts they will understand after finishing -->

## Common issues

Creating games using the scene editor allows for a fast visual system for setting up yout game. It also requires the use 
of string identifiers for elements created in the editor. This can lead to spelling errors that cause your code not 
to find elements in the scene. 

The same is true for setting the class customo classes for elements created in the scene editor. Custom classes also bring
a problem where they are not recognized when the name of your project uses spaces or special characters. These types of 
errors might look like: 

```
Could not cast value of type 'SKSpriteNode' (0x103bf7928) to 'Game_O_Life.MSButtonNode' (0x102fa46b8).
```

Here the project was named "Game-O-Life". Changing the name of the project to "GameOLife" fixed the problem. 

<!-- faq list of common issues students run into -->

- Misspelling, or not setting the name of an element in the scene causes the scene not to find the element
- Using a project name with sapce or special character causes the scene to not recognize custom classes

## What you should understand when finished

By the time you complete this tutorial you should be able to make an app game using SpriteKit. This learning how to create
Sprite nodes, place them on the screen, and apply actions provides limitless possibilities. 

<!-- list of check for understanding questions the student should be able to answer when finished -->

- Have a basic understanding of SpriteKit's core concepts.
- Understand the scene editor
- Use actions to move and animate sprites at your whim. 
