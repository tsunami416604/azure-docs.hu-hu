---
title: Az Azure SQL Database biztonsági szolgáltatásai
description: Ez a cikk általános leírást tartalmaz arról, hogy az Azure SQL Database hogyan védi az ügyféladatokat az Azure-ban.
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
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: ad6d3992f03802174eb03aa30b57b8d3dac1d6c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942952"
---
# <a name="azure-sql-database-security-features"></a>Az Azure SQL Database biztonsági szolgáltatásai    
Az Azure SQL Database relációs adatbázis-szolgáltatást biztosít az Azure-ban. Az ügyféladatok védelme és az ügyfelek által egy relációs adatbázis-szolgáltatástól elvárt erős biztonsági funkciók biztosítása érdekében az SQL Database saját biztonsági képességekkel rendelkezik. Ezek a képességek az Azure-ból örökölt vezérlőkre épülnek.

## <a name="security-capabilities"></a>Biztonsági képességek

### <a name="usage-of-the-tds-protocol"></a>A TDS protokoll használata
Az Azure SQL Database csak a táblázatos adatfolyam (TDS) protokollt támogatja, amely megköveteli, hogy az adatbázis csak a TCP/1433 alapértelmezett portján keresztül legyen elérhető.

### <a name="azure-sql-database-firewall"></a>Az Azure SQL Database tűzfala
Az ügyféladatok védelme érdekében az Azure SQL Database tartalmaz egy tűzfal funkciót, amely alapértelmezés szerint megakadályozza az SQL Database-kiszolgálóhoz való összes hozzáférést, az alábbiak szerint.

![Az Azure SQL Database tűzfala](./media/infrastructure-sql/sql-database-firewall.png)

Az átjárótűzfal korlátozhatja a címeket, így az ügyfelek részletes vezérlése elfogadható IP-címek tartományainak megadását teszi lehetővé. A tűzfal hozzáférést biztosít az egyes kérelmek eredeti IP-címe alapján.

Az ügyfelek egy felügyeleti portál használatával vagy az Azure SQL Database Management REST API-val konfigurálhatják a tűzfal-konfigurációt. Az Azure SQL Database átjáró tűzfal alapértelmezés szerint megakadályozza az összes ügyfél TDS hozzáférést az Azure SQL-adatbázis példányok. Az ügyfeleknek hozzáférés-vezérlési listák (ACL-ek) használatával kell konfigurálniuk a hozzáférést az Azure SQL Database-kapcsolatok lehetővé tételéhez forrás- és célinternetcímek, protokollok és portszámok szerint.

### <a name="dosguard"></a>DoSGuard között
A szolgáltatásmegtagadási (DoS) támadásokat a DoSGuard nevű SQL Database átjárószolgáltatás csökkenti. A DoSGuard aktívan nyomon követi az IP-címekről származó sikertelen bejelentkezéseket. Ha egy adott IP-címről egy adott időszakon belül több sikertelen bejelentkezés történik, az IP-cím egy előre meghatározott ideig nem férhet hozzá a szolgáltatás erőforrásaihoz.

Ezenkívül az Azure SQL Database átjáró ja:

- Biztonságos csatornaképességi egyeztetések a TDS FIPS 140-2 ellenőrzött titkosított kapcsolatok megvalósításához, amikor az adatbázis-kiszolgálókhoz csatlakozik.
- Állapotalapú TDS-csomagvizsgálat, miközben elfogadja az ügyfelek kapcsolatait. Az átjáró ellenőrzi a kapcsolatadatait, és továbbítja a TDS-csomagokat a megfelelő fizikai kiszolgálónak a kapcsolati karakterláncban megadott adatbázisnév alapján.

Az Azure SQL Database-ajánlat hálózati biztonságának átfogó elve, hogy csak a szolgáltatás működéséhez szükséges kapcsolatot és kommunikációt engedélyezze. Az összes többi port, protokoll és kapcsolat alapértelmezés szerint le van tiltva. A virtuális helyi hálózatok (VLAN- ok) és a akta a hálózati kommunikáció forrás- és célhálózatok, protokollok és portszámok szerint történő korlátozására szolgálnak.

A hálózati alapú ACL-ok megvalósítására jóváhagyott mechanizmusok közé tartoznak az útválasztókon és a terheléselosztókon lévő ACL-ok. Ezeket a mechanizmusokat az Azure-hálózatkezelés, a vendég virtuális gép tűzfala és az Azure SQL Database átjáró tűzfalszabályai kezelik, amelyeket az ügyfél konfigurál.

## <a name="data-segregation-and-customer-isolation"></a>Az adatok elkülönítése és az ügyfelek elkülönítése
Az Azure éles hálózat úgy van felépítve, hogy a nyilvánosan elérhető rendszerösszetevők elkülönítve vannak a belső erőforrásoktól. Fizikai és logikai határok vannak a nyilvános azure-portálhoz hozzáférést biztosító webkiszolgálók és az alapul szolgáló Azure virtuális infrastruktúra között, ahol az ügyfélalkalmazás-példányok és az ügyféladatok találhatók.

