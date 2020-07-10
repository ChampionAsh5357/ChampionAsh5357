# <a name="blocks"></a>Blocks
---

Now that you have created your first item. It's now time to expand your horizons into the world of blocks. In this tutorial, we will take a deep dive into creating blocks. Specifically, we will be creating a basic block (along with its item) along with a breakdown of everything within the class itself.

## <a name="registry-setup"></a>Registry Setup
---

First let's setup a `DeferredRegister` and add it to our event bus via `Tutorial::addRegistries` where `Tutorial` is our main mod file. This should be done similarly to how we did it within the [`DeferredRegister`](../introduction/registries#deferredregister) section of the registries overview;

```java
public class TutorialBlocks {

	public static final DeferredRegister<Block> BLOCKS = DeferredRegister.create(ForgeRegistries.BLOCKS, Tutorial.ID);
}
```

```java
public class Tutorial {
	...
	private void addRegistries(final IEventBus modEventBus) {
		...
		TutorialBlocks.BLOCKS.register(modEventBus);
	}
}
```

This might seem like a repeat of the [`Item`](./items) section, which is most definitely what it is. However, we have to address a few extra cases regarding to blocks.

First of all, blocks are not items. A `Block` constitutes something that exists within the `World` while an `Item` is specifically related to something in your inventory. So, how can a `Block` exist in both the `World` and the inventory? It's made up of two parts: the `Block` in the world and the `Item` that you can pick up. For this, any `Block` we have must have the option to be an `Item` as well. So, to deal with this case, we're gonna create a method with the ability to add an `Item` for every `Block` we register.

We know that every `Block` must have a `String` registry name and a `Block` instance passed into it, but what about the `Item`? That's a little less clear. To easily register a `Block` as an `Item`, we will utilize the `BlockItem` class. However, this also takes in a `Block` as a parameter. So, how do we go about creating a method that allows us to apply a parameter to another parameter? Well, we can simply use a [function](https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html) to apply our block parameter to our `Item` function. We should also make it `@Nullable` in case we don't want our `Block` to have an `Item`. From there, it's just as simple as calling two registries:

```java
public class TutorialBlocks {
	...
	private static <V extends Block> RegistryObject<V> register(String id, V block, @Nullable Function<V, BlockItem> item) {
		if(item != null) TutorialItems.ITEMS.register(id, () -> item.apply(block));
		return BLOCKS.register(id, () -> block);
	}
}
```

> Note: We do have to return a `RegistryObject` so that we can call this block anywhere within our code.

> Note: We create a generic V such that it extends `Block` so that we know that the block being applied to the function and the block created are the same.

From there, let's create our basic block. In this example, we will use **ruby ore**. Since we are not changing anything from the `Block` class, we can just pass in a new `Block` constructor holding a simple new `AbstractBlock$Properties` instance to our `RegistryObject` at the moment along with the standard `BlockItem` constructor:

```java
public class TutorialBlocks {
	...
	public static final RegistryObject<Block> RUBY_ORE = register("ruby_ore", new Block(AbstractBlock.Properties.create(Material.ROCK)), block -> new BlockItem(block, new Item.Properties()));
	...
}
```

> Note: `AbstractBlock$Properties`'s constructor is private so it must be accessed through `AbstractBlock$Properties::create`. The most basic implementation takes in a `Material`. So, I will use `Material::ROCK` until we go over in-depth what everything is.

## <a name="resource-setup"></a>Resource Setup
---

