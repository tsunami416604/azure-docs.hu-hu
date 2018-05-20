---
title: Az Azure mikroszolgáltatások hibáinak szimulálása |} Microsoft Docs
description: A szolgáltatások szabályos és ungraceful-hibákkal szemben támogatnia kell a módját.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: timlt
editor: ''
ms.assetid: 44af01f0-ed73-4c31-8ac0-d9d65b4ad2d6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: dccd8eeeda1a41f23c9e3dd9896e0630e2a7a0a4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="simulate-failures-during-service-workloads"></a>Hibák szimulálása a szolgáltatások számítási feladatai közben
Azure Service Fabric tesztelhetőségi forgatókönyvei a fejlesztők nem foglalkoznia az egyéni hibák foglalkozik. Nincsenek forgatókönyvek, azonban ha explicit kihagyásos ügyfél munkaterhelési és sikertelen lehet szükség. Az ügyfél munkaterhelési és hibák kihagyásos biztosítja, hogy a szolgáltatás van ténylegesen néhány művelet végrehajtása során hiba történik. Megadott tesztelhetőségi biztosító felügyeleti, ezek lehetnek a munkaterhelés végrehajtásának pontos pontokon. A hibák, az alkalmazás különböző állapotok indukciós megkeresheti a hibák és minőségének javítása.

## <a name="sample-custom-scenario"></a>Egyéni mintaforgatókönyv
Ez a vizsgálat jeleníti meg az olyan forgatókönyvekben, amelyek az üzleti munkaterhelés interleaves [szabályos és ungraceful hibák](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions). A hibák kell előidézett, a szolgáltatási műveletek vagy a legjobb eredmények elérése érdekében számítási közepén.

Példa egy szolgáltatás, amely elérhetővé teszi a munkaterhelések négy keresztül bemutatjuk: A, B, C és D. mindegyike megfelel a munkafolyamatok és számítási, tárolási vagy a kettőn lehet. Az egyszerűség kedvéért azt fogja absztrakt a munkaterhelések ki ebben a példában. Az ebben a példában végre különböző hibák a következők:

* A RestartNode: Ungraceful hiba szimulálása a számítógép újraindítását.
* RestartDeployedCodePackage: Ungraceful hiba szimulálása a szolgáltatás gazdafolyamat összeomlik.
* RemoveReplica: Szabályos hiba szimulálása a replika eltávolítása.
* A MovePrimary: Szabályos hiba szimulálása a Service Fabric terheléselosztó által indított replika helyezi át.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.ServiceManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.ClusterManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.ApplicationManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.ServiceManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.ServiceManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
