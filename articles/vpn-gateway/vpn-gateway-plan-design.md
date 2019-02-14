---
title: 'Tervezési és kialakítási létesítmények közötti kapcsolatok: Azure VPN Gateway| Microsoft Docs'
description: További tudnivalók a VPN Gateway tervezése és kialakítása cross-premises, hibrid és VNet – VNet kapcsolatokhoz
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/27/2017
ms.author: yushwang
ms.openlocfilehash: 7802061ba09a30ca34ed3804ace846118c5edb9b
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235363"
---
# <a name="planning-and-design-for-vpn-gateway"></a>A VPN Gateway tervezése és kialakítása

Tervezésekor és kialakításakor a létesítmények közötti és VNet – VNet konfigurációkkal lehet egyszerű vagy összetett, hálózati igényeitől függően. Ez a cikk végigvezeti alapvető tervezési és kialakítási szempontokat.

## <a name="planning"></a>Tervezése

### <a name="compare"></a>Létesítmények közötti kapcsolati lehetőségek

Ha szeretné a helyszíni webhelyek biztonságos csatlakoztatása virtuális hálózathoz, akkor ehhez három különböző módon: Site-to-Site, pont – hely és az ExpressRoute. Hasonlítsa össze a rendelkezésre álló különböző létesítmények közötti kapcsolatok. A kiválasztott lehetőség például függ különféle szempontok:

* Mekkora átviteli sebesség szükséges a megoldásához?
* A nyilvános interneten kíván kommunikálni biztonságos VPN-en keresztül, vagy privát kapcsolatot szeretne használni?
* Rendelkezik használható nyilvános IP-címmel?
* Kíván VPN-eszközt használni? Ha igen, ez kompatibilis a rendszerrel?
* Csak néhány számítógépet csatlakoztatna, vagy állandó kapcsolatra van szüksége a helyéhez?
* Milyen típusú VPN-átjáró szükséges az Ön által létrehozni kívánt megoldáshoz?
* Melyik átjáró-Termékváltozatot kell használni?

### <a name="planningtable"></a>Tervezési táblázat

Az alábbi táblázat segíthet eldönteni, melyik az Ön megoldásához legmegfelelőbb kapcsolat.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>A munkafolyamat

Az alábbi lista a felhővel általános munkafolyamatát ismerteti:

1. Tervezési és a kapcsolati topológia megtervezése és listázza az összes hálózat, amelyhez csatlakozni-címterét.
2. Hozzon létre egy Azure virtuális hálózatra. 
3. A virtuális hálózat VPN-átjáró létrehozása.
4. Hozzon létre, és a helyszíni hálózatok vagy más virtuális hálózatokhoz való csatlakozásának konfigurálása, (igény szerint).
5. Hozzon létre, és (igény szerint) az Azure VPN Gateway pont – hely kapcsolat konfigurálása.

## <a name="design"></a>Tervezés
### <a name="topologies"></a>Kapcsolódási topológiák

Megnézzük a diagramokat az első lépésként a [információk a VPN Gateway](vpn-gateway-about-vpngateways.md) cikk. A cikk az alapszintű diagramokat, minden egyes topológiát, és az elérhető üzembe helyezési eszközök segítségével a konfiguráció üzembe helyezése az üzembe helyezési modellel tartalmaz.

### <a name="designbasics"></a>Alapvető tervezési tudnivalók

A következő részekben bemutatjuk a VPN gateway alapjait. 

#### <a name="servicelimits"></a>Hálózati szolgáltatások korlátai

