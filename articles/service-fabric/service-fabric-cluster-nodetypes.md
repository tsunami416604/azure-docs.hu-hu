---
title: "Azure Service Fabric csomóponttípusok és a virtuális gép méretezési készletek |} Microsoft Docs"
description: "Megtudhatja, hogyan típusok kapcsolódnak a virtuálisgép-méretezési Azure Service Fabric-csomópont állítja be, és távolról csatlakoztatása egy méretezési állítsa be a példány vagy fürtcsomóponton."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/5/2018
ms.author: chackdan
ms.openlocfilehash: 720bb83c9d8540549852ce78ee1709f8c8717348
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric csomóponttípusok és a virtuális gép skálázása beállítása
Virtuálisgép-méretezési csoportok olyan Azure számítási erőforrás. Méretezési csoportok segítségével telepíthetnek és kezelhetnek olyan virtuális gépek gyűjteménye. Állítson be egy külön méretezési készletben megadhat egy Azure Service Fabric-fürt minden csomópont típushoz. Egymástól függetlenül az egyes csomóponttípusok méretezése felfelé vagy lefelé, eltérő számú portok nyitva és különböző teljesítmény-mérőszámait használja.

Az alábbi ábrán látható, amely két csomópont típusai, előtér- és háttérszolgáltatások nevű fürt. Minden csomópont az öt csomóponttal rendelkezik.

