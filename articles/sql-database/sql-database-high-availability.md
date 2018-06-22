---
title: Magas rendelkezésre állású - Azure SQL Database szolgáltatásban |} Microsoft Docs
description: 'További tudnivalók az Azure SQL Database szolgáltatás magas rendelkezésre állású szolgáltatásait és funkcióit:'
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: 4e1963e97a7458db8badb63e28dbc3d215ad88b2
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309630"
---
# <a name="high-availability-and-azure-sql-database"></a>Magas rendelkezésre állású és az Azure SQL-adatbázis

Az Azure SQL-adatbázis egy magas rendelkezésre állású adatbázis Platform szolgáltatás, amely biztosítja, hogy az adatbázis be-és idő, anélkül, hogy karbantartási és állásidőt futó 99,99 %. Ez az egy teljes körűen felügyelt, amely biztosítja, hogy az SQL Server adatbázis mindig frissíteni vagy javítani anélkül, hogy az hatással lenne a számítási feladatok Azure felhőalapú SQL Server adatbázismotor-folyamat. Az Azure SQL Database gyorsan helyreállíthatja még akkor is, a kritikus fontosságú esetekben annak biztosítása, hogy az adatok mindig elérhető.

Azure platform teljesen minden Azure SQL Database kezeli, és biztosítja, hogy az adatvesztés nélküli és a magas százalékos arányú adatok rendelkezésre állását. Azure automatikusan kezelje a javítását, biztonsági mentések, replikációs, tárhelyhiba-észlelés, alapul szolgáló potenciális hardver, szoftver-vagy hálózati, központi telepítésének hibajavításokat tartalmaz, feladatátvétel, adatbázis-frissítés és egyéb karbantartási műveleteket. SQL Server mérnökök alkalmaztunk a best-known eljárásokat, győződjön meg arról, hogy a karbantartási műveleteket befejeződött-e az adatbázis életét 0,01 % idején belül. Ez az architektúra győződjön meg arról, hogy véglegesített adatok soha nem elvész, és hogy karbantartási műveleteket az nem befolyásolja a munkaterhelés célja. Nincsenek, a karbantartási időszakok vagy állásidőt, érdemes beállítani, hogy állítsa le a munkaterhelés, amíg az adatbázis frissítése vagy karbantartása. Az Azure SQL-adatbázis magas rendelkezésre állást biztosítja, hogy az, hogy az adatbázis soha nem lesznek az kritikus hibapont a szoftver-architektúrában.

Alkalmazza az Azure SQL-ben két magas rendelkezésre állású modell áll:

- Standard/általános célú modell, amely a rendelkezésre állás, de néhány teljesítménycsökkenés karbantartási tevékenységek során 99,99 %.
- Premium/üzleti kritikus modell, amely is biztosít minimális a teljesítményt negatívan befolyásoló rendelkezésre állás 99,99 % a számítási feladatok még karbantartási tevékenységek során.

Azure rendszerre frissül, és javításokkal alapul szolgáló operációs rendszert, illesztőprogramokat és SQL Server adatbázismotor transzparens módon a minimális le-, amikor a végfelhasználók számára. Azure SQL Database fut az SQL Server adatbázismotor és a Windows operációs rendszer legújabb stabil verzióját, és a felhasználók többsége nem észre, hogy a frissítések folyamatosan hajtja végre.

## <a name="standard-availability"></a>Standard rendelkezésre állása

Standard rendelkezésre állás 99,99 % SLA-t a szabványos/Basic vagy általános célú rétegek alkalmazott hivatkozik. Rendelkezésre állási választani egymástól a számítási és tárolási rétegek használatával érhető el. A szabványos rendelkezésre állási modellben két réteg vezetünk be:

- Állapot nélküli számítási rétegét, amely a sqlserver.exe folyamat fut, és csak átmeneti és a gyorsítótárazott adatokat (például – tervgyorsítótár, pufferkészlet, oszlop tároló alkalmazáskészlet) tartalmaz. Ez az állapot nélküli SQL Server-csomópont üzemelteti Azure Service Fabric folyamat inicializálja, a csomópont állapotát vezérli és hajt végre feladatátvételt egy másik helyre, ha szükséges.
- Állapot-nyilvántartó adatrétege az adatbázisfájlok (.mdf/.ldf), Azure Premium Storage lemezek vannak tárolva. Az Azure Storage garantálja, hogy az összes rekord lévő bármely adatbázisfájl adatvesztés nélküli lesz. Az Azure Storage beépített rendelkezésre állási/adatredundanciát, amely biztosítja, hogy a naplófájl minden bejegyzéséhez vagy adatfájlban lap maradnak akkor is, ha az SQL Server-folyamat leállásából eredő rendelkezik.

