---
title: Az Azure mikroszolgáltatások hibák szimulálása |} A Microsoft Docs
description: Ez a cikk ismerteti a Microsoft Azure Service Fabric található testability műveletek.
services: service-fabric
documentationcenter: .net
author: motanv
manager: timlt
editor: heeldin
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 70ed1561af6dc06b4d1db89e6449540dd76b67be
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815881"
---
# <a name="testability-actions"></a>Testability műveletek
Annak érdekében, hogy szimulálja egy nem megbízható infrastruktúra, az Azure Service Fabric biztosít, a fejlesztői, azzal, hogy különböző valós hibák és állapotváltozási adat áramlik szimulálásához. Ezek a testability műveletként érhetők el. A műveletek végezhetők az alacsony szintű API-k, amelyek egy adott hibabeszúrás, állapotváltás vagy érvényesítés. Ezek a műveletek kombinálásával átfogó tesztelési helyzetek írhat a szolgáltatásokhoz.

A Service Fabric biztosít néhány gyakori tesztelési helyzetek mikroszolgáltatásokból álló, ezeket a műveleteket. Kifejezetten ajánljuk, hogy használja-e beépített forgatókönyveket, amelyek vannak közös állapotváltásra és hiba eseteket teszteléséhez választottuk ki. Azonban műveleteket hozhat létre egyéni tesztelési helyzetek, amikor forgatókönyvek, amelyek nem tartoznak a beépített forgatókönyvek még vagy az egyéni szabott az alkalmazáshoz lefedettséggel hozzáadni kívánt használható.

C#a műveletek megvalósítását a System.Fabric.dll szerelvényben találhatók. A rendszer Fabric PowerShell-modult a Microsoft.ServiceFabric.Powershell.dll szerelvény megtalálható. A ServiceFabric PowerShell-modul runtime telepítésének részeként, telepítve van a, hogy a használat megkönnyítése érdekében.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Biztonságos és végbemenjen tartalék műveletek
Testability műveletek két fő gyűjtők sorolhatók:

* Végbemenjen hibák: Ezek a hibák hibaszimuláció például a gép újraindul, és a szoftverleállások feldolgozni. Ebben az esetben a hibák a végrehajtási környezet folyamat váratlanul leáll. Ez azt jelenti, hogy az állapot nincs karbantartási futtathatja, mielőtt az alkalmazás indul újra.
* Sikeres-e hibák: Ezek a hibák szimulálása a szabályos műveletekhez, például a replika áthelyezését és terheléselosztási funkciók által aktivált csepp. Ezekben az esetekben a szolgáltatás lekérdezi egy értesítés, amely a Bezárás gombra, és mielőtt kilépne távolíthatja el az állapot.

Jobb minőségű ellenőrzés céljából a szolgáltatás és az üzleti számítási feladatok futtatásához közben, hogy a különböző szabályos és végbemenjen hibákat. Végbemenjen hibák gyakorolja forgatókönyvek, ahol a szolgáltatás folyamat váratlanul kilép közepén néhány munkafolyamatot. Ez teszteli a helyreállítási elérési út után a szolgáltatás replika által a Service Fabric helyreáll. Ez segít az adatkonzisztencia és -e a szolgáltatás állapotának karban kell hiba után teszteléséhez. Hibák (a sikeres-e hibák) más készletét tesztelje, hogy a szolgáltatás megfelelően reagál a Service Fabric áthelyezett replikákra. Megszakítás kezelési ellenőrzi a RunAsync metódusában. Ellenőrizze a visszavonás jogkivonat beállítása, megfelelően a hozzá tartozó állapot mentése folyamatban van, és lépjen ki a RunAsync metódusában kell a szolgáltatást.

