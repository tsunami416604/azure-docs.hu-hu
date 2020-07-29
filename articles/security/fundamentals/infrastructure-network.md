---
title: Azure hálózati architektúra
description: Ez a cikk a Microsoft Azure infrastruktúra hálózatának általános leírását tartalmazza.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "68727174"
---
# <a name="azure-network-architecture"></a>Azure hálózati architektúra
Az Azure hálózati architektúrája az iparági standard alap-/terjesztési/hozzáférési modell módosított verzióját követi, amely különböző hardverabsztrakciós rétegekkel rendelkezik. A rétegek a következők:

- Mag (Datacenter útválasztók)
- Eloszlás (hozzáférési útválasztók és L2-összesítés). A terjesztési réteg elkülöníti az L3-útválasztást az L2-váltástól.
- Hozzáférés (L2 gazdagép-kapcsolók)

A hálózati architektúra a 2. rétegbeli kapcsolók két szintje. Az egyik réteg a másik réteg forgalmát összesíti. A második réteg hurkokat tartalmaz a redundancia beépítéséhez. Az architektúra rugalmasabb VLAN-lábnyomot biztosít, és javítja a portok skálázását. Az architektúra megtartja az L2 és az L3 különbséget, ami lehetővé teszi a hardverek használatát a hálózat különböző rétegeiben, és az egyik rétegben lévő hibák minimalizálásával befolyásolja a többi réteget. A Trunks használata lehetővé teszi az erőforrások megosztását, például az L3-infrastruktúrához való kapcsolódást.

## <a name="network-configuration"></a>Hálózati konfiguráció
Az adatközponton belüli Azure-fürtök hálózati architektúrája a következő eszközökből áll:

- Útválasztók (Datacenter, hozzáférési útválasztó és szegély nélküli útválasztók)
- Kapcsolók (Összesítés és felső szintű kapcsolók)
- Digi CMs
- Energiaellátási egységek

Az Azure két különálló architektúrával rendelkezik. Néhány meglévő Azure-ügyfél és-szolgáltatás az alapértelmezett LAN-architektúrán (DLA) található, míg az új régiók és a virtuális ügyfelek a Quantum 10 (Q10) architektúrán helyezkednek el. A DLA architektúra egy hagyományos faszerkezetes kialakítás, amely aktív/passzív hozzáférési útválasztókkal és a hozzáférési útválasztók számára alkalmazott biztonsági hozzáférés-vezérlési listákkal (ACL-ekkel) rendelkezik. A Quantum 10 architektúra az útválasztók bezárási/rácsvonali kialakítása, ahol az ACL-eket nem alkalmazza az útválasztók. Az ACL-eket az Útválasztás alatt, a szoftveres terheléselosztás (SLB) vagy a szoftveresen megadott VLAN-ok alapján alkalmazza a rendszer.

Az alábbi ábra áttekintést nyújt az Azure-fürtön belüli hálózati architektúráról:

