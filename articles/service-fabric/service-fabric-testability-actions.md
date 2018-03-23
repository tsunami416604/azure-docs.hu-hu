---
title: Azure mikroszolgáltatások hibáinak szimulálása |} Microsoft Docs
description: Ez a cikk beszél található a Microsoft Azure Service Fabric tesztelhetőségi műveleteket.
services: service-fabric
documentationcenter: .net
author: motanv
manager: timlt
editor: toddabel
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv;heeldin
ms.openlocfilehash: c8ddc7732999ae555323bebaef60aa34c8f2ec17
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="testability-actions"></a>Tesztelhetőségi műveletek
Ahhoz, hogy egy nem megbízható infrastruktúra szimulálása, Azure Service Fabric biztosít, a fejlesztői, azzal, hogy különböző valós hibák és állapotváltozási adat áramlik szimulálásához. Ezek tesztelhetőségi műveletként érhetők el. A műveletek végezhetők, amelyek egy adott van, állapotváltás vagy érvényesítési alacsony szintű API-k. Ezek a műveletek kombinálásával átfogó Tesztelési forgatókönyvek írhat a szolgáltatások.

A Service Fabric biztosít néhány gyakori tesztesetek tevődik össze ezeket a műveleteket. Erősen ajánlott, hogy használhatja a beépített forgatókönyvekben, gondosan kiválasztott közös Állapotváltások és hiba esetekben teszteléséhez. Azonban műveletek segítségével hozzon létre egyéni Tesztelési forgatókönyvek, ha hozzá szeretne adni a forgatókönyvek, amelyek nem tartoznak a beépített forgatókönyvek még vagy egyéni alkalmazás igazított érvényességének.

A műveletek implementációja C# System.Fabric.dll szerelvényben találhatók. A rendszer Fabric PowerShell-modul a Microsoft.ServiceFabric.Powershell.dll szerelvény található. Runtime telepítésének részeként a ServiceFabric PowerShell modul telepítve engedélyezi a használat megkönnyítése érdekében.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Biztonságos és ungraceful tartalék műveletek
Tesztelhetőségi műveletek két fő gyűjtők sorolhatók:

* Ungraceful hibák: ezek a hibák hibák gépek újraindításának például szimulálásához, és dolgozza fel az összeomlások. Ebben az esetben a hibákat a végrehajtási környezet folyamat váratlanul leáll. Ez azt jelenti, hogy az állapot nem karbantartás előtt ismét elindul az alkalmazás futtatható.
* Sikeres-e hibák: ezek a hibák szimulálása szabályos műveletek, például a replika helyezi át, és elhagyta a(z) terheléselosztást által indított. Ezekben az esetekben a szolgáltatás lekérdezi egy értesítés, amely a Bezárás gombra, és mielőtt kilépne a állapotot fel tudja szabadítani.

Jobb minőségű ellenőrzéséhez futtassa le a szolgáltatást és az üzleti alkalmazások és szolgáltatások közben, hogy a különböző szabályos és ungraceful hibák. Ungraceful hibák forgatókönyvek, ahol a szolgáltatás-folyamat váratlanul kilép közepén egyes munkafolyamat megadásával. A szolgáltatás replika Service Fabric által történt visszaállítása után ellenőrzi a helyreállítási elérési úton található. Ez segít, tesztelje az adatok konzisztenciájának, és hogy a szolgáltatás állapotának karban kell hiba után. Hibák (a szabályos hibák) más készlete tesztelje, hogy a szolgáltatás megfelelően reagál a Service Fabric mozgatásának replikákra. A RunAsync metódusában a megszakítási kezelésének ellenőrzi. A szolgáltatás szüksége van a megszakítási token alatt állítsa be, megfelelően menteni az állapotát, és lépjen ki a RunAsync metódusában.

