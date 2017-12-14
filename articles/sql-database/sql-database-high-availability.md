---
title: "Magas rendelkezésre állású - Azure SQL Database szolgáltatásban |} Microsoft Docs"
description: "További tudnivalók az Azure SQL Database szolgáltatás magas rendelkezésre állású szolgáltatásait és funkcióit:"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
ms.assetid: 
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 12/13/2017
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: c0a140c959f14c2e8ceaddad5d323f0900be5d2f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>Magas rendelkezésre állású és az Azure SQL-adatbázis
Az Azure SQL adatbázis Platformszolgáltatási ajánlat kezdete óta a Microsoft tett a felhőalkalmazások nyújtotta ügyfeleink, amely a szolgáltatás részét magas rendelkezésre állású (HA), és az ügyfelek nem kell a fog működni, különleges logika hozzáadása, vagy magas rendelkezésre ÁLLÁSÚ körül döntéseket. A Microsoft kínál az ügyfelek egy SLA-t, és teljes hozzáféréssel a magas rendelkezésre ÁLLÁSÚ rendszer konfigurációjáról és működéséről karbantartása. A magas rendelkezésre ÁLLÁSÚ SLA régióban SQL-adatbázis vonatkozik, és nem nyújt védelmet azokban az esetekben a teljes tartomány hibák, amelyek kívül az irányítása alá tartozó tényezők miatt (például természeti katasztrófa, war, működik-e terrorakció is történhet, lázadások, kormányzati művelet vagy egy hálózati vagy az eszköz nem külső az adatközpontokban, beleértve az ügyfél telephelyén vagy ügyfél telephelyén és az Adatközpont között).

A magas rendelkezésre ÁLLÁSÚ probléma lemezterület leegyszerűsítése Microsoft használja a következő előfeltételek:
1.  Hardver- és hibák előbb vagy utóbb elkerülhetetlenné
2.  Műveleti személyzet vétett hibákhoz vezethet
3.  Tervezett karbantartási műveletek miatt leállások esetén 

Amíg ilyen egyéni események alkalomszerű, a felhőbeli skálázással, azok minden héten, ha nem minden nap. 

## <a name="fault-tolerant-sql-databases"></a>A hibatűrő SQL-adatbázisok
Az ügyfelek tervezheti meg a saját adatbázis rugalmasságát és kisebb rugalmasságát, az SQL Database szolgáltatás egészére. a szolgáltatás 99,99 % bekapcsolási ideje értelmetlen, ha "a" része a 0,01 %-os-adatbázisok, amelyek le. Minden adatbázis kell lennie a hibatűrő, és tartalék megoldás soha nem kell a véglegesített tranzakció elvesztését eredményezi. 

Adatok, az SQL-adatbázis közvetlen csatlakoztatott lemezek/VHD-alapú helyi tárolás (LS) és a prémium szintű Azure Storage lapblobokat-alapú távoli tárolás (r) használja. 
- Helyi tárolót használja a rendszer a Premium adatbázisokat és-készletek, magas IOPS-követelményekkel rendelkező OLTP alkalmazások tervezett. 
- Távoli tároló Basic és Standard szolgáltatási szinteket, small, cold vagy nagy adatbázisok tárolási rendszerre van szüksége, és számítási teljesítményt méretezését készült szolgál. Egyetlen oldalakra vonatkozó blob adatbázishoz és naplófájlokhoz, és beépített tárolókezelési replikációs és feladatátvételi mechanizmusok használják.

Mindkét esetben a replikáció, a tárhelyhiba-észlelés és a feladatátvételi mechanizmusok SQL-adatbázis teljesen automatizált, és emberi beavatkozás nélkül. Ez az architektúra győződjön meg arról, hogy véglegesített adatok soha nem elvész, és hogy adatok tartóssága elsőbbséget élvez az összes más célja.

Főbb előnyei:
- Az ügyfelek élvezheti teljes a replikált adatbázisok konfigurálása vagy bonyolult hardver, a szoftver, az operációs rendszer vagy a virtualizálási környezetek karbantartása nélkül.
- A rendszer teljes ACID tulajdonságok a relációs adatbázisok karbantartása.
- Feladatátvétel teljesen automatizált bármely véglegesített adatvesztés nélkül.
- Az elsődleges másodpéldányhoz kapcsolatok útválasztási dinamikusan kezeli a szolgáltatás nem szükséges alkalmazás logikával.
- Automatizált redundanciát a magas szintű külön díjfizetés nélkül van megadva.

> [!NOTE]
> A leírt magas rendelkezésre állású architektúra van, külön értesítés nélkül változhatnak. 

## <a name="data-redundancy"></a>Adatredundancia

A magas rendelkezésre állású megoldás az SQL-adatbázis alapuló [AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) technológia az SQL-kiszolgálóról, és működnek az LS és RS adatbázis is minimális eltérésekkel lehetővé teszi. LS konfigurációban AlwaysON szolgál az RS adatmegőrzési szolgál rendelkezésre állási (alacsony RTO). 

