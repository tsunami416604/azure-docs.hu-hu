---
title: Az Azure Virtual WAN áttekintése | Microsoft Docs
description: Ismerje meg a virtuális WAN automatikus méretezhető ág ágba irányuló kapcsolatot, az elérhető régiók és partnerekkel.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 6a6fc9df2b102fd16bba03f26df4e24a1c946875
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409992"
---
# <a name="what-is-azure-virtual-wan"></a>Mi az Azure Virtual WAN?

Az Azure Virtual WAN egy olyan hálózati szolgáltatás, amely optimalizált és automatizált ágak közötti kapcsolódást biztosít az Azure-on keresztül. A Virtual WAN segítségével ágeszközöket csatlakoztathat és konfigurálhat az Azure-ral való kommunikációra. Ez elvégezhető manuálisan vagy egy virtuális WAN-partneren keresztül partner eszközök használatával. Partner eszközökkel, könnyen használható, kapcsolat és a konfigurációkezelés egyszerűsítése lehetővé teszi. További információkért lásd: a [helyek és partnerek](virtual-wan-locations-partners.md) cikk. Emellett az Azure-WAN beépített Irányítópult segítségével azonnal képet kaphat hibaelhárítási, amelyek segítségével időt takarít meg, és a egy legegyszerűbb módja nagy méretű kapcsolat biztosít.

![A Virtual WAN ábrája](./media/virtual-wan-about/virtualwan.png)

A cikk gyors betekintést biztosít az Azure-beli és nem Azure-beli számítási feladatok hálózati kapcsolataiba. A Virtual WAN a következő előnyöket biztosítja:

* **Küllős topológiájú integrált kapcsolat megoldások:** Automatizálhatja a Site-to-Site konfiguráció és a helyszíni hely és a egy Azure-központ közötti kapcsolat.
* **Automatizált küllő beállítás és konfiguráció:** A virtuális hálózatok és a számítási feladatok Azure hubon zökkenőmentesen kapcsolódik.
* **Intuitív hibaelhárítási:** Tekintse meg a teljes körű folyamatot az Azure-ban, és a szükséges műveletekre ezen információk használatával.

## <a name="s2s"></a>Helyek közötti kapcsolatok

Ha helyek közötti kapcsolatot szeretne létrehozni a Virtual WAN használatával, ezt megteheti egy [Virtual WAN-partneren](virtual-wan-locations-partners.md) keresztül vagy manuálisan is.

### <a name="s2spartner"></a>Partner munkafolyamat

Ha egy virtuális WAN partnerrel dolgozik, a munkafolyamat van:

1. Az ágeszköz (VPN/SDWAN) vezérlője hitelesítve van, hogy a helyközpontú adatokat az Azure-ba egy [Azure-szolgáltatásnéven](../active-directory/develop/howto-create-service-principal-portal.md) keresztül exportálja.
2. Az ágeszköz (VPN/SDWAN) vezérlője kéri le az Azure-kapcsolat konfigurációját, és frissíti a helyi eszközt. Ez automatizálja a konfiguráció letöltését, szerkesztését és frissítését a helyszíni VPN-eszközön.
3. Miután az eszköz a megfelelő Azure-konfigurációval rendelkezik, a helyek közötti kapcsolat (két aktív alagút) felépül az Azure WAN irányába. Az Azure az IKEv1 és az IKEv2 támogatására is képes. A BGP használata nem kötelező.

Ha nem szeretne egy partnert, manuálisan konfigurálja a kapcsolatot, lásd: [hozzon létre egy helyek közötti kapcsolattal virtuális WAN](virtual-wan-site-to-site-portal.md).

## <a name="p2s"></a>Pont–hely kapcsolatok (előzetes verzió)

A pont–hely (P2S) kapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális központtal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás főleg távmunkások számára hasznos, akik egy távoli helyről szeretnének csatlakozni, például otthonról vagy konferenciáról. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne csatlakoztatni.

A kapcsolat manuális létrehozásával kapcsolatban lásd [a pont–hely kapcsolatok a Virtual WAN használatával való létrehozását](virtual-wan-point-to-site-portal.md) ismertető cikket.

## <a name="er"></a>ExpressRoute-kapcsolatok (előzetes verzió)

A kapcsolat manuális létrehozásával kapcsolatban lásd [az ExpressRoute-kapcsolatok a Virtual WAN használatával való létrehozását](virtual-wan-expressroute-portal.md) ismertető cikket.

## <a name="resources"></a>Virtual WAN-erőforrások

Végpontok közötti virtuális WAN konfigurálásához a következő erőforrásokat kell létrehozni:

* **virtualWAN:** A virtualWAN erőforrás jelöli az Azure-hálózat egy virtuális területre, és több erőforrások gyűjteménye. Hivatkozást tartalmaz az összes virtuális elosztóra, amelyet bele szeretne foglalni a virtuális WAN-hálózatba. A Virtual WAN-erőforrások elkülönülnek egymástól, és nem tartalmazhatnak közös elosztót. A Virtual WAN virtuális elosztói nem kommunikálnak egymással. Az „ág és ág közötti adatforgalom engedélyezése” tulajdonság engedélyezi a VPN-helyek közötti, illetve a VPN és az ExpressRoute-kompatibilis helyek közötti adatforgalmat. Ne feledje, hogy az Azure Virtual WAN-ban található ExpressRoute jelenleg előzetes verzióban érhető el.

* **Hely:** A hely erőforrás vpnsite néven jelöli, a helyszíni VPN-eszköz és annak beállításait. A Virtual WAN-partnerekkel együttműködve olyan beépített megoldásokhoz fér hozzá, amelyekkel automatikusan exportálhatók ezek az adatok az Azure-ba.

* **Hub:** Egy virtuális központtal egy Microsoft által felügyelt virtuális hálózatot. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Az elosztó a hálózat központja egy adott régióban. Azure-régiónként csak egy elosztó létezhet. Amikor az Azure Portalon létrehoz egy elosztót, ezzel létrehoz egy hozzá tartozó virtuális hálózatot és egy VPN Gateway-átjárót is.

  Az elosztó átjárója nem ugyanaz a virtuális hálózati átjáró, amelyet az ExpressRoute és a VPN Gateway esetében használ. Például a Virtual WAN használata esetén a helyszíni helyről nem közvetlenül a virtuális hálózatára hoz létre helyek közötti kapcsolatot. Ehelyett az elosztóra hozza létre a helyek közötti kapcsolatot. A forgalom minden esetben az elosztó átjáróján halad keresztül. Ez azt jelenti, hogy a virtuális hálózatoknak nincs szükségük saját virtuális hálózati átjáróra. A Virtual WAN segítségével a virtuális hálózatok könnyen méretezhetők a virtuális elosztón és annak átjáróján keresztül. 

* **Eseményközpont-kapcsolat virtuális hálózat:** A Hub virtuális hálózati kapcsolati erőforrás szolgál a hub zökkenőmentesen kapcsolódik a virtuális hálózathoz. Jelenleg csak olyan virtuális hálózatokhoz kapcsolódhat, amelyekkel egy elosztórégióban van.

* **Hub útválasztási táblázatot:**  Hozzon létre egy virtuális központ útvonalat, és a virtuális központ útválasztási táblázatot az útvonal vonatkozik. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat.

## <a name="partner-region"></a>Partnerek és helyek

### <a name="partner"></a>Partnerek

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

### <a name="locations"></a>helyek

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>További lépések

Nézet a [virtuális WAN-partnerek és helyek](virtual-wan-locations-partners.md) lapot.