## <a name="testability-actions-list"></a>Tesztelhetőségi műveletek listája
| Műveletek | Leírás | Felügyelt API | PowerShell-parancsmag | Biztonságos/ungraceful hibák |
| --- | --- | --- | --- | --- |
| CleanTestState |Eltávolítja az összes teszt állapota a fürt esetén a teszt illesztőprogram rossz leállítására. |CleanTestStateAsync |Remove-ServiceFabricTestState |Nem alkalmazható |
| InvokeDataLoss |Adatvesztés kapott egy szolgáltatás partícióra. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Graceful |
| InvokeQuorumLoss |Egy adott állapot-nyilvántartó szolgáltatása partíció elhelyezi a kvórum elvesztése. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Graceful |
| Elsődleges áthelyezése |A megadott elsődleges replika az állapotalapú szolgáltatás áthelyezése a megadott fürtcsomópont. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Graceful |
| Másodlagos áthelyezése |A jelenlegi másodlagos másodpéldány egy állapotalapú szolgáltatás áthelyezése egy másik fürtcsomópontra. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Graceful |
| RemoveReplica |Replika hiba szimulálja által replika eltávolítása egy fürtből. Ez a replika bezárul, és állapotba kerül, hogy szerepkör "None", a fürt összes állapotában eltávolítása. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Graceful |
| RestartDeployedCodePackage |A kód csomag folyamat hibájának szimulálja egy egy fürt egy csomópontján telepített kódcsomag újraindításával. Ez a minden felhasználó szolgáltatás replika üzemeltetett újraindul, hogy a folyamat kód csomag folyamat megszakítása. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Ungraceful |
| A RestartNode |A Service Fabric-fürt Csomóponthiba szimulálja egy csomópont újraindításával. |RestartNodeAsync |Restart-ServiceFabricNode |Ungraceful |
| RestartPartition |A datacenter blackout vagy a fürt blackout forgatókönyv szimulálja néhány vagy az összes partíció replikák újraindításával. |RestartPartitionAsync |Restart-ServiceFabricPartition |Graceful |
| RestartReplica |A replika hiba szimulálja a megőrzött replika újraindításával fürtben, a replika bezárása, és majd megnyitni. |RestartReplicaAsync |Restart-ServiceFabricReplica |Graceful |
| A StartNode |A csomópont elindul egy fürt, amely már le van állítva. |StartNodeAsync |Start-ServiceFabricNode |Nem alkalmazható |
| Stopnode parancs |Egy Csomóponthiba szimulálja a fürt egyik csomópontjában levő leállításával. A csomópont le maradnak, amíg StartNode nevezik. |StopNodeAsync |Stop-ServiceFabricNode |Ungraceful |
| ValidateApplication |A rendelkezésre állási és, hogy bizonyos hiba a rendszerbe után általában az alkalmazáson belül minden Service Fabric-szolgáltatás állapotát ellenőrzi. |ValidateApplicationAsync |Test-ServiceFabricApplication |Nem alkalmazható |
| ValidateService |A rendelkezésre állás és a Service Fabric-szolgáltatás állapotát ellenőrzi a általában után néhány tartalék hogy a rendszerbe. |ValidateServiceAsync |Test-ServiceFabricService |Nem alkalmazható |

## <a name="running-a-testability-action-using-powershell"></a>PowerShell-lel tesztelhetőségi műveletek futtatása
Az oktatóanyag bemutatja, hogyan tesztelhetőségi műveletet futtatni a PowerShell használatával. Megtudhatja, hogyan egy tesztelhetőségi művelet futtatásához helyi (1-box) fürt vagy egy Azure-fürttel. Microsoft.Fabric.Powershell.dll--a Service Fabric PowerShell-modul – a Microsoft Service Fabric MSI telepítésekor automatikusan telepítve van. A modul automatikusan betöltődik, amikor megnyit egy PowerShell-parancssorba.

Útmutató szegmensek:

