---
title: "Azure Service Fabric programozott skálázás |} Microsoft Docs"
description: "Egy bejövő vagy kimenő Azure Service Fabric-fürt méretezése programozott módon, az egyéni eseményindítók szerint"
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: mikerou
ms.openlocfilehash: 46b0b62f92abbac57bc27bbcdd5821eafedf5519
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>A Service Fabric-fürt méretezése programozott módon 

Az Azure Service Fabric-fürt méretezése alapjai dokumentációjában ismertetett a [fürtméretezés](./service-fabric-cluster-scale-up-down.md). Hogy a cikk ismerteti, hogyan Service Fabric-fürtök épülő virtuálisgép-méretezési csoportok és a manuális vagy automatikus skálázása szabályokkal is méretezhető. Ez a dokumentum speciális forgatókönyvekhez koordináló Azure skálázási műveletek programozási módszerek megvizsgálja. 

## <a name="reasons-for-programmatic-scaling"></a>Programozott méretezéshez okok
A sok esetben skálázás manuális vagy automatikus skálázása szabályok segítségével található jó megoldás. Más esetekben azonban nem feltétlenül a megfelelő méretezése. Ezek a módszerek lehetséges hátrányai a következők:

- Manuális skálázás meg kell-e jelentkezni, és igényelhetnek a skálázási műveletek. Ha a skálázási műveletek szükségesek gyakran vagy időpontban előre nem látható, ez a megközelítés nem lehet jó megoldás.
- Automatikus méretezése szabályok eltávolítása egy virtuálisgép-méretezési csoport egy példányát, ha azok nem automatikusan eltávolítása az adott csomópont Tudásbázis társított Service Fabric-fürt kivéve, ha a csomópont a tartóssági szint ezüst vagy arany van. Automatikus méretezése szabályok működik, a méretezési készletben szint (és nem a Service Fabric szinten), mert automatikus méretezése szabályok is távolítsa el a Service Fabric-csomópont őket szabályosan leállítása nélkül. Goromba csomópont eltávolítása fog hagy "szellemrekord" a Service Fabric-csomópont állapota méretezési a műveletek után. Egy adott (vagy egy szolgáltatás) kell rendszeresen törölnie a Service Fabric-fürtöt az eltávolított csomópont állapota.
  - Vegye figyelembe, hogy az arany vagy ezüst tartóssági szint csomóponttípus automatikusan be lesz tisztítása csomópontokat eltávolítani.  
- Bár vannak [sok metrikák](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) automatikus méretezése szabályok által támogatott, akkor még mindig korlátozott. Ha a forgatókönyv nem vonatkozik a készletben lévő egyes metrika alapján méretezéshez, majd automatikus méretezése szabályok nem lehet jó választás.

E tényezők alapján, érdemes több testreszabott automatikus méretezési modellek végrehajtásához. 

## <a name="scaling-apis"></a>Méretezési API-k
Az Azure API-k léteznek, amelyek lehetővé teszik a virtuális gép programozott módon használható alkalmazások méretezési készletek és a Service Fabric-fürtök. Meglévő automatikus méretezése lehetőségek nem működnek az adott esetben, ha ezen API-k lehetővé teszik egyéni méretezési logika végrehajtásához. 

Egy megközelítést a "otthoni végrehajtott" az automatikus skálázás funkció végrehajtására egy új állapotmentes szolgáltatások hozzáadása a Service Fabric-alkalmazás skálázási műveleteinek a felügyeletét. A szolgáltatáson belül `RunAsync` módszer, eseményindítók készlete is megállapítja, hogy skálázás szükség (többek között a paraméterek maximális fürt például ellenőrzése és a méretezés cooldowns).   

