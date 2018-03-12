---
title: "Magas rendelkezésre állású - Azure SQL Database szolgáltatásban |} Microsoft Docs"
description: "További tudnivalók az Azure SQL Database szolgáltatás magas rendelkezésre állású szolgáltatásait és funkcióit:"
keywords: 
services: sql-database
author: anosov1960
manager: jhubbard
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/07/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: a7771eebc8359a5de1c79328014f5ecc06c9673b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="high-availability-and-azure-sql-database"></a>Magas rendelkezésre állású és az Azure SQL-adatbázis
Az Azure SQL adatbázis Platformszolgáltatási ajánlat kezdete óta a Microsoft tett a felhőalkalmazások nyújtotta az ügyfelek, amelyek a szolgáltatás részét magas rendelkezésre állású (HA), és az ügyfelek nem szükséges üzemeltetéséhez, különleges logika hozzáadása, és magas rendelkezésre ÁLLÁSÚ körül döntéseket. A Microsoft fenntartja a magas rendelkezésre ÁLLÁSÚ rendszerkonfiguráció és a művelet, teljes hozzáféréssel, szolgáltatásiszint-szerződésben garantált kínál az ügyfeleknek. A magas rendelkezésre ÁLLÁSÚ SLA régióban SQL-adatbázis vonatkozik, és nem nyújt védelmet a teljes tartomány hibák, amely kívül a Microsoft irányítása alá tartozó tényezők miatt esetekben (például természeti katasztrófa, war, tevékenységéért terrorakció is történhet, lázadások, kormányzati műveletet, vagy egy hálózati vagy az eszköz hiba a Microsoft adatközpontokban, beleértve az ügyfél helyeken vagy a felhasználói helyek és a Microsoft-adatközpont közti külső).

A magas rendelkezésre ÁLLÁSÚ probléma lemezterület leegyszerűsítése Microsoft használja a következő előfeltételek:
1.  Hardver- és hibák előbb vagy utóbb elkerülhetetlenné
2.  Műveleti személyzet vétett hibákhoz vezethet
3.  Tervezett karbantartási műveletek miatt leállások esetén 

Amíg ilyen egyéni események alkalomszerű, a felhőbeli skálázással, azok minden héten, ha nem minden nap. 

## <a name="fault-tolerant-sql-databases"></a>A hibatűrő SQL-adatbázisok
Az ügyfelek tervezheti meg a saját adatbázis rugalmasságát és kisebb rugalmasságát, az SQL Database szolgáltatás egészére. a szolgáltatás 99,99 % bekapcsolási ideje értelmetlen, ha "a" része a 0,01 %-os-adatbázisok, amelyek le. Minden adatbázis kell lennie a hibatűrő, és tartalék megoldás soha nem kell a véglegesített tranzakció elvesztését eredményezi. 

Adatok, az SQL-adatbázis közvetlen csatlakoztatott lemezek/VHD-alapú helyi tárolás (LS) és a prémium szintű Azure Storage lapblobokat-alapú távoli tárolás (r) használja. 
- Helyi tárolót használja a rendszer a Premium adatbázisokat és-készletek, amelyek alapvető fontosságú OLTP alkalmazások magas IOPS-követelményekkel rendelkező tervezett. 
- Távoli tároló Basic és Standard szolgáltatásszintek célú költségvetés üzleti munkaterhelések tárolási rendszerre van szüksége, és számítási teljesítményt méretezését készült szolgál. Egyetlen oldalakra vonatkozó blob adatbázishoz és naplófájlokhoz, és beépített tárolókezelési replikációs és feladatátvételi mechanizmusok használják.

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

A magas rendelkezésre állású megoldás az SQL-adatbázis alapul [Alwayson](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) technológia az SQL-kiszolgálóról, és működnek az LS és RS adatbázis is minimális eltérésekkel lehetővé teszi. Az LS, mindig ON szolgál az RS adatmegőrzési beállítás (alacsony RTO) rendelkezésre állási használja azt. 

## <a name="local-storage-configuration"></a>Helyi tárolás beállítása

Ebben a konfigurációban az egyes adatbázisok online állapotba a szolgáltatás (MS) belül a vezérlő gyűrű. Egy elsődleges és legalább két másodlagos replika (kvórum-készlet) nem található, a bérlő ring kiterjedő három független fizikai alrendszerek ugyanabban az adatközpontban. Az átjáró (GW) az elsődleges másodpéldány által küldött összes olvasási és írási, és az írási műveletek aszinkron módon replikálva vannak a másodlagos replikákon. SQL-adatbázis egy kvórum-alapú véglegesítési sémát használja, ha az adatok írása az elsődleges és a tranzakciók véglegesítése előtt legalább egy másodlagos másodpéldány.

A [Service Fabric](/azure/service-fabric/service-fabric-overview.md) feladatátvételi rendszer automatikusan újraépíti a replikákat, a csomópont meghibásodik, és kvórum-készlet tagsági karbantartott csomópontok egymástól, és csatlakoztassa a rendszer. Tervezett karbantartás gondosan koordinált, hogy megakadályozza a kvórum-set állapotra vált a replikakészlet minimális száma (általában 2) alatt. Premium adatbázisok esetén ez a modell működik, de van szükség a redundancia számítási és a tárolási összetevők, és egy magasabb költsége eredményez.

## <a name="remote-storage-configuration"></a>Távoli tárolás beállítása

Távoli tárolókonfigurációkkal (Basic és Standard rétegek) pontosan egy másolás megőrződik távoli blob, a rendszer lehetőségeit tartósságot, a redundancia és a bit-rothadás észlelési használatával. 

A magas rendelkezésre állású architektúra által az alábbi ábrán látható:
 
