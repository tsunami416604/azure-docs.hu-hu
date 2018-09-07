---
title: Elindíthatja és leállíthatja a fürtcsomópontok tesztelése az Azure Service Fabric-alkalmazások |} A Microsoft Docs
description: Ismerje meg, hogyan hibabeszúrással tesztelheti a Service Fabric-alkalmazás elindításával és leállításával fürtcsomópontok.
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: ''
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 95c3726caeb19d6bbf7153533951bb18cd7d0e57
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055403"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>A csomópont elindítása és leállítása csomópont API-k cseréje a csomópont átmeneti API-val

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Mit hajtsa végre a csomópont leállítása és elindítása csomópont API-k?

A csomópont leállítása API (felügyelt: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) egy Service Fabric-csomópont leáll.  Egy Service Fabric-csomópont nem a folyamat, nem egy virtuális gép vagy gépek – a virtuális gép vagy a számítógép továbbra is futtatni.  A dokumentum többi részén a "csomópont" azt jelenti, Service Fabric-csomópont.  A csomópont leállítása állapotba állítja be azt egy *leállt* állapotba, ha még nem tagja a fürtnek és nem tárolható a szolgáltatások, ezzel szimulálva egy *le* csomópont.  Ez akkor hasznos, a hibák injektálásra a rendszer az alkalmazás teszteléséhez.  A Start csomópont API (felügyelt: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) megfordítja a csomópont leállítása API  amely visszahelyezi a csomópontot a szokásos állapotába.

## <a name="why-are-we-replacing-these"></a>Miért érdemes azt lecserélendő ezeket?

A korábban ismertetett módon egy *leállt* Service Fabric-csomópont egy csomópont szándékosan megcélzott, a csomópont leállítása API-val.  A *le* csomópontnak számít (például a virtuális gép vagy a gép ki van kapcsolva) bármilyen más okból nem üzemel csomópontot.  Csomópont leállítása API-val a rendszer nem fedi fel információkat megkülönböztetésére *leállt* csomópontok és *le* csomópontok.

Emellett egyes API-k által visszaadott hibák miatt nem, leíró jellegű, azok lehet.  Például az ad meg a csomópont leállítása API egy már *leállt* csomópont visszatér a hiba *InvalidAddress*.  Ez a tapasztalat lehet javítani.

Egy csomópont le van állítva, az az időtartam is "végtelen" mindaddig, amíg a csomópont Start API meghívása.  Azt tapasztaltuk, ez problémákat okozhat, és sok hibalehetőséget rejtő lehet.  Ha például azt már tudja, problémák, ahol a felhasználó meghívása a csomópont leállítása API egy csomóponton, és majd megfeledkeztem róla.  Később, nem egyértelmű, ha a csomópont nem volt *le* vagy *leállt*.


## <a name="introducing-the-node-transition-apis"></a>A csomópont átmeneti API-k ismertetése

Azt már tárgyalt ezeket a problémákat egy új API-készletet felett.  Az új csomópont átmeneti API-t (felügyelt: [StartNodeTransitionAsync()][snt]) is használható a Service Fabric csomópontjaival áttérés egy *leállt* állapotáról, vagy való áttérés a egy *leállt* állapot állapotát egy normál értékre.  Vegye figyelembe, hogy a "Start" az API-t be nevét nem hivatkozik egy csomópont elindítása.  Hivatkozik, amely a rendszer végrehajtja a nyerjenek a csomópont vagy aszinkron művelet megkezdése *leállt* vagy állam elindítva.

**Használat**

Ha a csomópont átmeneti API-t nem kivételt meghívásakor, majd a rendszer elfogadta az aszinkron művelet, és hajtja végre.  Egy sikeres hívás nem jelenti azt, még a művelet befejeződött.  A művelet a jelenlegi állapotával kapcsolatos információk lekéréséhez az csomópont áttérési folyamat API meghívása (felügyelt: [GetNodeTransitionProgressAsync()][gntp]) csomópont átmeneti API meghívásakor használt GUID Ehhez a művelethez.  A csomópont áttérési folyamat API NodeTransitionProgress objektumot adja vissza.  Ez az objektum állapota tulajdonság határozza meg, hogy a művelet aktuális állapotát.  Ha az állapot "fut", a művelet végrehajtásakor.  Ha elkészült, akkor a művelet hiba nélkül befejeződött.  Ha ez hibát okozott, hiba történt a művelet végrehajtásakor.  Az eredmény tulajdonság kivétel tulajdonság jelzi, mi a probléma volt.  Lásd: https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate további információ az Eszközállapot-tulajdonságot, és a "Sample" szakaszban alábbi hitelesítésikód-példák.


