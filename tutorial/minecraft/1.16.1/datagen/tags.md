# Data Generators Part 5: Tags
---

Tags are the Minecraft implementation of Ore Dictionary that took over in 1.13. They are use in almost everything from recipes to what blocks can infinitely burn to even what an enderman can pick up. Tags are by far the easiest to implement with data generators only made a bit difficult by the fact that all the methods are obfuscated.

## <a name="tagsprovider"></a>TagsProvider <a href="#tagsprovider"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

`TagsProvider` is the base class that `BlockTagsProvider`, `ItemTagsProvider`, and `FluidTagsProvider` all extend to implement their current features. There are two main methods within the class that are extremely important to know about.

Method | Parameter | Return Type | Use
--- | :---: | :---: | ---
`getBuilder (func_240522_a_)` | `ITag.INamedTag<T>` tagIn (p\_240522\_1\_) | `TagsProvider.Builder<T>` | Gets the provider builder for a tag.
`getBuilder (func_240525_b_)` | `ITag.INamedTag<T>` tagIn (p\_240525\_1\_) | `ITag.Builder` | Gets the tag builder for a tag.

Since we are using the `TagProvider`, we will be using the first method more often than not. The builder also comes with its own list of methods that you can use.

Method | Parameter | Return Type | Use
--- | :---: | :---: | ---
`add (func_240532_a_)` | `T` objectIn (p\_240532\_1\_) | `TagsProvider.Builder<T>` | Adds an object to a tag.
`add (func_240531_a_)` | `ITag.INamedTag<T>` tagIn (p\_240531\_1\_) | `TagsProvider.Builder<T>` | Adds a tag to a tag.
`add (func_240534_a_)` | `T...` objectsIn (p\_240534\_1\_) | `TagsProvider.Builder<T>` | Adds these objects to a tag.
`add` | `ITag.ITagEntry` tag | `TagsProvider.Builder<T>` | Adds a tag entry to a tag.

> Note: `ItemTagsProvider` also has the method `ItemTagsProvider::copy (func_240521_a_)` which copies a block tag to an item tag. This is especially useful as blocks can be referenced as both blocks and items.

To be able to get a specific tag, they are either referenced through Minecraft's `BlockTags`, `ItemTags`, or `FluidTags` and Forge's `Tags`. You can add your own tags as well by creating your own tag class. More on that in a bit.

## <a name="application"></a>Application <a href="#application"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Let's first review our six objects: ruby, ruby ore, and ruby armor. Now, let's look up a list of tags these could all fall under and create a list.

```
ItemTags
└── BEACON_PAYMENT (field_232908_Z_)
	└── Ruby

Tags
├── Blocks
│	└── ORES
│		└── ORES_RUBY
└── Items
	├── BEACON_PAYMENT
	│	└── Ruby
	└── GEMS
		└── GEMS_RUBY

TutorialBlockTags
└── ORES_RUBY
	└── Ruby Ore

TutorialItemTags
├── GEMS_RUBY
│	└── Ruby
└── ORES_RUBY
	└── Ruby Ore
```

As you may have noticed, some of these tags don't exist yet since we haven't created them. Well let's do that now. We will create a simple class that implements both block and item tags. Each class will then hold a tag creation method that calls `TAG_CLASS::makeWrapperTag` with a prefix of our mod id. From here, we just create our specific tags: `ORES_RUBY` and `GEMS_RUBY`.

```java
public class TutorialTags {

	public static class Blocks {
		
		public static final ITag.INamedTag<Block> ORES_RUBY = tag("ores/ruby");
		
		private static ITag.INamedTag<Block> tag(String id) {
			return BlockTags.makeWrapperTag(Tutorial.ID + ":" + id);
		}
	}

	public static class Items {
		
		public static final ITag.INamedTag<Item> GEMS_RUBY = tag("gems/ruby");
		public static final ITag.INamedTag<Item> ORES_RUBY = tag("ores/ruby");
		
		private static ITag.INamedTag<Item> tag(String id) {
			return ItemTags.makeWrapperTag(Tutorial.ID + ":" + id);
		}
	}
}
```

