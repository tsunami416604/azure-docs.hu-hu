---
title: Azure hálózati architektúra
description: Ez a cikk a Microsoft Azure infrastruktúra-hálózat általános leírását tartalmazza.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: c4756c36c2243840df69f3696e7ddac3628f3a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727174"
---
# <a name="azure-network-architecture"></a>Azure hálózati architektúra
Az Azure hálózati architektúra az iparági szabvány nak megfelelő core/distribution/access modell módosított verzióját követi, különböző hardverrétegekkel. A rétegek a következők:

- Core (adatközpont-útválasztók)
- Elosztás (hozzáférési útválasztók és L2 összesítés). Az elosztási réteg elválasztja az L3 útválasztást az L2 kapcsolástól.
- Hozzáférés (L2 állomáskapcsolók)

A hálózati architektúra két réteg 2 kapcsolóval rendelkezik. Az egyik réteg összesíti a forgalmat a másik rétegről. A második réteg hurkok bele redundancia. Az architektúra rugalmasabb VLAN lábnyomot biztosít, és javítja a portméretezést. Az architektúra megkülönbözteti az L2-t és az L3-at, ami lehetővé teszi a hardver használatát a hálózat minden egyes rétegében, és minimálisra csökkenti az egyik réteg hibáját a másik réteg(ek) befolyásolásában. A törzsek használata lehetővé teszi az erőforrások megosztását, például az L3 infrastruktúrához való csatlakozást.

## <a name="network-configuration"></a>Hálózati konfiguráció
Egy adatközponton belüli Azure-fürt hálózati architektúrája a következő eszközökből áll:

- Útválasztók (adatközpont, hozzáférési útválasztó és határlevelű útválasztók)
- Kapcsolók (aggregáció és felső rack-kapcsolók)
- Digi-k
- Energiaelosztó egységek

Az Azure két különálló architektúrával rendelkezik. Néhány meglévő Azure-ügyfél és megosztott szolgáltatás az alapértelmezett LAN-architektúrán (DLA) található, míg az új régiók és virtuális ügyfelek a Quantum 10 (Q10) architektúrán vannak. A DLA architektúra egy hagyományos fatervezés, aktív/passzív hozzáférési útválasztókkal és biztonsági hozzáférés-vezérlési listákkal (ACL-ekkel) a hozzáférési útválasztókra. A Quantum 10 architektúra az útválasztók close/mesh kialakítása, ahol az ACL-k nem alkalmazhatók az útválasztóknál. Ehelyett az ACL-ek az útválasztás alatt, a szoftveres terheléselosztás (SLB) vagy a szoftver által definiált VLAN-ok on keresztül kerülnek alkalmazásra.

Az alábbi ábra magas szintű áttekintést nyújt az Azure-fürthálózati architektúráról:

![Az Azure-hálózat diagramja](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Quantum 10 eszközök
A Quantum 10 kialakítás a 3-as rétegváltást több eszközre, clos/mesh kivitelben vezeti. A Q10 kialakítás előnyei közé tartozik a nagyobb képesség és a meglévő hálózati infrastruktúra méretezésének nagyobb képessége. A kialakítás határlevelű útválasztókat, gerinckapcsolókat és állványtetejére futó útválasztókat alkalmaz, hogy a forgalmat több útvonalon továbbítsa a fürtöknek, lehetővé téve a hibatűrést. A hardvereszközök helyett a szoftveres terheléselosztás kezeli a biztonsági szolgáltatásokat, például a hálózati címfordítást.

### <a name="access-routers"></a>Hozzáférés-útválasztók
A terjesztési/hozzáférési L3 útválasztók (ARs) a terjesztési és hozzáférési rétegek elsődleges útválasztási funkcióját hajtják végre. Ezek az eszközök párként vannak telepítve, és az alhálózatok alapértelmezett átjárói. Minden AR-pár több L2 aggregációs kapcsolópárt is támogathat, a kapacitástól függően. A maximális szám az eszköz kapacitásátótól, valamint a hibatartományoktól függ. Egy tipikus szám három L2 aggregációs kapcsolópár ar páronként.

### <a name="l2-aggregation-switches"></a>L2 összesítési kapcsolók  
Ezek az eszközök aggregációs pontként szolgálnak az L2 forgalomhoz. Ezek az L2 háló elosztási rétegei, és nagy mennyiségű forgalmat képesek kezelni. Mivel ezek az eszközök összesítik a forgalmat, 802.1q funkciókat és nagy sávszélességű technológiákat, például portösszesítést és 10GE-t igényelnek.

### <a name="l2-host-switches"></a>L2 állomáskapcsolók
A gazdagépek közvetlenül ezekhez a kapcsolókhoz csatlakoznak. Ezek lehetnek rackbe szerelt kapcsolók, vagy a ház üzembe helyezése. A 802.1q szabvány lehetővé teszi egy VLAN natív VLAN-ként való megjelölését, amely a VLAN-t normál (címkézetlen) Ethernet keretezésként kezeli. Normál körülmények között a natív VLAN-on lévő kereteket egy 802.1q törzsporton címkézetlenül továbbítják és fogadják. Ezt a funkciót a 802.1q-ra való áttéréshez és a nem 802.1q képes eszközökkel való kompatibilitáshoz tervezték. Ebben az architektúrában csak a hálózati infrastruktúra használja a natív VLAN.In this architecture, only the network infrastructure uses the native VLAN.

Ez az architektúra szabványt határoz meg a natív VLAN-kijelöléshez. A szabvány biztosítja, ahol lehetséges, hogy az AR-eszközök egy egyedi, natív VLAN minden törzs és az L2Aggregation l2Aggregation törzsek. Az L2Aggregation to L2Host Switch trunks rendelkezik egy nem alapértelmezett natív VLAN.The L2Aggregation to L2Host Switch trunks have a non-default native VLAN.

### <a name="link-aggregation-8023ad"></a>Hivatkozás összesítése (802.3ad)
A csatolásösszesítés lehetővé teszi, hogy több egyedi hivatkozást egyesítsen, és egyetlen logikai hivatkozásként kezelje őket. Az operatív hibakeresés megkönnyítése érdekében szabványosítsa a portcsatorna-összeköttetések kijelöléséhez használt számot. A hálózat többi része ugyanazt a számot használja a portcsatorna mindkét végén.

Az L2Agg–L2Host kapcsolóhoz megadott számok az L2Agg oldalon használt portcsatorna-számok. Mivel a számok tartománya korlátozottabb az L2Host oldalon, a szabvány az 1-es és 2-es számok használata az L2Host oldalon. Ezek az "a" oldalra, illetve a "b" oldalra haladó portcsatornára vonatkoznak.

### <a name="vlans"></a>Vlan
A hálózati architektúra VLAN-ok segítségével csoportosítja a kiszolgálókat egyetlen szórásos tartományba. A VLAN-számok megfelelnek a 802.1q szabványnak, amely támogatja az 1–4094 számozott VLAN-okat.

### <a name="customer-vlans"></a>Ügyfél VLAN-ok
Az Azure Portalon keresztül üzembe helyezheti a vlan-implementációs lehetőségeket, amelyek megfelelnek a megoldás elkülönítési és architektúrás igényeinek. Ezeket a megoldásokat virtuális gépeken keresztül telepíti. Az ügyfél-referencia architektúra példák: [Azure referencia architektúrák.](https://docs.microsoft.com/azure/architecture/reference-architectures/)

### <a name="edge-architecture"></a>Peremarchitektúra
Az Azure-adatközpontok rendkívül redundáns és jól kiépített hálózati infrastruktúrákra épülnek. A Microsoft az Azure-adatközpontokban lévő hálózatokat "need plus one" (N+1) redundanciaarchitektúrával vagy annál jobb megoldással valósítja meg. Az adatközpontokon belüli és azok közötti teljes feladatátvételi szolgáltatások segítenek a hálózat és a szolgáltatás rendelkezésre állásának biztosításában. Külsőleg az adatközpontokat dedikált, nagy sávszélességű hálózati áramkörök szolgálják ki. Ezek az áramkörök redundánsan több mint 1200 internetszolgáltatóval kötnek össze tulajdonságokat több társviszony-létesítési ponton. Ez több mint 2000 Gb/s potenciális peremhálózati kapacitást biztosít a hálózaton.

Az Azure-hálózat peremhálózati és hozzáférési rétegei szerinti útválasztók szűrése jól megalapozott biztonságot nyújt a csomag szintjén, és segít megakadályozni az Azure-hoz való jogosulatlan csatlakozást. Az útválasztók segítenek biztosítani, hogy a csomagok tényleges tartalma a várt formátumú adatokat tartalmazzon, és megfeleljen a várt ügyfél-kiszolgáló kommunikációs sémának. Az Azure egy rétegzett architektúrát valósít meg, amely a következő hálózati elkülönítési és hozzáférés-vezérlési összetevőkből áll:

- **Peremhálózati útválasztók.** Ezek elkülönítik az alkalmazáskörnyezetet az internettől. A peremhálózati útválasztókat úgy tervezték, hogy hamisításelleni védelmet nyújtsanak, és a hozzáférés korlátozása a hozzáférés-hozzáférés-hozzáférés sel.
- **Terjesztési (hozzáférési) útválasztók.** Ezek csak a Microsoft által jóváhagyott IP-címeket engedélyezik, hamisítás elleni védelmet biztosítanak, és kapcsolatokat hoznak létre a közel-, mint a cl-k használatával.

### <a name="ddos-mitigation"></a>DDOS-kockázatcsökkentés
Az elosztott szolgáltatásmegtagadási (DDoS) támadások továbbra is valós veszélyt jelentenek az online szolgáltatások megbízhatóságára. Ahogy a támadások egyre célzottabbá és kifinomultabbá válnak, és a Microsoft által nyújtott szolgáltatások földrajzilag sokszínűbbé válnak, a támadások hatásának azonosítása és minimalizálása kiemelt fontosságú.

[Az Azure DDoS Protection Standard](../../virtual-network/ddos-protection-overview.md) védelmet nyújt a DDoS-támadások ellen. Tekintse meg [az Azure DDoS-védelem: Gyakorlati tanácsok és referencia architektúrák](ddos-best-practices.md) további információkért.

> [!NOTE]
> A Microsoft alapértelmezés szerint DDoS-védelmet biztosít az összes Azure-ügyfél számára.
>
>

## <a name="network-connection-rules"></a>Hálózati kapcsolat szabályai
Hálózatán az Azure olyan peremhálózati útválasztókat telepít, amelyek csomagszinten biztosítják a biztonságot, hogy megakadályozzák az Azure-hoz való jogosulatlan csatlakozást. A peremhálózati útválasztók biztosítják, hogy a csomagok tényleges tartalma a várt formátumú adatokat tartalmazzon, és megfeleljen a várt ügyfél-kiszolgáló kommunikációs sémának.

Az edge útválasztók elkülönítik az alkalmazáskörnyezetet az internettől. Ezeket az útválasztókat úgy tervezték, hogy hamisítás elleni védelmet nyújtsanak, és korlátozzák a hozzáférést az ACL-ek használatával. A Microsoft rétegzett ACL-megközelítéssel konfigurálja a peremhálózati útválasztókat, hogy korlátozza a peremhálózati útválasztók átvitelére és az útválasztók elérésére engedélyezett hálózati protokollokat.

A Microsoft a hálózati eszközöket a hozzáférési és peremhelyekre helyezte el, hogy határpontként működjön, ahol a be- vagy kilépési szűrőket alkalmazza.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében, olvassa el a következő témakört:

- [Az Azure létesítményei, helyiségei és fizikai biztonsága](physical-security.md)
- [Az Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Az Azure információs rendszer összetevői és határai](infrastructure-components.md)
- [Azure éles hálózat](production-network.md)
- [Az Azure SQL Database biztonsági szolgáltatásai](infrastructure-sql.md)
- [Az Azure éles üzemei és kezelése](infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Az Azure infrastruktúra integritása](infrastructure-integrity.md)
- [Az Azure-beli ügyfelek adatainak védelme](protection-customer-data.md)


