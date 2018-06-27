---
title: A replikálás az Azure SQL adatszinkronizálás sémamódosítások automatizálásához |} Microsoft Docs
description: Ismerje meg, hogy a replikálás az Azure SQL adatszinkronizálás sémamódosítások automatizálása.
services: sql-database
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: a39e060708514fdca11a5d89858486b442a18309
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019587"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>A replikálás az Azure SQL adatszinkronizálás sémamódosítások automatizálásához

SQL adatszinkronizálás segítségével a felhasználók szinkronizálása az Azure SQL-adatbázisok közötti adatforgalom és a helyszíni SQL Server egy irányban vagy mindkét irányban. SQL Adatszinkronizálás a jelenlegi korlátozások egyike a replikációja támogatása hiánya. Minden alkalommal, amikor módosítja a következő tábla sémáját, kell alkalmazni a változtatásokat manuálisan végpontjai, beleértve a központ és az összes tag a, és módosítsa a szinkronizálás séma.

Ez a cikk a megoldás automatikusan replikálni sémamódosítások SQL adatszinkronizálás végpontjai be.
1. Ez a megoldás séma változásainak követése a DDL-eseményindítót használ.
2. Az eseményindító a séma módosítása parancsok beszúr egy nyomkövetési táblát.
3. A nyomkövetési táblázatnál az adatszinkronizálás szolgáltatással végpontjai van-e szinkronizálva.
4. A DML-eseményindítók Beszúrás után a sémaváltoztatások alkalmazása a többi végpont a szolgálnak.

Ebben a cikkben az ALTER TABLE a sémamódosítás példaként, de ez a megoldás is működik, más típusú változás történt.

