# Items
---

Welcome you novice modders! Congrats on completing the Introduction into Minecraft programming with Forge. In this tutorial, we will take a deep dive into creating items. Specifically, we will be creating a basic item along with a breakdown of everything within the class itself.

## Registry Setup

First let's setup a `DeferredRegister` and add it to our event bus via `Tutorial::addRegistries` where `Tutorial` is our main mod file. This should be done similarly to how we did it within the [`DeferredRegister`](../introduction/registries#deferredregister) section of the registries overview;

```java
public class TutorialItems {

	public static final DeferredRegister<Item> ITEMS = DeferredRegister.create(ForgeRegistries.ITEMS, Tutorial.ID);
}
```
```java
public class Tutorial {
	...
	private void addRegistries(final IEventBus modEventBus) {
		TutorialItems.ITEMS.register(modEventBus);
	}
}
```

From there, let's create our basic item. In this example, we will use a **ruby**. Since we are not changing anything from the `Item` class, we can just pass in a new `Item` constructor holding a new `Item$Properties` instance to our `RegistryObject` at the moment:

```java
public class TutorialItems {

	public static final DeferredRegister<Item> ITEMS = DeferredRegister.create(ForgeRegistries.ITEMS, Tutorial.ID);

	public static final RegistryObject<Item> RUBY = ITEMS.register("ruby", () -> new Item(new Item.Properties()));
}
```

> Note that we don't have to call the constructor for the `Item$Properties` from its main class. We could easily call it as `new Properties()` instead of `new Item.Properties()`. We mainly do this because there are multiple classes named `Properties`, so this helps us differentiate them.

Now if you load up the game, you can use the `give` command (like so `/give @p tutorial:ruby`) to give yourself the `Item`:

![Ruby with no resources](./images/items_before.png)

What's going on? Our `Item` is in the game, but it's missing a lot of things. As you can see, if we never give our `Item` a texture or a model, it will appear as the default missing texture model in-game. If we also don't add a localization string for our language, it will also appear as its default translation key (e.g. `item.tutorial.ruby`)

> Note that a translated key is usually broken down into three parts: the specific object type, the mod id, and then the registry name each separated by a '.'.

## Resource Setup

To do this, we're gonna need to update the file tree a bit. Currently we have the following file tree from our `src` folder:

```
src
└── main
	├── java
	│	└── io
	│		└── github
	│			└── championash5357
	│				└── tutorial
	│					├── client
	│					│	└── proxy
	│					│		└── ClientProxy.java
	│					├── init
	│					│	└── TutorialItems.java
	│					├── proxy
	│					│	└── IProxy.java
	│					├── server
	│					│	└── proxy
	│					│		└── ServerProxy.java
	│					└── Tutorial.java
	│
	└── resources
		├── pack.mcmeta
		└── META-INF
			└── mods.toml
```

Let's condense this down further to only reference our `src/main/resources` folder:

```
src/main/resources
├── pack.mcmeta
└── META-INF
	└── mods.toml
```

To generate anything that has to do with game assets (sounds, models, textures, etc.), we need to create a folder called `assets`. From there, to specify that this is for our mod, we need to add another folder within that with our mod id (e.g. `examplemod` or in this case `tutorial`).

```
src/main/resources
├── assets
│	└── tutorial
├── pack.mcmeta
└── META-INF
	└── mods.toml
```

From there, we need to add a few more directories/folders to `assets/tutorial`. First, our language localization files will be stored within the `lang` folder. Next, models shall be stored in the `models` folder. Since we are working with `Item` models, the `models` folder will have a subdirectory called `item`. Finally, textures shall be stored in the `textures` folder. Once again, since we are working with `Item`s, this will be stored in a subdirectory called `item`. Here is a view from the `assets/tutorial` folder:

```
assets/tutorial
├── lang
├── models
│	└── item
└── textures
	└── item
```

When we create anything that has to do with items from now on, I will be referring to these specific folders. 

### Texture File

Texture files are stored within `textures/item` as a **Portable Network Graphics (PNG)** file. Textures are traditionally 16 by 16 pixels, however they can be expanded to any size. You can also have a [animated texture](https://minecraft.gamepedia.com/Resource_Pack#Textures) by making the height increase by a multiple of the sides (e.g. 16 x 32 would have two 16 x 16 images to cycle through). To specify a animated texture, add another file on top of the image file that appends `.mcmeta` to the end (e.g. `test.png` would have another file called `test.png.mcmeta`). Take a look at the above link if you would like to learn more.

> Note: Textures are stretched and compressed according to the model. Unless otherwise specified as a dynamic texture or by the UV mapping of the model, the texture will be mapped to each individual pixel by ratio. For example: if I had a 32 by 256 pixel texture with no dynamic mappings, it would be compressed by a factor by 2 on the width and a factor of 16 on the height, resulting in a very weird looking texture. The quality of the picture will still remain, so for higher definition textures, stick to a square image with their sides being multiples of 16 pixels.

PNG files have different bit-depths corresponding to how the image will be read. The two we will be reviewing is **PNG-24** and **PNG-32**. **PNG-24** supports all 16 million colors and some background index transparency/matte. If you're image is saved with a 24-bit depth, there will be no transparency rendered within your image whatsoever. **PNG-32** supports all 16 million colors including full alpha transparency. All images within Minecraft are saved in this format. Make sure when you create your image it is saved as a PNG file with 32-bit depth.

For our case, I edited the emerald within Minecraft to make it look like a ruby for our example:

<div style="text-align:center">
![Ruby Texture](./images/ruby.png = 256x256)
</div>

From here, I will save it as a PNG-32 file within our `textures/item` folder resulting in our file tree now looking like this:

```
assets/tutorial
├── lang
├── models
│	└── item
└── textures
	└── item
		└── ruby.png
```

##TODO

### Item Model

### Language Localization

##TODO Advanced Setup for Items

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.57-web) under **Items**.

If you want to find more specific examples about items, go to [Item Extensions](../../#item-extensions).

Now that we have programmed an item, let's give it a [block](./blocks) to interact with.

Back to [Registries](../introduction/registries)  
Back to [Minecraft Tutorials](../../)  