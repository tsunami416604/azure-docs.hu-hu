---
title: Az Azure SQL Database biztonsági funkciók
description: A cikk ismerteti, hogyan védi az Azure SQL Database a vásárlói adatokat az Azure általános leírása.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: cd2ad16f910f5d2b3b801c8d54e9df7660751462
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103948"
---
# <a name="azure-sql-database-security-features"></a>Az Azure SQL Database biztonsági funkciók    
Az Azure SQL Database relációsadatbázis-szolgáltatás az Azure-ban biztosít. Az ügyféladatok védelme, és adja meg az erős biztonsági funkciókat, amelyeket az ügyfelek várható relációsadatbázis-szolgáltatás, SQL-adatbázis rendelkezik a saját csoportok biztonsági képességeket. Ezeket a képességeket, amely az Azure-ból örökölt útmutatóra épül.

## <a name="security-capabilities"></a>Biztonsági képességek

### <a name="usage-of-the-tds-protocol"></a>A TDS protokoll használatát
Az Azure SQL Database támogatja a csak a tabulált adatfolyam (TDS) protokoll, amely szükséges az adatbázis keresztül csak az alapértelmezett a TCP/1433-as port elérhetők lesznek.

### <a name="azure-sql-database-firewall"></a>Az Azure SQL Database-tűzfal
Ügyféladatok védelme érdekében az Azure SQL Database egy tűzfal funkciót tartalmaz, amely alapértelmezés szerint minden hozzáférés az SQL Database-kiszolgálóhoz, megakadályozza a lent látható módon.

![Az Azure SQL Database-tűzfal][1]

A gateway-tűzfalhoz korlátozhatja címek, amely lehetővé teszi a felhasználóknak szabályozható az elfogadható IP-címek tartományát adja meg. A tűzfal hozzáférést biztosít minden kérés eredeti IP-címe alapján.

Ügyfelek tűzfal-konfiguráció érheti el a felügyeleti portál használatával, vagy programozott módon, az Azure SQL Database Management REST API segítségével. Alapértelmezés szerint az Azure SQL Database átjáró tűzfal megakadályozza, hogy minden ügyfél TDS-hozzáférés az Azure SQL database-példányok. Ügyfelek konfigurálni kell a hozzáférését lehetővé teszi a csatlakozást az Azure SQL Database hozzáférés-vezérlési listák (ACL-ek) használatával a forrás és cél internetcímek, protokollok és a portszámok.

### <a name="dosguard"></a>DoSGuard
Egy SQL Database-átjáró szolgáltatás nevű DoSGuard csökkentik szolgáltatásmegtagadási (DoS) támadások ellen. DoSGuard aktívan nyomon követi a sikertelen bejelentkezések IP-címekről. Ha egy adott IP-címről több sikertelen bejelentkezés időn belül, az IP-cím hozzáférését az olyan erőforrások, az előre meghatározott időszakban a szolgáltatás le van tiltva.

Emellett az Azure SQL Database átjárója hajtja végre:

- Biztonságos csatorna képesség tárgyalások megvalósításához TDS FIPS 140-2 ellenőrzése a titkosított kapcsolatokat, amikor csatlakozik a az adatbázis-kiszolgálók.
- Állapot-nyilvántartó TDS csomagvizsgálatról, miközben ügyfelektől érkező kapcsolatokat fogad. Az átjáró érvényesíti a kapcsolati adatokat, és továbbítja a TDS-csomagokat a megfelelő fizikai kiszolgálóra, az adatbázis neve a kapcsolati karakterláncban megadott alapján.

A hálózati biztonság az Azure SQL Database-ajánlat címtérrel alapelve, hogy csak a kapcsolat és az, hogy a szolgáltatás működéséhez szükséges kommunikációt engedélyezze. Alapértelmezett blokkolja a többi portok, protokollok és kapcsolatokat. Virtuális helyi hálózatokat (VLAN) és ACL-ek forrás és cél hálózatok, a protokollok és a portszámok által használt hálózati kommunikáció korlátozása.

Neurálishálózat-alapú ACL-ek végrehajtásához jóváhagyott mechanizmusok ACL-ek belefoglalása az útválasztókon és terheléselosztókat. Ezek a mechanizmusok az Azure-hálózatok, a Vendég virtuális gép tűzfala és az Azure SQL Database átjáró tűzfalszabályok vannak konfigurálva, az ügyfél által felügyelt.

## <a name="data-segregation-and-customer-isolation"></a>Adatok elkülönítése és az ügyfél elkülönítése
Az Azure éles hálózati környezetben van felépítve, hogy nyilvánosan hozzáférhető rendszerösszetevők elkülönülnek a belső erőforrásokat. Fizikai és logikai határok webkiszolgálók, amelyeket a nyilvánosan elérhető az Azure portal eléréséhez és az alapul szolgáló Azure virtuális infrastruktúra, ahol az ügyfél az alkalmazáspéldányok és a vásárlói adatok találhatók közötti licencszerződéseket.