A virtuális gép méretezési készlet kapcsolati (mindkettő ellenőrizze a virtuálisgép-példányok jelenlegi száma és módosítható) használja az API a [Folyékonyan beszél Azure felügyeleti számítási könyvtár](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A Folyékonyan beszél számítási kódtár biztosít egy könnyen használható API-t használják a virtuálisgép-méretezési készlet.

Segítségével kommunikál a Service Fabric-fürt magát, [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

A fürt méretezhető szolgáltatásként természetesen a méretezési kód nem szükséges. Mindkét `IAzure` és `FabricClient` kapcsolódhatnak a kapcsolódó Azure-erőforrások távolról, így a méretezési szolgáltatás könnyen lehet egy konzolalkalmazás vagy a Windows-szolgáltatás fut a kívül a Service Fabric-alkalmazás. 

## <a name="credential-management"></a>Hitelesítő adatok kezelése
Méretezés kezelésére rendszerekben a szolgáltatás az egyik kihívás az, hogy a szolgáltatás kell tudni hozzáférni a virtuálisgép-méretezési készlet erőforrások egy interaktív bejelentkezés nélkül. A Service Fabric-fürt használata esetén könnyen a méretezési szolgáltatás módosítja a saját Service Fabric-alkalmazás, de a méretezési eléréséhez szükséges hitelesítő adatokat. Jelentkezzen be, használhatja a [egyszerű](https://github.com/Azure/azure-sdk-for-net/blob/Fluent/AUTH.md#creating-a-service-principal-in-azure) létrehozni a [Azure CLI 2.0](https://github.com/azure/azure-cli).

A szolgáltatás egyszerű hozhatók létre az alábbi lépéseket:

1. Jelentkezzen be az Azure parancssori felület (`az login`) egy olyan felhasználó nevében, aki hozzáféréssel rendelkezik a virtuálisgép-méretezési beállítása
2. Az egyszerű szolgáltatásnév létrehozása a`az ad sp create-for-rbac`
    1. Jegyezze meg a appId (más néven "ügyfél-azonosító" máshol), a neve, a jelszót és a bérlői későbbi használatra.
    2. Konfigurálnia kell az előfizetési azonosító, amely tekinthetők meg`az account list`

A Folyékonyan beszél számítási könyvtár jelentkezhetnek be ezeket a hitelesítő adatokat az alábbiak szerint (vegye figyelembe, hogy core Folyékonyan beszél Azure típusok hasonlóan `IAzure` szerepelnek a [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) csomag):

```C#
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

```C#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Azt is megteheti virtuális gép méretezési készlet méretét is felügyelhetők a PowerShell-parancsmagokkal. [`Get-AzureRmVmss`](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmss)lekérheti a virtuálisgép-méretezési készlet objektumot. A jelenlegi kapacitásnál fogja tárolni a `.sku.capacity` tulajdonság. Miután megváltoztatta a kapacitás a kívánt értékre, az Azure állítsa be a virtuálisgép-méretezési frissíthető a [ `Update-AzureRmVmss` ](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/update-azurermvmss) parancsot.

Ha manuálisan ad hozzá egy csomópont, a méretezési példány hozzáadása kell lennie egy új Service Fabric-csomópont el, mivel a méretezési sablon elegendő automatikusan új példányok csatlakoztatása a Service Fabric-fürt kiterjesztéseket is tartalmaz. 

## <a name="scaling-in"></a>A méretezés

A méretezés hasonlít kiterjesztése. A tényleges virtuálisgép-méretezési beállítása módosítások vannak gyakorlatilag azonos. De volt korábban bemutatott, a Service Fabric csak automatikusan törli a szükségtelenné az eltávolított csomópontokat a tartós arany vagy ezüst. Igen, a bronz-tartóssági méretezési a helyzet, szükség az eltávolítandó csomópont leállítása a Service Fabric-fürt kommunikál, majd távolítsa el az állapotát.

A csomópont előkészítése az leállítási magában foglalja a tekinti a csomópontot eltávolítani (a legutóbb hozzáadott csomópont) keresése és inaktiválása. Nem kezdőérték csomópontok újabb csomópontok található összehasonlításával `NodeInstanceId`. 

```C#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n => n.NodeInstanceId)
        .FirstOrDefault();
```

Vegye figyelembe, hogy *kezdőérték* csomópontok látszólag nem mindig kövesse az egyezmény nagyobb példány azonosítók először törlődnek.

Az eltávolítandó csomópont található, ha inaktiválhatók, és eltávolítja a azonos `FabricClient` példány és a `IAzure` példányának regisztrációját a korábbi.

```C#
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

```C#
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="potential-drawbacks"></a>Lehetséges hátrányai

Ahogyan az a megelőző kódrészletek, létrehozása a saját skálázás szolgáltatás biztosítja a legmagasabb szintű vezérlő és az alkalmazás keresztül testreszabhatóság miatt a skálázás viselkedését. Ez lehet hasznos, ha pontosan meghatározhatja hogyan vagy ha egy alkalmazás méretezi-e a bejövő vagy kimenő igénylő forgatókönyvek. Azonban ez a vezérlő tartalmaz a kompromisszummal jár, kód összetettségét. Ezzel a megközelítéssel azt jelenti, hogy saját méretezési kódot, amely nem triviális kell.

Hogyan meg kell megközelíti a Service Fabric skálázás attól függ, hogy a forgatókönyvéhez. Ha skálázás ritka, csomópontok hozzáadásához és eltávolításához manuálisan nem elegendő. Az összetettebb forgatókönyveket automatikus méretezése szabályok és SDK-k teszi ki a szolgáltatás szoftveres kínál az hatékony alternatívák.

## <a name="next-steps"></a>Következő lépések

Első lépésként a saját automatikus skálázás logikát megvalósító, ismerkedjen meg a következő fogalmakat és hasznos API-kat:

- [Manuális vagy automatikus skálázása szabályokkal skálázás](./service-fabric-cluster-scale-up-down.md)
- [A .NET-hez Folyékonyan beszél Azure kezelési kódtárakat](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (hasznos a Service Fabric-fürt alapul szolgáló virtuálisgép-méretezési csoportok való kommunikáció)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (hasznos a Service Fabric-fürt és csomópontjainak való kommunikáció)