## <a name="local-storage"></a>Helyi tárterület

Az LS esetében minden adatbázisnak online állapotba a szolgáltatás (MS) belül a vezérlő gyűrű. Egy elsődleges és legalább két másodlagos replika (kvórum-készlet) nem található, a bérlő ring kiterjedő három független fizikai alrendszerek ugyanabban az adatközpontban. Az átjáró (GW) az elsődleges másodpéldány által küldött összes olvasási és írási, és az írási műveletek aszinkron módon replikálva vannak a másodlagos replikákon. SQL-adatbázis egy kvórum-alapú véglegesítési sémát használja, ha az adatok írása az elsődleges és a tranzakciók véglegesítése előtt legalább egy másodlagos másodpéldány.

A [Service Fabric](/azure/service-fabric/service-fabric-overview.md) feladatátvételi rendszer automatikusan újraépíti a replikákat, a csomópont meghibásodik, és kvórum-készlet tagsági karbantartott csomópontok egymástól, és csatlakoztassa a rendszer. Tervezett karbantartás gondosan koordinált, hogy megakadályozza a kvórum-set állapotra vált a replikakészlet minimális száma (általában 2) alatt. Premium adatbázisok esetén ez a modell működik, de van szükség a redundancia számítási és a tárolási összetevők, és egy magasabb költsége eredményez.

## <a name="remote-storage"></a>Távtároló

Távoli tárolókonfigurációkkal (Basic és Standard rétegek) pontosan egy másolatot az adatbázisról távoli blob-tároló, a tárolási rendszerek képességeinek tartósságot, a redundancia és a bit-rothadás észlelési megőrződik. 

A magas rendelkezésre állású architektúra által az alábbi ábrán látható:
 
![magas rendelkezésre állású architektúrája](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>Tárhelyhiba-észlelés és helyreállítás 
A nagy méretű elosztott rendszerek kell egy nagymértékben megbízható hiba rendszer, amely észlelni tudja megbízhatóan, hibák gyorsan, és az ügyfélnek a lehető legközelebb. Az SQL Database esetén ez a rendszer Azure Service Fabric alapul. 

Az elsődleges másodpéldány azonnal nyilvánvaló, amikor az elsődleges másodpéldány sikertelen volt, és a munka nem folytatható, mert az összes olvasási és írási kerül sor az elsődleges replikán először. Ez a folyamat léptessen elő egy másodlagos másodpéldány az elsődleges állapotát rendelkezik helyreállítási idő célkitűzése (RTO) = 30 másodpercet és a helyreállítási időkorlát (RPO) = 0. A 30 másodpercet RTO hatásának mérsékléséhez, az ajánlott úgy próbáljon újból többször a kapcsolat sikertelen kísérletek kisebb várakozási időt.

Ha egy másodlagos másodpéldány nem sikerül, az adatbázis egy minimális kvórum-készlet nem szemben a le van. A Service fabric kezdeményezi az újrakonfigurálás folyamata hasonló a következő sikertelen volt-e az elsődleges másodpéldány, ezért annak megállapításához, hogy a hiba állandó, rövid várakozás után egy másik másodlagos replika jön létre. Esetekben ideiglenes out szolgáltatási állapot, operációs rendszer hibát vagy frissítés, például egy új replika nem-t azonnal engedélyezéséhez indítsa a sikertelen csomópont. 

A távoli tárolókonfigurációkkal SQL-adatbázis funkcióit használja az AlwaysON Feladatátvevőfürt-adatbázishoz a frissítések során. Ehhez egy új SQL-példányhoz van hoz előre a tervezett frissítési esemény részeként, és csatolja, és helyreállítja az adatbázisfájlt a távtároló. Folyamat leállásából eredő hiba vagy egyéb nem tervezett események esetén a Windows Fabric a példány rendelkezésre állási kezeli, és helyreállítási utolsó lépésként csatol a távoli adatbázis-fájl.

## <a name="conclusion"></a>Összegzés
Az Azure SQL DB mélyen integrált a Azure platformon, és nagymértékben függ, a Service Fabric a tárhelyhiba-észlelés és a helyreállításról és az Azure Storage blobs szolgáltatásban a data protection. Egy időben Azure SQL adatbázis replikációs és feladatátvételi mezőben terméket SQL Server AlwaysOn technológiát használ. Ezek a technológiák együttesen lehetővé teszi, hogy az alkalmazások teljes vegyes tárolási modell előnyök megvalósításához, és a legnagyobb igénybevételt jelentő SLA-k támogatják. 

## <a name="next-steps"></a>Következő lépések

- További tudnivalók [Fabric szolgáltatás](/azure/service-fabric/service-fabric-overview.md)
- További tudnivalók [az Azure Traffic Manager](/traffic-manager/traffic-manager-overview.md) 
