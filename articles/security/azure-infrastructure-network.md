---
title: Az Azure hálózati architektúra
description: Ez a cikk a Microsoft Azure infrastruktúra-hálózat általános leírása.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 5a0f0594a9ccb27a0f76a679e454e9a3ffe19a43
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505386"
---
# <a name="azure-network-architecture"></a>Az Azure hálózati architektúra
Az Azure-beli hálózati architektúra az iparági szabványos mag/terjesztési/hozzáférési modell hardver elkülönült rétege módosított változatát követi. A Rétegek a következők:

- Core (datacenter útválasztó)
- Terjesztési (hozzáférés útválasztók és L2 összesítési). A terjesztési réteg elválasztja az L3-útválasztás L2 váltásban.
- Hozzáférés (L2 gazdagép kapcsolók)

A hálózati architektúra van két szintje a 2. rétegbeli kapcsolók. A többi réteg egy réteg összesítések forgalmát. A második réteg hurkok redundancia révén. Ez egy olyan rugalmasabb VLAN erőforrás-igényű biztosít, és javítja a port méretezés. Az architektúra L2 és L3 darabszáma, amely engedélyezi a hardver a különböző rétegeket, a hálózat minden egyes, és minimálisra csökkenti a többi réteg(ek) befolyásolják egy rétegben tartalék megőrzi. Trönkölés használata lehetővé teszi, hogy az erőforrás-megosztás, például a kapcsolatot az L3-infrastruktúra.

## <a name="network-configuration"></a>Hálózati konfiguráció
A hálózati architektúra az adatközponton belül egy Azure-fürtön a következő eszközöket áll:

- Az útválasztók (datacenter, hozzáférés-útválasztó és levél határútválasztók)
- Kapcsolók (összesítését, és a tor kapcsolók)
- Digi tartalomkezelő rendszer
- Áramelosztó egységek

Az Azure két különböző architektúra tartalmaz. Egyes meglévő Azure-ügyfelek és a megosztott szolgáltatások találhatók az alapértelmezett LAN-architektúrával (DLA), mivel az új régiókban és virtuális ügyfelek találhatók Quantum 10 (10. kérdés) architektúrával. Az DLA architektúra egy hagyományos fa tervezés, az aktív/passzív hozzáférés útválasztók és biztonsági hozzáférés-vezérlési listák (ACL) a alkalmazni a hozzáférés-útválasztóhoz. A Quantum 10 architektúra kialakítás egy közeli/háló útválasztók, ahol hozzáférés-vezérlési listák nem vonatkoznak az útválasztók. Ehelyett ACL-ek alkalmazása az Útválasztás, keresztül szoftveres terheléselosztást (SLB) alatt, vagy a szoftver meghatározott VLAN-OK.

Az alábbi ábrán egy Azure-fürtön belül a hálózati architektúra magas szintű áttekintést nyújt:

![Azure-beli hálózati diagramja][1]

### <a name="quantum-10-devices"></a>Quantum 10-es eszközök
A Quantum 10 tervezési hajt végre a 3. rétegbeli váltása megállítását egy Clos/háló kialakításában több eszközön keresztül. A 10. kérdés tervezési előnyei közé tartozik a nagyobb funkció és a meglévő hálózati infrastruktúra nagyobb lehetőséget. A Tervező határútválasztók levél, fióktípusa kapcsolókhoz és a top-of-rack útválasztók forgalom átadása a fürtök között több útvonalat, lehetővé téve a hibatűrés érdekében alkalmaz. Szoftveres terheléselosztás, helyett hardvereszközök, kezeli a biztonsági szolgáltatások, például a hálózati címfordítást.

### <a name="access-routers"></a>Hozzáférés az útválasztók
A terjesztési/hozzáférési L3 útválasztók (ARs) az elsődleges útválasztási funkcióra a terjesztés és a hozzáférési rétegek végezze el. Ezek az eszközök egy pár üzemelnek, és az alapértelmezett átjáró alhálózatok. AR párjaihoz több L2 összesítési kapcsoló párt, attól függően, kapacitás képes támogatni. Maximális száma attól függ, hogy az eszközt, valamint a hiba tartományok kapacitását. Egy tipikus három L2 összesítési kapcsoló pár egy AR pár áll.

