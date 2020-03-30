---
title: Fürtcsomópontok indítása és leállítása
description: Ismerje meg, hogyan használhatja a hiba-injektálás a Service Fabric-alkalmazások teszteléséhez a fürtcsomópontok indításával és leállításával.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 8f2eefec94ad4763a054ee089b17232c41e642dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609791"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>A kezdő csomópont és a csomópont leállítása API-k cseréje a csomópontátmenet API-val

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Mire történik a Csomópont leállítása és a Kezdő csomópont API-k?

A Stop Node API (felügyelt: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) leállítja a Service Fabric csomópont.  A Service Fabric-csomópont folyamat, nem virtuális gép vagy gép – a virtuális gép vagy a gép továbbra is fut.  A dokumentum többi részében a "csomópont" a Service Fabric-csomópontot jelenti.  Egy csomópont leállítása *leállított* állapotba helyezi azt, amelyben nem tagja a fürtnek, és nem tud szolgáltatásokat üzemeltetni, így szimulálva egy *lefelé mutató* csomópontot.  Ez akkor hasznos, ha hibákat szeretne beadni a rendszerbe az alkalmazás teszteléséhez.  A Start Node API (felügyelt: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) megfordítja a Stop Node API-t, amely visszaállítja a csomópontot normál állapotba.

## <a name="why-are-we-replacing-these"></a>Miért cseréljük ki ezeket?

Akorábban leírtak szerint a *leállított* Service Fabric-csomópont egy csomópont, amelyet szándékosan céloznak meg a Csomópont-leállítás API használatával.  A *lefelé* csomópont olyan csomópont, amely bármely más okból nem érhető el (például a virtuális gép vagy a gép ki van kapcsolva).  A Stop Node API-val a rendszer nem tesz elérhetővé információkat a *leállított* és *a lefelé* futó csomópontok megkülönböztetéséhez.

Ezen felül az api-k által visszaadott egyes hibák nem olyan leírójellegűek, mint amilyenek lehetnek.  Ha például egy már *leállított* csomóponton a Csomópont leállítása API-t kéri, az *InvalidAddress*hibaüzenetet adja vissza.  Ez a tapasztalat lehetne javítani.

Emellett a csomópont leállított időtartama "végtelen", amíg a Start Node API-t meg nem hívja.  Azt találtuk, hogy ez problémákat okozhat, és hibaérzékeny lehet.  Például láttuk problémák, ahol a felhasználó meghívta a Stop Node API-t egy csomóponton, majd elfelejtette azt.  Később nem volt világos, hogy a csomópont *leállt* vagy *megállt*.


## <a name="introducing-the-node-transition-apis"></a>A csomópont-átmeneti API-k bemutatása

Ezeket a fenti problémákat új API-készletben oldottuk meg.  Az új csomópontátmeneti API (felügyelt: [StartNodeTransitionAsync()][snt]) használható a Service Fabric-csomópont *leállított* állapotba való átmenetéhez, vagy *a leállított* állapotból egy normál állapotba való átmenethez.  Kérjük, vegye figyelembe, hogy az API nevében a "Start" nem hivatkozik egy csomópont indítására.  Egy aszinkron művelet elindítására utal, amelyet a rendszer végrehajt, hogy a csomópontot *leállított* vagy elindított állapotba váltsa.

**Használat**

