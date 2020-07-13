# <a name="lang"></a>Data Generators Part 2: Language Localization
---

Language localization is a topic we reviewed in a [previous section](../basic/items#language-localization). As a quick recap, all strings that can show up in game should be specified by a translation key and then localized using whatever locale code specific to your area. This allows all your strings to be converted to different languages. This data provider created by Forge allows you to generate the names of all these translation keys.

> Note: For each language localization you would like to create, you have to create a new `LanguageProvider`. Of course, you could also just specify the locale code from the instance and use if/else or switch statements to generate accordingly.

## <a name="recipeprovider"></a>LanguageProvider
---

So, first let's create the provider class and register it to `GatherDataEvent`.

```java
public class TutorialLanguageProvider extends LanguageProvider {

	public TutorialLanguageProvider(DataGenerator gen, String modid, String locale) {
		super(gen, modid, locale);
	}

	@Override
	protected void addTranslations() {}
}
```

In this case, we can specify the modid of the Language provider internally like so:

```java
public class TutorialLanguageProvider extends LanguageProvider {

	public TutorialLanguageProvider(DataGenerator gen, String locale) {
		super(gen, Tutorial.ID, locale);
	}

	@Override
	protected void addTranslations() {}
}
```

> Note: I do not specify the locale code to make it more noticable in our main mode file which locale we are currently using.

We can then register it like any other provider:

```java
public class Tutorial {
	...
	private void gatherData(final GatherDataEvent event) {
		...
		if(event.includeClient()) {
			gen.addProvider(new TutorialLanguageProvider(gen, "en_us"));
		}
		...
	}
}

```

> Note: Language JSONs are client specific so we check if we are to include client generations.

Now let's go into some methods. Most of the ones to use have a `Supplier` parameter holding your object. Remember that your `RegistryObject` is a supplier, so you should be using these methods.

Method | Parameters | Use
--- | :---: | ---
`addBlock` | `Supplier<? extends Block>` key<br>`String` name | Gives the name of our specified block.
`add` | `Block` key<br>`String` name |  Gives the name of our specified block.
`addItem` | `Supplier<? extends Item>` key<br>`String` name |  Gives the name of our specified item.
`add` | `Item` key<br>`String` name |  Gives the name of our specified item.
`addItemStack` | `Supplier<ItemStack>` key<br>`String` name |  Gives the name of our specified item with NBT.
`add` | `ItemStack` key<br>`String` name |  Gives the name of our specified item with NBT.
`addEnchantment` | `Supplier<? extends Enchantment>` key<br>`String` name |  Gives the name of our specified enchantment.
`add` | `Enchantment` key<br>`String` name |  Gives the name of our specified enchantment.
`addBiome` | `Supplier<? extends Biome>` key<br>`String` name |  Gives the name of our specified biome.
`add` | `Biome` key<br>`String` name |  Gives the name of our specified biome.
`addEffect` | `Supplier<? extends Effect>` key<br>`String` name |  Gives the name of our specified effect.
`add` | `Effect` key<br>`String` name |  Gives the name of our specified effect.
`addEntityType` | `Supplier<? extends EntityType<?>>` key<br>`String` name |  Gives the name of our specified entity type.
`add` | `EntityType<?>` key<br>`String` name |  Gives the name of our specified entity type.
`add` | `String` key<br>`String` name |  Gives the name of our specified translation key.

## <a name="application"></a>Application
---

Although I do speak English, I also have a bit of background in Spanish and French as well. So, I will show a way to add multiple providers for locale codes.

Let's first start by translating our six objects in the game. Since, I am using three locale codes (`en_us`, `es_es`, `fr_fr`), we should implement a if/else or switch statement to be used. In our case, since we are using a non-boolean attribute and we plan on expanding more in the future, we will use a switch statement within `RecipeProvider::addTranslations`.

> Note: A switch statement provides about the same speed as an if/else statement if the number of elements iterating through is 5 or less. Any more and the hash list implementation of the switch statement is much better to use.

```java
public class TutorialLanguageProvider extends LanguageProvider {
	...
	@Override
	protected void addTranslations() {
		String locale = this.getName().replace("Languages: ", "");
		switch(locale) {
		case "en_us":
			addBlock(TutorialBlocks.RUBY_ORE, "Ruby Ore");
			addItem(TutorialItems.RUBY, "Ruby");
			addItem(TutorialItems.RUBY_HELMET, "Ruby Helmet");
			addItem(TutorialItems.RUBY_CHESTPLATE, "Ruby Chestplate");
			addItem(TutorialItems.RUBY_LEGGINGS, "Ruby Leggings");
			addItem(TutorialItems.RUBY_BOOTS, "Ruby Boots");
			break;
		case "es_es":
			addBlock(TutorialBlocks.RUBY_ORE, "Mena de rubí");
			addItem(TutorialItems.RUBY, "Rubí");
			addItem(TutorialItems.RUBY_HELMET, "Casco de rubí");
			addItem(TutorialItems.RUBY_CHESTPLATE, "Peto de rubí");
			addItem(TutorialItems.RUBY_LEGGINGS, "Grebas de rubí");
			addItem(TutorialItems.RUBY_BOOTS, "Botas de rubí");
			break;
		case "fr_fr":
			addBlock(TutorialBlocks.RUBY_ORE, "Minerai de rubis");
			addItem(TutorialItems.RUBY, "Rubis");
			addItem(TutorialItems.RUBY_HELMET, "Casque de rubis");
			addItem(TutorialItems.RUBY_CHESTPLATE, "Plastron de rubis");
			addItem(TutorialItems.RUBY_LEGGINGS, "Jambières de rubis");
			addItem(TutorialItems.RUBY_BOOTS, "Bottes de rubis");
			break;
		default:
			break;
		}
	}
}
```

> Note: Forge provides unicode translation support so characters can be entered as they appear.

From there, we can create a static final array holding the locale codes supported and loop through them to add providers for each one.

```java
public class Tutorial {
	...
	private static final String[] LOCALE_CODES = new String[] {
			"en_us",
			"es_es",
			"fr_fr"
	};
	...	
	private void gatherData(final GatherDataEvent event) {
		...
		if(event.includeClient()) {
			addLanguageProviders(gen);
		}
		...
	}
	
	private void addLanguageProviders(DataGenerator gen) {
		for(String locale : LOCALE_CODES) {
			gen.addProvider(new TutorialLanguageProvider(gen, locale));
		}
	}
}
```

Copy your `src/generated/resources` to `src/main/resources`, and now your language localizations exist in the game!

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.61-web) under **LanguageProvider**.

Now that you've got a handle on localization, take a look at part 3 on [states and models](./models).

Back to [Recipes](./recipes)  
Back to [Data Generators](../../index#data-generators)  
Back to [Minecraft Tutorials](../../index)  