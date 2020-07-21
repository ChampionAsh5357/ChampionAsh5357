# Data Generators Part 6: Advancements
---

Advancements were the start of the upgraded Minecraft system beginning in 1.12. Data generators didn't exist as of yet since they were first implemented in 1.13. As 1.13 came about and so many recipes and advancements were written by hand, data generators were the efficient way of parsing information into files and outputting it. Now in 1.16, all JSON files can be parsed by data generators in some way whether through Forge or Minecraft. So, let's finish this tutorial off by talking about advancements.

## <a name="advancement-builder"></a>Advancement$Builder <a href="#advancement-builde"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

JSON serialization is actually handled by the advancement class and not through any special class. This is because the advancement system was already in place and stored in builders through JSON and `PacketBuffer`, so it was much easier to just add a serialization method. Everything within `Advancement$Builder` can be chained and is created once `Advancement$Builder::register` is called to create a new `Advancement`.

Method | Parameter(s) | Use
--- | :---: | ---
`builder` | NONE | Gets the default advancement builder.
`withParent` | `Advancement` parentIn | Sets the parent as another advancement.
`withParentId` | `ResourceLocation` parentIdIn | Sets the parent as an id to get the advancement from.
`withDisplay` | `ItemStack` stack<br>`ITextComponent` title<br>`ITextComponent` description<br>`@Nullable ResourceLocation` background<br>`FrameType` frame<br>`boolean` showToast<br>`boolean` announceToChat<br>`boolean` hidden | Sets the display information.
`withDisplay` | `IItemProvider` itemIn<br>`ITextComponent` title<br>`ITextComponent` description<br>`@Nullable ResourceLocation` background<br>`FrameType` frame<br>`boolean` showToast<br>`boolean` announceToChat<br>`boolean` hidden | Sets the display information.
`withDisplay` | `DisplayInfo` displayIn | Sets the display information.
`withRewards` | `AdvancementRewards.Builder` rewardsBuilder | Sets the rewards received from the advancement. Only supports experience and recipes.
`withRewards` | `AdvancementRewards` rewardsBuilder | Sets the rewards received from the advancement. Can also support loot tables and a cacheable function.
`withCriterion` | `String` key<br>`ICriterionInstance` criterionIn | Sets a criteria required to unlock this advancement.
`withCriterion` | `String` key<br>`Criterion` criterionIn | Sets a criteria required to unlock this advancement.
`withRequirementsStrategy` | `IRequirementsStrategy` strategy | Sets whether the criteria is ORed together or ANDed together.

### <a name="conditionaladvancement"></a>ConditionalAdvancement <a href="#conditionaladvancement"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

