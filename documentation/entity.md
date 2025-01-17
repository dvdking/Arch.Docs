---
description: >-
  You make your way through the thicket and suddenly there's an entity... but
  what is it?
---

# Entity

In an [entity component system (ECS)](concepts.md), an entity is simply a **unique ID** tag. It represents **an object** in the game or application, but has no logic or data itself. The data and behavior come from **components** that are attached to this entity.

It is nothing more than a simple `record struct Entity{ int ID, ... }`. Either you work directly on this entity and its methods, or you simply use `PURE_ECS`. More on this later.

## Creating life

I'm not a dark mage, but I can tell you a little about how to create life. It's actually surprisingly simple... Let's take a look!

<pre class="language-csharp"><code class="lang-csharp">var world = World.Create(); // Must exist somewhere

// A dwarf with a string, position and velocity component was born and killed
<strong>var dwarf = world.Create(new Dwarf("Gimli)", new Position(0,0), new Velocity());
</strong><strong>if(dwarf.IsAlive()) world.Destroy(dwarf);
</strong></code></pre>

{% hint style="info" %}
Components may vary and this example uses generics, if you don't feel like using generics, there are still plenty of APIs without them.
{% endhint %}

It can be that simple. How does it feel to be a god?

## Changing lives

So you don't stop at changing lives, do you? Entities can be changed in a variety of ways with one or two spells.

<pre class="language-csharp"><code class="lang-csharp">// Create our beloved dwarf
var dwarf = world.Create(new Dwarf("Gimli"), new Position(0,0), new Velocity());

// Telepor the dwarf
ref var position = ref <a data-footnote-ref href="#user-content-fn-1">dwarf.Get&#x3C;Position>;</a>
position.X++;
position.Y++;

// Force him to move on its own
dwarf.Set(new Velocity(1,1));

// Give the dwarf a pickaxe and make him sad by taking it away
if(!dwarf.Has&#x3C;Pickaxe>) <a data-footnote-ref href="#user-content-fn-2">dwarf.Add(new Pickaxe());</a>
dwarf.Remove&#x3C;Pickaxe>();
</code></pre>

{% hint style="warning" %}
The safety check using `.Has<T>()` is important. Arch does not do this itself because because our mantra is raw performance and we trust you as a developer to know when to check and when not to.
{% endhint %}

Nah great, I think you've really made the dwarf angry. Nobody takes a pickaxe away from a dwarf. Let's try to make up for it.

```csharp
// Adding components to our dwarf in batch (We could also pass them as parameters)
dwarf.Add<Pickaxe, DwarfArmor, LeatherBoots, IronHelmet>();

// Receiving components in batch and modifying them
var equipment = dwarf.Get<Pickaxe, DwarfArmor, LeatherBoots, IronHelmet>();
equipment.t0.Durability = 100f;
equipment.t1.Defence = 100f;
equipment.t3.Weight = 0.5f;

// Overwriting components since, why not? 
dwarf.Set(new DwarfArmor(110f), new LeatherBoots(Sole.GRIPPY));

// Dwarf happy now, lets ensure he does not wear any other gear by removing it
if(dwarf.Has<Bow, ElvishArmor, LongWhiteHair>()){
    dwarf.Remove<Bow, ElvishArmor, LongWhiteHair>();
}
```

{% hint style="info" %}
Up to 25 generic overloads are available, order does not matter. This often saves code and is often faster.
{% endhint %}

Looks like you've made the gnome happy again. Let's take a closer look at him then!

## Dissecting our creature

We have created life, changed it, killed it. What is still missing? Looking at it.

```csharp
var types = dwarf.GetComponentTypes();       // Getting all types, readonly
var components = dwarf.GetComponents();      // Getting all components by boxing
Console.WriteLine(dwarf);                    // Printing the entity
```

{% hint style="info" %}
The entity also has a debug view which reveals even more information.
{% endhint %}



[^1]: `TryGet`also exists.

[^2]: \`Ensure\` also exists.
