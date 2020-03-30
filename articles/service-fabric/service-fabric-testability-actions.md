---
title: Hibák szimulálása az Azure mikroszolgáltatásokban
description: Ez a cikk a Microsoft Azure Service Fabricben található tesztelhetőségi műveletekről szól.
author: motanv
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 4bdb00eec38addc0c9f88eba8b73185ec5721277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282041"
---
# <a name="testability-actions"></a>Tesztelhető ségi műveletek
A megbízhatatlan infrastruktúra szimulálása érdekében az Azure Service Fabric a fejlesztő számára különböző valós hibák és állapotátmenetek szimulálásának módjait biztosítja. Ezek vannak kitéve a testability intézkedéseket. A műveletek az alacsony szintű API-k, amelyek egy adott hibainjektálás, állapotátmenet vagy érvényesítés okoz. Ezekkel a műveletekkel átfogó tesztforgatókönyveket írhat a szolgáltatásokhoz.

A Service Fabric néhány gyakori tesztforgatókönyvet biztosít, amelyek ezekből a műveletekből állnak. Javasoljuk, hogy használja ezeket a beépített forgatókönyveket, amelyek gondosan kiválasztott közös állapotátmenetek és hibaesetek teszteléséhez. A műveletek azonban egyéni tesztforgatókönyvek létrehozásához használhatók, ha olyan forgatókönyvekhez szeretne lefedettséget hozzáadni, amelyeket még nem fednek le a beépített forgatókönyvek, vagy amelyek az alkalmazáshoz szabottak.

A műveletek C# implementációi a System.Fabric.dll összeállításban találhatók. A System Fabric PowerShell modul a Microsoft.ServiceFabric.Powershell.dll szerelvényben található. A futásidejű telepítés részeként a ServiceFabric PowerShell modul telepítve van, hogy a könnyű használat.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Kecses vs ungraceful hiba akciók
A testability műveletek két fő vödörbe vannak sorolva:

* Nem megfelelő hibák: Ezek a hibák szimulálják a hibákat, például a gép újraindítását és a folyamat összeomlását. Ilyen esetekben a hibák, a végrehajtási környezetben a folyamat hirtelen leáll. Ez azt jelenti, hogy az állapot karbantartása nem futhat az alkalmazás újraindulása előtt.
* Kecses hibák: Ezek a hibák szimulálják a kecses műveleteket, például a replika áthelyezéseit és a terheléselosztás által kiváltott cseppeket. Ilyen esetekben a szolgáltatás értesítést kap a bezárásról, és a kilépés előtt megtisztíthatja az állapotot.

A jobb minőségű ellenőrzés érdekében futtassa a szolgáltatást és az üzleti munkaterhelést, miközben különböző kecses és nem kecses hibákat indukál. Ungraceful hibák gyakorlat forgatókönyvek, ahol a szolgáltatási folyamat hirtelen kilép a közepén néhány munkafolyamat. Ez teszteli a helyreállítási útvonalat, ha a szolgáltatás replikája a Service Fabric által visszaállított. Ez segít az adatok konzisztenciájának tesztelésében, valamint abban, hogy a szolgáltatás állapota megfelelően karbanmarad-e a hibák után. A többi hibakészlet (a kecses hibák) teszt, amely a szolgáltatás megfelelően reagál a Service Fabric által áthelyezett replikák. Ez a runasync metódusban teszteli a törlés kezelését. A szolgáltatásnak ellenőriznie kell a törlési jogkivonat beállítását, helyesen mentenie kell az állapotát, és ki kell lépnie a RunAsync metódusból.

