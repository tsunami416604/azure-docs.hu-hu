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
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77056342"
---
# <a name="about-azure-virtual-wan"></a>Tudnivalók az Azure Virtual WAN-ról

Az Azure Virtual WAN egy hálózati szolgáltatás, amely optimalizált és automatizált ág-kapcsolatot biztosít az Azure-hoz és a-n keresztül. Az Azure-régiók olyan hubok, amelyekhez az ágakat összekapcsolhatjuk. Az Azure-gerinc kihasználható az ágak összekapcsolásához és a VNet közötti kapcsolathoz is. Az Azure Virtual WAN VPN-kapcsolaton keresztüli automatizálást támogató partnereink listája. További információkért lásd a [Virtual WAN-partnerek és-helyszínek](virtual-wan-locations-partners.md) című cikket.

Az Azure Virtual WAN számos Azure Cloud connectivity-szolgáltatást kínál, többek között a helyek közötti VPN-t, a felhasználói VPN-t (pont – hely), a ExpressRoute pedig egyetlen operatív felületre. Az Azure virtuális hálózatok-hez való kapcsolódás virtuális hálózati kapcsolatok használatával történik. Lehetővé teszi a [globális átviteli hálózati architektúrát](virtual-wan-global-transit-network-architecture.md) egy olyan klasszikus sugaras kapcsolati modell alapján, ahol a felhőben üzemeltetett hálózat lehetővé teszi az olyan végpontok közötti tranzitív kapcsolódást, amelyek különböző típusú "küllők" között lehetnek elosztva.

![A Virtual WAN ábrája](./media/virtual-wan-about/virtualwan1.png)

Ez a cikk gyors áttekintést nyújt az Azure Virtual WAN hálózati kapcsolatáról. A Virtual WAN a következő előnyöket biztosítja:

* **Integrált csatlakozási megoldások a központban és küllő:** A helyek közötti konfiguráció és a helyszíni helyek és az Azure hub közötti kapcsolat automatizálása.
* **Automatikus küllőbeállítás és -konfiguráció:** A virtuális hálózatok és a számítási feladatok zökkenőmentes csatlakoztatása az Azure-elosztóhoz.
* **Intuitív hibaelhárítás:** Megtekintheti a végpontok közötti folyamatot az Azure-on belül, majd ezekkel az információkkal elvégezheti a szükséges műveleteket.

## <a name="basicstandard"></a>Alapszintű és standard virtuális WAN

A virtuális WAN-hálózatok két típusa létezik: alapszintű és standard. A következő táblázat az egyes típusok elérhető konfigurációit mutatja be.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

A virtuális WAN verziófrissítésének lépéseiért lásd: [virtuális WAN frissítése alapszintről standard verzióra](upgrade-virtual-wan.md).

## <a name="architecture"></a>Architektúra

A virtuális WAN architektúrával és a virtuális WAN-ra való áttelepítéssel kapcsolatos információkért tekintse meg a következő cikkeket:

* [Virtuális WAN-architektúra](migrate-from-hub-spoke-topology.md)
* [Globális átviteli hálózati architektúra](virtual-wan-global-transit-network-architecture.md)

## <a name="resources"></a>Virtual WAN-erőforrások

Végpontok közötti virtuális WAN konfigurálásához a következő erőforrásokat kell létrehozni:

* **virtualWAN:** A virtualWAN erőforrás az Azure-hálózat egy virtuális átfedését jelképezi, amely különféle erőforrások gyűjteménye. Hivatkozást tartalmaz az összes virtuális elosztóra, amelyet bele szeretne foglalni a virtuális WAN-hálózatba. A Virtual WAN-erőforrások elkülönülnek egymástól, és nem tartalmazhatnak közös elosztót. A virtuális WAN-kapcsolaton keresztüli virtuális hubok nem kommunikálnak egymással.

