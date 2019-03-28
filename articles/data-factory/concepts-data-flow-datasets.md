---
title: Az Azure Data Factory-folyamat adatkészletek adatok leképezése
description: Az Azure Data Factory leképezési adatfolyam rendelkezik az adott adatkészlet kompatibilitási
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: efb82c57a5620ef3eace8b39f6f27f2286202f84
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521839"
---
# <a name="mapping-data-flow-datasets"></a>Data Flow adatkészletek leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Adatkészlet lehet egy Data Factory-szerkezet, amely a folyamat használata az adatok felépítésének meghatározásához. Az adatfolyam sor- és szolgáltatói szükséges az adatok egy finom részletesebben adatkészlet-definícióban. A control flow folyamatokban használt Adatkészletekre nem szükséges adatok megismerése azonos mélységét.

Az adatfolyam adatkészletek forrásaként és fogadó átalakítások használhatók. Ezek az alapvető adatsémák meghatározására szolgálnak. Ha a séma az adatok nem rendelkeznek, a forrás és fogadó séma eltéréseket állíthatja a. Az adathalmaz definiált sémával kell a kapcsolódó adattípusok, adatformátumok a célnyelven, helye és a kapcsolódó Linked Service csatlakozási adataival. A metaadatok az adatkészletekből származó "Leképezés" forrásaként jelenik az adatforrás-átalakítás. A séma az adatkészletben a leképezésről az adatforrás-átalakítás jelöli az adatok meghatározott nevét és típusát adatreprezentációt folyamat során a fizikai adattípus és alakzat jelöli.

## <a name="dataset-types"></a>Adatkészlet-típusok

Jelenleg a data flow-ban található négy adatkészlet típusa:

* Azure SQL DB
* Azure SQL DW
* Parquet (az ADLS- és Blob)
* Tagolt szöveges (az ADLS- és Blob)

Data flow adatkészletek külön a *adatforrástípust* származó a *társított szolgáltatási kapcsolati típus*. Általában a Data Factory áttekintése válassza ki a kapcsolat típusát (Blob, ADLS stb.), és megadhatja az adatkészletben lévő fájl típusa. Belül adatfolyam a másik társított szolgáltatás kapcsolattípusok társítható forrástípusok válasszon ki.

![Átalakítási beállítások a forrás](media/data-flow/dataset1.png "források")

## <a name="data-flow-compatible-datasets"></a>Data Flow kompatibilis adatkészletek

Amikor egy új adatkészletet hoz létre, van egy "Data Flow kompatibilis" jobb felső sarokban lévő távolságát a panel feliratú jelölőnégyzetet. A gombra kattintva a rendszer kiszűri csak azokat az adatkészleteket, amelyek együtt adatáramlás. 

## <a name="import-schemas"></a>Sémák importálása

A séma az adatfolyam adatkészletek importálásakor egy séma importálása gomb jelenik meg. A gombra kattintva megjelennek a két lehetőség közül választhat: A forrásból importálja, vagy importáljon egy helyi fájlból. A legtöbb esetben közvetlenül a forrásból fogja importálja a sémát. Azonban ha egy meglévő sémafájl (Parquet-fájlok vagy CSV-fejlécek), mutatva, hogy helyi fájlt és a Data Factory határozza meg a séma séma fájl alapján.

## <a name="create-new-table"></a>Új tábla létrehozása

Az adatfolyam hozhat létre egy új tábla definícióját a céladatbázis adatkészlet beállítása a fogadó átalakítást, amely rendelkezik egy új tábla nevét az ADF teheti fel. Az alábbi a táblázat neve a "Szerkesztés" gombra az SQL-adatkészletet, és adja meg egy új tábla nevét. Ezt követően a fogadó átalakításában kapcsolja be a "Séma eltéréseket engedélyezése". Seth None értékre a "Séma importálása" beállítást.

![Átalakítás schéma zdroje](media/data-flow/dataset2.png "SQL-séma")

## <a name="choose-your-type-of-data-first"></a>Először válassza ki az adatok típusát

### <a name="delimited-text"></a>Karakterrel elválasztott szöveg

Elválasztójellel tagolt szöveges adatkészlet állítja a elválasztó kezeléséhez vagy egyetlen elválasztó karakterek ("\t"számára TSV,"," fürt megosztott kötetei szolgáltatás, ' |} "...) vagy több karakter határoló keresése. A fejléc sorra váltógombot állítsa be, és keresse meg azokat a forrás átalakítással automatikusan észlelje az adattípusokat. Ha egy fogadó tagolt szöveges adatok betöltése az adatkészletet használ, csak adja meg egy célmappát. A fogadó beállításaiban megadhatja a kimeneti fájlok nevét.

### <a name="parquet"></a>Parquet eszközökben

Az ADF-adatfolyamok az előnyben részesített átmeneti adatkészlettípus Parquet használja. Parquet fogja tárolni a bőséges metaadatok séma az adatokkal együtt.

### <a name="database-types"></a>Adatbázis típusa

Kiválaszthatja az Azure SQL Database vagy Azure SQL dw-ben.

A többi ADF adatkészlet típusra a másolási tevékenység használatával az adatok előkészítéséhez. Ez a minta segíteni a sablonkatalógusban egy ADF-sablon szerepel.

![másolja ki az átmeneti](media/data-flow/templatedf.png "átmeneti másolása")

## <a name="choose-your-connection-type"></a>Válassza ki a kapcsolat típusa

Ha a Parquet vagy tagolt szöveges adatkészletet használ, az adatok ezután kiválaszthatja a hely: Az ADLS- vagy Blob.

## <a name="next-steps"></a>További lépések

Első lépésként [létrehozása egy új adatfolyam](data-flow-create.md) , és adja hozzá egy adatforrás-átalakítás. Ezután konfigurálja az adatkészlet ahhoz az adatforráshoz.

Használja a [másolási tevékenység](copy-activity-overview.md) ahhoz, hogy az adatok minden ADF az adatforrásra, és adatfolyam eléri az ADLS- vagy Blob előkészítése.

