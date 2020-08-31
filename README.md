# EXP Library   

+ EXP-LIB by sk7725!   
+ Related files: `exp.js`   
   
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

### getExp()   

`<Location: Building>`   
Returns the building's exp.   

# How To Use   

Put in the correct fields to assign what fields should be increased per level.   
Use `this.setExp()` and `this.incExp()` in the functions of the building to increase the exp of the block when you want.   

## Fields   

Fields should be passed in `obj` of `extend`.    
`this` is the default value for each field.   
   
+ **maxLevel** `20`   
The maximum level the block reaches.   
+ **expFields** `[]`   
An array of `FieldCalculation`s, determining the fields that change based on level.   
+ **level0Color** `ffd37f`   
The color of the level bar at level 0.   
+ **levelMaxColor** `ffffff`   
The color of the level bar at max level.   
+ **exp0Color** `84ff00`   
The color of the exp bar at exp 0.   
+ **expMaxColor** `84ff00`   
The color of the exp bar at max exp.   

### FieldCalculation   

A `FieldCalculation ` is an object telling which field to calculate in what way.   
An example of a valid `FieldCalculation`:   

```
{
    type: "linear",
    field: "reloadTime",
    start: 60,
    intensity: -5
}
```

+ **field**   
The field of the block that should be calculated and changed.   
+ **type** `"linear"`   
The type of the calculation.   
`"linear"`: Level \* **intensity** + **start**   
`"exp"`: **intensity** ^ Level + **start**   
`"root"`: sqrt(Level \* **intensity**) + **start**   
+ **intensity** `1`   
+ **start** `0`   

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
  }
});
```
