---
title: 'Párhuzamosan fennálló ExpressRoute és Site-to-Site VPN-kapcsolatok konfigurálása: PowerShell: Azure |} A Microsoft Docs'
description: Ez a cikk bemutatja az ExpressRoute- és egy helyek közötti VPN-kapcsolat, amelyek párhuzamosan használhatók a PowerShell használatával a Resource Manager-modell konfigurálását
services: expressroute
author: charwen
manager: rossort
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/07/2018
ms.author: charwen
ms.openlocfilehash: c267e5002fbd603e4bb749550c19e8d022ce4d54
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162342"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>PowerShell-lel ExpressRoute- és helyek közötti egyidejű kapcsolatok konfigurálása
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-howto-coexist-classic.md)
> 
> 

Az egyidejű helyek közötti VPN- és ExpressRoute-kapcsolatok konfigurálása több előnnyel jár:

* Konfigurálhat helyek közötti VPN-t biztonságos feladatátvételi útvonalként az ExpressRoute számára. 
* Azt is megteheti, hogy helyek közötti VPN-t használ olyan helyekhez való csatlakozáshoz, amelyek nem az ExpressRoute-on keresztül kapcsolódnak. 

Ebben a cikkben ismertetjük mindkét forgatókönyv konfigurálásának lépéseit. Ez a cikk a Resource Manager-alapú üzemi modellre vonatkozik, és a PowerShellt használja. Ezek a forgatókönyvek az Azure Portal használatával is konfigurálhatja, bár dokumentáció még nem érhető el.

>[!NOTE]
>Ha helyek közötti VPN-t szeretne létrehozni egy ExpressRoute-kapcsolatcsoport között, tekintse meg [ezt a cikket](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Korlátok és korlátozások
* **A tranzit útválasztás nem támogatott.** Nem hajthat végre útválasztást (az Azure-on keresztül) a helyek közötti VPN használatával csatlakoztatott helyi hálózat és az ExpressRoute használatával csatlakoztatott helyi hálózat között.
* **A Basic SKU-átjáró nem támogatott.** Nem Basic SKU-átjárót kell használnia [ExpressRoute-](expressroute-about-virtual-network-gateways.md) és [VPN-átjáróként](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Kizárólag az útvonalalapú VPN-átjáró támogatott.** Útvonalalapú [VPN-átjárót](../vpn-gateway/vpn-gateway-about-vpngateways.md) kell használnia.
* **A VPN-átjáróhoz statikus útvonalat kell konfigurálni.** Ha a helyi hálózat az ExpressRoute-hoz és a helyek közötti VPN-hez is csatlakozik, konfigurálnia kell egy statikus útvonalat a helyi hálózaton a helyek közötti VPN-kapcsolat a nyilvános internetre történő átirányításához.

## <a name="configuration-designs"></a>Konfigurációs tervek
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Helyek közötti VPN konfigurálása feladatátvételi útvonalként az ExpressRoute számára
Konfigurálhat egy helyek közötti VPN-kapcsolatot tartalékként az ExpressRoute számára. Ez a kapcsolat csak az Azure privát társviszony-létesítési útvonalhoz társított virtuális hálózatokra vonatkozik. Az Azure Microsoft társviszony-létesítésekhez nem létezik VPN-alapú feladatátvételi megoldás. Minden esetben az ExpressRoute-kapcsolatcsoport az elsődleges kapcsolat. Az adatok csak akkor lesznek a helyek közötti VPN-útvonalon továbbítva, ha az ExpressRoute-kapcsolatcsoport meghibásodik. Az aszimmetrikus útválasztás elkerüléséhez a helyi hálózati konfigurációnak szintén az ExpressRoute-kapcsolatcsoportot kell előnyben részesítenie a helyek közötti VPN helyett. Az ExpressRoute elérési útjának előnyben részesítéséhez beállíthat magasabb helyi prioritást az ExpressRoute által fogadott útvonalakhoz. 

> [!NOTE]
> Bár a rendszer az ExpressRoute-kapcsolatcsoportot részesíti előnyben a helyek közötti VPN helyett, ha az útvonalak megegyeznek, az Azure a leghosszabb előtag-megfeleltetéssel választja ki a célcsomag útvonalát.
> 
> 

![Egyidejű jelenlét](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Helyek közötti VPN konfigurálása az ExpressRoute használatával nem csatlakozó helyekhez
A hálózatát konfigurálhatja úgy is, hogy egyes helyek közvetlenül az Azure-hoz kapcsolódnak helyek közötti VPN-en keresztül, míg más helyek az ExpressRoute használatával kapcsolódnak. 

![Egyidejű jelenlét](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Virtuális hálózatot nem konfigurálhat tranzit útválasztóként.
> 
> 

## <a name="selecting-the-steps-to-use"></a>A használni kívánt lépések kiválasztása
Két különböző eljáráscsoport közül választhat. A konfigurálás választott módja attól függ, hogy rendelkezik-e meglévő virtuális hálózattal, amelyhez csatlakozni szeretne, vagy egy új virtuális hálózatot szeretne létrehozni.

* Nem rendelkezem VNettel, és létre kell hoznom egyet.
  
    Ha még nem rendelkezik virtuális hálózattal, ez az eljárás lépésről lépésre végigvezeti az új virtuális hálózat létrehozásának folyamatán a Resource Manager-alapú üzemi modellnek megfelelően, valamint az új ExpressRoute- és helyek közötti VPN-kapcsolatok létrehozásának folyamatán. Virtuális hálózat konfigurálásához kövesse az [Új virtuális hálózat és egyidejű kapcsolatok létrehozása](#new) szakaszban foglalt lépéseket.
* Már rendelkezem egy Resource Manager-alapú üzemi modell szerinti VNettel.
  
    Előfordulhat, hogy már rendelkezik üzemelő virtuális hálózattal egy létező helyek közötti VPN- vagy ExpressRoute-kapcsolattal. Ebben a forgatókönyvben, ha az átjáró alhálózati maszkja /28 vagy kisebb (/28, /29 stb.), akkor törölnie kell a meglévő átjárót. Az [Egyidejű kapcsolatok konfigurálása meglévő VNet számára](#add) szakasz végigvezeti az átjáró törlésének, majd az új ExpressRoute- és helyek között VPN-kapcsolatok létrehozásának folyamatán.
  
    Ha törli és újra létrehozza az átjárót, akkor a létesítmények közötti kapcsolatoknál állásidő fog jelentkezni. Azonban, ha megfelelően vannak konfigurálva, a virtuális gépek és a szolgáltatások továbbra is képesek lesznek kommunikálni a terheléselosztón keresztül az átjáró konfigurálása közben.

## <a name="new"></a>Új virtuális hálózat és egyidejű kapcsolatok létrehozása
Az eljárás a VNetek, valamint az egyidejűleg jelenlévő helyek közötti és ExpressRoute-kapcsolatok létrehozásának módját ismerteti.

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióit. A parancsmagok telepítésével kapcsolatos információkért lásd: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview). Az ehhez a konfigurációhoz használt parancsmagok eltérőek lehetnek az Ön által már ismertektől. Ügyeljen arra, hogy az ebben az útmutatóban meghatározott parancsmagokat használja.

2. Jelentkezzen be a fiókjába, és állítsa be a környezetet.

  ```powershell
  Connect-AzureRmAccount
  Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
  $location = "Central US"
  $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
  $VNetASN = 65515
  ```
3. Hozzon létre egy virtuális hálózatot az átjáró-alhálózattal együtt. A virtuális hálózatok létrehozásával kapcsolatos további információkért lásd a [virtuális hálózatok létrehozásával](../virtual-network/manage-virtual-network.md#create-a-virtual-network) foglalkozó témakört. Az alhálózatok létrehozásával kapcsolatos további információkért lásd az [alhálózatok létrehozásával](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) foglalkozó témakört.
   
   > [!IMPORTANT]
   > Az átjáró-alhálózat /27 vagy egy rövidebb előtag kell legyen (például /26 vagy /25).
   > 
   > 
   
    Hozzon létre egy új VNetet.

  ```powershell
  $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
  ```
   
    Adjon hozzá alhálózatokat.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    Mentse a VNet konfigurációját.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
4. <a name="vpngw"></a>Ezután hozza létre a webhelyek közötti VPN-átjárót. A VPN-átjáró konfigurálásával kapcsolatos további információkért lásd: [VNet létrehozása helyek közötti kapcsolattal](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). A GatewaySku csak *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* és *Nagy teljesítményű* VPN-átjárók esetén támogatott. Az alapszintű termékváltozat nem támogatja az ExpressRoute-VPN-átjárók egyszerre konfigurált beállításait. A VpnType paramétert *RouteBased* értékre kell állítania.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
  ```
   
    Az Azure-os VPN-átjáró támogatja a BGP útválasztási protokollt. A virtuális hálózat ASN (AS-szám) értékét úgy határozhatja meg, hogy az alábbi parancshoz hozzáadja az -Asn kapcsolót. Ha ezt a paramétert nem határozza meg, az alapértelmezés szerinti AS-szám a 65515 lesz.

  ```powershell
  $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
  ```
   
    A következőkben megtalálhatja a BGP-társviszony-létesítés IP-címét és AS-számát, amelyeket az Azure használ a VPN-átjáróhoz: $azureVpn.BgpSettings.BgpPeeringAddress és $azureVpn.BgpSettings.Asn. További információk: [A BGP konfigurálása](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) az Azure-alapú VPN-átjáróhoz.
5. Hozzon létre egy helyi VPN-átjáró entitást. Ez a parancs nem konfigurálja a helyszíni VPN-átjárót. Ehelyett a helyi átjáró beállításai, például a nyilvános IP-cím és a helyszíni címtér megadására szolgál, hogy az Azure VPN-átjáró kapcsolódhasson hozzá.
   
    Ha a helyi VPN-eszköz csak a statikus útválasztást támogatja, a következő módon konfigurálhatja a statikus útvonalakat:

  ```powershell
  $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
  ```
   
    Ha a helyi VPN-eszköz támogatja a BGP-t, és Ön szeretné engedélyezni a dinamikus útválasztást, ismernie kell a helyi VPN-eszköz által használt BGP-társviszony-létesítés IP-címét és AS-számát.

  ```powershell
  $localVPNPublicIP = "<Public IP>"
  $localBGPPeeringIP = "<Private IP for the BGP session>"
  $localBGPASN = "<ASN>"
  $localAddressPrefix = $localBGPPeeringIP + "/32"
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
  ```
6. Konfigurálja helyi VPN-eszközét, hogy az új Azure VPN-átjáróhoz csatlakozzon. A VPN-eszköz konfigurálásával kapcsolatos további információkért lásd: [VPN-eszköz konfigurálása](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

7. Csatlakoztassa az Azure-on a helyek közötti VPN-átjárót a helyi átjáróhoz.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
  ```
 

8. Ha egy meglévő ExpressRoute-kapcsolatcsoporthoz csatlakozik, hagyja ki a 8. és 9. lépést, és ugorjon a 10.-re. ExpressRoute-kapcsolatcsoportok konfigurálása. ExpressRoute-kapcsolatcsoportok konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md).


9. Azure-beli privát társviszony konfigurálása az ExpressRoute-kapcsolatcsoporton keresztül. Azure-beli privát társviszony ExpressRoute-kapcsolatcsoporton keresztüli konfigurálásáról további információért lásd: [társviszony létesítésének konfigurálása](expressroute-howto-routing-arm.md)

10. <a name="gw"></a>Hozzon létre egy ExpressRoute-átjárót. Az ExpressRoute-átjáró konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute-átjáró konfigurálása](expressroute-howto-add-gateway-resource-manager.md). A GatewaySKU paraméterben a *Standard*, *HighPerformance* vagy *UltraPerformance* értéket kell megadni.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
  ```
11. Csatlakoztassa az ExpressRoute-átjárót az ExpressRoute-kapcsolatcsoporthoz. A lépés végrehajtásával létrejön a kapcsolat a helyszíni hálózat és az Azure között az ExpressRoute-on keresztül. A csatolási művelettel kapcsolatos további információkért lásd: [VNetek csatolása az ExpressRoute-hoz](expressroute-howto-linkvnet-arm.md).

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
  New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
  ```

## <a name="add"></a>Egyidejű kapcsolatok konfigurálása meglévő VNet számára
Ha a virtuális hálózat egyetlen virtuális hálózati átjáróval (például helyek közötti VPN-átjáróval) rendelkezik, és egy másik, eltérő típusú átjárót (például egy ExpressRoute-átjárót) szeretne hozzáadni, ellenőrizze az átjáró-alhálózat méretét. Ha az átjáró-alhálózat /27 vagy nagyobb, kihagyhatja az alábbi lépéseket, és az előző szakaszban ismertetett lépéseket követve hozzáadhat egy helyek közötti VPN-átjárót vagy egy ExpressRoute-átjárót. Ha az átjáró-alhálózat /28 vagy /29, először törölnie kell a virtuális hálózati átjárót, és növelnie kell az átjáró-alhálózat méretét. A jelen szakaszban ismertetett lépések bemutatják, mindez hogyan valósítható meg.

1. Az Azure PowerShell-parancsmagok legújabb verzióit kell telepítenie. A parancsmagok telepítésével kapcsolatos további információkért lásd: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview). Az ehhez a konfigurációhoz használt parancsmagok eltérőek lehetnek az Ön által már ismertektől. Ügyeljen arra, hogy az ebben az útmutatóban meghatározott parancsmagokat használja. 
2. Törölje a meglévő ExpressRoute- vagy helyek közötti VPN-átjárót.

  ```powershell 
  Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
  ```
3. Törölje az átjáró-alhálózatot.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
  ```
4. Adjon hozzá egy átjáró-alhálózatot, amely legfeljebb /27.
   
   > [!NOTE]
   > Ha már nincs elegendő IP-cím a virtuális hálózaton az átjáró-alhálózat méretének növeléséhez, további IP-címtereket kell hozzáadnia.
   > 
   > 

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    Mentse a VNet konfigurációját.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
5. Ezen a ponton egy átjáró nélküli virtuális hálózattal rendelkezik. Új átjárók létrehozásához és a kapcsolatok beállításához kövesse az előző szakasz lépéseit.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Pont-hely konfiguráció hozzáadása a VPN-átjáróhoz
Az alábbi lépések végrehajtásával pont–hely konfigurációt adhat hozzá a VPN-átjáróhoz egyidejű jelenléttel rendelkező beállításokban.

1. Adja hozzá a VPN-ügyfél címterét.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
  ```
2. Töltse fel a VPN-főtanúsítványt az Azure-ba a VPN-átjáró számára. Ebben a példában feltételezzük, hogy a főtanúsítványt a helyi gépen tárolja, ahol az alábbi PowerShell-parancsmagok futnak.

  ```powershell
  $p2sCertFullName = "RootErVpnCoexP2S.cer" 
  $p2sCertMatchName = "RootErVpnCoexP2S" 
  $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
  if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
  $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
  ```

A pont-hely VPN-ekkel kapcsolatos további információkért lásd: [Pont-hely kapcsolat konfigurálása](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>További lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
