---
title: Az Azure Virtual WAN áttekintése | Microsoft Docs
description: Ismerje meg a virtuális WAN automatikus méretezhető ág ágba irányuló kapcsolatot, az elérhető régiók és partnerekkel.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 6f2f002c5ff08e21741927d07a0facfd09ec0914
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295691"
---
# <a name="what-is-azure-virtual-wan"></a>Mi az Azure Virtual WAN?

Az Azure virtuális WAN egy hálózati szolgáltatás, amely optimalizált és automatizált ág kapcsolat, és az Azure-on keresztül. Azure-régióban, amely akkor is csatlakozhat az elágazásokhoz a hubs szolgál. Az ágak a csatlakozás után az Azure gerinchálózatra ág közötti és a fiókiroda ágba irányuló kapcsolat létrehozásához használhatja. Partnerek és virtuális WAN VPN támogató helyek listáját lásd: a [virtuális WAN-partnerek és helyek](virtual-wan-locations-partners.md) cikk.

Az Azure virtuális WAN egyesíti az Azure-felhőben számos szolgáltatás például site-to-site VPN (általánosan elérhető), az ExpressRoute (előzetes verzió), pont – hely felhasználói (előzetes verzió) VPN egyetlen operatív felületen. Az Azure-alapú virtuális hálózatokhoz van kapcsolódni a virtuális hálózati kapcsolatokon keresztül.

![A Virtual WAN ábrája](./media/virtual-wan-about/vwangraphic.png)

Ez a cikk a hálózati kapcsolatot az Azure virtuális WAN gyors betekintést biztosít. A Virtual WAN a következő előnyöket biztosítja:

* **Küllős topológiájú integrált kapcsolat megoldások:** Automatizálhatja a site-to-site konfiguráció és a helyszíni hely és a egy Azure-központ közötti kapcsolat.
* **Automatizált küllő beállítás és konfiguráció:** A virtuális hálózatok és a számítási feladatok Azure hubon zökkenőmentesen kapcsolódik.
* **Intuitív hibaelhárítási:** Tekintse meg a teljes körű folyamatot az Azure-ban, és ezen információk használatával szükséges műveleteket.

## <a name="resources"></a>Virtual WAN-erőforrások

Egy teljes körű virtuális WAN konfigurálásához hoz létre az alábbi forrásanyagokat:

* **virtualWAN:** A virtualWAN erőforrás jelöli az Azure-hálózat egy virtuális területre, és több erőforrások gyűjteménye. Hivatkozást tartalmaz az összes virtuális elosztóra, amelyet bele szeretne foglalni a virtuális WAN-hálózatba. A Virtual WAN-erőforrások elkülönülnek egymástól, és nem tartalmazhatnak közös elosztót. A Virtual WAN virtuális elosztói nem kommunikálnak egymással. A "Ág a fiókiroda forgalom engedélyezése" tulajdonság lehetővé teszi, hogy a helyek közötti VPN-helyek, valamint az expressroute-hoz (jelenleg előzetes verzióban elérhető) VPN forgalom engedélyezve.

* **Hub:** Egy virtuális központtal egy Microsoft által felügyelt virtuális hálózatot. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Az elosztó a hálózat központja egy adott régióban. Azure-régiónként csak egy elosztó létezhet. Amikor az Azure Portalon létrehoz egy elosztót, ezzel létrehoz egy hozzá tartozó virtuális hálózatot és egy VPN Gateway-átjárót is.

  Az elosztó átjárója nem ugyanaz a virtuális hálózati átjáró, amelyet az ExpressRoute és a VPN Gateway esetében használ. Például használatakor virtuális WAN, ne hozzon létre egy helyek közötti kapcsolat a helyszíni helyről közvetlenül a virtuális hálózat. Ehelyett hozzon létre egy helyek közötti kapcsolat a hubhoz. A forgalom minden esetben az elosztó átjáróján halad keresztül. Ez azt jelenti, hogy a virtuális hálózatoknak nincs szükségük saját virtuális hálózati átjáróra. A Virtual WAN segítségével a virtuális hálózatok könnyen méretezhetők a virtuális elosztón és annak átjáróján keresztül.

