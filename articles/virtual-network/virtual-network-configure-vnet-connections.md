---
title: VNet- vagy VPN-kapcsolatok konfigurálása és ellenőrzése
description: Részletes útmutató különböző Azure VPN-és VNet-telepítések konfigurálásához és ellenőrzéséhez
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
ms.openlocfilehash: fc4b649ce8d082d8d854c4c19b617c088ff3141c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901892"
---
# <a name="configure-and-validate-vnet-or-vpn-connections"></a>VNet- vagy VPN-kapcsolatok konfigurálása és ellenőrzése

Ez az útmutató részletes útmutatást nyújt a különböző Azure-beli VPN-és VNet-telepítések konfigurálásához és ellenőrzéséhez olyan forgatókönyvekben, mint például a tranzit útválasztás, a VNet – VNet, a BGP, a többhelyes, a pont – hely stb.

Az Azure-beli VPN-átjárók rugalmasságot biztosítanak szinte bármilyen csatlakoztatott Virtual Network (VNet) topológia megszervezésében az Azure-ban: a virtuális hálózatok különböző régiókban, VNet-típusok között (Azure Resource Manager és Klasszikus), az Azure-ban vagy a helyszíni hibrid környezettel, különböző előfizetésekben és így tovább. 

## <a name="scenario-1-vnet-to-vnet-vpn-connection"></a>forgatókönyv 1: VNet – VNet VPN-kapcsolat

A virtuális hálózat VPN-kapcsolaton keresztüli csatlakoztatása egy másik virtuális hálózathoz (VNet – VNet) hasonló ahhoz, hogy egy VNet egy helyszíni helyhez kapcsolódjon. Mindkét kapcsolattípus VPN-átjárót használ a biztonságos alagút biztosításához az **IPSec/IKE**használatával. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben.

