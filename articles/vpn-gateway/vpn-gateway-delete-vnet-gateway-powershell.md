---
title: 'Virtuális hálózati átjáró törlése: PowerShell: Az Azure Resource Manager |} A Microsoft Docs'
description: Törölje a virtuális hálózati átjáró PowerShell használatával a Resource Manager-alapú üzemi modellben.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: bf1cc3b95e8335b6e24dd405d82e5c51d2a8d11b
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792684"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>PowerShell-lel virtuális hálózati átjáró törlése
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasszikus)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Van néhány, különböző megközelítési módot használ, akkor is igénybe vehet, ha meg szeretné a VPN-átjáró konfigurálása a virtuális hálózati átjáró törlése.

- Ha azt szeretné, törölnie kell minden, és kezdje újra a folyamatot, hasonlóan a tesztkörnyezetben, törölheti az erőforráscsoportot. Amikor töröl egy erőforráscsoportot, a csoportban lévő összes erőforrást törli. Ez a módszer csak akkor javasolt, ha nem szeretné megőrizni a az erőforrások az erőforráscsoportban. Ez a megközelítés csak néhány erőforrásokat külön-külön nem törölhető.

- Ha azt szeretné, hogy egyes erőforrások az erőforráscsoportban, némileg összetettebb egy virtuális hálózati átjáró törlése folyamatban válik. A virtuális hálózati átjáró törlése előtt törölnie kell a olyan erőforrások, amelyek az átjáró függ. A lépéseket követheti az Ön által létrehozott kapcsolatok típusát, és minden kapcsolat esetén a tőle függő erőforrások függenek.

## <a name="before-beginning"></a>Mielőtt hozzálát

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Töltse le a legújabb Azure Resource Manager PowerShell-parancsmagokat.

Töltse le és telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. Letöltése és telepítése a PowerShell-parancsmagokkal kapcsolatos további információkért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Csatlakozás az Azure-fiókjával.

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

