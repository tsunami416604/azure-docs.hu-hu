---
title: A zónaredundáns virtuális hálózati átjáró létrehozása az Azure rendelkezésre állási zónákban – előzetes verzió |} Microsoft Docs
description: A rendelkezésre állási zónák - Preview VPN Gateway és ExpressRoute átjárók telepítését.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/25/2018
ms.author: cherylmc
ms.openlocfilehash: a08c0f772965ddb2b40ac1ced1f26ade4cba3197
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017032"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>A zónaredundáns virtuális hálózati átjáró létrehozása az Azure rendelkezésre állási zónákban – előzetes

Telepítheti a VPN- és ExpressRoute átjárók [Azure rendelkezésre állási zónák](../availability-zones/az-overview.md). Ekkor a rugalmasság, a méretezhetőség és a magas rendelkezésre állás érdekében a virtuális hálózati átjárókat. Fizikailag és logikailag, üzembe helyezése az Azure rendelkezésre állási zónákban átjárók elválasztja az átjárók, régión belül Azure a helyszíni hálózati kapcsolatot a zóna szintű meghibásodásával védelmének beállításakor.

Zonal és zónaredundáns átjárók alapvető teljesítménybeli javításokat keresztül rendszeres virtuális hálózati átjárók rendelkeznek. Zónaredundáns vagy zonal virtuális hálózati átjáró létrehozása pedig gyorsabb, mint a más átjárók létrehozása. Ahelyett, hogy 45 percig tart, hajtsa végre a megfelelő körülbelül 15 perc ExpressRoute-átjáró, és a VPN-átjáró 19 perc időpontokban létrehozása.

### <a name="zrgw"></a>Zónaredundáns átjárók

Automatikus központi telepítése a virtuális hálózati átjárók keresztül a rendelkezésre állási zónák, zónaredundáns virtuális hálózati átjárók is használhat. Zónaredundáns átjárókkal a 99,99 % hasznos üzemidő SLA-t, a kritikus fontosságú, méretezhető szolgáltatásokat Azure GA is hasznosítására.

<br>
<br>