## <a name="testability-actions-list"></a>Tesztelhetőségi műveletek listája
| Műveletek | Leírás | Felügyelt API | PowerShell-parancsmag | Kecses/kecses hibák |
| --- | --- | --- | --- | --- |
| CleanTestState között |Az összes tesztállapot eltávolítása a fürtből a tesztillesztőprogram hibás leállítása esetén. |CleanTestStateAsync |Remove-ServiceFabricTestState |Nem alkalmazható |
| InvokeDataLoss |Adatvesztést idéz elő egy szolgáltatáspartícióba. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Kecses |
| InvokeQuorumLoss |Egy adott állapotalapú szolgáltatáspartíciót a kvórum elvesztésébe helyez. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Kecses |
| MovePrimary (Elsődleges áthelyezés) |Áthelyezi az állapotalapú szolgáltatás megadott elsődleges replikáját a megadott fürtcsomópontra. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Kecses |
| MoveSecondary (Másodlagos áthelyezés) |Egy állapotalapú szolgáltatás aktuális másodlagos replikáját áthelyezi egy másik fürtcsomópontra. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Kecses |
| Replica eltávolítása |Kópiahibát szimulál, ha eltávolít egy replikát a fürtből. Ez bezárja a replika, és áthelyezi azt a szerepkör "Nincs", eltávolítja az összes állapotát a fürtből. |EltávolításReplicaAsync |Eltávolítás-ServiceFabricReplica |Kecses |
| RestartDeployedCodePackage |Kódcsomag-folyamat hibájának szimulálása a fürt csomópontján telepített kódcsomag újraindításával. Ez megszakítja a kódcsomag folyamatát, amely újraindítja a folyamatban tárolt összes felhasználói szolgáltatás replikáját. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Ungraceful |
| RestartNode (RestartNode) |Egy csomópont újraindításával szimulálja a Service Fabric fürtcsomópont-meghibásodását. |RestartNodeAsync |Újraindítás-ServiceFabricNode |Ungraceful |
| RestartPartition (Partíció újraindítása) |Egy adatközpont-elsötétítési vagy fürtelési forgatókönyvet szimulál a partíció néhány vagy összes replikájának újraindításával. |RestartPartitionAsync |Restart-ServiceFabricPartition |Kecses |
| ÚjraindításReplica |Replikák hibáját szimulálja egy megőrzött replika fürtben való újraindításával, a kópia bezárásával, majd újbóli megnyitásával. |ÚjraindításReplicaAsync |Újraindítás-ServiceFabricReplica |Kecses |
| StartNode között |Elindítja a csomópontot egy már leállított fürtben. |StartNodeAsync |Start-ServiceFabricNode |Nem alkalmazható |
| StopNode között |Csomóponthibát szimulál egy fürt csomópontjának leállításával. A csomópont a StartNode hívásáig lent marad. |StopNodeAsync |Stop-ServiceFabricNode |Ungraceful |
| ValidateApplication alkalmazás |Ellenőrzi az alkalmazáson belüli összes Service Fabric-szolgáltatás rendelkezésre állását és állapotát, általában miután valamilyen hibát indukálanak a rendszerben. |ValidateApplicationAsync |Test-ServiceFabricApplication |Nem alkalmazható |
| ValidateService (Érvényesítésszolgáltatás) |Ellenőrzi a service Fabric-szolgáltatás rendelkezésre állását és állapotát, általában miután valamilyen hibát indukálanak a rendszerben. |ValidateServiceAsync |Test-ServiceFabricService |Nem alkalmazható |

## <a name="running-a-testability-action-using-powershell"></a>Tesztelhető művelet futtatása a PowerShell használatával
Ez az oktatóanyag bemutatja, hogyan futtathatja a tesztelhetőségi műveletet a PowerShell használatával. Megtudhatja, hogyan futtathat egy tesztelhető műveletet egy helyi (egydobozos) fürt vagy egy Azure-fürt ellen. A Microsoft.Fabric.Powershell.dll – a Service Fabric PowerShell modul – automatikusan települ a Microsoft Service Fabric MSI telepítésekor. A modul automatikusan betöltődik, amikor megnyit egy PowerShell-kérdést.

Oktatóanyagok szegmensei:

* [Művelet futtatása egydobozos fürtön](#run-an-action-against-a-one-box-cluster)
* [Művelet futtatása Azure-fürtön](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Művelet futtatása egydobozos fürtön
Ha egy helyi fürtön tesztelhetőségi műveletet szeretne futtatni, először csatlakozzon a fürthöz, és nyissa meg a PowerShell-kérdést rendszergazdai módban. Nézzük meg a **Restart-ServiceFabricNode** műveletet.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Itt a **restart-serviceFabricNode** művelet egy "Node1" nevű csomóponton fut. A befejezési mód azt határozza meg, hogy nem kell ellenőriznie, hogy az újraindítás-csomópont művelet ténylegesen sikeres volt-e. Ha a befejezési módot "Ellenőrzés" állapotban adja meg, akkor ellenőrizni fogja, hogy az újraindítási művelet valóban sikeres volt-e. Ahelyett, hogy közvetlenül megnevezné a csomópontot a neve alapján, megadhatja azt egy partíciókulcson és a replika használatával, az alábbiak szerint:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**A ServiceFabricNode újraindítása** a fürt ben lévő Service Fabric node újraindításához kell használni. Ez leállítja a Fabric.exe folyamatot, amely újraindítja az adott csomóponton tárolt összes rendszerszolgáltatás- és felhasználói szolgáltatásreplikát. Az API-t a szolgáltatás teszteléséhez segít feltárni a hibákat a feladatátvételi helyreállítási útvonalak mentén. Segít szimulálni a csomóponthibákat a fürtben.

A következő képernyőképen látható a **Restart-ServiceFabricNode** testability parancs működés közben.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Az első **Get-ServiceFabricNode** kimenete (a Service Fabric PowerShell modul parancsmagja) azt mutatja, hogy a helyi fürt öt csomóponttal rendelkezik: Node.1 a Node.5. Miután a testability action (parancsmag) **Restart-ServiceFabricNode** végrehajtása a csomóponton, node.4 nevű, azt látjuk, hogy a csomópont üzemideje alaphelyzetbe állt.

### <a name="run-an-action-against-an-azure-cluster"></a>Művelet futtatása Azure-fürtön
Egy tesztelhető művelet futtatása (a PowerShell használatával) egy Azure-fürtön hasonló a művelet futtatása egy helyi fürtön. Az egyetlen különbség az, hogy a művelet futtatása előtt, ahelyett, hogy a helyi fürthöz való csatlakozás helyett, először csatlakoznia kell az Azure-fürthöz.

## <a name="running-a-testability-action-using-c35"></a>Tesztelhető művelet futtatása C&#35;
Ha egy tesztelhető műveletet a C# használatával, először csatlakoznia kell a fürthöz a FabricClient használatával. Ezután szerezze be a művelet futtatásához szükséges paramétereket. Ugyanazon művelet futtatásához különböző paraméterek használhatók.
A RestartServiceFabricNode művelet et tekintve a fürt csomópontadatainak (csomópontnév és csomópontpéldány-azonosító) használatával futtatható.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

A paraméter magyarázata:

* **A CompleteMode** azt határozza meg, hogy a mód nem ellenőrzi, hogy az újraindítási művelet valóban sikeres volt-e. Ha a befejezési módot "Ellenőrzés" állapotban adja meg, akkor ellenőrizni fogja, hogy az újraindítási művelet valóban sikeres volt-e.  
* **Az OperationTimeout** azt az időt adja meg, amíg a művelet befejeződik a TimeoutException kivétel eldobása előtt.
* **Az CancellationToken** lehetővé teszi egy függőben lévő hívás megszakítását.

Ahelyett, hogy közvetlenül megadnák a csomópontot a neve alapján, megadhatja azt egy partíciókulcson és a replika használatával.

További információ: PartitionSelector and ReplicaSelector.

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
### <a name="partitionselector"></a>PartitionSelector (Partícióválasztó)
PartitionSelector egy segítő kitéve a tesztelhetőség, és arra szolgál, hogy válasszon ki egy adott partíciót, amelyen végre kell hajtani a testability műveletek et. Egy adott partíció kiválasztására használható, ha a partícióazonosító előzetesen ismert. Vagy megadhatja a partíciókulcsot, és a művelet belsőleg feloldja a partícióazonosítót. Lehetősége van véletlenszerű partíció kiválasztására is.

A segítő használatához hozza létre a PartitionSelector objektumot, és jelölje ki a partíciót a Select* metódusok egyikével. Ezután adja át a PartitionSelector objektumot az AZT igénylő API-nak. Ha nincs bejelölve beállítás, akkor az alapértelmezett beállítás egy véletlenszerű partícióra kerül.

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

### <a name="replicaselector"></a>Replikakijelölő
A ReplicaSelector egy tesztelhetőségben elérhető segítő, és segítségével kiválaszthatja azt a replikát, amelyen a tesztelhetőségi műveletek bármelyikét végre kell hajtani. Egy adott kópia kiválasztására használható, ha a replikaazonosító előzetesen ismert. Emellett lehetősége van egy elsődleges replika vagy egy véletlenszerű másodlagos kiválasztására. A ReplicaSelector a PartitionSelector-ból származik, ezért ki kell választania mind a replikát, mind azt a partíciót, amelyen a tesztelhetőségi műveletet végre kívánja hajtani.

A segítő használatához hozzon létre egy Replikakijelölő objektumot, és állítsa be a kópia és a partíció kiválasztásának módját. Ezután adja át az API-t, amely előírja. Ha nincs bejelölve beállítás, akkor alapértelmezés szerint véletlenszerű replika és véletlenszerű partíció lesz.

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
  * [Hibák szimulálása a szolgáltatás-munkaterhelések során](service-fabric-testability-workload-tests.md)
  * [Szolgáltatás-szolgáltatás kommunikációs hibák](service-fabric-testability-scenarios-service-communication.md)

