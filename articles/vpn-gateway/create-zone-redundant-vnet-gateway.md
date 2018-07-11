---
title: A zónaredundáns virtuális hálózati átjáró létrehozása az Azure-beli rendelkezésre állási zónák – előzetes verzió |} A Microsoft Docs
description: A rendelkezésre állási zónák – előzetes verzió a VPN Gateway és ExpressRoute-átjárók üzembe helyezése.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/09/2018
ms.author: cherylmc
ms.openlocfilehash: fa349555a5effd41ca519cbd5a29005203d79543
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952555"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>A zónaredundáns virtuális hálózati átjáró létrehozása az Azure-beli rendelkezésre állási zónák – előzetes verzió

Telepítheti a VPN és ExpressRoute-átjárókra [Azure-beli rendelkezésre állási zónák](../availability-zones/az-overview.md). Ekkor a rugalmasság, a méretezhetőség és a magasabb rendelkezésre állás virtuális hálózati átjárók. Egy adott régión belül átjárók telepítése átjárók az Azure-beli rendelkezésre állási zónák fizikailag és logikailag elkülöníti a során a helyszíni hálózati kapcsolatok védelme az Azure-bA a zónaszintű hibák.

A zónaszintű és zónaredundáns gatewayek alapvető teljesítménybeli javításokat normál virtuális hálózati átjárók keresztül. Ezenkívül zónaredundáns vagy zónaszintű virtuális hálózati átjáró létrehozása a gyorsabb, mint a többi átjáró létrehozásához. Ahelyett, hogy 45 percig tart, hozzon létre alkalommal körülbelül 15 percenként egy ExpressRoute-átjárót, és a VPN-átjáró 19 percet vesz igénybe.

### <a name="zrgw"></a>Átjárók zónaredundáns

A virtuális hálózati átjárók a rendelkezésre állási zónák között automatikusan üzembe helyezéséhez, használhatja a zónaredundáns virtuális hálózati átjárók. Az átjárók zónaredundáns használja ki a 99,99 %-os SLA-t, a végleges verzió az alapvető fontosságú, skálázható az Azure-szolgáltatásokhoz való hozzáférést.

<br>
<br>

![zóna-redunant átjárók ábrája](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>A zónaszintű átjárók

Egy adott zónához az átjáró üzembe helyezéséhez használhatja a zónaszintű átjárók. Zónaszintű az átjáró telepítésekor az átjáró mindkét példányát azonos rendelkezésre állási zónában üzemelnek.

<br>
<br>

![a zónaszintű átjárók ábrája](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Átjáró-termékváltozatok

Átjárók zónaredundáns és zónaszintű kell használnia az új átjáró SKU-k. Egyszer, [léptetni az előzetes verzióban érhető el](#enroll), látni fogja az új virtuális hálózati átjárók Termékváltozatainak az összes, Azure AZ-régiót. Termékváltozatokban hasonlóak a megfelelő termékváltozata az ExpressRoute és VPN-átjáró, azzal a különbséggel, hogy ezek átjárók zónaredundáns és zónaszintű jellemzőek.

Az új átjáró termékváltozatok a következők:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Nyilvános IP-cím termékváltozatok

Átjárók zónaredundáns és a zónaszintű átjárókat egyaránt támaszkodjon az Azure nyilvános IP-erőforrásból *Standard* Termékváltozat. Az Azure nyilvános IP-erőforrásból konfigurációját határozza meg, hogy az átjáró üzembe helyezett zónaredundáns, vagy a zónaszintű. Ha létrehoz egy nyilvános IP-erőforráshoz az egy *alapszintű* SKU, az átjáró nem fog bármilyen zone redudancy, és az átjáró-erőforrásokat regionális lesz.

### <a name="pipzrg"></a>Átjárók zónaredundáns

Amikor hoz létre egy nyilvános IP cím használatával a **Standard** nyilvános IP SKU zóna megadása nélkül, a viselkedés eltér attól függően, hogy az átjáró VPN-átjáró vagy ExpressRoute-átjárónak. 

* A VPN-átjárót az két átjárópéldányok lesz telepítve, ezek három zónát zóna redundanciájának kívül bármely 2. 
* Egy ExpressRoute-átjárót, az is lehet több mint két olyan példányt, mert az átjáró is kiterjedhet a három zónák között.

### <a name="pipzg"></a>A zónaszintű átjárók

Amikor hoz létre egy nyilvános IP cím használatával a **Standard** nyilvános IP-Termékváltozat és adja meg a zóna (1, 2 vagy 3), az átjárópéldányok lesz telepítve, ugyanabban a zónában.

### <a name="piprg"></a>Regionális átjárók

Amikor hoz létre egy nyilvános IP cím használatával a **alapszintű** nyilvános IP-SKU, az átjáró regionális átjáróként üzemel, és minden zóna-redundanciát építve az átjáró nem rendelkezik.

## <a name="before-you-begin"></a>Előkészületek

PowerShell-lel helyben telepítve a számítógépen, vagy az Azure Cloud Shellt használhatja. Ha helyi telepítése és használata a PowerShell, a funkcióhoz a PowerShell-modul legújabb verzióját.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Ha a PowerShell segítségével helyileg

Ha a Powershellt használ helyileg a számítógépen, nem pedig a Cloud Shellt használja 6.1.1 PowerShell-modult kell telepítenie vagy újabb verziója. PowerShell telepített verziójának ellenőrzéséhez használja a következő parancsot:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1. Regisztráljon az előzetes verzió

Egy zónaredundáns vagy zónaszintű átjáró konfigurálása előtt először önálló regisztrálnia kell az előfizetés az előzetes verzióban érhető el. Amint az előfizetés kiépítését követően megkezdjük az új átjáró SKU-k az összes, Azure AZ-régiót. 

Győződjön meg arról, hogy az Azure-fiókjával bejelentkezett, és az előfizetést, amely ebben az előzetes verzióban az engedélyezési listára szeretné használják. Az alábbi példát követve regisztrálása:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

A következő parancs használatával győződjön meg arról, hogy a "AllowVMSSVirtualNetworkGateway" funkció regisztrálva van-e az előfizetéséhez:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Az eredmény ebben a példában hasonlóan néz ki:

![kiosztott](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. A változók deklarálása

A példa lépései használt értékek az alábbiak. Emellett néhány példája a deklarált változók belül a lépéseket használja. Ha ezeket a lépéseket a saját környezetében használ, mindenképpen ezeket az értékeket a saját értékeire cserélni. Adja meg a helyet, győződjön meg arról, hogy a megadott régióban támogatott. További információkért lásd: a [– gyakori kérdések](#faq).

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

Az átjáróalhálózat tartalmazza a fenntartott IP-címek, amelyek a virtuális hálózati átjáró-szolgáltatások használják. Az alábbi példák segítségével adja hozzá, és állítsa be az átjáró-alhálózat:

Adja hozzá az átjáró alhálózatát.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Állítsa be a virtuális hálózati átjáró alhálózati konfigurációját.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5. Nyilvános IP-cím kérése
 
Ebben a lépésben válassza ki a létrehozni kívánt átjáróhoz érvényes utasításokat. Az átjárók telepítésére zónák a kijelölt a zónák a nyilvános IP-címhez megadott függ.

### <a name="ipzoneredundant"></a>Az átjárók zónaredundáns

Kérjen egy nyilvános IP-címet egy **Standard** Termékváltozat nyilvános IP-címre, és ne adjon meg minden zónában. Ebben az esetben a Standard nyilvános IP-cím létrehozott zónaredundáns nyilvános IP-cím lesz.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>A zónaszintű átjárók esetében

Kérjen egy nyilvános IP-címet egy **Standard** Termékváltozat nyilvános IP-címre. Adja meg a zóna (1, 2 vagy 3). Ebben a zónában lesz telepítve, az összes átjárópéldányához.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Regionális átjárók esetében

Kérjen egy nyilvános IP-címet egy **alapszintű** Termékváltozat nyilvános IP-címre. Ebben az esetben az átjáró regionális átjáróként üzemel, és minden zóna-redundanciát építve az átjáró nem rendelkezik. Az átjárópéldányok rendre minden zónában létrejönnek.

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

A virtuális hálózati átjáró létrehozásához.

### <a name="for-expressroute"></a>Az expressroute-hoz

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>A VPN Gateway

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Visszajelzés mikéntjére

Örömmel visszajelzését. E-mail küldése aznetworkgateways@microsoft.com jelentse a problémákat, vagy visszajelzést (pozitív vagy negatív) a zónaredundáns és zónaszintű VPN és Expressroute-átjárók esetében. A cég nevét, a "[]" Felvétel a tárgysorban. Az előfizetés-Azonosítóját is tartalmazhatnak, ha problémát jelent.

## <a name="faq"></a>GYIK

### <a name="how-do-i-sign-up-for-the-preview"></a>Hogyan tudok regisztrálni az előzetes verzióra?

Is [önbeiktatással](#enroll) ebben a cikkben a PowerShell-parancsok használatával.

### <a name="what-will-change-when-i-enroll"></a>Mi változik, ha regisztrálom az?

A szempontból az előzetes időszakban is telepítheti az átjárók zóna redundanciával. Ez azt jelenti, hogy az átjárók az összes példányát telepíti, az Azure rendelkezésre állási zónák között, és minden egyes rendelkezésre állási zónában egy másik tartalék és frissítési tartományban. Ez lehetővé teszi az átjárók megbízható, elérhető és rugalmas zóna hibákkal szemben.

### <a name="can-i-use-the-azure-portal"></a>Használható az Azure Portalon?

Igen, használhatja az Azure Portalon az előzetes verzióra. Azonban továbbra is szeretné regisztrálni a PowerShell használatával, vagy nem tudja használni a portál előzetes verzió ideje alatt.

### <a name="what-regions-are-available-for-the-preview"></a>Mely régiók érhetők el az előzetes verzióra?

Átjárók zónaredundáns és zónaszintű nyilvános éles vagy az Azure-régiókban érhetők el.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>Számítják fel az előzetes verzióhoz való részvételre vonatkozó?

Akkor nem kell fizetnie az átjárók előzetes verzió ideje alatt. Azonban nem nincs csatlakoztatva a telepítés, nem biztosítunk szolgáltatói szerződést. Örömmel Várjuk visszajelzését nagyon hasznos.

> [!NOTE]
> Az ExpressRoute-átjáró az átjáró nem számlázzuk/díjat számítunk fel. Azonban számítjuk fel a kapcsolatcsoport magát (az átjáró nem).

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>Mely régiók érhetők el szeretné kipróbálni ezt a számomra?

A nyilvános előzetes verzióban érhető el az USA középső Régiójában és közép-Franciaország régió (Azure-régiók, amelyek a rendelkezésre állási zónák általánosan elérhető). Továbbítja, hogy elérhetővé teszi a Zónaredundáns átjárók Önnek más nyilvános Azure-régióban.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Módosíthatom a meglévő virtuális hálózati átjárók zónaredundáns vagy zónaszintű átjárók?

A meglévő virtuális hálózati átjárók zónaredundáns vagy zónaszintű átjárók migrálása jelenleg nem támogatott. Azonban, törölje a meglévő átjárót és hozhat újra létre zónaredundáns vagy zónaszintű átjáró.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Telepítheti a VPN- és Express Route átjárók azonos virtuális hálózatba?

Az azonos virtuális hálózatba VPN- és Express Route-átjárók együttes jelenléte támogatott a nyilvános előzetes verzió ideje alatt. Ugyanakkor vegye figyelembe a következő követelményei és korlátozásai:

* / 27-eset egy fenntartott IP-címtartományt az átjáró-alhálózat.
* Zóna-redundáns/zónaszintű Express Route-átjárókkal is csak elérhetőek az zóna-redundáns/zónaszintű VPN-átjárókkal.
* A zóna-redundáns/zónaszintű Express Route-átjáró telepítéséhez a zóna-redundáns/zónaszintű VPN-átjáró telepítése előtt.
* Egy zóna-redundáns/zónaszintű Express Route-átjáró képes csatlakozni, legfeljebb 4 Kapcsolatcsoportok.

## <a name="next-steps"></a>További lépések

Örömmel visszajelzését. E-mail küldése aznetworkgateways@microsoft.com jelentse a problémákat, vagy visszajelzést (pozitív vagy negatív) a zónaredundáns és zónaszintű VPN és Expressroute-átjárók esetében. A cég nevét, a "[]" Felvétel a tárgysorban. Az előfizetés-Azonosítóját is tartalmazhatnak, ha problémát jelent.
