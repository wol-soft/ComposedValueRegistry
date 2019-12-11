# ComposedValueRegistry

Decouple numbers with multiple modifiers.

Installation via NPM:

```
npm i composed-value-registry
```

Incrementals often have multiple currencies. Each of this currencies contains a logic how to calculate the current production rate (In most games the logic fetches data from different components of the game, eg. upgrades etc.). Let's say we develop a wood-themed incremental and we plant X trees per second. When adding additional features which affect the production rate of a given currency, eg. an automation which automatically plants trees (in a separate module to decouple the code and don't mess everything up) based on logic evaluated in the module, the core logic which calculates the trees planted per second needs to know about the trees added by the automation. Consequently the core logic depends the module, it needs to know all modules which modify the trees per second to calculate the correct total value.

How about decouple the value from the core module and let other modules register modifiers for the value. The core module registers all modifiers it's responsible for at the ComposedValue holding the final value for plantet trees per second and all modules, minigames etc. which provide features also modifying the amount of plantet trees register their modifiers independent from the core module. The core module doesn't even know which modules register (great possibility also for addons or other extensions to hook into the value calculation).

A ComposedValue is an object with a defined (and documented) chainable interface. All values are collected in the ComposedValueRegistry. To get a ComposedValue we simply call the registry:

```js
const composedValue = ComposedValueRegistry.getComposedValue('tree_plant_rate');
```

To add a modifier we use the method *addModifier* (as all modifiers are multiplicative calculated together also the base production is a modifier):

```js
composedValue
    .addModifier('tree_plant_rate__base_rate', () => 10)
    .addModifier('tree_plant_rate__upgrade_boost', () => 1)
    .onValueChange((newPlantRate) => $('#tree_plant_rate').text(newPlantRate));
```

In our automation module we want to double the plant rate (as it's a callback function there will be more complex logic casually, not only constant values):

```js
ComposedValueRegistry.getComposedValue('tree_plant_rate').addModifier('automation__tree_plant_rate', () => 2);
```

The complete API documentation of a ComposedValue can be found at the [docs](https://wol-soft.de/apps/beerplop/wiki/index.php/Composed_Value_Registry).