---
title: Az Azure SQL Database biztonsági funkciói
description: Ez a cikk ismerteti az Azure SQL Database általános leírása az Azure-ban felhasználói adatok védelmét.
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
ms.openlocfilehash: cca8febb004029b13b0df09a047da701c4528e8e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102582"
---
# <a name="microsoft-azure-sql-database-security-features"></a>A Microsoft Azure SQL Database biztonsági funkciói    
A Microsoft Azure SQL Database biztosít egy relációs adatbázis-szolgáltatás az Azure-ban. Felhasználói adatok védelmét, és adja meg, amely a relációs adatbázis-szolgáltatás elvárt erős biztonsági funkciói, SQL-adatbázis számára a saját biztonsági képességei készleteinek. Ezek a képességek az Azure-ból örökölt vezérlők épül.

## <a name="security-capabilities"></a>Biztonsági képességei

### <a name="usage-of-tabular-data-stream-tds-protocol"></a>Tabular Data Stream (TDS) protokoll használatát
A Microsoft Azure SQL Database csak támogatja a TDS-protokoll, amely szükséges az adatbázis csak keresztül az alapértelmezett port a TCP/1433 elérhetők lesznek.

### <a name="microsoft-azure-sql-database-firewall"></a>A Microsoft Azure SQL Database-tűzfal
Ügyfelek adatok védelme érdekében a Microsoft Azure SQL Database egy tűzfal funkcióit, így alapértelmezés szerint minden hozzáférés az SQL Database-kiszolgálóhoz, az alább látható módon tartalmazza.

![Az Azure SQL Database-tűzfal][1]

Az átjáró tűzfala lehetővé teszi a korlátozza az elfogadható IP-címek tartományát adja meg az ügyfél számára a tanúsítványhasználat pontos szabályzása lehetővé tevő címeket. A tűzfal hozzáférést biztosít minden kérelem származási IP-címe alapján.

Tűzfal-konfiguráció valósítható meg a felügyeleti portál használatával, vagy programozott módon, a Microsoft Azure SQL Database felügyeleti REST API. A Microsoft Azure SQL adatbázis átjáró tűzfal alapértelmezés szerint megakadályozza minden ügyfél TDS Microsoft Azure SQL-adatbázisok. Hozzáférés engedélyezéséhez a forrás és cél Internet címeket, protokollok, és a Microsoft Azure SQL Database kapcsolatok ACL-ekkel kell konfigurálni.

### <a name="dosguard"></a>DoSGuard
Egy SQL-adatbázis átjáró szolgáltatás DoSGuard-szolgáltatásmegtagadásos (DoS-) támadások csökkentik. DoSGuard aktívan IP-címekről nyomon követi a sikertelen bejelentkezések. Ha egy időszakon belül több sikertelen bejelentkezés egy adott IP-címről, az IP-cím le van tiltva a szolgáltatás egy előre meghatározott időszak bármely erőforrások eléréséhez.

A fentiek mellett a Microsoft Azure SQL Database átjáró is végez:

- Biztonságos csatorna funkció egyeztetések TDS FIPS 140-2 végrehajtásához érvényesíteni a titkosított kapcsolatokat, az adatbázis-kiszolgálókhoz való csatlakozáskor.
- Állapot-nyilvántartó TDS Csomagvizsgálat ügyfelektől érkező kapcsolatok elfogadása közben. Az átjáró érvényesíti a kapcsolati adatokat, és a megfelelő fizikai kiszolgálóhoz, az adatbázis neve a kapcsolati karakterláncban megadott alapján továbbítja a TDS-csomagok.

A hálózati biztonság, a Microsoft Azure SQL Database ajánlat átívelő elvet hoz engedélyezése csak a kapcsolat és a szükséges ahhoz, hogy a szolgáltatás működéséhez-kommunikációt. Más portok, protokollok és kapcsolatok alapértelmezett blokkolja. Virtuális helyi hálózatok és a hozzáférés-vezérlési listák segítségével korlátozhatja a hálózati kommunikáció a forrás és a cél hálózatok, a protokollok és a portszámokat.

Hálózati hozzáférés-vezérlési listák végrehajtásához jóváhagyott mechanizmusok az alábbiak: ACL-ek útválasztók és a terheléselosztók. Ezeket felügyelt Azure-hálózat, a Vendég virtuális gép tűzfal és a Microsoft Azure SQL Database átjáró tűzfalszabályok, amely az ügyfél által konfigurált.

## <a name="data-segregation-and-customer-isolation"></a>Adatok elkülönítése és az ügyfelek elkülönítési
Az Azure éles hálózati környezetben van felépítve, úgy, hogy a nyilvánosan elérhető rendszerösszetevők elkülönülnek a belső erőforrásokat. Fizikai és logikai létezik a nyilvánosan elérhető Azure portál és az alapjául szolgáló Azure virtuális infrastruktúra, felhasználói az alkalmazáspéldányok és a felhasználói adatokat tároló hozzáférést biztosító web-kiszolgálók között.

Összes nyilvánosan elérhető információk az Azure éles hálózaton belüli kezeli. Az éles hálózati környezetben az kéttényezős hitelesítést és a határ védelmi mechanizmust, az előző szakaszban ismertetett állítsa be a tűzfal és a biztonsági szolgáltatás, és adatok elkülönítési funkciók alábbi esetekben használja.

