---
title: 'Azure VPN-átjáró: Átjáró törlése: PowerShell'
description: Virtuális hálózati átjáró törlése a PowerShell használatával az Erőforrás-kezelő telepítési modellben.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: f351f14796ec736bd5525f139a518c9a0dd3d19f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162106"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Virtuális hálózati átjáró törlése a PowerShell használatával
> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Powershell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasszikus)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Van néhány különböző megközelítések, amit megtehetsz, ha törölni szeretné a virtuális hálózati átjáró egy VPN-átjáró konfiguráció.

- Ha mindent törölni szeretne, és újra szeretné kezdeni, mint egy tesztkörnyezet esetében, törölheti az erőforráscsoportot. Erőforráscsoport törlésekor a csoporton belüli összes erőforrás törlődik. Ez a módszer csak akkor ajánlott, ha nem szeretné megtartani az erőforráscsoport egyetlen erőforrását sem. Ezzel a módszerrel nem törölhet szelektíven csak néhány erőforrást.

- Ha meg szeretné tartani az erőforrások egy részét az erőforráscsoportban, a virtuális hálózati átjáró törlése kissé bonyolultabbá válik. A virtuális hálózati átjáró törlése előtt először törölnie kell az átjárótól függő erőforrásokat. Az alábbi lépések a létrehozott kapcsolatok típusától és az egyes kapcsolatok függő erőforrásaitól függenek.

## <a name="before-beginning"></a>Mielőtt hozzálát



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Töltse le a legújabb Azure Resource Manager PowerShell-parancsmagokat.

Töltse le és telepítse az Azure Resource Manager PowerShell-parancsmagok legújabb verzióját. A PowerShell-parancsmagok letöltéséről és telepítéséről az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben talál további információt.

### <a name="2-connect-to-your-azure-account"></a>2. Csatlakozzon Azure-fiókjához.

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

