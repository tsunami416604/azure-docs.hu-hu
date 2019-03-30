---
title: Az Azure Service Fabric állapot kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan elérése, mentése és távolítsa el a Service Fabric Reliable Actors-állapot.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 7c10d00916ef65767c98616c7337bfa444c339a9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664726"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Elérése, mentése és eltávolítása a Reliable actors – állapot
[A Reliable Actors](service-fabric-reliable-actors-introduction.md) egyszálas objektumok, amely magába foglalja a logikai és az állapot és megbízhatóan-állapot karbantartásához. Minden egyes színész példány rendelkezik a saját [állapotkezelője](service-fabric-reliable-actors-state-management.md): egy szótárt-szerű adatszerkezet, amely megbízhatóan tárolja az kulcs-érték pár. Az állapot-kezelő egy állapotszolgáltató burkolója. Függetlenül attól, amely adatok tárolására használható [adatmegőrzési beállítás](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) szolgál.

Állapotkezelő kulcsok karakterláncokat kell lennie. Értékek általános és állhat bármilyen, beleértve az egyéni típusokat. Az állapot Managerben tárolt értékek kell lennie adategyezményben szerializálható, mert más csomópontokon történő, hálózaton keresztüli replikálás során előfordulhat, hogy továbbítani, és a lemez, egy szereplő állapot megőrzése beállítás függően előfordulhat, hogy lehet írni.

Az állapot manager állapot, a megbízható szótárban található hasonló kezelése közös szótár metódusokat tárja fel.

További információ: [ajánlott eljárások az aktorok állapotának kezelésében](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Hozzáférési állapot
Állapot kulcs hozzáfért az állapot-kezelőn keresztül. Állapot kezelő metódusok összes aszinkron jellegűek, mert a lemez i/o szükséges amikor actors rendelkezik megőrzött állapot. Első hozzáférés esetén objektumok állapota is gyorsítótárazza a memóriában. Ismételje meg a hozzáférési műveletek hozzáférés objektumok közvetlenül a memória, és anélkül lemez i/o- vagy aszinkron kontextusváltás beleszámítva szinkron módon adja vissza. Egy állapot-objektumot eltávolítják a gyorsítótár a következő esetekben:

* Egy aktor módszer nem kezelt kivételt jelez, miután lekér egy objektumot az állapot managerből.
* Egy aktor újraaktiválnak, vagy folyamatban van az inaktiválása után, vagy hiba után.
* Szolgáltató lemezre oldalak állapot állapota. Ez a viselkedés attól függ, hogy az állapot adatbázisszolgáltatói implementációt. Az alapértelmezett állapotba szolgáltatóját a `Persisted` beállításhoz ezt a viselkedést.

Állapot-szabvány használatával lekérheti *első* felmerülésekor művelet `KeyNotFoundException`(C#) vagy `NoSuchElementException`(Java), ha a bejegyzés nem létezik a kulcs:

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

Állapot használatával is lekérhet egy *TryGet* metódushoz, amely nem lépett, ha a bejegyzés nem létezik egy kulcs:

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

## <a name="save-state"></a>Állapot mentése
Az állapot manager lekérés módszerek helyi memória-objektumra mutató hivatkozás visszaadása. Az objektumot az önálló helyi memória módosítása nem okoz tartósan menteni kell. Ha egy objektumot az állapot manager lekért és módosított, azt kell lehet ismételten beszúrni az tartósan menteni az állapot managerbe.

Állapot beszúrhat egy feltétel használatával *beállítása*, amely megegyezik a `dictionary["key"] = value` Szintaxis:

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

Állapot használatával adhat hozzá egy *Hozzáadás* metódust. Ez a módszer jelez `InvalidOperationException`(C#) vagy `IllegalStateException`(Java) való adjon hozzá egy kulcsot, amely már létezik.

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

Állapot használatával is hozzáadhat egy *TryAdd* metódust. Ez a módszer nem throw való hozzáadása egy kulcsot, amely már létezik.

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

Egy aktor metódus végén az állapot-kezelő automatikusan menti azokat az értékeket, adott hozzá, vagy egy insert nebo update művelet módosította. A "Mentés" lemezre és a replikáció, a használt beállításoktól függően megőrzése is tartalmazhatnak. Nem megőrzött vagy replikált értékeket, amelyek nem lett módosítva. Ha nincs érték módosítva lett, a mentési művelet nem csinál semmit. Mentés sikertelen lesz, ha a rendszer elveti a módosított állapota, és az eredeti állapotra újbóli.

Mentheti is állapot manuálisan meghívásával a `SaveStateAsync` az aktor alap metódust:

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

## <a name="remove-state"></a>Állapot törlése
Eltávolíthat állapot véglegesen egy szereplő állapotkezelője meghívásával a *eltávolítása* metódust. Ez a módszer jelez `KeyNotFoundException`(C#) vagy `NoSuchElementException`(Java) való eltávolítása egy kulcsot, amely nem létezik.

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

Valamint eltávolíthatja állapot véglegesen használatával a *TryRemove* metódust. Ez a módszer nem throw való eltávolítása egy kulcsot, amely nem létezik.

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

Reliable Actors tárolt, az állapot szerializálni kell a lemezre írt és magas rendelkezésre állású replikált előtt. Tudjon meg többet [Aktor típusszerializáció](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Ezután tudjon meg többet [Actors diagnosztizálása és teljesítményfigyelése](service-fabric-reliable-actors-diagnostics.md).
