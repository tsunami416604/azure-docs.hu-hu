---
title: Magas rendelkezésre állású - Azure SQL Database szolgáltatásban |} Microsoft Docs
description: 'További tudnivalók az Azure SQL Database szolgáltatás magas rendelkezésre állású szolgáltatásait és funkcióit:'
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 27f0c49913b424a6bd77b7cb6f7d6e97598c2157
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839809"
---
# <a name="high-availability-and-azure-sql-database"></a>Magas rendelkezésre állású és az Azure SQL-adatbázis
Az Azure SQL adatbázis Platformszolgáltatási ajánlat kezdete óta a Microsoft tett a felhőalkalmazások nyújtotta az ügyfelek, amelyek a szolgáltatás részét magas rendelkezésre állású (HA), és az ügyfelek nem szükséges üzemeltetéséhez, különleges logika hozzáadása, és magas rendelkezésre ÁLLÁSÚ körül döntéseket. A Microsoft fenntartja a magas rendelkezésre ÁLLÁSÚ rendszerkonfiguráció és a művelet, teljes hozzáféréssel, szolgáltatásiszint-szerződésben garantált kínál az ügyfeleknek. A magas rendelkezésre ÁLLÁSÚ SLA régióban SQL-adatbázis vonatkozik, és nem nyújt védelmet a teljes tartomány hibák, amely kívül a Microsoft irányítása alá tartozó tényezők miatt esetekben (például természeti katasztrófa, war, tevékenységéért terrorakció is történhet, lázadások, kormányzati műveletet, vagy egy hálózati vagy az eszköz hiba a Microsoft adatközpontokban, beleértve az ügyfél helyeken vagy a felhasználói helyek és a Microsoft-adatközpont közti külső).

A magas rendelkezésre ÁLLÁSÚ probléma lemezterület leegyszerűsítése Microsoft használja a következő előfeltételek:
1.  Hardver- és hibák előbb vagy utóbb elkerülhetetlenné
2.  Műveleti személyzet vétett hibákhoz vezethet
3.  Tervezett karbantartási műveletek miatt leállások esetén 

Míg ezek az egyéni események alkalomszerű, a felhőbeli skálázással, előfordulásuk időpontjában naplózhatja őket minden héten, ha nem minden nap. 

## <a name="fault-tolerant-sql-databases"></a>A hibatűrő SQL-adatbázisok
Az ügyfelek tervezheti meg a saját adatbázis rugalmasságát és kisebb rugalmasságát, az SQL Database szolgáltatás egészére. a szolgáltatás 99,99 % bekapcsolási ideje értelmetlen, ha "a" része a 0,01 %-os-adatbázisok, amelyek le. Minden adatbázis kell lennie a hibatűrő, és tartalék megoldás soha nem kell a véglegesített tranzakció elvesztését eredményezi. 

Adatok, az SQL-adatbázis közvetlen csatlakoztatott lemezek/VHD-alapú helyi tárolás (LS) és a prémium szintű Azure Storage lapblobokat-alapú távoli tárolás (r) használja. 
- A prémium szintű storage szerepel, vagy fontos üzleti (előzetes verzió) adatbázisok és rugalmas készletek, amelyek tervezett bevetésirányító kritikus OLTP alkalmazások magas IOPS-követelményekkel rendelkező helyi. 
- Távoli tároló Basic, Standard és az általános célú szolgáltatásrétegekben, keret célú üzleti munkaterhelések tárolási rendszerre van szüksége, és számítási teljesítményt méretezését tervezett használata. Egyetlen oldalakra vonatkozó blob adatbázishoz és naplófájlokhoz, és beépített tárolókezelési replikációs és feladatátvételi mechanizmusok használják.

Mindkét esetben a replikáció, a tárhelyhiba-észlelés és a feladatátvételi mechanizmusok SQL-adatbázis teljesen automatizált, és emberi beavatkozás nélkül. Ez az architektúra győződjön meg arról, hogy véglegesített adatok soha nem elvész, és hogy adatok tartóssága elsőbbséget élvez az összes más célja.

