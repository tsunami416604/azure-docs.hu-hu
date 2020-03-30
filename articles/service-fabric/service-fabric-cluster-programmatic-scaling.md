---
title: Azure Service Fabric programozott méretezés
description: Azure Service Fabric-fürt programozással vagy állapotban méretezése egyéni eseményindítók nak megfelelően
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: ffe07960c6d32bea8ec31b1fe8248b6abc2b63af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458289"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Service Fabric-fürt programozott méretezése 

Az Azure-ban futó Service Fabric-fürtök a virtuálisgép-méretezési csoportokra épülnek.  [Fürtméretezés](./service-fabric-cluster-scale-up-down.md) ismerteti, hogyan Service Fabric-fürtök manuálisan vagy automatikus skálázási szabályokkal méretezhető. Ez a cikk ismerteti, hogyan kezelheti a hitelesítő adatokat, és a fürt méretezése a folyékony Azure számítási SDK használatával, amely egy fejlettebb forgatókönyv használatával. Az áttekintésért olvassa el [az Azure skálázási műveleteinek koordinálásának programozott módszereit.](service-fabric-cluster-scaling.md#programmatic-scaling) 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Hitelesítő adatok kezelése
A skálázás kezelésére egy szolgáltatás írásának egyik kihívása, hogy a szolgáltatásnak interaktív bejelentkezés nélkül is hozzá kell tudnia férni a virtuálisgép-méretezési készlet erőforrásaihoz. A Service Fabric-fürt elérése egyszerű, ha a skálázási szolgáltatás módosítja a saját Service Fabric-alkalmazást, de a méretezési csoport eléréséhez hitelesítő adatokszükségesek. A bejelentkezéshez [használhatja](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) az [Azure CLI-vel](https://github.com/azure/azure-cli)létrehozott egyszerű szolgáltatás használatát.

Egyszerű szolgáltatás a következő lépésekkel hozható létre:

1. Jelentkezzen be az Azure`az login`CLI ( ) rendszerbe, mint a virtuálisgép-méretezési készlethez hozzáféréssel rendelkező felhasználó
2. A szolgáltatásnév létrehozása`az ad sp create-for-rbac`
    1. Jegyezze fel az appId (máshol "ügyfélazonosító" ), nevét, jelszavát és bérlőjét későbbi használatra.
    2. Szüksége lesz az előfizetés-azonosítójára is, amely megtekinthető a`az account list`

A folyékony számítási kódtár a következőképpen jelentkezhet be ezekkel `IAzure` a hitelesítő adatokkal (vegye figyelembe, hogy a [microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) csomag alapvető, folyékonyan beszélt Azure-típusokhoz hasonlóan):

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

Miután bejelentkezett, a méretezési példányok `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`száma lekérdezhető a segítségével.

## <a name="scaling-out"></a>Méretezés
A folyékony Azure compute SDK használatával a példányok néhány hívással hozzáadhatók a virtuálisgép-méretezési csoporthoz -

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Másik lehetőségként a virtuális gép méretezési csoport mérete is kezelhető PowerShell-parancsmagokkal. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss)lekérheti a virtuálisgép méretezési csoport objektumát. Az aktuális kapacitás a `.sku.capacity` szálláshelyen keresztül érhető el. Miután a kapacitást a kívánt értékre módosítja, az [`Update-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) Azure-ban beállított virtuálisgép-méretezési csoport frissíthető a paranccsal.

Mint amikor manuálisan ad hozzá egy csomópontot, egy méretezési csoport példányának hozzáadása kell minden, ami szükséges egy új Service Fabric-csomópont elindításához, mivel a méretezési csoport sablon bővítményeket tartalmaz, hogy automatikusan csatlakozzon az új példányok a Service Fabric-fürthöz. 

## <a name="scaling-in"></a>Méretezés a

A méretezés hasonló a horizontális felskálázáshoz. A virtuális gép tényleges méretezési készletének módosításai gyakorlatilag megegyeznek. De ahogy azt korábban már tárgyalta, a Service Fabric csak automatikusan törli az eltávolított csomópontok at arany vagy ezüst tartósságával. Így a Bronz-tartóssági skálázás esetén a Service Fabric-fürttel való interakciószükséges az eltávolítandó csomópont leállításához, majd az állapot ának eltávolításához.

A csomópont előkészítése a leállításhoz magában foglalja az eltávolítandó csomópont (a legutóbb hozzáadott virtuálisgép-méretezési csoport példánya) megkeresését és inaktiválását. A virtuálisgép-méretezési példányok számozása a hozzáadott sorrendben történik, így az újabb csomópontok a csomópontok nevében lévő számutótag összehasonlításával találhatók (amelyek megfelelnek az alapul szolgáló virtuálisgép-méretezési csoport példányainak nevéhez). 

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

Miután az eltávolítandó csomópont megtalálható, inaktiválható és eltávolítható `FabricClient` ugyanazzal `IAzure` a példánnyal és a korábbi példánysal.

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

A horizontális felskálázáshoz csakúgy, mint a powershell-parancsmagok a virtuálisgép-méretezési csoport kapacitásának módosításához, itt is használhatók, ha a parancsfájl-felügyeleti megközelítés előnyösebb. A virtuálisgép-példány eltávolítása után a Service Fabric-csomópont állapota eltávolítható.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>További lépések

A saját automatikus skálázási logika megvalósításának megkezdéséhez ismerkedjen meg a következő fogalmakkal és hasznos API-kkal:

- [Manuális méretezés vagy automatikus méretezési szabályok](./service-fabric-cluster-scale-up-down.md)
- [Fluent Azure Management Libraries for .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (hasznos a Service Fabric-fürt alapjául szolgáló virtuálisgép-méretezési csoportokkal való interakcióhoz)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (a Service Fabric-fürtés csomópontjaival való interakcióhoz hasznos)