![LEMEZKÉP](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
1\. ábra – VNet és VNet az IPsec használatával

Ha a virtuális hálózatok ugyanabban a régióban vannak, érdemes megfontolni, hogy a VNet-társítással kapcsolódjon hozzájuk, amely nem VPN-átjárót használ, növeli az átviteli sebességet, és csökkenti a késést, válassza a VNet-társítás **konfigurálása és érvényesítése** a VNet-társítás konfigurálásához lehetőséget. kapcsolat.

Ha a virtuális hálózatok az Azure Resource Manger-alapú üzemi modellel is létrehozták, akkor válassza a **Resource Manager-VNet konfigurálása és ellenőrzése Resource Manager VNet-kapcsolaton** lehetőséget a VPN-kapcsolat konfigurálásához.

Ha az egyik virtuális hálózatok-t a klasszikus Azure üzemi modellel hozza létre, a másikat a Resource Manager hozza létre, válassza a **klasszikus VNet konfigurálása és ellenőrzése Resource Manager VNet-kapcsolaton** lehetőséget a VPN-kapcsolat konfigurálásához.

### <a name="configuration-1-configure-vnet-peering-for-two-vnets-in-the-same-region"></a>1\. konfiguráció: VNet-társítás konfigurálása két virtuális hálózatok ugyanabban a régióban

Az Azure VNet-társítás megvalósításának megkezdése előtt győződjön meg arról, hogy megfelel a következő előfeltételeknek a VNet-társítás konfigurálásához:

* A virtuális társhálózatoknak ugyanabban az Azure-régióban kell lenniük.
* A társ virtuális hálózatoknak egymást nem fedő IP-címmel kell rendelkezniük.
* A virtuális hálózati társviszony két virtuális hálózat között jön létre. Nincs származtatott tranzitív kapcsolat a különböző társítások között. Ha például az A virtuális hálózat társviszonyban áll a B virtuális hálózattal, és a B hálózat társviszonyban áll a C virtuális hálózattal, az *nem* jelenti azt, hogy társviszony van az A virtuális hálózat és a C virtuális hálózat között.

A követelmények teljesítése után a VNet-társítás létrehozásához és konfigurálásához kövesse [a virtuális hálózat társításának létrehozása oktatóanyagot](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) .

A VNet-társítás konfigurációjának vizsgálatához használja a következő módszereket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan fiókkal, amely rendelkezik a szükséges [szerepkörökkel és engedélyekkel](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions).
2. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a következőt: *Virtual Networks (virtuális hálózatok*). Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, kattintson rá.
3. A megjelenő **Virtual Networks (virtuális hálózatok** ) panelen kattintson arra a virtuális hálózatra, amelyhez a társítást létre kívánja hozni.
4. A kiválasztott virtuális hálózatban megjelenő ablaktáblán kattintson a **Beállítások** szakaszban található társítások **elemre** .
5. Kattintson arra a társra, amelynél ellenőriznie szeretné a konfigurációt.

![LEMEZKÉP](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Az Azure PowerShell használatával futtassa a [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) parancsot a virtuális hálózat társításának beszerzéséhez, például:

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

### <a name="connection-type-1-connect-a-resource-manager-vnet-to-anther-resource-manager-vnet-azure-resource-manager-to-azure-resource-manager"></a>1\. kapcsolattípus: Resource Manager-VNet összekötése a portok Resource Manager-VNet (Azure Resource Manager Azure Resource Manager)

Az egyik Resource Manager-VNet közötti kapcsolatokat közvetlenül is konfigurálhatja egy másik Resource Manager-VNet, vagy konfigurálhatja a kapcsolódást az IPsec használatával.

### <a name="configuration-2-configure-vpn-connection-between-resource-manager-vnets"></a>2\. konfiguráció: A VPN-kapcsolat konfigurálása Resource Manager-virtuális hálózatok között

Ha az IPsec nélküli Resource Manager-virtuális hálózatok közötti kapcsolatot szeretne konfigurálni, tekintse meg a következőt: [VNet-to-VNET VPN Gateway-kapcsolat konfigurálása a Azure Portal használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Ha két Resource Manager-virtuális hálózatok között IPsec-kapcsolatot szeretne konfigurálni, kövesse 1-5 az egyes VNet [Azure Portal helyek közötti kapcsolat létrehozása](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) című szakaszát.

> [!Note]
> Ezek a lépések csak egyazon előfizetésben lévő virtuális hálózatokkal működnek. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, a kapcsolatot a PowerShell használatával kell létrehozni. Lásd a [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)-re vonatkozó cikket.

### <a name="validate-vpn-connection-between-resource-manager-vnets"></a>A Resource Manager virtuális hálózatok közötti VPN-kapcsolat ellenőrzése

![LEMEZKÉP](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

4\. ábra – klasszikus VNet-kapcsolatok Azure Resource Manager VNet

A VPN-kapcsolat megfelelő konfigurálásának ellenőrzéséhez kövesse az alábbi utasításokat:

> [!Note]
> A virtuális hálózati összetevők, például az alábbi lépések "vnet 1" utáni száma a 4. ábrán szereplő számoknak felel meg.

1. A csatlakoztatott virtuális hálózatok található átfedésben lévő címek keresése.

   > [!Note]
   > A vnet 1 és a vnet 6 nem tartalmazhat átfedésben lévő címeket. 

2. Ellenőrizze, hogy Azure Resource Manager vnet 1 címtartomány pontosan van-e definiálva a 4. **összekötő objektumban** .
3. Ellenőrizze, hogy Azure Resource Manager vnet 6 címtartomány pontosan van-e definiálva a 3. **objektumban** .
4. Ellenőrizze, hogy az előmegosztott kulcsok megfelelnek-e a 3. és a 4. összekapcsolási objektumoknak.
5. Ellenőrizze, hogy az Azure Resource Manager vnet Gateway 2 VIP-je pontosan van-e definiálva a 4. **összekötő objektumban** .
6. Ellenőrizze, hogy a Azure Resource Manager vnet-átjáró 5 VIP-je pontosan van-e definiálva a 3. **összekötő objektumban** .

### <a name="connection-type-2-connect-a-classic-vnet-to-a-resource-manager-vnet"></a>2\. kapcsolattípus: Hagyományos és erőforrás-kezelői virtuális hálózatok csatlakoztatása

Létrehozhat egy kapcsolatot a különböző előfizetésekben és különböző régiókban található virtuális hálózatok között. Olyan virtuális hálózatok is csatlakozhat, amelyek már rendelkeznek kapcsolattal a helyszíni hálózatokkal, feltéve, hogy az átjáró típusát Route-alapúként konfigurálták.

A klasszikus VNet és a Resource Manager-VNet közötti kapcsolat konfigurálásával kapcsolatban lásd: [a virtuális hálózatok összekapcsolása különböző üzemi modellekkel a Azure Portal használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal) további információért.

![LEMEZKÉP](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

5\. ábra – klasszikus VNet-kapcsolatok Azure Resource Manager VNet

A klasszikus VNet Azure Resource Manager VNet való összekapcsolásához kövesse az alábbi utasításokat:

> [!Note]
> A virtuális hálózati összetevők, például az alábbi lépések "vnet 1" utáni száma az 5. ábrán szereplő számoknak felel meg.

1. A csatlakoztatott virtuális hálózatok található átfedésben lévő címek keresése.

   > [!Note]
   > A vnet 1 és a vnet 6 nem tartalmazhat átfedésben lévő címeket.

2. Ellenőrizze, hogy Azure Resource Manager VNet 6 címtartomány pontosan van-e meghatározva a klasszikus helyi hálózati definícióban 3.
3. Ellenőrizze, hogy a klasszikus VNet 1 címtartomány pontosan van-e definiálva a (Azure Resource Manager) 4. **összekötő objektumban** .
4. Győződjön meg arról, hogy a klasszikus VNet-átjáró 2 VIP-je pontosan van definiálva a (Azure Resource Manager) 4. **összekötő objektumban** .
5. Ellenőrizze, hogy a Azure Resource Manager VNet-átjáró 5 VIP-je pontosan van-e meghatározva a klasszikus **helyi hálózati definícióban** 3.
6. Ellenőrizze, hogy az előmegosztott kulcsok megfelelnek-e mindkét csatlakoztatott virtuális hálózatok:
   1. Klasszikus vnet – helyi hálózati definíció 3
   2. Azure Resource Manager vnet – 4. összekötő objektum

## <a name="scenario-2-point-to-site-vpn-connection"></a>2\. forgatókönyv: Pont – hely típusú VPN-kapcsolat

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolatok akkor hasznosak, ha egy távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról, vagy akkor, ha csak néhány ügyfelet szeretne csatlakoztatni egy virtuális hálózathoz. A P2S VPN kapcsolatot az ügyfélszámítógépről kezdeményezik a natív Windows VPN-ügyfél használatával. Az ügyfelek hitelesítése a kapcsolódáskor tanúsítványokkal történik.

![LEMEZKÉP](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

2\. ábra – pont – hely kapcsolat

A pont – hely kapcsolatokhoz nem szükséges VPN-eszköz. A P2S-kapcsolat SSTP (Secure Socket Tunneling Protocol) használatával hozza létre a VPN-kapcsolatot. A pont – hely kapcsolat összekapcsolható egy VNet egy másik üzembe helyezési eszköz és telepítési modell használatával:

* [Pont–hely kapcsolat konfigurálása virtuális hálózat számára az Azure Portalon](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Pont – hely kapcsolat konfigurálása VNet a Azure Portal (klasszikus) használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Pont–hely kapcsolat konfigurálása virtuális hálózathoz a PowerShell segítségével](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connections"></a>Pont – hely kapcsolatok ellenőrzése

A cikk [hibaelhárítása: Az Azure pont – hely kapcsolati problémák](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) a pont – hely kapcsolatokkal kapcsolatos gyakori problémákon haladnak végig.

## <a name="scenario-3-multi-site-vpn-connection"></a>3\. forgatókönyv: Többhelyes VPN-kapcsolat

Hozzáadhat egy helyek közötti (S2S) kapcsolatokat egy olyan VNet, amely már rendelkezik S2S-kapcsolattal, pont – hely kapcsolattal vagy VNet – VNet kapcsolattal, ez a fajta kapcsolat gyakran **többhelyes** konfigurációként is ismert. 

![LEMEZKÉP](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

3\. ábra – többhelyes kapcsolat

Az Azure jelenleg két üzembe helyezési modellel működik: Resource Manager és klasszikus. A két modell nem teljesen kompatibilis egymással. A **többhelyes** kapcsolat különböző modellekkel való konfigurálásához olvassa el a következő cikkeket:

* [Helyek közötti kapcsolat hozzáadása egy meglévő VPN Gateway-kapcsolattal rendelkező VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Helyek közötti kapcsolat hozzáadása egy VNet meglévő VPN Gateway-kapcsolattal (klasszikus)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Ezek a lépések nem vonatkoznak a ExpressRoute és a helyek közötti egyidejű kapcsolati konfigurációkra. További információ a meglévő kapcsolatokról: [ExpressRoute/S2S](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)egyidejű kapcsolatok.

## <a name="scenario-4-configure-transit-routing"></a>4\. forgatókönyv: Tranzit útválasztás konfigurálása

A tranzitív útválasztás egy adott útválasztási forgatókönyv, amelyben több hálózat is csatlakoztatható egy "Daisy Chain" topológiában. Ez az Útválasztás lehetővé teszi, hogy a "lánc" végén a virtuális hálózatok-ban lévő erőforrások egymással kommunikáljanak egymással a virtuális hálózatok között. A tranzitív útválasztás nélkül a hub-on keresztül megjelenő hálózatok vagy eszközök nem tudnak egymáshoz jutni.

### <a name="configuration-1-configure-transit-routing-in-a-point-to-site-connection"></a>1\. konfiguráció: Tranzit útválasztás konfigurálása pont – hely kapcsolaton

Ebben a forgatókönyvben a helyek közötti VPN-kapcsolatot konfigurálja a Társviszonyban áll és a b között, továbbá egy pont – hely VPN-t is konfigurálhat az ügyfél számára a Társviszonyban áll-átjáróhoz való csatlakozáshoz. Ezt követően engedélyezni szeretné a pont – hely típusú ügyfelek számára a b-hez való kapcsolódáshoz szükséges tranzit útválasztást, amely áthalad a Társviszonyban áll. Ez a forgatókönyv akkor támogatott, ha a BGP engedélyezve van a helyen a Társviszonyban áll és a b közötti VPN-kapcsolaton. További információ: [a pont – hely VPN-útválasztás](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configuration-2-configure-transit-routing-in-an-expressroute-connection"></a>2\. konfiguráció: ExpressRoute-kapcsolat átviteli útvonalának konfigurálása

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy dedikált privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft-felhőszolgáltatásokkal, mint például a Microsoft Azure, az Office 365 és a Dynamics 365.  További információ: [ExpressRoute – áttekintés](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![LEMEZKÉP](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

6\. ábra – a "privát ExpressRoute" kapcsolódás az Azure virtuális hálózatok

> [!Note]
> Azt javasoljuk, hogy ha a Társviszonyban áll és a b ugyanabban a geopolitikai régióban legyenek, amelyet a [ExpressRoute-áramkörhöz](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) is csatlakoztatni kell a tranzitív útválasztás konfigurálása helyett. Ha a virtuális hálózatok különböző geopolitikai régiókban vannak, akkor közvetlenül is összekapcsolhatja őket az áramkörrel, ha [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)van. 

Ha a ExpressRoute és a helyek közötti párhuzamos tárolással rendelkezik, a tranzit útválasztás nem támogatott. További információ: [a ExpressRoute és a helyek közötti egyidejű kapcsolatok konfigurálása](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Ha engedélyezte a ExpressRoute számára a helyi hálózatok Azure-beli virtuális hálózathoz való összekapcsolását, akkor engedélyezheti a VNet a tranzitív útválasztást igénylő virtuális hálózatok között. Annak engedélyezéséhez, hogy a helyi hálózatok csatlakozzanak a távoli VNet, konfigurálnia kell a [virtuális hálózati](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)társítást. 

> [!Note]
> A VNet-társítás csak ugyanabban a régióban található virtuális hálózatok érhető el.

Kövesse az alábbi utasításokat, hogy meggyőződjön arról, hogy konfigurálta-e a VNet-társítás továbbítási útvonalát:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan fiókkal, amely rendelkezik a szükséges [szerepkörökkel és engedélyekkel](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions).
2. [Hozzon létre egy társítást az a és B VNet között](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) a korábbi diagramhoz hasonlóan (6. ábra). 
3. A kiválasztott virtuális hálózatban megjelenő ablaktáblán kattintson a **Beállítások** szakaszban **található** társítások elemre.
4. Kattintson a megtekinteni és **konfigurálni** kívánt társításra annak ellenőrzéséhez, hogy engedélyezve van-e az **átjáró továbbítása** az ExpressRoute-áramkörhöz csatlakoztatott társviszonyban áll, és használja a távoli **átjárót** a távoli b, amely nem csatlakozik a ExpressRoute kapcsolatcsoport.

### <a name="configuration-3-configure-transit-routing-in-a-vnet-peering-connection"></a>3\. konfiguráció: VNet-társítási kapcsolat átviteli útvonalának konfigurálása

Ha két virtuális hálózat között társhálózati viszony van, a virtuális társhálózatban működő átjáró is konfigurálható átviteli pontként a helyi hálózat felé. A VNet-társítás átviteli útvonalának konfigurálásához keresse meg a [virtuális hálózat – virtuális hálózati kapcsolatok](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)csomópontot.

> [!Note]
> Az átjáró átvitele nem támogatott a különböző üzembe helyezési modelleken keresztül létrehozott virtuális hálózatok közötti társítási kapcsolaton. Ahhoz, hogy az átjárón át történő átvitel működjön, a társviszonyban álló virtuális hálózatok közül mindkettőt a Resource Manager-alapú üzemi modellel kell létrehozni.

Kövesse az alábbi utasításokat, hogy meggyőződjön arról, hogy konfigurálta-e a VNet-társítás továbbítási útvonalát:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan fiókkal, amely rendelkezik a szükséges [szerepkörökkel és engedélyekkel](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions).
2. A Azure Portal tetején található szöveges keresési erőforrásokat tartalmazó mezőbe írja be a következőt: *Virtual Networks (virtuális hálózatok*). Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, kattintson rá.
3. A megjelenő **Virtual Networks (virtuális hálózatok** ) panelen kattintson arra a virtuális hálózatra, amelyen meg szeretné nézni a társítási beállítást.
4. A kiválasztott virtuális hálózatban megjelenő ablaktáblán kattintson a **Beállítások** szakaszban **található** társítások elemre.
5. Kattintson a megtekinteni kívánt társításra, és ellenőrizze, hogy engedélyezve van-e az **átjárók átvitelének engedélyezése** és **távoli átjáró használata** a **konfiguráció**alatt.

![LEMEZKÉP](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configuration-4-configure-transit-routing-in-a-vnet-to-vnet-connection"></a>4\. konfiguráció: VNet-VNet kapcsolat átviteli útvonalának konfigurálása

A virtuális hálózatok közötti átviteli útválasztás konfigurálásához engedélyeznie kell a BGP-t az összes köztes VNet-VNet kapcsolaton a Resource Manager-alapú üzemi modell és a PowerShell használatával. Útmutatásért lásd: a [BGP konfigurálása az Azure VPN Gatewayn a PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Az Azure VPN gatewayen keresztüli átmenő forgalom a klasszikus üzemi modell használatával lehetséges, de a hálózati konfigurációs fájlban statikusan definiált címekre támaszkodik. A BGP még nem támogatott az Azure Virtual Networks és a VPN Gateway használatával a klasszikus üzemi modellel. A BGP nélkül az átviteli címterület manuális definiálása hibás, és nem ajánlott.

> [!Note]
> A klasszikus VNet-VNet kapcsolatok az Azure Portal (klasszikus) vagy egy hálózati konfigurációs fájl használatával konfigurálhatók a klasszikus portálon. Klasszikus virtuális hálózat nem hozható létre és nem módosítható a Azure Resource Manager üzemi modell vagy Azure Portal használatával. További információ a klasszikus virtuális hálózatok átmenő útválasztásról: [Hub & küllő, Daisy-Chain és Full-Mesh VNET-topológiák az Azure ARM-ben VPN (v1) használatával](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configuration-5-configure-transit-routing-in-a-site-to-site-connection"></a>5\. konfiguráció: Az átviteli útválasztás konfigurálása helyek közötti kapcsolaton keresztül

A helyszíni hálózat és a helyek közötti kapcsolattal rendelkező VNet közötti átviteli útválasztás konfigurálásához engedélyeznie kell a BGP-t az összes köztes helyek közötti kapcsolathoz a Resource Manager-alapú üzemi modell és a PowerShell használatával: [Konfigurálás BGP az Azure VPN Gateway átjárón a PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) az utasításokhoz.

Az Azure VPN gatewayen keresztüli átmenő forgalom a klasszikus üzemi modell használatával lehetséges, de a hálózati konfigurációs fájlban statikusan definiált címekre támaszkodik. A BGP még nem támogatott az Azure Virtual Networks és a VPN Gateway használatával a klasszikus üzemi modellel. A BGP nélkül az átviteli címterület manuális definiálása hibás, és nem ajánlott.

> [!Note]
> A klasszikus helyek közötti kapcsolatok a Azure Portal (klasszikus) vagy egy hálózati konfigurációs fájl használatával konfigurálhatók a klasszikus portálon. Klasszikus virtuális hálózat nem hozható létre és nem módosítható a Azure Resource Manager üzemi modell vagy Azure Portal használatával. További információ a klasszikus virtuális hálózatok átmenő útválasztásról: [Hub & küllő, Daisy-Chain és Full-Mesh VNET-topológiák az Azure ARM-ben VPN (v1) használatával](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="scenario-5-configure-bgp-for-a-vpn-gateway"></a>5\. forgatókönyv: BGP konfigurálása egy VPN-átjáró számára

A BGP az interneten használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információk két vagy több hálózat közötti cseréjéhez. Ha a BGP az Azure Virtual Networks környezetében használatos, a BGP lehetővé teszi az Azure VPN Gateway és a helyszíni VPN-eszközök, azaz a BGP-társak vagy a szomszédok használatát. Ők cserélik az "útvonalakat", amely tájékoztatja mindkét átjárót az előtagok rendelkezésre állásáról és elérhetőségéről, hogy átmenjen az érintett átjárók vagy útválasztók között. A BGP lehetővé teszi a több hálózat közötti tranzit útválasztást azon útvonalak propagálásával az összes többi BGP-társ számára, amelyeket a BGP-átjáró az egyik BGP-társtól vesz át. További információ: [a BGP és az Azure VPN Gateway közötti áttekintés](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>A BGP konfigurálása VPN-kapcsolathoz

A BGP-t használó VPN-kapcsolat konfigurálásával kapcsolatban lásd: a [BGP konfigurálása Azure VPN Gateway-átjárón a PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

> [!Note]
> 1. Engedélyezze a BGP-t a Virtual Network átjárón úgy, hogy létrehoz egy AS-számot. Az alapszintű átjárók nem támogatják a BGP-t. Az átjáró SKU-jának vizsgálatához nyissa meg a Azure Portal VPN Gateway paneljének áttekintés szakaszát. Ha az SKU **alapszintű**, akkor módosítania kell az SKU-t ([az átjáró átméretezése](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) a **VpnGw1** SKU-ra. Az SKU ellenőrzése 20-30 percet eredményez. Amint az átjáró megfelelő SKU-val rendelkezik, az AS a [set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell-paranccsal vehető fel. A AS szám konfigurálása után a rendszer automatikusan megadja a BGP-társ IP-címet az átjáró számára.
> 2. A LocalNetworkGateway **manuálisan** kell MEGADNI az as-számmal és egy BGP-társi címtől. Az **ASN** és a **-BgpPeeringAddress** értékeket a [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) vagy a [set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell-paranccsal állíthatja be. Egyes számok az Azure számára vannak fenntartva, és nem használhatók fel az Azure-beli [BGP-vel VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview#bgp-faq).
> 3. Végezetül a kapcsolatok objektumnak BGP-kompatibilisnek kell lennie. A **-EnableBGP** értéket beállíthatja a `$True` [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) vagy a [set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)értékre.

### <a name="validate-the-bgp-configuration"></a>A BGP-konfiguráció ellenőrzése

Annak ellenőrzéséhez, hogy a BGP megfelelően van-e konfigurálva, `get-AzureRmVirtualNetworkGateway` futtathatja a parancsmagot, és `get-AzureRmLocalNetworkGateway`a BgpSettingsText részben a BGP-vel kapcsolatos kimenetet fogja észlelni. Példa: BgpSettingsText:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="scenario-6-highly-available-active-active-vpn-connection"></a>6\. forgatókönyv: Fokozottan elérhető aktív-aktív VPN-kapcsolat

Az aktív-aktív és az aktív-készenléti átjárók közötti fő különbségek:

* Két nyilvános IP-címmel rendelkező átjáró IP-konfigurációt kell létrehoznia
* Be kell állítania a *EnableActiveActiveFeature* jelzőt.
* Az átjáró SKU-jának VpnGw1, VpnGw2, VpnGw3 kell lennie

Ha magas rendelkezésre állást szeretne biztosítani a létesítmények közötti és VNet kapcsolatokhoz, több VPN-átjárót kell üzembe helyeznie, és több párhuzamos kapcsolatot kell létesítenie a hálózatok és az Azure között. A kapcsolódási lehetőségek és a topológia áttekintését lásd: a [létesítmények közötti, illetve a VNet és a VNet közötti kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable).

Az aktív-aktív és a VNet közötti kapcsolatok létrehozásához kövesse az [Active-Active S2S VPN-kapcsolatok konfigurálása Azure VPN-átjárókkal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) az Azure VPN Gateway aktív/aktív módban való konfigurálásához című témakör utasításait.

> [!Note]  
> 1. Ha olyan címeket ad hozzá a helyi hálózati átjáróhoz, amelyeken engedélyezve van a BGP, az aktív – aktív mód *csak a BGP-társak/32-címét adja hozzá*. Ha további címeket ad hozzá, akkor a rendszer statikus útvonalaknak számít, és elsőbbséget élvez a BGP-útvonalakkal szemben.
> 2. Az Azure-hoz csatlakozó helyszíni hálózatokhoz eltérő BGP-ASN kell használnia. (Ha ezek megegyeznek, akkor módosítania kell a VNet ASN-t, ha a helyszíni VPN-eszköze már használja az ASN-t a többi BGP-szomszédokkal.)

## <a name="scenario-7-change-an-azure-vpn-gateway-type-after-deployment"></a>7\. forgatókönyv: Azure-beli VPN-átjáró típusának módosítása üzembe helyezés után

Az Azure VNet-átjáró típusa nem módosítható házirend-alapúról Route-alapú vagy más módon közvetlenül. Az átjárót az IP-cím és az előmegosztott kulcs (PSK) won'tbe megőrzése után törölnie kell. Ezután létrehozhatja a kívánt típusú új átjárót. Átjáró törléséhez és létrehozásához kövesse az alábbi lépéseket:

1. Törölje az eredeti átjáróhoz társított összes kapcsolatot.
2. Az átjáró törlése Azure Portal, PowerShell vagy klasszikus PowerShell használatával: 
   * [Virtuális hálózati átjáró törlése a Azure Portal használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Virtuális hálózati átjáró törlése a PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Virtuális hálózati átjáró törlése a PowerShell (klasszikus) használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. A kívánt típusú új átjáró létrehozásához és a VPN-beállítás befejezéséhez kövesse a [VPN Gateway létrehozása](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#a-namevnetgatewaya4-create-the-vpn-gateway) című témakör lépéseit.

> [!Note]
> Ez a folyamat körülbelül 60 percet vesz igénybe.

## <a name="next-steps"></a>További lépések

* [Azure-beli virtuális gépek közötti kapcsolatok hibaelhárítása](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