* [Egy műveletet futtatni egy beépített fürt](#run-an-action-against-a-one-box-cluster)
* [Egy műveletet futtatni egy Azure-fürttel](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Egy műveletet futtatni egy beépített fürt
Egy tesztelhetőségi művelet futtatni a helyi fürthöz, először csatlakozzon a fürthöz, és nyissa meg a PowerShell-parancssorba rendszergazdai módban. Ossza meg velünk tekintse meg a **újraindítás-ServiceFabricNode** művelet.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Itt a művelet **újraindítás-ServiceFabricNode** "1. csomópont" nevű csomópont futtatja. A végrehajtási mód határozza meg, hogy azt kell nem ellenőrizze, hogy az újraindítás-csomópont művelet ténylegesen sikeres volt. Adja meg a befejezési módot, mint a "Hitelesítés", akkor ellenőrizheti, hogy sikerült-e ténylegesen az újraindítási művelete. Helyett a nevével adja meg közvetlenül a csomópont, megadhatja a partíciós kulcs és milyen típusú replika, az alábbiak szerint:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Újraindítás-ServiceFabricNode** használatával indítsa újra a Service Fabric-csomópont a fürtben. Ezzel leállítja a Fabric.exe folyamatban, ami fog indítsa újra az összes, a rendszer szolgáltatás és a felhasználó szolgáltatás replikák adott csomóponton található alkotóelem. Ez az API a szolgáltatás tesztelése segítségével fedje fel a feladatátvételi helyreállítási elérési út mentén hibák. Ennek segítségével szimulálása a fürtben lévő csomópontok hibáit.

Az alábbi képernyőfelvételen látható a **újraindítás-ServiceFabricNode** tesztelhetőségi parancs működés közben.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Az első kimeneti **Get-ServiceFabricNode** (a Service Fabric PowerShell-modul a parancsmag) jeleníti meg, hogy a helyi fürt rendelkezik-e az öt csomóponttal: Node.1 Node.5 számára. A tesztelhetőségi (parancsmag) művelet után **újraindítás-ServiceFabricNode** a csomópont végrehajtása nevű Node.4, látható, hogy a csomópont hasznos üzemidő alaphelyzetbe lett állítva.

### <a name="run-an-action-against-an-azure-cluster"></a>Egy műveletet futtatni egy Azure-fürttel
Tesztelhetőségi műveletek futtatása (a PowerShell használatával) egy Azure fürtön hajtották hasonlít a helyi fürtön hajtották a műveletek futtatása. Az egyetlen különbség az, hogy helyett a helyi fürthöz, a művelet futtatása előtt kell először csatlakozzon az Azure-fürttel.

## <a name="running-a-testability-action-using-c35"></a>C használatával tesztelhetőségi műveletek futtatása&#35;
C# használatával tesztelhetőségi műveletet futtatni, először meg kell FabricClient használatával csatlakozzon a fürthöz. A művelet futtatásához szükséges paraméterek majd beszerzése. Különböző paraméterek használhatók ugyanaz a művelet futtatásához.
A RestartServiceFabricNode művelet megnézi, egy futtassa módja a fürtben lévő csomópont információk (a csomópont neve és csomópont azonosítója) segítségével.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

A paraméter magyarázata:

* **CompleteMode** határozza meg, hogy a mód nem ellenőrizze, hogy sikerült-e ténylegesen az újraindítási művelete. Adja meg a befejezési módot, mint a "Hitelesítés", akkor ellenőrizheti, hogy sikerült-e ténylegesen az újraindítási művelete.  
* **OperationTimeout** beállítja azt az időtartamot, amíg a művelet befejezését, mielőtt egy TimeoutException kivétel történt az.
* **CancellationToken** lehetővé teszi, hogy a függőben lévő hívása szakítható meg.

Helyett a nevével adja meg közvetlenül a csomópont, megadhatja a partíciós kulcs és a replika típusú keresztül.

További információkért lásd: [partitionselector osztályt és replicaselector osztályt](#partition_replica_selector).

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>Partitionselector osztályt és replicaselector osztályt
### <a name="partitionselector"></a>PartitionSelector
Partitionselector osztályt tesztelhetőségi felfedett segítő, és válassza ki a tesztelhetőségi műveletek végrehajtásához egy adott partícióra szolgál. Egy adott partícióra válassza, ha a Partícióazonosító előzetesen ismert használható. Vagy megadhatja a partíciós kulcs, és a művelet feloldja a Partícióazonosító belső. Lehetősége is van egy véletlenszerű partíció kiválasztása.

A segítő használatához a partitionselector osztályt objektum létrehozása, és válassza ki a partíciót a Select * módszerek egyikének használatával. Az API-hoz, írja elő, akkor továbbítja a partitionselector osztályt objektumban. Ha nincs lehetőség van kiválasztva, alapértelmezés szerint egy véletlenszerű partícióra.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>Replicaselector osztályt
Replicaselector osztályt tesztelhetőségi felfedett segítő, és használja, válasszon egy replikát a tesztelhetőségi műveletek végrehajtásához. Válasszon egy adott replikát, ha a másodpéldány-azonosító előzetesen ismert használható. Ezenkívül lehetősége nyílik egy elsődleges másodpéldány, vagy egy véletlenszerű másodlagos kiválasztása. Replicaselector osztályt partitionselector osztályt, származik, ezért meg kell a replika és a partíció, amelyen a tesztelhetőségi művelet elvégezni kívánt egyaránt.

Ez a segédprogram használatához replicaselector osztályt objektum létrehozása, és állítsa be a kívánt válassza ki a replika és a partíció. Majd továbbíthatja azt be, amely ezt az API-t. Ha nincs lehetőség van kiválasztva, alapértelmezés szerint egy véletlenszerű replika és a véletlenszerű partíció.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>További lépések
* [Tesztelhetőségi forgatókönyvek](service-fabric-testability-scenarios.md)
* A szolgáltatás tesztelése
  * [Szolgáltatás-munkaterhelések során hibák szimulálása](service-fabric-testability-workload-tests.md)
  * [Szolgáltatások közötti kommunikációs hibái](service-fabric-testability-scenarios-service-communication.md)

