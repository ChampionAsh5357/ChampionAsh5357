# Common Issues and Recommendations
---

## What is this?
---

This is an updated version of the original [common issues and recommendations](https://forums.minecraftforge.net/topic/61757-common-issues-and-recommendations/) thread created by [**diesieben07**](https://forums.minecraftforge.net/profile/1697-diesieben07/). It is simply a collection of common issues and recommendations for the Modder Support subforum. Instead of repeating these details over and over again, a link to this thread can be used.

This post will be updated as needed. If you would like to suggest something, either PM me or put it in a separate thread to keep this clean.

## Problematic Code
---

1. Please use the [registry events](https://mcforge.readthedocs.io/en/latest/concepts/registries/#registering-things) or a [deferred register](https://mcforge.readthedocs.io/en/latest/concepts/registries/#deferredregister) to add your blocks, items, and other registry entries if a forge registry exists for them.  
If this is not applicable, the entry should be deferred and registered in an existing registry event for most cases.
1. Do not reach across logical sides. This will cause subtle and not-so-subtle issues that may occur randomly and very rarely. One of the most common side effects is having a dedicated server crash with a `ClassNotFoundException`. Read the [documentation about sides](https://mcforge.readthedocs.io/en/latest/concepts/sides/) to understand why this is a bad idea. Instead isolate and reference the code via `DistExecutor`.
1. Any `IForgeRegistryEntry` is singleton-like. That means there is only one instance of your object class. This means you cannot store dynamic data as instance fields on your object class. For example, there is not a new `Block` instance for every position in the world or a new `Item` instance for every `ItemStack`. Instead, you must use a `TileEntity` or the NBT data on an `ItemStack` respectively.
1. Do not use methods that are deprecated in Forge or vanilla Minecraft. Usually there will be a comment above the method explaining what to use instead.  
There are some exceptions for some methods such as those in the `Block` class. These methods may be overridden, but calls to these methods should use their respective supertype in `BlockState`.
1. Do not use `ITileEntityProvider` or `ContainerBlock`. These classes are legacy vanilla code. To add a `TileEntity` to your block, override `IForgeBlock#hasTileEntity` and `IForgeBlock#createTileEntity` (the method with a `BlockState` and `IBlockReader` parameter) in your `Block` class.
1. Do not use `IInventory`. Instead, use the [capability API](https://mcforge.readthedocs.io/en/latest/datastorage/capabilities/) with `IItemHandler`.  
*The forge docs on this still need to be updated with my 1.14.4 PR so it can be accurate.*
1. Capabilities that are invalidated should have their `LazyOptional<?>` holders invalidated as well. This can be added directly if you own the object (e.g. `TileEntity#remove` or `Entity#remove`) or indirectly through the event via `AttachCapabilitiesEvent#addListener`.
1. Containers do not get information from the client by default. Inventories and integers are synced when a slot or reference holder is used and tracked with those values. As for other data, it can be synced using a `PacketBuffer` sent through `NetworkHooks::openGui`. The container type does need to be registered using `IForgeContainerType::create` for it to send and be received.
1. All unlocalized names and enum entries should contain your mod ID to avoid conflicts between mods.
1. A `TileEntity` class must have a no-argument constructor to be passed into a `TileEntityType<?>` for registration. As a general recommendation, a `TileEntity` should not have more than this one constructor to avoid confusion.
1. Almost all of world generation is handled through JSON files. This means that if you want to create a biome, dimension, etc., it should be handled through a JSON file in their respective location. This does not apply for adding data to already existing biomes. That is handled through `BiomeLoadingEvent`.
1. An `ItemStack` should never be null as expected of vanilla and Forge code. Instead, use/return `ItemStack#EMPTY` over null and `ItemStack#isEmpty` to check for empty stacks. Do not compare against `ItemStack#EMPTY`.
1. Packets/Messages should be handled and registered through a [`SimpleChannel` network](https://mcforge.readthedocs.io/en/latest/networking/simpleimpl/).
1. Handle exceptions properly. Do not avoid a game crash at all costs. It is sometimes okay to crash the game. Read [this article](https://docs.microsoft.com/en-us/archive/blogs/ericlippert/vexing-exceptions) for more information.

## Code Style
---

1. Registry names and file names should be written in snake case (e.g. all lowercase letters, with spaces replaced by underscores like `simple_block` or `example_item`).
1. Just because you cannot find a method name does not mean it doesn't exist. Most likely, you are using an outdated version of the obfuscation mappings. If this is the case, you can dm the bot from the [forge discord](https://discord.com/invite/UvedJ9m) using `!mcp` followed by the mapped or unmapped name to get the method (`!mcpm`) or field (`!mcpf`) you are looking for.  
You can also update your mappings inside `build.gradle` to the latest version which can currently be found in the `#modder-support-116`'s channel pins on the forge discord.
1. Use `@Override` when you intend to override methods. This helps tremendously when updating your code to new versions of Minecraft, but in general is good practice. Read this [Stack Overflow answer](https://stackoverflow.com/questions/94361/when-do-you-use-javas-override-annotation-and-why/94411#94411) for an explanation.
1. There are many different ways to handle events. Please pick and stick to one of the following methods listed in the image below.  
![Event Image](https://cdn.discordapp.com/attachments/665281306426474506/665605979798372392/eventhandler.png)  
Credits to the MMD Discord for this image.
1. Do not abuse inheritance for code-reuse. This often manifests in classes like `BaseItem`. Using this pattern prevents you from extending other essential classes such as `ArmorItem` requiring a large level of code duplication and events. Prefer composition or utility methods instead to reuse code.
1. There should not be any reason to use `@OnlyIn` anywhere within your code regardless of if the extended method has the annotation. This is handled interally to remove certain classes/methods in the server jar. Any sided code should be handled properly via `DistExecutor`.
1. JSON files should be created via [Data Generators](https://mcforge.readthedocs.io/en/latest/datagen/intro/) whenever possible instead of copy-pasting/handwriting.
1. The point of the `DistExecutor` system is to abstract over physical client and server specific behavior, the opposite of "common". Common code should go in your main mod class.

## General Issues
---

1. Do not use "export" or "create jar file" functionalities of your IDE or other means to creat your final mod file. You must use the `gradlew build` task.
1. When creating a git repository for your mod, the repository root should be where your `build.gradle` file is. The MDK ships with a default `.gitignore` file so that the only necessary files will be added to version control.
1. If you are running into gradle decompilation errors when the buildscript hasn't been modified, try following these steps. First, close your IDE. Next, run `gradlew --stop` and then `gradlew clean`. Finally, repeat the setup process once again. If that doesn't work, remove the `.gradle` folder in the local and user directory first. This will usually fix most user problems with gradle.
1. Whenever you are asking for help on the forums or discord about code issues, you should provide the following items when applicable: a link to a paste of your stacktrace/log, a link to your current repository holding the mod, and a detailed explanation of the issue you are encountering. This allows others to more accurately help you with your current problem in a single reply rather than 10+ down the line from guessing and checking.

Thank you to diesieben07 for creating the original post and sciwhiz12 for reviewing this thread.