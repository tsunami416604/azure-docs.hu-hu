---
title: Az Azure Virtual WAN áttekintése | Microsoft Docs
description: Ismerje meg a virtuális WAN-alapú, automatikusan méretezhető ág-ág kapcsolatot, az elérhető régiókat és a partnereket.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 927c09f61ce0847c72cefb51935116070e956861
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80743108"
---
# <a name="about-azure-virtual-wan"></a>Tudnivalók az Azure Virtual WAN-ról

Az Azure Virtual WAN egy hálózati szolgáltatás, amely számos hálózati, biztonsági és útválasztási funkciót biztosít, így egyetlen operatív felületet biztosíthat. Ezen funkciók közé tartoznak az ág-kapcsolat (a virtuális WAN-partneri eszközöktől, például az SD-WAN vagy a VPN CPE-től származó kapcsolat automatizálásán keresztül), helyek közötti VPN-kapcsolat, a távoli felhasználói VPN (pont – hely) kapcsolat, a magánhálózati (ExpressRoute) kapcsolat, a felhőn belüli kapcsolat (virtuális hálózatok tranzitív kapcsolata), a VPN-ExpressRoute közötti kapcsolat, az Útválasztás, az Azure tűzfal, a privát kapcsolatok titkosítása stb. A virtuális WAN használatának megkezdéséhez nem szükséges az összes ilyen használati eset. Egyszerűen csak egy használati esettel kezdheti meg a hálózatot, és úgy állíthatja be, ahogy az fejlődik. A virtuális WAN-architektúra egy olyan hub és küllős architektúra, amely az ágak (VPN/SD-WAN-eszközök), a felhasználók (az Azure VPN/OpenVPN/IKEv2 ügyfelek), a ExpressRoute-áramkörök és a virtuális hálózatok számára beépített méretezési és teljesítményű. Lehetővé teszi a [globális átviteli hálózati architektúrát](virtual-wan-global-transit-network-architecture.md) , ahol a felhőben üzemeltetett hálózat "hub" lehetővé teszi az olyan végpontok közötti tranzitív kapcsolódást, amelyek különböző típusú "küllők" között terjeszthetők.

Az Azure-régiók olyan hubokként szolgálnak, amelyekhez csatlakozhat. Az összes hub egy szabványos virtuális WAN-kapcsolaton keresztül csatlakozik a teljes hálóhoz, így a felhasználó könnyedén használhatja a Microsoft-gerincet bármilyen (küllős) kapcsolathoz. Az SD-WAN/VPN-eszközökkel való küllős kapcsolat esetén a felhasználók manuálisan is be tudják állítani az Azure Virtual WAN-ban, vagy a virtuális WAN CPE (SD-WAN/VPN) partneri megoldás használatával állíthatják be az Azure-ral való kapcsolódást. A kapcsolat automatizálását támogató partnereink listáját (az eszköz adatainak az Azure-ba való exportálásának, az Azure-konfiguráció letöltésének és a kapcsolat létesítésének lehetőségét) az Azure Virtual WAN használatával találhatja meg. További információkért lásd a [Virtual WAN-partnerek és-helyszínek](virtual-wan-locations-partners.md) című cikket. 

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwan1.png)

Ez a cikk gyors áttekintést nyújt az Azure Virtual WAN hálózati kapcsolatáról. A Virtual WAN a következő előnyöket biztosítja:

* **Integrált csatlakozási megoldások a központban és küllő:** A helyek közötti konfiguráció és a helyszíni helyek és az Azure hub közötti kapcsolat automatizálása.
* **Automatikus küllőbeállítás és -konfiguráció:** A virtuális hálózatok és a számítási feladatok zökkenőmentes csatlakoztatása az Azure-elosztóhoz.
* **Intuitív hibaelhárítás:** Megtekintheti a végpontok közötti folyamatot az Azure-on belül, majd ezekkel az információkkal elvégezheti a szükséges műveleteket.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Alapszintű és standard virtuális WAN

A virtuális WAN-hálózatok két típusa létezik: alapszintű és standard. A következő táblázat az egyes típusok elérhető konfigurációit mutatja be.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