**A leállított csomópont és a egy lefelé csomópont megkülönböztetése** Ha egy csomópont *leállt* használatával a csomópont átmeneti API-t, a csomópont-lekérdezés kimenete (felügyelt: [GetNodeListAsync()] [ nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) jelennek meg, hogy rendelkezik-e ez a csomópont- *IsStopped* tulajdonság értéke igaz.  Ez eltér az értékét a *NodeStatus* tulajdonság, amely tudatja Önnel *le*.  Ha a *NodeStatus* tulajdonság értéke *le*, de *IsStopped* false (hamis), majd a csomópont nem állt le a csomópont átmeneti API-val, és *lefelé*  valamilyen más okból.  Ha a *IsStopped* tulajdonság értéke igaz, és a *NodeStatus* tulajdonság *le*, majd a csomópont átmeneti API-val leállt.

Indítása egy *leállt* csomópont a csomópont átmeneti API-val, hogy működik a fürt normál tagjaként adja vissza.  A csomópont-lekérdezés API-t a kimenet megjeleníti *IsStopped* hamis értéket, és *NodeStatus* néven, amely nem le (például be).


**Korlátozott időtartamú** a csomópont átmeneti API használatakor, hogy állítsa le a csomópontot, a szükséges paraméterek egyike *stopNodeDurationInSeconds*, jelöli, hogy mennyi idő másodpercben, hogy a csomópont *leállt*.  Ez az érték az engedélyezett tartományon, amelyben legalább 600, 14400 legfeljebb kell lennie.  Ez az idő lejárta után a csomópont magát az állapot mentése automatikusan újraindul.  Tekintse meg egy példát a használati minta 1.

> [!WARNING]
> Kerülje a csomópont átmeneti API-k és a csomópont leállítása és elindítása csomópont API-k.  A javaslat, hogy csak a csomópont átmeneti API-val.  > Ha egy csomópont már lett leállítva, hogy a csomópont leállítása API-val, akkor el kell használata előtt indítsa el csomópont az API-val a > csomópont átmeneti API-k.

> [!WARNING]
> Több csomópont átmeneti API-hívások ugyanazon a csomóponton egyidejűleg nem hajtható végre.  Ilyen esetben a csomópont átmeneti API-t fog > throw egy FabricException NodeTransitionInProgress egy ErrorCode tulajdonság értékével.  Ha rendelkezik egy adott csomóponton csomópontváltó > lett elindítva, kell megvárni, amíg a művelet feldolgozása (befejezve, Faulted vagy ForceCancelled) megkezdése előtt egy > új átmenet ugyanazon a csomóponton.  Engedélyezettek a Parallel csomópont átmenet hívások különböző csomópontokon.


#### <a name="sample-usage"></a>Példa


**1. példa** – a következő példa a csomópont átmeneti API csomópont leállítása.

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

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**2. példa** – a következő példa elindítja egy *leállt* csomópont.  Az első mintából néhány segédmetódusokat használ.

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

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Mintául szolgáló 3** – a következő minta bemutatja a helytelen használat.  A szintaxis helytelen. mivel a *stopDurationInSeconds* biztosít nagyobb, mint a megengedett tartományon.  StartNodeTransitionAsync() egy végzetes hibával meghiúsul, mert nem fogadta el a műveletet, és a folyamat API nem hívható.  Ebben a példában az első mintából néhány segédmetódusokat.

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

**4. minta** – a következő minta bemutatja a hibaadatokat, amelyek a csomópont átmeneti API által kezdeményezett művelet elfogadható, de később sikertelen végrehajtása során a csomópont áttérési folyamat API visszaad.  Abban az esetben, sikertelen lesz, mivel a csomópont átmeneti API-t próbálja meg elindítani a csomópont, amely nem létezik.  Ebben a példában az első mintából néhány segédmetódusokat.

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

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StopNodeAsync_System_String_System_Numerics_BigInteger_System_Fabric_CompletionMode_
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StartNodeAsync_System_String_System_Numerics_BigInteger_System_String_System_Int32_System_Fabric_CompletionMode_System_Threading_CancellationToken_
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient#System_Fabric_FabricClient_QueryClient_GetNodeListAsync_System_String_
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartNodeTransitionAsync_System_Fabric_Description_NodeTransitionDescription_System_TimeSpan_System_Threading_CancellationToken_
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_GetNodeTransitionProgressAsync_System_Guid_System_TimeSpan_System_Threading_CancellationToken_
