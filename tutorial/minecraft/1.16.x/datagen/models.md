# Data Generators Part 2: States and Models
---

States and models are one of the hardest topics to review (excluding loot tables) due to the sheer amount of data that could be specified and developed. There are highly impressive block and item models out there that are just insane in terms of what Minecraft can handle. Now, however, we have data generators, making it hundreds of times easier to develop model files.

> Note: Although Minecraft supports its own model provider system, Forge has kept theirs in. We will be using Forge's system as it is deobfuscated and formatted much better.

## <a name="modelprovider"></a>ModelProvider <a href="#modelprovider"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
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

## <a name="blockstateprovider"></a>BlockStateProvider <a href="#blockstateprovider"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
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

> Note: The `BlockStateProvider` contains the ability to add to both a block model and item model. Therefore, `ItemModelProvider` should not be used in anyway when pertaining to blocks.

## <a name="modelbuilders"></a>ModelBuilders <a href="#modelbuilders"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Although this is not recommended, you can create custom models using these providers. However, you will have no idea of the output until you generate it and run it within the game. In most cases, you should create a template model using whatever software you like (e.g. [Blockbench](https://web.blockbench.net/)) and then generate the required block model based of that template.

For those who don't heed my warning and would like to try it themselves, here is a list of all methods provided by the ModelBuilders.

