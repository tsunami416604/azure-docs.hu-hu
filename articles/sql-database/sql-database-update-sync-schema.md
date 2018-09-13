---
title: Az Azure SQL Data Sync sémamódosítások-replikáció automatizálása |} A Microsoft Docs
description: Ismerje meg, hogyan automatizálhatja a replikálást az Azure SQL Data Sync sémamódosítások.
services: sql-database
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 6859784dcb3b3905459e83cfcb7812d7f23ac630
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721857"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Az Azure SQL Data Sync sémamódosítások-replikáció automatizálása

Az SQL Data Sync lehetővé teszi a felhasználóknak az Azure SQL Database-adatbázis közötti adatszinkronizálás és a helyszíni SQL Server egy irányban vagy mindkét irányban. Az SQL Data Sync, a jelenlegi korlátozások egyike hiányoznak a sémamódosítások replikálását támogatása. Minden alkalommal, amikor módosítja a következő tábla sémáját, a módosítások mentéséhez minden végponton manuálisan, többek között a hub és az összes tagot, és majd a szinkronizálási sémájának frissítéséhez szüksége.

Ez a cikk egy megoldás összes SQL Data Sync-végpontot automatikusan replikálhatók sémaváltozások be.
1. Ez a megoldás a DDL-triggerek séma nyomon követésére használ.
1. Az eseményindító a séma módosítása parancsok beszúr egy nyomkövetési táblát.
1. A nyomkövetési táblát szinkronizálva van az összes olyan végpontok, a Data Sync szolgáltatás használatával.
1. A sémaváltozások alkalmazásához a többi végpont a DML-trigger Beszúrás után használhatók.

Ebben a cikkben az ALTER TABLE egy sémaváltozás példaként, de más típusú sémaváltozások is működik ez a megoldás.

> [!IMPORTANT]
> Azt javasoljuk, hogy olvassa el ezt körültekintően, különösen a szakaszok kapcsolatos cikk [hibaelhárítás](#troubleshooting) és [egyéb szempontok](#other), automatizált séma módosítása replikáció végrehajtása előtt a szinkronizálási környezet. Azt javasoljuk, hogy olvasási [szinkronizálja az adatokat több felhőalapú és helyszíni adatbázisban az SQL Data Sync szolgáltatással](sql-database-sync-data.md). Egyes adatbázis-műveletek megszakadhat a jelen cikkben ismertetett megoldás. Az SQL Server és a Transact-SQL ismerete további fiókdíjat kell végezzen hibakeresést.

![Automatizálja a sémamódosítások](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Automatizált séma módosítása a replikáció beállítása

### <a name="create-a-table-to-track-schema-changes"></a>Hozzon létre egy táblát a séma változásainak követése

Hozzon létre egy táblát a szinkronizálási csoportban lévő összes adatbázis sémamódosítások nyomon követéséhez:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Ez a tábla rendelkezik egy identitásoszlop sémaváltozások sorrendjének követéséhez. További adatok naplózására, szükség esetén további mezőket adhat hozzá.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Hozzon létre egy táblát a sémamódosítások előzményeinek nyomon követésére

Minden végponton az utoljára alkalmazott séma módosítása parancs azonosítója nyomon tábla létrehozása.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Az adatbázisban séma módosítások, hozzon létre egy ALTER TABLE DDL-triggert

A DDL-triggerek, az ALTER TABLE műveletek létrehozásához. Csak szeretne az adatbázisban séma módosítások ahol ez az eseményindító létrehozása. Az ütközések elkerülése érdekében csak séma módosításának engedélyezése az egy szinkronizálási csoportban egy adatbázis.

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

Az eseményindító egy rekordot a sémaváltozás-nyomkövetési minden ALTER TABLE parancsban táblázatban szúrja be. Ebben a példában egy szűrőt séma alapján végzett séma módosítások replikálása elkerülése érdekében ad **DataSync**, mivel az a legvalószínűbb a Data Sync szolgáltatás által végzett. Adjon hozzá további szűrőket, ha szeretné replikálni a sémamódosítások bizonyos típusú.

Sémaváltozások más típusú replikálásához további eseményindítók is hozzáadhat. Hozzon létre például CREATE_PROCEDURE, ALTER_PROCEDURE és DROP_PROCEDURE módosításokat replikálja a tárolt eljárások, eseményindítók.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>A sémaváltozások alkalmazásához a beszúrási során más végpontok eseményindító létrehozása

Ez az eseményindító végrehajtja a séma módosítása parancsot, amikor más végpontok szinkronizálva van. Ez az eseményindító létrehozása a séma módosítások ahol kivételével az összes végponton kell (vagyis az adatbázisban, ahol a DDL-trigger `AlterTableDDLTrigger` jön létre az előző lépésben).

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

Ez az eseményindító a Beszúrás után fut, és ellenőrzi az aktuális parancsot futtasson e tovább. A kód logika biztosítja, hogy nem séma-módosítási utasítást a rendszer kihagyta, és minden módosítás akkor is, ha a beszúrási sorrendben alkalmazza.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>A sémaváltozás-nyomkövetési tábla összes végpontokra szinkronizálása

A sémaváltozás-nyomkövetési táblát, a meglévő szinkronizálási csoporthoz vagy egy új szinkronizálási csoport összes végpontok szinkronizálhatók. Ellenőrizze, hogy a módosítások a nyomon követési tábla összes végpontok ingyeneseket, különösen akkor, ha egyirányú szinkronizálási használ.

Séma módosítása előzménytábla nem szinkronizálódnak, mivel, hogy a táblázat megőrzi a különböző végpontok a különböző állapotokat.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Az egy szinkronizálási csoportban a sémaváltozások alkalmazásához

Csak séma módosításait az adatbázisban, amelyben létrehozza a DDL-triggert replikálódnak. Az azokban lévő séma módosításait a rendszer nem replikálja.

Miután a séma változásai replikálódnak az összes végpontokra, is indítása vagy leállítása az új oszlopok szinkronizálása a szinkronizálási séma frissítésére további lépésekkel kell.

#### <a name="add-new-columns"></a>Új oszlopok hozzáadása

1.  Győződjön meg a séma módosításához.

1.  Ne használjon bármely adatmódosítás, ahol az új oszlopok érintett mindaddig, amíg Ön teljesítette a lépés, amely az eseményindítót hoz létre.

1.  Várjon, amíg a sémamódosítások érvénybe lépnek minden végpontok.

1.  Frissítse az adatbázissémát, és az új oszlop hozzáadása a szinkronizálási sémához.

1.  Az új oszlopban lévő adatok szinkronizálva van a következő szinkronizálási művelet során.

#### <a name="remove-columns"></a>Oszlopok eltávolítása

1.  Az oszlopok eltávolítása a szinkronizálási sémához. Adatszinkronizálás leállítja ezeket az oszlopokat az adatok szinkronizálása.

1.  Győződjön meg a séma módosításához.

1.  Frissítse az adatbázissémát.

#### <a name="update-data-types"></a>Az adattípusok módosítása

1.  Győződjön meg a séma módosításához.

1.  Várjon, amíg a sémamódosítások érvénybe lépnek minden végpontok.

1.  Frissítse az adatbázissémát.

1.  Ha a régi és új adattípusokat nem teljesen kompatibilis – például, ha módosítja a `int` való `bigint` -szinkronizálás meghiúsulhat előtt elvégezte a lépéseket, amelyek az eseményindítók létrehozásához. Szinkronizálás sikeres újrapróbálkozást követően.

#### <a name="rename-columns-or-tables"></a>Nevezze át az oszlopok vagy táblázatok

Oszlopok vagy táblázatok átnevezését teszi Data Sync tovább működni. Hozzon létre egy új tábla vagy oszlop, visszatöltési az adatokat, és ezután törölje a régi tábla vagy oszlop átnevezése helyett.

#### <a name="other-types-of-schema-changes"></a>Más típusú sémaváltozások

Más típusú sémaváltozások – például tárolt eljárások létrehozása vagy eltávolítását az index - szinkronizálási sémájának frissítése nem áll szükséges.

## <a name="troubleshoot"></a> Az automatikus módosítás a sémareplikálás hibaelhárítása

A replikációs logika leírt cikk leáll, az egyes esetekben – például megtette a sémát módosítani egy helyszíni adatbázisból, ami nem támogatott az Azure SQL Database. Ebben az esetben a sémaváltozás-nyomkövetési táblát szinkronizálása sikertelen. A probléma megoldásához manuálisan kell:

1.  Tiltsa le a DDL-triggerek, és további séma módosítások elkerülése érdekében, amíg a probléma nem oldódik.

1.  A végpont adatbázisban, ahol a hiba történik tiltsa le az AFTER INSERT eseményindítót a végponton, ahol a séma módosítás nem hajtható végre. Ez a művelet lehetővé teszi, hogy szinkronizálja a séma módosítása parancsot.

1.  A sémaváltozás-nyomkövetési táblát szinkronizálása a szinkronizálási események indítása.

1.  A végpont adatbázisban, ahol a hiba történik a lekérdezés a séma módosítása előzménytábla utolsó alkalmazott séma módosítása parancs Azonosítójának lekéréséhez.

1.  A lekérdezés a sémaváltozás-nyomkövetési táblát az összes parancs olyan azonosítót az azonosító értéke nagyobb az előző lépésben lekért listában.

    a.  Hagyja figyelmen kívül azokat a parancsokat, amelyek nem hajtható végre az endpoint adatbázisban. A séma inkonzisztenciája kezelni kell. Visszaállítás az eredeti sémamódosítások, ha a program inkonzisztenciát hatással van az alkalmazás.

    b.  Manuálisan a alkalmazni azokat a parancsokat, amelyek a alkalmazni kell.

1.  A séma változástábla-előzmények frissítéséhez, és állítsa be az utolsó alkalmazott azonosítója a helyes értékre.

1.  Ellenőrizze, hogy naprakész-e a séma.

1.  Engedélyezze újra az AFTER INSERT eseményindítót, a második lépésben le van tiltva.

1.  A DDL-triggerek le van tiltva, az első lépésben engedélyezze újra.

Ha szeretné törölni a nyomon követési séma változástábla-rekordokat, használja a DELETE TRUNCATE helyett. Soha ne reseed az identitásoszlop sémaváltozás-nyomkövetési táblát DBCC CHECKIDENT használatával. Hozzon létre új nyomkövetési táblát, és frissítse a tábla neve a DDL-triggert, ha újravetéssel szükség.

## <a name="other"></a> Egyéb szempontok

-   Adatbázis-felhasználók, akik a hubot és a tag-adatbázisok konfigurálása a séma módosítása parancsok futtatásával elegendő engedéllyel kell rendelkeznie kell.

-   A DDL-triggert csak replikálni a sémamódosítás a kiválasztott táblák vagy műveleteket is hozzáadhat további szűrőket.

-   Az adatbázisban, amelyben létrehozza a DDL-trigger csak séma módosításokat végezheti el.

-   Ha egy helyszíni SQL Server-adatbázis egy módosítást végez, ellenőrizze a séma módosítása az Azure SQL Database-ben támogatott.

-   Séma módosítása adatbázisokban nem az adatbázisból, ahol a DDL-trigger létrehozása esetén, a rendszer nem replikálja a módosításokat. A probléma elkerülése érdekében a többi végpont letiltása a DDL-triggerek is létrehozhat.

-   Ha módosítani szeretné a séma sémájával nyomkövetési táblát módosításához tiltsa le a DDL-triggert, mielőtt a módosítást, és manuálisan alkalmazhatja a módosítás minden végpontok. Az ugyanazon a táblán AFTER INSERT eseményindítón a séma frissítése nem működik.

-   Nem reseed az identitásoszlop DBCC CHECKIDENT használatával.

-   Ne használjon TRUNCATE tábla sémája változáskövetési adatok megtisztítása.
