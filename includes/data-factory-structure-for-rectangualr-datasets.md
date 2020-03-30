---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179789"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Struktúradefiníció megadása téglalap alakú adatkészletekhez
A JSON adatkészletek struktúraszakasza négyszögletes táblák (sorok & oszlopokkal) **választható** szakasza, és a táblázat oszlopainak gyűjteményét tartalmazza. A struktúraszakaszt a típuskonverziók hoz vagy az oszlopleképezések hez szükséges típusadatok megadására használhatja. A következő szakaszok részletesen ismertetik ezeket a szolgáltatásokat. 

Minden oszlop a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| név |Az oszlop neve. |Igen |
| type |Az oszlop adattípusa. Lásd az alábbi Típuskonverziók szakaszt, ha további részleteket szeretne megadni a típusadatok megadásához. |Nem |
| Kultúra |A típus megadása esetén használandó .NET alapú kulturális környezet, amely .NET típusú Datetime vagy Datetimeoffset. Az alapértelmezett az "en-us". |Nem |
| Formátum |A típus megadása esetén használandó formátumkarakterlánc, amely .NET típusú Datetime vagy Datetimeoffset. |Nem |

A következő minta egy három oszlophasználata, neve és lastlogindate oszlopával rendelkező táblázat JSON-szakaszát mutatja be.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Kérjük, használja az alábbi irányelveket, hogy mikor kell megadni a "struktúra" információkat, és mit kell tartalmaznia a **szerkezet** részben.

* Az adatsémát tároló és az adatokat tartalmazó **strukturált adatforrások (például** SQL Server, Oracle, Azure tábla stb.) esetében csak akkor adja meg a "struktúra" szakaszt, ha az adott forrásoszlopok oszlop-hozzárendelését a fogadó adott oszlopaihoz szeretné leképezni, és a nevük nem azonos (lásd az alábbi oszlopleképezési szakasz részleteit). 
  
    Mint már említettük, a típusinformáció nem kötelező a "struktúra" részben. Strukturált források esetén a típusadatok már elérhetők az adatkészlet-definíció részeként az adattárban, ezért ne adjon meg típusadatokat, amikor a "struktúra" szakaszt is tartalmazza.
* **Az olvasási adatforrások (különösen az Azure blob) sémája** esetén dönthet úgy, hogy az adatoktárolása nélkül tárolja a sémát, vagy írja be az adatokat. Az ilyen típusú adatforrások esetében a következő két esetben kell a "struktúra" szót felvennie:
  * Oszlopleképezést szeretne végezni.
  * Ha az adatkészlet egy másolási tevékenység forrása, a "struktúra" mezőben megadhat típusadatokat, és az adatgyár ezt a típusinformációt használja a fogadó natív típusokra való konvertálásához. További információ: [Adatok áthelyezése az Azure Blobba és az Azure Blobból](../articles/data-factory/v1/data-factory-azure-blob-connector.md) cikkből.

### <a name="supported-net-based-types"></a>Támogatott. NET-alapú típusok
A Data Factory a következő CLS-kompatibilis .NET alapú típusértékeket támogatja a "struktúra" típusadatainak megadásához az olvasási adatforrások, például az Azure blob esetében.

* Int16
* Int32 
* Int64
* Egyirányú
* Double
* Decimal
* Bájt[]
* Logikai
* Sztring 
* Guid
* Datetime
* Dátumidő-eltolás
* Időtartomány 

A Datetime & Datetimeoffset esetén megadhatja a "kulturális" & a "formátum" karakterláncot is, hogy megkönnyítse az egyéni Datetime karakterlánc elemzését. Lásd alább a típuskonverziót.