* **Eseményközpont-kapcsolat virtuális hálózat:** A Hub virtuális hálózati kapcsolati erőforrás szolgál a hub zökkenőmentesen kapcsolódik a virtuális hálózathoz. Jelenleg csak olyan virtuális hálózatokhoz kapcsolódhat, amelyekkel egy elosztórégióban van.

* **Hub útválasztási táblázatot:**  Hozzon létre egy virtuális központ útvonalat, és a virtuális központ útválasztási táblázatot az útvonal vonatkozik. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat.

**Virtuális WAN további források**

  * **Hely:** Ez az erőforrás csak a helyek közötti kapcsolatok szolgál. A hely erőforrás **vpnsite**. Azt jelöli, a helyszíni VPN-eszköz és annak beállításait. A Virtual WAN-partnerekkel együttműködve olyan beépített megoldásokhoz fér hozzá, amelyekkel automatikusan exportálhatók ezek az adatok az Azure-ba.

## <a name="connectivity"></a>Kapcsolat

Virtuális WAN lehetővé teszi, hogy a kapcsolat három típusa: site-to-site, pont – hely (előzetes verzió), és az ExpressRoute (előzetes verzió).

### <a name="s2s"></a>Site-to-site VPN-kapcsolatok

![A Virtual WAN ábrája](./media/virtual-wan-about/virtualwan.png)

Virtuális WAN helyek közötti kapcsolat létrehozásakor rendelkezésre álló partner használhatja. Ha nem szeretne egy partnert, manuálisan konfigurálhatja a kapcsolat. További információkért lásd: [hozzon létre egy helyek közötti kapcsolattal virtuális WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Virtuális WAN partner munkafolyamat

Ha egy virtuális WAN partnerrel dolgozik, a munkafolyamat van:

1. Az ágeszköz (VPN/SDWAN) vezérlője hitelesítve van, hogy a helyközpontú adatokat az Azure-ba egy [Azure-szolgáltatásnéven](../active-directory/develop/howto-create-service-principal-portal.md) keresztül exportálja.
2. Az ágeszköz (VPN/SDWAN) vezérlője kéri le az Azure-kapcsolat konfigurációját, és frissíti a helyi eszközt. Ez automatizálja a konfiguráció letöltését, szerkesztését és frissítését a helyszíni VPN-eszközön.
3. Miután az eszköz a megfelelő Azure-konfigurációval rendelkezik, a helyek közötti kapcsolat (két aktív alagút) felépül az Azure WAN irányába. Az Azure az IKEv1 és az IKEv2 támogatására is képes. A BGP használata nem kötelező.

#### <a name="partners"></a>Partnerek site-to-site virtuális WAN-kapcsolatok

A partnerek és a helyek listáját lásd: a [virtuális WAN-partnerek és helyek](virtual-wan-locations-partners.md) cikk.

### <a name="p2s"></a>Pont – hely VPN-kapcsolatok (előzetes verzió)

A pont–hely (P2S) kapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális központtal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás főleg távmunkások számára hasznos, akik egy távoli helyről szeretnének csatlakozni, például otthonról vagy konferenciáról. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne csatlakoztatni.

A kapcsolat létrehozásához lásd: [hozzon létre egy pont – hely kapcsolat használatával virtuális WAN](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>ExpressRoute-kapcsolatok (előzetes verzió)

Az ExpressRoute lehetővé teszi a helyszíni hálózat csatlakoztatása az Azure egy privát kapcsolaton keresztül. A kapcsolat létrehozásához lásd: [hozzon létre egy ExpressRoute-kapcsolat virtuális WAN használatával](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>helyek

A hely adatait, tekintse meg a [virtuális WAN-partnerek és helyek](virtual-wan-locations-partners.md) cikk.

## <a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>További lépések

[Virtuális WAN használatával helyek közötti kapcsolat létrehozása](virtual-wan-site-to-site-portal.md)
