# Data Generators Part 3: Recipes
---

A recipe simply converts some input into a output. Nothing particularly interesting about them as they just handle everything crafting, smelting, stonecutting, or smithing. However, creating data generators is much more expansive.

## <a name="recipeprovider"></a>RecipeProvider <a href="#recipeprovider"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Recipes are usually registered in two parts. The first is the actual recipe that is created. The second is the advancement that causes the recipe unlocked trigger. This advancement allows us to populate the recipe book. The best way to go about making both of these is to create a class that extends `RecipeProvider`. This provider contains everything we need to generate recipes.

```java
public class Recipes extends RecipeProvider {

	public Recipes(DataGenerator generatorIn) {
		super(generatorIn);
	}
}
```

Forge also adds in a way to add conditional recipes. To easily tack this on, we just need to implement `IConditionBuilder`.

```java
public class Recipes extends RecipeProvider implements IConditionBuilder {
	...
}
```

To be able to register our recipes within this provider, we need to override `RecipeProvider#registerRecipes`. The reason we do this is because this method is directly called in `IDataProvider#act`, the method used to run the main code of the data provider.

```java
public class TutorialRecipeProvider extends RecipeProvider implements IConditionBuilder {
	...
	@Override
	protected void registerRecipes(Consumer<IFinishedRecipe> consumer) {
		
	}
}
```

> Note: We do not call the `super` method because then all the recipes from Minecraft would be registered as well. That would make an unecessary use of resources as we are not changing these recipes.

> Note: We have it so that recipes are only generated if the server is included. This is because recipes are stored and looked up on the logical server side and synced to the logical client.

## <a name="recipe-builders"></a>Recipe Builders <a href="#recipe-builders"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

To register our recipes, we have to build an `IFinishedRecipe` version of them. So, recipe builders are used to construct these recipes to be used. We will go over each builder implementation and what methods you can use to construct your own recipe. All usable methods are chainable until `build` is called.

All builders will include three versions of `build`. The first requires only the `Consumer<IFinishedRecipe>`. The second requires a `String` for the name of the location tp save the recipe. The third takes in a `ResourceLocation` rather than a string. We will only be using the first and third of these build methods as the second is just a dumbed down version of the third.

### <a name="icriterioninstance"></a>ICriterionInstance <a href="#icriterioninstance"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

One of the major inclusions of triggers in advancements is the four `ICriterionInstance`s created in `RecipeProvider`. These four methods handle how recipes are unlocked within the game. If you would like to use your own criterion, you may go ahead and use it. However, if you just want to check whether the player has an item or entered a specific block, these are the methods for you.

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`enteredBlock` | `Block` block | `EnterBlockTrigger.Instance` | Returns a trigger that notifies if the player has entered this block.
`hasItem` | `IItemProvider` item | `InventoryChangeTrigger.Instance` | Returns a trigger that notifies if the player has this item in their inventory.
`hasItem` | `ITag<Item>` tag | `InventoryChangeTrigger.Instance` | Returns a trigger that notifies if the player has an item in this tag in their inventory.
`hasItem` | `ItemPredicate...` predicate | `InventoryChangeTrigger.Instance` | Returns a trigger that notifies if the player has an item that meets this condition(s) in their inventory.

> Note: These are all protected static methods meaning that they can only be accessed within your `RecipeProvider` and any extension of it. It should also be accessed statically.

### <a name="shapedrecipebuilder"></a>ShapedRecipeBuilder <a href="#shapedrecipebuilder"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

`ShapedRecipeBuilder` is used for creating shaped recipes. There are a couple of requirements needed for validation:  
- There must be a pattern defined that is greater than a size of 1.
- All keys used in the pattern must have a defined type.
- Whitespaces are not allowed as keys as they represent empty items.
- There must be a criteria to obtain the recipe in the recipe book.

