---
title: "Azure Service Fabric-fürt skálázása| Microsoft Docs"
description: "Ez az oktatóanyag a Service Fabric-fürtök gyors skálázásának módját ismerteti."
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
ms.date: 02/06/2018
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 21ef6d1981464d5943338c20fb06a218fc0e447d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-scale-a-service-fabric-cluster"></a>Oktatóanyag: Service Fabric-fürt skálázása

Ez az oktatóanyag egy sorozat második része, amely a meglévő fürtök horizontális fel- és leskálázásának módját mutatja be. Az oktatóanyag végére elsajátíthatja a fürtök skálázásának és a hátramaradt erőforrások eltávolításának módját.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A fürtcsomópontok számának olvasása
> * Fürtcsomópontok hozzáadása (horizontális felskálázás)
> * Fürtcsomópontok eltávolítása (horizontális leskálázás)

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) létrehozása az Azure-ban sablon használatával
> * Fürt horizontális fel- és leskálázása
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Az API Management üzembe helyezése a Service Fabrickel](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdése előtt:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Telepítse az [Azure PowerShell-modul 4.1-es vagy újabb verzióját](https://docs.microsoft.com/powershell/azure/install-azurerm-ps), vagy az [Azure CLI 2.0-ás verzióját](/cli/azure/install-azure-cli).
- Biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) létrehozása az Azure-ban
- Ha Windows-fürtöt telepít, állítson be egy Windows fejlesztési környezetet. Telepítse a [Visual Studio 2017](http://www.visualstudio.com) szoftvert, valamint az **Azure-fejlesztési**, **ASP.NET- és webes fejlesztési**, továbbá a **.NET Core platformfüggetlen fejlesztési** számítási feladatokat.  Ezután hozzon létre egy [.NET fejlesztési környezet](service-fabric-get-started.md).
- Ha Linux-fürtöt telepít, állítson be Java fejlesztési környezetet [Linux](service-fabric-get-started-linux.md) vagy [MacOS](service-fabric-get-started-mac.md) operációs rendszeren.  Telepítse a [Service Fabric parancssori felületet](service-fabric-cli.md). 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Azure-parancsok végrehajtása előtt jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Az oktatóanyag ezen részének sikeres elvégzéséhez a Service Fabric-fürthöz és a virtuálisgép-méretezési csoporthoz (amelyen a fürt üzemel) is csatlakoznia kell. A virtuálisgép-méretezési csoport az az Azure-erőforrás, amelyen az Azure Service Fabric fut az Azure-ban.

Ha csatlakozik egy fürthöz, adatokat kérdezhet le róla. A fürtről megtudhatja, hogy milyen csomópontok létezéséről tud. A fürthöz való csatlakozást megvalósító következő kódrészlet a sorozat első részében létrehozott tanúsítványt használja. Ne felejtse a saját értékeit megadni az `$endpoint` és a `$thumbprint` változóhoz.

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

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Miután csatlakozott, egy parancs használatával lekérheti a fürt egyes csomópontjainak állapotát. A PowerShell esetében használja a `Get-ServiceFabricClusterHealth`, az **sfctl** esetében pedig az `sfctl cluster select` parancsot.

## <a name="scale-out"></a>Horizontális felskálázás

A horizontális felskálázás során további virtuálisgép-példányokat ad a méretezési csoporthoz. Ezek a példányok lesznek a Service Fabric által használt csomópontok. A Service Fabric tudja, ha a méretezési csoport új példányokkal bővül (felskálázással), és automatikusan ennek megfelelően jár el. A következő kód név alapján megkeres egy méretezési csoportot, és 1-gyel növeli a **kapacitását**.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Ez a kód 6-ra állítja a kapacitást.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>Horizontális leskálázás

A leskálázás ugyanúgy működik, mint a felskálázás, azzal a különbséggel, hogy itt csökken a **kapacitás** értéke. A méretezési csoport horizontális leskálázásakor virtuálisgép-példányokat töröl a méretezési csoportból. A Service Fabric általában nem tudja, hogy mi történt, és azt gondolja, hogy valamelyik csomópont hiányzik. A Service Fabric ekkor a fürt hibás állapotát jelzi. Ha szeretné elkerülni ezt, tájékoztatnia kell a Service Fabricet, hogy a csomópont eltűnése várható.

### <a name="remove-the-service-fabric-node"></a>A Service Fabric-csomópont eltávolítása

> [!NOTE]
> Ez a rész kizárólag a *Bronz* tartóssági szintre vonatkozik. A tartóssággal kapcsolatos további információ: [Service Fabric-fürtök kapacitástervezése][durability].

A virtuálisgép-méretezési csoportok horizontális leskálázásakor a méretezési csoport (a legtöbb esetben) a legutoljára létrehozott virtuálisgép-példányt távolítja el. Így tehát meg kell keresnie az utoljára létrehozott, egyező Service Fabric-csomópontot. Az utolsó csomópont azonosításához a legnagyobb értékű `NodeInstanceId` tulajdonságot kell keresnie egyes Service Fabric-csomópontokon. Az alábbi példakód ezen tulajdonság alapján rendezi a csomópontpéldányokat, és visszaadja a legnagyobb azonosítóértékkel rendelkező példány adatait. 

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

A Service Fabric-fürtnek értesülnie kell róla, hogy ez a csomópont el lesz távolítva. Három lépést kell végrehajtania:

1. Tiltsa le a csomópontot, hogy ezentúl ne replikálja az adatokat.  
PowerShell: `Disable-ServiceFabricNode`  
sfcli: `sfctl node disable`

2. Állítsa le a csomópontot, hogy a Service Fabric-futtatókörnyezet szabályszerűen álljon le, és az alkalmazás kapjon egy megszakítási kérelmet.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfcli: `sfctl node transition --node-transition-type Stop`

2. Távolítsa el a csomópontot a fürtről.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfcli: `sfctl node remove-state`

A fenti három lépés alkalmazása után a csomópont eltávolítható a méretezési csoportból. A [Bronzon][durability] kívül bármely más tartóssági szint használata esetén a rendszer automatikusan végrehajtja ezeket a lépéseket a méretezési csoport adott példányának eltávolításakor.

A következő kódrészlet lekérdezi az utolsó létrehozott csomópontot, majd letiltja, leállítja, és eltávolítja a fürtről.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

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

Az alábbi **sfctl** kódban a következő parancs kéri le a legutóbb létrehozott csomópont **node-name** értékét: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> A következő **sfctl**-lekérdezésekkel ellenőrizheti az egyes lépések állapotát
>
> **Inaktiválási állapot ellenőrzése**  
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Leállítási állapot ellenőrzése**  
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>


### <a name="scale-in-the-scale-set"></a>A méretezési csoport horizontális leskálázása

Most, hogy a Service Fabric-csomópont el lett távolítva a fürtből, a virtuálisgép-méretezési csoport leskálázható. Az alábbi példa 1-gyel csökkenti a méretezési csoport kapacitását.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Ez a kód a kapacitást 5-re állítja.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A fürtcsomópontok számának olvasása
> * Fürtcsomópontok hozzáadása (horizontális felskálázás)
> * Fürtcsomópontok eltávolítása (horizontális leskálázás)


Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan frissíthetők a fürtök futtatókörnyezetei.
> [!div class="nextstepaction"]
> [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
