---
title: Az Azure Virtual WAN áttekintése | Microsoft Docs
description: Ismerje meg a virtuális WAN-alapú, automatikusan méretezhető ág-ág kapcsolatot, az elérhető régiókat és a partnereket.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/22/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: f1576e963f9c25821b5e3f57907662e3d86df4e0
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406345"
---
# <a name="what-is-azure-virtual-wan"></a>Mi az Azure Virtual WAN?

Az Azure Virtual WAN egy hálózati szolgáltatás, amely optimalizált és automatizált ág-kapcsolatot biztosít az Azure-hoz és a-n keresztül. Az Azure-régiók olyan hubok, amelyekhez az ágakat összekapcsolhatjuk. Az Azure-gerinc kihasználható az ágak összekapcsolásához és a VNet közötti kapcsolathoz is. Az Azure Virtual WAN VPN-kapcsolaton keresztüli automatizálást támogató partnereink listája. További információkért lásd a [Virtual WAN-partnerek és-helyszínek](virtual-wan-locations-partners.md) című cikket.

Az Azure Virtual WAN számos Azure Cloud connectivity-szolgáltatást (például helyek közötti VPN-t és ExpressRoute) egyesít egyetlen operatív felületen. Az Azure virtuális hálózatok-hez való kapcsolódás virtuális hálózati kapcsolatok használatával történik.

A Virtual WAN ExpressRoute jelenleg előzetes verzióban érhető el.

![A Virtual WAN ábrája](./media/virtual-wan-about/virtualwan1.png)

Ez a cikk gyors áttekintést nyújt az Azure Virtual WAN hálózati kapcsolatáról. A Virtual WAN a következő előnyöket biztosítja:

* **Integrált csatlakozási megoldások a központban és küllő:** A helyek közötti konfiguráció és a helyszíni helyek és az Azure hub közötti kapcsolat automatizálása.
* **Automatizált küllős telepítés és konfigurálás:** Zökkenőmentesen csatlakoztathatja virtuális hálózatait és munkaterheléseit az Azure hub-hoz.
* **Intuitív hibaelhárítás:** Megtekintheti a végpontok közötti folyamatot az Azure-on belül, majd ezekkel az információkkal elvégezheti a szükséges műveleteket.

## <a name="resources"></a>Virtual WAN-erőforrások

A végpontok közötti virtuális WAN konfigurálásához a következő erőforrásokat kell létrehoznia:

* **virtualWAN:** A virtualWAN-erőforrás az Azure-hálózat virtuális átfedését jelenti, és több erőforrás gyűjteménye. Hivatkozást tartalmaz az összes virtuális elosztóra, amelyet bele szeretne foglalni a virtuális WAN-hálózatba. A Virtual WAN-erőforrások elkülönülnek egymástól, és nem tartalmazhatnak közös elosztót. A Virtual WAN virtuális elosztói nem kommunikálnak egymással. Az "ágak közötti forgalom engedélyezése" tulajdonság lehetővé teszi a VPN-helyek és a VPN közötti forgalmat a ExpressRoute (jelenleg előzetes verzióban) engedélyezett helyek között.

* **Hub:** A virtuális központ egy Microsoft által felügyelt virtuális hálózat. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Az elosztó a hálózat központja egy adott régióban. Azure-régiónként csak egy elosztó létezhet. Amikor az Azure Portalon létrehoz egy elosztót, ezzel létrehoz egy hozzá tartozó virtuális hálózatot és egy VPN Gateway-átjárót is.

  Az elosztó átjárója nem ugyanaz a virtuális hálózati átjáró, amelyet az ExpressRoute és a VPN Gateway esetében használ. Ha például virtuális WAN-t használ, akkor nem hoz létre helyek közötti kapcsolatokat a helyszíni helyről közvetlenül a VNet. Ehelyett hozzon létre egy helyek közötti kapcsolódást a hubhoz. A forgalom minden esetben az elosztó átjáróján halad keresztül. Ez azt jelenti, hogy a virtuális hálózatoknak nincs szükségük saját virtuális hálózati átjáróra. A Virtual WAN segítségével a virtuális hálózatok könnyen méretezhetők a virtuális elosztón és annak átjáróján keresztül.

