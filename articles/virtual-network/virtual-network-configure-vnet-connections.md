---
title: Virtuális hálózati vagy VPN-kapcsolatok konfigurálása és érvényesítése
description: Részletes útmutatás a különböző Azure VPN- és virtuális hálózati telepítések konfigurálásához és érvényesítéséhez
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099055"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Virtuális hálózati vagy VPN-kapcsolatok konfigurálása és érvényesítése

Ez a forgatókönyv lépésenkénti útmutatást nyújt a különböző Azure VPN- és virtuális hálózati telepítések konfigurálásához és érvényesítéséhez. A forgatókönyvek közé tartoznak a tranzitútválasztás, a hálózat és a hálózat közötti kapcsolatok, a Border Gateway Protocol (BGP), a többhelyes kapcsolatok és a pont-hely kapcsolatok.

Az Azure VPN-átjárók rugalmasságot tesznek lehetővé szinte bármilyen csatlakoztatott virtuális hálózati topológiának az Azure-ban. Például csatlakozhat a virtuális hálózatokhoz:

- Régiók között.
- Virtuális hálózati típusok között (Azure Resource Manager versus klasszikus).
- Az Azure-on belül vagy egy helyszíni hibrid környezetben.
- Különböző előfizetésekben. 

## <a name="network-to-network-vpn-connection"></a>Hálózat-hálózati VPN-kapcsolat

A virtuális hálózat vpn-en keresztüli csatlakoztatása egy másik virtuális hálózathoz (hálózatról hálózatra) hasonló ahhoz, mintha egy virtuális hálózatot egy helyszíni helyhez csatlakoztatna. Mindkét kapcsolattípus VPN-átjárót használ a biztonságos alagút biztosítására az IPsec és az IKE segítségével. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben.

