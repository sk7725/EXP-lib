# EXP Library   

+ EXP-LIB 3.2 by sk7725!    
   
Makes blocks store EXP and level up, and makes its base stats change accordingly.   
Recommended for turrets or perhaps crafters.   
EXP calculation: `Level = [sqrt(Exp * 0.1)]`   

# Functions   

### extend(Block block, Building building, string name, Object obj, Object objBuild)   

`<Location: module.export>`   
Use instead of extendContent for the block you want to extend.   
Returns the block.   

+ **block**: the block you want to extend.   
+ **building**: the building of the block you want to extend.   
+ **name**: the block name.   
+ **obj**: the object that will extend block.   
+ **objBuild**: the object that will extend building.   

### setExp(int exp)   

`<Location: Building>`   
Sets the building's exp.   

### incExp(int exp)   

`<Location: Building>`   
Increments the building's exp. Clamped by `maxLevel` automatically.   

### totalExp()   

`<Location: Building>`   
Returns the building's exp.   

### totalLevel()   

`<Location: Building>`   
Returns the building's level.   

### expf()   

`<Location: Building>`   
Returns the building's `exp / maximum exp required to level up`, ranges from 0 to 1.   

### levelf()   

`<Location: Building>`   
Returns the building's `exp / maximum exp at max level`, ranges from 0 to 1.   

### getCache(string fieldName)   

`<Location: Building>`   
Returns the building's cached field, if it is cached.   

### currentUpgrades(int lvl)   

`<Location: Building>`   
Returns an array of possible `UpgradeObject`s in that level.   


# How To Use   

Put in the correct fields to assign what fields should be increased per level.   
Use `this.setExp()` and `this.incExp()` in the functions of the building to increase the exp of the block when you want.   
Here are some useful tips:   
+ **`updateTile()`, `read()`, `write()` will be overridden.** Use `customUpdate()`, `customRead()`, `customWrite()` instead.   
+ Define `levelUp(int level){}` in `objBuild` to do something after levelling up(**`hasLevelEffect` must be `true`**).   

## Fields   

Fields should be passed in `obj` of `extend`.    
`this` is the default value for each field.   
   
+ **maxLevel** `20`   
The maximum level the block reaches.   
+ **expFields** `[]`   
An array of `FieldCalculation`s, determining the fields that change based on level.   
+ **level0Color** `ffd37f`   
The color of the level bar at level 0.   
+ **levelMaxColor** `f3e979`   
The color of the level bar at max level.   
+ **exp0Color** `84ff00`   
The color of the exp bar at exp 0.   
+ **expMaxColor** `90ff00`   
The color of the exp bar at max exp.   
+ **hasLevelEffect** `true`   
Whether to check if a block levels up every time it gains experience. `false` is recommended for blocks that frequently/consistently gain experience to reduce lag. If a block has upgrades, `hasLevelEffect` will be overridden to `true`.   
+ **levelUpFx** `Fx.upgradeCore`   
The effect to create when levelling up. `hasLevelEffect` must be `true`.   
+ **levelUpSound** `Sounds.message`   
The sound to play when levelling up. `hasLevelEffect` must be `true`.   
+ **upgrades** `[]`   
An array of `UpgradeObject`s, determining the possible block upgrades.   
+ **upgradeFx** `module.export.upgradeFx`   
The effect to create when upgrading.   
+ **upgradeSparkleFx** `module.export.sparkleFx`   
The effect to create when a new upgrade is available.   
+ **upgradeSound** `Sounds.place`   
The sound to play when upgrading.   
+ **upgradeColor** `00ff00`   
The color of upgrading.   
+ **sparkleChance** `0.08`   
The intensity of the 'new upgrade available' sparkle.   
+ **useStringSync** `false`   
Use this if this block's Integer config is reserved, as upgrades use Integer config. Not recommended.   


### FieldCalculation   

A `FieldCalculation` is an object telling which field to calculate in what way.   
An example of a valid `FieldCalculation`:   

```
{
    type: "linear",
    field: "reloadTime",
    start: 60,
    intensity: -5,
    cacheValue: false
}
```

+ **field**   
The field of the block that should be calculated and changed.   
+ **type** `"linear"`   
The type of the calculation.   
`"linear"`: Level \* **intensity** + **start**   
`"exp"`: **intensity** ^ Level + **start**   
`"root"`: sqrt(Level \* **intensity**) + **start**   
`"bool"`: Special type; **start** should be a boolean, not an integer.   
If `Level >= intensity`, the field will change from **start** to !**start**.   
`"list"`: Special type; **intensity** should be a list, not an integer.   
The field will be `intensity[lvl]`, **start** is irrelevant.   
+ **intensity** `1`   
+ **start** `The default field's value`   
+ **cacheValue** `false`
Whether to store this value in each Building. If this is true, the value cached can be retrieved by `getCache(field)`. Use this to make use of the calculated field outside of `updateTile()`, where the `FieldCalculation` has no(or weird) effect.  

### UpgradeObject   

An `UpgradeObject` is an object containing upgrade information.   
An example of a valid `UpgradeObject`:   

```
{
    block: Blocks.meltdown,
    min: 5,
    max: 7
}
```

+ **block**   
The resulting `Block` type of this upgrade.   
+ **min** `maxLevel`   
The upgrade will be available if `lvl >= min`.  
+ **max** `INT_MAX`   
The upgrade will be available if `lvl <= max`.  

Note that the block size does not have to be the same; if the blocks differ in size, it will try to occupy a large enough space around it, and fails in none is found.

# Example   

```
const lib = require("exp-lib/library");

const inferno = lib.extend(ItemTurret, ItemTurret.ItemTurretBuild, "inferno", {
  maxLevel: 10,
  //Which fields to increase in what way.
  expFields: [
    {
      type: "linear",
      field: "reloadTime",
      start: 60,
      intensity: -5
    }
  ],
  upgrades: [
    {
      block: Vars.content.getByName(ContentType.block, "exp-lib-infernia"),
      min: 10
    }
  ],
  //The original Block extension object.
  init(){
		this.super$init();
		this.ammo(Items.coal, Bullets.basicFlame, Items.pyratite, Bullets.pyraFlame);
	}
}, {
  //The original Building extension object.
  shoot(type){
    //Increment EXP, replace this with whenever you want the block to gain EXP.
    this.incExp(20);
    print("Reload: "+inferno.reloadTime);
    this.super$shoot(type);
  },
  levelUp(level){
    print("You leveled up to level " + level);
  }
});
```
