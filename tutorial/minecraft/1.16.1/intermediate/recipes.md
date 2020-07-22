# Recipes
---

Recipes are one of the most common things to implement for any particular item in the game. However, as you get to more and more advanced tutorials, you tend to realize how many people create their own recipes for their own blocks.

So, welcome all you modders to Modding 202! This will go over what I consider to be the intermediate level of the core of Minecraft programming. To start this off, we will be creating a custom recipe for our [washer](../blocks/blockstate).

## <a name="recipe-breakdown"></a>Recipe Breakdown <a href="#recipe-breakdown"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

All Minecraft recipes are hooked in using three parts: the recipe class, the recipe type, and the recipe serializer. We will be doing a semi basic implementation of each of these. I will be creating an abstract version of the recipe class and serializer as well for more usage later on.

### <a name="irecipetype"></a>IRecipeType <a href="#irecipetype"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Let's start with `IRecipeType`. This just basically holds a string that will create a new map entry within `RecipeManager` for any recipe with that specific type. The string held within the registry should be your mod id appended with whatever you are going to call it.

```java
public class TutorialRecipes {
	public static class Type {
		public static final IRecipeType<WasherRecipe> WASHER = IRecipeType.register(Tutorial.ID + ":washer");
	}
}
```

### <a name="irecipe"></a>IRecipe <a href="#irecipe"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Next, let's go into `IRecipe`. Each instance takes in a generic that extends `IInventory`. As we shouldn't use `IInventory` at all, the reason we set the generic for this value is for backwards compatibility with Minecraft. We can call a version ourselves using `RecipeWrapper` to instead take in an `IItemHandlerModifiable` capability.

All `IRecipes` should have a constructor that takes in a `ResourceLocation`, a String group (if you are going to use the `RecipeBook`), an `Ingredient` (`NBTIngredient` if you want to use NBT data), and `ItemStack` output. Note, it doesn't have to be an ingredient or itemstack, that's just the case most people use it for.

So let's called a basic version of `IRecipe` and implement the methods.

```java
public abstract class AbstractSingleItemTimerRecipe implements IRecipe<IInventory> {

	public SingleItemRecipe() {
	}

	@Override
	public boolean matches(IInventory inv, World worldIn) {
		return false;
	}

	@Override
	public ItemStack getCraftingResult(IInventory inv) {
		return null;
	}

	@Override
	public boolean canFit(int width, int height) {
		return false;
	}

	@Override
	public ItemStack getRecipeOutput() {
		return null;
	}
	
	@Override
	public String getGroup() {
		return IRecipe.super.getGroup();
	}
	
	@Override
	public NonNullList<Ingredient> getIngredients() {
		return IRecipe.super.getIngredients();
	}
	
	@Override
	public ResourceLocation getId() {
		return null;
	}

	@Override
	public IRecipeSerializer<?> getSerializer() {
		return null;
	}

	@Override
	public IRecipeType<?> getType() {
		return null;
	}
}
```

Let's also review the methods in the class.

Method | Use
--- | ---
`matches` | Checks if a recipe matches current recipe inventory.
`getCraftingResult` | Returns the result of the recipe. Should be a copy of the output.
`canFit` | Determines if this recipe can fit in a grid of the given width/height.
`getRecipeOutput` | Gets the result of the recipe. Used for display purposes only.
`getRemainingItems` | Gets any remaining items after the recipe has been completed. Returns by default any container items.
`getIngredients` | Gets the ingredients of the recipe.
`isDynamic` | If the recipe is dynamic. This refers to recipes outputting some sort of NBT data based on highly dynamic variables.
`getGroup` | Recipes with equal group are combined into one button in the recipe book
`getIcon` | Gets the display icon of the recipe.
`getId` | Gets the id of the recipe.
`getSerializer` | Gets the serializer of the recipe.
`getType` | Gets the recipe type.

As most of these methods are required, we will be implementing all but one of these methods: `IRecipe::isDynamic`. The only other recipe we will reference in the main recipe class is `IRecipe::getIcon` as that can't be generalized. Before you try and implement it yourself, you should go and look at some source examples on how `IRecipe::matches` is implemented to figure out how you want to. In my case, I am doing a one-to-one recipe with a timer so that's what I will be specifying towards.