![zóna-redunant átjárók kép](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Zonal átjárók

A megadott zónában átjárói telepítéséhez zonal-átjárók használatára. Egy zonal átjáró telepítésekor az átjáró mindkét esetben az azonos rendelkezésre állási zónába lettek telepítve.

<br>
<br>

![zonal átjárók kép](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Átjáró-termékváltozatok

Zónaredundáns és zonal átjárók az új átjáró termékváltozatok kell használnia. Egyszer, [önálló regisztrálása a Preview](#enroll), látni fogja az új virtuális hálózati átjáró termékváltozatok összes, az Azure AZ-régiókat. Ezek termékváltozatok hasonlóak a ExpressRoute- és VPN-átjáró megfelelő Termékváltozatait, azzal a különbséggel, hogy adott zónaredundáns és zonal átjárókat.

Az új átjáró SKU van:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Nyilvános IP-cím termékváltozatok

Zónaredundáns átjárók és zonal átjárók mindkét támaszkodnak az Azure nyilvános IP-erőforrás *szabványos* Termékváltozat. Az Azure nyilvános IP-erőforrás konfigurációja határozza meg, hogy az átjáró üzembe helyezett zónaredundáns, vagy zonal. Ha létrehoz egy nyilvános IP-erőforrás a egy *alapvető* SKU, az átjáró nem fog minden zóna redundancia, és az átjáró erőforrások regionális lesz.

### <a name="pipzrg"></a>Zónaredundáns átjárók

Amikor hoz létre egy nyilvános IP cím használatával a **szabványos** nyilvános IP SKU zóna megadása nélkül, a viselkedés eltér attól függően, hogy az átjáró VPN-átjáró vagy ExpressRoute-átjárót. 

* A VPN-átjáró a két átjárópéldányokról lesz telepítve, három zónákhoz zóna-redundancia kívül bármely 2. 
* Egy ExpressRoute-átjáró esetén lehet több mint két olyan példányt, mert az átjáró is kiterjedhet a három zónákat között.

### <a name="pipzg"></a>Zonal átjárók

Amikor hoz létre egy nyilvános IP cím használatával a **szabványos** nyilvános IP-SKU, és adja meg a zóna (1, 2 vagy 3), az átjárópéldányokról lesz telepítve, az azonos zónába.

### <a name="piprg"></a>Regionális átjárók

Amikor hoz létre egy nyilvános IP cím használatával a **alapvető** nyilvános IP-SKU, az átjáró egy regionális átjáró van telepítve, és nem rendelkezik semmilyen beépítve az átjáró zóna-redundancia.

## <a name="before-you-begin"></a>Előkészületek

Vagy a számítógép, vagy az Azure-felhő rendszerhéj helyileg telepített PowerShell használható. Ha PowerShell telepítéséhez és használatához a helyileg, a szolgáltatás használatához a PowerShell modul legújabb verzióját.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Ha a PowerShell segítségével helyileg

Ha használ PowerShell helyileg a számítógépen, nem pedig felhő rendszerhéjának használatával, telepítenie kell PowerShell modul 6.1.1 vagy újabb verzióját. A PowerShell telepített verziója ellenőrzéséhez használja a következő parancsot:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1. Regisztrálás az előzetes verzió

Zónaredundáns vagy zonal átjáró konfigurálása előtt először önálló regisztrálnia kell az előfizetés Preview. Amennyiben van megadva az előfizetés, hozzákezdhet az új átjáró termékváltozatok összes a Azure AZ-régiók megtekintéséhez. 

Győződjön meg arról, hogy a bejelentkezett az Azure-fiókjával, és a kívánt engedélyezett az előzetes verzió-előfizetését használja. Az alábbi példát követve regisztrálása:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

A következő parancs használatával győződjön meg arról, hogy a "AllowVMSSVirtualNetworkGateway" funkció regisztrálva van-e az előfizetéséhez:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Az eredmény ebben a példában hasonlóan fog kinézni:

![kiosztott](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. A változók deklarálása

A példa lépései használt értékek listája látható. Emellett a példák némelyike változókkal deklarált belül a lépéseket. Ha ezeket a lépéseket a saját környezetben használ, ügyeljen arra, hogy cserélje le ezeket az értékeket a saját. Adja meg a helyet, győződjön meg arról, hogy támogatja-e a megadott régiót. További információkért lásd: a [gyakran ismételt kérdések](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>3. A virtuális hálózat létrehozása

Hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Hozzon létre egy virtuális hálózatot.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>4. Az átjáró alhálózatának hozzáadása

Az átjáró alhálózatának tartalmazza a fenntartott IP-címek, amelyek a virtuális hálózati átjáró szolgáltatások használják. Adja hozzá, és egy átjáró-alhálózatot beállításához használja az alábbi példákat:

Adja hozzá az átjáró alhálózatát.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

A virtuális hálózati átjáró alhálózati konfigurációjának beállítása

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5. Nyilvános IP-cím kérése
 
Ebben a lépésben válassza ki a létrehozni kívánt átjáró vonatkozó utasításokat. A kijelölés a zónák átjáró üzembe helyezéséhez a zónák a nyilvános IP-címhez megadott függ.

### <a name="ipzoneredundant"></a>Zónaredundáns átjárók

Egy nyilvános IP-cím kérése a **szabványos** PublicIpaddress SKU és bármely zóna nem ad meg. Ebben az esetben a szabványos nyilvános IP-cím létrehozott lesz zónaredundáns nyilvános IP-cím.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard
```

### <a name="ipzonalgw"></a>Zonal átjárók

Egy nyilvános IP-cím kérése a **szabványos** PublicIpaddress Termékváltozat. Adja meg a zóna (1, 2 vagy 3). A zónában lévő összes példány telepítve lesz.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Regionális átjárók

Egy nyilvános IP-cím kérése a **alapvető** PublicIpaddress Termékváltozat. Ebben az esetben az átjáró regionális átjáró telepítve van, és nem rendelkezik semmilyen beépítve az átjáró zóna-redundancia. A példány jönnek létre a területek, illetve.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. Az IP-konfiguráció létrehozása

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Az átjáró létrehozása

A virtuális hálózati átjáró létrehozása.

>[!NOTE]
>Ilyenkor az átjáró-Termékváltozat nem adható meg. A Termékváltozat automatikusan alapértelmezett ErGw1AZ ExpressRoute és VpnGw1AZ VPN-átjáró.
>

### <a name="for-expressroute"></a>Az ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>A VPN-átjáró

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Hogyan visszajelzés küldése

Köszönjük a visszajelzését lenne. E-mail küldése aznetworkgateways@microsoft.com jelentse az esetleges problémákat, vagy visszajelzést (pozitív vagy negatív) a zónaredundáns és zonal VPN- és az Express Route átjárók. Vegye fel a "["] vállalatnevet tárgyát. Az előfizetés-azonosító is tartalmazza, ha a jelentik a problémát.

## <a name="faq"></a>GYIK

### <a name="how-do-i-sign-up-for-the-preview"></a>Do I regisztrálás módja az előzetes?

Is [önregisztrációjára](#enroll) ebben a cikkben a PowerShell-parancsok használatával.

### <a name="what-will-change-when-i-enroll"></a>Ha regisztrálom változások?

A szempontjából előzetes, akkor átjárókat telepíthetnek, a zóna redundanciával. Ez azt jelenti, hogy az átjáró összes példányát Azure rendelkezésre állási zónák lesz alkalmazva, és minden egyes rendelkezésre állási zóna másik hiba és a frissítési tartományt. Így az átjárók megbízhatóbb, elérhető, és rugalmas zóna sikertelen.

### <a name="what-regions-are-available-for-the-preview"></a>Milyen régiók az előzetes kiadásban érhetők el?

Zónaredundáns és zonal átjárók éles vagy az Azure nyilvános régiókban érhetők el.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>I alapján számlázzuk ebben az előzetes verzióban való részvételre vonatkozó?

Nem a számlázás történik az átjárók előzetes. Van azonban nincs csatlakoztatva a telepítés SLA-t. Azt is nagyon várjuk visszajelzéseit.

> [!NOTE]
> Az ExpressRoute-átjáró, az átjáró nincs számlázva/számítjuk fel. Azonban a kapcsolatcsoport magát (nem az átjáró) lesz terhelve.

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>Milyen régiók a ismételje meg ezt a érhetők el?

USA középső RÉGIÓJA és Franciaország központi régiók (Azure-régiók rendelkezésre állási zónák általánosan elérhető rendelkező) a nyilvános előzetes verzió érhető el. Következő lépésként azt fogja elérhetővé Zónaredundáns átjáró Önnek más nyilvános Azure-régiókban.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Módosíthatja a meglévő virtuális hálózati átjárók zónaredundáns vagy zonal átjárók?

A meglévő virtuális hálózati átjárók zónaredundáns vagy zonal átjárók áttelepítése jelenleg nem támogatott. Is, azonban a meglévő átjáró törölje és hozza létre újból a zónaredundáns vagy zonal átjárót.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Telepítheti a VPN és a Express Route átjáró ugyanazon virtuális hálózatban lévő?

Az azonos virtuális hálózatban lévő VPN, mind az Express Route-átjárók együttes meglétét a nyilvános előzetes esetén támogatott. Azonban vegye figyelembe az alábbi követelményeket és korlátozásokat:

* Tartalék /27 egy IP-címtartományt az átjáró-alhálózat.
* Zóna-redundáns/zonal Express Route-átjárókat is csak üzemel zóna-redundáns/zonal VPN-átjárók.
* A zóna-redundáns/zonal Expressroute-átjáró üzembe helyezéséhez, a zóna-redundáns/zonal VPN-átjáró telepítése előtt.
* Egy zóna-redundáns/zonal Expressroute-átjáró csatlakoztatható, legfeljebb 4 kapcsolatok.

## <a name="next-steps"></a>További lépések

Köszönjük a visszajelzését lenne. E-mail küldése aznetworkgateways@microsoft.com jelentse az esetleges problémákat, vagy visszajelzést (pozitív vagy negatív) a zónaredundáns és zonal VPN- és az Express Route átjárók. Vegye fel a "["] vállalatnevet tárgyát. Az előfizetés-azonosító is tartalmazza, ha a jelentik a problémát.