* **Hub virtuális hálózati kapcsolatai:** A hub virtuális hálózati kapcsolati erőforrás a hub zökkenőmentes csatlakoztatására szolgál a virtuális hálózathoz. Jelenleg csak olyan virtuális hálózatokhoz kapcsolódhat, amelyekkel egy elosztórégióban van.

* **Központ útválasztási táblázata:**  Létrehozhatja a virtuális központ útvonalát, és alkalmazhatja az útvonalat a virtuális központ útválasztási táblájára. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat.

**További virtuális WAN-erőforrások**

  * **Hely** Ez az erőforrás csak a helyek közötti kapcsolatokhoz használatos. A hely erőforrása **vpnsite**. Ez a helyszíni VPN-eszközt és a hozzá tartozó beállításokat jelenti. A Virtual WAN-partnerekkel együttműködve olyan beépített megoldásokhoz fér hozzá, amelyekkel automatikusan exportálhatók ezek az adatok az Azure-ba.

## <a name="connectivity"></a>Connectivity

A virtuális WAN két típusú kapcsolatot tesz lehetővé: Helyek közötti és ExpressRoute (előzetes verzió).

### <a name="s2s"></a>Helyek közötti VPN-kapcsolatok

![A Virtual WAN ábrája](./media/virtual-wan-about/virtualwan.png)

Ha virtuális WAN-helyek közötti kapcsolatot hoz létre, akkor dolgozhat egy elérhető partnerrel. Ha nem szeretne partnert használni, manuálisan is konfigurálhatja a kapcsolatot. További információ: [helyek közötti kapcsolat létrehozása virtuális WAN használatával](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Virtuális WAN-partner munkafolyamata

Ha virtuális WAN-partnerrel dolgozik, a munkafolyamat a következőket teszi:

1. Az ágeszköz (VPN/SDWAN) vezérlője hitelesítve van, hogy a helyközpontú adatokat az Azure-ba egy [Azure-szolgáltatásnéven](../active-directory/develop/howto-create-service-principal-portal.md) keresztül exportálja.
2. Az ágeszköz (VPN/SDWAN) vezérlője kéri le az Azure-kapcsolat konfigurációját, és frissíti a helyi eszközt. Ez automatizálja a konfiguráció letöltését, szerkesztését és frissítését a helyszíni VPN-eszközön.
3. Miután az eszköz a megfelelő Azure-konfigurációval rendelkezik, a helyek közötti kapcsolat (két aktív alagút) felépül az Azure WAN irányába. Az Azure az IKEv1 és az IKEv2 támogatására is képes. A BGP használata nem kötelező.

#### <a name="partners"></a>Helyek közötti virtuális WAN-kapcsolatokhoz tartozó partnerek

Az elérhető partnerek és helyszínek listáját a [virtuális WAN-partnerek és-helyszínek](virtual-wan-locations-partners.md) című cikkben találja.


### <a name="er"></a>ExpressRoute-kapcsolatok (előzetes verzió)

A ExpressRoute lehetővé teszi a helyszíni hálózat csatlakoztatását az Azure-hoz egy privát kapcsolaton keresztül. A kapcsolat létrehozásával kapcsolatban lásd: [ExpressRoute-kapcsolat létrehozása virtuális WAN használatával](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Helyek

A tartózkodási helyről a [virtuális WAN-partnerek és-helyek](virtual-wan-locations-partners.md) című cikkben talál további információt.

## <a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>További lépések

[Helyek közötti kapcsolat létrehozása virtuális WAN használatával](virtual-wan-site-to-site-portal.md)
