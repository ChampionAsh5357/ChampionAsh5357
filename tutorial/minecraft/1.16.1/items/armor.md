# <a name="armor"></a>Armor
---

Hello warriors of the west! Welcome to your introduction to creating custom armor within Minecraft. In this tutorial, we will be going over a basic implementation of armor as well as how to customize your model to the fullest.

## <a name="registry-setup"></a>Registry Setup
---

Let's first go over how we are going to create our custom armor. Since our example `Item` was ruby, we will use for this example **ruby armor**.

<div style="text-align:center">
<img src="./images/ruby_helmet.png" alt="Ruby Helmet Texture" width="128" height="128" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
<img src="./images/ruby_chestplate.png" alt="Ruby Chestplate Texture" width="128" height="128" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
<img src="./images/ruby_leggings.png" alt="Ruby Leggings Texture" width="128" height="128" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
<img src="./images/ruby_boots.png" alt="Ruby Boots Texture" width="128" height="128" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
</div>

### <a name="iarmormaterial"></a>IArmorMaterial

All `ArmorItem`s take in an interface known as `IArmorMaterial`. This holds all stats about the armor from its enchantability to its durability to its repair material. By vanilla standards, `IArmorMaterial` is implemented on the enum `ArmorMaterial` and called from there. Although the enum is not extensible, there is no specific case where `ArmorMaterial` is cast to the code. This allows us to create our own enum `TutorialArmorMaterial` that implements `IArmorMaterial`:

```java
public enum TutorialArmorMaterial implements IArmorMaterial {
	;

	@Override
	public int getDurability(EquipmentSlotType slotIn) {
		return 0;
	}

	@Override
	public int getDamageReductionAmount(EquipmentSlotType slotIn) {
		return 0;
	}

	@Override
	public int getEnchantability() {
		return 0;
	}

	@Override
	public SoundEvent getSoundEvent() {
		return null;
	}

	@Override
	public Ingredient getRepairMaterial() {
		return null;
	}

	@Override
	public String getName() {
		return null;
	}

	@Override
	public float getToughness() {
		return 0;
	}

	@Override
	public float func_230304_f_() {
		return 0;
	}
}
```

Here is a quick overiew of all the functions in `IArmorMaterial`:

Method | Parameter(s) | Return Type | Use
--- | :---: | :---: | ---
`getDurability` | `EquipmentSlotType` slotIn | `int` | Returns the durability of the material specific to the equipment slot.
`getDamageReductionAmount` | `EquipmentSlotType` slotIn | `int` | Returns the damage mitigated specific to the equipment slot.
`getEnchantability` | NONE | `int` | Returns the enchantability of the material.
`getSoundEvent` | NONE | `SoundEvent` | Returns the equip sound of the material.
`getRepairMaterial` | NONE | `Ingredient` | Returns the repair material(s) of the material. This depends on how many `Item`s are passed into the `Ingredient` static intiailizer.
`getName` | NONE | `String` | Returns the name associated with the location of the `ArmorItem` texture. For example, if the method returned `domain:location`, this would map to `domain:textures/models/armor/location_layer_(1/2).png`.
`getToughness` | NONE | `float` | Returns the toughness of the material.
`getKnockbackResistance (func_230304_f_)` | NONE | `float` | Returns the knockback resistance of the material.

> Note: Minecraft uses a integer array to calculate its durability to use consistent ratios. This can be located at `ArmorMaterial::MAX_DAMAGE_ARRAY`.

Since we will be using most of Minecraft's code (with some small changes in name mappings and class usage), we can just copy it over:

```java
package io.github.championash5357.tutorial.item;

import java.util.function.Supplier;

import net.minecraft.inventory.EquipmentSlotType;
import net.minecraft.item.IArmorMaterial;
import net.minecraft.item.crafting.Ingredient;
import net.minecraft.util.SoundEvent;
import net.minecraftforge.api.distmarker.Dist;
import net.minecraftforge.api.distmarker.OnlyIn;
import net.minecraftforge.common.util.Lazy;

public enum TutorialArmorMaterial implements IArmorMaterial {
	;

	private static final int[] MAX_DAMAGE_ARRAY = new int[]{13, 15, 16, 11};
	private final String name;
	private final int maxDamageFactor;
	private final int[] damageReductionAmountArray;
	private final int enchantability;
	private final SoundEvent soundEvent;
	private final float toughness;
	private final float knockbackResistance;
	private final Lazy<Ingredient> repairMaterialLazy;

	private TutorialArmorMaterial(String nameIn, int maxDamageFactorIn, int[] damageReductionAmountArrayIn, int enchantabilityIn, SoundEvent soundEventIn, float toughnessIn, float knockbackResistanceIn, Supplier<Ingredient> repairMaterialSupplier) {
		this.name = Tutorial.ID + ":" + nameIn;
		this.maxDamageFactor = maxDamageFactorIn;
		this.damageReductionAmountArray = damageReductionAmountArrayIn;
		this.enchantability = enchantabilityIn;
		this.soundEvent = soundEventIn;
		this.toughness = toughnessIn;
		this.knockbackResistance = knockbackResistanceIn;
		this.repairMaterialLazy = Lazy.concurrentOf(repairMaterialSupplier);
	}

	@Override
	public int getDurability(EquipmentSlotType slotIn) {
		return MAX_DAMAGE_ARRAY[slotIn.getIndex()] * this.maxDamageFactor;
	}

	@Override
	public int getDamageReductionAmount(EquipmentSlotType slotIn) {
		return this.damageReductionAmountArray[slotIn.getIndex()];
	}

	@Override
	public int getEnchantability() {
		return this.enchantability;
	}

	@Override
	public SoundEvent getSoundEvent() {
		return this.soundEvent;
	}

	@Override
	public Ingredient getRepairMaterial() {
		return this.repairMaterialLazy.get();
	}

	@OnlyIn(Dist.CLIENT)
	public String getName() {
		return this.name;
	}

	@Override
	public float getToughness() {
		return this.toughness;
	}

	@Override
	public float func_230304_f_() {
		return this.knockbackResistance;
	}
}
```

