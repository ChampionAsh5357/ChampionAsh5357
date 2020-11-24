# Introduction to Data Generators
---

Writing any JSON file by hand is tedious work. Whether it's creating a language file, a loot table, or a block state, it takes a copious amount of effort to just create and copy them over and over. It would be much better if it could be automated and added to our current assets and data rather than writing it out by hand.

Enter `DataGenerator`. Data Generators is a program within Minecraft that is used to automate writing JSON files. By attaching `DataProvider`s, this allows them to just copy and paste a single method with a few changes to get some their assets generated. There are specific cases that cannot be handled such as custom models. However, the process to implement them can be added to a data generator.

This will be an explanation on how to create and use different `DataGenerator`s. Forge has a great [example](https://github.com/MinecraftForge/MinecraftForge/blob/1.15.x/src/test/java/net/minecraftforge/debug/DataGeneratorTest.java) that shows how to utilize these generators to their fullest extent.

## <a name="basic-setup"></a>Basic Setup <a href="#basic-setup"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

To be able to run a `DataGenerator`, only one change is necessary. If you have been following this series from the beginning, then you already are done setting up in the [build.gradle](../intro/getting_started#build-gradle). For those who haven't, we will focusing on this section within `build.gradle`:

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

            args '--mod', 'tutorial116x', '--all', '--output', file('src/generated/resources/')

            mods {
                tutorial116x {
                    source sourceSets.main
                }
            }
        }
    }
}
...
```

To be able to run a data generator for our mod, we need to adjust the arguments such that it recognizes our mod's existence. This can be done by changing the field after '--mod' to your mod id. Our generator will always output to `src/generated/resources`.

> Note: With the above settings, you will not be able to use pre-existing files in your mod within the generators. You will also not be able to view your generated files without copying them over to `src/main/resources`. Please view the below section for that information.

## <a name="existingfilehelper-and-output-setup"></a>`ExistingFileHelper` and Output Setup <a href="#existingfilehelper-and-output-setup"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

By default, all generated files will not be compiled into the resources and no file will not be recognized by the generators except vanilla. To work around this, Forge added a helper class called `ExistingFileHelper` that's used to specify where to grab files from. To add an existing file directory, you will need to append an `--existing` parameter to the end. Afterwards, you can follow this up with the location you want to read your pre-existing files from. For example, if I wanted to read the files in `src/main/resources`, this will change our `build.gradle` to look like so:

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

            args '--mod', 'tutorial116x', '--all', '--output', file('src/generated/resources/'), '--existing', file('src/main/resources/')

            mods {
                tutorial116x {
                    source sourceSets.main
                }
            }
        }
    }
}
...
```

What about generated files? Well since we want them to be compiled into resources along with being in the resources, we can instead add them to our main resources directory like so:

```
sourceSets {
    main.resources.srcDirs += 'src/generated/resources'
}
```

> Note: This should be in a standalone block, not nested in some other one.

Forge assets are included by default as of 35.1.3. To add other mod resources, you can append `--existing-mod <modid>` to the end of your data block arguments.

If you would like a more detailed explanation, I did a documentation writeup over at the [MMD Modding Resources](https://github.com/MinecraftModDevelopment/Modding-Resources/blob/master/pages/existingfilehelper.md) GitHub. I would highly recommend reading this before progressing any further in data generators.

All that's left to do is to refresh our gradle dependencies and regen our runs.

## <a name="class-setup"></a>Class Setup <a href="#class-setup"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

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

`GatherDataEvent` holds four important booleans that check whether the generator should run client JSONs, server JSONs, developement data, and dump reports. We will be specifying which providers are associated with which boolean as these tutorials goes on.

> Note: I make the data generator a parameter since there will be multiple calls to it, so it saves some resources.

Now all we have to do is execute `runData` via our editor or the command line and it should output our generated data to `src/generated/resources`.

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/) under **Introduction to Data Generators**.

<!--Now that you have the basics set up, it's time to move on to our first generation: [recipes](./recipes).-->

Back to [Minecraft Tutorials](../../index)  