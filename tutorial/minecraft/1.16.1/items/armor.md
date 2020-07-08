# <a name="armor"></a>Armor
---

Hello warriors of the west! Welcome to your introduction to creating custom armor within Minecraft. In this tutorial, we will be going over a basic implementation of armor as well as how to customize your model to the fullest.

## <a name="registry-setup"></a>Registry Setup
---

Let's first go over how we are going to create our custom armor. Since our example `Item` was ruby, we will use for this example **ruby armor**.

<div style="text-align:center">
<img src="./images/ruby_helmet.png" alt="Ruby Helmet Texture" width="64" height="64" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
<img src="./images/ruby_chestplate.png" alt="Ruby Chestplate Texture" width="64" height="64" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
<img src="./images/ruby_leggings.png" alt="Ruby Leggings Texture" width="64" height="64" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
<img src="./images/ruby_boots.png" alt="Ruby Boots Texture" width="64" height="64" style="image-rendering: pixelated; image-rendering: -moz-crisp-edges; image-rendering: crisp-edges; object-fit: cover">
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
damageReductionAmountArrayIn | `int[]` | The damage mitigation amount starting from `EquipmentSlotType::FEET` and going to `EquipmentSlotType::HEAD``.
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



##TODO

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.57-web) under **Armor**.

Back to [Item Extensions](../../#item-extensions).
Back to [Minecraft Tutorials](../../)  