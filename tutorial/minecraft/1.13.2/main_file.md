# Main Mod File

Congrats on setting up your workspace! Welcome to part 2 of the introduction where we set up the basic mod file and folders for assets. You can delete any packages within src/main/java via the Project Explorer as we will be writing the code from scratch.

## Java Class

Let's start with the java class. Head over to the package explorer located by default on the left side of the screen. Right click src/main/java and go to New->Class. You should now see a screen where you can name the class and set the package. Usually, the package refers to the developer's website in reverse with an appended mod id. Let's assume we'll use **tutorial** as the id. So, the package should read 'domain.website.tutorial' (e.g. 'com.championash5357.tutorial' or 'io.github.championash5357.tutorial'). As for the class name, you can use the name of the mod id (e.g. 'Tutorial'). After setting both of these parameters, click Finish to create the class.

You should now see something that looks like:
```java
package domain.website.tutorial;

public class Tutorial {
    
}
```

This is the default generated class for Eclipse. To be able to turn this into the mod, we need to add an `@Mod` annotation to the top of the class. This mod has a required parameter of the id. So, you would need to put in paraentheses the string id (e.g. `@Mod("tutorial")`). However, for consistent use over and over, it is recommend you create a public static final initializer for the id:
```java
@Mod(Tutorial.ID)
public static class Tutorial {
    public static final String ID = "tutorial";
}
```

At the current moment, that's all you need for the class; however, we will be adding in four events that will most likely be used in advanced modifications. To add events to our mod bus for execution, we need to call `@Mod#EventBusSubscriber`. This takes two parameters, the id of our mod (modid) and the bus it is sending the event to (bus). The Bus enum only contains `Bus#FORGE` and `Bus#MOD`. Whenever registries are used, the mod bus will be called. However, in the case of standard events the forge bus will be called by default instead. So, this would be appeneded once again above the class:
```java
@Mod(Tutorial.ID)
@Mod.EventBusSubscriber(modid = Tutorial.ID, bus = Bus.MOD)
public class Tutorial {...}
```

Next, we will need to create a static method with an `@SubscribeEvent` annotation to allow the event to be registered by forge. The parameters will be four final event classes: `FMLCommonSetupEvent`, `FMLClientSetupEvent`, `InterModEnqueueEvent`, and `InterModProcessEvent`. Both `FMLCommonSetupEvent` and `FMLClientSetupEvent` are part of the `ModLifecycleEvent` where the mod is initialized. `FMLCommonSetupEvent` is the basic initialization on both the client and server side. `FMLClientSetupEvent`, on the other hand, is the initialization on the client side. There is a `FMLDedicatedServerSetupEvent` specifically for the server side; however, it is rarely used. Then there is the `InterModEnqueueEvent` and `InterModProcessEvent` which is used to send and recieve messages between mods respectively. These are the four most common events used hence why we usually include them in the main mod file:
```java
...
@SubscribeEvent
public static void setup(final FMLCommonSetupEvent event) {

}

@SubscribeEvent
public static void clientSetup(final FMLClientSetupEvent event) {

}

@SubscribeEvent
public static void enqueueIMC(final InterModEnqueueEvent event) {

}

@SubscribeEvent
public static void processIMC(final InterModProcessEvent event) {

}
...
```
After doing this, your main mod file should be complete.

## TOML File

Tom's Obvious, Minimal Language or TOML is a file format for easy reading and writing. In modding, it is our mod loading data that is handled by the game. There are mandatory and optional variables that can be set depending on the preferences of your mod. If you haven't deleted the file there by default, then anything you don't want to use can be commented out using a pound or hashtag (e.g. `# var = value`).

If you also deleted everything in src/main/resources, right click that folder, go to New->Other...->General->Untitled Text File->Finish. Type something in the file and save it to version->src->main->resources->META-INF and call it **mods.toml**.

There are currently 13 variables and two dependencies that are written in default toml file of which nine are required. Let's start with the required variables:  
**modLoader** - the name of the mod loader to use, should remain default for regular mods (`"javafml"` by default)  
**loaderVersion** - the version range of the mod loader to use, also known as the forge version (`"[25,)"` by default)  
**modId** - the modid for the mod (e.g. `"tutorial"`)  
**version** - the version of the mod (e.g. `"1.0.0.0"`, usually in [semantic](https://mcforge.readthedocs.io/en/1.13.x/conventions/versioning/) form)  
**displayName** - display name for the mod (e.g. `"Tutorial Mod"`)  
**description** - description text for the mod written in multi line format ?(e.g. `'''This is our tutorial for programming Minecraft!'''`)  
There is one more variable simply listed as `[[mods]]` that just stores a list of mods and isn't set to anything by default to allow all mods.  

Now let's move on to the optional variables:  
**issueTrackerURL** - a URL to the issue tracker of this mod (e.g. `"https://github.com/ChampionAsh5357/1.13.2-Minecraft-Tutorial/issues"`)  
**updateJSONURL** - a URL to query for updates to this mod (e.g. `"https://github.com/ChampionAsh5357/1.13.2-Minecraft-Tutorial/update_checker"`)  
**displayURL** - a URL for the mod's homepage (e.g. `"https://github.com/ChampionAsh5357/1.13.2-Minecraft-Tutorial"`)  
**logoFile** - file name of the logo picture in src/main/resources (e.g. `"logo.png"`)  
**credits** - text field for giving credit (e.g. `"Everyone who has supported me over the years!"`)  
**authors** - text field for the author(s) of the mod (e.g. `"ChampionAsh5357"`)  

It is recommended you have all the variables in the file with the unused ones being commented out just for future purpose.

The final part is the dependencies which are formatted like so:
```toml
[[dependencies.tutorial]]
    # the modid of the dependency
    modId="forge" #mandatory
    # Does this dependency have to exist - if not, ordering below must be specified
    mandatory=true #mandatory
    # The version range of the dependency
    versionRange="[25,)" #mandatory
    # An ordering relationship for the dependency - BEFORE or AFTER required if the relationship is not mandatory
    ordering="NONE"
    # Side this dependency is applied on - BOTH, CLIENT or SERVER
    side="BOTH"
    
[[dependencies.tutorial]]
    modId="minecraft"
    mandatory=true
    versionRange="[1.13.2]"
    ordering="NONE"
    side="BOTH"
```
These were both taken directly from the default toml file. Obviously, 'tutorial' is replaced by your own mod id when you set the dependencies.

## MCMeta File

