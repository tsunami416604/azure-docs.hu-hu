---
title: Az Azure hálózati architektúra
description: Ez a cikk a Microsoft Azure infrastruktúra-hálózat általános leírását tartalmazza.
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
ms.openlocfilehash: 67781f196b445c9330e0dcb1fc7d8b0a1a53cbc0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102241"
---
# <a name="azure-network-architecture"></a>Az Azure hálózati architektúra
Az Azure hálózati architektúra követi az iparági szabványos Core-szoftverterjesztési-hozzáférés modell, különálló rétegekkel módosított változatát. A Rétegek a következők:

- Core (Datacenter útválasztó)
- Terjesztési (hozzáférés útválasztók és 2. szintű összesítő) – a terjesztési réteg elválasztja a 3. 2. szintű váltásban Útválasztás
- Access (L2 állomás kapcsolók)

A hálózati architektúra két olyan szintje van, a réteg 2 kapcsolók, a többi forgalom összesítése egy réteget, és redundanciát átfogó 2 hurkok réteg. Például egy olyan rugalmasabb VLAN erőforrásigényét előnyökkel, és javítja a port méretezés. Az architektúra tartja a 2. és 3. különálló, amely engedélyezi, hogy a hardver minden a különböző rétegeket, a hálózaton, és minimálisra csökkenti a hiba más réteg(ek) befolyásolják egy rétegben. A hálózati trönkölés használatával az erőforrás-megosztás, például a 3. infrastruktúra a kapcsolatot.

## <a name="network-configuration"></a>Hálózati konfiguráció
A Microsoft Azure-fürt adatközponton belül a hálózati architektúra tartalmaz a következő eszközöket:

- Útválasztók (Datacenter hozzáférés útválasztó és levél határútválasztók)
- Kapcsolók (összesítési és felső részén állvány kapcsolók)
- Digi CMs
- PDU-k vagy Nucleons

Az alábbi ábra magas szintű áttekintést nyújt az Azure hálózati architektúra a fürtön belül. Azure két külön architektúrák rendelkezik. Néhány meglévő Azure-ügyfél és a megosztott szolgáltatások helyezkedhet el az alapértelmezett hálózati architektúra (DLA), míg az új régiók és virtuális ügyfelek Quantum 10 (10. kérdés) architektúra keresztül kell elérnie. A DLA architektúra aktív-passzív hozzáférés útválasztóval hagyományos fa kialakítást, és biztonsági hozzáférés-vezérlési listákat a hozzáférési útválasztók alkalmazott. A Quantum 10 architektúra az útválasztók, ahol hozzáférés-vezérlési listákat a rendszer nem alkalmazza az útválasztók, de a útválasztási keresztül szoftver Load Balancing Állapotfigyelője alatt vagy szoftveralapú VLAN-OK clos/háló kialakítást.

![Azure-hálózat][1]

### <a name="quantum-10-devices"></a>Quantum 10-eszközök
A Quantum 10 tervezési 3 rétegbeli átváltását terjedésének végez egy CLOS/háló kialakításában több eszközön keresztül. A 10. kérdés tervezési előnyei közé tartozik a nagyobb funkció és a meglévő hálózati infrastruktúra nagyobb lehetőséget. A Tervező levél határútválasztók, gerinc kapcsolók és felső az állvány útválasztók, fürtök között lehetővé teszi a hibatűrés több útvonal forgalom átadása funkcióit használja. Biztonsági szolgáltatások, például a hálózati címfordítás a hardveres eszközökön keresztül szoftveres terheléselosztást ahelyett, hogy kezeli.

### <a name="access-routers"></a>Hozzáférés útválasztók
A 3. terjesztési/access útválasztók (ARs) az elsődleges útválasztási funkcióra a telepítési és a hozzáférési rétegek végezheti el. Ezek az eszközök párban vannak telepítve, és az alapértelmezett átjáró alhálózatok esetében. Minden AR párt több L2 összesítési kapcsoló párok, attól függően, hogy a kapacitás képes támogatni. Maximális kapacitását, az eszköz, valamint a sikertelen tartományok függ. Egy tipikus száma alapján várt kapacitás három L2 összesítési kapcsoló párok egy AR pár lenne.

