---
title: "VPN-átjáró beállítások Azure verem |} Microsoft Docs"
description: "Ismerje meg a VPN-átjárók használata Azure verem beállításait."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: ed4a84965c37f66bbc7734f6043ad6f8f1666c1f
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>VPN-átjáró konfigurációs beállítások Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

VPN-átjáró olyan virtuális hálózati átjáró által a titkosított forgalmat a virtuális hálózat Azure verem és a távoli VPN-átjáró között. A távoli VPN-átjáró Azure, az adatközpontban található eszköz vagy egy másik hely eszköz lehet.  Ha a két végpontok közötti hálózati kapcsolatot, létrehozhat egy biztonságos webhelyek (közötti S2S) VPN-kapcsolat a két hálózat között.

VPN gateway-kapcsolattal konfigurálható beállításokat tartalmaz, amelyek mindegyike több erőforrás konfigurációjának támaszkodik. Ebben a cikkben a szakaszok tárgyalják az erőforrások és a Resource Manager üzembe helyezési modellel létrehozott virtuális hálózat VPN-átjáró szoftverközponthoz kapcsolódó beállításokat. Minden kapcsolat megoldás megtalálhatja leírásokat és topológiai diagramot [kapcsolatos VPN-átjáró Azure verem](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>VPN-átjáró beállítások

### <a name="gateway-types"></a>Átjárótípusok
Minden Azure verem virtuális hálózat támogatja egyetlen virtuális hálózati átjáró, amely a típusúnak kell lennie **Vpn**.  Ez nem azonos Azure, amely további típusokat támogatja.  

Ha a virtuális hálózati átjáró hoz létre, győződjön meg arról, hogy helyesek-e az átjáró típusa a konfigurációhoz. A VPN-átjáró által igényelt a `-GatewayType Vpn`.

Példa:
```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Gateway termékváltozatok
Egy virtuális hálózati átjáró létrehozásakor meg kell adni a használni kívánt termékváltozatot. Válassza ki a számítási feladatok, a teljesítmény, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatokat.

>[!NOTE]
> A klasszikus virtuális hálózatok továbbra is a régi termékváltozatokat használják. További információkat az átjárók új termékváltozatairól [a virtuális hálózati átjárók termékváltozatainak használatát bemutató (régi)](/azure/vpn-gateway/vpn-gateway-about-skus-legacy) cikkben talál.

Az Azure verem kínál az alábbi VPN gateway SKU:

|   | VPN Gateway teljesítménye |VPN-átjáró maximális IPsec-alagutak |
|-------|-------|-------|
|**Alapszintű Termékváltozat**  | 100 Mbps  | 10    |
|**Standard Termékváltozat**           | 100 Mbps  | 10    |
|**Nagy teljesítményű Termékváltozat** | 200 Mbps    | 30    |

### <a name="resizing-gateway-skus"></a>Átjáró-termékváltozat átméretezése
Azure verem nem támogatja a méretezze át a támogatott régi termékváltozatok közötti SKU.

Hasonlóképpen Azure verem nem támogatja egy átméretezés egy támogatott örökölt termékváltozatok (Basic, Standard és HighPerformance), az Azure (VpnGw1 VpnGw2 és VpnGw3) támogatja az újabb termékváltozatok a.

### <a name="configure-the-gateway-sku"></a>Az átjáró-Termékváltozat konfigurálása
#### <a name="azure-stack-portal"></a>Verem Azure portálon
Ha a verem Azure portál használatával hozzon létre egy erőforrás-kezelő virtuális hálózati átjáró, válassza a legördülő lista használatával az átjáró-Termékváltozat. A beállítások, lehetősége lesz az átjáró típusa és a választott VPN-típus felel meg.

#### <a name="powershell"></a>PowerShell
A következő PowerShell-példa - GatewaySku VpnGw1 határozza meg.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```
### <a name="connection-types"></a>Kapcsolattípusok
A Resource Manager üzembe helyezési modellel minden egyes konfiguráció szükséges egy adott virtuális hálózati átjáró kapcsolattípus. Az elérhető erőforrás-kezelő PowerShell - ConnectionType értékei:

- IPsec

A következő PowerShell-példa S2S kapcsolatot hoz létre, amely a kapcsolat típusa IPSec protokollt igényel.  

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN-típusok
A virtuális hálózati átjáró VPN-átjáró konfigurációt létrehozásakor meg kell adnia egy VPN-típus. A VPN-típus az Ön által a létrehozni kívánt kapcsolat topológia függ.  A VPN-típus az Ön által használt hardver is függ. S2S-konfigurációk esetén van szükség, a VPN-eszközön. VPN-eszközök csak egy meghatározott VPN-típus támogatja.

> [!IMPORTANT]  
> Ilyenkor Azure verem csak az útvonal-alapú VPN-típus támogatja.  Ha az eszköz támogatja a házirend alapú VPN-hálózatokhoz csak, majd az Azure oszlopból az eszközök kapcsolatok nem támogatottak.

- **PolicyBased**: *(az Azure-ban, de nem Azure verem által támogatott)* házirendalapú VPN titkosítják és irányítják a csomagokat közötti címelőtag-kombinációkkal konfigurált IPsec-házirendek alapján IPsec-alagutakon keresztül a helyszíni hálózat és az Azure verem virtuális hálózatot. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-eszköz konfigurációjában.

- **RouteBased**: RouteBased VPN-EK "útvonalakat" használja az az IP-továbbítási vagy útvonalválasztási táblán, hogy a csomagokat a megfelelő alagútkapcsolatokhoz irányítsák a. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. A házirend (vagy forgalomválasztója) VPN RouteBased konfigurált bármely elem közöttiként (vagy helyettesítő karakterek). Egy RouteBased VPN-típus értéke RouteBased.

A következő PowerShell-példa – VpnType RouteBased határozza meg. Egy átjáró létrehozásakor biztosítania kell, hogy -VpnType megfeleljen a konfigurációnak.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```
### <a name="gateway-requirements"></a>Az átjáróra vonatkozó követelmények
A következő táblázat a VPN-átjáró rendszerkövetelményeit sorolja fel.

| |PolicyBased alapszintű VPN Gateway | RouteBased alapszintű VPN Gateway | RouteBased Standard VPN Gateway | RouteBased nagy teljesítményű VPN Gateway|
|--|--|--|--|--|
| **Pont-pont kapcsolat (S2S)** | Nem támogatott | RouteBased VPN-konfiguráció | RouteBased VPN-konfiguráció | RouteBased VPN-konfiguráció |
| **Hitelesítési módszer**  | Nem támogatott | Előre megosztott kulcs S2S  | Előre megosztott kulcs S2S  | Előre megosztott kulcs S2S  |   
| **S2S-kapcsolatok maximális száma**  | Nem támogatott | 10 | 10| 30|
|**Aktív útválasztás-támogatás (BGP)** | Nem támogatott | Nem támogatott | Támogatott | Támogatott |

### <a name="gateway-subnet"></a>Átjáró alhálózata
VPN-átjáró létrehozása előtt létre kell hoznia egy átjáró-alhálózatot. Az átjáró alhálózatának az IP-címek, amelyek a virtuális hálózati átjáró virtuális gépeket és szolgáltatásokat tartalmazza. A virtuális hálózati átjáró létrehozásakor átjáró virtuális gépek az átjáró alhálózatának telepítve és konfigurálva a VPN-átjáró szükséges beállításokat. Az átjáró alhálózatának bármely más (például további virtuális gépek) nem telepíti. Az átjáró alhálózatának a "GatewaySubnet" nevű kell megfelelően működjön. Az átjáró alhálózatának elnevezése "GatewaySubnet" lehetővé teszi, hogy tudja, hogy ez az alhálózat, a virtuális hálózati átjáró virtuális gépek és szolgáltatások telepítése Azure veremben.

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró alhálózatának IP-címek az átjáró virtuális gépek és az átjáró szolgáltatások foglal le. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. Nézze meg az utasításokat, amelyet szeretne létrehozni, és győződjön meg arról, hogy az átjáró alhálózatának szeretne létrehozni a konfiguráció megfelel ezeknek a követelményeknek. Emellett érdemes lehet ellenőrizze, hogy az átjáró-alhálózatot tartalmaz elég IP-cím lehetséges jövőbeli további konfiguráció alkalmazásához. Létrehozhat egy átjáró-alhálózat mérete /29 legyen, de javasolt, hogy hozzon létre egy átjáró-alhálózatot /28 vagy nagyobb (/ 28, /27, /26 stb.). Így ha a jövőben a Funkciók hozzáadása nincs szakadjon meg az átjáró el, majd törölje és hozza létre újra az átjáró alhálózatának további IP-címek lehetővé.

A következő erőforrás-kezelő PowerShell-példa bemutatja egy GatewaySubnet nevű átjáró-alhálózatot. A CIDR-jelöléssel Meghatározza, hogy egy /27 lehetővé teszi, hogy elegendő IP-címek a jelenleg létező legtöbb konfiguráció látható.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Amikor átjáró-alhálózatokkal dolgozik, kerülje a hálózati biztonsági csoportok (NSG) társítását az átjáró-alhálózathoz. A hálózati biztonsági csoportok és az alhálózat társítása esetén előfordulhat, hogy a VPN Gateway nem fog a várt módon működni. Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [Mi az a hálózati biztonsági csoport?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Helyi hálózati átjárók
A VPN-átjáró konfigurálása az Azure-ban létrehozásakor a helyi hálózati átjáró gyakran jelöli a helyszíni hely. Azure-készletben a távoli VPN-eszköz, amely az Azure-verem kívül jelöli.  Ennek oka lehet az adatközpontban, a távoli adatközpontban, illetve az Azure VPN-átjáró VPN-eszközhöz.

Nevezze el a helyi hálózati átjáró, a VPN-eszköz nyilvános IP-címét, és adja meg a címelőtagokat a helyszíni hely található. Azure ellenőrzi, hogy a hálózati forgalom cél címelőtagokat, a konfiguráció a helyi hálózati átjáró megadott olvas, és ennek megfelelően irányítja a csomagokat.

A következő PowerShell-példa létrehoz egy új helyi hálózati átjáró:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```
Néha módosítania kell a helyi hálózati átjáró beállításainak. Például ha fel vagy módosít a címtartományt, vagy ha megváltozik-e a VPN-eszköz IP-címét. Lásd: [PowerShell használatával a helyi hálózati átjáró beállításainak módosítása](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>IPsec/IKE-paraméterek
Azure-készletben VPN-kapcsolat beállításakor kell konfigurálni a kapcsolat mindkét végén.  Azure verem és a hardveres eszköz, például kapcsolót vagy útválasztót VPN-átjáróként működő közötti VPN-kapcsolat konfigurálása, a eszköz kérheti a további beállításokat.

Azure, amely támogatja több ajánlatok egy kezdeményező és a válaszoló is, eltérően Azure verem csak egy ajánlat támogatja.

###  <a name="ike-phase-1-main-mode-parameters"></a>Az IKE 1. fázis (Elsődleges mód) paraméterei
| Tulajdonság              | Érték|
|-|-|
| IKE verziószám           | IKEv2 |
|Diffie-Hellman Group   | 2. csoport (1024 bites) |
| Hitelesítési módszer | Előre megosztott kulcs |
|Titkosító és kivonatoló algoritmus | AES256, SHA256 |
|SA élettartama (Idő)     | 28 800 másodperc|

### <a name="ike-phase-2-quick-mode-parameters"></a>Az IKE 2. fázis (Gyors mód) paraméterei
| Tulajdonság| Érték|
|-|-|
|IKE verziószám |IKEv2 |
|Titkosítás és kivonatoló algoritmusokat (titkosítás)     | GCMAES256|
|Titkosítás és kivonatoló algoritmusokat (hitelesítés) | GCMAES256|
|SA élettartama (Idő)  | 3 600 másodperc |
|SA élettartama (bájt) | 819,200       |
|Sérülés utáni titkosságvédelem (PFS) |PFS2048 |
|Kapcsolat megszakadásának észlelése | Támogatott|  
