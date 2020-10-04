# Main File
---

Congrats on setting up your workspace! Welcome to part 2 of the introduction where we set up the basic mod file and folders for assets and data. You can delete any packages within `src/main/java` via the Project Explorer as we will be writing the code from scratch.

## <a name="java-class"></a>Java Class <a href="#java-class"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Let's start with the java class. Head over to the package explorer located by default on the left side of the screen. Create a new class within `src/main/java`. You should now see a screen where you can name the class and set the package. Usually, the package refers to the reverse DNS of the developer's website appended with the mod id. We will use `tutorial116x` as the mod id and the package as `io.github.championash5357.tutorial` in this example. As for the class name, you can use the name of the mod id (e.g. `Tutorial`) although it can literally be anything. Within eclipse, I will also check the box labeled `Constructors from superclass` as we will be using the constructor in our class.

You should now see something that looks something similar to:

```java
package io.github.championash5357.tutorial.common;

public class Tutorial {
    
	public Tutorial() {}
}
```

> Note: I tend to split my packages into client, common, server, and data. Each will hold code that's related to that specific side where common will hold anything that can exist in all three.

This is the default generated class for Eclipse. To be able to turn this class into our main mod file, we need to add a `@Mod` annotation to the top of the class. A `@Mod` annotation has a required string parameter for the mod id. So, you would need to put in parentheses the id as a string. However, for consistent use over and over, it is recommend you create a static final string variable for the id:

```java
@Mod(Tutorial.ID)
public static class Tutorial {

    public static final String ID = "tutorial116x";
	
	public Tutorial() {}
}
```

## <a name="resources"></a>Resources <a href="#resources"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

This is the barebones minimum java needed to run a mod. To be able to set up the information and actually be able to load the mod, we will need to do a few more things.

### <a name="mods-toml"></a>mods.toml <a href="#mods-toml"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Tom's Obvious, Minimal Language (or `TOML` for short) is a file format for easy reading and writing. In modding, it is our mod loading data that is handled by the game. There are mandatory and optional variables that can be set depending on the preferences of your mod. If you haven't deleted the file there by default, then anything you don't want to use can be commented out using a pound or hashtag (e.g. `var = value # This is commented out`).

If you also deleted everything in `src/main/resources`, create a new file and save it to `./META-INF` and call it **mods.toml**.

There are currently 13 variables and two dependencies that are written in default toml file of which nine are required. Let's start with the required variables:  