Now we are missing a few things. I will not be going over the [texture file](./items#texture-file) or [language localization](./items#language-localization) as those remain exactly the same besides each item instance being replaced with block. So, let create that file tree now:

<div style="text-align:center">
<img src="./images/ruby_ore.png" alt="Ruby Ore Texture" width="256" height="256" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
</div>

```
assets/tutorial
├── lang
│	└── en_us.json
├── models
│	└── item
└── textures
	└── block
		└── ruby_ore.png
```

There are a few things I will discuss, such as the block state mapper, block model, item model. However, anything else is just excess baggage until we get to [Data Generators](#). This will allow us to generate everything (except for custom models and textures) without needing to write it by hand.

### <a name="block-and-item-model"></a> Block and Item Model

I have already discussed a basic understanding of this back in [Items](./items) so if you like to have a shallow dive into it, take a look there. In this case, we won't be doing anything special, so let's just create a basic block and item model to use.

There are many different block models to choose from. However, since we only have one texture, we want a model that maps all sides and the particle effect to a single texture. In this case, `minecraft:block/cube_all` allows us to do this with a reference name of `all`.

```json
{
  "parent": "minecraft:block/cube_all",
  "textures": {
    "all": "tutorial:block/ruby_ore"
  }
}
```

For our item model, since the `parent` gets all the attributes of the previous model, we just need to redirect the `parent` to our block model.

```json
{
	"parent": "tutorial:block/ruby_ore"
}
```

As for where to save these files, they each go in their respective object folder inside `models`.

```
assets/tutorial
├── lang
│	└── en_us.json
├── models
│	├── block
│	│	└── ruby_ore.json
│	└── item
│		└── ruby_ore.json
└── textures
	└── block
		└── ruby_ore.png
```

> Note: The block model does not have to be named `ruby_ore.json` as it is called via reference from a block state mapper. However, a good practice is to name your block models such that it includes the name of the `Block` somewhere within it.

### <a name="block-state-mapper"></a> Block State Mapper

[Block States](https://mcforge.readthedocs.io/en/latest/blocks/states/) are one of the most important things to know for creating more advanced blocks that I won't be covering right now. The link above will giev you a decent rundown for anyone wanting a better understanding until we cover it. 

For now, this should suffice. A `BlockState` is a representation of a `Block` in the `World` at a specific moment. A `Block` can have a wide variety of properties such as what direction its facing or how the block is rotated. A `BlockState` takes that information and converts it into useable properties within the world that can be adjusted using a variety of different methods within the class. This is where a `BlockState` mapper comes in. It takes the information provided by the `BlockState` and displays the correct model according to the current state.

In our case, our `Block` only has one state, so no variants need to be specified. In this JSON mapper, to specify an empty state within `variants`, we just represent it as an empty string. From there, the object holds the `model` location. Remember that `domain:location` in this case maps to `domain/models/location` and will be located in our `blockstates` folder.

```json
{
  "variants": {
    "": {
      "model": "tutorial:block/ruby_ore"
    }
  }
}
```

```
assets/tutorial
├── lang
│	└── ruby_ore.json
├── lang
│	└── en_us.json
├── models
│	├── block
│	│	└── ruby_ore.json
│	└── item
│		└── ruby_ore.json
└── textures
	└── block
		└── ruby_ore.png
```

![Ruby Ore](./images/blocks.png)

As you can see, we now have our `Block` fully added to the game!

## <a name="blocks-in-depth"></a>Block In-Depth
---

Now that we have completed the main portion of our tutorial, it's now time to take a more in-depth perspective in regards to the `Block` class and the `AbstractBlock$Properties` it provides.

> Note: Although you can create a `AbstractBlock`, the only purpose is to specify the bare minimum to be a `Block` and to hold the properties. You should always use the `Block` class when extending.

### <a name="material"></a>Material

To be able to create an `AbstractBlock$Properties`, you need to supply the intializer function with a `Material`. Which `Material` you choose depends on the specific scenario. Here are all the default materials to choose from.

Material | `MaterialColor` | Liquid | Solid | Blocks Movement | Opaque | Flammable | Replaceable | `PushReaction`
--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---:
`AIR` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`STRUCTURE_VOID` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`PORTAL` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`CARPET` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`PLANTS` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`OCEAN_PLANT` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`TALL_PLANTS` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`SEA_GRASS` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`WATER` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`BUBBLE_COLUMN` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`LAVA` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`SNOW` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`FIRE` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`MISCELLANEOUS` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`WEB` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`REDSTONE_LIGHT` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`CLAY` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`EARTH` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`ORGANIC` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`PACKED_ICE` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`SAND` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`SPONGE` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`SHULKER` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`WOOD` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`NETHER_WOOD (field_237214_y_)` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`BAMBOO_SAPLING` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`BAMBOO` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`WOOL` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`TNT` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`LEAVES` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`GLASS` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`ICE` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`CACTUS` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`ROCK` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`IRON` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`SNOW_BLOCK` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`ANVIL` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`BARRIER` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`PISTON` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`CORAL` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`GOURD` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`DRAGON_EGG` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`
`CAKE` | `null` | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | `NORMAL`

### <a name="abstractblock-properties"></a>AbstractBlock$Properties

All `Block`s are initialized with a new instance of an `AbstractBlock$Properties` object called via `AbstractBlock$Properties::create`. This class determines the various properties of our `Block`. Each method can be [chained](https://www.geeksforgeeks.org/method-chaining-in-java-with-examples/) I will be adding some of these properties to our ruby ore to make it act more like an actual `Block`.

Method | Parameter(s) | Default | Use
--- | :---: | :---: | ---
`create` | `Material` materialIn | NONE | Creates a `AbstractBlock$Properties` with a certain `Material` and its default `MaterialColor`.
`create` | `Material` materialIn<br>`DyeColor` color | NONE | Creates a `AbstractBlock$Properties` with a certain `Material` the `MaterialColor` associated with a certain dye.
`create` | `Material` materialIn<br>`MaterialColor` mapColorIn | NONE | Creates a `AbstractBlock$Properties` with a certain `Material` and `MaterialColor`.
`create (func_235836_a_)` | `Material` materialIn (p\_235836\_0\_)<br>`Function<BlockState, MaterialColor>` mapColorFunctionIn (p\_235836\_1\_) | NONE | Creates a `AbstractBlock$Properties` with a certain `Material` and `MaterialColor` dependent on the `BlockState`.
`from` | `AbstractBlock` blockIn | NONE | Creates a `AbstractBlock$Properties` with the same properties as another `AbstractBlock`.
`doesNotBlockMovement` | NONE | true | Makes an `AbstractBlock` able to be moved through with no collision (e.g. grass, flowers, etc.).
`notSolid` | NONE | true | Specifies an `AbstractBlock` to be not solid or that light can pass through it.
`harvestLevel` | NONE | -1 | Sets the harvest level of an `AbstractBlock` (e.g. 0 is wood, 1 is stone, 2 is iron, 3 is diamond).
`harvestTool` | NONE | `null` | Sets the harvest tool of an `AbstractBlock`. This does not explicity state that the block must be broken by this tool to be harvested, merely, this tool will improve the efficiency of mining.
`slipperiness` | `float` slipperinessIn | 0.6f | Sets how slippery when walking upon. The greater the number, the more slipperty the `AbstractBlock` is.
`speedFactor` | `float` factor | 1.0f | Determines how fast you can walk over the `AbstractBlock`. The greater the number, the fast you can walk on it.
`jumpFactor` | `float` factor | 1.0f | Determines how high you can jump off the `AbstractBlock`. The greater the number, the higher you can jump.
`sound` | `SoundType` soundTypeIn | `STONE` | Sets the sound information on what to play during certain actions.
`lightValue (func_235838_a_)` | `ToIntFunction<BlockState>` intFunctionIn (p\_235838\_1\_) | 0 | Sets the light level of an `AbstractBlock` dependent on the `BlockState`.
`hardnessAndResistance` | `float` hardnessIn<br>`float` resistanceIn | 0 | Sets the hardness and resistance of an `AbstractBlock`. Hardness represents the time taken to mine while resistance refers to its blast resistance.
`zeroHardnessAndResistance` | NONE | 0 | Sets the hardness and resistance of an `AbstractBlock` to zero.
`hardnessAndResistance` | `float` hardnessAndResistance | 0 | Sets the hardness and resistance of an `AbstractBlock` to the same value.
`tickRandomly` | NONE | false | Allows the `AbstractBlock` to tick randomly calling `AbstractBlock::randomTick` when ticked.
`variableOpacity` | NONE | false | Sets that the `AbstractBlock` has a varying level of transparency.
`noDrops` | NONE | `LootTables::EMPTY` | Makes the `AbstractBlock` not drop anything.
`lootFrom` | `Block` blockIn | Block Loot Table Location | Sets the `AbstractBlock` to drop loot corresponding to another `Block`.
`air (func_235859_g_)` | NONE | false | Sets the `AbstractBlock` to be recognized as air. This should not be used unless you are creating a new type of air.
`canEntitySpawnOn (func_235827_a_)` | `AbstractBlock.IExtendedPositionPredicate<EntityType<?>>` spawnTestFunction (p\_235827\_1\_) | `Block` is solid (on spawn side) and light is less than 14 | Sets a function to determine if an `Entity` can spawn on this `AbstractBlock`. This is just a general caes for all entities and can be more specifically tuned from an entity spawn placement function.
`isNormalCube (func_235828_a_)` | `AbstractBlock.IPositionPredicate` positionTestFunction (p\_235828\_1\_) | `Material` and `BlockState` opaque | Sets a function to determine whether this `AbstractBlock` should be treated normally (e.g. allow redstone wire on, allow chests to open when above, etc.).
`canSuffocate (func_235842_b_)` | `AbstractBlock.IPositionPredicate` positionTestFunction (p\_235828\_1\_) | `Material` blocks movement and is opaque | Sets a function to determine whether this `AbstractBlock` can suffocate the player.
`causesSuffocation (func_235847_c_)` | `AbstractBlock.IPositionPredicate` positionTestFunction (p\_235828\_1\_) | `Material` blocks movement and is opaque | Sets a function to determine whether this `AbstractBlock` can cause suffocation. Used to determine the overlay the player sees when suffocating.
`needsPostProcessing (func_235852_d_)` | `AbstractBlock.IPositionPredicate` positionTestFunction (p\_235828\_1\_) | false | Sets a function to determine whether this block needs some post processing when being set in the world (e.g. mushrooms and magma blocks).
`isEmissiveRendering (func_235856_e_)` | `AbstractBlock.IPositionPredicate` positionTestFunction (p\_235828\_1\_) | false | Sets a function to determine if this block uses emissive rendering. Only effects light map coordinates by setting it to 0xF000F0.
`requiresTool (func_235861_h_)` | NONE | false | Determines whether this `AbstractBlock` needs a tool to be harvested.

Since I want my ruby ore to have a hardness and resistance of 3 along with a harvest level of an iron pickaxe, I need to chain my methods to include these properties.

```java
public class TutorialBlocks {
	...
	public static final RegistryObject<Block> RUBY_ORE = register("ruby_ore", new Block(AbstractBlock.Properties.create(Material.ROCK).func_235861_h_().hardnessAndResistance(3.0f).harvestTool(ToolType.PICKAXE).harvestLevel(2)), block -> new BlockItem(block, new Item.Properties().group(ItemGroup.BUILDING_BLOCKS)));
	...
}
```

##TODO

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.61-web) under **Blocks**.

If you want to find more specific examples about blocks, go to [Block Extensions](../../#block-extensions).

Back to [Items](./items)  
Back to [Minecraft Tutorials](../../)  