---
title: "Tervezési és kialakítási létesítmények közötti kapcsolatok: Azure VPN Gateway |} Microsoft Docs"
description: "További tudnivalók a VPN-átjáró tervezése és kialakítása létesítmények közötti, hibrid és VNet – VNet kapcsolatokhoz"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: d5aaab83-4e74-4484-8bf0-cc465811e757
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2017
ms.author: cherylmc
ms.openlocfilehash: 0ebc3ef4a64432e993dd6ed69766bb64544fe433
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="planning-and-design-for-vpn-gateway"></a>A VPN Gateway tervezése és kialakítása

Megtervezéséről és kialakításáról a létesítmények közötti és VNet – VNet konfigurációkkal lehet egyszerű vagy összetett, hálózati igényeitől függően. Ez a cikk bemutatja, hogyan alapvető tervezési és kialakítási szempontjai.

## <a name="planning"></a>Tervezése

### <a name="compare"></a>Létesítmények közötti kapcsolati lehetőségek

Ha szeretné a helyszíni helyek biztonságos kapcsolódás egy virtuális hálózatot, ehhez három különböző módon van:-webhelyek, pont-pont és az ExpressRoute. Hasonlítsa össze a rendelkezésre álló különböző létesítmények közötti kapcsolatokat. A választott lehetőség függőségi viszonyban lehet különböző szempontok, például:

* Mekkora átviteli sebesség szükséges a megoldásához?
* A nyilvános interneten kíván kommunikálni biztonságos VPN-en keresztül, vagy privát kapcsolatot szeretne használni?
* Rendelkezik használható nyilvános IP-címmel?
* Kíván VPN-eszközt használni? Ha igen, ez kompatibilis a rendszerrel?
* Csak néhány számítógépet csatlakoztatna, vagy állandó kapcsolatra van szüksége a helyéhez?
* Milyen típusú VPN-átjáró szükséges az Ön által létrehozni kívánt megoldáshoz?
* Melyik átjárót SKU használja?

### <a name="planningtable"></a>Tervezési táblázat

Az alábbi táblázat segítségével eldöntheti, ajánlott a kapcsolódási beállítást választja, a megoldás.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Munkafolyamat

Az alábbi lista ismerteti a gyakori munkafolyamat a felhő hálózati kapcsolatot:

1. Tervezése és a kapcsolat topológia megtervezése és listázza az összes hálózathoz szeretné csatlakoztatni-címterét.
2. Hozzon létre egy Azure virtuális hálózatra. 
3. A virtuális hálózat VPN-átjáró létrehozásához.
4. Hozzon létre, és konfigurálja a helyszíni hálózatokban vagy más virtuális hálózatok felé (szükség szerint).
5. Létrehozhat és konfigurálhat egy pont – hely kapcsolat az Azure VPN gateway (szükség szerint).

## <a name="design"></a>Tervezési
### <a name="topologies"></a>Kapcsolat topológiák

Indítsa el a diagramok megnézi a [VPN-átjáró](vpn-gateway-about-vpngateways.md) cikk. Egyszerű diagramokat, az üzembe helyezési modellel minden topológia, és az elérhető eszközök segítségével telepítheti a configuration tartalmaz.

### <a name="designbasics"></a>Tervezési alapjai

Az alábbi szakaszok ismertetik a VPN-átjáró alapjait. 

#### <a name="servicelimits"></a>Hálózatkezelési szolgáltatások korlátok