```powershell
Connect-AzAccount
```

Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzSubscription
```

Ha egynél több előfizetéssel rendelkezik, adja meg a használni kívánt előfizetést.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Helyek közötti VPN-átjáró törlése

Az S2S-konfigurációhoz tartozó virtuális hálózati átjáró törléséhez először törölnie kell a virtuális hálózati átjáróhoz tartozó minden egyes erőforrást. Az erőforrásokat függőségek miatt bizonyos sorrendben törölni kell. Az alábbi példákkal való munka során meg kell adni néhány értéket, míg más értékek kimeneti eredményt jelentenek. A példákban a következő konkrét értékeket használjuk demonstrációs célokra:

Virtuális hálózat neve: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjáró neve: GW1<br>

A következő lépések az Erőforrás-kezelő telepítési modelljére vonatkoznak.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Szerezd meg a törölni kívánt virtuális hálózati átjárót.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Ellenőrizze, hogy a virtuális hálózati átjáró rendelkezik-e kapcsolattal.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Törölje az összes kapcsolatot.

A rendszer kérheti az egyes kapcsolatok törlésének megerősítését.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Törölje a virtuális hálózati átjárót.

A rendszer kérheti az átjáró törlésének megerősítését. Ha az S2S-konfiguráció mellett p2S-konfigurációval is rendelkezik ehhez a virtuális hálózathoz, a virtuális hálózati átjáró törlése figyelmeztetés nélkül automatikusan leválasztja az összes P2S-ügyfelet.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjáró törölve lett. A következő lépésekkel törölheti a már nem használt erőforrásokat.

### <a name="5-delete-the-local-network-gateways"></a>5 Törölje a helyi hálózati átjárókat.

A megfelelő helyi hálózati átjárók listájának beszereznie.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Törölje a helyi hálózati átjárókat. A rendszer kérheti, hogy erősítse meg az egyes helyi hálózati átjárók törlését.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Törölje a nyilvános IP-cím erőforrásokat.

A virtuális hálózati átjáró IP-konfigurációinak beszereznie.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

A virtuális hálózati átjáróhoz használt nyilvános IP-cím erőforrások listájának beszereznie. Ha a virtuális hálózati átjáró aktív-aktív volt, két nyilvános IP-cím jelenik meg.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Törölje a nyilvános IP-erőforrásokat.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Törölje az átjáró alhálózatát, és állítsa be a konfigurációt.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Virtuális hálózat és virtuális hálózat VPN-átjáró törlése

V2V-konfigurációhoz tartozó virtuális hálózati átjáró törléséhez először törölnie kell minden olyan erőforrást, amely a virtuális hálózati átjáróhoz kapcsolódik. Az erőforrásokat függőségek miatt bizonyos sorrendben törölni kell. Az alábbi példákkal való munka során meg kell adni néhány értéket, míg más értékek kimeneti eredményt jelentenek. A példákban a következő konkrét értékeket használjuk demonstrációs célokra:

Virtuális hálózat neve: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjáró neve: GW1<br>

A következő lépések az Erőforrás-kezelő telepítési modelljére vonatkoznak.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Szerezd meg a törölni kívánt virtuális hálózati átjárót.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Ellenőrizze, hogy a virtuális hálózati átjáró rendelkezik-e kapcsolattal.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Előfordulhat, hogy más kapcsolatok is vannak a virtuális hálózati átjáróval, amelyek egy másik erőforráscsoport részét képezik. További kapcsolatok minden további erőforráscsoportban. Ebben a példában az RG2-ből származó kapcsolatokat ellenőrizzük. Futtassa ezt minden olyan erőforráscsoporthoz, amely kapcsolatban állhat a virtuális hálózati átjáróval.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Szerezd meg a kapcsolatok listáját mindkét irányban.

Mivel ez egy virtuális hálózat-virtuális hálózat konfiguráció, mindkét irányban szüksége van a kapcsolatok listájára.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Ebben a példában az RG2-ből származó kapcsolatokat ellenőrizzük. Futtassa ezt minden olyan erőforráscsoporthoz, amely kapcsolatban állhat a virtuális hálózati átjáróval.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Törölje az összes kapcsolatot.

A rendszer kérheti az egyes kapcsolatok törlésének megerősítését.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Törölje a virtuális hálózati átjárót.

A rendszer kérheti a virtuális hálózati átjáró törlésének megerősítését. Ha a V2V-konfiguráció mellett p2S-konfigurációk at a virtuális hálózatok konfigurációja mellett rendelkezik, a virtuális hálózati átjárók törlése figyelmeztetés nélkül automatikusan leválasztja az összes P2S-ügyfelet.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjáró törölve lett. A következő lépésekkel törölheti a már nem használt erőforrásokat.

### <a name="6-delete-the-public-ip-address-resources"></a>6. A nyilvános IP-cím erőforrások törlése

A virtuális hálózati átjáró IP-konfigurációinak beszereznie.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

A virtuális hálózati átjáróhoz használt nyilvános IP-cím erőforrások listájának beszereznie. Ha a virtuális hálózati átjáró aktív-aktív volt, két nyilvános IP-cím jelenik meg.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Törölje a nyilvános IP-erőforrásokat. A rendszer kérheti a nyilvános IP törlésének megerősítését.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Törölje az átjáró alhálózatát, és állítsa be a konfigurációt.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Pont-hely VPN-átjáró törlése

A P2S-konfigurációhoz tartozó virtuális hálózati átjáró törléséhez először törölnie kell a virtuális hálózati átjáróhoz tartozó minden egyes erőforrást. Az erőforrásokat függőségek miatt bizonyos sorrendben törölni kell. Az alábbi példákkal való munka során meg kell adni néhány értéket, míg más értékek kimeneti eredményt jelentenek. A példákban a következő konkrét értékeket használjuk demonstrációs célokra:

Virtuális hálózat neve: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjáró neve: GW1<br>

A következő lépések az Erőforrás-kezelő telepítési modelljére vonatkoznak.


>[!NOTE]
> A VPN-átjáró törlésekor az összes csatlakoztatott ügyfél figyelmeztetés nélkül lelesz választva a virtuális hálózatról.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Szerezd meg a törölni kívánt virtuális hálózati átjárót.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Törölje a virtuális hálózati átjárót.

A rendszer kérheti a virtuális hálózati átjáró törlésének megerősítését.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjáró törölve lett. A következő lépésekkel törölheti a már nem használt erőforrásokat.

### <a name="3-delete-the-public-ip-address-resources"></a>3. A nyilvános IP-cím erőforrások törlése

A virtuális hálózati átjáró IP-konfigurációinak beszereznie.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

A virtuális hálózati átjáróhoz használt nyilvános IP-címek listájának begyűjtése. Ha a virtuális hálózati átjáró aktív-aktív volt, két nyilvános IP-cím jelenik meg.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Törölje a nyilvános IP-ket. A rendszer kérheti a nyilvános IP törlésének megerősítését.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Törölje az átjáró alhálózatát, és állítsa be a konfigurációt.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>VPN-átjáró törlése az erőforráscsoport törlésével

Ha nem aggódik amiatt, hogy az erőforrások at az erőforráscsoportban tartsa, és csak újra szeretné kezdeni, törölhet i a teljes erőforráscsoportot. Ez egy gyors módja annak, hogy távolítsa el mindent. A következő lépések csak az Erőforrás-kezelő telepítési modelljére vonatkoznak.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Az előfizetés összes erőforráscsoportjának listája.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Keresse meg a törölni kívánt erőforráscsoportot.

Keresse meg a törölni kívánt erőforráscsoportot, és tekintse meg az adott erőforráscsoport ban lévő erőforrások listáját. A példában az erőforráscsoport neve RG1. Módosítsa a példát az összes erőforrás listájának beolvasásához.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Ellenőrizze a listában szereplő erőforrásokat.

A lista visszaadásaután tekintse át, és ellenőrizze, hogy törölni szeretné-e az erőforráscsoport összes erőforrását, valamint magát az erőforráscsoportot is. Ha meg szeretné tartani az erőforrások egy részét az erőforráscsoportban, a cikk korábbi szakaszaiban ismertetett lépésekkel törölheti az átjárót.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Törölje az erőforráscsoportot és az erőforrásokat.

Az erőforráscsoport és az erőforráscsoportban lévő összes erőforrás törléséhez módosítsa a példát, és futtassa a parancsot.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Ellenőrizze az állapotát.

Az Azure az összes erőforrás törlése némi időt vesz igénybe. Ezzel a parancsmagból ellenőrizheti az erőforráscsoport állapotát.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

A visszaadott eredmény a "Sikeres" értéket jeleníti meg.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