Minden nyilvánosan elérhető információ kezelése az Azure éles hálózaton belül. Az éles hálózatkétfaktoros hitelesítési és határvédelmi mechanizmusok hatálya alá tartozik, az előző szakaszban ismertetett tűzfal- és biztonsági szolgáltatáskészletet használja, és a következő szakaszokban leírtak szerint adatelkülönítési függvényeket használ.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Nem engedélyezett rendszerek és az FC elkülönítése
Mivel a hálóvezérlő (FC) az Azure-háló központi vezénylője, jelentős vezérlők vannak érvényben a fenyegetések csökkentésére, különösen a potenciálisan sérült pénzügyi megsérült az ügyfélalkalmazásokon belül. Az FC nem ismer fel olyan hardvert, amelynek eszközadatai (például MAC-cím) nincsenek előre betöltve az FC-n belül. Az FC DHCP-kiszolgálói konfigurálták az általuk indítani hajlandó csomópontok MAC-címeit. Még akkor is, ha a jogosulatlan rendszerek vannak csatlakoztatva, nem vannak beépítve a szövet leltár, és ezért nem csatlakozik, vagy jogosult kommunikálni bármely rendszer a szövet leltár. Ez csökkenti annak kockázatát, hogy illetéktelen rendszerek kommunikáljanak az FC-vel, és hozzáférjenek a VLAN-hoz és az Azure-hoz.

### <a name="vlan-isolation"></a>VLAN szigetelés
Az Azure éles hálózat a logikailag három elsődleges VLAN-ra van elkülönítve:

- A fő VLAN: Nem megbízható ügyfélcsomópontok összekapcsolása.
- Az FC VLAN: Megbízható FC-ket és támogató rendszereket tartalmaz.
- Az eszköz VLAN: Megbízható hálózati és egyéb infrastruktúra-eszközöket tartalmaz.

### <a name="packet-filtering"></a>Csomagszűrés
Az IPFilter és a szoftveres tűzfalak, amelyek a gyökér operációs rendszeren és a csomópontok vendég operációs rendszerén vannak megvalósítva, kapcsolódási korlátozásokat kényszerítenek ki, és megakadályozzák a virtuális gépek közötti jogosulatlan forgalmat.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hipervizor, gyökéroperációs rendszer és vendég virtuális gépek
A legfelső szintű operációs rendszer elkülönítése a vendég virtuális gépek és a vendég virtuális gépek egymástól a hipervizor és a gyökér operációs rendszer által kezelt.

### <a name="types-of-rules-on-firewalls"></a>A tűzfalakra vonatkozó szabályok típusai
A szabály definíciója:

{Src IP, Src port, cél IP,célport, célprotokoll, be/ki, állapotnélküli, állapotnélküli folyamat időtúlértéke}.

A szinkron tétlen karakter (SYN) csomagok csak akkor engedélyezettek be- és kiengedésre, ha a szabályok bármelyike ezt lehetővé teszi. A TCP esetében az Azure állapotnélküli szabályokat használ, ahol az alapelv az, hogy csak az összes nem SYN-csomagot engedélyezi a virtuális gépbe vagy onnan. A biztonsági feltevés az, hogy minden állomásverem rugalmasan figyelmen kívül hagyja a nem SYN-t, ha korábban nem látott SYN-csomagot. Maga a TCP-protokoll állapotalapú, és az állapot nélküli SYN-alapú szabállyal kombinálva egy állapotalapú implementáció általános viselkedését valósítja meg.

A User Datagram Protokoll (UDP) esetében az Azure állapotalapú szabályt használ. Minden alkalommal, amikor egy UDP-csomag megfelel egy szabálynak, a másik irányban fordított folyamat jön létre. Ez a folyamat beépített időhammal rendelkezik.

Az ügyfelek felelősek a saját tűzfalak beállításáért az Azure által biztosított adatokon felül. Itt az ügyfelek képesek meghatározni a szabályokat a bejövő és kimenő forgalom.

### <a name="production-configuration-management"></a>Termeléskonfiguráció kezelése
A szabványos biztonságos konfigurációkat a megfelelő műveleti csapatok tartják karban az Azure-ban és az Azure SQL Database-ben. Az éles rendszerek összes konfigurációs változását dokumentálják és nyomon követik egy központi nyomkövető rendszeren keresztül. A szoftver- és hardverváltozásokat a központi nyomkövető rendszeren keresztül követik nyomon. Az ACL-hez kapcsolódó hálózati változásokat az ACL-kezelő szolgáltatás követi nyomon.

Az Azure összes konfigurációs módosítása az átmeneti környezetben van kifejlesztve és tesztelve, majd éles környezetben vannak üzembe helyezve. A szoftverbuildeket a tesztelés részeként ellenőrizzük. A biztonsági és adatvédelmi ellenőrzéseket a belépési ellenőrzőlista feltételeinek részeként ellenőrizzük. A módosításokat az adott üzembe helyezési csapat ütemezett időközönként telepíti. A kiadásokat a megfelelő üzembe helyezési csapat munkatársai ellenőrzik és aláírják, mielőtt éles környezetbe telepítenék őket.

A változásokat a rendszer figyeli a siker érdekében. Hiba esetén a módosítás visszaáll az előző állapotába, vagy egy gyorsjavítás takar, hogy a kijelölt személyzet jóváhagyásával orvosolja a hibát. A Source Depot, a Git, a TFS, a Master Data Services (MDS), a runners, az Azure biztonsági figyelése, az FC és a WinFabric platform központilag kezelheti, alkalmazza és ellenőrzi a konfigurációs beállításokat az Azure virtuális környezetben.

Hasonlóképpen, a hardver- és hálózati módosítások ellenőrzési lépéseket hoztak létre a buildkövetelmények betartásának kiértékelésére. A kiadásokat a rendszer a megfelelő csoportok koordinált változástanácsadó testületén (CAB) keresztül vizsgálja felül és engedélyezi a veremben.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében, olvassa el a következő témakört:

- [Az Azure létesítményei, helyiségei és fizikai biztonsága](physical-security.md)
- [Az Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Az Azure információs rendszer összetevői és határai](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure éles hálózat](production-network.md)
- [Az Azure éles üzemei és kezelése](infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Az Azure infrastruktúra integritása](infrastructure-integrity.md)
- [Az Azure-beli ügyfelek adatainak védelme](protection-customer-data.md)
