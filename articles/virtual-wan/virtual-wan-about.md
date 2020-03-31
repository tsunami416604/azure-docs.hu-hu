---
title: Az Azure Virtual WAN áttekintése | Microsoft Docs
description: Ismerje meg a Virtual WAN automatizált skálázható ág-ág kapcsolat, a rendelkezésre álló régiók és partnerek.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241415"
---
# <a name="about-azure-virtual-wan"></a>Az Azure Virtual WAN-ról

Az Azure Virtual WAN egy hálózati szolgáltatás, amely optimalizált és automatizált fiókkapcsolatot biztosít az Azure-hoz és azon keresztül. Az Azure-régiók hubokként szolgálnak, amelyekhez összekapcsolhatja az ágakat. Az Azure gerinchálózatának kihasználásával ágakat is csatlakoztathat, és élvezheti az ág-vnet-kapcsolatot. Van egy listánk azokról a partnerekről, amelyek támogatják a kapcsolatautomatizálást az Azure Virtual WAN VPN-nel. További információt a [Virtual WAN-partnerek és helyek](virtual-wan-locations-partners.md) című cikkben talál.

Az Azure Virtual WAN számos Azure-felhőbeli kapcsolódási szolgáltatást egyesít, például a helyek közötti VPN-t, a felhasználói VPN-t (pontról helyre) és az ExpressRoute-ot egyetlen operatív felületbe. Az Azure virtuális hálózatokkal való kapcsolat virtuális hálózati kapcsolatok használatával jön létre. Lehetővé teszi a [globális tranzit hálózati architektúra](virtual-wan-global-transit-network-architecture.md) alapján a klasszikus hub-and-küllős kapcsolat modell, ahol a felhőben üzemeltetett hálózati "hub" lehetővé teszi a tranzitív kapcsolat a végpontok között, amelyek között elosztható a különböző típusú "küllők".

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwan1.png)

Ez a cikk gyors áttekintést nyújt az Azure Virtual WAN hálózati kapcsolatát. A Virtual WAN a következő előnyöket biztosítja:

* **Integrált kapcsolódási megoldások a hubban és a küllőben:** Automatizálhatja a helyek közötti konfigurációt és a helyszíni helyek és az Azure-központ közötti kapcsolatot.
* **Automatikus küllőbeállítás és -konfiguráció:** A virtuális hálózatok és a számítási feladatok zökkenőmentes csatlakoztatása az Azure-elosztóhoz.
* **Intuitív hibaelhárítás:** Megtekintheti a végpontok között az Azure-on belüli folyamatot, majd ezt az információt használhatja a szükséges műveletek megtetézéséhez.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Alapszintű és szabványos virtuális WAN-ek

A virtuális WAN-oknak két típusa van: alapszintű és normál. Az alábbi táblázat az egyes típusokhoz rendelkezésre álló konfigurációkat mutatja be.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

A virtuális WAN frissítésének lépéseit a [Virtuális WAN frissítése alapról standardra című](upgrade-virtual-wan.md)témakörben olvassa el.

## <a name="architecture"></a><a name="architecture"></a>Architektúra

A Virtual WAN architektúráról és a Virtual WAN-ra való áttelepítésről az alábbi cikkekben talál tájékoztatást:

* [Virtuális WAN architektúra](migrate-from-hub-spoke-topology.md)
* [Globális átviteli hálózati architektúra](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-erőforrások

Végpontok közötti virtuális WAN konfigurálásához a következő erőforrásokat kell létrehozni:

* **virtualWAN:** A virtualWAN erőforrás az Azure-hálózat egy virtuális átfedését jelképezi, amely különféle erőforrások gyűjteménye. Hivatkozást tartalmaz az összes virtuális elosztóra, amelyet bele szeretne foglalni a virtuális WAN-hálózatba. A Virtual WAN-erőforrások elkülönülnek egymástól, és nem tartalmazhatnak közös elosztót. A Virtual WAN virtuális hubjai nem kommunikálnak egymással.

* **Elosztó:** A virtuális elosztó egy, a Microsoft által felügyelt virtuális hálózat. A hub különböző szolgáltatásvégpontokat tartalmaz a kapcsolat engedélyezéséhez. A helyszíni hálózatról (vpnsite) csatlakozhat a virtuális hubon belüli VPN-átjáróhoz, összekötheti az ExpressRoute-áramköröket egy virtuális elosztóval, vagy akár mobilfelhasználókat is csatlakoztathat egy pont-hely átjáróhoz a virtuális központban. Az elosztó a hálózat központja egy adott régióban. Azure-régiónként csak egy elosztó létezhet.

  Az elosztó átjárója nem ugyanaz a virtuális hálózati átjáró, amelyet az ExpressRoute és a VPN Gateway esetében használ. A Virtual WAN használata esetén például nem hoz létre helyek közötti kapcsolatot a helyszíni helyről közvetlenül a virtuális hálózatra. Ehelyett létrehoz egy hely-hely kapcsolatot a hubkal. A forgalom minden esetben az elosztó átjáróján halad keresztül. Ez azt jelenti, hogy a virtuális hálózatoknak nincs szükségük saját virtuális hálózati átjáróra. A Virtual WAN segítségével a virtuális hálózatok könnyen méretezhetők a virtuális elosztón és annak átjáróján keresztül.

* **Elosztó virtuális hálózati kapcsolata:** Az elosztó virtuális hálózati kapcsolata erőforrás az elosztó a virtuális hálózathoz való zökkenőmentes kapcsolódását biztosítja.

* **(Preview) Hub-to-Hub kapcsolat** – A hubok egy virtuális WAN-ban kapcsolódnak egymáshoz. Ez azt jelenti, hogy egy helyi hubhoz csatlakoztatott ág, felhasználó vagy virtuális hálózat kommunikálhat egy másik ágkal vagy virtuális hálózattal a csatlakoztatott elosztók teljes hálóarchitektúrájának használatával. Virtuális hálózatok at is csatlakoztathat egy hubon keresztül a virtuális hubon keresztül, valamint a virtuális hálózatok hub on hub használatával hub-to-hub csatlakoztatott keretrendszer.

* **Elosztó útválasztási táblázata:** Létrehozhat egy virtuális elosztási útvonalat, és alkalmazhatja az útvonalat a virtuális elosztó útválasztási táblázatára. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat.

**További Virtuális WAN-erőforrások**

  * **Oldal:** Ez az erőforrás csak helyek közötti kapcsolatokhoz használatos. A hely erőforrás **vpnsite**. A helyszíni VPN-eszközt és annak beállításait jelöli. A Virtual WAN-partnerekkel együttműködve olyan beépített megoldásokhoz fér hozzá, amelyekkel automatikusan exportálhatók ezek az adatok az Azure-ba.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>A kapcsolat típusai

A Virtual WAN a következő típusú kapcsolatokat teszi lehetővé: helyek közötti VPN, Felhasználói VPN (Point-to-Site) és ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Webhelyek közötti VPN-kapcsolatok

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwan.png)

Amikor virtuális WAN-helyek közötti kapcsolatot hoz létre, együttműködhet egy elérhető partnerrel. Ha nem szeretne partnert használni, manuálisan is konfigurálhatja a kapcsolatot. További információt a Helyek közötti [kapcsolat létrehozása a Virtual WAN használatával című témakörben talál.](virtual-wan-site-to-site-portal.md)

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Virtuális WAN-partnermunkafolyamat

Ha virtuális WAN-partnerrel dolgozik, a munkafolyamat a következő:

1. A fiókeszköz (VPN/SDWAN) vezérlő jevessze magát, hogy az Azure egyszerű szolgáltatás használatával webhelyközpontú adatokat exportáljon az [Azure-ba.](../active-directory/develop/howto-create-service-principal-portal.md)
2. Az ágeszköz (VPN/SDWAN) vezérlője kéri le az Azure-kapcsolat konfigurációját, és frissíti a helyi eszközt. Ez automatizálja a konfiguráció letöltését, szerkesztését és frissítését a helyszíni VPN-eszközön.
3. Miután az eszköz a megfelelő Azure-konfigurációval rendelkezik, a helyek közötti kapcsolat (két aktív alagút) felépül az Azure WAN irányába. Az Azure az IKEv1 és az IKEv2 támogatására is képes. A BGP használata nem kötelező.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Partnerek a helyek közötti virtuális WAN-kapcsolatokhoz

Az elérhető partnerek és helyek listáját a [Virtual WAN-partnerek és helyek](virtual-wan-locations-partners.md) című cikkben olvashatja.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Felhasználói VPN-kapcsolatok (pont-hely) kapcsolatok

Az Erőforrásokhoz IPsec/IKE (IKEv2) vagy OpenVPN-kapcsolaton keresztül csatlakozhat az erőforrásokhoz. Az ilyen típusú kapcsolathoz vpn-ügyfél konfigurálásához kell hozzá. További információt a [Pont-hely kapcsolat létrehozása](virtual-wan-point-to-site-portal.md)című témakörben talál.

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-kapcsolatok
Az ExpressRoute lehetővé teszi, hogy a helyszíni hálózatot magánkapcsolaton keresztül csatlakoztassa az Azure-hoz. A kapcsolat létrehozásáról az [ExpressRoute-kapcsolat létrehozása a Virtual WAN használatával](virtual-wan-expressroute-portal.md)című témakörben található.

## <a name="locations"></a><a name="locations"></a>Helyek

A helyekkel kapcsolatos információkért tekintse meg a [Virtual WAN-partnerek és helyek](virtual-wan-locations-partners.md) című cikket.

## <a name="faq"></a><a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>További lépések

[Helyek közötti kapcsolat létrehozása a Virtual WAN használatával](virtual-wan-site-to-site-portal.md)