Főbb előnyei:
- Az ügyfelek élvezheti teljes a replikált adatbázisok konfigurálása vagy bonyolult hardverek, szoftverek, operációs rendszerek vagy virtualizálási környezetek karbantartása nélkül.
- A rendszer teljes ACID tulajdonságok a relációs adatbázisok karbantartása.
- Feladatátvétel teljesen automatizált bármely véglegesített adatvesztés nélkül.
- Az elsődleges másodpéldányhoz kapcsolatok útválasztási dinamikusan kezeli a szolgáltatás nem szükséges alkalmazás logikával.
- Automatizált redundanciát a magas szintű külön díjfizetés nélkül van megadva.

> [!NOTE]
> A leírt magas rendelkezésre állású architektúra van, külön értesítés nélkül változhatnak. 

## <a name="data-redundancy"></a>Adatredundancia

A magas rendelkezésre állású megoldás az SQL-adatbázis alapuló [Always ON rendelkezésre állási csoportok](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technológia az SQL-kiszolgálóról, és működnek az LS és RS adatbázis is minimális eltérésekkel lehetővé teszi. LS a konfigurációban az Always ON rendelkezésre állási csoport technológia használjuk a megőrzéséhez az RS-ben a rendszer rendelkezésre állás (aktív georeplikáció által alacsony RTO). 

## <a name="local-storage-configuration"></a>Helyi tárolás beállítása

Ebben a konfigurációban az egyes adatbázisok online állapotba a szolgáltatás (MS) belül a vezérlő gyűrű. Egy elsődleges és legalább két másodlagos replika (kvórum-készlet) nem található, a bérlő ring kiterjedő három független fizikai alrendszerek ugyanabban az adatközpontban. Az átjáró (GW) az elsődleges másodpéldány által küldött összes olvasási és írási, és az írási műveletek aszinkron módon replikálva vannak a másodlagos replikákon. SQL-adatbázis egy kvórum-alapú véglegesítési sémát használja, ha az adatok írása az elsődleges és a tranzakciók véglegesítése előtt legalább egy másodlagos másodpéldány.

A [Service Fabric](../service-fabric/service-fabric-overview.md) feladatátvételi rendszer automatikusan újraépíti a replikákat, a csomópont meghibásodik, és kvórum-készlet tagsági karbantartott csomópontok egymástól, és csatlakoztassa a rendszer. Tervezett karbantartás gondosan koordinált, hogy megakadályozza a kvórum-set állapotra vált a replikakészlet minimális száma (általában 2) alatt. Prémium szintű és kritikus fontosságú üzleti esetén működik ez a modell (előzetes verzió) adatbázisok, de van szükség a redundancia számítási és a tárolási összetevők, és magasabb költsége eredményez.

## <a name="remote-storage-configuration"></a>Távoli tárolás beállítása

Távoli tárolókonfigurációkkal (alapszintű, Standard vagy általános célú rétegek) pontosan egy másolás megőrződik távoli blob, a rendszer lehetőségeit tartósságot, a redundancia és a bit-rothadás észlelési használatával. 

A magas rendelkezésre állású architektúra által az alábbi ábrán látható:
 
![magas rendelkezésre állású architektúrája](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>Hibaészlelés és helyreállítás 
A nagy méretű elosztott rendszerek kell egy nagymértékben megbízható hiba rendszer, amely észlelni tudja megbízhatóan, hibák gyorsan, és az ügyfélnek a lehető legközelebb. Az SQL Database esetén ez a rendszer Azure Service Fabric alapul. 

Az elsődleges másodpéldány azonnal nyilvánvaló, amikor az elsődleges másodpéldány sikertelen volt, és a munka nem folytatható, mert az összes olvasási és írási kerül sor az elsődleges replikán először. Ez a folyamat léptessen elő egy másodlagos másodpéldány az elsődleges állapotát rendelkezik helyreállítási idő célkitűzése (RTO) = 30 másodpercet és a helyreállítási időkorlát (RPO) = 0. A 30 másodpercet RTO hatásának mérsékléséhez, az ajánlott úgy próbáljon újból többször a kapcsolat sikertelen kísérletek kisebb várakozási időt.

Ha egy másodlagos másodpéldány nem sikerül, az adatbázis egy minimális kvórum-készlet nem szemben a le van. A Service fabric kezdeményezi az újrakonfigurálás folyamata hasonló a következő sikertelen volt-e az elsődleges másodpéldány, ezért annak megállapításához, hogy a hiba állandó, rövid várakozás után egy másik másodlagos replika jön létre. Esetekben ideiglenes out szolgáltatási állapot, operációs rendszer hibát vagy frissítés, például egy új replika nem-t azonnal engedélyezéséhez indítsa a sikertelen csomópont. 

A távoli tárolókonfigurációkkal SQL-adatbázis funkcióit használja az Alwayson feladatátvételi adatbázisokhoz frissítéskor. Ehhez egy új SQL-példányhoz van hoz előre a tervezett frissítési esemény részeként, és csatolja, és helyreállítja az adatbázisfájlt a távtároló. Folyamat leállásából eredő hiba vagy egyéb nem tervezett események esetén a Windows Fabric a példány rendelkezésre állási kezeli, és helyreállítási utolsó lépésként csatol a távoli adatbázis-fájl.

## <a name="zone-redundant-configuration-preview"></a>Redundáns zónakonfigurációk (előzetes verzió)

Alapértelmezés szerint a helyi tárolókonfigurációkkal kvórum-set replikáit ugyanabban az adatközpontban jönnek létre. Bevezetésével [Azure rendelkezésre állási zónák](../availability-zones/az-overview.md), arra, hogy a különböző replikák a kvórum beállítása másik rendelkezésre állási zónákhoz ugyanabban a régióban van. Elkerülése érdekében a hibaérzékeny pontok kialakulását, a vezérlő gyűrű is duplikált több zóna között, három átjáró körök (GW). Egy adott átjáró ring útválasztást vezérli [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Mivel redundáns zónakonfigurációk nem hoz létre további adatbázis-redundancia biztosítása, a prémium szintű rendelkezésre állási zónával használatát, vagy a szolgáltatásszintek üzleti kritikus (előzetes verzió) érhető el: nem további költség. A zóna redundáns adatbázis kiválasztásával biztosíthatja, hogy a prémium szintű, vagy fontos üzleti (előzetes verzió) adatbázisok rugalmas, hogy egy sokkal nagyobb hibák, beleértve a katasztrofális datacenter kimaradások, az alkalmazás logikája módosítás nélkül. Bármely létező Premium vagy a fontos üzleti adatbázisok vagy készletek (előzetes verzió) átválthat a zóna redundáns konfigurációra.

Mivel a redundáns kvórum-zónakészlet replikák néhány távolsága a különböző adatközpontokban van, a hálózati késés növelheti a véglegesítés ideje, és így csökkenti a teljesítményt az egyes OLTP-munkaterhelések. Mindig visszatérhet a egyetlen zónakonfigurációk által a zóna redundancia beállításnak a letiltása. Ez a folyamat egy Adatműveletek mérete, és a rendszeres szolgáltatásiszint-célkitűzés (SLO) frissítési hasonló. A folyamat végén az adatbázis vagy a készletbe telepítenek át egy zóna redundáns gyűrű egy zóna gyűrű vagy fordítva.

> [!IMPORTANT]
> Zóna redundáns adatbázisok és rugalmas készletek jelenleg csak akkor támogatott a prémium szolgáltatásszintet. Nyilvános előzetes verziójához, biztonsági másolatok és naplózása során a rekordok RA-GRS tárolási tárolódnak, és ezért nem lehet egy zóna kiterjedő leállás esetén automatikusan elérhető. 

A magas rendelkezésre állású architektúra redundáns zóna verziója által az alábbi ábrán látható:
 
![magas rendelkezésre állású architektúra zónaredundáns](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Olvassa el a kibővített
Ismertetettek szerint Premium és fontos üzleti (előzetes verzió) szolgáltatás rétegek emelés Kvórum beállítása és az AlwaysON technológia a magas rendelkezésre állás egyetlen zóna és a redundáns zónabeállítások egyaránt. A AlwasyON előnyeit egyike, hogy a replikákat mindig a tranzakciós úton konzisztens állapotban van. A replikának az elsődleges teljesítmény szintjét, mert az alkalmazás kihasználhatja a csak olvasható munkaterhelések nem extra a karbantartáshoz, hogy további kapacitást költség (olvasási kibővített). Ezzel a módszerrel a csak olvasható lekérdezések elkülönül a fő olvasási és írási terhelést, és nem lesz hatással a teljesítményét. Olvasási kibővített szolgáltatás célja az alkalmazásokhoz, amelyek logikailag tartalmazzák például írásvédett munkaterhelések elválasztva, és ezért sikerült kihasználja a további kapacitást anélkül, hogy az elsődleges. 

Az olvasási kibővített szolgáltatással az adott adatbázishoz, explicit módon aktiválnia kell az adatbázis létrehozásakor vagy ezek után a PowerShell használatával való konfigurációját megváltoztatásával a [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) vagy a [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) parancsmagok vagy az Azure Resource Manager REST API használatával a [- adatbázisok létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate) metódust.

Olvasási kibővített adatbázis engedélyezése után az adatbázishoz való kapcsolódás alkalmazásokat a rendszer kéri, vagy az olvasási és írási replikára vagy egy csak olvasható replika az adatbázist a következők szerint a `ApplicationIntent` tulajdonság, az alkalmazás konfigurálva kapcsolati karakterlánc. Információk a `ApplicationIntent` tulajdonság, lásd: [megadó alkalmazás szándéka](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Olvasási kibővített le van tiltva, vagy a ReadScale tulajdonsága egy nem támogatott szolgáltatási rétegben, ha az írható-olvasható replika, függetlenül az összes kapcsolat van irányítva a `ApplicationIntent` tulajdonság.  

> [!NOTE]
> Lehetőség olvasási kibővített a szabványos vagy egy általános célú adatbázis aktiválása annak ellenére, hogy nem okoz a csak olvasható útválasztási szánt munkamenet egy külön replikára. Ez történik, Standard vagy általános célú és a prémium szintű/üzleti kritikus rétegek közötti felfelé és lefelé méretezési meglévő alkalmazások támogatásához.  

Az olvasási kibővített funkció támogatja a szint a munkamenet-konzisztencia. A csak olvasható munkamenet újracsatlakozik-e után a replika elérhetetlensége kapcsolódási hiba okozhatja, ha azt egy másik replikára lehet átirányítani. Amíg nem valószínű, eredményezhet, amely elavult az adatkészlet feldolgozása. Ehhez hasonlóan az alkalmazások írja az adatokat, olvasási és írási munkamenet használatával, és azonnal beolvassa a csak olvasható munkamenet, esetén lehetséges, hogy az új adatok nem jelennek meg azonnal.

## <a name="conclusion"></a>Összegzés
Az Azure SQL Database az Azure platformon mélyen integrált, és nagymértékben függ, a Service Fabric tárhelyhiba-észlelés és a helyreállításra, Azure Storage blobs szolgáltatásban a data protection és a rendelkezésre állási zónák magasabb a hibatűrés. Egyszerre az Azure SQL adatbázis teljes mértékben kihasználja a mindig bekapcsolva technológia replikációt és feladatátvételt az SQL Server mezőben termékből. Ezek a technológiák együttesen lehetővé teszi, hogy az alkalmazások teljes vegyes tárolási modell előnyök megvalósításához, és a legnagyobb igénybevételt jelentő SLA-k támogatják. 

## <a name="next-steps"></a>További lépések

- További tudnivalók [Azure rendelkezésre állási zónák](../availability-zones/az-overview.md)
- További tudnivalók [Fabric szolgáltatás](../service-fabric/service-fabric-overview.md)
- További tudnivalók [az Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 