Amikor database engine vagy az operációs rendszer frissítése, vagy az Sql Server-folyamat néhány kritikus problémát észlel, Azure Service Fabric áthelyezi az állapot nélküli SQL Server-folyamat egy másik állapotmentes számítási csomópont. Ez nem befolyásolja az adatok Azure Storage-rétegben, és adatok/naplófájlok újonnan inicializált SQL Server-folyamat vannak csatolva. Várt feladatátvételi idő másodpercben is. Ez a folyamat biztosítja, hogy rendelkezésre állás 99,99 %, de előfordulhat, hogy egyes részeinek teljesítményére nagy terhelést a váltás ideje miatt futtató rendelkezik, és az új SQL Server-csomópont tény cold gyorsítótár kezdődik.

## <a name="premium-availability"></a>Prémium szintű rendelkezésre állás

Prémium szintű rendelkezésre állást engedélyezték Azure SQL adatbázis Premium szint, és intenzív munkaterhelések esetén, amelyek működését zavarják a teljesítményre gyakorolt hatás a folyamatos karbantartási műveletek miatti tervezték.

Azure SQL adatbázis premium modellben egyesíti a számítási és tárolási csomóponton. Az SQL Server adatbázis-kezelő folyamat és az alapul szolgáló mdf/ldf-fájlok kerülnek, ugyanazon a csomóponton a helyileg csatlakoztatott SSD-tárolóba, a munkaterhelés kis késést biztosít.

