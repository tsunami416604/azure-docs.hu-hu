---
title: "Elindítása és leállítása a fürtcsomópontok Azure mikroszolgáltatások tesztelése |} Microsoft Docs"
description: "Útmutató van a Service Fabric-alkalmazás tesztelése indítása és leállítása a fürtcsomópontokon."
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: 
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 850fbc0c74811ec942292da64064dec867cd1b9e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>A Start csomópont és a leállítási csomópont API-k cseréje csomópont átmenet API-val

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Milyen hajtsa végre a csomópont leállítása és elindítása csomópont API-műveleteket?

A csomópont leállítása API (felügyelt: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) leáll a Service Fabric-csomópont.  A Service Fabric-csomópont a folyamat, nem a virtuális gép vagy gépek – a virtuális gép vagy a számítógép továbbra is futtatja.  A dokumentum többi részén "csomópont" a Service Fabric-csomópont fog jelenti.  Csomópont leállítása állapotba állítja be azt a *leállt* állapotba, ha azt nem tagja a fürtnek, amely nem nyújt szolgáltatásokat, így szimulálva egy *le* csomópont.  Ez akkor hasznos, a hibák hogy a rendszer az alkalmazás teszteléséhez.  A Start csomópont API (felügyelt: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) megfordítja a leállítása csomópont API, amely a csomópontnál vissza a normál állapotban.

## <a name="why-are-we-replacing-these"></a>Miért azt cserélni ezeket?

A korábban ismertetett módon egy *leállt* Service Fabric-csomópont a csomópont szándékosan megcélzott csomópont leállítása API használatával.  A *le* csomópontja a csomópont (pl. a virtuális gép vagy a számítógép ki van kapcsolva) bármilyen más okból nem működik.  A Stop csomópont API-t, a rendszer nem fed fel különböztetheti meg az információkat *leállt* csomópontok és *le* csomópontok.

Emellett az ezen API-k által visszaadott hibák tartoznak, leíró jellegű, azok lehet.  Például DISPID a csomópont leállítása API már egy *leállt* csomópont visszatér a hiba *InvalidAddress*.  Ez a felület javítható ki.

Az időtartam, egy csomópont le legyen állítva a, "végtelen" amíg van a csomópont Start API.  Jelenleg ez problémákat okozhat, és lehet, hogy hibalehetőség talált.  Például ha egy felhasználó a csomópont leállítása csomópont API meghívása, és majd elfelejtette kapcsolatos problémák is láttuk.  Később, nem egyértelmű, ha a csomópont nem volt *le* vagy *leállt*.


## <a name="introducing-the-node-transition-apis"></a>A csomópont átmenet API-k ismertetése

Azt is venni ezeket a problémákat fent egy új sor API-k.  Az új csomópont átmenet API-t (felügyelt: [StartNodeTransitionAsync()][snt]) is használható a Service Fabric-csomópont való áttérés egy *leállt* állapotát, vagy hogy az egy *leállt* állapotát normál állapotát.  Vegye figyelembe, hogy a "Start" nevében az API nem hivatkozik, csomópont elindítása.  Egy aszinkron művelet, amely a rendszer végrehajtja a csomópont, vagy hogy verziótól hivatkozik *leállt* vagy elindította az állapot.

**Használat**

Ha a csomópont átmenet API nem lépett fel kivételhiba meghívásakor, majd a rendszer elfogadta az aszinkron művelet, és hajtja végre.  Sikeres meghívását nem feltétlenül jelenti azt, a művelet még befejeződött.  Ahhoz, hogy a tevékenység jelenlegi állapotára vonatkozó adatokat, a csomópont átviteli folyamat API hívása (felügyelt: [GetNodeTransitionProgressAsync()][gntp]) csomópont átmenet API meghívása ehhez a művelethez használt GUID.  A csomópont átviteli folyamat API NodeTransitionProgress objektum beállítása/beolvasása.  Ez az objektum State tulajdonsága meghatározza a művelet aktuális állapotát.  Ha a állapota "fut" a művelet végrehajtása.  Ha elkészült, a művelet hiba nélkül befejeződött.  Ha a hibás, hiba történt a művelet végrehajtása.  A Result tulajdonsága Exception tulajdonság tájékoztatja arról, mi a probléma volt.  Lásd: https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate további információt az állapot tulajdonság, és példákat a "Példa" című szakaszt.