### <a name="l2-aggregation-switches"></a>2. szintű Összesítő kapcsolók  
Ezek az eszközök szolgáljanak, a 2. szintű forgalmat egy összesítési pontra. A terjesztési réteg a 2. szintű a háló, és kezelni tud a nagy forgalmat okoznak a. Mivel ezek az eszközök összesített forgalom, 802.1Q funkció is szükséges, és a nagy sávszélesség technológiák, például a port összesítésére és 10GE szolgálnak.

### <a name="l2-host-switches"></a>2. szintű Állomás kapcsolók
Gazdagépek csatlakozzon közvetlenül a kapcsolók. Állványra szerelt kapcsolók- vagy váz telepítések esetén is lehetnek. A 802.1Q szabvány lehetővé teszi egy VLAN a natív VLAN-OK, mint a megnevezését normál (címkézetlen) Ethernet keretezési adott VLAN kezelni. Normál körülmények a natív VLAN-on keretek küldött és fogadott címkézetlen egy 802.1Q a törzs port. Ez a szolgáltatás úgy lett kialakítva, áttelepítés 802.1Q és nem kompatibilisek-802.1Q használatára képes eszközök. Ebben a rendszerben csak a hálózati infrastruktúra a natív VLAN-t használja.

Ez az architektúra olyan szabvány, amely biztosítja, ahol lehetséges, hogy az AR eszközök a minden trönk egyedi natív VLAN és a L2Aggregation L2Aggregation hálózati trönkölés a natív VLAN-kijelölés határozza meg. A L2Host kapcsoló hálózati trönkölés L2Aggregation rendelkezik egy nem alapértelmezett natív VLAN-t.

### <a name="link-aggregation-8023ad"></a>Hivatkozás összesítési (802.3ad)
Kapcsolat-összesítési (eltérése) lehetővé teszi, hogy több egyes hivatkozások egyetlen kötegbe foglalnak és egy logikai hivatkozás számít. A felhasznált port-csatorna felületek számát kell kell szabványosított, így ahhoz, hogy működési könnyebb hibakeresés, a hálózat többi részétől fogja használni a azonos számú port-csatorna mindkét végpontján. Ehhez olyan szabvány, mely a port csatorna adhat meg a következő érték végét meghatározásához.

A megadott L2Host kapcsolóhoz L2Agg számadatok L2Agg oldalán használt port-csatorna számok. Számok tartománya korlátozottabb L2Host oldalán, mert a normál használandó számot 1 és 2 L2Host oldalán olvassa el a port-csatornára, a "a" oldalon és a "b" állapotra.

### <a name="vlans"></a>VLAN-OK
A hálózati architektúra használ VLAN-OK kiszolgálók együtt egy szórási tartományba. 802.1Q VLAN-számok felelnek meg szabványok, amely támogatja a VLAN-OK számozása 1 – 4094.

