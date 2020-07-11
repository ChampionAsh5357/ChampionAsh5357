# <a name="introduction-to-data-generators"></a>Introduction to Data Generators
---

Writing any JSON file by hand is tedious work. Whether it's creating a language file, a loot table, or a block state, it takes a copious amount of effort to just create and copy them over and over. It would be much better if it could be automated and added to our current assets and data rather than writing it out by hand.

Enter `DataGenerator`. Data Generators are programs within Minecraft that are used to automate writing JSON files for their code. This allows them to just copy and paste a single method with a few changes to get some their assets generated. There are specific cases that cannot be handled such as custom models. However, the process to implement them can be added to a data generator.

This will be a six part explanation with each part explaining how to create and use a different `DataGenerator`. Forge has a great [example](https://github.com/MinecraftForge/MinecraftForge/blob/1.15.x/src/test/java/net/minecraftforge/debug/DataGeneratorTest.java) that shows how to utilize these generators to their fullest extent.

## <a name="baic-setup"></a>Basic Setup

To be able to run a `DataGenerator`, two things must be setup. If you have been following this series from the beginning, then you already have completed step 1 when setting up [build.gradle](../introduction/getting_started#build-gradle). For those who haven't, we will focusing on this section within `build.gradle`:

```gradle
...
minecraft {
    ...
    runs {
        ...
        data {
            workingDirectory project.file('run')

            // Recommended logging data for a userdev environment
            property 'forge.logging.markers', 'SCAN,REGISTRIES,REGISTRYDUMP'

            // Recommended logging level for the console
            property 'forge.logging.console.level', 'debug'

            args '--mod', 'tutorial', '--all', '--output', file('src/generated/resources/')

            mods {
                tutorial {
                    source sourceSets.main
                }
            }
        }
    }
}
...
```

To allow our data to generate, we want to set it so that it replaces all existing files and pushes it to a isolated location. This allows us to overwrite and create all our files at will without worry of replacing custom textures or models. To replace existing files, we are going to append an `--existing` parameter to the end. To also specify where this will supposed to go, we will also append `file('src/main/resources/')` directly afterwards. This will change our `build.gradle` to now look like this:

```gradle
...
minecraft {
    ...
    runs {
        ...
        data {
            workingDirectory project.file('run')

            // Recommended logging data for a userdev environment
            property 'forge.logging.markers', 'SCAN,REGISTRIES,REGISTRYDUMP'

            // Recommended logging level for the console
            property 'forge.logging.console.level', 'debug'

            args '--mod', 'tutorial', '--all', '--output', file('src/generated/resources/'), '--existing', file('src/main/resources/')

            mods {
                tutorial {
                    source sourceSets.main
                }
            }
        }
    }
}
...
```

All that's left to do is to rerun our gradle setup commands, specifically `gradlew eclipse` or within the IDE itself.

To be able to register our `DataGenerator`s, we need to also subscribe to `GatherDataEvent` on our mod event bus like so:

```java
public class Tutorial {
	...
	public Tutorial() {
		...
		modEventBus.addListener(this::gatherData);
	}
	...
	private void gatherData(final GatherDataEvent event) {
		DataGenerator gen = event.getGenerator();
	}
}
```

> Note: I make the data generator a parameter since there will be multiple calls to it, so it saves some resources.

Now all we have to do is execute `runData` via our editor or the command line and it should output our generated data to `src/generated/resources`. From there, we can just copy it to `src/main/resources`.

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.61-web) under **Introduction to Data Generators**.

Now that you have the basics set up, it's time to move on to our first generation: [recipes](./recipes).

Back to [Minecraft Tutorials](../../index)  