A táblák megtekintéséhez végiggörgeti [hálózati szolgáltatási korlátok](../azure-subscription-service-limits.md#networking-limits). A felsorolt korlátok befolyásolhatják a tervezés.

#### <a name="subnets"></a>Alhálózatok kapcsolatban

Amikor kapcsolatokat hoz létre, meg kell fontolnia az alhálózat-címtartományok. Alhálózati címtartomány átfedő rendelkezhet. Egy átfedő alhálózattal van, amikor egy virtuális hálózat vagy a helyszíni hely tartalmazza ugyanazt a címtartományt, amely tartalmazza a másik helyre. Ez azt jelenti, hogy kell-e a hálózati mérnökök hogy különítsen el egy kell használni az Azure IP-címtartományt a helyszíni hálózatok címzés terület/alhálózatokat. Nem használja a helyi hálózat a címtér van szüksége.

Kerülje az átfedésben lévő alhálózatok akkor is fontos, ha dolgozik a VNet – VNet kapcsolatokhoz. Ha az alhálózat átfedésben vannak, és a egy IP-cím létezik a küldő és a cél virtuális hálózatok, virtuális hálózatok közötti kapcsolatok sikertelen. Az Azure nem továbbíthatják az adatokat a virtuális hálózathoz, mert a cél címe a küldő virtuális hálózat része.

VPN-átjárókhoz szükséges egy átjáróalhálózat nevű alhálózatot. A megfelelő működéshez az összes átjáró-alhálózatnak a GatewaySubnet névvel kell rendelkeznie. Győződjön meg arról, hogy nem egy másik nevet az átjáróalhálózat neve, és ne helyezzen üzembe virtuális gépeket, vagy bármi más, az átjáró-alhálózat. Lásd: [Átjáróalhálózatok](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Helyi hálózati átjárókkal kapcsolatos

A helyi hálózati átjáró általában a helyszínt jelenti. A klasszikus üzemi modellben a helyi hálózati átjáró neve egy helyi hálózati telephelyhez. A helyi hálózati átjáró konfigurálásakor, adjon meg egy nevet, adja meg a helyszíni VPN-eszköz nyilvános IP-címét és a helyszínen található címelőtagok. Azure a cél-címelőtagokat, a hálózati forgalmat megvizsgál, consults szintűre frissül az Ön által megadott konfigurációt a helyi hálózati átjáró, és ennek megfelelően irányítja a csomagokat. A címelőtagok szükség szerint módosíthatja. További információkért lásd: [helyi hálózati átjárók](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>Információk az átjárótípusokról

A topológia a megfelelő átjárótípus kiválasztása, kritikus fontosságú. Ha a megfelelő típusú választja, az átjáró nem fog megfelelően működni. Az átjáró típusa az átjáró kapcsolódási módját adja meg, és az Erőforrás-kezelő üzembe helyezési modelljének kötelező konfigurációs beállításai közé tartozik.

Az átjáró-típusok a következők:

* Vpn
* ExpressRoute

#### <a name="connectiontype"></a>Kapcsolattípusok kapcsolatban

Minden egyes konfiguráció esetében egy adott kapcsolattípusra van szükség. A kapcsolattípusok közül a következők:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Tudnivalók a VPN-típusok

Az egyes konfigurációkhoz egy adott VPN-típus szükséges. Kétféle konfiguráció kombinálásakor (például Helyek közötti és Pont–hely kapcsolat létesítésekor ugyanazon a virtuális hálózaton) olyan VPN-típust kell használni, amely mindkét kapcsolat követelményeit kielégíti.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Az alábbi táblázatok bemutatják a VPN-típust, mivel mindegyik kapcsolat konfigurációja vannak leképezve. Ellenőrizze, hogy az átjáró VPN-típust megegyezik a létrehozni kívánt beállításait. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Site-to-Site-kapcsolatok VPN-eszközök

Helyek közötti kapcsolat, függetlenül üzembe helyezési modellben konfigurálása a következőkre van szükség:

* Az Azure VPN-átjárókkal kompatibilis VPN-eszköz
* Egy nyilvános IPv4 IP-címet, amely nem NAT mögött

A VPN-eszköz konfigurálása felhasználói élményt, vagy rendelkezik valaki az Ön számára az eszközök konfigurálása kell.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Fontolja meg kényszerített bújtatásos Útválasztás

A legtöbb konfiguráció esetében is kényszerített bújtatást konfigurál. Kényszerített bújtatás lehetővé teszi az átirányítási vagy "kényszerített" internetre irányuló összes forgalmat a helyszíni helyre biztonsági és vizsgálati és naplózási Site-to-Site VPN-alagúton keresztül. Ez a legtöbb vállalati informatikai kritikus fontosságú biztonsági követelményeket a szabályzatokat. 

Anélkül, hogy kényszerített bújtatást végez, internetre irányuló forgalmat a virtuális gépekről az Azure-ban fog mindig haladnak át az Azure hálózati infrastruktúráról közvetlenül meg az internethez, ezáltal lehetővé teszi a forgalmat, vagy vizsgálja meg a beállítás nélkül. Jogosulatlan Internet-hozzáférés potenciálisan vezethet információfelfedés vagy más biztonsági résekkel szemben.

A kényszerített bújtatás kapcsolat konfigurálható mindkét üzemi modellben és a különböző eszközök használatával. További információkért lásd: [kényszerített bújtatás konfigurálása](vpn-gateway-forced-tunneling-rm.md).

**Kényszerített bújtatás diagramja**

![Az Azure VPN Gateway kényszerített bújtatás diagramja](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>További lépések

Tekintse meg a [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md) és [információk a VPN Gateway](vpn-gateway-about-vpngateways.md) cikkek segítséget nyújt a tervezés további információt.

Adott gateway beállításaival kapcsolatos további információkért lásd: [tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md).
