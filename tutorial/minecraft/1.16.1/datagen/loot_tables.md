# Data Generators Part 4: Loot Tables
---

Loot Tables are the voodoo magic of Minecraft. They make absolutely perfect sense 100% of the time and fail 99% of the time due to incorrect formatting or just logic issues within the system itself. However, with data generators, the system becomes a little less difficult as you understand the nuances that make the loot table system what it is today.

## <a name="application"></a>Application <a href="#application"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Unlike in other tutorials, I will not be showing off all the methods in each specific file. The reason is that `LootTable` is massive spanning over many files each having their own implementation. I do not have the time nor the patience to copy all those methods over. If you would like to go and review each method for yourself, take a look through `net.minecraft.loot`. Get acquainted with all the files if you can. It will be imperative as we create more and more custom tables.

**If you would like to help contribute on making a documentation for these classes, leave a PR on this webpage.**

Since our only existing object with a loot table is a `Block`, we will be creating a class that overrides `BlockLootTables`.

```java
public class TutorialBlockLootTables extends BlockLootTables {}
```

From there we can override two methods: `BlockLootTables::addTables` and `BlockLootTables::getKnownBlocks`. The first method allows us to add our own tables to the data provider. The second method does a validation check to make sure that all the blocks has a loot table of some kind (which it should).

```java
public class TutorialBlockLootTables extends BlockLootTables {
	
	@Override
	protected void addTables() {
		
	}
	
	@Override
	protected Iterable<Block> getKnownBlocks() {
		return super.getKnownBlocks();
	}
}
```

By default, `BlockLootTabltes::getKnownBlocks` iterates through the entire `Block` registry. Now, we could just filter out our specific blocks using a stream map. However, this is extremely bad for one reason: we have a registry that we know only holds our blocks. So, instead of referencing the actual registry, we can reference our `DeferredRegister` instead. We still need to map the values since they are `RegistryObjects`, but we can use the double colon operator with streams to easily accomplish this for us.

```java
public class TutorialBlockLootTables extends BlockLootTables {
	...
	@Override
	protected Iterable<Block> getKnownBlocks() {
		return TutorialBlocks.BLOCKS.getEntries().stream().map(RegistryObject::get)::iterator;
	}
}
```

> Note: Since we are only calling `BlockLootTables::getKnownBlocks` once, we don't need to define a variable that holds the above iterable to save on resources.

As for our extension of `LootTableProvider`, we need to override two more methods: `LootTableProvider::getTables` and `LootTableProvider::validate`. The first method allows us to provide our own loot tables. The second method uses a validation feature which is purely irrelevant for us since its checking for specific Minecraft loot tables.

```java
public class TutorialLootTableProvider extends LootTableProvider {

	public TutorialLootTableProvider(DataGenerator dataGeneratorIn) {
		super(dataGeneratorIn);
	}
	
	@Override
	protected List<Pair<Supplier<Consumer<BiConsumer<ResourceLocation, Builder>>>, LootParameterSet>> getTables() {
		return super.getTables();
	}
	
	@Override
	protected void validate(Map<ResourceLocation, LootTable> map, ValidationTracker validationtracker) {}
}
```

As for what we are going to return for `LootTableProvider::getTables`, we're gonna repurpose `LootTableProvider::field_218444_e` and modify it to take in our loot tables for a parameter set.

```java
public class TutorialLootTableProvider extends LootTableProvider {

	private final List<Pair<Supplier<Consumer<BiConsumer<ResourceLocation, LootTable.Builder>>>, LootParameterSet>> loot_tables = ImmutableList.of(Pair.of(TutorialBlockLootTables::new, LootParameterSets.BLOCK));
	...
	@Override
	protected List<Pair<Supplier<Consumer<BiConsumer<ResourceLocation, Builder>>>, LootParameterSet>> getTables() {
		return this.loot_tables;
	}
	...
}
```

Now for our ruby ore. I want the ore to drop a ruby when mined normally and drop itself when mined with silk touch. I would also want the fortune enchantment to play a role in what happens when the block is dropped normally. Good for us, `BlockLootTables` comes with a bunch of premade methods that make replicating some amount of Minecraft drops easy. In our case, `BlockLootTables::droppingItemWithFortune` best suits our needs. We can then register it using `BlockLootTables::registerLootTable`, add the provider to `GatherDataEvent`, and execute `runData`.

```java
public class TutorialBlockLootTables extends BlockLootTables {
	
	@Override
	protected void addTables() {
		this.registerLootTable(TutorialBlocks.RUBY_ORE.get(), (block) -> {
			return droppingItemWithFortune(block, TutorialItems.RUBY.get());
		});
	}
	...
}
```

```java
public class Tutorial {
	...
	private void gatherData(final GatherDataEvent event) {
		...
		if(event.includeServer()) {
			...
			gen.addProvider(new TutorialLootTableProvider(gen));
		}
	}
	...
}
```

After moving your files from `src/generated/resources` to `src/main/resources`, your loot tables should now be applied to your objects!

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.61-web) under **Loot Tables**.

Now that you finished the hardest tutorial of all the data generators, take a break with part 5 on [tags](./tags).

Back to [States and Models](./models)  
Back to [Data Generators](../../index#data-generators)  
Back to [Minecraft Tutorials](../../index)  