### <a name="unauthorized-systems-and-isolation-of-fc"></a>Jogosulatlan rendszerek és az FC elkülönítése
Mivel az FC központi az orchestrator, a Microsoft Azure háló, jelentős vezérlők, amelyek csökkentik fenyegetéseket, különösen a potenciálisan veszélyeztetett eszközök belül vevő alkalmazások rendelkezése. FC nem ismeri fel a hardvereket, amelynek eszköz adatait (például MAC-cím) nem előre betölti az FC belül. A DHCP-kiszolgálók az FC konfigurálta azokat a rendszer rendszerindító csomópontja MAC-címek listáját. Akkor is, ha a jogosulatlan rendszerek összekötése nem szóló háló készlet, és ezért nem csatlakozó vagy kommunikálhat a rendszer a háló készlet belül engedélyezett. Ez csökkenti a jogosulatlan rendszerek és az FC, majd a VLAN és az Azure hozzáférjenek kockázatát.

### <a name="vlan-isolation"></a>VLAN elkülönítése
Az Azure éles hálózati környezetben logikailag elkülönített a három elsődleges VLAN-ok:

- A fő VLAN – összeköttetések használatára a nem megbízható felhasználói csomópontok
- Az FC VLAN – tartalmazza a megbízható FCs és a támogató rendszerek
- Az eszköz VLAN – tartalmazza a megbízható hálózatok és egyéb eszközök

### <a name="packet-filtering"></a>Hálózaticsomag-szűrés
A IPFilter és a szoftver tűzfalak megvalósítva a gyökér operációs rendszer és a vendég operációs rendszer, a csomópontok kapcsolat korlátozások érvényesítése, és megakadályozza a jogosulatlan virtuális gépek közötti forgalmat.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hipervizor, a gyökér operációs rendszer és a Vendég virtuális gépek
A Vendég virtuális gépek és a Vendég virtuális gépek egymástól a gyökér operációs rendszer elkülönítését a hipervizor és a gyökér operációs rendszer kezeli.

### <a name="types-of-rules-on-firewalls"></a>A tűzfalak szabályok típusai
Egy szabály típusúként van definiálva:

{Biztonsági válasz Center (forrás) IP, a forrásport, cél IP-, Célport, cél protokoll, állapotalapú alkalmazások és szolgáltatások állapotmentes, állapotalapú folyamat időtúllépés ki- /}.

Szinkronizálás a mi csomagját bejövő vagy kimenő csak akkor, ha a szabályok lehetővé teszi. A TCP, Microsoft Azure állapot nélküli szabályokat használ ahol elve az, hogy csak lehetővé teszi az összes nem SZIN csomagok virtuális gépbe vagy onnan a virtuális Gépet. A biztonsági helyi, hogy minden gazdagép verem is lehetséges, a figyelmen kívül hagyva nem SZIN, ha már nem látottaknál SZIN csomag korábban. A TCP protokoll állapotalapú, és az állapot nélküli SYNbased együtt szabály éri el egy átfogó állapot-nyilvántartó megvalósítási viselkedését.

A User Datagram Protocol (UDP), a Microsoft Azure egy állapotalapú szabályt használ. Minden alkalommal, amikor egy UDP csomag megfelel a szabálynak, a fordított áramlását az ellenkező irányba jön létre. Ez a folyamat egy beépített időtúllépési rendelkezik.

Ügyfelek felelőssége saját Microsoft Azure biztosít fölött tűzfalak beállítása. Itt az ügyfelek képesek a bejövő és kimenő forgalom vonatkozó szabályok meghatározásához.

### <a name="production-configuration-management"></a>Éles konfigurációkezelés
Standard biztonságos konfiguráció Azure-ban és a Microsoft Azure SQL Database megfelelő műveletek csoportok karbantartása. Éles rendszerek esetén minden konfigurációs módosítását dokumentált, és egy központi követőrendszerrel és nyomon követni. A központi követőrendszerrel keresztül a szoftverek és hardverek változások nyomon követése. A hálózat módosításai vonatkozó hozzáférés-vezérlési lista követi ACL kezelési szolgáltatás (AMS) használatával.

A Microsoft Azure minden konfigurációs módosítását fejlesztett és tesztelése az átmeneti környezetben; és azt követően telepített éles környezetben. Szoftver buildek felülvizsgálata tesztelés részeként. Biztonság és adatvédelem ellenőrzések felülvizsgálata bejegyzés ellenőrzőlista feltétel részeként. Változások a megfelelő telepítési csapat által ütemezés szerint vannak telepítve. Kiadásokban felülvizsgálata, és írja alá a megfelelő központi telepítési csapat személyzet éles környezetben telepítésük előtt.

Változások a sikeres figyeli. Egy meghibásodási forgatókönyv szerint a módosítás van állítva vissza korábbi állapotba, vagy a gyorsjavítást a rendszer a kijelölt személyzet jóváhagyással hiba. Forrás raktár számára, Git, TFS, az MDS, indák, Azure biztonsági figyelése (ASM), a FC, és a WinFabric platform végzi központilag kezelheti, alkalmazza, és ellenőrizze a konfigurációs beállításokat az Azure virtuális környezetben.

Hasonlóképpen hardver- és hálózati módosítások létesítése elfogadását, a build követelmények kiértékeléséhez ellenőrzési lépéseket. A kiadások felülvizsgálata, és jogosult keresztül egy koordinált módosítás tanácsadó bizottsága (CAB-fájl) megfelelő csoportok között a veremben.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Az Azure létesítményekben, a helyszíni és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure információk rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Az Azure éles műveletek és kezelése](azure-infrastructure-operations.md)
- [Azure-infrastruktúra megfigyelése](azure-infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](azure-infrastructure-integrity.md)
- [Az Azure-ban felhasználói adatok védelme](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
