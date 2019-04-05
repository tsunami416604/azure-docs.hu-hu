---
title: Az Azure Service Fabric programozott skálázása |} A Microsoft Docs
description: Méretezése az Azure Service Fabric-fürt vagy programozott módon, megfelelően egyéni eseményindítók
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
ms.openlocfilehash: 552c9820cca4380c00e1bf435fdb3d068c0690fb
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047939"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>A Service Fabric-fürt programozott skálázása 

Azure-ban futó Service Fabric-fürtök a virtual machine scale sets épülnek.  [Fürtméretezés](./service-fabric-cluster-scale-up-down.md) ismerteti, hogyan Service Fabric-fürtök vertikálisan fel-vagy manuálisan, vagy az automatikus skálázási szabályok. Ez a cikk hitelesítő adatok kezelése és a fürtök skálázásának módját ismerteti, vagy ki az fluent Azure számítási SDK-t, amely egy speciális forgatókönyv. Áttekintéséhez olvassa el a [Azure méretezési műveletek koordinálása a programozott módszerekkel](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Hitelesítő adatok kezelése
Méretezés kezelésére való írásával szolgáltatás az egyik kihívás, hogy a szolgáltatás hozzáférjen a virtuális gép méretezési csoport erőforrásainak nélkül egy interaktív bejelentkezést kell lennie. A Service Fabric-fürt eléréséhez akkor egyszerűen, ha a méretezési szolgáltatás módosítja a saját Service Fabric-alkalmazás, de a méretezési eléréséhez szükséges hitelesítő adatokat. Jelentkezzen be, használhatja a [szolgáltatásnév](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) hoztak létre a [Azure CLI-vel](https://github.com/azure/azure-cli).

Egy egyszerű szolgáltatást az alábbi lépéseket követve hozható létre:

1. Jelentkezzen be az Azure parancssori felület (`az login`) a virtuálisgép-méretezési csoport-hozzáféréssel rendelkező felhasználóként beállítása
2. Az egyszerű szolgáltatás létrehozásához `az ad sp create-for-rbac`
    1. Jegyezze fel az appId-(ügyfél-azonosító néven máshol), a neve, a jelszót és a bérlői későbbi használatra.
    2. Emellett az előfizetés-azonosítója, amelyet a tekinthet meg `az account list`

A fluent számítási kódtár használatával ezeket a hitelesítő adatokat az alábbiak szerint lehet bejelentkezni (vegye figyelembe, hogy alapvető fluent Azure típusok, például `IAzure` szerepelnek a [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) csomag):

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

Miután bejelentkezett, scale set példányszám keresztül kérhetők le `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Méretezés
SDK-t használja a fluent Azure számítási, néhány hívások esetén – a virtuális gép méretezési hozzáadható példányok

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Azt is megteheti virtuális gép méretezési készlet mérete is kezelhetők a PowerShell-parancsmagokkal. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) a virtuálisgép-méretezési készlet objektumot is lekérheti. A jelenlegi kapacitás keresztül érhető el a `.sku.capacity` tulajdonság. Miután megváltoztatta a kapacitás a kívánt értékre, az Azure-beli virtuálisgép-méretezési frissíthetők a [ `Update-AzVmss` ](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) parancsot.

Amikor csomópontot ad manuálisan, hozzáadása egy méretezési csoport példánya kell lennie minden, ami szükséges egy új Service Fabric-csomópont elindítani, mert a méretezési csoport sablonját automatikusan az új példányok csatlakoztatása a Service Fabric-fürt kiterjesztéseket is tartalmaz. 

## <a name="scaling-in"></a>A méretezés

A méretezés hasonlít a horizontális felskálázás. A tényleges virtuálisgép-méretezési csoport beállítása a módosítások vannak gyakorlatilag ugyanaz. De lett korábban említett, a Service Fabric csak automatikusan törli a eltávolított csomópontokat tartóssági arany és ezüst szintű. Így a bronz-tartóssági horizontális leskálázási esetben szükség kommunikál a Service Fabric-fürt leállítása el kell távolítani a csomópontot, majd eltávolítja a állapotában.

A csomópont Felkészülés shutdown magában foglalja a keresés, a csomópontot, eltávolítja (a legutóbb hozzáadott virtuális gép méretezési készlet példány) és inaktiválása. Virtuális gép méretezési csoport példányaihoz kerül, a sorrendben vannak számozva, így újabb csomópontok összehasonlítja a numerikus utótagot a (melyik egyezés az alapul szolgáló virtulálisgép-skálázási beállítása példányok neve) a csomópontok nevében található. 

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

Miután található, el kell távolítani a csomópontot inaktiválhatók, és azonos segítségével távolítja el `FabricClient` példány és a `IAzure` példányának regisztrációját a korábban.

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

Mint a horizontális felskálázás, módosítsa a virtuálisgép-méretezési csoport PowerShell-parancsmagok kapacitásának is itt is használható, ha egy parancsfájl-kezelési módszer előnyösebb. Miután a virtuálisgép-példányt távolítja el, a Service Fabric-csomópont állapota távolíthatja el.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>További lépések

Ismerkedés a saját automatikus skálázási logika megvalósítása, ismerkedjen meg a következő fogalmak és hasznos API-kat:

- [Manuális vagy automatikus skálázási szabályok méretezése](./service-fabric-cluster-scale-up-down.md)
- [Fluent Azure kezelési kódtárak .NET-keretrendszerhez készült](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (hasznos egy Service Fabric-fürt alapjául szolgáló virtuális gépek méretezési folytatott kommunikációhoz)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (hasznos Service Fabric-fürt és a csomópontjai folytatott kommunikációhoz)
