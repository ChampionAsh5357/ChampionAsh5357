# Data Generators Part 4: Tags
---

Tags are the Minecraft implementation of Ore Dictionary that took over in 1.13. They are use in almost everything from recipes to what blocks can infinitely burn to even what an enderman can pick up. Tags are by far the easiest to implement with data generators.

## <a name="tagsprovider"></a>TagsProvider <a href="#tagsprovider"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

`TagsProvider` is the base class that `BlockTagsProvider`, `ItemTagsProvider`, 'EntityTypeTagsProvider', and `FluidTagsProvider` all extend to implement their current features. There can also be other implementations since the tag system is now extended to include types such as enchantments and potions, so you could add more providers for those as well. 

There are two main methods within the class that are extremely important to know about. For reference, `T` is a generic that extends some object.

Method | Parameter | Return Type | Use
--- | :---: | :---: | ---
`getOrCreateBuilder` | `ITag.INamedTag<T>` tag | `TagsProvider.Builder<T>` | Gets the provider builder for a tag.
`createBuilderIfAbsent` | `ITag.INamedTag<T>` tag | `ITag.Builder` | Gets the tag builder for a tag.

Since we are using the `TagProvider`, we will be using the first method more often than not. The builder also comes with its own list of methods that you can use.

Method | Parameter | Return Type | Use
--- | :---: | :---: | ---
`addItemEntry` | `T` item | `TagsProvider.Builder<T>` | Adds an object to a tag.
`addTag` | `ITag.INamedTag<T>` tag | `TagsProvider.Builder<T>` | Adds a tag to a tag.
`add` | `T...` toAdd | `TagsProvider.Builder<T>` | Adds these objects to a tag.
`add` | `ITag.ITagEntry` tag | `TagsProvider.Builder<T>` | Adds a tag entry to a tag.

> Note: `ItemTagsProvider` also has the method `ItemTagsProvider#copy` which copies a block tag to an item tag. This is especially useful as blocks can be referenced as both blocks and items.

To be able to get a specific tag, they are either referenced through Minecraft's `BlockTags`, `ItemTags`, `EntityTypeTags` or `FluidTags` and Forge's `Tags`. You can add your own tags as well by creating your own tag class. More on that in a bit.

## <a name="application"></a>Application <a href="#application"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Once again, we need to extend the specific tags class and override a specific method without returning the super: `TagsProvider#registerTags`. I will do this for all current supported tags in Minecraft and Forge, but I will only showcase two: blocks and items. All tag providers take in three parameters: the `DataGenerator`, the mod id, and the `ExistingFileHelper`. The item tag provider takes in the block tag provider as well since it has to be able to copy the data from blocks to items.

```java
public class BlockTags extends BlockTagsProvider {

	public BlockTags(DataGenerator generatorIn, ExistingFileHelper existingFileHelper) {
		super(generatorIn, Tutorial.ID, existingFileHelper);
	}

	@Override
	protected void registerTags() {
		
	}
}
```

```java
public class ItemTags extends ItemTagsProvider {

	public ItemTags(DataGenerator dataGenerator, BlockTagsProvider blockTagProvider, ExistingFileHelper existingFileHelper) {
		super(dataGenerator, blockTagProvider, Tutorial.ID, existingFileHelper);
	}

	@Override
	protected void registerTags() {
		
	}
}
```

```java
private void data(final GatherDataEvent event) {
	...
	if(event.includeServer()) {
		BlockTags blockTags = new BlockTags(gen, helper);
		gen.addProvider(blockTags);
		gen.addProvider(new ItemTags(gen, blockTags, helper));
		...
	}
}
```

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/) under **Tags**.

<!-- Finish off this six part tutorial with [advancements](./advancements). -->

Back to [Data Generators](../../index#data-generators)  
Back to [Minecraft Tutorials](../../index)  