## <a name="testability-actions-list"></a>Testability műveletek listája
| Műveletek | Leírás | Managed API | PowerShell-parancsmag | Normális/végbemenjen hibák |
| --- | --- | --- | --- | --- |
| CleanTestState |A test-illesztőprogram a hibás leállás esetén a fürt eltávolítja az összes teszt állapota. |CleanTestStateAsync |Remove-ServiceFabricTestState |Nem alkalmazható |
| InvokeDataLoss |Adatvesztés kapott egy szolgáltatás partícióra. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Sikeres-e |
| InvokeQuorumLoss |Egy adott állapot-nyilvántartó szolgáltatási partíció beteszi a kvórum elvesztése. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Sikeres-e |
| A MovePrimary |A megadott fürtcsomópont helyezi át a megadott elsődleges replika egy állapotalapú szolgáltatás. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Sikeres-e |
| MoveSecondary |Az állapotalapú szolgáltatások aktuális másodlagos replikája áthelyezi egy másik fürtcsomópontra. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Sikeres-e |
| RemoveReplica |Egy replika meghibásodása szimulálja a replika eltávolítása egy fürtről. Ez a replika bezárul, és a szerepkör veszi "None", a fürt összes állapotában eltávolítását. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Sikeres-e |
| RestartDeployedCodePackage |A kód csomag folyamat hibája szimulálja egy fürt egy csomópontján üzembe helyezett egy kódcsomaghoz újraindításával. Ez megszakítja a kód csomag folyamat, amely minden felhasználó szolgáltatás replika üzemeltetett újraindul, az adott folyamatban. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Végbemenjen |
| RestartNode |A Service Fabric-fürt Csomóponthiba szimulálja egy csomópont újraindításával. |RestartNodeAsync |Restart-ServiceFabricNode |Végbemenjen |
| RestartPartition |Egy adatközpont kieső vagy a fürt kieső forgatókönyv szimulálja a partíció vagy az összes replika újraindításával. |RestartPartitionAsync |Restart-ServiceFabricPartition |Sikeres-e |
| RestartReplica |Egy replika meghibásodása szimulálja a megőrzött replika újraindításával egy fürtben, a replika bezárása és majd megnyitni. |RestartReplicaAsync |Restart-ServiceFabricReplica |Sikeres-e |
| Kiindulási_csomópont |Elindul a csomópont egy fürtöt, amely már le van állítva. |StartNodeAsync |Start-ServiceFabricNode |Nem alkalmazható |
| StopNode |Egy Csomóponthiba szimulálja egy fürt csomópontja leállításával. A csomópont le marad, amíg Kiindulási_csomópont nevezzük. |StopNodeAsync |Stop-ServiceFabricNode |Végbemenjen |
| ValidateApplication |A rendelkezésre állás és a egy alkalmazásban, a rendszer néhány hiba hogy után általában az összes Service Fabric-szolgáltatások állapotát ellenőrzi. |ValidateApplicationAsync |Test-ServiceFabricApplication |Nem alkalmazható |
| ValidateService |A rendelkezésre állás és a egy Service Fabric-szolgáltatás állapotát ellenőrzi után általában néhány tartalék hogy a rendszer. |ValidateServiceAsync |Test-ServiceFabricService |Nem alkalmazható |

## <a name="running-a-testability-action-using-powershell"></a>Egy PowerShell-lel testability-művelet futtatása
Ez az oktatóanyag bemutatja, hogyan testability-művelet futtatása a PowerShell használatával. Megtudhatja, hogyan testability-művelet futtatásához (beépített) a helyi fürt vagy egy Azure-fürtön. Microsoft.Fabric.Powershell.dll – a Service Fabric PowerShell-modul – a Microsoft Service Fabric MSI telepítésekor automatikusan telepítve van. A modul automatikusan betöltődik, amikor megnyit egy PowerShell-parancssort.

Az oktatóanyag szegmensek:

