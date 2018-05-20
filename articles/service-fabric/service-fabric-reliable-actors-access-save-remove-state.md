---
title: Azure Service Fabric állapot kezelése |} Microsoft Docs
description: Megtudhatja, hogyan elérését, mentse, és távolítsa el a Service Fabric Reliable Actors állapotát.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: ac3afe144b9cf9e2fb307087edb175a603ffe4e9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Hozzáférés, mentse, és távolítsa el a Reliable Actors állapota
[Reliable Actors](service-fabric-reliable-actors-introduction.md) egyszálas objektum, amely megbízhatóan-állapot karbantartásához és beágyazására egyaránt programot, és állapotát. Minden szereplő példány rendelkezik saját [állapotkezelője](service-fabric-reliable-actors-state-management.md): a szótár-szerű adatszerkezet, amely megbízhatóan tárolja az kulcs/érték párok. Az állapotkezelő a állapotszolgáltató csomagolásának. Függetlenül attól, amely adatok tárolására használható [adatmegőrzési beállítás](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) szolgál.

Állapot manager kulcsok karakterláncoknak kell lenniük. Értékek általános és állhat bármilyen, beleértve az egyéni típusok. Az állapotkezelő tárolt értékekkel kell lennie adategyezmény szerializálható, mert előfordulhat, hogy hálózaton át továbbítani a többi csomópontra replikáció során, és előfordulhat, hogy lemezre lehet írni, attól függően, hogy egy szereplő állapota adatmegőrzési beállítást.

Az állapotkezelő közös szótár módszerek állapot, a megbízható szótárban található hasonló kezeléséről mutatja.

További információ: [ajánlott eljárások a aktorállapot felügyeletéhez](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Hozzáférés állapota
Állapot kulcs által az állapotkezelő keresztül érhető el. Állapot kezelő metódusok nincsenek minden aszinkron, mert a lemez i/o azok lehet szükség, amikor szereplője teszi lehetővé a megőrzött állapot. Első hozzáférést, akkor objektumokat gyorsítótárba kerüljenek-e a memóriában. Ismételje meg a hozzáférési műveleteket access objektumok közvetlenül a memória, és lemezes i/o- vagy aszinkron környezetben-váltás terhet nélkül szinkron módon adja vissza. Egy állapotú objektumot eltávolítják a gyorsítótárból, a következő esetekben:

* Egy aktormetódus nem kezelt kivételt jelez, miután egy objektum kikeresi az állapotkezelő.
* Egy szereplő javítása után aktiválása folyamatban vagy meghibásodás után.
* Az állapotszolgáltató lapok lemezre állapotát. Ez a viselkedés attól függ, hogy az állapot szolgáltató általi megvalósítása. Az alapértelmezett állapota szolgáltató a `Persisted` beállítás van ez a viselkedés.

Állapot szabványos használatával kérheti le *beolvasása* művelet, amelynek jelez `KeyNotFoundException`(C#) vagy `NoSuchElementException`(Java), ha a bejegyzés nem létezik a kulcs:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Állapotának beolvasására használatával egy *TryGet* módszerrel, amely nem lépett, ha a bejegyzés nem létezik a kulcs:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Mentse az állapotot
Az állapot manager lekérdezési módszerek olyan objektumra hivatkozik helyi memória adja vissza. Módosítja az objektumot a helyi memória önmagában nem okoz úgy, hogy tartósan menthető. Ha egy objektum állapotkezelő lekért és módosítani, azt kell lehet ismételten beszúrni az állapotkezelő tartósan menteni.

Állapot is beszúrhat egy feltétel nélküli használatával *beállítása*, amely megegyezik a a `dictionary["key"] = value` Szintaxis:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Állapot használatával adhat hozzá egy *Hozzáadás* metódust. Ez a módszer jelez `InvalidOperationException`(C#) vagy `IllegalStateException`(Java), amikor kulcs hozzáadása a már létezik.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Állapot segítségével is hozzáadhat egy *TryAdd* metódust. Ez a módszer nem lépett, már meglévő kulcs hozzáadása során.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

Egy aktormetódus végén állapotkezelő automatikusan menti a hozzáadott vagy egy insert vagy az update művelet által módosított értékeket. A "Mentés" tartalmazhatnak megőrzése lemezre és a replikáció, a használt beállításoktól függ. Nem módosult az értékeket nem megőrzött és replikálja. Ha nincs érték módosítva lett, a mentési művelet nincs semmi hatása. Ha mentése sikertelen, a program elveti a módosított állapota, és az eredeti állapotra van töltve.

Mentheti is állapot manuálisan meghívásával a `SaveStateAsync` szereplő alap metódust:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Távolítsa el a állapota
Eltávolíthat állapot véglegesen egy szereplő állapotkezelője meghívásával a *eltávolítása* metódust. Ez a módszer jelez `KeyNotFoundException`(C#) vagy `NoSuchElementException`(Java), amikor megpróbálja eltávolítani a kulcsot, amely nem létezik.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Akkor is eltávolíthatja állapot véglegesen a *TryRemove* metódust. Ez a módszer nem lépett, amikor megpróbálja eltávolítani a kulcsot, amely nem létezik.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>További lépések

A Reliable Actors tárolt állapotban kell szerializálni a lemezre írva, és a magas rendelkezésre állású replikált előtt. További információ [szereplő típus szerializálási](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

A következő további információ [szereplő diagnosztika és teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md).
