# Main Mod File
---

Congrats on setting up your workspace! Welcome to part 2 of the introduction where we set up the basic mod file and folders for assets and data. You can delete any packages within `src/main/java` via the Project Explorer as we will be writing the code from scratch.

## Java Class
---

Let's start with the java class. Head over to the package explorer located by default on the left side of the screen. Right click `src/main/java` and go to `New`->`Class`. You should now see a screen where you can name the class and set the package. Usually, the package refers to the developer's website in reverse with an appended mod id, the same as we did for the `group` tag in [`build.gradle`](./getting_started#build.gradle). We will use `tutorial` as the mod id and the package as `io.github.championash5357.tutorial` in this example. As for the class name, you can use the name of the mod id (e.g. `Tutorial`). I will also check the box labeled `Constructors from superclass` as we will be using the constructor in our class. After setting these parameters, click `Finish` to create the class.

You should now see something that looks like:

```java
package domain.website.tutorial;

public class Tutorial {
    
	public Tutorial() {}
}
```

This is the default generated class for Eclipse. To be able to turn this class into our main mod file, we need to add an `@Mod` annotation to the top of the class. A `@Mod` annotation has a required string parameter for the mod id. So, you would need to put in paraentheses the id (e.g. `@Mod("tutorial")`). However, for consistent use over and over, it is recommend you create a public static final string variable for the id:

```java
@Mod(Tutorial.ID)
public static class Tutorial {

    public static final String ID = "tutorial";
	
	public Tutorial() {}
}
```

## Resources
---

This is the barebones minimum needed to run a mod. To be able to set up the information, we will need to do a few more things.

### mods.toml

Tom's Obvious, Minimal Language (or `TOML` for short) is a file format for easy reading and writing. In modding, it is our mod loading data that is handled by the game. There are mandatory and optional variables that can be set depending on the preferences of your mod. If you haven't deleted the file there by default, then anything you don't want to use can be commented out using a pound or hashtag (e.g. `var = value # This is commented out`).

If you also deleted everything in `src/main/resources`, right click that folder, go to `New`->`Other...`->`General`->`Untitled Text File`->`Finish`. Type something in the file and save it to `src/main/resources/META-INF` and call it **mods.toml**.

There are currently 13 variables and two dependencies that are written in default toml file of which nine are required. Let's start with the required variables:  

| Required Variables | Description |
| --- | --- |
| **modLoader** | The name of the mod loader to use, should remain default for regular mods (`javafml` by default). |
| **loaderVersion** | The version range of the mod loader to use, also known as the Forge version (`[32,)` by default). |
| **modId** | The modid for the mod (e.g. `examplemod` or `tutorial` in our case).  | 
| **version** | The version of the mod (e.g. `${file.jarVersion}` by default). If you want to use the data stored in [build.gradle](./getting_started#build.gradle), keep it default. This will only work once the mod is built into its jar form. |
| **displayName** | Display name for the mod (e.g. `Example Mod` or `Tutorial`in our case). |
| **description** | Description text for the mod written in multi-line format (e.g. `This is our tutorial for programming Minecraft!`). Multi-line format is specified by three apostrophes (`'''`) at the start and end of the text. This can still be replaced with the standard quotation marks for single line format. |
| **[[mods]]** | Stores a list of mods to be loaded. |

Now let's move on to the optional variables:  

| Optional Variables | Description |
| --- | --- |
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
    versionRange="[32,)" #mandatory
    # An ordering relationship for the dependency - BEFORE or AFTER required if the relationship is not mandatory
    ordering="NONE"
    # Side this dependency is applied on - BOTH, CLIENT or SERVER
    side="BOTH"
    
[[dependencies.examplemod]]
    modId="minecraft"
    mandatory=true
    versionRange="[1.16.1]"
    ordering="NONE"
    side="BOTH"
```

These were both taken directly from the default toml file. Obviously, `examplemod` is replaced by your own mod id when you set the dependencies (in our case `tutorial`).

### pack.mceta

This MCMeta file is basically the configuration file for the assets used in your mod. It allows the game to know that your mod has assets to attach to it. Although it has a different file extension, it is written in the `JSON` format. All you need to do is create an object `pack` with a `description` and a `pack_format` of 5:

```json
{
    "pack": {
        "description": "examplemod resources",
        "pack_format": 5
    }
}
```

This is to be saved as **pack.mcmeta** in `src/main/resources`. If you again deleted this file originally, follow the process for create an `Untitled Text File` in the [toml](./main_file#toml-file) section.

After doing this, your main mod file should be complete.

## Additional Setup
---

To prepare for the next tutorials, we're going to do some pre-setup of the main mod file.

### IEventBus

`IEventBus` is an interface where we register specific events handled by Forge to. There are two main event buses in the game: our mod's event bus and Forge's event bus. The mod event bus is used for listening to lifecycle events (where mods initialize). The Forge event bus handles all the intercepts of vanilla code within the game. 

There are two ways to subscribe a method to an event bus. The first is to go into a constructor (most likely on your main mod class) and call `IEventBus::addListener` which should take in a method that has a single parameter of an object extending `Event`. It would look something like this:

```java
...
modEventBus.addListener(this::method);
...
```

These methods should be private, non-static, and return nothing since they are specific to the class accessed.

The second method is to annotate the top of a class with `@Mod$EventBusSubscriber`. You will need to pass in your mod id as well as the event bus you are currently working on. From there, you would annotate a public static method (one again having a single parameter extending `Event`) with `@SubscribeEvent`. This would look something like:

```java
@Mod.EventBusSubscriber(modid = Tutorial.ID, bus = Bus.MOD)
public class CommonEvents {

	@SubscribeEvent
	public static void newEventCall(final Event event) {
		//Do code
	}
}
```

We will be using the first method throughout the tutorial, but feel free to use the second if you are more comfortable with it.

If you want a more detailed explanation, you can check out the [Forge documentation](https://mcforge.readthedocs.io/en/latest/events/intro/).

### Proxies

One of the most important things about Minecraft is the fact that there are two sides: the `client` and the `server`. There is a bit of ambiguity between the terms, so I will try to make this as clear as I possibly can:  
**Physical Client** - This refers to accessing `Minecraft` on your computer. Everything that runs are your computer when you open the game refers to this specific client.  
**Physical Server** - This refers to accessing the `Minecraft Server` file whenever you are setting up a server. Everything that runs on this server is specific to the server.  
**Logical Server** - This refers to how game logic is handled on the `Server Thread`. This can run on both the `Physical Client (IntegratedServer)` and `Physical Server (DedicatedServer)`.  
**Logical Client** - This refers to how the game renders on a screen and accepts user input to relay to the `Logical Server` from the `Client Thread`. This can only exist on the `Physical Client`.
If you would like more information, check out the [Forge documentation](https://mcforge.readthedocs.io/en/latest/concepts/sides/) on it once again.

So, if you haven't already guessed, we can't really get information on the logical client from the logical server or vice versa without the use of packets. If we did, our mod could never execute on a physical server and would cause a whole number of other issues within the code. So, we need to separate our code such that logical client information is never called on the physical server.

Enter, proxies. What a proxy allows us to do is execute certain methods exclusively on either the physical client or physical server. This prevents reaching across sides and lets our code run smoothly on both sides.

To do this, we are going to setup an interface. Go to your `Package Explorer` and right-click your main package. Click `New`->`Interface`. We are going to extend our package name by putting `.proxy` after it and name the interface `IProxy`. Once you have clicked `Finish`, you should see this:

```java
public interface IProxy {

}
```

Within here, we are going to add a new method called `setup` that will take two `IEventBus` parameters like so:

```java
public interface IProxy {
	
	void setup(IEventBus modEventBus, IEventBus forgeEventBus);
}
```

These two parameters refer to our mod's event bus and Forge's event bus. We take in two parameters as we will have these variables already initialized in our main mod class to save a bit of resources from calling the method redundantly.

Once we have done that, we are going to create two new classes:  
`ClientProxy` - This will be stored within the package `.client.proxy`  
`ServerProxy` - This will be stored within the package `.server.proxy`  
We want to keep logical client specific and logical server specific code separate from the common code. So, subclassing them in packages is the easiest way to keep track of them.

If you want to add your interface via the creation menu, click `Add...` and type in `IProxy`. Double-click the entry, click `Ok`, make sure `Inherited abstract methods` is selected, and then click `Finish`.

This will generate the following two files:

```java
public class ClientProxy implements IProxy {

	@Override
	public void setup(IEventBus modEventBus, IEventBus forgeEventBus) {}
}
```

```java
public class ServerProxy implements IProxy {

	@Override
	public void setup(IEventBus modEventBus, IEventBus forgeEventBus) {}
}
```

From here, we have to be able to call `setup` for our proxies. But how do we necessarily call each method so that they only exist on a specific physical side? Enter `DistExecutor`. This allows us to create a variable that is set to one class if it is the physical client and the other class if it is the physical server.

Once again, go into your main mod file and create a public static final variable with an `IProxy` type. We use `IProxy` instead of the specific class name because we want to be able to access either class from this specific variable. We will set this equal to `DistExecutor::safeRunForDist` which takes in a [supplier](https://www.geeksforgeeks.org/supplier-interface-in-java-with-examples/) holding a new `IProxy` instance:

```java
...
public class Tutorial {
	...
	public static final IProxy PROXY = DistExecutor.safeRunForDist(() -> ClientProxy::new, () -> ServerProxy::new);
	
	public Tutorial() {}
}
```

From here we can create two variables within our constructor that hold `FMLJavaModLoadingContext::getModEventBus` for our mod's event bus and `MinecraftForge::EVENT_BUS` for Forge's event bus and pass them into our proxy variable:

```java
...
public class Tutorial {
	...
	public static final IProxy PROXY = DistExecutor.safeRunForDist(() -> ClientProxy::new, () -> ServerProxy::new);
	
	public Tutorial() {
		IEventBus modEventBus = FMLJavaModLoadingContext.get().getModEventBus(),
				forgeEventBus = MinecraftForge.EVENT_BUS;
		
		PROXY.setup(modEventBus, forgeEventBus);
	}
}
```

### Lifecycle Events

As metioned previously in [IEventBus](#ieventbus), the lifecycle events are what is used to initialize information specific to the mod. This is run parallelly so that multiple mods can be loaded at once.

Let's quickly go over the five main events:  
**FMLCommonSetupEvent** - Basic initialization of methods on both the physical client and physical server.  
**FMLClientSetupEvent** - Basic initialization of methods on both the physical client.  
**FMLDedicatedServerSetupEvent** - Basic initialization of methods on both the physical server.  
**InterModEnqueueEvent** - Send messages to other mods loaded.   
**InterModProcessEvent** - Receives messages from other mods loaded.  

In our case, we will only be setting up two since the rest are irrelevant at the current moment: `FMLCommonSetupEvent` and `FMLClientSetupEvent`. We will setup the methods as mentioned above in our main mod file for the common event and our `ClientProxy` for the client event:

```java
public class Tutorial {
	...
	public Tutorial() {
		IEventBus modEventBus = FMLJavaModLoadingContext.get().getModEventBus(),
				forgeEventBus = MinecraftForge.EVENT_BUS;
		...
		modEventBus.addListener(this::commonSetup);
	}
	
	private void commonSetup(final FMLCommonSetupEvent event) {}
}
```

```java
public class ClientProxy implements IProxy {

	@Override
	public void setup(IEventBus modEventBus, IEventBus forgeEventBus) {
		modEventBus.addListener(this::clientSetup);
	}
	
	private void clientSetup(final FMLClientSetupEvent event) {}
}
```

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.47-web) under **Main Mod File**.

Before we get started with programming something into the game, let's talk about [registries](./registries).

Back to [Getting Started](./getting_started)  
Back to [Minecraft Tutorials](../../)  