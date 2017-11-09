---
title: "Az Azure SQL adatszinkronizálás (előzetes verzió) |} Microsoft Docs"
description: "Ez az áttekintés bemutatja az Azure SQL adatszinkronizálás (előzetes verzió)"
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 5c4509bc1d05bc422f6bc5599d4635020ded63e9
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-azure-sql-data-sync-preview"></a>Szinkronizálja az adatokat több felhőalapú és helyszíni adatbázisokat az Azure SQL adatszinkronizálás (előzetes verzió)

SQL adatszinkronizálás egy olyan szolgáltatás, az Azure SQL Database, amely lehetővé teszi, hogy szinkronizálja az adatokat több SQL-adatbázisok és SQL Server-példányok kiválasztása kétirányúan épül.

Adatszinkronizálás a egy szinkronizálás csoport fogalmát körül alapul. A szinkronizálás csoport olyan a szinkronizálni kívánt adatbázisok.

A szinkronizálás csoport tulajdonságai a következők:

-   A **szinkronizálási séma** írja le, mely adatok szinkronizálása.

-   A **szinkronizálási irány** kétirányú lehet, vagy csak egy irányban áramolhasson. Ez azt jelenti, hogy a szinkronizálás lehet *tagra Hub* vagy *Hub tag*, vagy mindkettőt.

-   A **szinkronizálási intervallum** milyen gyakran szinkronizálásra kerül sor.

-   A **ütközés névfeloldási házirend** csoport szintű házirend, amely lehet *Hub wins* vagy *tag wins*.

Adatszinkronizálás küllős topológia használatával szinkronizálja az adatokat. Megadhat egy adatbázis a csoport Hub-adatbázisként. Az adatbázis többi tag adatbázisok. Szinkronizálási következik be, csak a központ és az egyes tagok között.
-   A **Hub adatbázis** egy Azure SQL Database kell lennie.
-   A **tag adatbázisok** SQL-adatbázisok, a helyszíni SQL Server-adatbázisok vagy Azure virtuális gépeken futó SQL Server-példányokat.
-   A **Sync-adatbázis** Adatszinkronizálás a metaadatok és naplófájl tartalmazza. A Sync-adatbázis nem lehet egy Azure SQL-adatbázis és a központ adatbázis ugyanabban a régióban található. A Sync-adatbázis létrehozott felhasználói és felhasználói tulajdonban lévő.

> [!NOTE]
> Ha egy a helyi adatbázist használ, hogy [egy helyi ügynök konfigurálása.](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-sql-data-sync)