Ha a csomópontátmenet API nem okoz kivételt, amikor meghívja, majd a rendszer elfogadta az aszinkron műveletet, és végrehajtja azt.  A sikeres hívás nem jelenti azt, hogy a művelet még befejeződött.  A művelet aktuális állapotáról való információ hozása érdekében hívja meg a Csomópontváltás folyamata API-t (felügyelt: [GetNodeTransitionProgressAsync()][gntp]a művelethez a csomópontátmeneti API meghívásakor használt guid használatával.  A Csomópontátmenet folyamata API egy NodeTransitionProgress objektumot ad vissza.  Az objektum State tulajdonsága a művelet aktuális állapotát adja meg.  Ha az állapot "Futás", akkor a művelet végrehajtása.  Ha befejeződött, a művelet hiba nélkül befejeződött.  Ha hibás, probléma merült fel a művelet végrehajtásakor.  Az Eredmény tulajdonság Exception tulajdonsága jelzi a probléma.  További https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate információt az állapot tulajdonságról és az alábbi "Mintahasználat" című szakaszban a kódpéldákért olvashat.


**Különbséget egy leállított csomópont és egy lefelé csomópont között** Ha egy csomópont *leáll* a csomópontátmenet API használatával, egy csomópontlekérdezés kimenete (felügyelt: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) megmutatja, hogy ez a csomópont *egy IsStopped* tulajdonság értéke igaz.  Ne feledje, hogy ez eltér a *NodeStatus* tulajdonság értékétől, amely a *Down*.  Ha a *NodeStatus* tulajdonság értéke *Le*, de *IsStopped* hamis, majd a csomópont nem állt le a Csomópont átmenet API-t, és *le* valamilyen más okból.  Ha az *IsStopped* tulajdonság igaz, és a *NodeStatus* tulajdonság *nem működik,* akkor a Csomópontátmenet API használatával leállt.

A csomópontátmenet API-val *indított leállított* csomópont ismét visszaadja azt a fürt normál tagjaként való működéshez.  A kimeneta a csomópont lekérdezési API-t jeleníti *IsStopped* hamis, és *NodeStatus,* mint valami, ami nem le (például, Fel).


**Korlátozott időtartam** Amikor a csomópont leállításához a csomópontot használja, a *stopNodeDurationInSeconds*paraméter egyik szükséges paraméter e másodpercben azt az időt jelöli másodpercben, amíg a csomópont *levan állítva.*  Ennek az értéknek a megengedett tartományban kell lennie, amely nek legalább 600, de legfeljebb 14400-nak kell lennie.  Ezen idő lejárta után a csomópont automatikusan újraindul felállási állapotba.  A használat ra vonatkozó példát az alábbi 1.

> [!WARNING]
> Ne keverje a csomópontátmeneti API-kat, valamint a Csomópont leállítása és a csomópont indítása API-kat.  A javaslat csak a csomópont átmeneti API-t használja.  > Ha egy csomópont már leállt a Stop Node API használatával, először a Csomópont-> használata előtt el kell kezdenie a Kezdő csomópont API-k használatát.

> [!WARNING]
> Több csomópontátmeneti API-hívás nem kezdeményezhető ugyanazon a csomóponton párhuzamosan.  Ilyen esetben a csomóponti átmenet API > a NodeTransitionInProgress ErrorCode tulajdonságértékkel rendelkező FabricException-et dobja.  Miután egy csomópontváltás > el indult, meg kell várnia, amíg a művelet eléri a terminálállapotot (Befejezett, Hibás vagy ForceCancelled), mielőtt új > új átmenetet kezdene el ugyanazon a csomóponton.  A párhuzamos csomópont-átmenet hívások különböző csomópontokon engedélyezettek.


#### <a name="sample-usage"></a>Példa a használatra


**1. minta** – A következő minta a csomópontátmenet API-t használja egy csomópont leállításához.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**2. minta** – A következő minta *egy leállított* csomópontot indít el.  Használ néhány segítő módszerek az első minta.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**3. minta** – A következő minta helytelen használatot mutat.  Ez a használat helytelen, mert az általa biztosított *stopDurationInSeconds* nagyobb, mint az engedélyezett tartomány.  Mivel a StartNodeTransitionAsync() végzetes hibával sikertelen lesz, a művelet nem lett elfogadva, és a progress API-t nem kell meghívni.  Ez a minta az első minta néhány segítő módszerét használja.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**4. minta** – A következő minta a csomópontváltási folyamat API-ból visszaadott hibainformációkat mutatja be, amikor a csomópontátmenet API által kezdeményezett műveletet elfogadja, de később sikertelen a végrehajtás során.  Abban az esetben sikertelen, mert a csomópontátmenet API nem létező csomópont ot próbál elindítani.  Ez a minta az első minta néhány segítő módszerét használja.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