Az összes nyilvánosan elérhető információk az Azure éles hálózati környezetben van kezelhető. Az éles hálózati környezetben a kétfaktoros hitelesítés, illetve a határ védelmi mechanizmust, használja a tűzfal- és biztonsági funkció, amely az előző szakaszban leírt, és az adatok elkülönítése funkciók feljegyzett használja a következő szakaszokban.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Jogosulatlan rendszerek és az fc elkülönítés
Mivel a hálóvezérlő (FC) központi az orchestrator, az Azure fabric, jelentős vezérlők hely, ahol elhárítani a fenyegetéseket, főleg a potenciálisan veszélyeztetett FAs ügyfélalkalmazások belül találhatók. Az FC nem ismeri fel a hardvereket, amelynek eszköz adatait (például MAC-cím) nem előre betölti az FC belül. A DHCP-kiszolgálók az FC konfigurálta a csomópontok arra, hogy a MAC-címek listáját. Akkor is, ha jogosulatlan rendszerek csatlakoznak, nem szóló fabric készlet, és ezért nem csatlakoztatott vagy kommunikálni bármely olyan rendszeren belül a háló készlet engedélyezett. Ez csökkenti a jogosulatlan rendszerek az FC kommunikáló, illetve hozzáférjenek a VLAN- és Azure kockázatát.

### <a name="vlan-isolation"></a>VLAN elkülönítése
Az Azure éles hálózati környezetben van logikailag elkülönítve három elsődleges VLAN-ok:

- A fő virtuális helyi hálózat: Amellyel az ügyfél nem megbízható csomópontokon.
- A Szálcsatornás virtuális helyi hálózat: Megbízható FCs és a támogató rendszerek tartalmazza.
- Az eszköz VLAN: Megbízható hálózatok és egyéb infrastrukturális eszközök tartalmazza.

### <a name="packet-filtering"></a>Hálózaticsomag-szűrés
A IPFilter és a szoftver-tűzfalak, amelyek a gyökér operációs rendszer és a vendég operációs rendszer a csomópontok kapcsolat kikényszerítheti, és megakadályozza a jogosulatlan forgalom a virtuális gépek között.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hipervizor, a gyökér operációs rendszer és a Vendég virtuális gépek
A gyökér operációs rendszer a Vendég virtuális gépek és a Vendég virtuális gépeket egy másik elkülönítése a hipervizor és a gyökér operációs rendszer kezeli.

### <a name="types-of-rules-on-firewalls"></a>A tűzfalak szabályok típusai
Egy szabály határozza meg:

{Security Response Center (forrás) IP, forrásport, cél IP-, Célport, cél protokoll, és horizontális, állapotalapú és állapotmentes, állapotalapú folyamat időtúllépés}.

Csak akkor, ha a szabályok közül bármelyik lehetővé teszi, hogy szinkron tétlen karakter (külön) csomagok vagy használata engedélyezett. TCP, az Azure használ állapotmentes szabályok ahol elve az, hogy csak az összes nem külön csomag lehetővé teszi vagy onnan máshová a virtuális Gépet. A biztonsági helyi, hogy a gazdagépen szoftverkörnyezettől rugalmas figyelmen kívül tudják nem külön, ha azt nem találkozott egy külön csomag korábban. A TCP protokoll állapotalapú, és az állapot nélküli szinkronizálás a mi-alapú szabály együtt éri el az általános működést egy állapotalapú megvalósítási.

A User Datagram Protocol (UDP), az Azure állapot-nyilvántartó szabály használ. Minden alkalommal, amikor egy UDP csomag megfelel valamelyik szabálynak, a másik irányba fordított folyamat jön létre. Ez a folyamat rendelkezik egy beépített időkorlátja.

Ügyfelek felelőssége saját tűzfallal felül az Azure biztosít beállításához. Ügyfelek itt tudnak meghatározzák azon szabályokat a bejövő és kimenő forgalmat.

### <a name="production-configuration-management"></a>Éles konfigurációkezelés
Standard biztonságos konfigurációját az Azure és az Azure SQL Database a megfelelő üzemeltetési csapatok által karbantartása. Minden konfigurációmódosítás éles rendszereket dokumentált, és a egy központi követőrendszerhez és nyomon követni. A központi követési rendszer-n keresztül a hardver- és változások nyomon követése. A hálózat módosításai vonatkozó ACL-t, a rendszer nyomon követi egy ACL management szolgáltatással.

Az Azure-ban minden konfigurációmódosítás kifejlesztett és tesztelt átmeneti környezetben, és ezt követően az éles környezetben vannak telepítve. Szoftver buildek lektorálhatók a tesztelés során. Biztonság és adatvédelem ellenőrzések nyilvánosan lektorálhatók bejegyzés ellenőrzőlista feltétel részeként. Változások a megfelelő telepítési csapat által telepített ütemezett időközönként. Kiadások felül, és jóváhagyta a megfelelő központi telepítési csapat munkatársak által, éles környezetben való telepítésük előtt.

Módosítások figyeli a program sikeres. Egy meghibásodási forgatókönyv szerint a módosítás vissza lesz állítva az előző állapotába, vagy a gyorsjavítás telepítve van a kijelölt személyzet jóváhagyással a hiba megoldása érdekében. Forrás Depotban, a Git, TFS, Master Data Services (MDS), indák, az Azure security monitoring, az FC és a WinFabric platform központilag kezelheti, alkalmazza, és ellenőrizze a konfigurációs beállításokat az Azure-beli virtuális szolgálnak.

Hasonlóképpen hardver- és hálózati módosítások létrehozott elfogadását, a build követelmények kiértékeléséhez ellenőrzési lépéseket. A kiadások tekintse át és a verem között egy koordinált változások tanácsadó bizottsága (CAB-fájl), a megfelelő csoportok keresztül engedélyezett.

## <a name="next-steps"></a>További lépések
A Microsoft használ az Azure-infrastruktúra secure kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure vásárlói adatok védelmére](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