> [!IMPORTANT]
> Azt javasoljuk, hogy olvassa el ezt a következő cikket, különösen a szakaszok kapcsolatos [hibaelhárítás](#troubleshooting) és [egyéb szempontok](#other), az automatizált séma módosítása többszörözésére megkezdése előtt a szinkronizálási környezetet. Azt javasoljuk, hogy olvassa el [szinkronizálni az adatokat több felhőalapú és helyszíni adatbázisok között az SQL adatszinkronizálás](sql-database-sync-data.md). Egyes adatbázis-műveletek megszakadhat a jelen cikkben ismertetett megoldás. SQL Server és a Transact-SQL ismerete további tartományt kell hibakeresést.

![A replikáció sémamódosítások automatizálása](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Automatizált séma módosítása a replikáció beállítása

### <a name="create-a-table-to-track-schema-changes"></a>Hozzon létre egy táblát séma változásainak követése

A szinkronizálás csoportban lévő összes adatbázis sémamódosítások követéséhez tábla létrehozása:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Ez a táblázat sémamódosítások sorrendjének követéséhez azonosító oszlopot tartalmaz. További információk naplózásához, szükség esetén további mezőket adhat hozzá.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Hozzon létre egy táblát sémamódosítások előzményeinek nyomon követésére

Összes végponton az utoljára alkalmazott séma módosítása parancs azonosítója nyomon tábla létrehozása.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Az ALTER TABLE DDL-eseményindítót az adatbázisban séma módosításai ahol létrehozása

Hozzon létre egy DDL-eseményindítót az ALTER TABLE műveletekhez. Csak az adatbázisban, ahol séma módosításai az ehhez az eseményindítóhoz létrehozásához szükséges. Az ütközések elkerülése érdekében csak engedélyezése változás történt a szinkronizálási csoport egy adatbázis.

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

Az eseményindító rekord sémaváltozás-nyomkövetési tábla minden egyes az ALTER TABLE parancs szúrja be. Ebben a példában ad hozzá egy szűrőt, amely séma alatti séma módosítás replikálódik elkerülése **DataSync**, mert ezek oka valószínűleg az adatszinkronizálás szolgáltatásnak köszönhetően. További szűrők hozzáadása, ha csak bizonyos típusú változás történt replikálásához.

Más típusú sémamódosítások replikálásához több eseményindítók is hozzáadhat. Hozzon létre például a változások replikálni tárolt eljárások CREATE_PROCEDURE, ALTER_PROCEDURE és DROP_PROCEDURE eseményindítók.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Sémaváltoztatások alkalmazása során beszúrási más végpontok létrehozhasson egy eseményindítót

Ehhez az eseményindítóhoz végrehajtja a séma módosítása parancsot, amikor más végpontok szinkronizálva van. Ehhez az eseményindítóhoz séma módosításai ahol kivételével az összes végponton létrehozásához szükséges (Ez azt jelenti, hogy az adatbázisban, ahol a DDL indítás `AlterTableDDLTrigger` az előző lépésben létrehozott).

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

Ehhez az eseményindítóhoz a Beszúrás után fut, és ellenőrzi, hogy az aktuális parancs következő futtasson-e. A kód logika biztosítja, hogy a nem séma-módosítási utasítást a rendszer kihagyja, és a minden a módosításai érvényesek lesznek, akkor is, ha a beszúrási sorrendje.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>A sémaváltozás-nyomkövetési tábla összes végpontokkal való szinkronizálása

A sémaváltozás-nyomkövetési tábla használatával a meglévő szinkronizálási vagy egy új szinkronizálási csoport összes végpontokhoz történő szinkronizálása. Győződjön meg arról, hogy a módosítások a nyomon követési tábla is szinkronizálva végpontjai, különösen akkor, ha egyirányú szinkronizálási használata.

Séma előzmények változástábla, nem szinkronizálható, mivel tábla különböző állapotot a különböző végpontok tart fenn.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>A szinkronizálás csoportban sémaváltoztatások alkalmazása

Ha a DDL-eseményindítót jön létre az adatbázisban végrehajtott csak séma változásai replikálódnak. Séma módosítások más adatbázisokban nem replikálódnak.

Miután a séma változásai replikálódnak az összes végponton, szükség további lépések elvégzésével a szinkronizálási sémafrissítést indítása vagy leállítása szinkronizálása az oszlopokat.

#### <a name="add-new-columns"></a>Új oszlopok hozzáadása

1.  Ellenőrizze a séma módosítása.

2.  Ne használjon minden adatok módosítását, ahol az új oszlopok érintett fejezze be a lépést, az eseményindítót hoz létre.

3.  Várjon, amíg a séma módosításai érvényesek lesznek minden végpontok.

4.  Az adatbázis-séma frissítése, és az új oszlop hozzáadása a szinkronizálási sémát.

5.  Az új oszlopban lévő adatok szinkronizálva van a következő szinkronizálási művelet során.

#### <a name="remove-columns"></a>Oszlopok eltávolítása

1.  Az oszlop eltávolítása a szinkronizálási sémát. Adatszinkronizálás leállítja ezeket az oszlopokat az adatok szinkronizálása.

2.  Ellenőrizze a séma módosítása.

3.  Az adatbázis-séma frissítése.

#### <a name="update-data-types"></a>Frissítés adattípusok

1.  Ellenőrizze a séma módosítása.

2.  Várjon, amíg a séma módosításai érvényesek lesznek minden végpontok.

3.  Az adatbázis-séma frissítése.

4.  Ha a régi és új adattípusok kompatibilisek nem teljesen - például, ha módosítja a `int` való `bigint` -szinkronizálás sikertelen lehet ahhoz, hogy az eseményindító létrehozása a lépést elvégezte volna. Szinkronizálás sikeres egy ismételt próbálkozás után.

#### <a name="rename-columns-or-tables"></a>Tábla vagy oszlop átnevezése

Tábla vagy oszlop átnevezése működésképtelenné adatszinkronizálás teszi. Hozzon létre egy új tábla vagy oszlop, visszatöltési az adatokat, és törölje a régi tábla vagy oszlop átnevezése helyett.

#### <a name="other-types-of-schema-changes"></a>Más típusú sémaváltozások

Más típusú sémamódosítások - például tárolt eljárások létrehozza vagy eldobja az index - frissítésre a szinkronizálási séma szükség.

## <a name="troubleshoot"></a> Az automatikus módosítás a sémareplikálás hibaelhárítása

A replikációs logika leírt cikk leállítja az működik-e a bizonyos esetekben – például, ha egy helyszíni adatbázisban, ami nem támogatott az Azure SQL-adatbázis módosítása a séma. Ebben az esetben a séma változáskövetési tábla szinkronizálása sikertelen. A probléma megoldásához manuálisan kell:

1.  Tiltsa le a DDL-eseményindítót, és elkerülheti a további séma módosítások a probléma a megoldásáig.

2.  A végpont adatbázis, ha a probléma történik tiltsa le a Beszúrás után eseményindító a végpont, ahol a séma módosítás nem hajtható végre. Ez a művelet lehetővé teszi a a séma módosítása parancs szinkronizálva.

3.  A sémaváltozás-nyomkövetési tábla szinkronizálására szinkronizálási események indítása.

4.  A végpont adatbázis, ha a probléma történik, a lekérdezés a séma módosítása előzménytábla utolsó alkalmazott séma módosítása parancs azonosítót.

5.  A lekérdezés sémaváltozás-nyomkövetési tábla összes parancs nagyobb, mint az ID Azonosítóval rendelkező az előző lépésben lekért listában.

    a.  Figyelmen kívül hagyja azokat a parancsokat, amelyek nem hajtható végre, a végpont-adatbázisban. A séma inkonzisztenciája kezelésére van szükség. Állítsa vissza az eredeti séma módosítást, ha a inkonzisztenciát hatással van az alkalmazás.

    b.  Manuálisan alkalmazza azokat a parancsokat kell alkalmazni.

6.  A séma módosítása előzménytábla frissítése és az utolsó alkalmazott Azonosítóját a megfelelő értéket.

7.  Ellenőrizze, hogy naprakész-e a séma.

8.  Engedélyezze újra az le van tiltva, a második lépésben után INSERT eseményindítót.

9.  Engedélyezze újra a DDL-eseményindítót az első lépésben le van tiltva.

Ha azt szeretné, a séma változástábla követési rekord karbantartása, használják TRUNCATE helyett törölje. Soha ne ismételt előállításával sémaváltozás-nyomkövetési táblát a DBCC CHECKIDENT azonosító oszlopához. Hozzon létre új sémaváltozás nyomkövetési táblát, és frissítse a DDL-eseményindítót az a táblázat nevét, ha újravetéssel szükség.

## <a name="other"></a> Egyéb szempontok

-   Adatbázis-felhasználók számára a tag és hub-adatbázisok konfigurálása a séma módosítása parancsok elegendő engedéllyel kell rendelkeznie kell.

-   A DDL-eseményindítót csak replikálni a kijelölt táblák és műveletek sémaváltozás további szűrőket adhat hozzá.

-   Csak módosításokat végezheti el séma az adatbázisban, ahol a DDL-eseményindítót jön létre.

-   Ha egy helyszíni SQL Server-adatbázis módosítását végez, győződjön meg arról a séma módosulását Azure SQL Database esetén támogatott.

-   Séma módosításai az adatbázisok nem az adatbázisból, ahol a DDL-eseményindítót jön létre, ha a rendszer nem replikálja a módosításokat. A probléma elkerülése érdekében, a módosítások a többi végpont letiltása DDL-eseményindítók is létrehozhat.

-   Ha módosítania kell a sémát a séma nyomkövetési táblát módosításához tiltsa le a DDL-eseményindítót, mielőtt a módosítást, és ezután manuálisan a módosítás alkalmazása az összes végponton. Ugyanaz a tábla után BESZÚRÁSA eseményindítón a séma frissítése nem működik.

-   Az azonosító oszlop az ismételt előállításával nem DBCC CHECKIDENT használatával.

-   Ne használjon TRUNCATE a sémaváltozás-nyomkövetési tábla adatainak karbantartását.
