---
title: 'A virtuális hálózati átjáró törlése: PowerShell: Azure Resource Manager |} Microsoft Docs'
description: A PowerShell használatával a Resource Manager üzembe helyezési modellel virtuális hálózati átjáró törlése.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: cherylmc
ms.openlocfilehash: a23a969f1381e3a10c81a903793bad2870b436f6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>A PowerShell használatával virtuális hálózati átjáró törlése
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasszikus)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Többféle különböző megközelítés közül választhat, ha törli a virtuális hálózati átjáró VPN gateway-konfigurációt.

- Ha teljes tartalmának törlése, és kezdje újra a folyamatot, ahogy a gyorsítás esetében is egy tesztkörnyezetben, törölheti az erőforráscsoportot. Ha töröl egy erőforráscsoport, a csoportban lévő összes erőforrást törli. Ez a módszer csak akkor javasolt, ha nem szeretné megtartani az erőforrások az erőforráscsoportban. Ezzel a megközelítéssel csak néhány erőforrásokat külön-külön nem törölhető.

- Ha meg szeretné tartani a erőforrások az erőforráscsoportban, a virtuális hálózati átjáró törlése válik kicsit bonyolultabb. A virtuális hálózati átjáró törlése előtt először törölnie kell az átjáró függő erőforrásokat. A szükséges lépések attól függ, a létrehozott kapcsolatok és a tőle függő erőforrások, minden egyes kapcsolathoz.

## <a name="before-beginning"></a>Mielőtt hozzálát

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Töltse le a legújabb Azure Resource Manager PowerShell-parancsmagokkal.

Töltse le és telepítse a legújabb verziót az Azure Resource Manager PowerShell-parancsmagokat. Letölti és telepíti a PowerShell-parancsmagokkal kapcsolatos további információkért lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Csatlakozás az Azure-fiókjával.

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

```powershell
Connect-AzureRmAccount
```

Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzureRmSubscription
```

Ha egynél több előfizetéssel rendelkezik, adja meg a használni kívánt előfizetést.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Telephelyek közötti VPN-átjáró törlése

Törölje a virtuális hálózati átjáró S2S-konfigurációt, először törölnie kell az egyes erőforrások, amelyek a virtuális hálózati átjáró vonatkozik. Erőforrások miatt függőségek meghatározott sorrendben kell törölni. Az alábbi néhány példa az használatakor az értékeket meg kell adni, miközben más értékek egy kimeneti eredmények. A következő megadott értékek a példákban, bemutatási célokra használjuk:

VNet-name: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjárójához megadott név: GW1<br>

Az alábbi lépéseket a Resource Manager üzembe helyezési modellel vonatkozik.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. A virtuális hálózati átjáró, amely a törölni kívánt beolvasása.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Ellenőrizze, hogy a virtuális hálózati átjáró rendelkezik-e a kapcsolatokat.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Törölje az összes kapcsolat.

A kapcsolatok mindegyikének a törlés megerősítéséhez kérheti.

```powershell
$Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. A virtuális hálózati átjáró törlése.

Az átjáró a törlés megerősítéséhez kérheti. Ha egy P2S-konfigurációt a vneten mellett S2S-konfigurációjáról, a virtuális hálózati átjáró törlése folyamatban lesz automatikusan ügyfelek leválasztása a összes P2S figyelmeztetés nélkül.


```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjáró törölve lett. A következő lépések segítségével törölje minden olyan erőforrásnál, amely már nincsenek használatban.

### <a name="5-delete-the-local-network-gateways"></a>5 helyi hálózati átjáró törlése.

A megfelelő helyi hálózati átjáró listájának lekérdezése.

```powershell
$LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Helyi hálózati átjáró törlése. Előfordulhat, hogy kérni fogja a helyi hálózati átjáró mindegyikének a törlés jóváhagyásához.

```powershell
$LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. A nyilvános IP-cím erőforrás törlése.

A virtuális hálózati átjáró IP-konfigurációk beolvasása.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

A nyilvános IP-cím erőforrások ehhez a virtuális hálózati átjáróhoz használt listájának lekérdezése. Ha a virtuális hálózati átjáró aktív-aktív volt, két nyilvános IP-cím jelenik meg.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

A nyilvános IP-erőforrások törlése.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Az átjáró-alhálózat törléséhez és a konfiguráció.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>VNet – VNet VPN-átjáró törlése

V2V konfiguráció esetén a virtuális hálózati átjáró törléséhez először törölnie kell az egyes erőforrások, amelyek a virtuális hálózati átjáró vonatkozik. Erőforrások miatt függőségek meghatározott sorrendben kell törölni. Az alábbi néhány példa az használatakor az értékeket meg kell adni, miközben más értékek egy kimeneti eredmények. A következő megadott értékek a példákban, bemutatási célokra használjuk:

VNet-name: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjárójához megadott név: GW1<br>

Az alábbi lépéseket a Resource Manager üzembe helyezési modellel vonatkozik.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. A virtuális hálózati átjáró, amely a törölni kívánt beolvasása.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Ellenőrizze, hogy a virtuális hálózati átjáró rendelkezik-e a kapcsolatokat.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Előfordulhat, hogy a virtuális hálózati átjáró egyéb kapcsolatokat, amelyek egy másik erőforráscsoportban. Ellenőrizze, hogy minden további erőforráscsoportban további kapcsolatokat. Ebben a példában keresése RG2 érkező kapcsolatok. Futtassa ezt minden erőforráscsoportban, hogy rendelkezik, amely lehet kapcsolódni a virtuális hálózati átjáró.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. A kapcsolatok listájának lekérdezése mindkét irányban.

Mivel ez egy VNet – VNet konfigurációs, kell mindkét irányban kapcsolatok listáját.

```powershell
$ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Ebben a példában keresése RG2 érkező kapcsolatok. Futtassa ezt minden erőforráscsoportban, hogy rendelkezik, amely lehet kapcsolódni a virtuális hálózati átjáró.