| Required Variables | Description |
| :---: | --- |
| **modLoader** | The name of the mod loader to use, should remain default for regular mods (`javafml` by default for the Forge Mod Loader). |
| **loaderVersion** | The version range of the mod loader to use (`[3x,)` by default for the Forge Mod Loader). |
| **license** | Stores the license used by the mod. It is required in the latest minor. |
| **[[mods]]** | Creates a list of fields to load for the mod. This is required above all the variables listed below. |
| **modId** | The modid for the mod (e.g. `examplemod` or `tutorial116x` in our case).  | 
| **version** | The version of the mod (e.g. `${file.jarVersion}` by default). If you want to use the data stored in [build.gradle](./getting_started#build-gradle), keep it default. This will only work once the mod is built into its jar form and is reliant on what the jar implemenation version is. |
| **displayName** | Display name for the mod (e.g. `Example Mod` or `Tutorials for 1.16.x` in our case). |
| **description** | Description text for the mod written in multi-line format (e.g. `This is our tutorial for programming Minecraft!`). Multi-line format is specified by three apostrophes (`'''`) at the start and end of the text. This can still be replaced with the standard quotation marks for single line format. |


> Note: Text formatting only works in single line format for the values in toml. Also, the file must be formatted in LF (Line Feed) and not CRLF (Carriage Return Line Feed) if there is any new lines within your strings.

Now let's move on to the optional variables. These must all be defined underneath `[[mods]]`:  

| Optional Variables | Description |
| :---: | --- |
| **issueTrackerURL** | A URL to the issue tracker of this mod (e.g. `https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/issues`). |
| **updateJSONURL** | A URL to query for updates to this mod (e.g. `https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/update_checker`). |
| **displayURL** | A URL for the mod's homepage (e.g. `https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial`). |
| **logoFile** | File name of the logo picture in src/main/resources (e.g. `logo.png`). |
| **credits** | Text field for giving credit (e.g. `Everyone who has supported me over the years!`). |
| **authors** | Text field for the author(s) of the mod (e.g. `ChampionAsh5357`). |

It is recommended you have all the variables in the file with the unused ones being commented out just for future purpose.

The final part is the dependencies which are formatted like so:

```toml
[[dependencies.examplemod]]
    # the modid of the dependency
    modId="forge" #mandatory
    # Does this dependency have to exist - if not, ordering below must be specified
    mandatory=true #mandatory
    # The version range of the dependency
    versionRange="[3x,)" #mandatory
    # An ordering relationship for the dependency - BEFORE or AFTER required if the relationship is not mandatory
    ordering="NONE"
    # Side this dependency is applied on - BOTH, CLIENT or SERVER
    side="BOTH"
    
[[dependencies.examplemod]]
    modId="minecraft"
    mandatory=true
    versionRange="[1.16.x]"
    ordering="NONE"
    side="BOTH"
```

| Variables | Description |
| --- | --- |
| modId | The mod id of the dependency.
| mandatory | If the dependency is required for this mod to run.
| versionRange | The valid versions this dependency will run on.
| ordering | This can be `NONE`, `BEFORE`, or `AFTER`. This specifies when your mod will be constructed compared to this one.
| side | This can be the `CLIENT`, `SERVER`, or `BOTH`. This specifies which [physical side](#sided-references) your mod needs to be present on.

These were both taken directly from the default toml file. Obviously, `examplemod` is replaced by your own mod id when you set the dependencies (in our case `tutorial116x`).

> Note: This does not review all possible variables you can have in your toml file. Those can be found by looking through the source on the [Minecraft Forge Github](https://github.com/MinecraftForge/MinecraftForge) yourself.

### <a name="pack-mcmeta"></a>pack.mceta <a href="#pack-mcmeta"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

This MCMeta file is basically the configuration file for the assets used in your mod. It allows the game to know that your mod has assets to attach to it. Although it has a different file extension, it is written in the `JSON` format. All you need to do is create an object `pack` with a `description` and a `pack_format` of 6:

```json
{
    "pack": {
        "description": "examplemod resources",
        "pack_format": 6
    }
}
```

This is to be saved as **pack.mcmeta** in `src/main/resources`.

After doing this, your main mod setup should be complete.

## <a name="additional-setup"></a>Additional Setup <a href="#additional-setup"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

To prepare for the next tutorials, we're going to do some pre-setup of the main mod file.

### <a name="ieventbus"></a>IEventBus <a href="#ieventbus"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

`IEventBus` is an interface where we register specific events handled by Forge to. These are used to dispatch all mods that have a listener attached to the specific event. There are two main event buses in the game: our mod's event bus and Forge's event bus. The mod event bus is used for listening to lifecycle events (where mods initialize). The Forge event bus handles all the intercepts of vanilla code within the game. 

There are a few ways to subscribe a method to an event bus. One way is to go into a constructor (most likely on your main mod class) and call `IEventBus::addListener` which should take in a method that has a single parameter of an object extending `Event`. It would look something like this:

```java
...
modEventBus.addListener(this::method);
...
```

These methods should be private, non-static, and return nothing since they are specific to the class accessed.

Another method is to annotate the top of a class with `@Mod$EventBusSubscriber`. You will need to pass in your mod id as well as the event bus you are currently working on. From there, you would annotate a public static method (once again having a single parameter extending `Event`) with `@SubscribeEvent`. This would look something like:

```java
@Mod.EventBusSubscriber(modid = Tutorial.ID, bus = Bus.MOD)
public class CommonEvents {

	@SubscribeEvent
	public static void newEventCall(final Event event) {
		//Do code
	}
}
```

We will be using the first method throughout the tutorials, but feel free to use the second if you are more comfortable with it. Just remember to stick with one style of formatting and remain consistent.

You can find a list of all possible event handlers at the [Common Issues and Recommendations](https://championash5357.github.io/ChampionAsh5357/tutorial/minecraft/ciar#code-style-4) thread.

If you want a more detailed explanation, you can check out the [Forge documentation](https://mcforge.readthedocs.io/en/latest/events/intro/).

### <a name="sided-references"></a>Sided References <a href="#sided-references"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

One of the most important things about Minecraft is the fact that there are two sides: the `client` and the `server`. There is a bit of ambiguity between the terms, so I will try to make this as clear as I possibly can:  
**Physical Client** - This refers to accessing `Minecraft` on your computer. Everything that runs are your computer when you open the game refers to this specific client.  
**Physical Server** - This refers to accessing the `Minecraft Server` file whenever you are setting up a server. Everything that runs on this server is specific to the server.  
**Logical Server** - This refers to how game logic is handled on the `Server Thread`. This can run on both the `Physical Client (IntegratedServer)` and `Physical Server (DedicatedServer)`.  
**Logical Client** - This refers to how the game renders on a screen and accepts user input to relay to the `Logical Server` from the `Client Thread`. This can only exist on the `Physical Client`.  

If you would like more information, check out the [Forge documentation](https://mcforge.readthedocs.io/en/latest/concepts/sides/) on it once again.

So, if you haven't already guessed, we can't really get information on the logical client from the logical server or vice versa without the use of packets. Not only that, if we tried to, our mod might crash on the physical server and would cause a whole number of other issues within the code. So, we need to separate our code such that logical client information is never called on the physical server.

Enter, `DistExecutor`. What `DistExecutor` allows us to do is run or call certain methods exclusively on either the physical client or physical server. This prevents reaching across sides and lets our code run smoothly on both sides.

To do this, we are going to setup an interface to store the reference to. I will name the interface `ISidedReference`. You should see something like this:

```java
public interface ISidedReference {

}
```

Within here, we are going to add a new method called `setup` that will take two `IEventBus` parameters like so:

```java
public interface ISidedReference {
	
	void setup(final IEventBus mod, final IEventBus forge);
}
```

These two parameters refer to our mod's event bus and Forge's event bus. We take in two parameters as we will have these variables already initialized in our main mod class to save a bit of resources from calling the method redundantly.

Once we have done that, we are going to create two new classes:  
**ClientReference** - This will be stored within the package `.client`  
**DedicatedServerReference** - This will be stored within the package `.server.dedicated`  

We want to keep logical client specific and logical server specific code separate from the common code. So, subclassing them in packages is the easiest way to keep track of them.

For Eclipse, if you want to add your interface via the creation menu, click `Add...` and type in `ISidedReference`. Double-click the entry, click `Ok`, make sure `Inherited abstract methods` is selected, and then click `Finish`.

This will generate the following two files:

```java
public class ClientReference implements ISidedReference {

	@Override
	public void setup(final IEventBus mod, final IEventBus forge) {}
}
```

```java
public class DedicatedServerReference implements ISidedReference {

	@Override
	public void setup(final IEventBus mod, final IEventBus forge) {}
}
```

From here, we have to be able to call `setup` for our proxies. But how do we necessarily call each method such that they only exist on a specific physical side?

Once again, go into your main mod file and create a static final variable with an `ISidedReference` type. We use `ISidedReference` instead of the specific class name because we want to be able to access either class from this specific variable. We will set this equal to `DistExecutor::safeRunForDist` which takes in a [supplier](https://www.geeksforgeeks.org/supplier-interface-in-java-with-examples/) holding a new `ISidedReference` instance:

```java
...
public class Tutorial {
	...
	public static final ISidedReference SIDED_SYSTEM = DistExecutor.safeRunForDist(() -> ClientReference::new, () -> DedicatedServerReference::new);
	
	public Tutorial() {}
}
```

From here we can create two variables within our constructor that holds `FMLJavaModLoadingContext#getModEventBus` for our mod's event bus and `MinecraftForge#EVENT_BUS` for Forge's event bus and pass them into our method holder:

```java
...
public class Tutorial {
	...
	public static final ISidedReference SIDED_SYSTEM = DistExecutor.safeRunForDist(() -> ClientReference::new, () -> DedicatedServerReference::new);
	
	public Tutorial() {
		final IEventBus mod = FMLJavaModLoadingContext.get().getModEventBus(),
				forge = MinecraftForge.EVENT_BUS;
		
		SIDED_SYSTEM.setup(modEventBus, forgeEventBus);
	}
}
```

### <a name="lifecycle-events"></a>Lifecycle Events<a href="#lifecycle-events"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

As metioned previously in [IEventBus](#ieventbus), the lifecycle events are what is used to initialize information specific to the mod. This is run parallelly so that multiple mods can be loaded at once.

> Note: There is one more lifecycle that runs synchronously for creating and loading registries; however, these tutorials will use Forge references that call them internally, so we will not go over them.

Let's quickly go over the seven main events:  

Lifecycle Events | Description
--- | ---
**FMLConstructModEvent** | Called when mod is first initialized.  
**FMLCommonSetupEvent** | Basic initialization of methods on both the physical client and physical server.  
**FMLClientSetupEvent** | Basic initialization of methods on both the physical client.  
**FMLDedicatedServerSetupEvent** | Basic initialization of methods on both the physical server.  
**InterModEnqueueEvent** | Send messages to other mods loaded.   
**InterModProcessEvent** | Receives messages from other mods loaded.  
**FMLLoadCompleteEvent** | Called when all mods should be fully initialized.  

In our case, we will only be setting up two since the rest are irrelevant at the current moment: `FMLCommonSetupEvent` and `FMLClientSetupEvent`. We will setup the methods as mentioned above in our main mod file for the common event and our `ClientReference` for the client event:

```java
public class Tutorial {
	...
	public Tutorial() {
		...
		modEventBus.addListener(this::aetup);
	}
	
	private void setup(final FMLCommonSetupEvent event) {}
}
```

```java
public class ClientReference implements ISidedReference {

	@Override
	public void setup(IEventBus modEventBus, IEventBus forgeEventBus) {
		modEventBus.addListener(this::clientSetup);
	}
	
	private void clientSetup(final FMLClientSetupEvent event) {}
}
```

### <a name="linefeed-settings"></a>Linefeed Settings<a href="#linefeed-settings"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

If you plan on uploading your mod to a repository, you should make sure that at least the `mods.toml` file is saved and pulled in a LF end-of-line (EOL). To do this, simply add the following line to your `.gitattributes` file:

```git
src/main/resources/META-INF/mods.toml text eol=lf
```

This tells the github to store the file using LF EOL instead of your system's EOL settings.

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial) under **Main File**.

Thanks to `JTK222` for recommending that the system should be renamed. Proxies have been discouraged since 1.7.10.

<!-- Before we get started with programming something into the game, let's talk about [registries](./registries). -->

Back to [Getting Started](./getting_started)  
Back to [Minecraft Tutorials](../../index)  