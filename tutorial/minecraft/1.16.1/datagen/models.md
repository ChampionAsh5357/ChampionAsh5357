# <a name="models"></a>Data Generators Part 3: States and Models
---

States and models were one of the hardest topics to review (excluding loot tables) due to the sheer amount of data that could be specified and developed. There are highly impressive block and item models out there that are just insane in terms of what Minecraft can handle. Now, however, we have data generators, making it hundreds of times easier to develop model files.

> Note: Although Minecraft supports its own model provider system, Forge as kept theirs in. We will be using Forge's system as it is deobfuscated and formatted much better.

## <a name="modelprovider"></a>ModelProvider
---

Both `BlockModelProvider` and `ItemModelProvider` extends a class known as `ModelProvider`. This holds all the default model creating methods used by both classes as all models are created the same way. First, let's review a few extra methods.

Method | Parameter | Return Type | Use
--- | :---: | :---: | :---: | ---
`modLoc` | `String` name | `ResourceLocation` | Gets the mod location of the path
`mcLoc` | `String` name | `ResourceLocation` | Gets the Minecraft location of the path
`getExistingFile` | `ResourceLocation` path | `ModelFile.ExistingModelFile` | Checks if there is an existing file with this path. If not, throws an error.

> Note: There are certain cases where you won't have an existing model file (e.g. `minecraft:item/generated`), so you will need to use `UncheckedModelFile` instead.

The methods above will most likely be used with item models as `BlockStateProvider` recreates these methods for use.

The rest of the methods mentioned can be chained and returns `T extends ModelBuilder<T>`. These methods will be the ones used to generate the models specific to what you are doing.

