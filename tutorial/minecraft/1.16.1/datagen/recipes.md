# <a name="recipes"></a>Data Generators Part 1: Recipes
---

Now that you have completed the basic setup. Let's go into making your first `DataGenerator`. In this tutorial, we will be covering the basics of recipes and their builders. Whenever we get around to doing custom recipes, I will show you how to create the builders required to get them working with this data generator.

## <a name="recipeprovider"></a>RecipeProvider
---

Recipes are usually registered in two parts. The first is the actual recipe that is created. The second is the advancement that causes the recipe unlocked trigger. This advancement allows us to populate the recipe book. The best way to go about making both of these is to create a class that extends `RecipeProvider`. This provider contains everything we need to generate recipes.

```java
public class TutorialRecipeProvider extends RecipeProvider {

	public TutorialRecipeProvider(DataGenerator generatorIn) {
		super(generatorIn);
	}
}
```

Forge also adds in a way to add conditional recipes. To easily tack this on, we just need to implement `IConditionBuilder`.

```java
public class TutorialRecipeProvider extends RecipeProvider implements IConditionBuilder {
	...
}
```

To be able to register our recipes within this provider, we need to override `RecipeProvider::registerRecipes`. The reason we do this is because this method is directly called in `IDataProvider::act`, the method used to run the main code of the data provider.

```java
public class TutorialRecipeProvider extends RecipeProvider implements IConditionBuilder {
	...
	@Override
	protected void registerRecipes(Consumer<IFinishedRecipe> consumer) {
		
	}
}
```

> Note: We do not call the `super` method because then all the recipes from Minecraft would be registered as well. That would make an unecessary use of resources as we are not changing these recipes.

Now all we need to do is to call `DataGenerator::addProvider` within our `GatherDataEvent` to register our provider to be called.

```java
public class Tutorial {
	...	
	private void gatherData(final GatherDataEvent event) {
		DataGenerator gen = event.getGenerator();
		
		if(event.includeServer()) {
			gen.addProvider(new TutorialRecipeProvider(gen));
		}
	}
}
```

> Note: We have it so that recipes are only generated if the server is included. This is because recipes are stored and looked up on the logical server side and synced to the logical client.

## <a name="recipe-builders"></a>Recipe Builders
---

To register our recipes, we have to build an `IFinishedRecipe` version of them. So, recipe builders are used to construct these recipes to be used. We will go over each builder implementation and what methods you can use to construct your own recipe.

### <a name="icriterioninstance"></a>RecipeProvider: ICriterionInstance

One of the major inclusions of triggers in advancements is the four `ICriterionInstance`s created in `RecipeProvider`. These four methods handle how recipes are unlocked within the game. If you would like to use your own criterion, you may go ahead and use it. However, if you just want to check whether the player has an item or entered a specific block, these are the methods for you.

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`enteredBlock` | `Block` block (p\_200407\_0\_) | `EnterBlockTrigger.Instance` | Returns a trigger that notifies if the player has entered this block.
`hasItem` | `IItemProvider` provider (p\_200403\_0\_) | `InventoryChangeTrigger.Instance` | Returns a trigger that notifies if the player has this item in their inventory.
`hasItem` | `ITag<Item>` tag (p\_200409\_0\_) | `InventoryChangeTrigger.Instance` | Returns a trigger that notifies if the player has an item in this tag in their inventory.
`hasItem` | `ItemPredicate...` predicate (p\_200405\_0\_) | `InventoryChangeTrigger.Instance` | Returns a trigger that notifies if the player has an item that meets this condition(s) in their inventory.

> Note: These are all protected static methods meaning that they can only be accessed within your `RecipeProvider` and any extension of it. It should also be accessed statically.

### <a name="shapedrecipebuilder"></a>ShapedRecipeBuilder

##TODO

### <a name="shapelessrecipebuilder"></a>ShapelessRecipeBuilder

##TODO

### <a name="customrecipebuilder"></a>CustomRecipeBuilder

##TODO

### <a name="cookingrecipebuilder"></a>CookingRecipeBuilder

##TODO

### <a name="singleitemrecipebuilder"></a>SingleItemRecipeBuilder

##TODO

### <a name="smithingrecipebuilder"></a>SmithingRecipeBuilder

##TODO

### <a name="conditionalrecipe"></a>ConditionalRecipe

##TODO

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.61-web) under **RecipeProvider**.

Now that you understand recipes, take your hand at part 2 on [language localization](./lang).

Back to [Introduction](./introduction)  
Back to [Data Generators](../../index#data-generators)  
Back to [Minecraft Tutorials](../../)  