### <a name="l2-aggregation-switches"></a>2. összesítő kapcsolók  
Ezek az eszközök egy összesítési pontra L2 forgalom szolgál. A terjesztési réteg a L2 hálóra, és képes kezelni a nagy mennyiségű forgalmat. Mivel ezek az eszközök összesített forgalmat, 802.1Q szükséges funkciókat, és nagy sávszélességű technológiák, például port összesítési és 10GE.

### <a name="l2-host-switches"></a>L2 gazdagép kapcsolók
Gazdagépek közvetlenül csatlakozhat a kapcsolók. Ezek lehetnek csatlakoztatott kapcsolók, vagy a váz központi telepítések. A 802.1Q szabvány lehetővé teszi, hogy a megjelölés egy VLAN a natív VLAN-t, mint a VLAN kezelésére, normál (címkézetlen) Ethernet keretező. Normál körülmények keretek a natív VLAN-on küldött és fogadott címkézetlen, a 802.1Q a trönkportjához. Ez a szolgáltatás úgy lett kialakítva, a 802.1Q és a nem kompatibilis az áttelepítéshez-802.1Q kompatibilis eszközökhöz. Ebben az architektúrában csak a hálózati infrastruktúra a natív VLAN-t használ.

Ez az architektúra egy natív VLAN-kiválasztási szabvány meghatározza. A standard biztosítja, ahol lehetséges, hogy az AR eszközök minden trönk egy egyedi, a natív VLAN Hálózatot és a L2Aggregation L2Aggregation trönkölés való. A L2Aggregation L2Host kapcsoló trönkölés, van egy nem alapértelmezett natív VLAN-t.

### <a name="link-aggregation-8023ad"></a>Hivatkozás összesítés (802.3ad)
Hivatkozás összesítési lehetővé teszi, hogy több egyes hivatkozások összecsomagolja, és egyetlen logikai hivatkozás számít. Megkönnyítése érdekében működési hibakeresés, a port-csatorna felületek jelölésére használt számot kell szabványos. A hálózat többi részétől azonos számú port-csatorna mindkét végpontján használ.

A számok az L2Agg L2Host kapcsolóhoz megadott L2Agg oldalán használt port-csatorna számok. Mivel a tartomány a számok korlátozottabb L2Host oldalán, a standard, hogy számok, 1. és 2 L2Host oldalán. Ezek tekintse meg a "a" oldalon és a "b" oldalon, a port-csatorna folyamatos jelölik.

### <a name="vlans"></a>VLAN-OK
A hálózati architektúra használ VLAN-OK csoport kiszolgálókat együtt egy szórási tartományba. 802.1Q VLAN-számot felelnek meg szabványok, amely támogatja a VLAN-OK számozott 1 – 4094.