* [Művelet futtatásához egy beépített fürtön](#run-an-action-against-a-one-box-cluster)
* [Művelet futtatásához egy Azure-fürtön](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Művelet futtatásához egy beépített fürtön
Testability-művelet futtatásához egy helyi fürtön, először csatlakozzon a fürthöz, és nyissa meg a PowerShell-parancssort rendszergazdai módban. Lássunk a **újraindítás-ServiceFabricNode** művelet.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

A művelet itt **újraindítás-ServiceFabricNode** fut egy csomóponton "Csomópont1" nevű. A befejezési mód meghatározza, hogy azt nem ellenőrizze hogy a csomópont újraindítása művelet ténylegesen sikeres volt. A befejezési mód megadásával, mint a "Hitelesítés" eredményezi, hogy ellenőrizze, hogy az újraindítás művelet ténylegesen sikeres volt. Helyett a nevével adja meg közvetlenül a csomópont, adhatja meg azt egy partíciókulcsot és a replika, milyen módon:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Újraindítás-ServiceFabricNode** indítsa újra a Service Fabric-csomópont egy fürtben használandó. Ezzel leállítja a Fabric.exe folyamat, amely a rendszer szolgáltatás és a felhasználó szolgáltatás replikák mindegyike ezen a csomóponton futó újraindul. Ez az API segítségével tesztelheti a szolgáltatást segít tárhat fel a feladatátvétel helyreállítási elérési út mentén hibák. A fürtben lévő csomópont hibák szimulálása nyújt segítséget.

Az alábbi képernyőfelvételen a **újraindítás-ServiceFabricNode** testability parancsot a művelet.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

A kimenet az első **Get-ServiceFabricNode** (a parancsmag a Service Fabric PowerShell-modul) jeleníti meg, hogy rendelkezik-e a helyi fürt öt csomópontjának: A Node.5 node.1. A testability-művelet (parancsmag) után **újraindítás-ServiceFabricNode** hajtja végre a csomópont nevű Node.4, láthatjuk, hogy a csomóponti üzemidőt alaphelyzetbe lett állítva.

### <a name="run-an-action-against-an-azure-cluster"></a>Művelet futtatásához egy Azure-fürtön
Testability-művelet (a PowerShell használatával) futtat egy Azure-fürtön a hasonló művelet helyi fürtön történő futtatása. Az egyetlen különbség, hogy a művelet helyett a helyi fürthöz való csatlakozás futtatása előtt kell először csatlakozik az Azure-fürtben.

## <a name="running-a-testability-action-using-c35"></a>A C használatával testability-művelet futtatása&#35;
Testability-művelet futtatása használatával C#, először szüksége FabricClient használatával csatlakozhat a fürthöz. Ezután szerezze be a művelet végrehajtásához szükséges paraméterek. Különböző paraméterek használhatók ugyanaz a művelet végrehajtásához.
A RestartServiceFabricNode művelet megnézzük, futtassa egyik módja, a csomópont adatokat (a csomópont nevét és a csomópont azonosítója) a fürt használatával.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

A paraméter magyarázata:

* **CompleteMode** Megadja, hogy a mód nem ellenőrizze-e az újraindítási művelet ténylegesen sikeres volt. A befejezési mód megadásával, mint a "Hitelesítés" eredményezi, hogy ellenőrizze, hogy az újraindítás művelet ténylegesen sikeres volt.  
* **Így időtúllépés történt** beállítása, a művelet befejezését, mielőtt egy TimeoutException kivétel történt.
* **CancellationToken** lehetővé teszi, hogy a függőben lévő hívást kell lennie.

Helyett a nevével adja meg közvetlenül a csomópont, később is megadhatja egy partíciókulcsot és a replika típusú keresztül.

További információkért lásd: PartitionSelector és ReplicaSelector.

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

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector és ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector érhető el a testability segítő, és válasszon ki egy adott partíciót, amelyen a testability műveletek végrehajtására szolgál. Egy adott partícióra válassza, ha a Partícióazonosító előre ismert használható. Vagy megadhatja a partíciókulcs és a művelet feloldja a Partícióazonosító belső használatra. Akkor is kiválaszthatja, véletlenszerű partíció.

A segítő hozza létre a PartitionSelector objektumot, és válassza ki a partíciót a Select * módszerek egyikének használatával. Az API-hoz, amely azt igényli, akkor továbbítja a PartitionSelector objektumban. Ha nincs lehetőség be van jelölve, a rendszer alapértelmezés szerint egy véletlenszerű partíciót.

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

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector érhető el a testability Segéd, amely segítségével kiválaszthatja egy replikát, amelyen a testability műveletek végrehajtására szolgál. Válassza az adott replika, ha a másodpéldány-azonosító néven ismert előre használható. Emellett van egy elsődleges másodpéldány, vagy egy véletlenszerű másodlagos kiválaszthatja. ReplicaSelector PartitionSelector, származik, ezért ki kell választania a replika és a a partíciót, amelyen szeretné, a testability-művelet végrehajtásához.

A segítő használatához ReplicaSelector objektum létrehozása, és állítsa be a kívánt válassza ki a replika és a partíció. Meg lehet majd át azt az API-t, amely ezt megköveteli. Ha nincs lehetőség be van jelölve, a rendszer alapértelmezés szerint egy véletlenszerű replika és a véletlenszerű partíció.

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
* [Testability alkalmazási helyzetek](service-fabric-testability-scenarios.md)
* A szolgáltatás tesztelése
  * [Hibák szimulálása a szolgáltatások számítási feladatai közben](service-fabric-testability-workload-tests.md)
  * [Szolgáltatások közötti kommunikációs hibákkal szemben](service-fabric-testability-scenarios-service-communication.md)