A táblák görgetéséhez [hálózati szolgáltatási korlátok](../azure-subscription-service-limits.md#networking-limits). A felsorolt korlátai hatással lehet a tervező.

#### <a name="subnets"></a>Alhálózatok kapcsolatos

Kapcsolatok létrehozásakor meg kell fontolnia alhálózati tartományt. Nem lehet átfedésben lévő alhálózati címtartományt. Egy átfedő alhálózattal akkor, ha egy virtuális hálózat vagy a helyszíni hely tartalmazza ugyanazt a címtartományt, amely tartalmazza a másik helyre. Ez azt jelenti, hogy kell-e a hálózati szakemberek a helyi a helyszíni hálózatokhoz ki egy tartományt az Azure IP-cím használatára carve terület/alhálózatok címzést. Címterületek használatát, amelyek nem használják a helyszíni helyi hálózaton van szüksége.

Egymást átfedő alhálózatokat elkerülve az során is fontos dolgozik VNet – VNet kapcsolatokhoz. Ha az alhálózat átfedésben vannak, és egy IP-cím szerepel a küldő és a célmappa Vnetek, VNet – VNet kapcsolatokhoz sikertelen. Azure nem átirányíthatja az adatokat a virtuális hálózat, mert a cél címe a küldő virtuális hálózat része.

VPN-átjárók nevű egy átjáró-alhálózatot a megadott alhálózat szükséges. A megfelelő működéshez az összes átjáró-alhálózatnak a GatewaySubnet névvel kell rendelkeznie. Ne felejtse el nem nevet az átjáró alhálózatának egy másik nevet, és nem telepítendő virtuális gépek vagy bármi más az átjáró alhálózatának. Lásd: [átjáró alhálózatok](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Tudnivalók a helyi hálózati átjáró

A helyi hálózati átjáró általában a helyszínt jelenti. A klasszikus üzembe helyezési modellel a helyi hálózati átjáró nevezzük helyi hálózati helyet. A helyi hálózati átjáró konfigurálásakor ehhez adjon neki egy nevet, adja meg a helyszíni VPN-eszköz nyilvános IP-címét, és a címelőtagokat, amely a helyszíni a helyen. Azure ellenőrzi, hogy a hálózati forgalom cél címelőtagokat, a megadott konfigurációs tekint helyi hálózati átjáró, és ennek megfelelően irányítja a csomagokat. A címelőtagokat igény szerint módosíthatók. További információkért lásd: [helyi hálózati átjárók](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>Átjáró típusával kapcsolatban

Jelölje ki a megfelelő átjáró a topológia fontos. Ha a megfelelő típusú választja, az átjáró nem fog megfelelően működni. Az átjáró típusa az átjáró kapcsolódási módját adja meg, és az Erőforrás-kezelő üzembe helyezési modelljének kötelező konfigurációs beállításai közé tartozik.

Az átjáró típusok a következők:

* Vpn
* ExpressRoute

#### <a name="connectiontype"></a>Kapcsolattípusok kapcsolatos

Minden egyes konfiguráció esetében egy adott kapcsolattípusra van szükség. A csatlakozási típusok a következők:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Információ a VPN-típusai

Minden egyes konfiguráció szükséges egy adott VPN-típus. Kétféle konfiguráció kombinálásakor (például Helyek közötti és Pont–hely kapcsolat létesítésekor ugyanazon a virtuális hálózaton) olyan VPN-típust kell használni, amely mindkét kapcsolat követelményeit kielégíti.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Az alábbi táblázatok bemutatják a VPN-típus, akkor minden kapcsolat konfigurációs van leképezve. Győződjön meg arról, hogy az átjáró a VPN-típus megfelel a létrehozni kívánt konfigurációs. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>A pont-pont kapcsolatok VPN-eszközök

A pont-pont kapcsolat létrehozásakor, függetlenül a telepítési modell konfigurálásához a következőkre van szükség:

* A VPN-eszköz, amely kompatibilis a Azure VPN gatewayek
* Egy nyilvánosan elérhető IPv4 IP-címet, amely nincs NAT mögött.

Felhasználói élmény beállítása a VPN-eszköz, vagy valaki meg az eszköz által konfigurált kell.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Fontolja meg kényszerített bújtatás Útválasztás

A legtöbb konfigurációk esetén konfigurálhatja a kényszerített bújtatást. A kényszerített bújtatás lehetővé teszi az átirányítási vagy "kényszerített" minden internetre irányuló forgalomnak biztonsági másolatot a helyszíni helyre vizsgálati és naplózási pont-pont VPN-alagúton keresztül. Ez az kritikus fontosságú biztonsági előfeltétele annak, hogy a legtöbb vállalati informatikai házirendek. 

Kényszerített bújtatás nélkül internetre irányuló forgalomnak a virtuális gépek Azure-ban lesz mindig haladnak át Azure hálózati infrastruktúráról közvetlenül kimenő csatlakozik az internethez, a beállítás lehetővé teszi vizsgálja meg, vagy a forgalom naplózása nélkül. Jogosulatlan Internet-hozzáférés is eredményezhet, információfelfedés vagy más típusú biztonsági problémákat.

A kényszerített bújtatás kapcsolat két üzembe helyezési modell és a különböző eszközök használatával konfigurálható. További információkért lásd: [kényszerített bújtatás konfigurálása](vpn-gateway-forced-tunneling-rm.md).

**A kényszerített bújtatás diagramja**

![Az Azure VPN Gateway kényszerített bújtatás diagramja](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md) és [VPN-átjáró](vpn-gateway-about-vpngateways.md) cikkek segítséget nyújtanak a tervező az további információt.

Adott átjáró beállításaival kapcsolatos további információkért lásd: [kapcsolatos VPN-átjáró beállítások](vpn-gateway-about-vpn-gateway-settings.md).