> Note: Some return types specify a generic. This generic extends `ModelBuilder` in some fashion.

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`parent` | `ModelFile` parent | `T` | Set the parent model for the current model.
`texture` | `String` key<br>`String` texture | `T` | Set the texture for a given dictionary key. This can specify another texture key (e.g. `#all`).
`texture` | `String` key<br>`ResourceLocation` texture | `T` | Set the texture for a given dictionary key.
`transforms` | N/A | `TransformsBuilder` | Creates a perspective map used to determine the orientation of a model during a particular `TransformType`.
`ao` | `boolean` ao | `T` | Sets if the model should have [ambient occulsion](https://en.wikipedia.org/wiki/Ambient_occlusion).
`guiLight` | `GuiLight` light | `T` | Set what type of [shading](https://minecraft.gamepedia.com/Model#Item_models) the model should have.
`element` | N/A | `ElementBuilder` | Creates an element to be added to the model. Usually a rectangular prism of some kind.
`element` | `int` index | `ElementBuilder` | Gets an existing element builder.

### <a name="transformsbuilder"></a>TransformsBuilder <a href="#transformsbuilder"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`transform` | `Perspective` type | `TransformVecBuilder` | Begin building a new transform for the given perspective. Takes in a `Perspective` as the `TransformType` does not hold the name specified in the JSON.
`end` | N/A | `T` | Returns the original instance of the model builder.

#### <a name="transformvecbuilder"></a>TransformVecBuilder <a href="#transformvecbuilder"><img src="../../../../images/link.png" alt="Link" style="width:10px;height:10px;"></a>

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`rotation` | `float` x<br>`float` y<br>`float` z | `TransformVecBuilder` | Sets the rotation of the model.
`translation` | `float` x<br>`float` y<br>`float` z | `TransformVecBuilder` | Sets the translation of the model.
`scale` | `float` sc | `TransformVecBuilder` | Sets the scale of the model equally.
`scale` | `float` x<br>`float` y<br>`float` z | `TransformVecBuilder` | Sets the scale of the model.
`end` | N/A | `TransformsBuilder` | Returns the current instance of the transformation builder.

### <a name="elementbuilder"></a>ElementBuilder <a href="#elementbuilder"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`from` | `float` x<br>`float` y<br>`float` z | `ElementBuilder` | Set the "from" position for this element.
`to` | `float` x<br>`float` y<br>`float` z | `ElementBuilder` | Set the "to" position for this element.
`face` |`Direction` dir | `FaceBuilder` | Return or create the face builder for the given direction.
`rotation` | N/A | `RotationBuilder` | Return or create the rotation builder of the given element.
`shade` | `boolean` shade | `ElementBuilder` | Defines if shadows are centered (`true` by default).
`allFaces` | `BiConsumer<Direction, FaceBuilder>` action | `ElementBuilder` | Modify all possible> faces dynamically using a function, creating new faces as necessary.
`faces` | `BiConsumer<Direction, FaceBuilder>` action | `ElementBuilder` | Modify all existing faces dynamically using a function.
`textureAll` | `String` texture | `ElementBuilder` | Texture all possible faces in the current element with the given texture, creating new faces where necessary.
`texture` | `String` texture | `ElementBuilder` | Texture all existing faces in the current element with the given texture.
`cube` | `String` texture | `ElementBuilder` | Create a typical cube element, creating new faces as needed, applying the given texture, and setting the cullface.
`end` | N/A | `T` | Returns the original instance of the model builder.

#### <a name="facebuilder"></a>FaceBuilder <a href="#facebuilder"><img src="../../../../images/link.png" alt="Link" style="width:10px;height:10px;"></a>

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`cullface` | `@Nullable Direction` dir | `FaceBuilder` | Specifies if the face does not need to be rendered when a block touches it at a certain position.
`tintindex` | `int` index | `FaceBuilder` | Specifies a hardcoded index value to look for when determining the value to tint the face with. This can usually be registered via a `IBlockColor` or `IItemColor`.
`texture` | `String` texture | `FaceBuilder` | Set the texture for the current face.
`uvs` | `float` u1<br>`float` v1<br>`float` u2<br>`float` v2 | `FaceBuilder` | Defines the area of the texture to use. These values should be between 0 and 16.
`rotation` | `FaceRotation` rot | `FaceBuilder` | Set the texture rotation for the current face.
`end` | N/A | `T` | Returns the current instance of the element builder.

#### <a name="rotationbuilder"></a>RotationBuilder <a href="#rotationbuilder"><img src="../../../../images/link.png" alt="Link" style="width:10px;height:10px;"></a>

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`origin` | `float` x<br>`float` y<br>`float` z | `RotationBuilder` | The origin to rotate the element around.
`axis` | `Direction$Axis` axis | `RotationBuilder` | The axis to rotate the element around.
`angle` | `float` angle | `RotationBuilder` | The angle of rotation. Needs to be one of the following values: `-45`, `-22.5`, `0`, `22.5`, `45`.
`rescale` | `boolean` rescale | `RotationBuilder` | Whether to scale all faces across the whole block.
`end` | N/A | `T` | Returns the current instance of the element builder.

### <a name="itemmodelbuilder"></a>ItemModelBuilder <a href="#itemmodelbuilder"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

This is an extension of `ModelBuilder` that can only be referenced for item models.

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`override` | N/A | `OverrideBuilder` | Creates a new override builder.
`override` | `int` index | `OverrideBuilder` | Gets an existing override builder.

#### <a name="overridebuilder"></a>OverrideBuilder <a href="#overridebuilder"><img src="../../../../images/link.png" alt="Link" style="width:10px;height:10px;"></a>

These are directly associated with item properties.

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`model` | `ModelFile` model | `OverrideBuilder` | The model file to display assuming the predicate returns true.
`predicate` | `ResourceLocation` key<br>`float` value | `OverrideBuilder` | Returns what the value of a certain property must be to display the corresponding model file.
`end` | N/A | `ItemModelBuilder` | Returns the original instance of the item model builder.

## <a name="application"></a>Application <a href="#application"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

So, let's once again create our basic `BlockStateProvider` and `ItemModelProvider` and register them within `GatherDataEvent`.

```java
public class BlockStates extends BlockStateProvider {

	public BlockStates(DataGenerator gen, ExistingFileHelper exFileHelper) {
		super(gen, Tutorial.ID, exFileHelper);
	}

	@Override
	protected void registerStatesAndModels() {}
}
```

```java
public class ItemModels extends ItemModelProvider {

	public ItemModels(DataGenerator generator, ExistingFileHelper existingFileHelper) {
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
		ExistingFileHelper helper = event.getExistingFileHelper();
		if(event.includeClient()) {
			...
			gen.addProvider(new ItemModels(gen, helper));
			gen.addProvider(new BlockStates(gen, helper));
		}
	}
}
```

> Note: The client providers take in an [`ExistingFileHelper`](./intro#existingfilehelper-and-output-setup).

Now all you have to do is rerun your datagens and you should have your models within the game. Once again an example will be added once all of data generators have been reviewed. If you are having trouble understanding any of the information above, take some time to learn about them before continuing on in this tutorial series. Otherwise, you will be extremely lost.

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/) under **States and Models**.

<!-- Completing states and models is an accomplishment within itself. Moving onto [loot tables](./loot_tables) will be a breeze. -->

Back to [Language Localization](./lang)  
Back to [Data Generators](../../index#data-generators)  
Back to [Minecraft Tutorials](../../index)  