> Note: The main changes occur in `ArmorMaterial::field_234660_o_` being renamed to `TutorialArmorMaterial::knockbackResistance` and `LazyValue<Ingredient>` being replaced with `Lazy<Ingredient>` and `Lazy::concurrentOf` to make the implementation [thread-safe](https://www.baeldung.com/java-thread-safety).

From there, all we have to do is define our armor using our constructor. In our example, I am going to make our ruby armor sit between iron and diamond with some minor benefits such as increased enchantability.

While I implement this, here's a breakdown of the constructor:

Variable | Type | Use
--- | :---: | ---
nameIn | `String` | The name of the material.
maxDamageFactorIn | `int` | The durability factor of the material.
damageReductionAmountArrayIn | `int[]` | The damage mitigation amount starting from `EquipmentSlotType::FEET` and going to `EquipmentSlotType::HEAD`.
enchantabilityIn | `int` | The enchantability of the material.
soundEventIn | `SoundEvent` | The equip sound of the material.
toughnessIn | `float` | The toughness of the material.
knockbackResistanceIn | `float` | The knockback resistance of the material.
repairMaterialSupplier | `Supplier<Ingredient>` | A supplier holding the repair `Item`(s) of the material.

> Note: When we pass in the name of the material, we prefix it with our mod id and a colon. This is so that our armor model is found in the correct location.

```java
public enum TutorialArmorMaterial implements IArmorMaterial {
	RUBY("ruby", 30, new int[] {2, 6, 7, 3}, 15, SoundEvents.ITEM_ARMOR_EQUIP_GENERIC, 1.0F, 0.0F, () -> Ingredient.fromItems(TutorialItems.RUBY.get()));
	...
}
```

### <a name="item-registry"></a>Item Registry

From here, we are now able to register our item once again using our [DeferredRegister](../basic/items#registry-setup). We will create an item for each `EquipmentSlotType` (`HEAD`, `CHEST`, `LEGS`, `FEET`) and group them within `ItemGroup::COMBAT`.

```java
public class TutorialItems {
	...
	public static final RegistryObject<Item> RUBY_HELMET = ITEMS.register("ruby_helmet", () -> new ArmorItem(TutorialArmorMaterial.RUBY, EquipmentSlotType.HEAD, new Item.Properties().group(ItemGroup.COMBAT)));
	public static final RegistryObject<Item> RUBY_CHESTPLATE = ITEMS.register("ruby_chestplate", () -> new ArmorItem(TutorialArmorMaterial.RUBY, EquipmentSlotType.CHEST, new Item.Properties().group(ItemGroup.COMBAT)));
	public static final RegistryObject<Item> RUBY_LEGGINGS = ITEMS.register("ruby_leggings", () -> new ArmorItem(TutorialArmorMaterial.RUBY, EquipmentSlotType.LEGS, new Item.Properties().group(ItemGroup.COMBAT)));
	public static final RegistryObject<Item> RUBY_BOOTS = ITEMS.register("ruby_boots", () -> new ArmorItem(TutorialArmorMaterial.RUBY, EquipmentSlotType.FEET, new Item.Properties().group(ItemGroup.COMBAT)));
}
```

From there all we need to is setup our [resources](../basic/items#resource-setup) and viola our armor should be in the game, right?

![Basic Armor Missing Model Textures](./images/armor_basic_before.png)

Well everything seems to be rendering...except for, you know, the armor model. This is because armor models aren't handled by a simple texture, but are rather a layer applied via a `BipedArmorLayer` and texture using an `BipedModel` format.

## <a name="extra-resouces"></a>Extra Resources
---

So, now we neeed to create the `BipedModel` textures for our `ArmorItem`.

### <a name="armor-layer-1-and-2"></a>Armor Layer 1 and 2

All armor model textures has four layers: `armor_layer_1`, `armor_layer_2`, `armor_layer_1_overlay`, and `armor_layer_2_overlay`. Layer 1 is scaled normally and usually holds the head, chest, and feet textures. Layer 2 is scaled to half size and usually holds the legs texture. Layer 1 and Layer 2 are always rendered with the default `BipedModel`. Layer 1 Overlay and Layer 2 Overlay are only rendered if the `ArmorItem` used is implemented with `IDyeableArmorItem`. These two are usually used for rendering dynamic color on the armor model.

To be able to create our armor textures, we are going to need to edit a `BipedModel`texture. Now you can use any texture editor you want. However, for a better visualization, I will be using [Blockbench](https://blockbench.net/) to show off these textures on a model.

Currently, I am going to edit the skin of a zombie. A zombie uses the standard `BipedModel` allowing us to do a direct trace of the entity to an armor model. Opening up the program and doing the configurations (opening the `Skin` tab, setting the model to `Zombie` at `16x`, adjusting the model to correctly represent the `BipedModel` class) gives me this:

![Blockbench Armor Default](./images/blockbench_armor_default.png)

From here I can paint my textures directly into the program and see the result. Since I want to keep the continuity of Minecraft's armor setup, I will just use the default Layer 1 and Layer 2 setup and adjust accordingly. Using this, I can create my armor textures:

<div style="text-align:center">
<img src="./images/ruby_layer_1.png" alt="Ruby Layer 1 Texture" width="256" height="128" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
<img src="./images/ruby_layer_2.png" alt="Ruby Layer 2 Texture" width="256" height="128" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
</div>

<div style="text-align:center">
<img src="./images/ruby_layer_1_mapped.png" alt="Ruby Layer 1 Texture Mapped" width="225" height="400" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
<img src="./images/ruby_layer_2_mapped.png" alt="Ruby Layer 2 Texture Mapped" width="225" height="400" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
</div>

To get these textures to render on the model, I need to save them as `material_layer_1.png` and `material_layer_2.png` where material is our `IArmorMaterial` name (e.g. `ruby`). Since they are not technically items nor entities, they have their own special location to be saved. So let's once again open up our file tree all the way down to `assets/tutorial/textures`:

```
assets/tutorial/textures
└── item
```

We need to add a new subdirectory `models/armor` and save our two textures in there.

```
assets/tutorial/textures
├── item
└── models
	└── armor
		├── ruby_layer_1.png
		└── ruby_layer_2.png
```

Now if we go back into the game, we should see our model fully loaded on our player.

![Basic Armor with Textures](./images/armor_basic_after.png)

And now we have our armor within the game!

## <a name="custom-armor-model"></a>Custom Armor Model
---

Custom Armor Models are one of the more difficult things to implement due to a lack of understanding and consistency between programmers. Today, I will attempt my best explanation of how to correctly implement and create a custom armor model for your player.

There will be two methods explaining how to do this. The first method assumes you will be using some semblance of the original Minecraft armor model. The second method will be if you decided to scrap how Minecraft renders its armor altogether and make your own from scratch.

### <a name="method-1"></a>Method 1: Child Models

Child Models are by far the most popular method in rendering an armor model in Minecraft. However, the amount of detail required to correctly render one takes a bit of detail and effort.

#### <a name="armor-model"></a>Armor Model

First, let's create our custom model. I'm going to build it off our ruby armor and just add some spikes coming out of the back of the armor. I will be using [Blockbench](https://blockbench.net/) once again for this.

There are a few things that you need to consider when creating your model:  
**Box Size** - This is one of the biggest issues I see when people include custom models. They just add boxes and dont necessarily think of the consequences. Whenever you add a box into the game, if the number isn't an integer, then the texture will scale up to the size required to correct the ratio discrepency. So, if I had a box with the dimensions of 1, 1, and 1.5. From the standard texture of 64x32, I would need a texture of 128x64 to correctly render my box. Box sizes of 0 also are an issue as they just don't exist. A box dimension must be at least 0.005 to correctly render on the screen with no flickering.  
**Box Position** - Just because they are floats doesn't mean you can you can a position of 1.4348927f. After a certain point the model movement will show absolutely nothing. Please try to stick with a precision of at most 0.001f.  
**ModelRenderer Sanity** - There are two major distinctions of `ModelRenderer`s in Minecraft: Global `ModelRenderer`s and Local `ModelRenderer`s. In any example where your `ModelRenderer` is becoming a child, it should be a local `ModelRenderer`. Local `ModelRenderer`s are inaccessible outside of the constructor. However, since they inherit all their traits from the parent, it makes absolutely no difference since the data will be copied corresponding to its parent. In the case of Global `ModelRenderer`s, they should never be put as a child. In that case, copy the model angles of the part you want to mimic and then supply it in either `AgeableModel::getHeadParts` or `AgeableMode::getBodyParts` where applicable. There should be absolutely no reason to touch `Model::render` **AT ALL**. We must make sure that every box or `ModelRenderer` we create that they are always are in a sub directory of one of `BipedModel`'s `ModelRenderer`s.

![Blockbench Subdirectory](./images/blockbench_directory.png)

As you can see, each folder represents a `ModelRenderer` while each cube represents a box. All of my added data is within a subdirectory of one of `BipedModel`'s `ModelRenderer`s.  
**Texture Information** - If you decide to change the texture size for you model, please pay close attention. In respect to the set texture width and size, the first 64x32 pixels starting at the top left corner are reserved for the `BipedModel` `ModelRenderer`s. If you decide to add any extra texture data, make sure it is not within those bounds or else you will render some of your custom texture on the default `BipedModel`. Your default texture size will have to be larger than 64x32 if you would like to render any other texture to your model.  
**EquipmentSlotType Sanity** - As mentioned [previously](#armor-layer-1-and-2), the legs are rendered at half size compared to the rest of the model. If you add anything specifically for the legs, you do need to take this into consideration and create the model separately. Of course, you could always go the one size fits all route, but then you would lose the point of having two layers for the armor.

From there, you can export your [textures](#armor-layer-1-and-2) and the completed model. Make sure you name the textures `material_layer_(1/2).png` where material is your `IArmorMaterial` in `textures/models/armor`. 

<div style="text-align:center">
<img src="./images/ruby_layer_1_custom.png" alt="Custom Ruby Layer 1 Texture" width="256" height="256" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
<img src="./images/ruby_layer_2_custom.png" alt="Custom Ruby Layer 2 Texture" width="256" height="256" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
</div>

<div style="text-align:center">
<img src="./images/ruby_layer_1_custom_mapped.png" alt="Custom Ruby Layer 1 Texture Mapped" width="220" height="420" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
<img src="./images/ruby_layer_2_custom_mapped.png" alt="Custom Ruby Layer 2 Texture Mapped" width="220" height="420" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
</div>

Your model class will be saved to your client folder. Since I prefer to model my files similar to Minecraft, I will store my armor file as `RubyArmorModel` in `client.renderer.entity.model`.

```
src/main/java/io/github/championash5357/tutorial/client
├── client
│	├── proxy
│	└── renderer
│		└── entity
│			└── model
│				└── RubyArmorModel.java
├── init
├── item
├── proxy
├── server
└── Tutorial.java
```

#### <a name="model-class-cleanup"></a>Model Class Cleanup

### Method 2: Global ModelRenderers

##TODO

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.57-web) under **Armor**.

Back to [Item Extensions](../../#item-extensions).
Back to [Minecraft Tutorials](../../)  