Magas rendelkezésre állású van megvalósítva a szabványos [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Minden adatbázisa elérhető az ügyfél munkaterhelési egy elsődleges adatbázis, és néhány másodlagos folyamatok adatok másolatát tartalmazó adatbázis-csomópontból álló fürtben. Az elsődleges csomópont folyamatosan leküldéses értesítések a módosítások a másodlagos csomópont érdekében győződjön meg arról, hogy az adatok esetén érhető el, a másodlagos replikákon az elsődleges csomópont bármilyen okból összeomlik. Feladatátvételi kezeli az SQL Server adatbázismotor – egy másodlagos másodpéldány az elsődleges csomópont válik, és egy új másodlagos replikára annak biztosítása érdekében a fürt elég csomópont jön létre. A munkaterhelés a rendszer automatikusan átirányítja az új elsődleges csomóponton. Feladatátvételi idő ezredmásodpercben, és az új elsődleges példány azonnal készen áll a folytatásra küldött kérelmek.

## <a name="zone-redundant-configuration-preview"></a>Redundáns zónakonfigurációk (előzetes verzió)

Alapértelmezés szerint a helyi tárolókonfigurációkkal kvórum-set replikáit ugyanabban az adatközpontban jönnek létre. Bevezetésével [Azure rendelkezésre állási zónák](../availability-zones/az-overview.md), arra, hogy a különböző replikák a kvórum beállítása másik rendelkezésre állási zónákhoz ugyanabban a régióban van. Elkerülése érdekében a hibaérzékeny pontok kialakulását, a vezérlő gyűrű is duplikált több zóna között, három átjáró körök (GW). Egy adott átjáró ring útválasztást vezérli [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Mivel redundáns zónakonfigurációk nem hoz létre további adatbázis-redundancia biztosítása, a prémium szintű rendelkezésre állási zónával használatát, vagy a szolgáltatásszintek üzleti kritikus (előzetes verzió) érhető el: nem további költség. A zóna redundáns adatbázis kiválasztásával biztosíthatja, hogy a prémium szintű, vagy fontos üzleti (előzetes verzió) adatbázisok rugalmas, hogy egy sokkal nagyobb hibák, beleértve a katasztrofális datacenter kimaradások, az alkalmazás logikája módosítás nélkül. Bármely létező Premium vagy a fontos üzleti adatbázisok vagy készletek (előzetes verzió) átválthat a zóna redundáns konfigurációra.

Mivel a redundáns kvórum-zónakészlet replikák néhány távolsága a különböző adatközpontokban van, a hálózati késés növelheti a véglegesítés ideje, és így csökkenti a teljesítményt az egyes OLTP-munkaterhelések. Mindig visszatérhet a egyetlen zónakonfigurációk által a zóna redundancia beállításnak a letiltása. Ez a folyamat egy Adatműveletek mérete, és a rendszeres szolgáltatásiszint-célkitűzés (SLO) frissítési hasonló. A folyamat végén az adatbázis vagy a készletbe telepítenek át egy zóna redundáns gyűrű egy zóna gyűrű vagy fordítva.

> [!IMPORTANT]
> Zóna redundáns adatbázisok és rugalmas készletek jelenleg csak akkor támogatott a prémium szolgáltatásszintet. Nyilvános előzetes verziójához, biztonsági másolatok és naplózása során a rekordok RA-GRS tárolási tárolódnak, és ezért nem lehet egy zóna kiterjedő leállás esetén automatikusan elérhető. 

A magas rendelkezésre állású architektúra redundáns zóna verziója által az alábbi ábrán látható:
 
![magas rendelkezésre állású architektúra zónaredundáns](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Olvassa el a kibővített
Ismertetettek szerint Premium és fontos üzleti (előzetes verzió) szolgáltatás rétegek emelés Kvórum beállítása és az AlwaysOn technológia a magas rendelkezésre állás egyetlen zóna és a redundáns zónabeállítások egyaránt. A AlwasyON előnyeit egyike, hogy a replikákat mindig a tranzakciós úton konzisztens állapotban van. A replikának az elsődleges teljesítmény szintjét, mert az alkalmazás kihasználhatja a csak olvasható munkaterhelések nem extra a karbantartáshoz, hogy további kapacitást költség (olvasási kibővített). Ezzel a módszerrel a csak olvasható lekérdezések elkülönül a fő olvasási és írási terhelést, és nem lesz hatással a teljesítményét. Olvasási kibővített szolgáltatás célja az alkalmazásokhoz, amelyek logikailag tartalmazzák például írásvédett munkaterhelések elválasztva, és ezért sikerült kihasználja a további kapacitást anélkül, hogy az elsődleges. 

Az olvasási kibővített szolgáltatással az adott adatbázishoz, explicit módon aktiválnia kell az adatbázis létrehozásakor vagy ezek után a PowerShell használatával való konfigurációját megváltoztatásával a [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) vagy a [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) parancsmagok vagy az Azure Resource Manager REST API használatával a [- adatbázisok létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate) metódust.

Olvasási kibővített adatbázis engedélyezése után az adatbázishoz való kapcsolódás alkalmazásokat a rendszer kéri, vagy az olvasási és írási replikára vagy egy csak olvasható replika az adatbázist a következők szerint a `ApplicationIntent` tulajdonság, az alkalmazás konfigurálva kapcsolati karakterlánc. Információk a `ApplicationIntent` tulajdonság, lásd: [megadó alkalmazás szándéka](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Olvasási kibővített le van tiltva, vagy a ReadScale tulajdonsága egy nem támogatott szolgáltatási rétegben, ha az írható-olvasható replika, függetlenül az összes kapcsolat van irányítva a `ApplicationIntent` tulajdonság.  

> [!NOTE]
> Lehetőség olvasási kibővített a szabványos vagy egy általános célú adatbázis aktiválása annak ellenére, hogy nem okoz a csak olvasható útválasztási szánt munkamenet egy külön replikára. Ez történik, Standard vagy általános célú és a prémium szintű/üzleti kritikus rétegek közötti felfelé és lefelé méretezési meglévő alkalmazások támogatásához.  

Az olvasási kibővített funkció támogatja a szint a munkamenet-konzisztencia. A csak olvasható munkamenet újracsatlakozik-e után a replika elérhetetlensége kapcsolódási hiba okozhatja, ha azt egy másik replikára lehet átirányítani. Amíg nem valószínű, eredményezhet, amely elavult az adatkészlet feldolgozása. Ehhez hasonlóan az alkalmazások írja az adatokat, olvasási és írási munkamenet használatával, és azonnal beolvassa a csak olvasható munkamenet, esetén lehetséges, hogy az új adatok nem jelennek meg azonnal.

## <a name="conclusion"></a>Összegzés
Az Azure SQL Database az Azure platformon mélyen integrált, és nagymértékben függ, a Service Fabric tárhelyhiba-észlelés és a helyreállításra, Azure Storage blobs szolgáltatásban a data protection és a rendelkezésre állási zónák magasabb a hibatűrés. Egyszerre az Azure SQL adatbázis teljes mértékben kihasználja az Always On rendelkezésre állási csoportnak technológia replikációt és feladatátvételt az SQL Server mezőben termékből. Ezek a technológiák együttesen lehetővé teszi, hogy az alkalmazások teljes vegyes tárolási modell előnyök megvalósításához, és a legnagyobb igénybevételt jelentő SLA-k támogatják. 

## <a name="next-steps"></a>További lépések

- További tudnivalók [Azure rendelkezésre állási zónák](../availability-zones/az-overview.md)
- További tudnivalók [Fabric szolgáltatás](../service-fabric/service-fabric-overview.md)
- További tudnivalók [az Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 
