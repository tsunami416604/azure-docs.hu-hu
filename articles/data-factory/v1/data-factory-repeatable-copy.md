---
title: "Az Azure Data Factory ismételhető másolása |} Microsoft Docs"
description: "Megtanulhatja, hogyan ismétlődések annak ellenére, hogy a szelet, amely másolja az adatokat csak egyszer fut-e."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: dbe2f7cb1d843c5e4ec7bc00a7e7dc5a49b31896
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Az Azure Data Factory ismételhető másolása

## <a name="repeatable-read-from-relational-sources"></a>A relációs források ismételhető Olvasás
Ha az adatok másolását a relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja úgy is egy adatkészlet újrapróbálkozási házirendje, hogy a szelet akkor fut újra, ha hiba történik. A szelet akkor fut újra, vagy módon, ha győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy a szelet futtatása hány alkalommal kell.  
 
> [!NOTE]
> A következő mintákat az Azure SQL, de bármilyen adattároló, amely támogatja a téglalap alakú adatkészletek vonatkoznak. Előfordulhat, hogy úgy, hogy a **típus** forrás- és a **lekérdezés** tulajdonság (például: lekérdezés helyett sqlReaderQuery) az adatok tárolásához.   

Általában relációs áruházakból olvasásakor szeretne olvasni a csak az, hogy a szelet megfelelő adatokat. Ehhez úgy lenne a WindowStart és WindowEnd rendszerváltozók érhető el az Azure Data Factory használatával. Olvassa el a változók és az Azure Data Factory Itt a függvényt a [Azure Data Factory - funkciók és rendszerváltozók](data-factory-functions-variables.md) cikk. Példa: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Ez a lekérdezés a táblából MyTable (WindowStart -> WindowEnd) szelet időtartama tartományba eső adatok beolvasása. Futtassa újból a szelet mindig is biztosítja, hogy ugyanazokat az adatokat olvasható. 

Más esetekben előfordulhat, hogy a teljes táblázat olvasni kíván, és az alábbiak szerint határozhatnak meg a sqlReaderQuery:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>SqlSink ismételhető írása
Az adatok másolásakor **Azure SQL-/ SQL Server** az egyéb adattárakhoz ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében szükséges. 

Amikor adat másolása az Azure SQL-/ SQL Server-adatbázis, a másolási tevékenység hozzáfűzi adatokat a fogadó tábla alapértelmezés szerint. Tegyük fel például, adatokat másol egy Azure SQL-/ SQL Server-adatbázisban a következő táblázat két rekordokat tartalmazó CSV (vesszővel tagolt) fájlból. A szelet futtatásakor a két bejegyzés az SQL-tábla lesz másolva. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Tegyük fel, hogy hiba található a forrásfájl és a mennyiséget le cső 2 4 frissítése. Az adatszelet manuálisan adott időszakra futtassa, ha talál fűzött Azure SQL-/ SQL Server-adatbázis két új bejegyzést. Ez a példa feltételezi, hogy rendelkezik-e az oszlopok a tábla egyik elsődleges kulcs megszorítását.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Ez a viselkedés elkerülése érdekében meg kell adnia a UPSERT szemantikáját használja az alábbi két módszerek egyikét:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>1. módszer: sqlWriterCleanupScript használatával
Használhatja a **sqlWriterCleanupScript** tulajdonság előtt a beillesztéssel szelet futtatásakor a fogadó tábla adatainak karbantartását. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

A szelet futtatásakor a tisztítás parancsfájl futtatása először törli az adatokat, amely megfelel a szelet az SQL-táblából. A másolási tevékenység majd adatbeszúrást az SQL-tábla. A szelet akkor fut újra, ha a mennyiség frissül igény szerint.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Tegyük fel, a strukturálatlan szélvédőmosó rekordot a rendszer eltávolítja az eredeti csv a. Majd futtassa újból a szeletet volna eredményt a következő: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

A másolási tevékenység futott a karbantartási parancsprogramot a megfelelő adatokat, hogy a szelet törlése. Ezt követően a bemeneti olvasni a fürt megosztott kötetei szolgáltatás (amelyeket majd csak egy rekord) és a táblába beszúrt. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>2. módszer: sliceIdentifierColumnName használatával
> [!IMPORTANT]
> SliceIdentifierColumnName az Azure SQL Data Warehouse nem támogatott. 

A második mechanizmus ismételhetőség eléréséhez van azzal, hogy a kijelölt oszlop (sliceIdentifierColumnName) a cél tábla. Ebben az oszlopban szeretne győződjön meg arról, a forrás és cél maradnak szinkronizált Azure Data Factory által használni. Ezt a módszert akkor működik, ha módosításával vagy a cél SQL tábla sémáját definiáló rugalmasságot. 

Ebben az oszlopban Azure Data Factory ismételhetőség célra használja, és a folyamat az Azure Data Factory nem módosítja séma a táblában. Ez a megközelítés használatának módja:

1. Adja meg a típusú oszlop **bináris (32)** a célként megadott SQL-tábla. Ebben az oszlopban megkötés kell lennie. Ehhez a példához tegyük nevezze ebben az oszlopban AdfSliceIdentifier.


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

2. Használhatja a másolási tevékenység az alábbiak szerint:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Az Azure Data Factory tölti fel. Ez az oszlop szerint annak érdekében, hogy a forrás és cél maradnak szinkronizált igényét. Az oszlop értékeinek kívül ebben a környezetben nem használható. 

Hasonló mechanizmus 1, másolási tevékenység során automatikusan törli a szükségtelenné érkező SQL táblázat megadott szeletre vonatkozó adatokat. Majd adatok beszúrása a forráskiszolgálóról a céltábla. 

## <a name="next-steps"></a>Következő lépések
Tekintse át a következő összekötő, amely annak teljes JSON-példák: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)