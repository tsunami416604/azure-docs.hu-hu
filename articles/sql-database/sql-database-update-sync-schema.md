---
title: Sémamódosítások replikációjának automatizálása az SQL Data Sync programban
description: Ismerje meg, hogyan automatizálhatja a sémaváltozások replikációját az Azure SQL Data Sync szolgáltatásban.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/14/2018
ms.openlocfilehash: 639901975bbb66b9f410bea297d9e48cd96d6d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822435"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Sémamódosítások replikációjának automatizálása az Azure SQL Data Sync szolgáltatásban

Az SQL Data Sync lehetővé teszi a felhasználók számára az adatok szinkronizálását az Azure SQL-adatbázisok és a helyszíni SQL Server között egy irányba vagy mindkét irányban. Az SQL Data Sync egyik jelenlegi korlátozása a sémamódosítások replikációjának támogatásának hiánya. Minden alkalommal, amikor módosítja a táblasémát, a módosításokat manuálisan kell alkalmaznia az összes végponton, beleértve a hubot és az összes tagot, majd frissítenie kell a szinkronizálási sémát.

Ez a cikk egy olyan megoldást vezet be, amely automatikusan replikálja a sémamódosításokat az összes SQL Data Sync végpontra.
1. Ez a megoldás DDL-eseményindítót használ a sémaváltozások nyomon követéséhez.
1. Az eseményindító beszúrja a sémamódosítási parancsokat egy követési táblába.
1. Ez a követési tábla az Adatszinkronizálás szolgáltatás használatával minden végponthoz szinkronizálva van.
1. A DML-eseményindítók a beszúrás után a sémamódosítások alkalmazására szolgálnak a többi végponton.

Ez a cikk az ALTER TABLE-t használja a sémamódosítás példájaként, de ez a megoldás más típusú sémamódosításokesetén is működik.

