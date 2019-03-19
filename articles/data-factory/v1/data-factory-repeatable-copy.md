---
title: Reprodukálható másolatot az Azure Data Factoryban |} A Microsoft Docs
description: Megtanulhatja, hogyan ismétlődések annak ellenére, hogy olyan szelet, amely adatokat másol egynél többször van-e futtatni.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b15dcd9ae5f2f1668db3925b659625497eca491f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079758"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Az Azure Data Factoryban reprodukálható másolatot

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell.  
 
> [!NOTE]
> A következő minták az Azure SQL, de alkalmazhatók minden adattárhoz, amely támogatja a téglalap alakú adatkészlet. Előfordulhat, hogy módosítani kell a **típusa** forrás és a **lekérdezés** tulajdonságot (például: lekérdezés helyett sqlReaderQuery) az adatok tárolására.   

Általában relációs áruházakból származó olvasásakor szeretné csak az adott szeletre megfelelő adatokat. Ehhez úgy lenne a WindowStart és WindowEnd rendszer változók használatával érhető el az Azure Data Factoryban. Olvassa el a változókat és az Azure Data Factory itt az a funkciók a [Azure Data Factory - függvények és rendszerváltozók](data-factory-functions-variables.md) cikk. Példa: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Ez a lekérdezés beolvassa az adatokat, amelyek a tábla MyTable (WindowStart -> WindowEnd) szelet időtartama tartományon belülre essen. Futtassa újból a szeletet, akkor is mindig győződjön meg arról, hogy ugyanazokat az adatokat olvasható. 

Más esetekben elolvasnia az egész tábla és a következőképpen adhat meg a sqlReaderQuery:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>SqlSink megismételhető írása
Amikor az adatok másolásának **Azure SQL és SQL Server** más adattárakban, az ismételhetőség nem kívánt eredmények elkerülése érdekében szem előtt tartani kell. 

Adatok másolása az Azure SQL és SQL Server-adatbázist, amikor a másolási tevékenység adatokat fűz hozzá a fogadó tábla alapértelmezés szerint. Tegyük fel, adatokat másolhat az alábbi táblázat az Azure SQL és SQL Server adatbázis két rekordot tartalmazó CSV (vesszővel tagolt értékek) fájlból. A szelet futtatásakor a két rekord lesz másolva SQL-táblát. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Tegyük fel, hogy hibákat talált a forrásfájl, és a le cső mennyiség 2 4-re frissíteni. Ha újra futtatja az adatszelet az adott időszak manuálisan, látni fogja a két új rekordot hozzáfűzi az Azure SQL és SQL Server-adatbázist. Ez a példa feltételezi, hogy a táblában levő oszlopok egyike nem az elsődleges kulcshoz tartozó korlátozás.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Ez a viselkedés elkerülése érdekében meg kell adnia a UPSERT szemantika az alábbi két módszerek egyikének használatával:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>1. mechanizmus: sqlWriterCleanupScript használatával
Használhatja a **sqlWriterCleanupScript** tulajdonságot a fogadó tábla adatok törlése előtt az adatok beszúrása a szelet futtatásakor. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

A szelet futtatásakor a karbantartási parancsprogramot fut először törli az adatokat, amely megfelel a szelet az SQL-táblát. A másolási tevékenység majd szúrja be az adatokat az SQL-táblába. Ha a szelet akkor fut újra, a mennyiség frissül igény szerint.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Tegyük fel, a strukturálatlan ablakmosó rekordot akkor távolítja el az eredeti csv-fájlból. A szelet majd újbóli eredményezne a következő eredményt: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

A másolási tevékenység lefutott a karbantartási parancsprogramot, törölheti az adott szeletre vonatkozó megfelelő adatait. Ezután a bemeneti olvasni (amely csak egy olyan rekorddal majd található) a CSV-t és a táblába beszúrt. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>2. mechanizmus: sliceIdentifierColumnName használatával
> [!IMPORTANT]
> SliceIdentifierColumnName az Azure SQL Data Warehouse jelenleg nem támogatott. 

A második mechanizmus ismételhetőség érdekében van egy dedikált (sliceIdentifierColumnName) oszlop létesíteni a céloldali tábla. Ebben az oszlopban annak érdekében, hogy a forrás- és legyen naprakész szinkronizált Azure Data Factory által volna használható. Ez a módszer rugalmasságot biztosít a cél SQL-tábla sémájának definiálásához vagy módosítása esetén működik. 

Ebben az oszlopban ismételhetőség célokat szolgál az Azure Data Factory által és a folyamat az Azure Data Factory nem bármilyen módosítás séma a táblához. Ez a módszer használatának módja:

1. Típusú oszlop meghatározása **bináris fájlt (32)** a cél SQL-táblát. Ez az oszlop a korlátozások nélkül kell lennie. Nevet ebben az oszlopban, AdfSliceIdentifier ebben a példában.


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

1. Használja az a másolási tevékenység az alábbiak szerint:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Az Azure Data Factory tölti fel. Ez az oszlop a szükséges forrás és cél maradjon szinkronizált megfelelően. Ez az oszlop értékeit az adott környezeten kívül nem használható. 

1 mechanizmus hasonlóan, a másolási tevékenység automatikusan törli azokat az adatokat a célhelyről az SQL-táblát az adott szeletre vonatkozó. Ezután adatok beszúrása a forrásból a célként megadott táblához. 

## <a name="next-steps"></a>További lépések
Tekintse át a következő összekötő cikkek, teljes JSON-példák: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