`ConditionalAdvancement` is made for the sole purpose of being implemented with [`ConditionalRecipe`](./recipes#conditionalrecipe) as its JSON call to write the file doesn't exist anywhere else. It can still be implemented as a standalone, it just takes a tiny bit more creativity to get it working.

Method | Parameter(s) | Use
--- | :---: | ---
`addCondition` | `ICondition` condition | Adds a condition to the next advancement added to the builder.
`addAdvancement` | `Consumer<Consumer<Advancement.Builder>>` callable | Adds an advancement with the previous conditions. Conditions are then cleared.
`addAdvancement` | `Advancement.Builder` recipe | Adds an advancement with the previous conditions. Conditions are then cleared. 

## <a name="application"></a>Application <a href="#application"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

In this case only, I will rewrite `AdvancementProvider` instead of extending it. The main reason is that the class is completely inaccessible to modders who want to append to vanilla advancements. Also, we would have arbritrary data stored that can't be overwritten in any way which is just pointless. The class was made with literally no implementation of extending it. So we have to rewrite the class to support modder implementation. Lucky for us, we can use the knowledge that `Advancement$Builder` holds the serialize method and work around this issue. I will implement a system similar to `IFinishedRecipe`.

```java
public class FinishedAdvancement {

	private final ResourceLocation id;
	private final Advancement.Builder builder;
	
	private FinishedAdvancement(ResourceLocation idIn, Advancement.Builder builderIn) {
		this.id = idIn;
		this.builder = builderIn;
	}

	public JsonObject serialize() {
		return this.builder.serialize();
	}
	
	public ResourceLocation getId() {
		return id;
	}
	
	public static class Builder {
		
		private Advancement.Builder builder;
		
		private Builder() {}
		
		public static FinishedAdvancement.Builder builder() {
			return new FinishedAdvancement.Builder();
		}
		
		public FinishedAdvancement.Builder advancement(Advancement.Builder builderIn) {
			this.builder = builderIn;
			return this;
		}
		
		public FinishedAdvancement build(Consumer<FinishedAdvancement> consumer, ResourceLocation id) {
			FinishedAdvancement advancement = new FinishedAdvancement(id, builder);
			consumer.accept(advancement);
			return advancement;
		}
	}
}
```

```java
public class TutorialAdvancementsProvider implements IDataProvider {
	private static final Logger LOGGER = LogManager.getLogger();
	private static final Gson GSON = (new GsonBuilder()).setPrettyPrinting().create();
	private final DataGenerator generator;
	private final List<Consumer<Consumer<FinishedAdvancement>>> advancements = ImmutableList.of();

	public TutorialAdvancementsProvider(DataGenerator generatorIn) {
		this.generator = generatorIn;
	}

	public void act(DirectoryCache cache) throws IOException {
		Path path = this.generator.getOutputFolder();
		Set<ResourceLocation> set = Sets.newHashSet();
		Consumer<FinishedAdvancement> consumer = (advancementIn) -> {
			if (!set.add(advancementIn.getId())) {
				throw new IllegalStateException("Duplicate advancement " + advancementIn.getId());
			} else {
				Path path1 = getPath(path, advancementIn);

				try {
					IDataProvider.save(GSON, cache, advancementIn.serialize(), path1);
				} catch (IOException ioexception) {
					LOGGER.error("Couldn't save advancement {}", path1, ioexception);
				}

			}
		};

		for(Consumer<Consumer<FinishedAdvancement>> consumer1 : this.advancements) {
			consumer1.accept(consumer);
		}

	}

	private static Path getPath(Path pathIn, FinishedAdvancement advancementIn) {
		return pathIn.resolve("data/" + advancementIn.getId().getNamespace() + "/advancements/" + advancementIn.getId().getPath() + ".json");
	}

	public String getName() {
		return "Advancements";
	}
}
```

We then need to create a class where it implements a `Consumer<Consumer<FinishedAdvancement>>`.  Since I'm going to be adding onto story advancements, I'm gonna add a similar name and then add it to our advancement list.

```java
public class TutorialStoryAdvancements implements Consumer<Consumer<FinishedAdvancement>> {

	@Override
	public void accept(Consumer<FinishedAdvancement> consumer) {}
}
```

```java
public class TutorialAdvancementsProvider implements IDataProvider {
	...
	private final List<Consumer<Consumer<FinishedAdvancement>>> advancements = ImmutableList.of(new TutorialStoryAdvancements());
	...
}
```

Since I want the advancement to mimic `mine_diamond`, I will use that as a template to create mine.

```java
public class TutorialStoryAdvancements implements Consumer<Consumer<FinishedAdvancement>> {

	@Override
	public void accept(Consumer<FinishedAdvancement> consumer) {
		FinishedAdvancement.Builder.builder().advancement(Advancement.Builder.builder().withParentId(new ResourceLocation("story/iron_tools")).withDisplay(TutorialItems.RUBY.get(), TextTranslations.ADVANCEMENT_MINE_RUBY_TITLE, TextTranslations.ADVANCEMENT_MINE_RUBY_DESCRIPTION, null, FrameType.TASK, true, true, false).withCriterion("ruby", InventoryChangeTrigger.Instance.forItems(TutorialItems.RUBY.get()))).build(consumer, new ResourceLocation(Tutorial.ID, "story/mine_ruby"));
	}
}
```

> Note: I implemented the `TranslationTextComponent` as static final variables so I can have them referenced in one place and called anywhere I want. We stil need to call them within our `LanguageProvider` to register the localization text.

Let's make sure to add the provider to `GatherDataEvent` as well.

```java
public class Tutorial {
	...
	private void gatherData(final GatherDataEvent event) {
		...
		if(event.includeServer()) {
			...
			gen.addProvider(new TutorialAdvancementsProvider(gen));
		}
	}
	...
}
```

After we provide the keys and execute `runData`, we just need to copy our files from `src/generated/resources` to `src/main/resources`.

And with that, you have now completed data generators!

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.61-web) under **Advancements**.

Back to [Tags](./tags)  
Back to [Data Generators](../../index#data-generators)  
Back to [Minecraft Tutorials](../../index)  