![Az Azure Network diagramja](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Quantum 10-es eszközök
A Quantum 10 terv a 3. rétegbeli váltást hajtja végre több eszközön a Clos/Mesh kialakításban. A koenzim kialakításának előnyei közé tartozik a nagyobb képesség és a meglévő hálózati infrastruktúra méretezésének lehetősége. A kialakítás a Border Leaf-útválasztók, a gerinc-kapcsolók és a Top-of-rack útválasztók használatával továbbítja a forgalmat a fürtökhöz több útvonalon keresztül, ami lehetővé teszi a hibatűrést. A szoftveres terheléselosztás hardveres eszközök helyett a biztonsági szolgáltatásokat, például a hálózati címfordítást kezeli.

### <a name="access-routers"></a>Hozzáférési útválasztók
A Distribution/Access L3 útválasztók (ARs) elvégzik az elsődleges útválasztási funkciót a terjesztési és hozzáférési rétegekhez. Ezeket az eszközöket a rendszer pár-ként telepíti, és az alhálózatok alapértelmezett átjárója. Az egyes AR-párok a kapacitástól függően több L2 aggregációs kapcsolót is támogatnak. A maximális szám az eszköz képességeitől és a meghibásodási tartománytól függ. Egy tipikus szám három L2 aggregációs kapcsoló pár AR pár.

### <a name="l2-aggregation-switches"></a>L2-összesítési kapcsolók  
Ezek az eszközök az L2-forgalom összesítési pontként szolgálnak. Ezek az L2 háló terjesztési rétege, és nagy mennyiségű forgalmat kezelhetnek. Mivel ezek az eszközök összesítik a forgalmat, 802.1 q funkciókat és nagy sávszélességű technológiákat igényelnek, például a portok összesítését és a 10GE.

### <a name="l2-host-switches"></a>L2 gazdagép kapcsolói
A gazdagépek közvetlenül csatlakoznak ezekhez a kapcsolókhoz. Lehetnek állványra csatlakoztatott kapcsolók vagy alváz-telepítések. A 802.1 q szabvány lehetővé teszi, hogy egy VLAN-t natív VLAN-ként megtervezzen, a VLAN-t normál (címkézett) Ethernet-keretezésként kezelje. A normál körülmények között a natív VLAN kereteit a rendszer a 802.1 q Trunk porton továbbított és címkézett címkével fogadja el. Ez a szolgáltatás a 802.1 q-ra való áttelepítésre és a nem 802.1 q-kompatibilis eszközökkel való kompatibilitásra lett tervezve. Ebben az architektúrában csak a hálózati infrastruktúra használja a natív VLAN-t.

Ez az architektúra egy szabványt határoz meg a natív VLAN kiválasztásához. A standard biztosítja, hogy ha lehetséges, az AR-eszközök egyedi, natív VLAN-t biztosítanak minden törzshöz és a L2Aggregation L2Aggregation-törzsekhez. A L2Host kapcsoló törzsei nem alapértelmezett natív VLAN-L2Aggregation rendelkeznek.

### <a name="link-aggregation-8023ad"></a>Csatolás összesítése (802.3 ad)
A csatolások összesítése lehetővé teszi, hogy több különálló hivatkozás együtt legyen összecsomagolva, és egyetlen logikai hivatkozásként legyen kezelve. Az operatív hibakeresés megkönnyítése érdekében a port-Channel felületek kijelöléséhez használt számot szabványosítani kell. A hálózat többi része ugyanazt a számot használja a port csatornájának mindkét végén.

A L2Agg a L2Host kapcsolóhoz megadott számok a L2Agg oldalon használt port-Channel számok. Mivel a számok tartománya korlátozottabb a L2Host oldalon, a standard az 1. és a 2. szám használata a L2Host oldalon. Ezek az "a" és a "b" oldalon található portra vonatkoznak.

### <a name="vlans"></a>VLAN
A hálózati architektúra VLAN-okat használ a kiszolgálók csoportosítására egyetlen szórási tartományba. A VLAN-számok megfelelnek a 802.1 q szabványnak, amely támogatja az 1 – 4094 VLAN-okat.

### <a name="customer-vlans"></a>Ügyfél VLAN-ok
Számos VLAN-megvalósítási lehetőség közül választhat a Azure Portal segítségével, hogy megfeleljen a megoldás elkülönítési és architektúrára vonatkozó igényeinek. Ezeket a megoldásokat virtuális gépeken keresztül helyezheti üzembe. Az ügyfélszolgálati architektúrával kapcsolatos Példákért lásd: [Azure-referenciák architektúrái](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Peremhálózati architektúra
Az Azure-adatközpontok nagyon redundáns és jól kiépített hálózati infrastruktúrára épülnek. A Microsoft az Azure-adatközpontokban található hálózatokat a "szükséges plusz egy" (N + 1) redundancia-architektúrával vagy annál jobb megoldással valósítja meg. Az adatközpontok teljes feladatátvételi szolgáltatásai segítenek a hálózat és a szolgáltatás rendelkezésre állásának biztosításában. Külsőleg az adatközpontokat dedikált, nagy sávszélességű hálózati áramkörök szolgálják ki. Ezek az áramkörök redundánsan csatlakoznak a több mint 1200 internetszolgáltatóval rendelkező szolgáltatásokhoz. Ez több, mint 2 000 GB/s potenciális peremhálózati kapacitást biztosít a hálózaton belül.

Az útválasztók az Azure-hálózat peremhálózati és hozzáférési rétegében való szűrése jól bevált biztonságot biztosít a csomagok szintjén, és segít megakadályozni az Azure-hoz való kapcsolódást a jogosulatlan kísérletekben. Az útválasztók segítenek biztosítani, hogy a csomagok tényleges tartalma a várt formátumú adatokat tartalmazza, és megfeleljen a várt ügyfél/kiszolgáló kommunikációs sémának. Az Azure többplatformos architektúrát valósít meg, amely a következő hálózati elkülönítési és hozzáférés-vezérlési összetevőkből áll:

- **Edge-útválasztók.** Ezek elkülönítik az alkalmazási környezetet az internetről. Az Edge-útválasztók úgy vannak kialakítva, hogy a hamisítás elleni védelmet és a hozzáférés korlátozását ACL-ek használatával korlátozzák.
- **Terjesztési (hozzáférési) útválasztók.** Ezek csak a Microsoft által jóváhagyott IP-címeket engedélyezik, hamisítást tesznek lehetővé, és az ACL-eket használva kapcsolatok hozhatók létre.

### <a name="ddos-mitigation"></a>DDOS-mérséklés
Az elosztott szolgáltatásmegtagadási (DDoS) támadások továbbra is valós fenyegetést jelentenek a online szolgáltatások megbízhatósága szempontjából. Mivel a támadások célzottabbak és kifinomultabbak lesznek, és a Microsoft által nyújtott szolgáltatások földrajzilag sokrétűek, a támadások hatásainak azonosítása és minimalizálása magas prioritást jelent.

[Azure DDoS Protection standard](../../virtual-network/ddos-protection-overview.md) védelmet nyújt a DDOS-támadások ellen. További információért lásd [Azure DDoS Protection: ajánlott eljárások és hivatkozási architektúrák](ddos-best-practices.md) .

> [!NOTE]
> A Microsoft alapértelmezés szerint minden Azure-ügyfél számára biztosít DDoS-védelmet.
>
>

## <a name="network-connection-rules"></a>Hálózati kapcsolatok szabályai
A hálózatán az Azure olyan Edge-útválasztókat helyez üzembe, amelyek a csomagok szintjén biztosítanak biztonságot, hogy megakadályozzák az Azure-hoz való jogosulatlan kapcsolódást. Az Edge-útválasztók biztosítják, hogy a csomagok tényleges tartalma a várt formátumú adatokat tartalmazza, és megfeleljen a várt ügyfél/kiszolgáló kommunikációs sémának.

Az Edge-útválasztók elkülönítik az alkalmazás környezetét az internetről. Ezek az útválasztók a hamisítás elleni védelem biztosítására szolgálnak, és ACL-ek használatával korlátozzák a hozzáférést. A Microsoft többplatformos ACL-megközelítéssel konfigurálja az Edge-útválasztókat, így korlátozhatja a peremhálózati útválasztók és az elérési útválasztók átadására jogosult hálózati protokollokat.

A Microsoft a hozzáférési és a peremhálózati helyeken lévő hálózati eszközöket olyan határként viselkedik, ahol a bejövő vagy kimenő szűrőket alkalmazza.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy mit tesz a Microsoft az Azure-infrastruktúra biztonságossá tételéhez, tekintse meg a következőt:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)