```powershell
Connect-AzAccount
```

Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzSubscription
```

Ha több előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Site-to-Site VPN gateway törlése

S2S-konfigurációnak a virtuális hálózati átjáró törléséhez először törölnie kell az egyes virtuális hálózati átjáróhoz tartozó erőforrások. Erőforrások függőségek miatt bizonyos sorrendben kell törölni. Az egyes alábbi példák használatakor az értékeket meg kell adni, míg más értékek a következők egy kimeneti eredmény. A következő egyedi értékeket a példákban, bemutatási célokra használjuk:

Virtuális hálózat neve: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjáró neve: GW1<br>

Az alábbi lépéseket a Resource Manager-alapú üzemi modellre vonatkoznak.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. A virtuális hálózati átjárót, amelyet törölni szeretne kaphat.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Ellenőrizze, hogy ha a virtuális hálózati átjáró van-e a kapcsolatokat.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Összes kapcsolatot törli.

A rendszer felkérheti az egyes kapcsolatok a törlés jóváhagyásához.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. A virtuális hálózati átjáró törlése.

A rendszer felkérheti az átjáró törlésének megerősítéséhez. Ha egy virtuális hálózaton P2S konfiguráció mellett az S2S-konfigurációnak, a virtuális hálózati átjáró törlése folyamatban automatikusan leválasztja az összes figyelmeztetés nélkül P2S-ügyfelekhez.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjáró törölve lett. A következő lépések segítségével inaktiválja azokat az erőforrásokat, amelyek már nem használ.

### <a name="5-delete-the-local-network-gateways"></a>5 a helyi hálózati átjárók törlése.

A megfelelő helyi hálózati átjárók listájának beolvasása.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

A helyi hálózati átjárók törlése. A rendszer felkérheti az egyes helyi hálózati átjáró a törlés jóváhagyásához.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. A nyilvános IP-cím erőforrások törlése.

A virtuális hálózati átjáró IP-konfigurációk beolvasása.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Nyilvános IP-cím erőforrások a virtuális hálózati átjáróhoz használt listájának beolvasása. Ha a virtuális hálózati átjáró aktív-aktív volt, látni fogja a két nyilvános IP-címet.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Törli a nyilvános IP-cím-erőforrást.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Az átjáró-alhálózat törlése, és állítsa be a konfigurációt.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>A virtuális hálózatok közötti VPN gateway törlése

Virtuális hálózati átjáró V2V konfiguráció törléséhez először törölnie kell az egyes virtuális hálózati átjáróhoz tartozó erőforrások. Erőforrások függőségek miatt bizonyos sorrendben kell törölni. Az egyes alábbi példák használatakor az értékeket meg kell adni, míg más értékek a következők egy kimeneti eredmény. A következő egyedi értékeket a példákban, bemutatási célokra használjuk:

Virtuális hálózat neve: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjáró neve: GW1<br>

Az alábbi lépéseket a Resource Manager-alapú üzemi modellre vonatkoznak.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. A virtuális hálózati átjárót, amelyet törölni szeretne kaphat.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Ellenőrizze, hogy ha a virtuális hálózati átjáró van-e a kapcsolatokat.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Előfordulhat, hogy a virtuális hálózati átjáró egyéb kapcsolatokat, amelyek részei egy másik erőforráscsoportban található. Ellenőrizze, hogy az egyes további erőforráscsoportokban további kapcsolatokat. Ebben a példában a rendszer ellenőrzi a RG2 érkező kapcsolatokat. Futtassa ezt az egyes erőforráscsoportokban, hogy rendelkezik, amely előfordulhat, hogy rendelkezik egy kapcsolatot a virtuális hálózati átjárót.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. A kapcsolatok listájának beolvasása mindkét irányban.

Mivel ez egy VNet – VNet-konfiguráció, kell mindkét irányban a kapcsolatok listájának megtekintéséhez.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Ebben a példában a rendszer ellenőrzi a RG2 érkező kapcsolatokat. Futtassa ezt az egyes erőforráscsoportokban, hogy rendelkezik, amely előfordulhat, hogy rendelkezik egy kapcsolatot a virtuális hálózati átjárót.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Összes kapcsolatot törli.

A rendszer felkérheti az egyes kapcsolatok a törlés jóváhagyásához.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. A virtuális hálózati átjáró törlése.

A rendszer felkérheti a virtuális hálózati átjáró a törlés jóváhagyásához. P2S konfiguráció a virtuális hálózatok kell a V2V konfiguráció mellett, ha a virtuális hálózati átjárók törlése automatikusan leválasztja az összes figyelmeztetés nélkül P2S-ügyfelekhez.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjáró törölve lett. A következő lépések segítségével inaktiválja azokat az erőforrásokat, amelyek már nem használ.

### <a name="6-delete-the-public-ip-address-resources"></a>6. A nyilvános IP-cím erőforrások törlése

A virtuális hálózati átjáró IP-konfigurációk beolvasása.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Nyilvános IP-cím erőforrások a virtuális hálózati átjáróhoz használt listájának beolvasása. Ha a virtuális hálózati átjáró aktív-aktív volt, látni fogja a két nyilvános IP-címet.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Törli a nyilvános IP-cím-erőforrást. A rendszer felkérheti a nyilvános IP-cím a törlés jóváhagyásához.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Az átjáró-alhálózat törlése, és állítsa be a konfigurációt.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Pont – hely VPN gateway törlése

Virtuális hálózati átjáró P2S konfiguráció törléséhez először törölnie kell az egyes erőforrások a virtuális hálózati átjáróhoz tartozó. Erőforrások függőségek miatt bizonyos sorrendben kell törölni. Az egyes alábbi példák használatakor az értékeket meg kell adni, míg más értékek a következők egy kimeneti eredmény. A következő egyedi értékeket a példákban, bemutatási célokra használjuk:

Virtuális hálózat neve: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjáró neve: GW1<br>

Az alábbi lépéseket a Resource Manager-alapú üzemi modellre vonatkoznak.


>[!NOTE]
> Ha törli a VPN-átjárót, az összes csatlakoztatott ügyfelek figyelmeztetés nélkül a virtuális hálózat le lesz választva.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. A virtuális hálózati átjárót, amelyet törölni szeretne kaphat.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. A virtuális hálózati átjáró törlése.

A rendszer felkérheti a virtuális hálózati átjáró a törlés jóváhagyásához.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjáró törölve lett. A következő lépések segítségével inaktiválja azokat az erőforrásokat, amelyek már nem használ.

### <a name="3-delete-the-public-ip-address-resources"></a>3. A nyilvános IP-cím erőforrások törlése

A virtuális hálózati átjáró IP-konfigurációk beolvasása.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

A virtuális hálózati átjáróhoz használt nyilvános IP-címek listájának beolvasása. Ha a virtuális hálózati átjáró aktív-aktív volt, látni fogja a két nyilvános IP-címet.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Törölje a nyilvános IP-címek. A rendszer felkérheti a nyilvános IP-cím a törlés jóváhagyásához.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Az átjáró-alhálózat törlése, és állítsa be a konfigurációt.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Az erőforráscsoport törlésével a VPN gateway törlése

Ha csak át szeretné újrakezdeni, amelyek nem azok tartja az erőforrásokat az erőforráscsoport, egy egész erőforráscsoportot törölheti. Ez az, hogy gyorsan távolítson el minden. Az alábbi lépéseket csak a Resource Manager-alapú üzemi modellre vonatkoznak.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Az erőforráscsoportok listájának lekérése az előfizetéséhez.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Keresse meg a törölni kívánt erőforráscsoportot.

Keresse meg az erőforráscsoportot, amelyet szeretne törölni, és az adott erőforráscsoportba tartozó erőforrások listájának megtekintése. A példában az erőforráscsoport neve RG1. Módosítsa úgy a példát lekérni az összes erőforrás listáját.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Ellenőrizze az erőforrások listájában.

Ha a listát ad vissza, át, és győződjön meg arról, hogy szeretné-e törölheti az erőforráscsoportot, valamint magát az erőforráscsoportot az összes erőforrást. Ha azt szeretné, hogy egyes erőforrások az erőforráscsoportban, kövesse a lépéseket a korábbi szakaszokban, ez a cikk az átjárót.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Törölje az erőforráscsoportot és az erőforrásokat.

Az erőforráscsoport és az erőforráscsoportban lévő összes erőforrás törléséhez módosítsa úgy a példát, és futtassa.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Ellenőrizze az állapotát.

Összes erőforrást törli az Azure némi időt vesz igénybe. Ez a parancsmag használatával ellenőrizheti az erőforráscsoport állapota.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

A visszaadott eredmény látható a "Sikeres".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
