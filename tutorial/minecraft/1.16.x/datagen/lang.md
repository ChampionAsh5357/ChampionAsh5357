# Data Generators Part 1: Language Localization
---

Language localization is one of the most important topics when it comes to programming any mod. All strings that can show up in game should be specified by a translation key and then localized within a JSON using whatever locale code specific to the area. This allows all strings to be converted to different languages. Otherwise, all strings would default to the language of origin which is problematic in some cases (e.g. a mod made in English not translating to Spanish). This data provider created by Forge allows you to generate the names of all these translation keys.

> Note: For each language localization you would like to create, you have to create a new `LanguageProvider`. Of course, you could also just specify the locale code from the instance and use if-then-else or switch statements to generate accordingly.

## <a name="languageprovider"></a>LanguageProvider <a href="#languageprovider"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

All providers that would like to add JSON files should be specified by overriding a specific class in most cases. In this case, we we be overriding the Forge provided `LanguageProvider` to create these files. So, first let's create the provider class and register it to `GatherDataEvent`.

```java
public class Localization extends LanguageProvider {

	public Localization(DataGenerator gen, String modid, String locale) {
		super(gen, modid, locale);
	}

	@Override
	protected void addTranslations() {}
}
```

Looking at the three parameters, we have the `DataGenerator` used to create and output the files, the mod id, and the locale code. We cannot specify the generator as that is passed in through the event. The locale code we could specify, but it's much more efficient not to specify it. We can create a statement to generate locale codes based on the string inputted. So, we can specify the mod id of the provider internally like so:

```java
public class Localization extends LanguageProvider {

	public Localization(DataGenerator gen, String locale) {
		super(gen, Tutorial.ID, locale);
	}

	@Override
	protected void addTranslations() {}
}
```

We can then register the provider via `DataGenerator#addProvider` within our [`GatherDataEvent`](./intro#class-setup) we created last time. The locale code for American English is `en_us`, so we will set that as our locale parameter:

```java
public class Tutorial {
	...
	private void gatherData(final GatherDataEvent event) {
		...
		if(event.includeClient()) {
			gen.addProvider(new Localization(gen, "en_us"));
		}
		...
	}
}

```

> Note: Language JSONs are client specific so we check if we are to include client generations.

Now let's go into some methods. Most of them have a `Supplier` parameter holding your object due to `RegistryObject` references being used. However, there are non-supplier methods for those who choose the `RegistryEvent` way. In our case, we should be using the `Supplier` methods.

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
`addBiome` | `Supplier<? extends Biome>` key<br>`String` name |  Gives the name of our specified biome. **THIS DOES NOT EXIST PAST 1.16.1**
`add` | `Biome` key<br>`String` name |  Gives the name of our specified biome. **THIS DOES NOT EXIST PAST 1.16.1**
`addEffect` | `Supplier<? extends Effect>` key<br>`String` name |  Gives the name of our specified effect.
`add` | `Effect` key<br>`String` name |  Gives the name of our specified effect.
`addEntityType` | `Supplier<? extends EntityType<?>>` key<br>`String` name |  Gives the name of our specified entity type.
`add` | `EntityType<?>` key<br>`String` name |  Gives the name of our specified entity type.
`add` | `String` key<br>`String` name |  Gives the name of our specified translation key.

## <a name="application"></a>Application <a href="#application"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Although I do speak English, I also have a bit of background in Spanish and French as well. So, I will show a way to add multiple providers for locale codes.

So, I will set up three locale codes (`en_us`, `es_es`, `fr_fr`). Since we are using a non-boolean attribute and we plan on expanding more in the future, we will use a switch statement within `LanguageProvider#addTranslations`. Remember how we left the `locale` parameter as an input? Well, we can switch on that statement now to store all of our files within the same location. As the field itself is private, we can switch off of `IDataProvider#getName` provided we parse the string correctly.

> Note: A switch statement provides about the same speed as an if/else statement if the number of elements iterating through is 5 or less. Any more and the hash list implementation of the switch statement is much better to use.

```java
public class Localization extends LanguageProvider {
	...
	@Override
	protected void addTranslations() {
		String locale = this.getName().replace("Languages: ", "");
		switch(locale) {
		case "en_us":
			break;
		case "es_es":
			break;
		case "fr_fr":
			break;
		default:
			break;
		}
	}
}
```

> Note: Forge provides unicode translation support so characters can be entered as they appear.

From there, we can create a loop of some kind and add a provider for each locale code we would like to support. In my case, I will use a stream to do this for me. However, any method will work.

```java
public class Tutorial {
	...	
	private void gatherData(final GatherDataEvent event) {
		...
		if(event.includeClient()) {
			Stream.of("en_us", "es_es", "fr_fr").forEach(locale -> gen.addProvider(new Localization(gen, locale)));
		}
		...
	}
}
```

Now all you have to do is rerun your datagens and you should have your translations within the game. We will be adding an example once we start creating our registry objects. This is all still setup for the main attraction.

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/) under **Localizations**.

Now that you've got a handle on localization, take a look at part 2 on [states and models](./models).

Back to [Data Generators](../../index#data-generators)  
Back to [Minecraft Tutorials](../../index)  