![Adatbázisok között szinkronizálja az adatokat](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Használati adatok szinkronizálása

Adatszinkronizálás hasznos olyan esetekben, amikor adatokat kell több Azure SQL-adatbázisok vagy SQL Server-adatbázisok között naprakészen kell tartani. Az alábbiakban a fő alkalmazási helyzetei adatszinkronizálás:

-   **Hibrid adatszinkronizálás:** az adatszinkronizálás, hálózati adaptere esetében megtarthatja az adatok szinkronizálása a helyszíni adatbázisokat és az Azure SQL-adatbázisok számára lehetővé teszi hibrid alkalmazások között. Ezt a képességet is konfigurálja, hogy a felhasználóknak, akik a felhőre történő készül, és mit kell állítania néhány az alkalmazásokban az Azure-ban.

-   **Elosztott alkalmazások:** sok esetben hasznos különböző terhelésekhez külön különböző adatbázisok közötti. Például ha olyan nagy éles adatbázist, de is futtatni szeretné a jelentéskészítés és elemzés munkaterhelés ezeken az adatokon, érdemes további munkaterhelés második adatbázisában. Ez a megközelítés minimalizálja a teljesítmény a a termelési számítási feladatok. Adatszinkronizálás segítségével a két adatbázis szinkronizálva tartása.

-   **Globálisan elosztott alkalmazások:** számos vállalat több régióban, illetve akár több ország kiterjedhetnek. Hálózati késés minimalizálása érdekében célszerű előkészítheti az adatait az Önhöz legközelebb eső régiót. Az adatszinkronizálás hogy könnyedén képes tárolni adatbázisok szinkronizálása a világ különböző régiókban.

Adatszinkronizálás nincs megfelelő, az alábbi helyzetekben:

-   Vészhelyreállítás

-   Olvassa el a skála

-   ETL (OLTP való OLAP)

-   A helyszíni SQL Server az Azure SQL Database-áttelepítés

## <a name="how-does-data-sync-work"></a>Hogyan működik az adatszinkronizálás? 

-   **Adatok változásainak követése:** adatszinkronizálás segítségével insert módosításokat követi nyomon, frissítési és törlési eseményindító. A módosítások tárolja, amely a felhasználói adatbázis oldali táblához.

-   **Adatok szinkronizálása:** adatszinkronizálás célja egy küllős modellben. A központ külön-külön minden tag szinkronizál. Módosításokat a központi letöltődnek a tagja, és majd a tagja változtatásokat szeretne az elosztóhoz feltöltése.

-   **Az ütközések feloldása:** adatszinkronizálás ütközések feloldása, két lehetőséget biztosít *Hub wins* vagy *tag wins*.
    -   Ha *Hub wins*, a módosítások a központban mindig felülírja a tag változásai.
    -   Ha *tag wins*, a felülírási módosításához központban változásait. Ha egynél több tag, végső értéke függ, mely tag szinkronizálásának először.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Milyen gyakran adatszinkronizálás adatok bármikor szinkronizálhatók a? 
A minimális gyakoriság 5 perc nem.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Adatszinkronizálás segítségével csak SQL Server helyszíni adatbázisok közötti szinkronizálás? 
Közvetlenül nem. Szinkronizálhatja a helyszíni SQL Server-adatbázisok közötti közvetve, azonban Hub-adatbázis létrehozása az Azure-ban, és majd a szinkronizálási csoportba való felvételekor a helyszíni adatbázisokat.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>Is szeretnék adatait egy üres adatbázist a termelési adatbázisból származó adatszinkronizálás segítségével, és majd őrizzük szinkronizálva? 
Igen. A séma manuális létrehozása az új adatbázis alkalmazott parancsprogrammal azt az eredeti. Miután létrehozta a séma, vegye fel a táblák egy szinkronizálási csoportba másolja az adatokat, és legyen szinkronizálva.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Miért látom azt táblázatot, amely nem hozta létre?  
Adatszinkronizálás ügyféloldali táblák az adatbázisban változáskövetési hoz létre. Ne törölje őket, vagy adatszinkronizálás nem működik.
   
### <a name="i-got-an-error-message-that-said-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-the-error"></a>Figyelmeztető hibaüzenet jelenik meg, az említett "NULL érték nem lehet beszúrni az oszlop \<oszlop\>. Oszlop nem szerepelhet nulls érték." Ez mit jelent, és hogyan lehet javítsa a hibát? 
Ez a hibaüzenet azt jelzi, a két alábbi problémák egyike:
1.  Előfordulhat, hogy a tábla elsődleges kulcs nélkül. A probléma megoldásához, még a szinkronizálás minden tábla elsődleges kulcs hozzáadása.
2.  A CREATE INDEX utasítás WHERE záradék lehet. Szinkronizálási nem kezeli ezt az állapotot. A probléma megoldásához távolítsa el a WHERE záradékot, vagy manuálisan végezze el a módosításokat az összes adatbázisra. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Hogyan adatszinkronizálás körkörös hivatkozásokat kezelni? Ez azt jelenti, ha ugyanazokat az adatokat több szinkronizálási csoportban van-e szinkronizálva, és emiatt tartja módosítása?
Adatszinkronizálás. a körkörös hivatkozások nem tud kezelni. Feltétlenül elkerüljék azokat. 

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Hogyan exportálása és importálása az adatszinkronizálás adatbázis?
Adatbázis exportálása után egy `.bacpac` fájlt, és importálja a fájlt egy új adatbázis létrehozásához, meg kell nyitnia a következő két dolog adatszinkronizálás használni az új adatbázis:
1.  Az adatszinkronizálás objektumok és kiszolgálóoldali táblák tisztítása a **új adatbázis** használatával [ezt a parancsfájlt](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Ez a parancsfájl az összes szükséges adatszinkronizálás objektum törli az adatbázisból.
2.  Hozza létre újra az új adatbázis szinkronizálási csoportban. Ha már nincs szüksége a régi szinkronizálású csoport, törölje azt.

## <a name="sync-req-lim"></a>Követelmények és korlátozások

### <a name="general-requirements"></a>Általános követelmények

-   Minden tábla elsődleges kulccsal kell rendelkeznie. Ne módosítsa az összes sort az elsődleges kulcs értékét. Ha ehhez, törölni a sort, és hozza létre újra az új elsődleges kulcs értéke. 

-   Egy táblázat azonosító oszlopot, amely nem az elsődleges kulcs nem lehet.

-   Objektumok (adatbázisok, táblákat és oszlopokat) nevét nem tartalmazza a nyomtatható karakterek pont (.), bal oldali szögletes zárójel ([), és jobb szögletes zárójel (]).

-   Pillanatkép-elkülönítés engedélyezve kell lennie. További információk: [pillanatkép-elkülönítést az SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-considerations"></a>Általános megfontolások

#### <a name="eventual-consistency"></a>Végleges konzisztencia
Mivel adatszinkronizálás eseményindító-alapú, a tranzakciós konzisztencia nem garantált. A Microsoft biztosítja, hogy minden módosításai felé, és nem Adatszinkronizálás a adatvesztéshez vezethet.

#### <a name="performance-impact"></a>Teljesítményre gyakorolt hatás
Adatok szinkronizálása által beszúrási, frissítési és törlési eseményindítók követni a változásokat. Ügyféloldali táblák létrehozza a változáskövetési felhasználói adatbázisban. E módosítás követési tevékenységek hatással vannak az adatbázisban munkaterhelés. A szolgáltatási rétegben felmérése, és ha szükséges.

### <a name="general-limitations"></a>Általános korlátozások

#### <a name="unsupported-data-types"></a>Nem támogatott adattípusok

-   A FileStream

-   SQL/CLR UDT

-   XmlSchemaCollection gyűjteményben (XML támogatott)

-   Kurzor esetén Timestamp, Hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>A szolgáltatás és az adatbázis-dimenziókra korlátozásai

| **Dimenziók**                                                      | **Korlát**              | **Megkerülő megoldás**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Szinkronizálási csoportok maximális száma bármely adatbázis is tartozik.       | 5                      |                             |
| Több végpont már egy szinkronizálási csoportban              | 30                     | Több szinkronizálási csoportok létrehozása |
| A helyi végpont egy szinkronizálási csoportban maximális száma. | 5                      | Több szinkronizálási csoportok létrehozása |
| Adatbázis, a tábla, a séma és az oszlop neve                       | nevenként 50 karakter hosszú lehet |                             |
| A szinkronizálás csoport táblák                                          | 500                    | Több szinkronizálási csoportok létrehozása |
| A táblázat egy szinkronizálási csoportban                              | 1000                   |                             |
| Adatok sorméret táblán                                        | 24 mb                  |                             |
| Minimális szinkronizálási időköz                                           | 5 perc              |                             |
|||

## <a name="next-steps"></a>Következő lépések

SQL adatszinkronizálás kapcsolatos további információkért lásd:

-   [Ismerkedés az Azure SQL-adatok szinkronizálása](sql-database-get-started-sql-data-sync.md)
-   [Ajánlott eljárások az Azure SQL-adatok szinkronizálása](sql-database-best-practices-data-sync.md)
-   [Az Azure SQL adatszinkronizálás problémák elhárítása](sql-database-troubleshoot-data-sync.md)

-   PowerShell-példák bemutatják, hogyan konfigurálja az SQL adatszinkronizálás befejezése:
    -   [A PowerShell szolgáltatás használatával több Azure SQL-adatbázisok közötti szinkronizálása](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Egy Azure SQL-adatbázis és a helyszíni SQL Server-adatbázisok közötti szinkronizálása a PowerShell használatával](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Töltse le az SQL Data szinkronizálási REST API dokumentációja](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL-adatbázis kapcsolatos további információkért lásd:

-   [SQL-adatbázis – áttekintés](sql-database-technical-overview.md)
-   [Adatbázis életciklusának kezelésére](https://msdn.microsoft.com/library/jj907294.aspx)
