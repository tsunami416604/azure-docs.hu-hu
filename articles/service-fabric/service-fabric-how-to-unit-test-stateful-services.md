---
title: Fejlesztés az Azure Service Fabric állapotalapú szolgáltatások egységteszteket |} A Microsoft Docs
description: Ismerje meg, hogyan fejleszthet a Service Fabric Stateful Services egységteszteket.
services: service-fabric
documentationcenter: .net
author: charleszipp
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: ryanwi
ms.openlocfilehash: 945cdf63a178a09f121f355aaa7635537e46e5ff
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703705"
---
# <a name="create-unit-tests-for-stateful-services"></a>A Stateful Services egységtesztek létrehozása
A Service Fabric állapotalapú szolgáltatások egységtesztelés tárja, akkor nem feltétlenül kell érint a hagyományos alkalmazás vagy a tartomány-specifikus egységtesztelés gyakori hibákat. Az állapotalapú szolgáltatások esetében egységteszteket fejlesztésekor nincsenek néhány szempontot, amelyeket figyelembe kell tárolni.

1. Minden egyes replikának alkalmazáskód hajt végre, de eltérő környezetben. Ha a szolgáltatás három replika használ, a szolgáltatást kód végrehajtása alatt különféle szerepkör/környezet párhuzamosan három csomóponton.
2. Az állapotalapú szolgáltatásban tárolt állapotban kell lennie többek között az összes replika konzisztens. A konzisztencia-a-beépített a state manager és a reliable collections biztosít. Azonban a memóriában kell kezelnie az alkalmazáskód.
3. Minden egyes replikának a fürt futtatása során valamikor szerepkörök változik. Egy másodlagos másodpéldány egy elsődleges lesz, abban az esetben, ha a tároló az elsődleges csomópont nem érhető el vagy túlterhelt válik. Ez a természetes működés a Service fabric, ezért meg kell terveznie az szolgáltatások végül végre egy másik szerepkör alatt.

Ez a cikk feltételezi, hogy [a Service Fabric állapotalapú szolgáltatások Egységtesztelés](service-fabric-concepts-unit-testing.md) elolvasták.

## <a name="the-servicefabricmocks-library"></a>A ServiceFabric.Mocks könyvtár
Verzió 3.3.0, kezdődően [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) utánzási mindkét a vezénylés, a replikák és az állapot-felügyeleti API-k biztosít. Ez lesz a példákban.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[Github](https://github.com/loekd/ServiceFabric.Mocks)

*Nem birtokolt vagy a Microsoft által karbantartott ServiceFabric.Mocks. Ez azonban jelenleg a Microsoft által ajánlott egységtesztelés állapotalapú szolgáltatások könyvtára.*

## <a name="set-up-the-mock-orchestration-and-state"></a>A vezénylési utánzatként funkcionáló és állapot beállítása
Egy teszt elrendezése részének részeként utánzatként funkcionáló replika beállítása és állapotkezelője jön létre. A replikakészlet lesz majd saját egy példányát, a tesztelt szolgáltatás minden egyes replikához. Ez lesz is saját végrehajtó életciklussal kapcsolatos események például `OnChangeRole` és `RunAsync`. A utánzatként funkcionáló állapotkezelője biztosítja az állapot a hálózatkezelőn végrehajtott valamennyi művelet futtatása, és a tényleges állapotának manager ugyanúgy tartani.

1. Hozzon létre egy service gyári meghatalmazott, amely létrehozza nekünk a tesztelt szolgáltatás. Ez legyen hasonló vagy azzal megegyező módon általában találhatók a szolgáltatás gyári visszahívás `Program.cs` vagy aktor Service Fabric-szolgáltatás. Ez a következő aláírást kell követnie:
```csharp
MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
```
2. Hozzon létre egy példányt `MockReliableStateManager` osztály. Ez lesz utánzása az állapotkezelője folytatott mindennemű interakcióhoz.
3. Hozzon létre egy példányt `MockStatefulServiceReplicaSet<TStatefulService>` ahol `TStatefulService` a tesztelt szolgáltatás típusa. Ezt a beállítást a #1. lépésben létrehozott delegált és a # 2 példány állapotkezelője
4. Adja hozzá a replikákat a replikakészlethez. Adja meg a szerepkör (például az elsődleges, ActiveSecondary, IdleSecondary) és a replika azonosítója
> Kis türelmet, a replika azonosítók! Ezek a törvény során valószínűleg használandó és vyhodnocení egy test jednotky részeit.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Hajtsa végre a szolgáltatáskérések
A szolgáltatáskérések hajtható végre az adott replika használatával a kényelem és a kereséseket.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Hajtsa végre a szolgáltatás áthelyezése
A utánzatként funkcionáló replikakészlet metódusokat tárja fel számos kényelmi szolgáltatás áthelyezését különböző típusú aktiválásához.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secodary with replica id 4 to active secondary 
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>A teljes kép
A következő vizsgálat bemutatja egy három csomópontos replikakészlethez beállítása és ellenőrzése, hogy az adatok egy szerepkör módosítása után egy másodlagos érhető el. Ez előfordulhat, hogy a tényleges egy tipikus probléma, ha az adatok során hozzá `InsertAsync` valamit a memóriában vagy egy megbízható gyűjteményben futtatása nélkül lett mentve `CommitAsync`. Mindkét esetben a másodlagos nincs szinkronban az elsődleges lenne. Ez nem vezet inkonzisztens által küldött válaszokhoz után a szolgáltatás helyezi át.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan tesztelheti [szolgáltatások közötti kommunikációs](service-fabric-testability-scenarios-service-communication.md) és [szabályozott káosz használata hibák szimulálása](service-fabric-controlled-chaos.md).