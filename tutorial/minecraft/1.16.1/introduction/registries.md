# <a name="registries"></a>Registries
---

Before we get into the meat of programming a mod for Minecraft, we are going to talk about registries. For anyone new to Minecraft, all objects (e.g. `Blocks`, `Items`, `Sounds`, `Biomes`, etc.) must first be registered to be recognized and loaded into the game. A list of all currently supported registries can be located in `ForgeRegistries`.

## <a name="registering-things"></a>Registering Things
---

In this tutorial, we will be going over the two different methods of registering an object and some additional setup to prepare ourselves for creating a mod. If you would like some more information on either of these two topics, check out the [Forge documentation](https://mcforge.readthedocs.io/en/latest/concepts/registries/) on it.

### <a name="registryevent"></a>RegistryEvent

The original way to register objects within the game was done through a `RegistryEvent`. These events would be called on the mod's event bus and register the object. You could then access the object from a public static final variable through injection via an `@ObjectHolder`. If that was a bit too condensed of an explanation, don't worry. Let's break it down.

There are three parts when it comes to registering an object and injecting it into a variable:  
**@ObjectHolder** - This annotation is appended atop the class that will be holding the objects to be injected. It takes a parameter of the mod id.  
**RegistryEvent$Register** - This event is what we use to register our objects within the game. It has a generic that must be filled with a valid registry currently existing (or created) in the game.  
**@Mod$EventBusSubscriber** - If you remember from our [IEventBus](./main_file#ieventbus) overview, this was the second way to add an event to the event bus. This was the most common way of adding listeners when using `RegistryEvent`.  

Let's view this with a basic example. Let's say I wanted to create an `Item` within the game. First, I would create a class that would have an object holder annotation:

```java
@ObjectHolder(Tutorial.ID)
public class TutorialItems {}
```

From there, I would create a public static final variable of the object type I wanted to implement (in this case `Item`):

```java
@ObjectHolder(Tutorial.ID)
public class TutorialItems {
	public static final Item TEST_ITEM = null;
}
```

Now that we have created an `Item` within that class, we need to inject it with a value. Now, the value to be injected is determined by the `modid:variable_name`. This means that for our `Item`, we need to set the registry name to `tutorial:test_item`.

Now let's create our event class. For that, we can create a static class within the current one and attach the `@Mod$EventBusSubscriber` annotation onto it:

```java
@ObjectHolder(Tutorial.ID)
public class TutorialItems {
	public static final Item TEST_ITEM = null;
	
	@Mod.EventBusSubscriber(modid = Tutorial.ID, bus = Bus.MOD)
	public static class Registry {
		
	}
}
```

> Note: We have to specify the bus to our mod's event bus since it defaults to Forge's instead.

Next we have to create a public static final method with our `RegistryEvent$Register` and add a `@SubscribeEvent` annotation to it:

```java
@ObjectHolder(Tutorial.ID)
public class TutorialItems {
	public static final Item TEST_ITEM = null;
	
	@Mod.EventBusSubscriber(modid = Tutorial.ID, bus = Bus.MOD)
	public static class Registry {
		
		@SubscribeEvent
		public static void register(final RegistryEvent.Register<Item> event) {
			
		}
	}
}
```

Now we have to create a new instance of our `Item`, set the registry name using `IForgeRegistryEntry::setRegistryName`, and register it using `IForgeRegistry::registerAll`. The reason we register it using `IForgeRegistry::registerAll` instead of `IForgeRegistry::register` is that it takes in a [Variable Argument](https://docs.oracle.com/javase/tutorial/java/javaOO/arguments.html#varargs) instead of just a single entry. Once we finish, we should end up with something like this:

```java
@ObjectHolder(Tutorial.ID)
public class TutorialItems {
	public static final Item TEST_ITEM = null;
	
	@Mod.EventBusSubscriber(modid = Tutorial.ID, bus = Bus.MOD)
	public static class Registry {
		
		@SubscribeEvent
		public static void register(final RegistryEvent.Register<Item> event) {
			event.getRegistry().registerAll((new Item(new Item.Properties())).setRegistryName(new ResourceLocation(Tutorial.ID, "test_item")));
		}
	}
}
```

Not the prettiest line of code, but it gets the job done quite well and efficiently.

### <a name="deferredregister"></a>DeferredRegister

Due to the amount of people who tried to skip all of the above work and statically initialize their objects the same way Minecraft does, Forge decided to implement a new way of registering objects to avoid these issues. It became a new, safer wrapper around the existing registry events adding another layer of abstraction that provides convenience and safety. This became known as `DeferredRegister`.

A `DeferredRegister` can be set up using the method `DeferredRegister::create`. This takes in two parameters: an `IForgeRegistry` which can be located in `ForgeRegistries` and the mod id. Bringing back our `Item` example from above, it would look like this:

```java
public class TutorialItems {
	public static final DeferredRegister<Item> ITEMS = DeferredRegister.create(ForgeRegistries.ITEMS, Tutorial.ID);
}
```

To replace the `@ObjectHolder` system, we create a `RegistryObject` using `DeferredRegister::register` since this returns a `RegistryObject`. This method takes two parameters: a `String` holding the registry name (no need for the mod id as it is added via the `DeferredRegister`) and a `Supplier` containing the object instance.  
> Note that `RegistryObject`s can still return null like their counterpart until the object has been initialized.  

A basic implementation would look like this:

```java
public class TutorialItems {
	public static final DeferredRegister<Item> ITEMS = DeferredRegister.create(ForgeRegistries.ITEMS, Tutorial.ID);

	public static final RegistryObject<Item> TEST_ITEM = ITEMS.register("test_item", () -> new Item(new Item.Properties()));
}
```

Then all that's left to do is to attach it to our mod's event bus using a different `DeferredRegister::register` method which takes in our mod's event bus as a parameter:

```java
public class Tutorial {
	public Tutorial() {
		...
		TutorialItems.ITEMS.register(modEventBus);
		...
	}
}
```

> Note: If you want to get your item from the `RegistryObject`, you will need to call `RegistryObject::get` as it is a `Supplier`. 

From that, we have registered our `Item` within the game! We will be using this method to register our objects within the game itself.

## <a name="additional-setup"></a>Additional Setup
---

To be able to register our `DeferredRegister`s all in one place, we will need to add a few lines of code to our current project.

### <a name="main-mod-file"></a>Main Mod File

Let's create a private method that returns nothing called `addRegistries` that takes in our mod's event bus. From there, we just need to call it within our constructor and viola, we're done!

```java
public class Tutorial {
	...
	public Tutorial() {
		IEventBus modEventBus = FMLJavaModLoadingContext.get().getModEventBus(),
				forgeEventBus = MinecraftForge.EVENT_BUS;
		...
		addRegistries(modEventBus);
		...
	}
	...
	private void addRegistries(final IEventBus modEventBus) {}
}
```

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.57-web) under **Registries**.

Now let's get into programming our first [item](../basic/items).

Back to [Main Mod File](./main_file)  
Back to [Minecraft Tutorials](../../)  