```java
public abstract class AbstractSingleItemTimerRecipe implements IRecipe<IInventory> {

	protected final ResourceLocation id;
	protected final String group;
	protected final Ingredient ingredient;
	protected final int time;
	protected final ItemStack result;
	private final IRecipeType<?> type;
	private final IRecipeSerializer<?> serializer;
	
	public AbstractSingleItemTimerRecipe(IRecipeType<?> type, IRecipeSerializer<?> serializer, ResourceLocation id, String group, Ingredient ingredient, int time, ItemStack result) {
		this.type = type;
		this.serializer = serializer;
		this.id = id;
		this.group = group;
		this.ingredient = ingredient;
		this.time = time;
		this.result = result;
	}

	@Override
	public boolean matches(IInventory inv, World worldIn) {
		return this.ingredient.test(inv.getStackInSlot(0));
	}

	@Override
	public ItemStack getCraftingResult(IInventory inv) {
		return this.result.copy();
	}

	@Override
	public boolean canFit(int width, int height) {
		return true;
	}

	@Override
	public ItemStack getRecipeOutput() {
		return this.result;
	}
	
	@Override
	public String getGroup() {
		return this.group;
	}
	
	@Override
	public NonNullList<Ingredient> getIngredients() {
		NonNullList<Ingredient> ingredients = NonNullList.create();
		ingredients.add(ingredient);
		return ingredients;
	}

	@Override
	public NonNullList<ItemStack> getRemainingItems(IInventory inv) {
		return NonNullList.create();
	}
	
	@Override
	public ResourceLocation getId() {
		return this.id;
	}
	
	public int getTime() {
		return time;
	}

	@Override
	public IRecipeSerializer<?> getSerializer() {
		return this.serializer;
	}

	@Override
	public IRecipeType<?> getType() {
		return this.type;
	}
}
```

> Note: The time variable will be useful outside of the class, so we will need get it for later.

### <a name="irecipeserializer"></a>IRecipeSerializer <a href="#irecipeserializer"><img src="../../../../images/link.png" alt="Link" style="width:15px;height:15px;"></a>

