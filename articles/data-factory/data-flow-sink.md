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
ms.date: 06/03/2020
ms.openlocfilehash: 143c94527b947495709d2e94f107dc578e7f2866
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610191"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Fogadó átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Miután befejezte az adatátalakítást, írja azt egy célhelyre a fogadó transzformáció használatával. Minden adatfolyamhoz szükség van legalább egy fogadó átalakításra, de az átalakítási folyamat befejezéséhez annyi süllyedés szükséges, amennyit csak lehet. További mosogatók írásához hozzon létre új streameket új ágak és feltételes felosztások segítségével.

Minden fogadó transzformáció pontosan egy Azure Data Factory adatkészlet-objektumhoz vagy társított szolgáltatáshoz van társítva. A fogadó átalakítás meghatározza a írni kívánt adatmennyiséget és helyet.

## <a name="inline-datasets"></a>Beágyazott adatkészletek

A fogadó-átalakítás létrehozásakor adja meg, hogy a fogadó információ egy adatkészlet-objektumon belül vagy a fogadó átalakításban van-e definiálva. A legtöbb formátum csak az egyikben vagy a másikban érhető el. Adja meg a megfelelő összekötő-dokumentumot, amelyből megtudhatja, hogyan használhat egy adott összekötőt.

Ha egy formátum a beágyazott és egy adatkészlet objektumban is támogatott, akkor mindkettőnek van előnye. Az adatkészlet-objektumok újrafelhasználható entitások, amelyek más adatfolyamatokban és tevékenységekben, például másolásban is kihasználhatók. Ezek különösen akkor hasznosak, ha megerősített sémát használ. Az adatkészletek nem a Spark-alapúak, és esetenként előfordulhat, hogy felül kell bírálni bizonyos beállításokat vagy sémákat a fogadó átalakításban.

A beágyazott adatkészletek használata rugalmas sémák, egyszeri fogadó példányok vagy paraméteres mosogatók használata esetén ajánlott. Ha a fogadó erősen paraméteres, a beágyazott adatkészletek lehetővé teszik, hogy ne hozzon létre egy "dummy" objektumot. A beágyazott adatkészletek a Spark szolgáltatáson alapulnak, és a tulajdonságaik az adatfolyamok számára natívak.

Ha egy beágyazott adatkészletet szeretne használni, válassza ki a kívánt formátumot a fogadó **típus** -választóban. A fogadó adatkészlet kiválasztása helyett válassza ki azt a társított szolgáltatást, amelyhez csatlakozni szeretne.

![Beágyazott adatkészlet](media/data-flow/inline-selector.png "Beágyazott adatkészlet")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a>Támogatott fogadó típusok

Az adatforgalom leképezése egy kinyerési, betöltési, átalakítási (ELT) módszert követ, és az Azure-ban mind az *előkészítési* adatkészletekkel működik. A forrás-átalakítás jelenleg a következő adatkészleteket használhatja:

| Összekötő | Formátum | Adatkészlet/beágyazott |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties) <br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> ✓/- |
| [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties) <br> [Parquet](format-parquet.md#mapping-data-flow-properties)  | ✓/- <br> ✓/- <br> ✓/- <br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties) <br> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common adatmodell (előzetes verzió)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure CosmosDB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |

Az ezekhez az összekötőhöz tartozó beállítások a **Beállítások** lapon találhatók. az információ-és adatfolyam-szkriptek példái a következő beállításokon találhatók az összekötő dokumentációjában. 

Az Azure Data Factorynek több mint [90 natív összekötőhöz](connector-overview.md) van hozzáférése. Ha az adatfolyamatból más forrásoknak is szeretne adatírást készíteni, a másolási tevékenység használatával töltse be az adatok egy támogatott fogadóból.

## <a name="sink-settings"></a>Fogadó beállításai

Miután hozzáadta a fogadót, konfigurálja a fogadó **lapon.** Itt kiválaszthatja vagy létrehozhatja azt az adatkészletet, amelyet a fogadó ír. Az alábbi videó számos különböző fogadó lehetőséget mutat be a szöveges tagolt fájltípusok esetében:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Fogadó beállításai](media/data-flow/sink-settings.png "Fogadó beállításai")

**Séma drift:** a [Schema drift](concepts-data-flow-schema-drift.md) az adatforgalomban lévő rugalmas sémák natív módon történő kezelése, anélkül, hogy explicit módon meg kellene határozni az oszlopok módosításait. Engedélyezze a séma eltolásának engedélyezése **lehetőséget** a fogadó adatsémában definiált további oszlopok írására.

**Séma ellenőrzése:** Ha a séma ellenőrzése beállítás be van jelölve, az adatfolyam sikertelen lesz, ha a bejövő forrás sémájának bármely oszlopa nem található a forrás kivetítésben, vagy ha az adattípusok nem egyeznek. Ezzel a beállítással kényszerítheti ki, hogy a forrásadatok megfelelnek a megadott leképezési szerződésnek. Az adatbázis-forrás forgatókönyvekben nagyon hasznos az oszlopnevek vagy típusok megváltozásának jelzése.

## <a name="field-mapping"></a>Mezőleképezés

A kiválasztott átalakításhoz hasonlóan a fogadó **leképezés** lapján eldöntheti, hogy a bejövő oszlopok milyen módon lesznek írva. Alapértelmezés szerint a rendszer az összes bemeneti oszlopot leképezi, beleértve az elsodródott oszlopokat is. Ez az úgynevezett **automatikus leképezés**.

Ha kikapcsolja az automatikus leképezést, lehetőség van a rögzített oszlop alapú hozzárendelések vagy a szabály alapú leképezések hozzáadására. A szabályokon alapuló leképezések lehetővé teszik, hogy a mintázat egyezésének megfelelő kifejezéseket írjon a logikai és fizikai oszlopnevek. A szabályokon alapuló leképezéssel kapcsolatos további információkért lásd: az [adatforgalom leképezése az oszlopok mintái között](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Egyéni fogadó rendelés

Alapértelmezés szerint az determinált sorrendben több mosogatóba történik az adatgyűjtés. A végrehajtó motor párhuzamosan fogja írni az Adatátalakítási logikát, és az egyes futtatások esetében a fogadó sorrend eltérő lehet. A fogadó rendezésének megadásához és pontos elfogadásához engedélyezze az **Egyéni fogadó megrendelését** az adatfolyam Általános lapján. Ha ez a beállítás engedélyezve van, a mosogatók egymás után, növekvő sorrendben lesznek írva.

![Egyéni fogadó rendelés](media/data-flow/custom-sink-ordering.png "Egyéni fogadó rendelés")

## <a name="data-preview-in-sink"></a>Az adatelőnézet a fogadóban

Amikor egy hibakeresési fürtön beolvas egy előnézetet, a rendszer nem írja le az adatait a fogadóba. Egy pillanatkép arról, hogy az adatok milyen módon lesznek visszaadva, de semmi sem kerül a célhelyre. Az adatok a fogadóba való írásának teszteléséhez futtasson egy folyamat-hibakeresést a folyamat vásznon.

## <a name="next-steps"></a>Következő lépések
Most, hogy létrehozta az adatfolyamatot, adjon hozzá egy [adatfolyam-tevékenységet a folyamathoz](concepts-data-flow-overview.md).
