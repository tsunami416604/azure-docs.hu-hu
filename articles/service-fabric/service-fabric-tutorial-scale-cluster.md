---
title: "Az Azure Service Fabric-fürt méretezése |} Microsoft Docs"
description: "Ismerje meg gyorsan a Service Fabric-fürt méretezése."
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2017
ms.author: adegeo
ms.openlocfilehash: e1d35bcd51349e6460d50acec0d9706fcd291e89
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="scale-a-service-fabric-cluster"></a>A Service Fabric-fürt méretezése

Ebben az oktatóanyagban része egy sor és bemutatja, hogyan és a meglévő fürt méretezése. Amikor végzett, akkor a fürt méretezése és a bal oldali átvevő erőforrásokat újraépítése.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Olvassa el a fürt csomópontok száma
> * Adja hozzá a fürtcsomópontok (bővített)
> * Távolítsa el a fürt csomópontjai (skála)

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Telepítse a [Azure Powershell 4.1-es vagy újabb verziója](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) vagy [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) az Azure-on
- Ha telepít egy Windows-fürt, a Windows környezet beállítása. Telepítés [Visual Studio 2017](http://www.visualstudio.com) és a **Azure fejlesztési**, **ASP.NET és a webes fejlesztési**, és **.NET Core platformfüggetlen fejlesztésekhez**munkaterhelések.  Hozzon létre egy [.NET fejlesztőkörnyezet](service-fabric-get-started.md).
- Ha a Linux-fürt központi telepítése, állítsa be a Java-fejlesztőkörnyezet a [Linux](service-fabric-get-started-linux.md) vagy [MacOS](service-fabric-get-started-mac.md).  Telepítse a [háló CLI szolgáltatás](service-fabric-cli.md). 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Az Azure-fiókjába történő bejelentkezéshez jelölje ki az előfizetését, Azure parancsok végrehajtása előtt.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Sikeres végrehajtásához az oktatóanyag ezen része, akkor csatlakoztatni kell a Service Fabric-fürt és a virtuálisgép-méretezési csoport (a fürt üzemeltető). A virtuálisgép-méretezési csoport, amelyen az Azure Service Fabric Azure-erőforrás.

Egy fürt való csatlakozáskor lekérdezhető információt. A fürt segítségével megtudhatja, milyen csomópontokkal kapcsolatos a fürt tudomást. A fürthöz a következő kódrészlet használja ugyanazt a tanúsítványt, amely az adatsorozat első részében szereplő létrejött. Ellenőrizze, hogy beállította a `$endpoint` és `$thumbprint` változókat, az értékek.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

Az a `Get-ServiceFabricClusterHealth` parancs állapota Önnek is visszaad a fürt minden csomópontja állapotának részleteit.

## <a name="scale-out"></a>Horizontális felskálázás

Amikor kiterjesztése, hozzáadhat további virtuálisgép-példányok a méretezési. Ezek a példányok a Service Fabric használó csomópontok válik. A Service Fabric tudja, amikor a méretezési (kiterjesztése) által hozzáadott több példánya van-e, és automatikusan reagál. A következő kód jogosultságot kap a skála szerint nevét, és növeli a **kapacitás** a skála 1 állítja be.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

A frissítési művelet befejezése után futtassa a `Get-ServiceFabricClusterHealth` parancsot az új csomópont információk megtekintéséhez.

## <a name="scale-in"></a>A méretezési

A méretezés megegyezik a kiterjesztése, azzal a különbséggel alsó használhatja **kapacitás** érték. A méretezési csoportban lévő méretezni, akkor távolítsa el virtuálisgép-példányok a méretezési készlet. Általában a Service Fabric tudnak Mi történt, és szerinte csomópontja elveszett. A Service Fabric majd jelentést készít a fürt állapota nem kifogástalan. Ha szeretné megakadályozni, hogy rossz állapotban, akkor tájékoztatnia kell a service fabric, hogy a csomópont eltűnnek a várt.

### <a name="remove-the-service-fabric-node"></a>Távolítsa el a service fabric-csomópont

> [!NOTE]
> Ez a rész csak vonatkozik a *bronz* tartóssági szint. Tartóssági kapcsolatos további információkért lásd: [Service Fabric-fürt kapacitástervezés][durability].

Amikor egy virtuálisgép-méretezési csoportban lévő méretezni, a méretezési készletben (a legtöbb esetben) eltávolítja az utolsó létrehozott virtuálisgép-példányt. Ezért kell a megfelelő, legutóbbi létrehozása a service fabric-csomópont található. Az utolsó csomópont található a legnagyobbnak ellenőrzésével `NodeInstanceId` a service fabric-csomópontokon a tulajdonság értéke. 

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

A service fabric-fürt tudnia kell, hogy ez a csomópont fog eltávolítani. Kell venni a három lépésben történik:

1. Tiltsa le a csomóponton, úgy, hogy az már nem egy replikálja az adatokat.  
`Disable-ServiceFabricNode`

2. Állítsa le a kiszolgálót, hogy a service fabric-futtatókörnyezet szabályszerűen leáll, és az alkalmazás lekérdezi a megszakítási kérelmet.  
`Start-ServiceFabricNodeTransition -Stop`

2. Távolítsa el a csomópontot a fürtből.  
`Remove-ServiceFabricNodeState`

Miután a fenti három lépést a csomópont alkalmaztak, akkor eltávolíthatja a méretezési. Bármely tartóssági szint mellett használata [bronz][durability], ezeket a lépéseket, ha a méretezési példányt eltávolítják a történik.

A következő kódrészletet lekérdezi az utolsó létrehozott csomópont, letiltja, leáll, és eltávolítja a csomópontot a fürtből.

```powershell
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300
    
    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }
    
    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

### <a name="scale-in-the-scale-set"></a>A méretezési csoportban lévő méretezése

Most, hogy a service fabric-csomópont eltávolították a fürtből, a virtuálisgép-méretezési csoport is méretezhető. Az alábbi példában a méretezési készlet kapacitásának 1 csökken.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Olvassa el a fürt csomópontok száma
> * Adja hozzá a fürtcsomópontok (bővített)
> * Távolítsa el a fürt csomópontjai (skála)


A következő előzetes az alábbi oktatóanyag áttekintésével megismerheti, hogyan telepítsen egy alkalmazást, és az API management használata.
> [!div class="nextstepaction"]
> [Alkalmazás üzembe helyezése](service-fabric-tutorial-deploy-api-management.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster