---
title: Ismételhető másolat az Azure Data Factoryban
description: Megtudhatja, hogy miként kerülheti el az ismétlődéseket annak ellenére, hogy az adatokat másolt szeletek többször is futnak.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7188cb5774699fc6e31fc3b8c78068bb33c6f552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281144"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Ismételhető másolat az Azure Data Factoryban

## <a name="repeatable-read-from-relational-sources"></a>Relációs forrásokból ismételhető olvasmony
Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet.  
 
> [!NOTE]
> A következő minták az Azure SQL-hez, de minden olyan adattárban, amely támogatja a téglalap alakú adatkészletek. Előfordulhat, hogy módosítania kell **az** adattár forrástípusát és **lekérdezési** tulajdonságát (például: lekérdezés az sqlReaderQuery helyett).   

A relációs tárolókból történő olvasáskor általában csak az adott szeletnek megfelelő adatokat szeretné olvasni. Ennek egyik módja az Azure Data Factoryban elérhető WindowStart és WindowEnd rendszerváltozók használatával. Az Azure Data Factory változóiról és függvényeiről itt olvashat az [Azure Data Factory – Functions és system variables](data-factory-functions-variables.md) cikkben. Példa: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Ez a lekérdezés a MyTable táblából olvassa be a szelet időtartamtartományába (WindowStart -> WindowEnd) alá sorba esik adatokat. A szelet ismétlése mindig biztosítaná, hogy ugyanazokat az adatokat olvassa be a rendszer. 

Más esetekben érdemes elolvasni a teljes táblázatot, és az sqlReaderQuery-t az alábbiak szerint határozhatja meg:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Ismételhető írás az SqlSink kiszolgálóra
Amikor adatokat **Azure SQL/SQL Server** más adattárakból más adatokat más adattárakból más adatokat másadatokba másol, a nem kívánt eredmények elkerülése érdekében szem előtt kell tartania az ismételhetőséget. 

Amikor adatokat másol az Azure SQL/SQL Server Database, a másolási tevékenység hozzáfűzi az adatokat a fogadó tábla alapértelmezés szerint. Tegyük fel, hogy adatokat másol egy CSV(vesszővel tagolt értékek) fájlból, amely két rekordot tartalmaz az alábbi táblázatban egy Azure/SQL Server adatbázisban. Amikor egy szelet fut, a két rekord az SQL táblába kerül. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Tegyük fel, hogy hibákat talált a forrásfájlban, és frissítette a Down Tube mennyiségét 2-ről 4-re. Ha manuálisan futtatja újra az adatszeletet az adott időszakban, két új rekordot talál az Azure SQL/SQL Server adatbázishoz. Ez a példa feltételezi, hogy a tábla egyik oszlopa sem rendelkezik elsődleges kulcsmegkötéssel.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

A viselkedés elkerülése érdekében a UPSERT szemantikát az alábbi két mechanizmus egyikének használatával kell megadnia:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>1. mechanizmus: az sqlWriterCleanupScript használata
Az **sqlWriterCleanupScript** tulajdonsággal megtisztíthatja az adatokat a fogadótáblából, mielőtt beszúrna az adatokat egy szelet futtatásakor. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Amikor egy szelet fut, a törlési parancsfájl először az SQL-táblából származó szeletnek megfelelő adatok törléséhez fut. A másolási tevékenység ezután adatokat szúr be az SQL táblába. Ha a szelet et újrafuttatja, a mennyiség a kívánt módon frissül.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Tegyük fel, hogy a lapos écsi rekord törlődik az eredeti csv-ből. Ezután a szelet újrafuttatása a következő eredményt eredményezi: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

A másolási tevékenység futtatta a törlési parancsfájlt a szelet megfelelő adatainak törléséhez. Ezután leolvasta a csv bemenetét (amely csak egy rekordot tartalmazott), és beillesztette a táblába. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>2. mechanizmus: sliceIdentifierColumnName használata
> [!IMPORTANT]
> Jelenleg sliceIdentifierColumnName nem támogatott az Azure SQL Data Warehouse. 

Az ismételhetőség elérésének második mechanizmusa egy dedikált oszlop (sliceIdentifierColumnName) a céltáblában. Ezt az oszlopot az Azure Data Factory fogja használni a forrás és a cél szinkronizálásának biztosításához. Ez a megközelítés akkor működik, ha a cél SQL-tábla sémájának módosítása vagy definiálása rugalmas. 

Ezt az oszlopot az Azure Data Factory ismételhetőségi célokra használja, és a folyamat során az Azure Data Factory nem módosítja a sémát a táblában. A módszer használata:

1. Definiáljon egy **bináris (32)** típusú oszlopot a cél SQL-táblában. Ebben az oszlopban nem lehetnek korlátozások. Nevezzük el ezt az oszlopot AdfSliceIdentifier-ként ebben a példában.


    Forrástábla:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Céltábla: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Használja a másolási tevékenységben az alábbiak szerint:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Az Azure Data Factory feltölti ezt az oszlopot, mivel annak szükségességét, hogy a forrás és a cél marad szinkronizálva. Ennek az oszlopnak az értékeit nem szabad ezen a környezeten kívül használni. 

Az 1-es mechanizmushoz hasonlóan a Másolási tevékenység automatikusan törli az adott szelet adatait a cél SQL-táblából. Ezután beszúrja a forrásból származó adatokat a céltáblába. 

## <a name="next-steps"></a>További lépések
Tekintse át a következő összekötő cikkeket, amelyek a teljes JSON-példák: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL adattárház](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
