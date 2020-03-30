---
title: Az Azure Service Fabric állapotának kezelése
description: Ismerje meg az Azure Service Fabric megbízható szereplő állapotának elérését, mentését és eltávolítását, valamint az alkalmazások tervezésekor figyelembe vett szempontokat.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 788c337a37ec66c5aa1521c5cd9f2816ed7a8bf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645633"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Megbízható szereplők állapot elérése, mentése és eltávolítása
[A Reliable Actors](service-fabric-reliable-actors-introduction.md) egyszálas objektumok, amelyek logikai és állapotalapú és megbízható állapot-fenntartási képest beágyaznak. Minden aktorpéldánysaját [állapotkezelővel](service-fabric-reliable-actors-state-management.md)rendelkezik: egy szótárszerű adatstruktúra, amely megbízhatóan tárolja a kulcs-/értékpárokat. Az állapotkezelő egy állami szolgáltató körül van. Használhatja az adatok tárolására, függetlenül attól, hogy melyik [adatmegőrzési beállítást](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) használja.

Az állapotkezelő kulcsoknak karakterláncoknak kell lenniük. Az értékek általánosak, és bármilyen típusúak lehetnek, beleértve az egyéni típusokat is. Az állapotkezelőben tárolt értékeknek szerializálhatónak kell lenniük, mert a replikáció során a hálózaton keresztül más csomópontokra továbbíthatók, és az aktor állapotmegőrzési beállításától függően lemezre írhatók.

Az állapotkezelő a Megbízható szótárban találhatóhoz hasonló általános szótári módszereket tár fel az állapot kezeléséhez.

További információt az [aktorállapot kezelésével kapcsolatos gyakorlati tanácsok](service-fabric-reliable-actors-state-management.md#best-practices)című témakörben talál.

## <a name="access-state"></a>Hozzáférési állapot
Az állapot kulcson keresztül érhető el az állapotkezelőn keresztül. Állapotkezelő metódusok mind aszinkron, mert szükség lehet a lemez I/O, ha a szereplők megmaradnak állapot. Az első hozzáféréskor az állapotobjektumok a memóriában gyorsítótárazódnak. Az ismételt hozzáférési műveletek közvetlenül a memóriából érik el az objektumokat, és szinkron módon adják vissza anélkül, hogy lemezI/O-t vagy aszinkron környezetváltást eredményezne. Egy állapotobjektum a következő esetekben törlődik a gyorsítótárból:

* Az aktormetódus egy nem kezelt kivételt okoz, miután lekér egy objektumot az állapotkezelőtől.
* Az aktor újraaktiválódik, vagy inaktiválás után, vagy hiba után.
* Az állapotszolgáltató lapjai lemezre vannak állítva. Ez a viselkedés az állapotszolgáltató implementációjátől függ. A beállítás alapértelmezett `Persisted` állapotszolgáltatója rendelkezik ezzel a viselkedéssel.

Az állapotlekérés egy szabványos *Get* művelettel érhető el, amely (C#) vagy `KeyNotFoundException` `NoSuchElementException`(Java) szór, ha a kulcshoz nem létezik bejegyzés:

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

Az állapotot olyan *TryGet* metódussal is lekérheti, amely nem jelenik meg, ha egy kulcshoz nem létezik bejegyzés:

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
Az állapotkezelő lekérési metódusai a helyi memóriában lévő objektumra mutató hivatkozást adnak vissza. Ha csak a helyi memóriában módosítja az objektumot, az nem eredményezi tartós mentését. Amikor egy objektumot beolvas az állapotkezelőből, és módosítja, azt újra be kell szúrni az állapotkezelőbe, hogy tartósan menthető legyen.

Az állapotot feltétel nélküli *készlet*használatával szúrhatja be, amely a `dictionary["key"] = value` szintaxisnak felel meg:

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

Az állapot ot hozzáadási módszerrel is *hozzáadhatja.* Ez a `InvalidOperationException`módszer (C#) vagy `IllegalStateException`(Java) értéket ad, amikor olyan kulcsot próbál hozzáadni, amely már létezik.

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

Az állapotot *TryAdd* metódussal is hozzáadhatja. Ez a módszer nem dobja, amikor megpróbál hozzáadni egy kulcsot, amely már létezik.

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

Az aktor metódus végén az állapotkezelő automatikusan menti a beszúrási vagy frissítési művelet által hozzáadott vagy módosított értékeket. A "mentés" magában foglalhatja a lemezre való megőrzést és a replikációt, a használt beállításoktól függően. A nem módosított értékek nem maradnak meg és nem replikálódnak. Ha egyetlen érték sem módosult, a mentési művelet nem tesz semmit. Ha a mentés sikertelen, a módosított állapot elvetésre kerül, és az eredeti állapot újratöltődik.

Az állapotot manuálisan is `SaveStateAsync` mentheti, ha meghívja a metódust az aktor alapon:

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

## <a name="remove-state"></a>Állapot eltávolítása
Az állapot ot véglegesen eltávolíthatja az aktor állapotkezelőjéből az Eltávolítás metódus *meghívásával.* Ez a `KeyNotFoundException`módszer (C#) vagy `NoSuchElementException`(Java) parancsot ad, amikor megpróbál eltávolítani egy nem létező kulcsot.

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

Az állapotot a *TryRemove* metódussal is véglegesen eltávolíthatja. Ez a módszer nem dobja, amikor megpróbálja eltávolítani a kulcsot, amely nem létezik.

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

A Reliable Actors-ben tárolt állapotot szerializálni kell a lemezre írt és a magas rendelkezésre állás érdekében replikálódó állapotot. További információ az [Aktor típusszerializálásáról.](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

Ezután tudjon meg többet [a szereplő diagnosztikáról és a teljesítményfigyelésről.](service-fabric-reliable-actors-diagnostics.md)
