# World Creation

Creating Worlds for Lavender is simple, all you need is to have a GameObject in your scene with the “World” Lavender SDK component attached to it, this acts as the spawn point for players and this is the absolute minimum required to get a scene functional for use within Lavender.

There’s a few ways to do this, one of them is to create a new object within the scene (Empty GameObject) and attach the “World” component to it, this is where players will start when entering your World. To see the starting point of players much easier in the editor window it is possible to use a 3D GameObject such as a 3D Cube to position the World component better and then later remove physics and mesh renderer/filter from it to prevent spawning issues. Make sure to save the scene.

When you’re ready to build your scene into a Lavender compatible World, all that is left to do is to create a new Lavender World bundle, this is done right clicking on an empty space in the Project Browser window and going to `Create -> Lavender -> World`
Fill out the name field of the World, this will be the name of the file on disk, drag the scene that was saved earlier into the scene field and press Package/Build and we’re done!

Now take that .world file to the website:
[Uploading Content](./uploading-content.md)
