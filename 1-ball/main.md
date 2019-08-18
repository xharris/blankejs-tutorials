# Bouncy Ball

## [ 1 ] Create a new project

1. click `New project`.

2. Choose the folder that will store your BlankE projects.

3. Name your poject `Bouncy Ball` (or anything you want, really) and click the checkmark to proceed.

## [ 2 ] Add a scene

__Add a what? A scene??__

> scenes are used for easily changing what's currently happening in the game. For example:
> 
> _MenuScene_ could be the main menu where you can start playing, go to settings, or click a quit button.
> 
> _SettingScene_ would contain code for a settings menu where you can change volume or controls. 
> 
> _PlayScene_ This is where you can actually play the game.

 It really doesn't matter what order you do them so we'll skip right to _PlayScene_.

 1. Go to the top left of the window, where it shows the name of the project. There's actually a search bar there.

 2. Search and select `Add a scene`.

 >NOTE: There are two ways to use the search bar. After typing something in, you can press _Tab_ or _Shift+Tab_ to move up and down the search results. Pressing _Enter_ will select that result. Another way is to just click the result.

 3. Let's name our scene `Playscene` and click the checkmark. This will open a code window containing our fresh new scene. :)

3 function are automatically created for Playscene: 

_enter()_ This will run whenever the scene starts. In this case, when the game is started.

_update(dt)_ This will run every frame of the game. Imagine watching a movie frame by frame, and in between each frame we can move actors around and change stuff around. That's pretty much how this function works. dt is a variable containing the amount of milliseconds that passed since the last frame.

_draw()_ This also runs every frame of the game. But try to use this function for just drawing stuff, which will be covered later.

This new file contains our BouncyBall entity class. 

## [ 3 ] Adding assets

Drag and drop an image, sound, or font file onto the IDE. It will be added to the project's asset folder.