```powershell
 $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Törölje az összes kapcsolat.

A kapcsolatok mindegyikének a törlés megerősítéséhez kérheti.

```powershell
$ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. A virtuális hálózati átjáró törlése.

A virtuális hálózati átjáró törlésének megerősítéséhez kérheti. A Vnetek P2S konfigurációk kell a V2V konfigurációjában, ha a virtuális hálózati átjáró törlése lesz automatikusan ügyfelek leválasztása a összes P2S figyelmeztetés nélkül.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjáró törölve lett. A következő lépések segítségével törölje minden olyan erőforrásnál, amely már nincsenek használatban.

### <a name="6-delete-the-public-ip-address-resources"></a>6. A nyilvános IP-cím erőforrás törlése

A virtuális hálózati átjáró IP-konfigurációk beolvasása.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

A nyilvános IP-cím erőforrások ehhez a virtuális hálózati átjáróhoz használt listájának lekérdezése. Ha a virtuális hálózati átjáró aktív-aktív volt, két nyilvános IP-cím jelenik meg.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

A nyilvános IP-erőforrások törlése. A nyilvános IP-cím törlésének megerősítéséhez kérheti.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Az átjáró-alhálózat törléséhez és a konfiguráció.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Egy pont – hely típusú VPN-átjáró törlése

Törölje a virtuális hálózati átjáró P2S-konfigurációt, először törölnie kell az egyes erőforrások, amelyek a virtuális hálózati átjáró vonatkozik. Erőforrások miatt függőségek meghatározott sorrendben kell törölni. Az alábbi néhány példa az használatakor az értékeket meg kell adni, miközben más értékek egy kimeneti eredmények. A következő megadott értékek a példákban, bemutatási célokra használjuk:

VNet-name: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjárójához megadott név: GW1<br>

Az alábbi lépéseket a Resource Manager üzembe helyezési modellel vonatkozik.


>[!NOTE]
> Ha törli a VPN-átjárót, az összes kapcsolódó ügyfelek a VNet figyelmeztetés nélkül megszakad.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. A virtuális hálózati átjáró, amely a törölni kívánt beolvasása.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. A virtuális hálózati átjáró törlése.

A virtuális hálózati átjáró törlésének megerősítéséhez kérheti.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjáró törölve lett. A következő lépések segítségével törölje minden olyan erőforrásnál, amely már nincsenek használatban.

### <a name="3-delete-the-public-ip-address-resources"></a>3. A nyilvános IP-cím erőforrás törlése

A virtuális hálózati átjáró IP-konfigurációk beolvasása.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Használja a virtuális hálózati átjáró nyilvános IP-címek listájának. Ha a virtuális hálózati átjáró aktív-aktív volt, két nyilvános IP-cím jelenik meg.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

A nyilvános IP-cím törlése. A nyilvános IP-cím törlésének megerősítéséhez kérheti.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Az átjáró-alhálózat törléséhez és a konfiguráció.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Az erőforráscsoport törlésével VPN-átjáró törlése

Ha nem aggódik megőrzi az erőforrásokat az erőforráscsoport található, és szeretné elölről, törölheti a teljes erőforráscsoport. Ez az gyorsan távolítson el minden. Az alábbi lépéseket csak a Resource Manager üzembe helyezési modellel vonatkoznak.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Az erőforráscsoportok listájának lekérése az előfizetéséhez.

```powershell
Get-AzureRmResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Keresse meg a törölni kívánt erőforráscsoportot.

Keresse meg az erőforráscsoport, amelyet szeretne törölni, és az erőforráscsoport erőforrások listájának megtekintése. A példában az erőforráscsoport neve nem RG1. Módosítsa a példa az erőforrások listájának beolvasása.

```powershell
Find-AzureRmResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Ellenőrizze az erőforrások listájában.

Ha a listát ad vissza, át, és győződjön meg arról, hogy valóban törli az erőforráscsoportot, valamint az erőforráscsoportot, maga az összes erőforrást. Ha meg szeretné tartani a erőforrások az erőforráscsoportban, a lépések segítségével a cikk korábbi szakaszaiban ismertetett törölje az átjárót.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Az erőforráscsoport és erőforrások törlése.

Az erőforráscsoport és az erőforrás-csoportban lévő összes erőforrás törlése, módosítása a példa, és futtassa.

```powershell
Remove-AzureRmResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Ellenőrizze az állapotát.

Az Azure-erőforrások törlése némi időt vesz igénybe. Ez a parancsmag használatával ellenőrizheti az erőforráscsoport állapota.

```powershell
Get-AzureRmResourceGroup -ResourceGroupName RG1
```

A visszaadott eredmény jeleníti meg a "Sikeres".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
