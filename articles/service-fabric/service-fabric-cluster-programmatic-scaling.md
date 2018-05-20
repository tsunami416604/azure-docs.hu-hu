---
title: Azure Service Fabric programozott skálázás |} Microsoft Docs
description: Egy bejövő vagy kimenő Azure Service Fabric-fürt méretezése programozott módon, az egyéni eseményindítók szerint
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: dcf4721012fb8ec39bcd1de02c294747357b3539
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>A Service Fabric-fürt méretezése programozott módon 

Azure-ban futó Service Fabric-fürtök épülő virtuálisgép-méretezési készlet.  [A fürtméretezés](./service-fabric-cluster-scale-up-down.md) ismerteti, hogyan Service Fabric-fürtök méretezhetők, manuális vagy automatikus skálázása szabályait. Ez a cikk ismerteti, hogyan kezelheti a hitelesítő adatokat, és a fürt méretezése vagy használni a Folyékonyan beszél Azure számítási SDK, amely ennél összetettebb környezetben. Áttekintéséhez olvassa el a [programozási módszerek összehangolása a skálázás műveletek Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 

## <a name="manage-credentials"></a>Hitelesítő adatok kezelése
Méretezés kezelésére rendszerekben a szolgáltatás az egyik kihívás az, hogy a szolgáltatás kell tudni hozzáférni a virtuálisgép-méretezési készlet erőforrások egy interaktív bejelentkezés nélkül. A Service Fabric-fürt használata esetén könnyen a méretezési szolgáltatás módosítja a saját Service Fabric-alkalmazás, de a méretezési eléréséhez szükséges hitelesítő adatokat. Jelentkezzen be, használhatja a [egyszerű](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) létrehozni a [Azure CLI 2.0](https://github.com/azure/azure-cli).

A szolgáltatás egyszerű hozhatók létre az alábbi lépéseket:

1. Jelentkezzen be az Azure parancssori felület (`az login`) egy olyan felhasználó nevében, aki hozzáféréssel rendelkezik a virtuálisgép-méretezési beállítása
2. Az egyszerű szolgáltatásnév létrehozása a `az ad sp create-for-rbac`
    1. Jegyezze meg a appId (más néven "ügyfél-azonosító" máshol), a neve, a jelszót és a bérlői későbbi használatra.
    2. Konfigurálnia kell az előfizetési azonosító, amely tekinthetők meg `az account list`

A Folyékonyan beszél számítási könyvtár jelentkezhetnek be ezeket a hitelesítő adatokat az alábbiak szerint (vegye figyelembe, hogy core Folyékonyan beszél Azure típusok hasonlóan `IAzure` szerepelnek a [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) csomag):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Miután bejelentkezett, méretezési készlet példányszáma lekérdezhetők, keresztül `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Méretezés
SDK használata a Folyékonyan beszél Azure számítási, példányok adhatók hozzá a virtuálisgép-méretezési pár hívások - beállítása

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Azt is megteheti virtuális gép méretezési készlet méretét is felügyelhetők a PowerShell-parancsmagokkal. [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) lekérheti a virtuálisgép-méretezési készlet objektumot. A jelenlegi kapacitásnál keresztül érhető el a `.sku.capacity` tulajdonság. Miután megváltoztatta a kapacitás a kívánt értékre, az Azure állítsa be a virtuálisgép-méretezési frissíthető a [ `Update-AzureRmVmss` ](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) parancsot.

Ha manuálisan ad hozzá egy csomópont, a méretezési példány hozzáadása kell lennie egy új Service Fabric-csomópont el, mivel a méretezési sablon elegendő automatikusan új példányok csatlakoztatása a Service Fabric-fürt kiterjesztéseket is tartalmaz. 

## <a name="scaling-in"></a>A méretezés

A méretezés hasonlít kiterjesztése. A tényleges virtuálisgép-méretezési beállítása módosítások vannak gyakorlatilag azonos. De volt korábban bemutatott, a Service Fabric csak automatikusan törli a szükségtelenné az eltávolított csomópontokat a tartós arany vagy ezüst. Igen, a bronz-tartóssági méretezési a helyzet, szükség az eltávolítandó csomópont leállítása a Service Fabric-fürt kommunikál, majd távolítsa el az állapotát.

A csomópont előkészítése az leállítási magában foglalja a tekinti a csomópontot keresése eltávolítani (a legutóbb hozzáadott virtuális gép méretezési készlet példány) és inaktiválása. Virtuálisgép-méretezési készlet példánya a sorrendben Hozzáadás számozása, újabb csomópontok összehasonlítva a szám utótagot a csomópontok (amelyek egyeznek az alapul szolgáló virtuálisgép-méretezési megadott nevei példánynevek) található. 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Az eltávolítandó csomópont található, ha inaktiválhatók, és eltávolítja a azonos `FabricClient` példány és a `IAzure` példányának regisztrációját a korábbi.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Mint meg, a PowerShell-parancsmagok a módosítását a virtuálisgép-méretezési készlet kapacitása is használható itt egy parancsfájl-kezelési megoldás hatékonyabb, ha. A virtuálisgép-példányt eltávolítást követően a Service Fabric-csomópont állapota lehet eltávolítani.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>További lépések

Első lépésként a saját automatikus skálázás logikát megvalósító, ismerkedjen meg a következő fogalmakat és hasznos API-kat:

- [Manuális vagy automatikus skálázása szabályokkal skálázás](./service-fabric-cluster-scale-up-down.md)
- [A .NET-hez Folyékonyan beszél Azure kezelési kódtárakat](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (hasznos a Service Fabric-fürt alapul szolgáló virtuálisgép-méretezési csoportok való kommunikáció)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (hasznos a Service Fabric-fürt és csomópontjainak való kommunikáció)