* **Elosztó:** A virtuális elosztó egy, a Microsoft által felügyelt virtuális hálózat. A hub különböző szolgáltatási végpontokat tartalmaz a kapcsolatok engedélyezéséhez. A helyszíni hálózatról (vpnsite) csatlakozhat egy VPN Gateway a virtuális központban, csatlakoztathatja a ExpressRoute-áramköröket egy virtuális központhoz, vagy akár a mobil felhasználókat is csatlakoztathatja egy pont – hely típusú átjáróhoz a virtuális központban. Az elosztó a hálózat központja egy adott régióban. Azure-régiónként csak egy elosztó létezhet.

  Az elosztó átjárója nem ugyanaz a virtuális hálózati átjáró, amelyet az ExpressRoute és a VPN Gateway esetében használ. Ha például virtuális WAN-t használ, akkor nem hoz létre helyek közötti kapcsolatokat a helyszíni helyről közvetlenül a VNet. Ehelyett hozzon létre egy helyek közötti kapcsolódást a hubhoz. A forgalom minden esetben az elosztó átjáróján halad keresztül. Ez azt jelenti, hogy a virtuális hálózatoknak nincs szükségük saját virtuális hálózati átjáróra. A Virtual WAN segítségével a virtuális hálózatok könnyen méretezhetők a virtuális elosztón és annak átjáróján keresztül.

* **Elosztó virtuális hálózati kapcsolata:** Az elosztó virtuális hálózati kapcsolata erőforrás az elosztó a virtuális hálózathoz való zökkenőmentes kapcsolódását biztosítja.

* **(Előzetes verzió) a hub és a hub közötti kapcsolat** – a virtuális WAN-ban mind csatlakoznak egymáshoz. Ez azt jelenti, hogy egy helyi hubhoz csatlakozó ág, felhasználó vagy VNet a csatlakoztatott hubok teljes rácsvonal-architektúrájának használatával kommunikálhat egy másik fiókteleppel vagy VNet. A virtuális hálózatok egy, a virtuális központon keresztül áthaladó hubhoz, valamint az virtuális hálózatok-n keresztül is csatlakoztathatja a hub – hub kapcsolati keretrendszer használatával.

* **Elosztó útválasztási táblázata:** Létrehozhat egy virtuális elosztási útvonalat, és alkalmazhatja az útvonalat a virtuális elosztó útválasztási táblázatára. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat.

**További virtuális WAN-erőforrások**

  * **Webhely:** Ez az erőforrás csak a helyek közötti kapcsolatokhoz használatos. A hely erőforrása **vpnsite**. Ez a helyszíni VPN-eszközt és a hozzá tartozó beállításokat jelenti. A Virtual WAN-partnerekkel együttműködve olyan beépített megoldásokhoz fér hozzá, amelyekkel automatikusan exportálhatók ezek az adatok az Azure-ba.

## <a name="connectivity"></a>Csatlakozási típusok

A virtuális WAN a következő típusú kapcsolatokat teszi lehetővé: helyek közötti VPN, felhasználói VPN (pont – hely) és ExpressRoute.

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

### <a name="uservpn"></a>Felhasználói VPN (pont – hely) kapcsolatok

Az Azure-beli erőforrásokhoz IPsec/IKE (IKEv2) vagy OpenVPN-kapcsolat használatával kapcsolódhat. Az ilyen típusú kapcsolathoz a VPN-ügyfél konfigurálására van szükség az ügyfélszámítógépen. További információ: [pont – hely kapcsolat létrehozása](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>ExpressRoute-kapcsolatok
A ExpressRoute lehetővé teszi a helyszíni hálózat csatlakoztatását az Azure-hoz egy privát kapcsolaton keresztül. A kapcsolat létrehozásával kapcsolatban lásd: [ExpressRoute-kapcsolat létrehozása virtuális WAN használatával](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Helyek

A tartózkodási helyről a [virtuális WAN-partnerek és-helyek](virtual-wan-locations-partners.md) című cikkben talál további információt.

## <a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Következő lépések

[Helyek közötti kapcsolat létrehozása virtuális WAN használatával](virtual-wan-site-to-site-portal.md)