Then we create our providers that extend `BlockTagsProvider` and `ItemTagsProvider` (we do not have any fluids yet) and override `TagsProvider::registerTags`. There is where we call the methods to implement the above structure we have created.

```java
public class TutorialBlockTagsProvider extends BlockTagsProvider {

	public TutorialBlockTagsProvider(DataGenerator generatorIn) {
		super(generatorIn);
	}

	@Override
	protected void registerTags() {
		func_240522_a_(Tags.Blocks.ORES).func_240531_a_(TutorialTags.Blocks.ORES_RUBY);
		
		func_240522_a_(TutorialTags.Blocks.ORES_RUBY).func_240534_a_(TutorialBlocks.RUBY_ORE.get());
	}
}
```

```java
public class TutorialItemTagsProvider extends ItemTagsProvider {

	public TutorialItemTagsProvider(DataGenerator generatorIn, BlockTagsProvider blockTagsProviderIn) {
		super(generatorIn, blockTagsProviderIn);
	}

	@Override
	protected void registerTags() {
		func_240521_a_(Tags.Blocks.ORES, Tags.Items.ORES);
		func_240521_a_(TutorialTags.Blocks.ORES_RUBY, TutorialTags.Items.ORES_RUBY);
		
		func_240522_a_(Tags.Items.GEMS).func_240531_a_(TutorialTags.Items.GEMS_RUBY);
		
		func_240522_a_(TutorialTags.Items.GEMS_RUBY).func_240534_a_(TutorialItems.RUBY.get());
		
		func_240522_a_(ItemTags.field_232908_Z_).func_240534_a_(TutorialItems.RUBY.get());
		func_240522_a_(Tags.Items.BEACON_PAYMENT).func_240534_a_(TutorialItems.RUBY.get());
	}
}
```

> Note: All block tags that are also item tags if referenced must be copied. This is true in all cases.

Now all we need to do is to register these providers with `GatherDataEvent`. Since `ItemTagsProvider` takes in a `BlockTagsProvider`, we will be declaring it as a variable.

```java
public class Tutorial {
	...
	private void gatherData(final GatherDataEvent event) {
		...
		if(event.includeServer()) {
			TutorialBlockTagsProvider block_tags = new TutorialBlockTagsProvider(gen);
			
			gen.addProvider(block_tags);
			gen.addProvider(new TutorialItemTagsProvider(gen, block_tags));
			...
		}
	}
	...
}
```

Now execute `runData`, copy `src/generated/resources` to `src/main/resources`, and run the game. Now your tags should be implemented.

## <a name="changes"></a>Changes <a href="#changes"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---
As of **32.0.62**, `Tags$Items::BEACON_PAYMENT` is no longer a field along with `IForgeItem::isBeaconPayment`. Minecraft already has a hook into this method. For that reason, we will remove it from our item tags provider.

```java
public class TutorialItemTagsProvider extends ItemTagsProvider {
	...
	@Override
	protected void registerTags() {
		func_240521_a_(Tags.Blocks.ORES, Tags.Items.ORES);
		func_240521_a_(TutorialTags.Blocks.ORES_RUBY, TutorialTags.Items.ORES_RUBY);
		
		func_240522_a_(Tags.Items.GEMS).func_240531_a_(TutorialTags.Items.GEMS_RUBY);
		
		func_240522_a_(TutorialTags.Items.GEMS_RUBY).func_240534_a_(TutorialItems.RUBY.get());
		
		func_240522_a_(ItemTags.field_232908_Z_).func_240534_a_(TutorialItems.RUBY.get());
		func_240522_a_(Tags.Items.BEACON_PAYMENT).func_240534_a_(TutorialItems.RUBY.get());
	}
}
```

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.61-web) under **Tags**.

All changes are uploaded to this [branch](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.70-web) under **Tags Port**.

Finish off this six part tutorial with [advancements](./advancements).

Back to [Loot Tables](./loot_tables)  
Back to [Data Generators](../../index#data-generators)  
Back to [Minecraft Tutorials](../../index)  