![magas rendelkezésre állású architektúrája](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>Hibaészlelés és helyreállítás 
A nagy méretű elosztott rendszerek kell egy nagymértékben megbízható hiba rendszer, amely észlelni tudja megbízhatóan, hibák gyorsan, és az ügyfélnek a lehető legközelebb. Az SQL Database esetén ez a rendszer Azure Service Fabric alapul. 

Az elsődleges másodpéldány azonnal nyilvánvaló, amikor az elsődleges másodpéldány sikertelen volt, és a munka nem folytatható, mert az összes olvasási és írási kerül sor az elsődleges replikán először. Ez a folyamat léptessen elő egy másodlagos másodpéldány az elsődleges állapotát rendelkezik helyreállítási idő célkitűzése (RTO) = 30 másodpercet és a helyreállítási időkorlát (RPO) = 0. A 30 másodpercet RTO hatásának mérsékléséhez, az ajánlott úgy próbáljon újból többször a kapcsolat sikertelen kísérletek kisebb várakozási időt.

Ha egy másodlagos másodpéldány nem sikerül, az adatbázis egy minimális kvórum-készlet nem szemben a le van. A Service fabric kezdeményezi az újrakonfigurálás folyamata hasonló a következő sikertelen volt-e az elsődleges másodpéldány, ezért annak megállapításához, hogy a hiba állandó, rövid várakozás után egy másik másodlagos replika jön létre. Esetekben ideiglenes out szolgáltatási állapot, operációs rendszer hibát vagy frissítés, például egy új replika nem-t azonnal engedélyezéséhez indítsa a sikertelen csomópont. 

A távoli tárolókonfigurációkkal SQL-adatbázis funkcióit használja az Alwayson feladatátvételi adatbázisokhoz frissítéskor. Ehhez egy új SQL-példányhoz van hoz előre a tervezett frissítési esemény részeként, és csatolja, és helyreállítja az adatbázisfájlt a távtároló. Folyamat leállásából eredő hiba vagy egyéb nem tervezett események esetén a Windows Fabric a példány rendelkezésre állási kezeli, és helyreállítási utolsó lépésként csatol a távoli adatbázis-fájl.

## <a name="zone-redundant-configuration-preview"></a>Redundáns zónakonfigurációk (előzetes verzió)

Alapértelmezés szerint a helyi tárolókonfigurációkkal kvórum-set replikáit ugyanabban az adatközpontban jönnek létre. Bevezetésével [Azure rendelkezésre állási zónák](/azure/availability-zones/az-overview.md), arra, hogy a különböző replikák a kvórum beállítása másik rendelkezésre állási zónákhoz ugyanabban a régióban van. Elkerülése érdekében a hibaérzékeny pontok kialakulását, a vezérlő gyűrű is duplikált több zóna között, három átjáró körök (GW). Egy adott átjáró ring útválasztást vezérli [Azure Traffic Manager](/traffic-manager/traffic-manager-overview.md) (ATM). A redundáns zónakonfigurációk további adatbázis-redundancia nem hoz létre, mert a rendelkezésre állási zónák a prémium szintű szolgáltatási rétegben használata nincs további elérhető költség. A zóna redundáns adatbázis kiválasztásával tehet a Premium adatbázisokat rugalmas sokkal nagyobb mennyiségű hibák, beleértve a katasztrofális datacenter kimaradások, az alkalmazás logikája módosítás nélkül. Bármely létező Premium adatbázis vagy a készlet átválthat a zóna redundáns konfigurációra.

Mivel a redundáns kvórum-zónakészlet replikák néhány távolsága a különböző adatközpontokban van, a hálózati késés növelheti a véglegesítés ideje, és így csökkenti a teljesítményt az egyes OLTP-munkaterhelések. Mindig visszatérhet a egyetlen zónakonfigurációk által a zóna redundancia beállításnak a letiltása. Ez a folyamat egy Adatműveletek mérete, és a rendszeres szolgáltatásiszint-célkitűzés (SLO) frissítési hasonló. A folyamat végén az adatbázis vagy a készletbe telepítenek át egy zóna redundáns gyűrű egy zóna gyűrű vagy fordítva.

> [!IMPORTANT]
> Zóna redundáns adatbázisok és rugalmas készletek csak támogatottak a prémium szolgáltatásszintet. Nyilvános előzetes verziójához, biztonsági másolatok és naplózása során a rekordok RA-GRS tárolási tárolódnak, és ezért nem lehet egy zóna kiterjedő leállás esetén automatikusan elérhető. 

A magas rendelkezésre állású architektúra redundáns zóna verziója által az alábbi ábrán látható:
 
![magas rendelkezésre állású architektúra zónaredundáns](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="conclusion"></a>Összegzés
Az Azure SQL Database az Azure platformon mélyen integrált, és nagymértékben függ, a Service Fabric tárhelyhiba-észlelés és a helyreállításra, Azure Storage blobs szolgáltatásban a data protection és a rendelkezésre állási zónák magasabb a hibatűrés. Egyszerre az Azure SQL adatbázis teljes mértékben kihasználja a mindig bekapcsolva technológia replikációt és feladatátvételt az SQL Server mezőben termékből. Ezek a technológiák együttesen lehetővé teszi, hogy az alkalmazások teljes vegyes tárolási modell előnyök megvalósításához, és a legnagyobb igénybevételt jelentő SLA-k támogatják. 

## <a name="next-steps"></a>További lépések

- További tudnivalók [Azure rendelkezésre állási zónák](/azure/availability-zones/az-overview.md)
- További tudnivalók [Fabric szolgáltatás](/azure/service-fabric/service-fabric-overview.md)
- További tudnivalók [az Azure Traffic Manager](/traffic-manager/traffic-manager-overview.md) 