**Egy leállított csomópont és lefelé csomópont megkülönböztetése** Ha egy csomópont *leállt* a csomópont átmenet API-val, a csomópont-lekérdezés kimenetét (felügyelt: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) jelennek meg, hogy rendelkezik-e a csomópont egy *IsStopped* tulajdonság értéke TRUE.  Ez az érték eltér a *NodeStatus* tulajdonságot, amelynek megtudhatja, hogy *le*.  Ha a *NodeStatus* tulajdonság értéke *le*, de *IsStopped* értéke HAMIS, akkor a csomópont nem állította le a csomópont átmenet API használatával, és *le* valamilyen más okból miatt.  Ha a *IsStopped* tulajdonság értéke igaz, és a *NodeStatus* tulajdonság *le*, akkor azt le lett állítva a csomópont átmenet API használatával.

Indítása egy *leállt* csomópont átmenet API használatával csomópont visszatér, úgy, hogy működik a fürt normál tagjaként.  A csomópont-lekérdezés API ekkor *IsStopped* hamis értéket, és *NodeStatus* , valamit, hogy nem működik (pl.).


**Korlátozott időtartam** amikor a csomópont átmenet API használatával, a szükséges paraméterek közül az egyik csomópont leállítása *stopNodeDurationInSeconds*, adja meg annak a csomópont tartása másodpercben *leállt*.  Ez az érték, amelynek legalább 600 és 14400 legfeljebb az engedélyezett tartományon belül kell lennie.  Az idő lejárta után a csomópont magát a állapotát automatikusan újraindul.  Tekintse meg a minta 1 alatt használati példát.

> [!WARNING]
> Kerülje a csomópont átmenet API-k és a csomópont leállítása és elindítása csomópont API-k.  A javaslat, hogy a csomópont átmenet API csak használja.  > Ha egy csomópont már nincs leállt, a csomópont leállítása API használatával, akkor el kell indítani a Start csomópont API-jával használata előtt a > csomópont átmenet API-k.

> [!WARNING]
> Több csomópont átmenet API-hívások nem végezhető el, párhuzamosan ugyanazon a csomóponton.  Ilyen esetben a csomópont átmenet API fog > throw egy FabricException NodeTransitionInProgress ErrorCode tulajdonság értékkel.  Miután egy adott csomóponton csomópont átmenet > lett elindítva, meg kell várnia, amíg a művelet megkezdése előtt eléri a egy terminál állapota (befejezve, Faulted vagy ForceCancelled) egy > új átmenet ugyanazon a csomóponton.  Engedélyezettek a Parallel csomópont átmenet hívások különböző csomópontokon.


#### <a name="sample-usage"></a>Példa


**Minta 1** -az alábbi példában a csomópont átmenet API csomópont leállítása.

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

**Minta 2** -a következő mintát elindul egy *leállt* csomópont.  Az első mintából néhány segédmódszereket használ.

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

**Minta 3** -a következő példában helytelen használat.  A szintaxis érvénytelen mert a *stopDurationInSeconds* biztosít nagyobb, mint a megengedett tartományon.  StartNodeTransitionAsync() egy végzetes hibával meghiúsul, mert nem fogadta el a műveletet, és a folyamatban lévő API nem hívható.  Ez a minta az első mintából néhány segédmódszereket használja.

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

**Minta 4** -a következő példában a hibaadatokat, amelyek az eredmény a csomópont átviteli folyamat API a csomópont átmenet API által kezdeményezett művelet elfogadható, de később sikertelen végrehajtása közben.  Abban az esetben ez sikertelen, mert a csomópont átmenet API megkísérli indítja el a csomópontot, amely nem létezik.  Ez a minta az első mintából néhány segédmódszereket használja.

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
[nodequeryps]: https://docs.microsoft.com/powershell/servicefabric/vlatest/Get-ServiceFabricNode?redirectedfrom=msdn
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartNodeTransitionAsync_System_Fabric_Description_NodeTransitionDescription_System_TimeSpan_System_Threading_CancellationToken_
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_GetNodeTransitionProgressAsync_System_Guid_System_TimeSpan_System_Threading_CancellationToken_