### <a name="customer-vlans"></a>Ügyfél VLAN-OK
Ügyfelek különböző VLAN megvalósítási lehetősége van a megoldás elkülönítését és architektúra igényeinek az Azure portálon keresztül telepíthet. Ezek a megoldások keresztül virtuális gépek vannak telepítve. Ügyfél referencia architektúra példák Microsoft [Azure hivatkozás architektúrák](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Peremhálózati architektúrája
Azure adatközpontjaiban beépített magas redundáns és jól kiépített hálózati infrastruktúrák esetén. Hálózatok az Azure adatközpontjaiban van megvalósítva "szükség plusz egy" (N + 1) redundancia architektúrák vagy nagyobb frekvenciával. Teljes feladatátvételi szolgáltatások belül, és az Adatközpont között révén a hálózati és a szolgáltatás rendelkezésre állásának biztosításához. Külsőleg az adatközpontok dedikált, nagy sávszélességű hálózati kapcsolatok redundantly kapcsolódó tulajdonságok a több mint 1200-as Internet szolgáltatóknak globálisan pontokon több társviszony-létesítési, amelyek átlépik ezt a potenciális biztonsági kapacitás 2000 GB/s szolgálja ki a hálózaton.

A Microsoft Azure-hálózat széle és hozzáférési rétegben szűrési útválasztó nyújt jól meghatározott biztonsági a csomag szintjén, hogy megakadályozza a jogosulatlan kísérel meg csatlakozni az Azure-bA. Győződjön meg arról, hogy a csomagokat a tényleges tartalmát a várt formátumú adatokat tartalmaznak, és felelnek meg a várt ügyfél – kiszolgáló kommunikáció rendszer segítenek. Azure valósítja meg a következő hálózati elkülönítést és a hozzáférés-vezérlés összetevői egy rétegzett architektúra:

- A szegély útválasztók - elkülönítse az alkalmazás-környezet az internetről. Peremhálózati útválasztók lettek kialakítva megszemélyesítés elleni védelmet, és korlátozza a hozzáférés-vezérlési listák (ACL).
- Terjesztési (elérés) útválasztók - hozzáférés útválasztók engedélyezése csak a Microsoft jóváhagyott IP-címek, adja meg az ACL-ekkel hamisításszűrés, és létesített kapcsolatok tervezték.

### <a name="a10-ddos-mitigation-architecture"></a>A10 DDOS megoldás architektúrája
Szolgáltatásmegtagadási támadások továbbra is a Microsoft online services megbízhatóságát valós fenyegetést jelenthet. Módon támadások több vált megcélzott, kifinomultabb, és a szolgáltatásként a Microsoft válik több számos különböző földrajzi helyen, hogy a hatékony mechanizmusok azonosításához, és minimálisra csökkenthető az ilyen támadások káros hatása az egy magas prioritású virtuális gép.

Az alábbiakban részletesen ismertetjük, hogyan van megvalósítva a A10 DDOS megoldás rendszer a hálózati architektúra szempontjából.  
A Microsoft Azure A10 hálózati eszközöket, amelyek az automatikus észlelés és javaslat biztosítanak DCR (Datacenter útválasztó) használja. A A10 megoldás hálózatfigyelési Azure nettó folyamata csomagok mintát, és megállapítja, hogy van-e a támadás. Ha a támadás észlel, védelme az A10 eszközök használatosak gázmosók. Megoldás, után további tiszta érkező forgalom nem engedélyezett az Azure adatközpontjába közvetlenül a DCR. A A10 megoldás az Azure hálózati infrastruktúra védelmére szolgál.

DDoS védelem a A10 megoldásban a következők:

- UDP-IPv4 és IPv6 kéréssekkel védelme
- ICMP IPv4 és IPv6 kéréssekkel védelme
- TCP IPv4 és IPv6 kéréssekkel védelme
- TCP SZIN támadás védelmi IPv4 és IPv6
- Töredezettsége támadás

> [!NOTE]
> DDoS-védelem biztosított alapértelmezés szerint az összes Azure-ügyfél.
>
>

## <a name="network-connection-rules"></a>Hálózati kapcsolat szabályok
Azure peremhálózati útválasztók a hálózaton, amely a csomag szintjén, hogy megakadályozza a jogosulatlan kísérel meg csatlakozni a Microsoft Azure biztonsági telepíti. Biztosítják, hogy a csomagokat a tényleges tartalmát a várt formátumú adatokat tartalmaznak, és felelnek meg a várt ügyfél – kiszolgáló kommunikáció séma.

Peremhálózati útválasztók elkülönítse az alkalmazás-környezet az internetről. Peremhálózati útválasztók lettek kialakítva megszemélyesítés elleni védelmet, és korlátozza a hozzáférés-vezérlési listák (ACL). A peremhálózati útválasztók egy rétegzett korlát hálózati protokollok, amelyek számára engedélyezett a peremhálózati útválasztók tranzit és útválasztók eléréséhez megközelítése hozzáférés-vezérlési lista használatával vannak konfigurálva.

A hálózati eszközök hozzáférés és a peremhálózati helyeken legyenek elhelyezve, és ahol érkező és/vagy a kilépési alkalmazza a rendszer határ pontok összekötőként.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Az Azure létesítményekben, a helyszíni és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure információk rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciói](azure-infrastructure-sql.md)
- [Az Azure éles műveletek és kezelése](azure-infrastructure-operations.md)
- [Azure-infrastruktúra megfigyelése](azure-infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](azure-infrastructure-integrity.md)
- [Az Azure-ban felhasználói adatok védelme](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
