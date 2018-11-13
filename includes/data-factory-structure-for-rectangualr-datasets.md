---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572239"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Struktúrameghatározások négyszögletes adatkészletek esetén
A struktúra szakasza az adatkészletek JSON- **választható** téglalap alakú táblák (a sorok és oszlopok) szakaszt, és a tábla oszlopait gyűjteményét tartalmazza. A struktúra szakasz vagy biztosító informace o typu típuskonverziók vagy az oszlop-hozzárendelések során fogja használni. A következő szakaszok ismertetik részletesebben ezeket a funkciókat. 

Minden oszlop a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| név |Az oszlop neve. |Igen |
| type |Az oszlop adattípusát. Tekintse meg a típus átalakítások kapcsolatos alábbi szakasz további részletek kapcsolatban, hogy mikor kell hogy típussal kapcsolatos információk megadása |Nem |
| kulturális környezet |.NET-alapú kulturális környezet esetén a típus van megadva, és jelenleg .NET Datetime vagy Datetimeoffset használandó. Alapértelmezett érték "en-us". |Nem |
| Formátum |Formázó karakterlánc típus van megadva, és .NET használandó Datetime vagy Datetimeoffset írja be. |Nem |

A következő minta bemutatja a struktúra részben JSON három oszlopot felhasználói azonosítóját, nevét és lastlogindate tartalmazó tábla.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Használja a következő irányelveket mikor "struktúra" információval és tartalmának a **struktúra** szakaszban.

* **A strukturált adatok források** , hogy áruházbeli adatok sémát, és írja be az adatokat mozgatná (forrásokhoz, amilyen az SQL Server, Oracle, az Azure table stb.), a "struktúra" szakaszban kell megadnia, csak akkor, ha azt szeretné, valamint adatokat hajtsa végre az adott forrás oszlop-hozzárendelés a fogadó és a nevük adott oszlopok az oszlopok nem azonosak (lásd az alábbi oszlop leképezése részben részleteit). 
  
    Ahogy említettük, a típussal kapcsolatos információk nem kötelező "struktúra" szakaszban. Strukturált források típussal kapcsolatos információk már rendelkezésre áll az adatkészlet-definícióban az adattárban részeként, ezért műveket nem szabad típussal kapcsolatos információk elvégzését indokló, hogy a "struktúra" szakaszban.
* **Az olvasási adatforráson (pontosabban az Azure blob) séma** dönthet úgy, hogy adatokat tárolni az adatokat bármely séma vagy típusú adatok tárolására nélkül. Az ilyen típusú adatforrások "struktúra" kell tartalmaznia a következő 2 esetekben:
  * Szeretne tenni, oszlopleképezés.
  * Ha az adatkészlet egy forrást a másolási tevékenység, "struktúra" írja be az adatokat megadhatja, és a data factory való átalakításra a fogadóhoz natív típusai használják típusú adatokat. Lásd: [adatok importálására és az Azure-Blobból](../articles/data-factory/v1/data-factory-azure-blob-connector.md) cikkben további információt.

### <a name="supported-net-based-types"></a>Támogatott. NET-alapú típusok
A Data factory a következő CLS megfelelő .NET-alapú típusú értékeket támogatja "struktúra" írja be az adatokat, amelyek biztosítják az olvasási adatforrásokhoz, például az Azure blob-séma.

* Int16
* Int32 
* Int64
* Önálló
* Dupla
* tizedes tört
* Byte]
* Logikai
* Karakterlánc 
* GUID
* Dátum és idő
* Datetimeoffset
* Időtartomány 

A dátum és idő & Datetimeoffset is megadhat az egyéni dátum/idő karakterlánc-elemzés elősegítése érdekében a "kulturális környezet" és "formátum" karakterlánc. Tekintse meg a minta az alábbi típusa átalakításához.