> [!IMPORTANT]
> Javasoljuk, hogy figyelmesen olvassa el ezt a cikket, különösen a [hibaelhárításról](#troubleshoot) és [egyéb szempontokról](#other)szóló szakaszokat, mielőtt elkezdené végrehajtani az automatikus sémamódosítás-replikációt a szinkronizálási környezetben. Azt is javasoljuk, hogy olvassa el [az Adatok szinkronizálása több felhőbeli és helyszíni adatbázisban az SQL Data Sync segítségével.](sql-database-sync-data.md) Egyes adatbázis-műveletek megszakíthatják a jelen cikkben ismertetett megoldást. A problémák megoldásához szükség lehet az SQL Server és a Transact-SQL további tartományi ismeretekre.

![Sémamódosítások replikációjának automatizálása](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Automatikus sémamódosítás-replikáció beállítása

### <a name="create-a-table-to-track-schema-changes"></a>Táblázat létrehozása a séma változásainak nyomon követésére

Hozzon létre egy táblát a sémaváltozások nyomon követésére a szinkronizálási csoport összes adatbázisában:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Ez a tábla rendelkezik egy identitásoszloplal a sémamódosítások sorrendjének nyomon követésére. Szükség esetén további mezőket is hozzáadhat a további információk naplózásához.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Táblázat létrehozása a sémamódosítások előzményeinek nyomon követésére

Hozzon létre egy táblázatot a legutóbb alkalmazott sémamódosítási parancs azonosítójának nyomon követésére.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>ALTER TABLE DDL eseményindító létrehozása abban az adatbázisban, ahol sémamódosítások történnek

Hozzon létre egy DDL-eseményindítót az ALTER TABLE műveletekhez. Ezt az eseményindítót csak abban az adatbázisban kell létrehoznia, ahol a séma módosítások at hajtják végre. Az ütközések elkerülése érdekében csak a szinkronizálási csoport egyik adatbázisában engedélyezze a sémamódosításokat.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

Az eseményindító minden ALTERTABLE parancshoz beszúr egy rekordot a sémaváltozás-követési táblába. Ez a példa hozzáad egy szűrőt, hogy elkerülje a séma **DataSync**alatt végzett sémamódosítások replikálását, mivel ezeket valószínűleg az Adatszinkronizálás szolgáltatás hajtotta végre. Adjon hozzá további szűrőket, ha csak bizonyos típusú sémamódosításokat szeretne replikálni.

További eseményindítók at is hozzáadhat más típusú sémamódosítások replikálásához. Hozzon létre például CREATE_PROCEDURE, ALTER_PROCEDURE és DROP_PROCEDURE eseményindítókat a tárolt eljárások módosítási változásainak replikálásához.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Eseményindító létrehozása más végpontokon a sémamódosítások alkalmazásához a beszúrás során

Ez az eseményindító végrehajtja a sémamódosítási parancsot, ha más végpontokkal van szinkronizálva. Létre kell hoznia ezt az eseményindítót az összes végponton, kivéve azt, ahol a séma módosítások at hajtják végre (azaz abban az adatbázisban, ahol a DDL eseményindító `AlterTableDDLTrigger` jön létre az előző lépésben).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Ez az eseményindító a beszúrás után fut, és ellenőrzi, hogy az aktuális parancs fusson-e legközelebb. A kódlogika biztosítja, hogy a rendszer ne hagyja ki a sémamódosítási utasítást, és akkor is alkalmazza a módosításokat, ha a beszúrás nem megfelelő.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>A sémaváltozás-követési tábla szinkronizálása az összes végponttal

A sémaváltozás-követési táblát szinkronizálhatja az összes végpontra a meglévő szinkronizálási csoport vagy egy új szinkronizálási csoport használatával. Győződjön meg arról, hogy a követési tábla módosításai szinkronizálhatók az összes végponttal, különösen akkor, ha egyirányú szinkronizálást használ.

Ne szinkronizálja a sémamódosítási előzménytáblát, mivel az a tábla különböző végpontokon eltérő állapotot tart fenn.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>A sémamódosítás alkalmazása szinkronizálási csoportban

A rendszer csak a DDL-eseményindítót létrehozó adatbázisban végrehajtott sémamódosításokat replikálja. A más adatbázisokban végrehajtott sémamódosítások nem replikálódnak.

Miután a séma módosításait replikálja az összes végpontra, további lépéseket kell tennie a szinkronizálási séma frissítéséhez az új oszlopok szinkronizálásának elindításához vagy leállításához.

#### <a name="add-new-columns"></a>Új oszlopok hozzáadása

1.  Módosítsa a sémát.

1.  Kerülje az adatmódosításokat, ahol az új oszlopok érintettek, amíg el nem végezte az eseményindítót létrehozó lépést.

1.  Várjon, amíg a séma módosításai minden végpontra vonatkoznak.

1.  Frissítse az adatbázissémát, és adja hozzá az új oszlopot a szinkronizálási sémához.

1.  Az új oszlopban lévő adatok szinkronizálása a következő szinkronizálási művelet során történik.

#### <a name="remove-columns"></a>Oszlopok eltávolítása

1.  Távolítsa el az oszlopokat a szinkronizálási sémából. Az Adatszinkronizálás leállítja az adatok szinkronizálását ezekben az oszlopokban.

1.  Módosítsa a sémát.

1.  Frissítse az adatbázissémát.

#### <a name="update-data-types"></a>Adattípusok frissítése

1.  Módosítsa a sémát.

1.  Várjon, amíg a séma módosításai minden végpontra vonatkoznak.

1.  Frissítse az adatbázissémát.

1.  Ha az új és a régi adattípusok nem teljesen `int` kompatibilisek – például ha átvált `bigint` róluk – a szinkronizálás sikertelen lehet, mielőtt az eseményindítókat létrehozó lépések befejeződnek. A szinkronizálás az újrapróbálkozás után sikeres.

#### <a name="rename-columns-or-tables"></a>Oszlopok vagy táblázatok átnevezése

Az oszlopok vagy táblák átnevezése leállítja az adatszinkronizálást. Hozzon létre egy új táblát vagy oszlopot, töltse ki az adatokat, majd törölje a régi táblát vagy oszlopot átnevezés helyett.

#### <a name="other-types-of-schema-changes"></a>A sémamódosítások egyéb típusai

Más típusú sémamódosítások – például tárolt eljárások létrehozása vagy egy index elvetése esetén nem szükséges a szinkronizálási séma frissítése.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a>Automatikus sémamódosítás-replikáció – problémamegoldás

A jelen cikkben leírt replikációs logika bizonyos helyzetekben leáll, például ha sémamódosítást hajtott végre egy helyszíni adatbázisban, amelyet az Azure SQL Database nem támogat. Ebben az esetben a sémaváltozás-követési tábla szinkronizálása sikertelen. Manuálisan kell megoldania a problémát:

1.  Tiltsa le a DDL eseményindítót, és kerülje a további sémamódosításokat, amíg a probléma meg nem oldható.

1.  Abban a végpont-adatbázisban, ahol a probléma történik, tiltsa le az AFTER INSERT eseményindítót azon a végponton, ahol a sémamódosítása nem végezhető el. Ez a művelet lehetővé teszi a sémamódosítási parancs szinkronizálását.

1.  A sémaváltozás-követési tábla szinkronizálásához aktiválja a szinkronizálást.

1.  A végponti adatbázisban, ahol a probléma történik, a sémamódosítási előzmények táblájának lekérdezésével lekellkérdezni az utolsó alkalmazott sémamódosítási parancs azonosítóját.

1.  A sémaváltozás-követési tábla lekérdezésével az előző lépésben beolvasott azonosítónál nagyobb azonosítóval rendelkező összes parancsot listázhatja.

    a.  Figyelmen kívül hagyja azokat a parancsokat, amelyek nem hajthatók végre a végpont-adatbázisban. A séma inkonzisztenciával kell foglalkoznia. Az eredeti séma módosításai visszaállítása, ha az inkonzisztencia hatással van az alkalmazásra.

    b.  Manuálisan alkalmazza azokat a parancsokat, amelyeket alkalmazni kell.

1.  Frissítse a sémamódosítási előzmények tábláját, és állítsa az utolsó alkalmazott azonosítót a megfelelő értékre.

1.  Ellenőrizze, hogy a séma naprakész-e.

1.  A második lépésben engedélyezze újra az AFTER INSERT eseményindítót.

1.  Az első lépésben újra engedélyezze a DDL-eseményindítót.

Ha törölni szeretné a sémaváltozás-követési tábla rekordjait, a Csonkolás helyett használja a DELETE parancsot. Soha ne magozza újra az identitásoszlopot a sémaváltozás-követési táblában a DBCC CHECKIDENT használatával. Új sémaváltozás-követési táblákat hozhat létre, és frissítheti a tábla nevét a DDL eseményindítóban, ha újravetésre van szükség.

## <a name="other-considerations"></a><a name="other"></a>Egyéb szempontok

-   A központi és tagadatbázisokat konfiguráló adatbázis-felhasználóknak elegendő engedéllyel kell rendelkezniük a sémamódosítási parancsok végrehajtásához.

-   A DDL-eseményindítóban további szűrőket adhat hozzá, hogy csak a kijelölt táblák vagy műveletek sémájának változását replikálja.

-   A séma módosításacsak abban az adatbázisban hajtható végre, ahol a DDL eseményindító létrejön.

-   Ha egy helyszíni SQL Server-adatbázisban végez módosítást, győződjön meg arról, hogy a sémamódosítása támogatott az Azure SQL Database-ben.

-   Ha a séma módosítások nem abban az adatbázisban történnek, ahol a DDL-eseményindító t létrehozták, a módosítások nem replikálódnak. A probléma elkerülése érdekében DDL-eseményindítókat hozhat létre a többi végpont módosításának blokkolására.

-   Ha módosítania kell a sémaváltozás-követési tábla sémáját, tiltsa le a DDL eseményindítót a módosítás előtt, majd manuálisan alkalmazza a módosítást az összes végpontra. Az AFTER INSERT eseményindító ban lévő séma frissítése ugyanazon a táblán nem működik.

-   Ne magozzon újra az identitásoszlop segítségével DBCC CHECKIDENT.

-   Ne használja a Csonkolás parancsot a sémaváltozás-követési táblában lévő adatok karbantartásához.

## <a name="next-steps"></a>További lépések

További információ az SQL Data Syncről:

-   Áttekintés – [Adatok szinkronizálása több felhőbeli és helyszíni adatbázisban az Azure SQL Data Sync segítségével](sql-database-sync-data.md)
-   Adatszinkronizálás beállítása
    - A portálon – [Oktatóanyag: Az SQL Data Sync beállítása az Adatok szinkronizálásához az Azure SQL Database és a helyszíni SQL Server között](sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Adatszinkronizálási ügynök – [Adatszinkronizálási ügynök az Azure SQL Data Sync szolgáltatáshoz](sql-database-data-sync-agent.md)
-   Gyakorlati tanácsok – [Gyakorlati tanácsok az Azure SQL Data Sync szolgáltatáshoz](sql-database-best-practices-data-sync.md)
-   Figyelő – [SQL-adatszinkronizálás figyelése az Azure Figyelő naplóival](sql-database-sync-monitor-oms.md)
-   Hibaelhárítás – [Az Azure SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása](sql-database-troubleshoot-data-sync.md)
-   A szinkronizálási séma frissítése
    -   A PowerShell használatával – [A PowerShell használatával frissítse a szinkronizálási sémát egy meglévő szinkronizálási csoportban](scripts/sql-database-sync-update-schema.md)
