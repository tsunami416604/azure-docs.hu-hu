---
title: Az Azure Service Fabric-alkalmazások hibák szimulálása |} A Microsoft Docs
description: Hogyan felvértezni a szolgáltatások szabályos és végbemenjen meghibásodásokkal szemben.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: chackdan
editor: ''
ms.assetid: 44af01f0-ed73-4c31-8ac0-d9d65b4ad2d6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: ceb6ad1a6a1182d78c473b8b0387c365eb660065
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667208"
---
# <a name="simulate-failures-during-service-workloads"></a>Hibák szimulálása a szolgáltatások számítási feladatai közben
A testability alkalmazási helyzetek, az Azure Service Fabric lehetővé teszi a fejlesztőknek, nem kell többé vesződnie a sérült egyes hibák foglalkoznia. Vannak helyzetek, azonban, ahol explicit kihagyásos ügyfél számítási feladatok és a hibák akkor lehet szükség. Ügyfél számítási feladatok és hibák kihagyásos biztosítja, hogy a szolgáltatás ténylegesen működik valamilyen művelet során hiba történik. Adja meg a szabályozás, amely testability biztosít, ilyenek lehetnek a számítási feladatok végrehajtásának pontos időpontokban. A hibák, az alkalmazás különböző állapoton indukciós megkeresheti a hibák és minőségének javítására.

## <a name="sample-custom-scenario"></a>Egyéni mintaforgatókönyv
Ez a vizsgálat jeleníti meg az olyan forgatókönyvekben, amelyek az üzleti számítási feladat interleaves [szabályos és végbemenjen hibák](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions). A hibák lehet elérni, a középső szolgáltatási műveletek vagy számítási a legjobb eredmények elérése érdekében.

Vegyük végig egy szolgáltatás, amely négy számítási feladatokat egy példát: A, B, C és a d Minden egyes munkafolyamatok megfelel, és lehet, számítási, tárolási vagy vegyesen. Az egyszerűség kedvéért azt fogja absztrakt ki a számítási feladatok ebben a példában. Az ebben a példában végrehajtott különböző hibák a következők:

* RestartNode: Végbemenjen tartalék szimulálva egy gép újraindítása.
* RestartDeployedCodePackage: Végbemenjen hiba szimulálása a szolgáltatás gazdagép-folyamat leáll.
* RemoveReplica: Sikeres-e hiba szimulálása a replika eltávolítását.
* A MovePrimary: Sikeres-e hiba szimulálása a replika áthelyezését váltja ki, a Service Fabric terheléselosztó.

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
