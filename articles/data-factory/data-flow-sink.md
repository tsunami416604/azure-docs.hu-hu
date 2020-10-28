---
title: Fogadó átalakítás a leképezési adatfolyamban
description: Megtudhatja, hogyan konfigurálhat egy fogadó transzformációt a leképezési folyamatokban.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/27/2020
ms.openlocfilehash: 6354b0a1df9d8c331de0731b230d628ac4e435df
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891406"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Fogadó átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Miután befejezte az adatátalakítást, írja azt egy célhelyre a fogadó transzformáció használatával. Minden adatfolyamhoz szükség van legalább egy fogadó átalakításra, de az átalakítási folyamat befejezéséhez annyi süllyedés szükséges, amennyit csak lehet. További mosogatók írásához hozzon létre új streameket új ágak és feltételes felosztások segítségével.

Minden fogadó transzformáció pontosan egy Azure Data Factory adatkészlet-objektumhoz vagy társított szolgáltatáshoz van társítva. A fogadó átalakítás meghatározza a írni kívánt adatmennyiséget és helyet.

## <a name="inline-datasets"></a>Beágyazott adatkészletek

A fogadó-átalakítás létrehozásakor adja meg, hogy a fogadó információ egy adatkészlet-objektumon belül vagy a fogadó átalakításban van-e definiálva. A legtöbb formátum csak az egyikben vagy a másikban érhető el. Egy adott összekötő használatának megismeréséhez tekintse meg a megfelelő összekötő-dokumentumot.

Ha egy formátum a beágyazott és egy adatkészlet objektumban is támogatott, akkor mindkettőnek van előnye. Az adatkészlet-objektumok újrafelhasználható entitások, amelyek más adatfolyamatokban és hasonló tevékenységekben, például másolásban is használhatók. Ezek az újrafelhasználható entitások különösen akkor hasznosak, ha megerősített sémát használ. Az adathalmazok nem a Sparkon alapulnak. Esetenként előfordulhat, hogy felül kell bírálnia bizonyos beállításokat vagy séma-kivetítést a fogadó átalakításban.

A beágyazott adatkészletek használata akkor ajánlott, ha rugalmas sémákat, egyszeri fogadó példányokat vagy paraméteres mosogatókat használ. Ha a fogadó erősen paraméteres, a beágyazott adatkészletek lehetővé teszik, hogy ne hozzon létre egy "dummy" objektumot. A beágyazott adatkészletek a Sparkon alapulnak, és a tulajdonságaik az adatfolyamhoz képest natívak.

Ha egy beágyazott adatkészletet szeretne használni, válassza ki a kívánt formátumot a fogadó **típus** -választóban. A fogadó adatkészlet kiválasztása helyett válassza ki azt a társított szolgáltatást, amelyhez csatlakozni szeretne.

![A beágyazott kijelölést bemutató képernyőkép.](media/data-flow/inline-selector.png "A beágyazott kijelölést bemutató képernyőkép.")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Támogatott fogadó típusok

Az adatforgalom leképezése egy kinyerési, betöltési és átalakítási (ELT) módszert követ, és az Azure-ban mind az *előkészítési* adatkészletekkel működik. Jelenleg a következő adatkészletek használhatók forrás-átalakításban.

| Összekötő | Formátum | Adatkészlet/beágyazott |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (előzetes verzió)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (előzetes verzió)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common adatmodell (előzetes verzió)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Felügyelt Azure SQL-példány (előzetes verzió)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Az ezekhez az összekötőhöz tartozó beállítások a **Beállítások** lapon találhatók. A beállításokkal kapcsolatos információ-és adatfolyam-parancsfájlok példák az összekötő dokumentációjában találhatók.

Azure Data Factory több mint [90 natív összekötőhöz](connector-overview.md)férhet hozzá. Ha az adatfolyamatból más forrásoknak is szeretne adatírást készíteni, a másolási tevékenység használatával töltse be az adatok egy támogatott fogadóból.

## <a name="sink-settings"></a>Fogadó beállításai

Miután hozzáadta a fogadót, konfigurálja a fogadó **lapon.** Itt kiválaszthatja vagy létrehozhatja azt az adatkészletet, amelyet a fogadó ír. Az adatkészlet paramétereinek fejlesztői értékeit a [hibakeresési beállításokban](concepts-data-flow-debug-mode.md)lehet konfigurálni. (A hibakeresési módot be kell kapcsolni.)