A virtuális WAN verziófrissítésének lépéseiért lásd: [virtuális WAN frissítése alapszintről standard verzióra](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Architektúra

A virtuális WAN architektúrával és a virtuális WAN-ra való áttelepítéssel kapcsolatos információkért tekintse meg a következő cikkeket:

* [Virtuális WAN-architektúra](migrate-from-hub-spoke-topology.md)
* [Globális átviteli hálózati architektúra](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-erőforrások

Végpontok közötti virtuális WAN konfigurálásához a következő erőforrásokat kell létrehozni:

* **virtualWAN:** A virtualWAN erőforrás az Azure-hálózat egy virtuális átfedését jelképezi, amely különféle erőforrások gyűjteménye. Hivatkozást tartalmaz az összes virtuális elosztóra, amelyet bele szeretne foglalni a virtuális WAN-hálózatba. A Virtual WAN-erőforrások elkülönülnek egymástól, és nem tartalmazhatnak közös elosztót. A virtuális WAN-kapcsolaton keresztüli virtuális hubok nem kommunikálnak egymással.

* **Elosztó:** A virtuális elosztó egy, a Microsoft által felügyelt virtuális hálózat. A hub különböző szolgáltatási végpontokat tartalmaz a kapcsolatok engedélyezéséhez. A helyszíni hálózatról (vpnsite) csatlakozhat egy VPN Gateway a virtuális központban, csatlakoztathatja a ExpressRoute-áramköröket egy virtuális központhoz, vagy akár a mobil felhasználókat is csatlakoztathatja egy pont – hely típusú átjáróhoz a virtuális központban. Az elosztó a hálózat központja egy adott régióban. Azure-régiónként csak egy elosztó létezhet.

  Az elosztó átjárója nem ugyanaz a virtuális hálózati átjáró, amelyet az ExpressRoute és a VPN Gateway esetében használ. Ha például virtuális WAN-t használ, akkor nem hoz létre helyek közötti kapcsolatokat a helyszíni helyről közvetlenül a VNet. Ehelyett hozzon létre egy helyek közötti kapcsolódást a hubhoz. A forgalom minden esetben az elosztó átjáróján halad keresztül. Ez azt jelenti, hogy a virtuális hálózatoknak nincs szükségük saját virtuális hálózati átjáróra. A Virtual WAN segítségével a virtuális hálózatok könnyen méretezhetők a virtuális elosztón és annak átjáróján keresztül.

* **Elosztó virtuális hálózati kapcsolata:** Az elosztó virtuális hálózati kapcsolata erőforrás az elosztó a virtuális hálózathoz való zökkenőmentes kapcsolódását biztosítja.

* **(Előzetes verzió) a hub és a hub közötti kapcsolat** – a virtuális WAN-ban mind csatlakoznak egymáshoz. Ez azt jelenti, hogy egy helyi hubhoz csatlakozó ág, felhasználó vagy VNet a csatlakoztatott hubok teljes rácsvonal-architektúrájának használatával kommunikálhat egy másik fiókteleppel vagy VNet. A virtuális hálózatok egy, a virtuális központon keresztül áthaladó hubhoz, valamint az virtuális hálózatok-n keresztül is csatlakoztathatja a hub – hub kapcsolati keretrendszer használatával.

* **Elosztó útválasztási táblázata:** Létrehozhat egy virtuális elosztási útvonalat, és alkalmazhatja az útvonalat a virtuális elosztó útválasztási táblázatára. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat.

**További virtuális WAN-erőforrások**

  * **Webhely:** Ez az erőforrás csak a helyek közötti kapcsolatokhoz használatos. A hely erőforrása **vpnsite**. Ez a helyszíni VPN-eszközt és a hozzá tartozó beállításokat jelenti. A Virtual WAN-partnerekkel együttműködve olyan beépített megoldásokhoz fér hozzá, amelyekkel automatikusan exportálhatók ezek az adatok az Azure-ba.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Csatlakozási típusok

A virtuális WAN a következő típusú kapcsolatokat teszi lehetővé: helyek közötti VPN, felhasználói VPN (pont – hely) és ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Webhelyek közötti VPN-kapcsolatok

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwan.png)

Ha virtuális WAN-helyek közötti kapcsolatot hoz létre, akkor dolgozhat egy elérhető partnerrel. Ha nem szeretne partnert használni, manuálisan is konfigurálhatja a kapcsolatot. További információ: [helyek közötti kapcsolat létrehozása virtuális WAN használatával](virtual-wan-site-to-site-portal.md).

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Virtuális WAN-partner munkafolyamata

Ha virtuális WAN-partnerrel dolgozik, a munkafolyamat a következőket teszi:

1. A fiókirodai (VPN/SDWAN) vezérlőt hitelesítettük a hely-központú információk Azure-ba való exportálásához egy [Azure-szolgáltatásnév](../active-directory/develop/howto-create-service-principal-portal.md)használatával.
2. Az ágeszköz (VPN/SDWAN) vezérlője kéri le az Azure-kapcsolat konfigurációját, és frissíti a helyi eszközt. Ez automatizálja a konfiguráció letöltését, szerkesztését és frissítését a helyszíni VPN-eszközön.
3. Miután az eszköz a megfelelő Azure-konfigurációval rendelkezik, a helyek közötti kapcsolat (két aktív alagút) felépül az Azure WAN irányába. Az Azure az IKEv1 és az IKEv2 támogatására is képes. A BGP használata nem kötelező.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Helyek közötti virtuális WAN-kapcsolatokhoz tartozó partnerek

Az elérhető partnerek és helyszínek listáját a [virtuális WAN-partnerek és-helyszínek](virtual-wan-locations-partners.md) című cikkben találja.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Felhasználói VPN (pont – hely) kapcsolatok

Az Azure-beli erőforrásokhoz IPsec/IKE (IKEv2) vagy OpenVPN-kapcsolat használatával kapcsolódhat. Az ilyen típusú kapcsolathoz a VPN-ügyfél konfigurálására van szükség az ügyfélszámítógépen. További információ: [pont – hely kapcsolat létrehozása](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-kapcsolatok
A ExpressRoute lehetővé teszi a helyszíni hálózat csatlakoztatását az Azure-hoz egy privát kapcsolaton keresztül. A kapcsolat létrehozásával kapcsolatban lásd: [ExpressRoute-kapcsolat létrehozása virtuális WAN használatával](virtual-wan-expressroute-portal.md).

## <a name="locations"></a><a name="locations"></a>Helyek

A tartózkodási helyről a [virtuális WAN-partnerek és-helyek](virtual-wan-locations-partners.md) című cikkben talál további információt.

## <a name="faq"></a><a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>További lépések

[Helyek közötti kapcsolat létrehozása virtuális WAN használatával](virtual-wan-site-to-site-portal.md)
