---
title: "Felügyeleti állapot: Reliable Actors |} Microsoft Docs"
description: "Útmutatás a Reliable Actors állapot felügyelt, megőrzött és a magas rendelkezésre állás érdekében replikálva."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fae68c9fb40951e3f7a6fce67d75872cecfc52bd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="reliable-actors-state-management"></a>Megbízható szereplője állapotkezelés
Reliable Actors egyszálas objektumok, amellyel skálája ágyazható be mind a programot, és a állapotban. Szereplője Reliable Services futtatni, mert azok is-állapot karbantartásához megbízhatóan azonos adatmegőrzési és Reliable Services használó replikációs mechanizmusok vonatkoznak. Ezzel a módszerrel szereplője ne veszítse el állapotukra hiba esetén a szemétgyűjtés után vagy a terheléselosztás erőforrás vagy frissítés miatt fürt csomópontjai között áthelyezett Újraaktiválási után.

## <a name="state-persistence-and-replication"></a>Állapot megőrzését és a replikáció
Minden Reliable Actors minősülnek *állapotalapú alkalmazások és szolgáltatások* mivel egyes szereplő példányok leképezve egy egyedi azonosítót. Ez azt jelenti, hogy az azonos szereplő Azonosítóval ismételt hívása szereplő ugyanazon legyenek átirányítva. Állapot nélküli rendszerben ellentétben ügyfél hívások nem garantált, hogy ugyanarra a kiszolgálóra irányítsa minden alkalommal. Emiatt aktorszolgáltatások a mindig állapotalapú szolgáltatások.

Annak ellenére, hogy gyakrabban minősülnek állapotfüggő, hogy nem jelenti azt, azok állapot megbízhatóan kell tárolnia. Szereplője használhatja az állapot megőrzését szintjét, és a replikáció a tárhellyel kapcsolatos követelmények alapján az adatokat:

* **A megőrzött állapot**: állapot megőrizve a lemez- és 3 vagy több replikákat a rendszer replikálja. Ez a lehetőség a tartós állapot tárolási, ahol állapota is megőrizni a teljes fürt szolgáltatáskimaradás keresztül.
* **"Volatile" állapot**: állapot 3 vagy több replikák replikálja, és csak a memóriában tárolja. Ez lehetővé teszi a rugalmasság Csomóponthiba és szereplő hibák ellen, és a frissítések és az erőforrás terheléselosztás során. Azonban nem őrzi meg állapot lemezre. Így az összes replika egyszerre is elvesznek, ha az állapot elvész is.
* **Megőrzött állapot nélküli**: állapota nem replikált vagy írva lemez. Ez a szint, egyszerűen nincs szükségük-állapot karbantartásához megbízhatóan szereplője van.

Minden egyes adatmegőrzési szintje egyszerűen egy másik *állapotszolgáltató* és *replikációs* a szolgáltatás konfigurációját. E állapot írni lemezre függ az állapotszolgáltató--tároló állapota működnie az összetevőt. Replikáció működése függ a szolgáltatása hány replikák üzembe helyezéséhez. Csakúgy, mint a Reliable Services, az állapotszolgáltató és a replikaszám egyszerűen megadhatók manuálisan. Aktor keretében biztosít attribútum, amely, amikor egy szereplő használja, a rendszer automatikusan kijelöli egy alapértelmezett állapotszolgáltató, és automatikusan létrehozza a valamelyik három adatmegőrzési beállítások replikaszám beállításait. Származtatott osztály nem örökli a StatePersistence attribútum, szereplő típusonkénti biztosítania kell a StatePersistence szintje.

### <a name="persisted-state"></a>Megőrzött állapot
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
A beállítás a lemezen tárolja az adatokat, és automatikusan beállítja a szolgáltatás replikáinak száma 3 állapota szolgáltató használ.

### <a name="volatile-state"></a>"Volatile" állapota
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Ez a beállítás a memória-csak állapotszolgáltató használja, és beállítja a replikáinak száma 3.

### <a name="no-persisted-state"></a>Nincs olyan megőrzött állapot
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Ez a beállítás a memória-csak állapotszolgáltató használ, és a replikáinak száma 1 értékűre állítja be.

### <a name="defaults-and-generated-settings"></a>Alapértelmezett és a létrehozott beállítások
Ha a a `StatePersistence` attribútum, egy állapotszolgáltató automatikusan ki van jelölve, futási időben a szereplő szolgáltatás elindulásakor. A replika száma azonban állítja be fordítás során a Visual Studio szereplő összeállítási eszközök. A build tools automatikus generálása a *alapértelmezett szolgáltatás* ApplicationManifest.xml szereplő szolgáltatása esetében. Paraméterek létrejönnek az **a replikakészlet minimális mérete** és **cél replika méretének beállítása**.

Ezek a paraméterek manuálisan módosíthatja. Azonban minden alkalommal, amikor a `StatePersistence` attribútum módosul, a paraméterei vannak beállítva az alapértelmezett értékre replika készlet mérete a kiválasztott `StatePersistence` attribútum, felülírja az előző értéket. Más szóval ServiceManifest.xml beállított értékei *csak* felülírását build idő, amikor módosítja a `StatePersistence` attribútum értéke.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Állapot manager
Minden szereplő példány rendelkezik saját állapotkezelője: a szótár-szerű adatszerkezet, amely megbízhatóan tárolja az kulcs/érték párok. Az állapotkezelő a állapotszolgáltató csomagolásának. Függetlenül attól, amely adatmegőrzési beállítást használja adatok tárolására használható. Nem biztosít semmilyen garantálja, hogy egy futó szereplő szolgáltatás módosítható-e egy "volatile" (a memória-csak) állapota beállítást a működés közbeni frissítés során megőrzi az adat a megőrzött állapot beállítását. Azonban célszerű lehet módosítani egy futó szolgáltatással replika száma.

Állapot manager kulcsok karakterláncoknak kell lenniük. Értékek általános és állhat bármilyen, beleértve az egyéni típusok. Az állapotkezelő tárolt értékekkel kell lennie adategyezmény szerializálható, mert előfordulhat, hogy hálózaton át továbbítani a többi csomópontra replikáció során, és előfordulhat, hogy lemezre lehet írni, attól függően, hogy egy szereplő állapota adatmegőrzési beállítást.

Az állapotkezelő közös szótár módszerek állapot, a megbízható szótárban található hasonló kezeléséről mutatja.

### <a name="accessing-state"></a>Hozzáférés állapota
Állapot kulcs által az állapotkezelő keresztül elérhető. Állapot kezelő metódusok nincsenek minden aszinkron, mert a lemez i/o azok lehet szükség, amikor szereplője teszi lehetővé a megőrzött állapot. Első hozzáférést, akkor objektumokat gyorsítótárba kerüljenek-e a memóriában. Ismételje meg a hozzáférési műveleteket access objektumok közvetlenül a memória, és lemezes i/o- vagy aszinkron környezetben-váltás terhet nélkül szinkron módon adja vissza. Egy állapotú objektumot eltávolítják a gyorsítótárból, a következő esetekben:

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

### <a name="saving-state"></a>Állapot mentése
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

### <a name="removing-state"></a>Állapot eltávolítása
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

## <a name="next-steps"></a>Következő lépések

A Reliable Actors tárolt állapotban kell szerializálni a lemezre írva, és a magas rendelkezésre állású replikált előtt. További információ [szereplő típus szerializálási](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

A következő további információ [szereplő diagnosztika és teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md).