Implementation | Method | Parameter(s) | Use
--- | :---: | :---: | ---
`ModelBuilder` | `parent` | `ModelFile` parent | Sets a parent model.
`ModelBuilder` | `texture` | `String` key<br>`String` texture | Sets a texture with the specified key.
`ModelBuilder` | `texture` | `String` key<br>`ResourceLocation` texture | Sets a texture with the specified key.
`ModelBuilder` | `ao` | `boolean` ao | Sets if the model should have [ambient occulsion](https://en.wikipedia.org/wiki/Ambient_occlusion).
`ModelBuilder` | `guiLight` | `GuiLight` light | Set what type of [shading](https://minecraft.gamepedia.com/Model#Item_models) the model should have.
`ModelProvider` | `getBuilder` | `String` path | Gets the builder of the specified model.
`ModelProvider` | `withExistingParent` | `String` name<br>`String` parent | Gets a builder with a parent for the specified model.
`ModelProvider` | `withExistingParent` | `String` name<br>`ResourceLocation` parent | Gets a builder with a parent for the specified model.
`ModelProvider` | `cube` | `String` name<br>`ResourceLocation` down<br>`ResourceLocation` up<br>`ResourceLocation` north<br>`ResourceLocation` south<br>`ResourceLocation` east<br>`ResourceLocation` west | Gets a builder with a cube parent and textures for the specified model.
`ModelProvider` | `singleTexture` | `String` name<br>`ResourceLocation` parent<br>`ResourceLocation` texture | Gets a builder with a single texture for the specified model.
`ModelProvider` | `singleTexture` | `String` name<br>`ResourceLocation` parent<br>`String` textureKey<br>`ResourceLocation` texture | Gets a builder with a single texture for the specified model.
`ModelProvider` | `cubeAll` | `String` name<br>`ResourceLocation` texture | Gets a builder with a cube all parent and textures for the specified model.
`ModelProvider` | `cubeTop` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` top | Gets a builder with a cube top parent and textures for the specified model.
`ModelProvider` | `sideBottomTop` | `String` name<br>`String` parent<br>`ResourceLocation` side<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with sides, bottom, and top textures for the specified model.
`ModelProvider` | `cubeBottomTop` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a cube bottom top parent and textures for the specified model.
`ModelProvider` | `cubeColumn` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` end | Gets a builder with a cube column parent and textures for the specified model.
`ModelProvider` | `orientableVertical` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` front | Gets a builder with a orientable verical parent and textures for the specified model.
`ModelProvider` | `orientableWithBottom` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` front<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a orientable with top and bottom parent and textures for the specified model.
`ModelProvider` | `orientable` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` front<br>`ResourceLocation` top | Gets a builder with a orientable with top parent and textures for the specified model.
`ModelProvider` | `crop` | `String` name<br>`ResourceLocation` crop | Gets a builder with a crop parent and textures for the specified model.
`ModelProvider` | `cross` | `String` name<br>`ResourceLocation` cross | Gets a builder with a cross parent and textures for the specified model.
`ModelProvider` | `stairs` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a stairs parent and textures for the specified model.
`ModelProvider` | `stairsOuter` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a outer stairs parent and textures for the specified model.
`ModelProvider` | `stairsInner` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a inner stairs parent and textures for the specified model.
`ModelProvider` | `slab` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a slab parent and textures for the specified model.
`ModelProvider` | `slabTop` | `String` name<br>`ResourceLocation` side<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a slab top parent and textures for the specified model.
`ModelProvider` | `fencePost` | `String` name<br>`ResourceLocation` texture | Gets a builder with a fence post parent and textures for the specified model.
`ModelProvider` | `fenceSide` | `String` name<br>`ResourceLocation` texture | Gets a builder with a fence side parent and textures for the specified model.
`ModelProvider` | `fenceInventory` | `String` name<br>`ResourceLocation` texture | Gets a builder with a fence inventory parent and textures for the specified model.
`ModelProvider` | `fenceGate` | `String` name<br>`ResourceLocation` texture | Gets a builder with a fence gate parent and textures for the specified model.
`ModelProvider` | `fenceGateOpen` | `String` name<br>`ResourceLocation` texture | Gets a builder with a open fence gate parent and textures for the specified model.
`ModelProvider` | `fenceGateWall` | `String` name<br>`ResourceLocation` texture | Gets a builder with a wall fence gate parent and textures for the specified model.
`ModelProvider` | `fenceGateWallOpen` | `String` name<br>`ResourceLocation` texture | Gets a builder with a open wall fence gate parent and textures for the specified model.
`ModelProvider` | `wallPost` | `String` name<br>`ResourceLocation` wall | Gets a builder with a wall post parent and textures for the specified model.
`ModelProvider` | `wallSide` | `String` name<br>`ResourceLocation` wall | Gets a builder with a wall side parent and textures for the specified model.
`ModelProvider` | `wallInventory` | `String` name<br>`ResourceLocation` wall | Gets a builder with a wall inventory parent and textures for the specified model.
`ModelProvider` | `panePost` | `String` name<br>`ResourceLocation` pane<br>`ResourceLocation` edge | Gets a builder with a pane post parent and textures for the specified model.
`ModelProvider` | `paneSide` | String` name<br>`ResourceLocation` pane<br>`ResourceLocation` edge | Gets a builder with a pane side parent and textures for the specified model.
`ModelProvider` | `paneSideAlt` | String` name<br>`ResourceLocation` pane<br>`ResourceLocation` edge | Gets a builder with a alternate pane side parent and textures for the specified model.
`ModelProvider` | `paneNoSide` | `String` name<br>`ResourceLocation` pane | Gets a builder with a no pane side parent and textures for the specified model.
`ModelProvider` | `paneNoSideAlt` | `String` name<br>`ResourceLocation` pane | Gets a builder with a no alternate pane side parent and textures for the specified model.
`ModelProvider` | `doorBottomLeft` | `String` name<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a bottom left door parent and textures for the specified model.
`ModelProvider` | `doorBottomRight` | `String` name<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a bottom right door parent and textures for the specified model.
`ModelProvider` | `doorTopLeft` | `String` name<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a top left door parent and textures for the specified model.
`ModelProvider` | `doorTopRight` | `String` name<br>`ResourceLocation` bottom<br>`ResourceLocation` top | Gets a builder with a top right door parent and textures for the specified model.
`ModelProvider` | `trapdoorBottom` | `String` name<br>`ResourceLocation` texture | Gets a builder with a bottom trapdoor parent and textures for the specified model.
`ModelProvider` | `trapdoorTop` | `String` name<br>`ResourceLocation` texture | Gets a builder with a top trapdoor parent and textures for the specified model.
`ModelProvider` | `trapdoorOpen` | `String` name<br>`ResourceLocation` texture | Gets a builder with a open trapdoor parent and textures for the specified model.
`ModelProvider` | `trapdoorOrientableBottom` | `String` name<br>`ResourceLocation` texture | Gets a builder with a orientable bottom trapdoor parent and textures for the specified model.
`ModelProvider` | `trapdoorOrientableTop` | `String` name<br>`ResourceLocation` texture | Gets a builder with a orientable top trapdoor parent and textures for the specified model.
`ModelProvider` | `trapdoorOrientableOpen` | `String` name<br>`ResourceLocation` texture | Gets a builder with a orientable open trapdoor parent and textures for the specified model.
`ModelProvider` | `torch` | `String` name<br>`ResourceLocation` torch | Gets a builder with a torch parent and textures for the specified model.
`ModelProvider` | `torchWall` | `String` name<br>`ResourceLocation` torch | Gets a builder with a wall torch parent and textures for the specified model.
`ModelProvider` | `carpet` | `String` name<br>`ResourceLocation` wool | Gets a builder with a cube carpet and textures for the specified model.

## <a name="modelprovider"></a>BlockStateProvider
---

One extra property blocks have is the addition to switch their model depending on their state. So, they also come with a whole range of methods that integrate the above ones to make an easily accessible and dynamic system.

> Note: Since these provide specifically for block states, there are no calls for item models. They have to be generated separately.

So now let's review the methods provided from `BlockStateProvider`.

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`getVariantBuilder` | `Block` b | `VariantBlockStateBuilder` | Gets the standard variant builder for the block.
`getMultipartBuilder` | `Block` b | `MultiPartBlockStateBuilder` | Gets the standard multipart builder for the block.
`models` | NONE | `BlockModelProvider` | Gets the block model builder.
`itemModels` | NONE | `ItemModelProvider` | Gets the item model builder.
`modLoc` | `String` name | `ResourceLocation` | Gets a mod location of the path.
`mcLoc` | `String` name | `ResourceLocation` | Gets a Minecraft location of the path.
`blockTexture` | `Block` block | `ResourceLocation` | Gets the default texture location of the block.
`cubeAll` | `Block` block | `ModelFile` | Creates a cube all model file.
`simpleBlock` | `Block` block | `void` | Creates a simple block state with a cube all model file.
`simpleBlock` | `Block` block<br>`Function<ModelFile, ConfiguredModel[]>` expander | `void` | Creates a simple block state with a cube all model file via a function.
`simpleBlock` | `Block` block<br>`ModelFile` model | `void` | Creates a simple block state with a model file.
`simpleBlockItem` | `Block` block<br>`ModelFile` model | `void` | Creates a simple item model.
`simpleBlock` | `Block` block<br>`ConfiguredModel...` models | `void` | Creates a simple block with the specified models.
`axisBlock` | `RotatedPillarBlock` block | `void` | Creates a rotated block state.
`logBlock` | `RotatedPillarBlock` block | `void` | Creates a rotated block state with log names.
`axisBlock` | `RotatedPillarBlock` block<br>`ResourceLocation` baseName | `void` | Creates a rotated block state.
`axisBlock` | `RotatedPillarBlock` block<br>`ResourceLocation` side<br>`ResourceLocation` end | `void` | Creates a rotated block state.
`axisBlock` | `RotatedPillarBlock` block<br>`ModelFile` model | `void` | Creates a rotated block state.
`horizontalBlock` | `Block` block<br>`ResourceLocation` side<br>`ResourceLocation` front<br>`ResourceLocation` top | `void` | Creates a horizontal block state.
`horizontalBlock` | `Block` block<br>`ModelFile` model | `void` | Creates a horizontal block state.
`horizontalBlock` | `Block` block<br>`ModelFile` model<br>`int` angleOffset | `void` | Creates a horizontal block state.
`horizontalBlock` | `Block` block<br>`Function<BlockState, ModelFile>` modelFunc | `void` | Creates a horizontal block state.
`horizontalBlock` | `Block` block<br>`Function<BlockState, ModelFile>` modelFunc<br>`int` angleOffset | `void` | Creates a horizontal block state.
`horizontalFaceBlock` | `Block` block<br>`ModelFile` model | `void` | Creates a horizontal facing block state.
`horizontalFaceBlock` | `Block` block<br>`ModelFile` model<br>`int` angleOffset | `void` | Creates a horizontal facing block state.
`horizontalFaceBlock` | `Block` block<br>`Function<BlockState, ModelFile>` modelFunc | `void` | Creates a horizontal facing block state.
`horizontalFaceBlock` | `Block` block<br>`Function<BlockState, ModelFile>` modelFunc<br>`int` angleOffset | `void` | Creates a horizontal facing block state.
`directionalBlock` | `Block` block<br>`ModelFile` model | `void` | Creates a directional block state.
`directionalBlock` | `Block` block<br>`ModelFile` model<br>`int` angleOffset | `void` | Creates a directional block state.
`directionalBlock` | `Block` block<br>`Function<BlockState, ModelFile>` modelFunc | `void` | Creates a directional block state.
`directionalBlock` | `Block` block<br>`Function<BlockState, ModelFile>` modelFunc<br>`int` angleOffset | `void` | Creates a directional block state.
`stairsBlock` | `StairsBlock` block<br>`ResourceLocation` texture | `void` | Creates a stairs block state.
`stairsBlock` | `StairsBlock` block<br>`String` name<br>`ResourceLocation` texture | `void` | Creates a stairs block state.
`stairsBlock` | `StairsBlock` block<br>`ResourceLocation` side<br>`ResourceLocation` bottom<br>`ResourceLocation` top | `void` | Creates a stairs block state.
`stairsBlock` | `StairsBlock` block<br>`String` name<br>`ResourceLocation` side<br>`ResourceLocation` bottom<br>`ResourceLocation` top | `void` | Creates a stairs block state.
`stairsBlock` | `StairsBlock` block<br>`ModelFile` stairs<br>`ModelFile` stairsInner<br>`ModelFile` stairsOuter | `void` | Creates a stairs block state.
`slabBlock` | `SlabBlock` block<br>`ResourceLocation` doubleslab<br>`ResourceLocation` texture | `void` | Creates a slab block state.
`slabBlock` | `SlabBlock` block<br>`ResourceLocation` doubleslab<br>`ResourceLocation` side<br>`ResourceLocation` bottom<br>`ResourceLocation` top | `void` | Creates a slab block state.
`slabBlock` | `SlabBlock` block<br>`ModelFile` bottom<br>`ModelFile` top<br>`ModelFile` doubleslab | `void` | Creates a slab block state.
`fourWayBlock` | `FourWayBlock` block<br>`ModelFile` post<br>`ModelFile` side | `void` | Creates a four way block state.
`fourWayMultipart` | `MultiPartBlockStateBuilder` builder<br>`ModelFile` side | `void` | Creates a four way block state.
`fenceBlock` | `FenceBlock` block<br>`ResourceLocation` texture | `void` | Creates a fence block state.
`fenceBlock` | `FenceBlock` block<br>`String` name<br>`ResourceLocation` texture | `void` | Creates a fence block state.
`fenceGateBlock` | `FenceGateBlock` block<br>`ResourceLocation` texture | `void` | Creates a fence gate block state.
`fenceGateBlock` | `FenceGateBlock` block<br>`String` name<br>`ResourceLocation` texture | `void` | Creates a fence gate block state.
`fenceGateBlock` | `FenceGateBlock` block<br>`ModelFile` gate<br>`ModelFile` gateOpen<br>`ModelFile` gateWall<br>`ModelFile` gateWallOpen | `void` | Creates a fence gate block state.
`wallBlock` | `WallBlock` block<br>`ResourceLocation` texture | `void` | Creates a wall block state.
`wallBlock` | `WallBlock` block<br>`String` name<br>`ResourceLocation` texture | `void` | Creates a wall block state.
`wallBlock` | `WallBlock` block<br>`ModelFile` post<br>`ModelFile` side | `void` | Creates a wall block state.
`paneBlock` | `PaneBlock` block<br>`ResourceLocation` pane<br>`ResourceLocation` edge | `void` | Creates a pane block state.
`paneBlock` | `PaneBlock` block<br>`String` name<br>`ResourceLocation` pane<br>`ResourceLocation` edge | `void` | Creates a pane block state.
`paneBlock` | `PaneBlock` block<br>`ModelFile` post<br>`ModelFile` side<br>`ModelFile` sideAlt<br>`ModelFile` noSide<br>`ModelFile` noSideAlt | `void` | Creates a pane block state.
`doorBlock` | `DoorBlock` block<br>`ResourceLocation` bottom<br>`ResourceLocation` top | `void` | Creates a door block state.
`doorBlock` | `DoorBlock` block<br>`String` name<br>`ResourceLocation` bottom<br>`ResourceLocation` top | `void` | Creates a door block state.
`doorBlock` | `DoorBlock` block<br>`ModelFile` bottomLeft<br>`ModelFile` bottomRight<br>`ModelFile` topLeft<br>`ModelFile` topRight | `void` | Creates a door block state.
`trapdoorBlock` | `TrapDoorBlock` block<br>`ResourceLocation` texture<br>`boolean` orientable | `void` | Creates a trapdoor block state.
`trapdoorBlock` | `TrapDoorBlock` block<br>`String` name<br>`ResourceLocation` texture<br>`boolean` orientable | `void` | Creates a trapdoor block state.
`trapdoorBlock` | `TrapDoorBlock` block<br>`ModelFile` bottom<br>`ModelFile` top<br>`ModelFile` open<br>`boolean` orientable | `void` | Creates a trapdoor block state.

> Note: As of **v32.0.61**, the `BlockStateProvider::wallBlock` methods are broken due to a change in state parameters.

## <a name="application"></a>Application
---

So, let's once again create our basic `BlockStateProvider` and `ItemModelProvider` and register them within `GatherDataEvent`.

```java
public class TutorialBlockStateProvider extends BlockStateProvider {

	public TutorialBlockStateProvider(DataGenerator gen, ExistingFileHelper exFileHelper) {
		super(gen, Tutorial.ID, exFileHelper);
	}

	@Override
	protected void registerStatesAndModels() {}
}
```

```java
public class TutorialItemModelProvider extends ItemModelProvider {

	public TutorialItemModelProvider(DataGenerator generator, ExistingFileHelper existingFileHelper) {
		super(generator, Tutorial.ID, existingFileHelper);
	}

	@Override
	protected void registerModels() {}
}
```

```java
public class Tutorial {
	...
	private void gatherData(final GatherDataEvent event) {
		...
		if(event.includeClient()) {
			ExistingFileHelper helper = event.getExistingFileHelper();
			...
			gen.addProvider(new TutorialItemModelProvider(gen, helper));
			gen.addProvider(new TutorialBlockStateProvider(gen, helper));
		}
		...
	}
}
```

> Note: The client providers take in an `ExistingFileHelper`. As this is only needed on the client at the moment, we declare the local variable inside the if statement.

Let's start with the basic item models. To do this, we have to get the current builder, declare the parent as an `UncheckedModelFile` for `minecraft:item/generated`, and give it a texture key of `layer0` and our path including the item folder.

```java
public class TutorialItemModelProvider extends ItemModelProvider {
	...
	public void simpleItem(Supplier<? extends Item> itemSupplier) {
		ResourceLocation location = itemSupplier.get().getRegistryName();
		this.getBuilder(location.getPath())
		.parent(new ModelFile.UncheckedModelFile("item/generated"))
		.texture("layer0", new ResourceLocation(location.getNamespace(), ITEM_FOLDER + "/" + location.getPath()));
	}
}
```

> Note: We make the value a `Supplier` since we are using `RegistryObject`s. For consistency, we will also create a `Supplier` version when we get to the block states.

From there, we can call the method within `ModelProvider::registerModels` for our items.

```java
public class TutorialItemModelProvider extends ItemModelProvider {
	...
	@Override
	protected void registerModels() {
		simpleItem(TutorialItems.RUBY);
		simpleItem(TutorialItems.RUBY_HELMET);
		simpleItem(TutorialItems.RUBY_CHESTPLATE);
		simpleItem(TutorialItems.RUBY_LEGGINGS);
		simpleItem(TutorialItems.RUBY_BOOTS);
	}
	...
}
```

As for the block states, there is already a method called `BlockStateProvider::simpleBlockItem`. All we need to do is override one of the other methods and call that method inside of it. Then, we can register the states and `runData`.

```java
public class TutorialBlockStateProvider extends BlockStateProvider {
	...
	@Override
	protected void registerStatesAndModels() {
		simpleBlock(TutorialBlocks.RUBY_ORE);
	}
	
	public void simpleBlock(Supplier<? extends Block> blockSupplier) {
		simpleBlock(blockSupplier.get());
	}
	
	@Override
	public void simpleBlock(Block block, ModelFile model) {
		super.simpleBlock(block, model);
		this.simpleBlockItem(block, model);
	}
}
```

After moving your files from `src/generated/resources` to `src/main/resources`, your block and item models should now be in the game!

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.61-web) under **States and Models**.

Completing states and models is an accomplishment within itself. Moving onto [loot tables](./loot_tables) will be a breeze.

Back to [Language Localization](./lang)  
Back to [Data Generators](../../index#data-generators)  
Back to [Minecraft Tutorials](../../index)  