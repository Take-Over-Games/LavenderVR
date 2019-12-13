# Avatar Creation

[Advanced Users](#For-Advanced-Users)

## For new creators

Creating Avatars for Lavender requires a rigged character that Unity can use (a Humanoid character or any other Unity Mecanim viable rigged model), if such a model is available, creating an avatar is possible and can be done by first importing the 3d model into Unity by dragging and dropping it into the Project Folder, clicking on the model & then going to Rig, selecting “Humanoid” from the Animation Type then applying settings so the Avatar can be configured, then under Avatar Definition, select “Create From This Model” from the dropdown menu.  
This will switch Unity into the Rig Mapping mode, check if every bone is correctly assigned (If everything is green, things are likely good to go - depending on the character), scroll down on the very left side and press Apply/Done, assuming the model has been correctly configured, drag the model into the scene/hierarchy window and everything is good to go for the next step.


### Materials
Setting up Materials is required for the Avatar model to shade properly, if the model has already had materials setup in a 3d modelling suite such as Blender, Maya, 3DSMax, then it is possible to extract those materials. This is done by going to the same page as the Model Setup, clicking on the Materials tab and clicking on “Extract Materials”, choose a folder within the models Project Folder. In the material folder, select each material one by one and assign the texture(s) corresponding to the Material, if everything is setup correctly, you should see the changes reflected within the scene, assuming that the Model is present in the current scene.

**Note:** If the Standard shader material setup does not look satisfactory for an Anime/Cartoon style character, you may download a open-source toon shader. Here are two that should work with the latest version of Unity:

[NoeNoe](https://github.com/HugoZink/NoeNoeShaderEdits)
[Poiyomi](https://github.com/poiyomi/PoiyomiToonShader)

For the new Unity Scriptable Render Pipelines such as HDRP/URP, a Shader Graph shader will be linked here on a later date.

### Avatar Setup
This is the final step within Unity, to setup your Avatar for Lavender, click on your model, on the very right side click “Add Component” and search for “Avatar” and press Enter. This should add the Lavender SDK Avatar component to the character, this is a required component. Within this Script, we have to setup the View Offset and Mouth Offset and assign our Visemes, once this is done we can hit Quick Build Avatar to create it.  
The View Offset is used as the point of view position of our character, usually this point is positioned in between our characters eyes with a depth that matches this.  
The Mouth Offset is used as the audio position of our character, this is from where a sound will be heard from when a player talks.

### Avatar Head
In most cases, the Hide Action does not need to be changed, only in certain cases such as when our character has their face or head model separated from the body we may need to use Toggle Mesh instead of Scale Head Down and move the model into the selection field, advanced users are also able to use the Shape Key function.

#### Visemes
If our character in the scene has Visemes (pre-made mouth shapes required for lip sync), all we have to do is expand the prefab from the Unity Hierarchy found on the left side of the Editor and drag the character’s main mesh into the Viseme field, often this is called Body. If you have used a Blender addon called CATS to create these shapes, Lavender SDK script can auto-assign the Visemes for us. (Auto Assign by close enough name).

If everything is correct, hit Quick Build Avatar, the file will be in the root of our Project Folder. We’re done!

[Now we just need to upload to our account on the website](./uploading-content.md)

## For Advanced Users
Import your model, configure the Rig as Humanoid, setup materials, add the Lavender SDK “Avatar” component to the rigged model in your scene, adjust your viewball, mouth offset using either the input field or gizmos, assign/auto-assign Oculus Visemes and press Quick Build Avatar.

**Note:** If the filesize of the built avatar is larger than expected, check “Legacy Blend Shape Normals” in the model import options.

[With that out of the way, you can upload the avatar](./uploading-content.md)






