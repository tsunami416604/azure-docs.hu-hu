---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179586"
---
## <a name="repeatability-during-copy"></a>Ismételhetőség másolás közben
Amikor adatokat más adattárolókból más adatokat más adattárolókból másadatokba másol, az ismételhetőséget szem előtt kell tartani a nem kívánt eredmények elkerülése érdekében. 

Amikor adatokat másol az Azure SQL/SQL Server Database, másolási tevékenység alapértelmezés szerint APPEND az adatkészletet a fogadó tábla alapértelmezés szerint. Ha például két rekordot tartalmazó CSV-fájlból (vesszővel elválasztott értékek et tartalmazó) fájlból másol adatokat az Azure SQL/SQL Server Database szolgáltatásba, a tábla így néz ki:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Tegyük fel, hogy hibákat talált a forrásfájlban, és frissítette a Down Tube mennyiségét 2-ről 4-re a forrásfájlban. Ha újra futtatja az adatszeletet az adott időszakra, két új rekordot talál az Azure SQL/SQL Server adatbázishoz. Az alábbi feltételezés feltételezi, hogy a tábla egyik oszlopa sem rendelkezik elsődleges kulcsmegkötéssel.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Ennek elkerülése érdekében meg kell adnia a UPSERT szemantikát az alábbi 2 mechanizmus egyikének kihasználásával.

> [!NOTE]
> Egy szelet automatikusan futtatható az Azure Data Factory-ban a megadott újrapróbálkozási szabályzat nak.
> 
> 

### <a name="mechanism-1"></a>mechanizmus
Az **sqlWriterCleanupScript** tulajdonsággal először egy szelet futtatásakor végezhet törlési műveletet. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

A törlési parancsfájl végrehajtása először egy adott szelet másolása során történik, amely törli az adott szeletnek megfelelő SQL-táblából származó adatokat. A tevékenység ezt követően beilleszti az adatokat az SQL táblába. 

Ha a szelet most újra fut, akkor megtalálja a mennyiség frissül a kívánt módon.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Tegyük fel, hogy a lapos écsi rekord törlődik az eredeti csv-ből. Ezután a szelet újbóli futtatása a következő eredményt eredményezi: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Semmi újat nem kellett tenni. A másolási tevékenység futtatta a törlési parancsfájlt a szelet megfelelő adatainak törléséhez. Ezután leolvasta a csv bemenetét (amely csak 1 rekordot tartalmazott), és beillesztette a táblázatba. 

### <a name="mechanism-2"></a>mechanizmus
> [!IMPORTANT]
> SliceIdentifierColumnName jelenleg nem támogatott az Azure SQL Data Warehouse számára. 

Az ismételhetőség elérésének másik mechanizmusa, hogy a céltáblában egy dedikált oszlop (**sliceIdentifierColumnName**) található. Ezt az oszlopot az Azure Data Factory fogja használni a forrás és a cél szinkronizálásának biztosításához. Ez a megközelítés akkor működik, ha a cél SQL-tábla sémájának módosítása vagy definiálása rugalmas. 

Ezt az oszlopot az Azure Data Factory ismételhetőségi célokra használja, és a folyamat során az Azure Data Factory nem módosítja a sémát a táblában. A módszer használata:

1. Definiáljon egy bináris (32) típusú oszlopot a cél SQL-táblában. Ebben az oszlopban nem lehetnek korlátozások. Nevezzük el ezt az oszlopot "ColumnForADFuseOnly" néven ebben a példában.
2. Használja a másolási tevékenységben az alábbiak szerint:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Az Azure Data Factory feltölti ezt az oszlopot, mivel annak szükségességét, hogy a forrás és a cél marad szinkronizálva. Ennek az oszlopnak az értékeit a felhasználó nem használja ezen a környezeten kívül. 

Az 1. 

