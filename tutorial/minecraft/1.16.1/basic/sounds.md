# Sounds
---

Have you ever wanted to hear something within the game? Well with this tutorial, you'll be able to add custom sounds and call them within your code.

## <a name="sounds-json"></a>sounds.json <a href="#sounds-json"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

To be able to represent a sound within the game, you must first declare the sound within a `sounds.json` file. Here is an overview of all the [parameters](https://minecraft.gamepedia.com/Sounds.json#File_structure) that can appear in one of these files. However, we will only be focusing on three values.

First, you must declare a `JsonObject` with a name of some kind. This name is what you will use to determine the location within the code. Within this object, there will be two parameters. The first is `subtitle` which takes a translation key of how the sound should be displayed as a subtitle. The second is `sounds` which takes an array of strings that point to the current location of the sound. If there is more than one string, it will randomly select one. In my case, I am going to add a sound for the [washer](../blocks/blockstate) called `running` and store it within `assets/tutorial/sounds/block/washer`.

```java
{
  "block.washer.running": {
    "subtitle": "subtitle.tutorial.block.washer.running",
    "sounds": [
      "tutorial:block/washer/running"
    ]
  }
}
```

> Note: Translation keys should always include the mod id within them.

> Note: The mod id is needed to determine the location of the sound. However, the reference (`block.washer.running`) is appended to the current location of this json file. In our case, since it is in the `tutorial` folder, the registry id for this sound would be `tutorial:block.washer.running`.

We will then save this file within our `tutorial` folder like so:

```
assets/tutorial
├── blockstates
├── lang
├── models
├── textures
└── sounds.json
```

Now we are going to need a sound to point to. All audio files within the game must be a [Ogg Vorbis](https://xiph.org/vorbis/) (.ogg) sound file. There are thousands of ways to convert audio files to this type such as [Audacity](https://www.audacityteam.org/). To store the file, it will be located within a `sounds` folder within the namespace specified.

```
assets/tutorial
├── blockstates
├── lang
├── models
├── sounds
│	└── block
│		└── washer
│			└── running.ogg
├── textures
└── sounds.json
```

Remember to add the localization key for the subtitle in your lang JSON. I wrote a little code for the provider that converts the next section into these translation keys for me.

## <a name="soundevent"></a>SoundEvent <a href="#soundevent"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Once again, we will need to setup a [`DeferredRegister`](../introduction/registries#deferredregister). However, we won't be doing the same simple repeat. Each `SoundEvent` has a parameter to point to the reference within `sounds.json`. This just so happens to be the same as our registry name. So, let's create a method that optimizes this transfer. The easiest method to do this is to pass in a function that takes in a `ResourceLocation` and returns an object that extends `SoundEvent`. Then we can register our sound in the game like normal.

```java
public class TutorialSounds {

	public static final DeferredRegister<SoundEvent> SOUNDS = DeferredRegister.create(ForgeRegistries.SOUND_EVENTS, Tutorial.ID);
	
	public static final RegistryObject<SoundEvent> BLOCK_WASHER_RUNNING = register("block.washer.running", name -> new SoundEvent(name));
			
	private static <V extends SoundEvent> RegistryObject<V> register(String id, Function<ResourceLocation, V> function) {
		return SOUNDS.register(id, () -> function.apply(new ResourceLocation(Tutorial.ID, id)));
	}
}
```

> Note: `SoundEvent`s are only needed to call a sound within the code. All sounds can be called in the game with only a `sounds.json`.

## <a name="soundsprovider"></a>SoundsProvider <a href="#soundsprovider"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Because I am lazy when it comes to writing JSON files, I have writen a data generator to create sounds for me. As it hasn't currently been verified as proper standard by Forge, I will not create a tutorial on it. You can check out the code in the GitHub however if you want to try it.

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.70-web) under **Sounds**.

Back to [Ore Generation](./ore_gen)  
Back to [Basics](../../index#modding-101)   
Back to [Minecraft Tutorials](../../index)  