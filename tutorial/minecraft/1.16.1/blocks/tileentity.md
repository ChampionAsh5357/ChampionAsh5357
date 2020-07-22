# Tile Entities
---

Sometimes a [`BlockState`](./blockstate) cannot represent a block that well. The data could by highly dynamic, have custom animation, or just too variable. In that case, it's best to give a block some more information to store via a `TileEntity`. In this tutorial, we will be continuing on our washer making a simple way to store some items along with a timer.

## <a name="tileentity-class"></a>TileEntity Class <a href="#tileentity-class"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

A good reference to note is the one provided by [Forge](https://mcforge.readthedocs.io/en/latest/tileentities/tileentity/#tileentities). There might be a few things I gloss over that will probably be relevant there.

First things first, let's review all the methods provided by `TileEntity` and its extensions/implementations that might help us.

Class | Method | Use
--- | :---: | ---
`TileEntity` | `getWorld` | Gets the world.
`TileEntity` | `hasWorld` | Checks to see if this has a world.
`TileEntity` | `read` | Reads a stored NBT file.
`TileEntity` | `write` | Writes to a NBT file.
`TileEntity` | `readTileEntity` | Creates a `TileEntity` based on the `BlockState` and `CompoundNBT` passed in.
`TileEntity` | `markDirty` | Marks the block to save information during its appropriate cycle.
`TileEntity` | `getMaxRenderDistanceSquared` | Gets the render distance of the block.
`TileEntity` | `getPos` | Gets the current position of the block.
`TileEntity` | `getBlockState` | Gets the `BlockState` of the block.
`TileEntity` | `getUpdatePacket` | Gets the packet sent to the client whenever World::notifyBlockUpdate is called.
`TileEntity` | `getUpdateTag` | Gets the NBT file to sync with the client with `SChunkDataPacket`.
`TileEntity` | `isRemoved` | Checks to see if the `TileEntity` has been removed.
`TileEntity` | `remove` | Removes the `TileEntity` from the `Block`.
`TileEntity` | `validate` | Validates the `TileEntity`.
`TileEntity` | `receiveClientEvent` | Receives information from the client assuming `AbstractBlock::eventReceived` returns true on the server.
`TileEntity` | `updateContainingBlockInfo` | Updates the containing block.
`TileEntity` | `onlyOpsCanSetNbt` | Checks if players can use this tile entity to access operator (permission level 2) commands either directly or indirectly.
`TileEntity` | `rotate` | Rotates the information stored. Specific to side information.
`TileEntity` | `mirror` | Mirrors the information stored. Specific to side information.
`TileEntity` | `getType` | Gets the `TileEntityType`.
`CapabilityProvider` | `invalidateCaps` | Invalidates the capabilities on this `TileEntity`.
`CapabilityProvider` | `reviveCaps` | Revives the capabilities on this `TileEntity`.
`ICapabilityProvider` | `getCapability` | Gets the `LazyOptional` associated with the capability. Can be sided.
`ICapabilityProvider` | `getCapability` | Gets the `LazyOptional` associated with the capability.
`IForgeTileEntity` | `onDataPacket` | Called when you receive a TileEntityData packet for the location this `TileEntity` is currently in. On the client, the `NetworkManager` will always be the remote server. On the server, it will be whomever is responsible for sending the packet.
`IForgeTileEntity` | `handleUpdateTag` | Called when the chunk's `TileEntity` update tag is received on the client.
`IForgeTileEntity` | `getTileData` | Gets a `CompoundNBT` that can be used to store custom data for this tile entity. It will be written, and read from disc, so it persists over world saves.
`IForgeTileEntity` | `onChunkUnloaded` | Called when the chunk is about to be unloaded.
`IForgeTileEntity` | `onLoad` | Called when this is first added to the world.
`IForgeTileEntity` | `requestModelDataUpdate` | Requests a refresh for the model data.
`IForgeTileEntity` | `getModelData` | Allows you to return additional model data.
`ITickableTileEntity` | `tick` | Ticks a `TileEntity` to allow for progress and time-based data to be done.

> Note: The last method must be implemented on the class you wish to extend.

You will most likely involve these methods in some way or another when creating your `TileEntity`. Please note at this point, if you haven't already read through the Forge documentation, you might not understand why I choose to implement some methods and neglect others.

Now let's construct a basic class for our `TileEntity`.

```java
public class WasherTileEntity extends TileEntity {

	public WasherTileEntity(TileEntityType<?> tileEntityTypeIn) {
		super(tileEntityTypeIn);
	}
}
```

Before we go any further, we're actually going to switch around what class to implement. This is because we want to make it easier to store items in any `TileEntity` we want to create. All credit for the following class goes to [Choonster](https://github.com/Choonster-Minecraft-Mods/TestMod3/blob/1.14.4/src/main/java/choonster/testmod3/tileentity/ItemHandlerTileEntity.java).

```java
public abstract class ItemHandlerTileEntity<INVENTORY extends IItemHandler & INBTSerializable<CompoundNBT>> extends TileEntity {

	protected final INVENTORY inventory = createInventory();
	private final LazyOptional<INVENTORY> inventoryHolder = LazyOptional.of(() -> inventory);
	
	public ItemHandlerTileEntity(TileEntityType<?> tileEntityTypeIn) {
		super(tileEntityTypeIn);
	}
	
	protected abstract INVENTORY createInventory();
	
	public INVENTORY getInventory() {
		return inventory;
	}
	
	@Override
	public void read(BlockState stateIn, CompoundNBT nbtIn) {
		super.read(stateIn, nbtIn);
		inventory.deserializeNBT(nbtIn.getCompound("inventory"));
	}
	
	@Override
	public CompoundNBT write(CompoundNBT compound) {
		super.write(compound);
		compound.put("inventory", inventory.serializeNBT());
		return compound;
	}
	
	@Override
	protected void invalidateCaps() {
		super.invalidateCaps();
		inventoryHolder.invalidate();
	}
	
	@Override
	public <T> LazyOptional<T> getCapability(Capability<T> cap, Direction side) {
		if(cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY) {
			return inventoryHolder.cast();
		}
		return super.getCapability(cap, side);
	}
}
```

Let's break this down shall we? First, the capability about to be attached must be an instance of `IItemHandler` and serializable. Next, we have to read and write the item information to the `TileEntity`. We store an instance of the inventory in a `LazyOptional` for `ICapabilityProvider::getCapability`. We use `LazyOptional::cast` to make sure that the reference type is correct. After we remove the `TileEntity`, we want to invalidate the `LazyOptional` holder. This will already happen for the capability as long as we don't remove the super method. So instead, let's extend this class and pass in `ItemStackHandler` for the generic.

```java
public class WasherTileEntity extends ItemHandlerTileEntity<ItemStackHandler> {

	public WasherTileEntity(TileEntityType<?> tileEntityTypeIn) {
		super(tileEntityTypeIn);
	}

	@Override
	protected ItemStackHandler createInventory() {
		return null;
	}
}
```

So now we have to create our inventory. Well in my case, I would like the inventory to have nine slots. Eight will be used to store to items to be washed, and one will be used to hold a water bucket. For expansion sake, this will be an item tag instead when referenced.

I will also need one more variable to determine how long the washing machine will cycle and the ability to tick to count down the cycle length. How to calculate the washing machine cycle will be expanded upon later.

```java
public class WasherTileEntity extends ItemHandlerTileEntity<ItemStackHandler> implements ITickableTileEntity {

	private int cycleTime;
	...
	@Override
	public void tick() {
	}
	
	@Override
	protected ItemStackHandler createInventory() {
		return new ItemStackHandler(9);
	}
	
	@Override
	public void read(BlockState stateIn, CompoundNBT nbtIn) {
		super.read(stateIn, nbtIn);
		cycleTime = nbtIn.getInt("cycleTime");
	}
	
	@Override
	public CompoundNBT write(CompoundNBT compound) {
		super.write(compound);
		compound.putInt("cycleTime", cycleTime);
		return compound;
	}	
}
```

This will be all the code we write for right now. Most of this information will be written as we go through more of the use cases and logic for our code. However, this will not be happening here.

## <a name="tileentitytype-registry"></a>TileEntityType Registry <a href="#tileentitytype-registry"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Once again, we have to create a [`DeferredRegister`](../introduction/registries#deferredregister) for our `TileEntity`. However, `TileEntity` is not the registry object. It is merely a class used to store information. The class used to create the `TileEntity` for specific blocks is `TileEntityType`. It holds a generic of the `TileEntity` to be used.

To create our `TileEntityType`, we need need to call it through its builder. It takes in a supplier and some blocks it can be stored in. To build a the object, it requires a data fixer to be put in as a parameter. This only matter if your mod existed before 1.12 or has some changes that require information to be remapped. In our case, we can just pass in null. As for the supplier, we have to remove the parameter in our `TileEntity` constructor and pass in the type directly to use our double colon operator.

```java
public class TutorialTileEntities {
	...
	public static final RegistryObject<TileEntityType<WasherTileEntity>> WASHER = TILE_ENTITIES.register("washer", () -> TileEntityType.Builder.create(WasherTileEntity::new, TutorialBlocks.WASHER.get()).build(null));
}
```

```java
public class WasherTileEntity extends ItemHandlerTileEntity<ItemStackHandler> implements ITickableTileEntity {
	...
	public WasherTileEntity() {
		super(TutorialTileEntities.WASHER.get());
	}
	...
}
```

## <a name="block-implementation"></a>Block Implementation <a href="#block-implementation"><img src="../../../../images/link.png" alt="Link" style="width:20px;height:20px;"></a>
---

Now all we have to do is attach the `TileEntity` to our block using `IForgeBlock::hasTileEntity` and `IForgeBlock::createTileEntity`. Since we want it for all states and are not passing in any variables, we can return true and the basic constructor respectively.

```java
public class WasherBlock extends Block {
	...
	@Override
	public boolean hasTileEntity(BlockState state) {
		return true;
	}
	
	@Override
	public TileEntity createTileEntity(BlockState state, IBlockReader world) {
		return new WasherTileEntity();
	}
}
```

Now our `TileEntity` is within the game. However, to be able to get information from it, we are going to need to create a [`Container` and `Screen`](../../index#not-found). Since we also want to have some recipes for our washer, we need to create a custom [`IRecipe`](../../index#not-found) and [tag](../datagen/tags) as well.

---
All files are uploaded to the [GitHub](https://github.com/ChampionAsh5357/1.16.x-Minecraft-Tutorial/tree/1.16.1-32.0.70-web) under **TileEntity**.

Review [Blocks](../basic/blocks)  
Back to [Block Extensions](../../index#block-extensions)  
Back to [Minecraft Tutorials](../../index)  