A következő videó számos különböző fogadó lehetőséget ismertet a szöveggel tagolt fájltípusok esetében.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![A fogadó beállításait bemutató képernyőkép.](media/data-flow/sink-settings.png "A fogadó beállításait bemutató képernyőkép.")

**Séma drift** : a [séma eltolódása](concepts-data-flow-schema-drift.md) Data Factory a rugalmas sémák natív kezelésére az adatfolyamatokban anélkül, hogy explicit módon meg kellene határozni az oszlopok módosításait. Engedélyezze a séma eltolásának engedélyezése **lehetőséget** , hogy további oszlopokat írjon a fogadó adatsémában definiált elemek fölé.

**Séma ellenőrzése** : Ha a séma ellenőrzése beállítás be van jelölve, az adatfolyam sikertelen lesz, ha a bejövő forrás sémájának bármely oszlopa nem található a forrás kivetítésben, vagy ha az adattípusok nem egyeznek. Ezzel a beállítással kényszerítheti ki, hogy a forrásadatok megfelelnek a megadott leképezési szerződésnek. Az adatbázis-forrás forgatókönyvekben hasznos lehet az oszlopnevek vagy típusok megváltozásának jelzésére.

**Tempdb használata:** Alapértelmezés szerint a Data Factory globális ideiglenes táblázatot fog használni az adattároláshoz a betöltési folyamat részeként. Azt is megteheti, hogy kijelöli a "TempDB használata" lehetőséget, és ehelyett megkérdezi Data Factory, hogy az ideiglenes tároló táblát egy olyan felhasználói adatbázisban tárolja, amely a fogadóhoz használt adatbázisban található.

![TempDB](media/data-flow/tempdb.png "TempDB")

## <a name="field-mapping"></a>Mezőleképezés

A kiválasztott átalakításhoz hasonlóan a fogadó **leképezés** lapján eldöntheti, hogy a bejövő oszlopok milyen módon lesznek írva. Alapértelmezés szerint a rendszer az összes bemeneti oszlopot leképezi, beleértve az elsodródott oszlopokat is. Ezt a viselkedést az *automapping* szolgáltatásnak nevezzük.

Ha kikapcsolja az automapping szolgáltatást, a rögzített oszlop alapú hozzárendelések vagy a szabályokon alapuló leképezések is hozzáadhatók. A szabályokon alapuló hozzárendelésekkel a mintázatok egyeztetésével írhat kifejezéseket. A rögzített leképezési leképezések logikai és fizikai oszlopnevek. A szabályokon alapuló leképezéssel kapcsolatos további információkért lásd: az [adatforgalom leképezése az oszlopok mintái között](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Egyéni fogadó rendelés

Alapértelmezés szerint az determinált sorrendben több mosogatóba történik az adatgyűjtés. A végrehajtó motor párhuzamosan írja az adatelemzési logikát, mivel az átalakítási logika befejeződött, és az egyes futtatások sorrendje eltérő lehet. A fogadó pontos sorrendjének megadásához engedélyezze az **Egyéni fogadó sorrendet** az adatfolyam **általános** lapján. Ha engedélyezve van, a mosogatók egymás után, növekvő sorrendben íródnak.

![Képernyőkép, amely az egyéni fogadó sorrendet mutatja.](media/data-flow/custom-sink-ordering.png "Képernyőkép, amely az egyéni fogadó sorrendet mutatja.")

## <a name="data-preview-in-sink"></a>Az adatelőnézet a fogadóban

Amikor egy hibakeresési fürtön beolvas egy előnézetet, a rendszer nem írja le az adatait a fogadóba. Egy pillanatkép arról, hogy az adatok milyen módon lesznek visszaadva, de semmi sem kerül a célhelyre. Az adatok a fogadóba való írásának teszteléséhez futtasson egy folyamat-hibakeresést a folyamat vásznon.

## <a name="next-steps"></a>Következő lépések
Most, hogy létrehozta az adatfolyamatot, adjon hozzá egy [adatfolyam-tevékenységet a folyamathoz](concepts-data-flow-overview.md).