Method | Parameter(s) | Use
--- | :---: | ---
`shapedRecipe` | `IItemProvider` resultIn | Creates a builder with the following provider.
`shapedRecipe` | `IItemProvider` resultIn<br>`int` countIn | Creates a builder with the following provider and amount.
`key` | `Character` symbol<br>`ITag<Item>` tagIn | Creates a pattern key for anything in this tag.
`key` | `Character` symbol<br>`IItemProvider` itemIn | Creates a pattern key for this provider.
`key` | `Character` symbol<br>`Ingredient` ingredientIn | Creates a pattern key for this ingredient.
`patternLine` | `String` patternIn | Creates a pattern line. Calling this multiple times will add a new row to the pattern.
`addCriterion` | `String` name<br>`ICriterionInstance` criterionIn | Adds a critertia to unlock this recipe. The name is what the variable will be called.
`setGroup` | `String` groupIn | Sets the group this result will appear in the recipe book.

> Note: This does not support NBT tags in the result. That is something Forge added, so you will need to create a custom builder if you would like to generate it.

### <a name="shapelessrecipebuilder"></a>ShapelessRecipeBuilder <a href="#shapelessrecipebuilder"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

`ShapelessRecipeBuilder` is used for creating shapeless recipes. There is only one requirement needed for validation:  
- There must be a criteria to obtain the recipe in the recipe book.

Method | Parameter(s) | Use
--- | :---: | ---
`shapelessRecipe` | `IItemProvider` resultIn | Creates a builder with the following provider.
`shapelessRecipe` | `IItemProvider` resultIn<br>`int` countIn | Creates a builder with the following provider and amount.
`addIngredient` | `ITag<Item>` tagIn | Adds an ingredient that can be anything in this tag.
`addIngredient` | `IItemProvider` itemIn | Adds an ingredient for this provider.
`addIngredient` | `IItemProvider` itemIn<br>`int` quantity | Adds an ingredient for this provider multiple times.
`addIngredient` | `Ingredient` ingredientIn | Adds an ingredient.
`addIngredient` | `Ingredient` ingredientIn<br>`int` quantity | Adds an ingredient multiple times.
`addCriterion` | `String` name<br>`ICriterionInstance` criterionIn | Adds a critertia to unlock this recipe. The name is what the variable will be called.
`setGroup` | `String` groupIn | Sets the group this result will appear in the recipe book.

### <a name="customrecipebuilder"></a>CustomRecipeBuilder <a href="#customrecipebuilder"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

`CustomRecipeBuilder` is used for recipes that are not deterministic. This is ususally used for handling items that return NBT tags (e.g. fireworks, dyed armor). This should only be used if the recipe you are creating is dynamic for large amounts of data. NBT tags for one item should just have its own builder created.

> Note: This is the one exception to the `build` rule where we must implement method two. Remember to include your mod id and a colon before the name of the file.

Method | Parameter(s) | Use
--- | :---: | ---
`customRecipe` | `SpecialRecipeSerializer<?>` serializer (p\_218656\_0\_) | Creates a builder with the following serializer.

### <a name="cookingrecipebuilder"></a>CookingRecipeBuilder <a href="#cookingrecipebuilder"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

`CookingRecipeBuilder` is used for recipes within the furnace types. There is only one requirement needed for validation:  
- There must be a criteria to obtain the recipe in the recipe book.

Method | Parameter(s) | Use
--- | :---: | ---
`cookingRecipe` | `Ingredient` ingredientIn<br>`IItemProvider` resultIn<br>`float` experienceIn<br>`int` cookingTimeIn<br>`CookingRecipeSerializer<?>` serializer | Creates a cooking recipe for a specific serializer with the ingredient, time taken to cook, experience received, and result.
`blastingRecipe` | `Ingredient` ingredientIn<br>`IItemProvider` resultIn<br>`float` experienceIn<br>`int` cookingTimeIn | Creates a blast furnace recipe with the ingredient, time taken to cook, experience received, and result.
`smeltingRecipe` | `Ingredient` ingredientIn<br>`IItemProvider` resultIn<br>`float` experienceIn<br>`int` cookingTimeIn | Creates a furnace recipe with the ingredient, time taken to cook, experience received, and result.

> Note: The specific smelters (e.g. Blast Furnace, Smoker) usually take half as much time to smelt as the Furnace.