### <a name="customer-vlans"></a>Ügyfél VLAN-OK
VLAN-implementáció számos lehetősége van a elkülönül az architektúra igényeinek, hogy a megoldás az Azure Portalon keresztül telepíthető. Ezek a megoldások segítségével virtuális gépeket telepít. Ügyfél referencia-architektúra példákért lásd [Azure-referenciaarchitektúrák](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Edge-architektúra
Az Azure-adatközpontok beépített magas redundáns és jól létesített hálózati infrastruktúrák esetén. A Microsoft valósítja meg az Azure-adatközpontban való "szükség plusz egy" hálózatok (N + 1) a redundancia architektúrák vagy jobb. Teljes feladatátvételi funkciók belül és az adatközpontok közötti segít a hálózat és szolgáltatás rendelkezésre állásának biztosításához. Külsőleg adatközpontok dedikált, nagy sávszélességű hálózati Kapcsolatcsoportok szolgálja. Ezek a Kapcsolatcsoportok globálisan időpontokban több társviszony-létesítés feletti 1200-as internetszolgáltatók redundantly találkozzon tulajdonságai. Ez itt ajánlatunkban 2000 GB/s a potenciális biztonsági kapacitás, a hálózaton keresztül.

Az edge és a hozzáférési rétegben az Azure-hálózat szűrési útválasztó csomagszinten jól bevált biztonsági nyújt. Ez segít megakadályozni a jogosulatlan kísérel meg csatlakozni az Azure-bA. Az útválasztók, hogy a csomagokat a tényleges tartalmát a várt formátumú adatokat tartalmazhat, és felelnek meg a várt ügyfél/kiszolgáló kommunikációs rendszer segítségével. Az Azure egy többrétegű architektúra, a következő hálózati elkülönítési és hozzáférés-vezérlés összetevői valósít meg:

- **Peremhálózati útválasztók.** Ezek elkülönítse az alkalmazás-környezet az internetről. Peremhálózati útválasztói megszemélyesítés elleni védelmet biztosíthat és a hozzáférés korlátozása a hozzáférés-vezérlési listák használatával lettek kialakítva.
- **Az útválasztók terjesztési (hozzáférés).** Ezek csak a Microsoft a jóváhagyott IP-címek engedélyezése, biztosítása hamisításszűrés és hozzáférés-vezérlési listák használatával kapcsolatokat hozhat létre.

### <a name="a10-ddos-mitigation-architecture"></a>A10-es DDOS kockázatcsökkentő architektúrája
Továbbra is valódi fenyegetést jelenthetnek az online szolgáltatások megbízhatóságát szolgáltatásmegtagadási támadások ellen. Célzott és kifinomult támadások válik, és a szolgáltatások, a Microsoft biztosít több különböző, azonosítása és minimálisra csökkentik a ezeket a támadásokat a hatása a magas prioritású. Az alábbi részletek bemutatják, hogyan valósul meg az a10-es DDOS kockázatcsökkentési rendszer a hálózati architektúra szempontjából.

Az Azure az Adatközpont útválasztó (DCR), amelyek az automatikus észlelési és kockázatenyhítő a10-es hálózati eszközöket használja. Az a10-es megoldás Azure Hálózatfigyelésre használja minta folyamat csomagokat, és határozza meg, hogy van-e a támadás. A támadás észlelése esetén a10-es eszközök el kell távolítani a támadások számának csökkentése érdekében. Csak ezt követően a rendszer tiszta forgalom közvetlenül a a DCR be az Azure-adatközpont engedélyezett. A Microsoft az Azure hálózati infrastruktúra védelmét az a10-es megoldás használja.

Az a10-es megoldásban a DDoS elleni védelmi a következők:

- UDP-IPv4 és IPv6-kéréssekkel túlterhelheti a védelem
- ICMP IPv4 és IPv6-kéréssekkel túlterhelheti a védelem
- TCP IPv4 és IPv6-kéréssekkel túlterhelheti a védelem
- Az IPv4 és IPv6-alapú TCP SZIN támadás elleni védelem
- Töredezettség támadás

> [!NOTE]
> A Microsoft alapértelmezés szerint a DDoS protection biztosít az Azure-ügyfelekre.
>
>

## <a name="network-connection-rules"></a>A hálózati kapcsolat szabályok
A hálózaton Azure üzembe helyezte a peremhálózati útválasztók által biztosított biztonsági csomagszinten, hogy jogosulatlan kísérel meg csatlakozni az Azure-bA. Peremhálózati útválasztói győződjön meg arról, hogy a csomagokat a tényleges tartalmát a várt formátumú adatokat tartalmazhat, és megfelel a várt ügyfél – kiszolgáló kommunikáció séma.

Peremhálózati útválasztói elkülönítse az alkalmazás-környezet az internetről. Ezek az útválasztók megszemélyesítés elleni védelmet biztosíthat, és a hozzáférés korlátozása a hozzáférés-vezérlési listák használatával lettek kialakítva. A Microsoft peremhálózati útválasztói konfigurálja a rétegzett ACL megközelítéssel, amelyek jogosultak a tranzit a peremhálózati útválasztói és az útválasztók eléréséhez korlát hálózati protokollok használatával.

A Microsoft a hálózati eszközök Bűvös határ pontokat, ahol bejövő és kimenő alkalmazza a rendszer-kiszolgálóként való hozzáférés és a peremhálózati helyeken.

## <a name="next-steps"></a>További lépések
A Microsoft nem biztonságossá tétele az Azure-infrastruktúra kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Az Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure vásárlói adatok védelmére](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
