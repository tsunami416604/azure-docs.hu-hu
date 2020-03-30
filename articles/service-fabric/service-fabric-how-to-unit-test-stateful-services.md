---
title: Egységvizsgálatok kidolgozása az állapotalapú szolgáltatásokhoz
description: Ismerje meg az azure Service Fabric állapotalapú szolgáltatások egységtesztelését, valamint a fejlesztés során szem előtt tartandó speciális szempontokat.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c657bd8295d01a4e0fa4e44e969b33946684bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639836"
---
# <a name="create-unit-tests-for-stateful-services"></a>Egységtesztek létrehozása állapotalapú szolgáltatásokhoz
A Service Fabric állapotalapú szolgáltatásainak egységtesztelése olyan gyakori hibákat tár fel, amelyeket nem feltétlenül fog meg a hagyományos alkalmazás vagy a tartományspecifikus egységtesztelés. Az állapotalapú szolgáltatások egységtesztjeinek kidolgozása során van néhány speciális szempont, amelyet szem előtt kell tartani.

1. Minden replika alkalmazáskódot hajt végre, de különböző környezetben. Ha a szolgáltatás három replikát használ, a szolgáltatáskód három csomóponton hajt végre párhuzamosan, különböző környezetben/szerepkörben.
2. Az állapotalapú szolgáltatáson belül tárolt állapotnak konzisztensnek kell lennie az összes replikák között. Az állapotkezelő és a megbízható gyűjtemények biztosítják ezt a konzisztenciát a beépített. A memórián belüli állapotot azonban az alkalmazáskódnak kell kezelnie.
3. Minden replika módosítja a szerepköröket egy bizonyos ponton, miközben fut a fürtön. A másodlagos replika lesz az elsődleges, abban az esetben, ha az elsődleges csomópont nem lesz elérhető vagy túlterhelt lesz. Ez a Service Fabric természetes viselkedése, ezért a szolgáltatásoknak meg kell tervezniük, hogy végül egy másik szerepkör alatt hajtsanak végre.

Ez a cikk feltételezi, hogy [a Service Fabric állapotalapú szolgáltatások tesztelése](service-fabric-concepts-unit-testing.md) a szolgáltatásban olvasott.

## <a name="the-servicefabricmocks-library"></a>A ServiceFabric.Mocks könyvtár
A 3.3.0-s verziótól a [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) api-t biztosít a replikák vezénylésének és az állapotkezelésnek a gedvezénylésének gúnyolódásához. Ezt fogja használni a példákban.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*A ServiceFabric.Mocks nem a Microsoft tulajdonában vagy karbantartottja. Ez azonban jelenleg a Microsoft által ajánlott könyvtár az állapotalapú szolgáltatások teszteléséhez.*

## <a name="set-up-the-mock-orchestration-and-state"></a>A modell vezénylési és állapotbeállítása
A teszt elrendezési részének részeként létrejön egy modell replikakészlet és állapotkezelő. A replikakészlet ezután saját létrehozása a vizsgált szolgáltatás minden replika. Emellett saját életciklus-eseményeket is `OnChangeRole` birtokol, például és `RunAsync`. A modell állapotkezelő biztosítja, hogy az állapotkezelő ellen végrehajtott műveletek et úgy futtassa és tartsa meg, ahogy azt a tényleges állapotkezelő tenné.

1. Hozzon létre egy szolgáltatásgyári delegált, amely példányosítja a tesztelt szolgáltatást. Ennek hasonlónak vagy azonosnak kell lennie, mint `Program.cs` a service-gyár visszahívása általában megtalálható a Service Fabric szolgáltatás vagy aktor. Ennek a következő aláírást kell követnie:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Hozzon létre `MockReliableStateManager` egy osztálypéldányt. Ez kigúnyolminden interakciót az államkezelővel.
3. Hozzon létre `MockStatefulServiceReplicaSet<TStatefulService>` `TStatefulService` egy példányt, ahol a tesztelt szolgáltatás típusa. Ehhez a #1 lépésben létrehozott delegáltra és az #2-ben lett létrehozva az állapotkezelőre.
4. Kópiák hozzáadása a kópiakészlethez. Adja meg a szerepkört (például Elsődleges, ActiveSecondary, IdleSecondary) és a replika azonosítóját
   > Kapaszkodjanak a replikaazonosítók! Ezeket valószínűleg a cselekmény során fogják használni, és az egységteszt egyes részeit.

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

## <a name="execute-service-requests"></a>Szolgáltatáskérések végrehajtása
A szolgáltatáskérések egy adott replikán hajthatók végre a kényelmi tulajdonságok és a megkeresések használatával.
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

## <a name="execute-a-service-move"></a>Szolgáltatásáthelyezés végrehajtása
A modell replikakészlet számos kényelmi módszert tesz elérhetővé a különböző típusú szolgáltatásáthelyezések aktiválásához.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Végső összeállítás
A következő teszt bemutatja egy három csomópontos replikakészlet beállítását, és ellenőrzi, hogy az adatok egy szerepkör-módosítás után másodlagosból érhetők-e el. Ez egy tipikus probléma, amelyet `InsertAsync` akkor kaphat el, ha a közben hozzáadott `CommitAsync`adatokat a memóriában lévő vagy egy megbízható gyűjteménybe mentették futás nélkül. Mindkét esetben a másodlagos lenne szinkronban az elsődleges. Ez a szolgáltatás áthelyezése után inkonzisztens válaszokhoz vezetne.

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
Ismerje meg, hogyan tesztelhet [szolgáltatás-szolgáltatás kommunikációt,](service-fabric-testability-scenarios-service-communication.md) és [szimulálhatja a hibákat ellenőrzött káosz használatával.](service-fabric-controlled-chaos.md)