Finally, we have the recipe serializer. This will be [registered](../introduction/registries#deferredregister) as well. There are three methods we have to implement.

Method | Use
--- | ---
`read` | Reads the data from a `JsonObject` and returns an instance of your recipe (JSON file stored in datapack).
`read` | Reads the data from a `PacketBuffer` and returns an instance of your recipe (server/client packet sending).
`write` | Writes the data from your instance to a `PacketBuffer` (server/client packet sending).

The last two are especially important if you are going to be implementing a `RecipeBook`. Since we are trying to make a generalized version, we need to create some sort of interface so that we can quickly reference our instance for creation. This is one of the two reasons we have to write our own version of `SingleItemRecipe` as the interface is not for public use.

> Note: We could use an Access Transformer to change that; however, we needed a time implementation anyways and the ability to give an `ItemStack` as a result.

So let's create some JSON and `PacketBuffer` deserialization.

```java
public static class Serializer<T extends AbstractSingleItemTimerRecipe> extends ForgeRegistryEntry<IRecipeSerializer<?>> implements IRecipeSerializer<T> {

	private final IRecipeFactory<T> factory;

	public Serializer(IRecipeFactory<T> factory) {
		this.factory = factory;
	}

	@Override
	public T read(ResourceLocation recipeId, JsonObject json) {
		String group = JSONUtils.getString(json, "group", "");
		Ingredient ingredient = JSONUtils.isJsonArray(json, "ingredient") ? Ingredient.deserialize(JSONUtils.getJsonArray(json, "ingredient")) : Ingredient.deserialize(JSONUtils.getJsonObject(json, "ingredient"));
		int time = JSONUtils.getInt(json, "time");
		ItemStack stack = ShapedRecipe.deserializeItem(JSONUtils.getJsonObject(json, "result"));
	}

	@Override
	public T read(ResourceLocation recipeId, PacketBuffer buffer) {
		String group = buffer.readString();
		Ingredient ingredient = Ingredient.read(buffer);
		int time = buffer.readInt();
		ItemStack stack = buffer.readItemStack();
		return this.factory.create(recipeId, group, ingredient, stack);
	}

	@Override
	public void write(PacketBuffer buffer, T recipe) {
		buffer.writeString(recipe.group);
		recipe.ingredient.write(buffer);
		buffer.writeInt(recipe.time);
		buffer.writeItemStack(recipe.result);
	}

	public interface IRecipeFactory<T extends SingleItemRecipe> {
		T create(ResourceLocation id, String group, Ingredient ingredient, ItemStack result);
	}
}
```

> Note: `PacketBuffer` is written and read from like a queue. Whatever information is written first will be read first.

From there, we can create our `RegistryObject` and call the serializer with a functional interface as a parameter for convience.

```java
public class TutorialRecipes {
	...
	public static final RegistryObject<AbstractSingleItemTimerRecipe.Serializer<WasherRecipe>> WASHER = RECIPES.register("washer", () -> new AbstractSingleItemTimerRecipe.Serializer<>(WasherRecipe::new));
}
```

Now we can start adding our own recipes to the game.

## <a name="data-generation"></a>Data Generation <a href="#data-generation"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Once again, laziness will get the better of me. So, I created a builder to generate recipes for me within our `RecipeProvider`. Once again, this will not be something I go over as it goes into the realm of [`Gson`](https://github.com/google/gson/blob/master/UserGuide.md). Feel free to take a read through and see if you can make sense of how JSON can write information to files using this. However, for the time being, I will just show you my implementation. It is relatively similar to that of Minecraft since it needs to output an `IFinishedRecipe` anyways.

```java
public class SingleItemTimerRecipeBuilder {

	private String group;
	private final Ingredient ingredient;
	private final int time;
	private final ItemStack result;
	private final Advancement.Builder advancementBuilder = Advancement.Builder.builder();
	private final AbstractSingleItemTimerRecipe.Serializer<?> recipeSerializer;

	private SingleItemTimerRecipeBuilder(Ingredient ingredient, ItemStack result, int time, AbstractSingleItemTimerRecipe.Serializer<?> serializer) {
		this.ingredient = ingredient;
		this.result = result;
		this.time = time;
		this.recipeSerializer = serializer;
	}

	public static SingleItemTimerRecipeBuilder recipe(Ingredient ingredient, ItemStack result, int time, AbstractSingleItemTimerRecipe.Serializer<?> serializer) {
		return new SingleItemTimerRecipeBuilder(ingredient, result, time, serializer);
	}

	public static SingleItemTimerRecipeBuilder washerRecipe(Ingredient ingredient, ItemStack result, int time) {
		return recipe(ingredient, result, time, TutorialRecipes.WASHER.get());
	}

	public static SingleItemTimerRecipeBuilder washerRecipe(Ingredient ingredient, IItemProvider result, int count, int time) {
		return recipe(ingredient, new ItemStack(result, count), time, TutorialRecipes.WASHER.get());
	}

	public static SingleItemTimerRecipeBuilder washerRecipe(Ingredient ingredient, IItemProvider result, int time) {
		return recipe(ingredient, new ItemStack(result), time, TutorialRecipes.WASHER.get());
	}

	public SingleItemTimerRecipeBuilder addCriterion(String name, ICriterionInstance criterion) {
		this.advancementBuilder.withCriterion(name, criterion);
		return this;
	}

	public SingleItemTimerRecipeBuilder setGroup(String group) {
		this.group = group;
		return this;
	}

	public void build(Consumer<IFinishedRecipe> consumerIn) {
		this.build(consumerIn, this.result.getItem().getRegistryName());
	}

	public void build(Consumer<IFinishedRecipe> consumerIn, String save) {
		ResourceLocation resourcelocation = this.result.getItem().getRegistryName();
		ResourceLocation resourcelocation1 = new ResourceLocation(save);
		if (resourcelocation1.equals(resourcelocation)) {
			throw new IllegalStateException("Recipe " + resourcelocation1 + " should remove its 'save' argument");
		} else {
			this.build(consumerIn, resourcelocation1);
		}
	}

	public void build(Consumer<IFinishedRecipe> consumerIn, ResourceLocation id) {
		this.validate(id);
		this.advancementBuilder.withParentId(new ResourceLocation("recipes/root")).withCriterion("has_the_recipe", RecipeUnlockedTrigger.func_235675_a_(id)).withRewards(AdvancementRewards.Builder.recipe(id)).withRequirementsStrategy(IRequirementsStrategy.OR);
		consumerIn.accept(new SingleItemTimerRecipeBuilder.Result(id, this.group == null ? "" : this.group, this.ingredient, this.time, this.result, this.advancementBuilder, new ResourceLocation(id.getNamespace(), "recipes/" + this.result.getItem().getGroup().getPath() + "/" + id.getPath()), this.recipeSerializer));
	}

	private void validate(ResourceLocation id) {
		if (this.advancementBuilder.getCriteria().isEmpty()) {
			throw new IllegalStateException("No way of obtaining recipe " + id);
		}
	}

	public static class Result implements IFinishedRecipe {

		private final ResourceLocation id;
		private final String group;
		private final Ingredient ingredient;
		private final ItemStack result;
		private final int time;
		private final Advancement.Builder advancementBuilder;
		private final ResourceLocation advancementId;
		private final IRecipeSerializer<? extends AbstractSingleItemTimerRecipe> serializer;

		public Result(ResourceLocation id, String group, Ingredient ingredient, int time, ItemStack result, Advancement.Builder advancementBuilder, ResourceLocation advancementId, IRecipeSerializer<? extends AbstractSingleItemTimerRecipe> serializer) {
			this.id = id;
			this.group = group;
			this.ingredient = ingredient;
			this.time = time;
			this.result = result;
			this.advancementBuilder = advancementBuilder;
			this.advancementId = advancementId;
			this.serializer = serializer;
		}

		@Override
		public void serialize(JsonObject json) {
			if(!this.group.isEmpty()) {
				json.addProperty("group", this.group);
			}
			
			json.add("ingredient", this.ingredient.serialize());
			json.addProperty("time", this.time);
			json.add("result", RecipeHelper.serializeItemStack(this.result));
		}

		@Override
		public ResourceLocation getID() {
			return this.id;
		}

		@Override
		public IRecipeSerializer<?> getSerializer() {
			return this.serializer;
		}

		@Override
		public JsonObject getAdvancementJson() {
			return this.advancementBuilder.serialize();
		}

		@Override
		public ResourceLocation getAdvancementID() {
			return this.advancementId;
		}
	}
}
```

## <a name="tag-importance"></a>Tag Importance <a href="#tag-importance"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

When creating anything, you should try and make the system as dynamic as possible. Especially if you are using a highly common item (like rubies). You should instead make your recipe take in a tag parameter for better compatibility between mods. Now, there are some sections where you want to create your own item/block that's unique. However, this should not be the case for every single thing. To deal with this situation, I have rewritten the `RecipeProvider` below to take in tag inputs if the item is generically common between mods. This goes for water buckets, rubies, and iron ingots. If I had a recipe that took in a nether star, I probably could just leave it as is. However, this implementation should be assumed in all cases. If there is a common generality, you should use the "forge" namespace instead of your own.

```java
public class TutorialRecipeProvider extends RecipeProvider implements IConditionBuilder {

	public TutorialRecipeProvider(DataGenerator generatorIn) {
		super(generatorIn);
	}

	@Override
	protected void registerRecipes(Consumer<IFinishedRecipe> consumer) {
		addOreSmeltingRecipes(consumer, TutorialTags.Items.ORES_RUBY, TutorialItems.RUBY.get(), 1.0f, 200);
		addBasicArmorRecipes(consumer, TutorialTags.Items.GEMS_RUBY, TutorialItems.RUBY_HELMET.get(), TutorialItems.RUBY_CHESTPLATE.get(), TutorialItems.RUBY_LEGGINGS.get(), TutorialItems.RUBY_BOOTS.get());
		ShapedRecipeBuilder.shapedRecipe(TutorialBlocks.WASHER.get()).key('I', Tags.Items.INGOTS_IRON).key('W', TutorialTags.Items.CONTAINER_WATER).patternLine("III").patternLine("IWI").patternLine("III").addCriterion("in_water", enteredBlock(Blocks.WATER)).build(consumer);
		addWasherRecipe(consumer, TutorialTags.Items.GEMS_RUBY, Items.EMERALD, 600, "gems");
	}
	...
	protected static void addWasherRecipe(Consumer<IFinishedRecipe> consumer, INamedTag<Item> tag, Item result, int time, String group) {
		SingleItemTimerRecipeBuilder.washerRecipe(Ingredient.fromTag(tag), result, time).setGroup(group).addCriterion("has_dirty_object", hasItem(tag)).build(consumer, new ResourceLocation(Tutorial.ID, result.getRegistryName().getPath() + "_from_washing"));
	}
}
```

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.70-web) under **Recipe**.
  
Back to [Intermediates](../../index#modding-202)  
Back to [Minecraft Tutorials](../../index)  