### <a name="singleitemrecipebuilder"></a>SingleItemRecipeBuilder <a href="#singleitemrecipebuilder"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

`SingleItemRecipeBuilder` is used for recipes with a one to one output. There is only one requirement needed for validation:  
- There must be a criteria to obtain the recipe in the recipe book.

Method | Parameter(s) | Use
--- | :---: | ---
`stonecuttingRecipe` | `Ingredient` ingredientIn<br>`IItemProvider` resultIn | Creates a stonecutter recipe with the following ingredient and result.
`stonecuttingRecipe` | `Ingredient` ingredientIn<br>`IItemProvider` resultIn<br>`int` countIn | Creates a stonecutter recipe with the following ingredient and result amount.
`addCriterion` | `String` name<br>`ICriterionInstance` criterionIn | Adds a critertia to unlock this recipe. The name is what the variable will be called.

### <a name="smithingrecipebuilder"></a>SmithingRecipeBuilder <a href="#smithingrecipebuilder"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

`SmithingRecipeBuilder` is used for smithing recipes. There is only one requirement needed for validation:  
- There must be a criteria to obtain the recipe in the recipe book.

Method | Parameter(s) | Use
--- | :---: | ---
`smithingRecipe` | `Ingredient` base<br>`Ingredient` addition<br>`Item` output | Creates a smithing recipe with the following input and ingredient to make the result.
`addCriterion` | `String` name<br>`ICriterionInstance` criterion | Adds a critertia to unlock this recipe. The name is what the variable will be called.

### <a name="conditionalrecipe"></a>ConditionalRecipe <a href="#conditionalrecipe"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

`CondtionalRecipe` is a recipe system added by Forge. This allows you to create conditions of whether to use certain recipes.

> Note: This is not an independent builder. This just takes one or more of the above or custom recipe builders and adds certain conditions of whether it will use that specific recipe or not.

The first thing I will go over is `IConditionBuilder`. This adds in some conditional statements that will help determine whether your recipe exists:

Method | Parameter(s) | Use
--- | :---: | ---
`and` | `ICondition...` values | All conditions must return true.
`FALSE` | NONE | This will never occur.
`TRUE` | NONE | This will always occur.
`not` | `ICondition` value | Returns the opposite result of the condition.
`or` | `ICondition...` values | At least one condition must return true.
`itemExists` | `String` namespace<br>`String` path | True if the item exists.
`modLoaded` | `String` modid | True if the mod exists.

The other thing to mention is `ConditionalRecipe$Builder` called by `ConditionalRecipe::builder`. This will allow you to generate a recipe that only exists under the set conditions. `build` method three should be used. However, `build` method two now supports a namespace for easy access.

Method | Parameter(s) | Use
--- | :---: | ---
`addCondition` | `ICondition` condition | Adds a condition to the next recipe added to the builder.
`addRecipe` | `Consumer<Consumer<IFinishedRecipe>>` callable | Adds a recipe with the previous conditions. Conditions are then cleared. Can be via `BUILDER::build`.
`addRecipe` | `IFinishedRecipe` recipe | Adds a recipe with the previous conditions. Conditions are then cleared. 
`setAdvancement` | `String` namespace<br>`String` path<br>`ConditionalAdvancement.Builder` advancement | Sets the conditional advancment to be used with the conditional recipe.
`setAdvancement` | `ResourceLocation` id<br>`ConditionalAdvancement$Builder` advancement | Sets the conditional advancment to be used with the conditional recipe.

## <a name="application"></a>Application <a href="#application"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

When registering this provider, we are not going to check if they should be included in the client. This is because recipes are part of a datapack which checks on the server. Therefore, we will use `GatherDataEvent#includeServer` to check whether to add the provider or not.

```java
private void data(final GatherDataEvent event) {
	...
	if(event.includeServer()) {
		gen.addProvider(new Recipes(gen));
	}
}
```

> Note: We do not chain if statements to check if the value is present since these variables are independent of one another. Therefore, we must check each regardless.

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/) under **Recipes**.

Now take a gander at [tags](./tags).

Back to [Data Generators](../../index#data-generators)  
Back to [Minecraft Tutorials](../../index)  