![Egy fürt, amely két csomópont tartozik][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Virtuálisgép-méretezési készlet példányok leképezése csomópontok
A fenti ábrán látható, méretezési készlet példányok példányt 0 kezdjék, és növelje meg az 1. A csomópont nevének számozására is megjelenik. Csomópont BackEnd_0 például a háttér-méretezési csoport 0 példány. Ez különösen méretezési öt példánya, BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 és BackEnd_4 nevű van.

A méretezési növelheti, ha egy új példánya jön létre. A méretezés új példány nevének általában a virtuális gépek méretezési csoportjának név, valamint a következő példányok számát. A jelen példában BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Méretezési készlet terheléselosztók leképezése csomóponttípusok, a méretezés beállítása
Ha az Azure portálon lévő telepítve, vagy használja a minta Azure Resource Manager-sablon, egy erőforráscsoportba tartozó összes erőforrást találhatók. Megtekintheti az egyes méretezési készlet vagy csomópont azokat a terheléselosztókat. A terheléselosztó neve a következő formátumot használja: **LB -&lt;csomóponttípus&gt;**. Példa: LB-sfcluster4doc-0, a következő ábrán látható módon:

![További források][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli kapcsolódás a virtuálisgép-méretezési készlet példányt vagy egy fürt csomópontja
Állítson be egy külön méretezési készletben, amelyet megadott, a fürt minden egyes csomóponttípus. A csomóponttípusok felfelé vagy lefelé egymástól függetlenül méretezheti. Másik virtuális gép termékváltozatok is használhatja. Egypéldányos virtuális gépeket, eltérően méretezési készlet példányok nem rendelkezik a saját virtuális IP-címek. Ez komoly kihívást jelenthet, amikor egy IP-címet és portot, amelynek távoli csatlakozás az egy adott példányt keres.

Egy IP-címet és portot, amelynek távoli csatlakozás az egy adott példányt megkereséséhez kövesse az alábbi lépéseket.

**1. lépés**: a virtuális IP-címének a csomóponttípus olvasson be a bejövő NAT-szabályok a távoli asztal protokoll (RDP).

Először lekérni az erőforrás-definíciójának részeként meghatározott bejövő NAT szabályok értékek `Microsoft.Network/loadBalancers`.

Az Azure portálon, a betöltés terheléselosztó lapon válassza ki a **beállítások** > **bejövő forgalmat kezelő NAT-szabályok**. Ez lehetővé teszi az IP-címet, és a portot, amelynek az első méretezési távolról csatlakozni állítsa be a példány. 

![Terheléselosztó][LBBlade]

Az alábbi ábrán az IP-cím és port nem **104.42.106.156** és **3389-es**.

![NAT-szabályok][NATRules]

**2. lépés**: a portot, amelyet segítségével távolról kapcsolódni a megadott méretezési készlet példányt, vagy a csomópont található.

A méretezési példányok térkép csomópontokra. A méretezési készlet információ használatával határozhatja meg a pontos port használatára.

Portok foglal le, amely megfelel a méretezési készlet példány növekvő sorrendben. A korábbi előtér csomóponttípus például az alábbi táblázat az egyes öt csomópont példányok portokat sorolja fel. A méretezési készlet példányát azonosak maradjanak érvényesek.

| **Virtuálisgép-méretezési készlet példány** | **Port** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

**3. lépés**: távolról kapcsolódni a megadott méretezési készlet példányhoz.

A következő ábra bemutatja a távoli asztali kapcsolaton keresztül csatlakozni a FrontEnd_1 méretezési készlet példányhoz:

![Távoli asztali kapcsolat][RDP]

## <a name="change-the-rdp-port-range-values"></a>Az RDP-port tartományértékeknek módosítása

### <a name="before-cluster-deployment"></a>Fürttelepítés előtt
Ha a fürt beállítása a Resource Manager-sablon használatával, adja meg a tartomány `inboundNatPools`.

Keresse fel az erőforrás-definíció `Microsoft.Network/loadBalancers`. Keresse meg a leírását `inboundNatPools`.  Cserélje le a `frontendPortRangeStart` és `frontendPortRangeEnd` értékeket.

![inboundNatPools értékek][InboundNatPools]

### <a name="after-cluster-deployment"></a>Fürt telepítése után
Az RDP-port tartományértékeknek módosítjuk, a fürt telepítése után az összetettebb. Győződjön meg arról, hogy a virtuális gépek nem újrahasznosít, Azure PowerShell használatával adjon meg új értékeket. 

> [!NOTE]
> Győződjön meg arról, hogy rendelkezik-e Azure PowerShell 1.0-s vagy újabb verzióját a számítógépen. Ha nem rendelkezik Azure Powershell 1.0-s vagy újabb verzió, azt javasoljuk, hogy a leírt lépések végrehajtásával [hogyan Azure PowerShell telepítése és konfigurálása.](/powershell/azure/overview)

1. Jelentkezzen be az Azure-fiókjával. Ha a következő PowerShell-parancsot a sikertelen, ellenőrizze a PowerShell megfelelően telepítve.

    ```
    Login-AzureRmAccount
    ```

2. Részletes információkat a terheléselosztó és a leírás a értékeit `inboundNatPools`, futtassa a következő kódot:

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. Állítsa be `frontendPortRangeEnd` és `frontendPortRangeStart` a használni kívánt értékekre.

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>A távoli asztalhoz tartozó felhasználónév és jelszó csomópontok módosítása

Egy adott csomópont típusú csomópontokhoz tartozó jelszó módosításához kövesse az alábbi lépéseket. Ezeket a módosításokat a méretezési csoportban lévő összes jelenlegi és jövőbeli csomópontra érvényes lesz.

1. Nyissa meg a PowerShellt rendszergazdaként. 
2. Jelentkezzen be, és jelölje ki az előfizetését a munkamenet, futtassa a következő parancsokat. Módosítsa a `SUBSCRIPTIONID` paramétert az előfizetés-azonosító. 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. Futtassa a következő parancsfájlt. Használja a megfelelő `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME`, és `PASSWORD` értékeket. A `USERNAME` és `PASSWORD` értékei az új hitelesítő adatok használata a jövőben az RDP-munkamenetekhez. 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>További lépések
* Tekintse meg a [a "Üzembe helyezés bárhol" szolgáltatás és az Azure által kezelt fürtökkel összehasonlítása áttekintése](service-fabric-deploy-anywhere.md).
* További tudnivalók [fürt biztonsági](service-fabric-cluster-security.md).
* További tudnivalók a [Service Fabric SDK és az első lépések](service-fabric-get-started.md).

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