![Hálózat-hálózat kapcsolat az IPsec-vel](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Ha a virtuális hálózatok ugyanabban a régióban vannak, érdemes megfontolni a virtuális hálózati társviszony-létesítés használatával való csatlakoztatását. A virtuális hálózati társviszony-létesítés nem használ VPN-átjárót. Növeli az átviteli és csökkenti a késést. Virtuális hálózati társviszony-létesítési kapcsolat konfigurálásához válassza **a Virtuálistárs-társviszony-létesítés konfigurálása és érvényesítése**lehetőséget.

Ha a virtuális hálózatok az Azure Resource Manger telepítési modellen keresztül jöttek létre, válassza **az Erőforrás-kezelő virtuális hálózat konfigurálása és érvényesítése egy Erőforrás-kezelő virtuális hálózathoz** a VPN-kapcsolat konfigurálásához.

Ha az egyik virtuális hálózat az Azure klasszikus telepítési modellen keresztül jött létre, a másik pedig az Erőforrás-kezelőn keresztül jött létre, válassza **a Klasszikus virtuális hálózat konfigurálása és érvényesítése egy Erőforrás-kezelő virtuális hálózathoz** a VPN-kapcsolat konfigurálásához.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Virtuális hálózati társviszony-létesítés konfigurálása két virtuális hálózathoz ugyanabban a régióban

Mielőtt elkezdené megvalósítani és konfigurálni az Azure virtuális hálózati társviszony-létesítését, győződjön meg arról, hogy megfelel az alábbi előfeltételeknek:

* A virtuális társhálózatoknak ugyanabban az Azure-régióban kell lenniük.
* A társviszony-létesített virtuális hálózatoknak olyan IP-címterekkel kell rendelkezniük, amelyek nem fedik át egymást.
* A virtuális hálózati társviszony két virtuális hálózat között jön létre. Nincs származtatott tranzitív kapcsolat a társviszony-létesítések között. Ha például a VNetA társviszonyban van a VNetB-vel, és a VNetB a VNetC-vel van társviszonyban, a VNetA *nem* társviszonyba kerül a VNetC-vel.

Ha megfelel a követelményeknek, kövesse [az oktatóanyag: Virtuális hálózatok csatlakoztatása virtuális hálózati társviszony-létesítés az Azure Portal segítségével](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) hozza létre és konfigurálja a társviszony-létesítés.

A társviszony-létesítési konfiguráció ellenőrzéséhez használja a következő módszert:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a szükséges [szerepkörökkel és engedélyekkel](virtual-network-manage-peering.md#permissions)rendelkező fiókkal.
2. A portál tetején található **Keresési erőforrások** szövegét tartalmazó mezőbe írja be a virtuális **hálózatok**kifejezést. Amikor **a virtuális hálózatok** megjelennek a keresési eredmények között, jelölje ki azt.
3. A **megjelenő Virtuális hálózatok** panelen válassza ki azt a virtuális hálózatot, amelyhez társviszony-létesítést szeretne létrehozni.
4. A virtuális hálózathoz megjelenő ablaktáblán válassza a **Beállítások** szakasz **Társviszony-létesítése** lehetőséget.
5. Jelöljön ki egy társviszony-létesítést, és tekintse meg a konfigurációs eredményeket.

![A virtuális hálózati társviszony-létesítési konfiguráció ellenőrzésére szolgáló beállítások](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Az Azure PowerShell esetében futtassa a [Get-AzureRmVirtualNetworkPeering parancsot](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) a virtuális hálózati társviszony-létesítés lekérni. Például:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Erőforrás-kezelő virtuális hálózatának csatlakoztatása másik Erőforrás-kezelő virtuális hálózathoz

Az egyik Erőforrás-kezelő virtuális hálózatból közvetlenül konfigurálható a kapcsolat egy másik Erőforrás-kezelő virtuális hálózattal. Vagy konfigurálhatja a kapcsolatot az IPsec használatával.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Vpn-kapcsolat konfigurálása az Erőforrás-kezelő virtuális hálózatai között

Az Erőforrás-kezelő virtuális hálózatok közötti kapcsolat IPsec nélküli konfigurálásához olvassa el a [hálózat-hálózati VPN-átjárókapcsolat konfigurálása az Azure Portal használatával című témakört.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)

Két Erőforrás-kezelő virtuális hálózat közötti IPsec-kapcsolat konfigurálásához [Create a site-to-site connection in the Azure portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) kövesse az 1–5.

> [!Note]
> Ezek a lépések csak az azonos előfizetésben lévő virtuális hálózatok esetében működnek. Ha a virtuális hálózatok különböző előfizetések, a PowerShell használatával kell létesíteni a kapcsolatot. Lásd a [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)-re vonatkozó cikket.

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Az Erőforrás-kezelő virtuális hálózatai közötti VPN-kapcsolat ellenőrzése

![Klasszikus virtuális hálózati kapcsolat az Azure Resource Manager virtuális hálózatával](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

A VPN-kapcsolat megfelelő konfigurálásának ellenőrzéséhez kövesse az alábbi utasításokat.

> [!Note] 
> A virtuális hálózati összetevők utáni számok ezekben a lépésekben megegyeznek az előző ábrán látható számokkal.

1. Győződjön meg arról, hogy a csatlakoztatott virtuális hálózatokban nincsenek egymást átfedő címterek.
2. Ellenőrizze, hogy az Azure Resource Manager virtuális hálózat (1) címtartománya pontosan van-e definiálva a **Kapcsolatobjektum-példányban** (4).
3. Ellenőrizze, hogy az Azure Resource Manager virtuális hálózat (6) címtartománya pontosan van-e definiálva a **Kapcsolatobjektum-példányban** (3).
4. Ellenőrizze, hogy az előmegosztott kulcsok megfelelnek-e a kapcsolatobjektumoknak.
5. Ellenőrizze, hogy az Azure Resource Manager virtuális hálózati átjáró VIP (2) pontosan van-e definiálva a **Kapcsolat objektumpéldányban** (4).
6. Ellenőrizze, hogy az Azure Resource Manager virtuális hálózati átjáró VIP (5) pontosan definiálva van-e a **Kapcsolatobjektum-példányban** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Klasszikus virtuális hálózat csatlakoztatása erőforrás-kezelő virtuális hálózathoz

Létrehozhat kapcsolatot a különböző előfizetésekben és különböző régiókban lévő virtuális hálózatok között. Olyan virtuális hálózatokat is csatlakoztathat, amelyek már rendelkeznek kapcsolattal a helyszíni hálózatokkal, feltéve, hogy az átjárótípust útvonalalapúként konfigurálta.

A klasszikus virtuális hálózat és az Erőforrás-kezelő virtuális hálózat közötti kapcsolat konfigurálásához olvassa el a [Virtuális hálózatok csatlakoztatása különböző telepítési modellekből az Azure Portal használatával című témakört.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)

![Klasszikus virtuális hálózati kapcsolat az Azure Resource Manager virtuális hálózatával](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Ha ellenőrizni szeretné a konfigurációt, amikor egy klasszikus virtuális hálózatot csatlakoztat egy Azure Resource Manager virtuális hálózathoz, kövesse az alábbi utasításokat.

> [!Note] 
> A virtuális hálózati összetevők utáni számok ezekben a lépésekben megegyeznek az előző ábrán látható számokkal. 

1. Győződjön meg arról, hogy a csatlakoztatott virtuális hálózatokban nincsenek egymást átfedő címterek.
2. Ellenőrizze, hogy az Azure Resource Manager virtuális hálózat (6) címtartománya pontosan van-e definiálva a klasszikus helyi hálózati definícióban (3).
3. Ellenőrizze, hogy a klasszikus virtuális hálózat (1) címtartománya pontosan van-e definiálva az Azure Resource Manager **connection object-példányban** (4).
4. Ellenőrizze, hogy a klasszikus virtuális hálózati átjáró VIP (2) pontosan definiálva van-e az Azure Resource Manager **connection object-példányban** (4).
5. Ellenőrizze, hogy az Azure Resource Manager virtuális hálózati átjáró (5) pontosan definiálva van-e a klasszikus **helyi hálózati definíciós** példányban (3).
6. Ellenőrizze, hogy az előmegosztott kulcsok megfelelnek-e mindkét csatlakoztatott virtuális hálózaton:
   - Klasszikus virtuális hálózat: **Helyi hálózati definíció** (3)
   - Azure Resource Manager virtuális hálózat: **Kapcsolatobjektum** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Pont-hely VPN-kapcsolat létrehozása

A pont-hely *(P2S* a következő ábrán) konfiguráció lehetővé teszi, hogy biztonságos kapcsolatot hozzon létre egy adott ügyfélszámítógépről egy virtuális hálózatra. A pont-hely kapcsolatok akkor hasznosak, ha távoli helyről, például otthonról vagy konferenciáról szeretne csatlakozni a virtuális hálózathoz. Akkor is hasznosak, ha csak néhány ügyféllel kell csatlakoznia egy virtuális hálózathoz. 

A pont-hely VPN-kapcsolat az ügyfélszámítógépről a natív Windows VPN-ügyfélen keresztül indul. Az ügyfelek hitelesítése a kapcsolódáskor tanúsítványokkal történik.

![Pont–hely kapcsolat](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

A helyek közötti kapcsolatokhoz nincs szükség VPN-eszközre. A VPN-kapcsolatot a Secure Socket Tunneling Protocol (SSTP) protokollon keresztül hozzák létre. A helyek közötti kapcsolatot különböző telepítési eszközök és telepítési modellek használatával csatlakoztathatja a virtuális hálózathoz:

* [Pont-hely kapcsolat konfigurálása virtuális hálózattal az Azure Portal használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Virtuális hálózattal való pont-hely kapcsolat konfigurálása az Azure Portal használatával (klasszikus)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Virtuális hálózattal létesített pont-hely kapcsolat konfigurálása a PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>A helyek közötti kapcsolat ellenőrzése

A [cikk Hibaelhárítás: Az Azure-pont-hely kapcsolat problémák](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) végigvezeti a gyakori problémák pont-hely kapcsolatok.

## <a name="create-a-multisite-vpn-connection"></a>Többhelyes VPN-kapcsolat létrehozása

A helyek közötti *(s2S)* kapcsolatot hozzáadhat egy olyan virtuális hálózathoz, amely már rendelkezik hely–, pont-hely vagy hálózat-hálózati kapcsolattal. Ezt a fajta kapcsolatot gyakran *többhelyes* konfigurációnak nevezik. 

![Többhelyes kapcsolat](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Az Azure jelenleg két üzemi modellt használ: a Resource Manager-alapú és a klasszikus modellt. A két modell nem teljesen kompatibilis egymással. A különböző modellekkel való többhelyes kapcsolat konfigurálásához olvassa el az alábbi cikkeket:

* [Helyek közötti kapcsolat hozzáadása meglévő VPN-átjárókapcsolattal rendelkező virtuális hálózathoz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Helyek közötti kapcsolat hozzáadása meglévő VPN-átjárókapcsolattal rendelkező virtuális hálózathoz (klasszikus)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Az ezekben a cikkekben ismertetett lépések nem vonatkoznak az Azure ExpressRoute-ra és a helyek közötti kapcsolatkonfigurációkra. További információ: [ExpressRoute és a helyek közötti, egymás mellett lévő kapcsolatok.](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)

## <a name="configure-transit-routing"></a>Tranzitútválasztás konfigurálása

A tranzitútválasztás egy adott útválasztási forgatókönyv, ahol több hálózatot kapcsol össze egy sorláncú topológiában. Ez az útválasztás lehetővé teszi, hogy a lánc mindkét végén lévő virtuális hálózatok erőforrásai kommunikáljanak egymással a közöttük lévő virtuális hálózatokon keresztül. Tranzitútválasztás nélkül a hubon keresztül kukucskált hálózatok vagy eszközök nem érhetik el egymást.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Tranzitútválasztás konfigurálása pont-hely kapcsolatban

Képzeljen el egy olyan forgatókönyvet, amelyben a VNetA és a VNetB közötti hely-hely VPN-kapcsolatot szeretné konfigurálni. Azt is szeretné konfigurálni egy pont-hely VPN az ügyfél számára, hogy csatlakozzon a VNetA átjárójához. Ezután engedélyezni szeretné a pont-hely ügyfelek közötti tranzitútválasztást a VNetB-hez való csatlakozáshoz, amely a VNetA-n keresztül halad. 

Ez a forgatókönyv akkor támogatott, ha a BGP engedélyezve van a vneta és a vnetb közötti helyek közötti VPN-en. További információt a [Pont-hely VPN-útválasztás – például című témakörben talál.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Tranzitútválasztás konfigurálása ExpressRoute-kapcsolatban

Az Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy dedikált privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365. További információkat az [ExpressRoute áttekintésében](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) találhat.

![ExpressRoute privát társviszony-létesítési kapcsolat az Azure virtuális hálózataival](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Azt javasoljuk, hogy ha a VNetA és a VNetB ugyanabban a geopolitikai régióban, [mindkét virtuális hálózatok az ExpressRoute-kapcsolat az ExpressRoute-kapcsolat](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) konfigurálása helyett. Ha a virtuális hálózatok különböző geopolitikai régiókban vannak, közvetlenül is összekapcsolhatja őket a hálózattal, ha [ExpressRoute Premium szolgáltatással](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)rendelkezik. 

Ha ExpressRoute és a helyek közötti együttélés, a tranzit-útválasztás nem támogatott. További információt az ExpressRoute és a helyek közötti [webhely konfigurálása a PowerShell használatával című témakörben talál.](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)

Ha engedélyezte az ExpressRoute számára, hogy a helyi hálózatokat egy Azure virtuális hálózathoz csatlakoztassa, engedélyezheti a társviszony-létesítést azon virtuális hálózatok között, ahol tranzitútválasztást szeretne. Ahhoz, hogy a helyi hálózatok csatlakozhassanak a távoli virtuális hálózathoz, konfigurálnia kell a [virtuális hálózati társviszony-létesítést.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity) 

> [!Note]
> A virtuális hálózati társviszony-létesítés csak az ugyanabban a régióban lévő virtuális hálózatok esetében érhető el.

Annak ellenőrzéséhez, hogy konfigurálta-e a virtuális hálózati társviszony-létesítéshez való tranzitútválasztást, kövesse az alábbi utasításokat:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a szükséges [szerepkörökkel és engedélyekkel](virtual-network-manage-peering.md#permissions)rendelkező fiókkal.
2. [Hozzon létre egy társviszony-létesítést a VNetA és a VNetB között,](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) ahogy az a korábbi diagramon látható. 
3. A virtuális hálózathoz megjelenő ablaktáblán válassza a **Beállítások** szakasz **Társviszony-létesítése** lehetőséget.
4. Jelölje ki a megtekinteni kívánt társviszony-létesítést. Ezután válassza a **Konfiguráció** lehetőséget annak ellenőrzéséhez, hogy engedélyezte-e az **átjáróátvitelt** az ExpressRoute-kapcsolathoz csatlakoztatott VNetA-hálózaton, és használja a távoli virtuális **átjárót** az ExpressRoute-kapcsolathoz nem kapcsolódó távoli VNetB-hálózaton.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Tranzitútválasztás konfigurálása virtuális hálózati társviszony-létesítési kapcsolatban

Ha két virtuális hálózat között társhálózati viszony van, a virtuális társhálózatban működő átjáró is konfigurálható átviteli pontként a helyi hálózat felé. Ha a virtuális hálózati társviszony-létesítésben szeretne tranzitútvonalat konfigurálni, olvassa el [a Hálózat-hálózat kapcsolatok című témakört.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)

> [!Note]
> Átjáró-átvitel nem támogatott a különböző telepítési modelleken keresztül létrehozott virtuális hálózatok közötti társviszony-létesítési kapcsolat. A társviszony-létesítési kapcsolat mindkét virtuális hálózatának az Erőforrás-kezelőn keresztül kell létrehoznia az átjáró átvitelének működését.

Annak ellenőrzéséhez, hogy konfigurált-e tranzitútvonalat a virtuális hálózati társviszony-létesítéshez, kövesse az alábbi utasításokat:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a szükséges [szerepkörökkel és engedélyekkel](virtual-network-manage-peering.md#permissions)rendelkező fiókkal.
2. A portál tetején található **Keresési erőforrások** szövegét tartalmazó mezőbe írja be a virtuális **hálózatok**kifejezést. Amikor **a virtuális hálózatok** megjelennek a keresési eredmények között, jelölje ki azt.
3. A **megjelenő Virtuális hálózatok** panelen jelölje ki azt a virtuális hálózatot, amelynek társviszony-létesítési beállítását ellenőrizni szeretné.
4. A kiválasztott virtuális hálózathoz megjelenő ablaktáblán válassza a **Beállítások** szakasz **Társviszony-létesítése** lehetőséget.
5. Jelölje ki a megtekinteni kívánt társviszony-létesítést. Ellenőrizze, hogy engedélyezte-e **az átjárók átvitelének engedélyezését** és **a Távoli átjárók használatát** a Konfiguráció **csoportban.**

![Kijelölések annak ellenőrzésére, hogy konfigurált-e tranzitútvonalat a virtuális hálózati társviszony-létesítéshez](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Tranzitútválasztás konfigurálása hálózat-hálózat kapcsolatban

A virtuális hálózatok közötti tranzitútválasztás konfigurálásához engedélyeznie kell a BGP-t az összes köztes hálózat-hálózat kapcsolatokon az Erőforrás-kezelő telepítési modelljének és a PowerShellnek. További információt a [BGP konfigurálása az Azure VPN-átjáróin a PowerShell használatával című témakörben talál.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

Az Azure VPN-átjárókon keresztül imitátor a klasszikus üzembe helyezési modellen keresztül érhető el, de ez a hálózati konfigurációs fájlban statikusan definiált címterekre támaszkodik. A BGP még nem támogatott az Azure virtuális hálózataival és VPN-átjáróival a klasszikus üzembe helyezési modellen keresztül. BGP nélkül a tranzitcímterek manuális definiálása hibaérzékeny, és nem javasoljuk.

> [!Note]
> Klasszikus hálózati kapcsolatok konfigurálása az Azure klasszikus portálhasználatával, vagy egy hálózati konfigurációs fájl használatával a klasszikus portálon. Klasszikus virtuális hálózatot nem hozhat létre és nem módosíthat az Azure Resource Manager telepítési modelljén vagy az Azure Portalon keresztül. A klasszikus virtuális hálózatok tranzitútválasztásáról a [Microsoft Developer blogban](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)talál további információt.

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Tranzitútválasztás konfigurálása helyek közötti kapcsolatban

A helyszíni hálózat és a helyek közötti kapcsolattal rendelkező virtuális hálózat közötti tranzitútválasztás konfigurálásához engedélyeznie kell a BGP-t az összes köztes hely-hely kapcsolatokon az Erőforrás-kezelő telepítési modelljének és a PowerShellnek a használatával. [Olvassa el a BGP konfigurálása az Azure VPN-átjárókon](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) a PowerShell használatával című témakört az utasításokért.

Az Azure VPN-átjárókon keresztül imitátor a klasszikus üzembe helyezési modellen keresztül érhető el, de ez a hálózati konfigurációs fájlban statikusan definiált címterekre támaszkodik. A BGP még nem támogatott az Azure virtuális hálózataival és VPN-átjáróival a klasszikus üzembe helyezési modellen keresztül. BGP nélkül a tranzitcímterek manuális definiálása hibaérzékeny, és nem javasoljuk.

> [!Note]
> Klasszikus helyek közötti kapcsolatok konfigurálása az Azure klasszikus portálhasználatával, vagy egy hálózati konfigurációs fájl használatával a klasszikus portálon. Klasszikus virtuális hálózatot nem hozhat létre és nem módosíthat az Azure Resource Manager telepítési modelljén vagy az Azure Portalon keresztül. A klasszikus virtuális hálózatok tranzitútválasztásáról a [Microsoft Developer blogban](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)talál további információt.

## <a name="configure-bgp-for-a-vpn-gateway"></a>BGP konfigurálása egy VPN-átjáró számára

A BGP az a szabványos útválasztási protokoll, amelyet az interneten két vagy több hálózat közötti útválasztásés elérhetőségi információk cseréjére használnak. Ha a BGP-t az Azure virtuális hálózatok környezetében használja, engedélyezi az Azure VPN-átjárókat és a helyszíni VPN-eszközöket, más néven BGP-társakat vagy szomszédokat. "útvonalakat" cserélnek, amelyek tájékoztatják mindkét átjárót az előtagok elérhetőségéről és elérhetőségéről, hogy az érintett átjárókon vagy útválasztókon keresztül haladjanak. 

A BGP több hálózat közötti átmenő útválasztást is képes engedélyezni, ha propagálja azokat az útvonalakat, amelyeket a BGP-átjáró az egyik BGP-társtól az összes többi BGP-társhoz tanul. További információ: [BGP az Azure VPN-átjáróval című témakörben.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)

### <a name="configure-bgp-for-a-vpn-connection"></a>A BGP konfigurálása VPN-kapcsolathoz

A BGP-t használó VPN-kapcsolat konfigurálásához olvassa el [a BGP konfigurálása az Azure VPN-átjáróin a PowerShell használatával című témakört.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

Engedélyezze a BGP-t a virtuális hálózati átjárón egy autonóm rendszerszám (AS) létrehozásával. Az alapvető átjárók nem támogatják a BGP-t. Az átjáró termékváltozatának ellenőrzéséhez nyissa meg az Azure Portalon a **VPN-átjáró** panel **Áttekintés szakaszát.** Ha a termékváltozat **alapszintű,** akkor a termékváltozatot [(lásd: Az átjáró átméretezése](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) **vpnGw1 értékre kell módosítania.** 

A termékváltozat ellenőrzése 20–30 perc állásidőt eredményez. Amint az átjáró rendelkezik a megfelelő termékváltozattal, hozzáadhatja az [AS-számot a Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell parancslap használatával. Az AS-szám konfigurálása után a rendszer automatikusan megad egy BGP-társ IP-címet az átjáróhoz.

Manuálisan meg `LocalNetworkGateway` kell adnia egy AS-számot és egy BGP-társcímet. A `ASN` [new-azurermlocalnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) vagy a [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell powershell parancslap használatával állíthatja be a és `-BgpPeeringAddress` az értékeket. Egyes AS-számok az Azure számára vannak fenntartva, és nem használhatja őket a [BGP-ről az Azure VPN-átjáróval](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)című részben leírtak szerint.

A kapcsolatobjektumban engedélyezni kell a BGP-t. Az értéket `-EnableBGP` a `$True` [New-AzureRmVirtualNetworkConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) vagy a [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)segítségével állíthatja be.

### <a name="validate-the-bgp-configuration"></a>A BGP-konfiguráció ellenőrzése

Annak ellenőrzéséhez, hogy a BGP megfelelően `get-AzureRmLocalNetworkGateway` van-e konfigurálva, futtathatja a és a `get-AzureRmVirtualNetworkGateway` parancsleteket. Akkor észre fogod venni, BGP `BgpSettingsText` kapcsolatos kimenet a részen. Példa:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Magas rendelkezésre állású aktív/aktív VPN-kapcsolat létrehozása

Az aktív/aktív és az aktív/készenléti átjárók közötti legfontosabb különbségek a következők:

* Két átjáró IP-konfigurációját kell létrehoznia két nyilvános IP-címmel.
* Be kell állítania az **EnableActiveActiveFeature** jelzőt.
* Az átjáró termékváltozatának **VpnGw1**, **VpnGw2**vagy **VpnGw3**kell lennie.

A létesítmények közötti és a hálózatközi kapcsolat magas rendelkezésre állásának elérése érdekében több VPN-átjárót kell üzembe helyeznie, és több párhuzamos kapcsolatot kell létesítenie a hálózatok és az Azure között. A kapcsolódási lehetőségek és a topológia áttekintését a [Magas rendelkezésre állású létesítmények közötti és a hálózatközi kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)című témakörben találja.

Aktív/aktív létesítmények közötti és hálózatközi kapcsolatok létrehozásához kövesse az [Aktív/aktív S2S VPN-kapcsolatok konfigurálása az Azure VPN-átjárókkal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) című útmutató utasításait az Azure VPN-átjárók aktív/aktív módban való konfigurálásához.

> [!Note]  
> * Amikor címeket ad hozzá a helyi hálózati átjáróhoz A BGP-kompatibilis aktív/aktív módban, *csak a BGP-partnerek /32-es címét adja hozzá.* Ha további címeket ad hozzá, azok statikus útvonalaknak minősülnek, és elsőbbséget élveznek a BGP-útvonalakkal szemben.
> * Az Azure-hoz csatlakozó helyszíni hálózatokhoz különböző BGP AS-számokat kell használnia. (Ha megegyeznek, meg kell változtatnia a virtuális hálózat AS számát, ha a helyszíni VPN-eszköz már használja az ASN-t a többi BGP-szomszédtársi kapcsolatra.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Azure VPN-átjárótípus módosítása üzembe helyezés után

Az Azure virtuális hálózati átjáró típusa nem módosítható házirend-alapú útvonal-alapú, vagy a másik út közvetlenül. Először törölnie kell az átjárót. Ezt követően az IP-cím és az előmegosztott kulcs nem marad meg. Ezután létrehozhat egy új átjárót a kívánt típushoz. 

Átjáró törléséhez és létrehozásához hajtsa végre az alábbi lépéseket:

1. Törölje az eredeti átjáróhoz társított kapcsolatokat.
2. Törölje az átjárót az Azure Portalon, a PowerShellen vagy a klasszikus PowerShellen keresztül: 
   * [Virtuális hálózati átjáró törlése az Azure Portal használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Virtuális hálózati átjáró törlése a PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Virtuális hálózati átjáró törlése a PowerShell használatával (klasszikus)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Kövesse a [VPN-átjáró létrehozása](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) című részben leírt lépéseket a kívánt típusú új átjáró létrehozásához és a VPN-beállítás befejezéséhez.

> [!Note]
> Ez a folyamat körülbelül 60 percet vesz igénybe.

## <a name="next-steps"></a>További lépések

* [Azure-beli virtuális gépek közötti kapcsolatok hibaelhárítása](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

