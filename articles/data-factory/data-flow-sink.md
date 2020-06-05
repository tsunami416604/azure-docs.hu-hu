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
ms.openlocfilehash: 2c57ddd88046044cccd13b0ade23144cd5649455
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433314"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Fogadó átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az adatátalakítást követően az adatokat egy célként megadott adatkészletbe lehet elosztani. Minden adatfolyamhoz szükség van legalább egy fogadó átalakításra, de az átalakítási folyamat befejezéséhez annyi süllyedés szükséges, amennyit csak lehet. További mosogatók írásához hozzon létre új streameket új ágak és feltételes felosztások segítségével.

Minden fogadó transzformáció pontosan egy Data Factory adatkészlethez van társítva. Az adatkészlet meghatározza a írni kívánt adat alakját és helyét.

## <a name="inline-datasets"></a>Beágyazott adatkészletek

A fogadó-átalakítás létrehozásakor adja meg, hogy a fogadó információ egy adatkészlet-objektumon belül vagy a fogadó átalakításban van-e definiálva. A legtöbb formátum csak az egyikben vagy a másikban érhető el. Adja meg a megfelelő összekötő-dokumentumot, amelyből megtudhatja, hogyan használhat egy adott összekötőt.

Ha egy formátum a beágyazott és egy adatkészlet objektumban is támogatott, akkor mindkettőnek van előnye. Az adatkészlet-objektumok újrafelhasználható entitások, amelyek más adatfolyamatokban és tevékenységekben, például másolásban is kihasználhatók. Ezek különösen akkor hasznosak, ha megerősített sémát használ. Az adatkészletek nem a Spark-alapúak, és esetenként előfordulhat, hogy felül kell bírálni bizonyos beállításokat vagy sémákat a fogadó átalakításban.

A beágyazott adatkészletek használata rugalmas sémák, egyszeri fogadó példányok vagy paraméteres mosogatók használata esetén ajánlott. Ha a fogadó erősen paraméteres, a beágyazott adatkészletek lehetővé teszik, hogy ne hozzon létre egy "dummy" objektumot. A beágyazott adatkészletek a Spark szolgáltatáson alapulnak, és a tulajdonságaik az adatfolyamok számára natívak.

Ha egy beágyazott adatkészletet szeretne használni, válassza ki a kívánt formátumot a fogadó **típus** -választóban. A fogadó adatkészlet kiválasztása helyett válassza ki azt a társított szolgáltatást, amelyhez csatlakozni szeretne.

![Beágyazott adatkészlet](media/data-flow/inline-selector.png "Beágyazott adatkészlet")

### <a name="supported-inline-dataset-formats"></a>Támogatott beágyazott adatkészlet-formátumok

Jelenleg az egyetlen elérhető beágyazott adatkészlet-formátum a [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md)beolvasott [közös adatmodell](format-common-data-model.md#sink-properties) .

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Támogatott fogadó összekötők a leképezési adatfolyamban

Jelenleg a következő adatkészletek használhatók a fogadó átalakításban:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Az ezekhez az összekötőhöz tartozó beállítások a **Beállítások** lapon találhatók. ezekkel a beállításokkal kapcsolatos információk az összekötő dokumentációjában találhatók. 

Az Azure Data Factorynek több mint [90 natív összekötőhöz](connector-overview.md) van hozzáférése. Ha az adatfolyamból más összekötők számára szeretne adatírást készíteni, a másolási tevékenységgel betöltheti az adatok egyikét a támogatott átmeneti területekről az adatfolyamat befejezése után.

## <a name="sink-settings"></a>Fogadó beállításai

Miután hozzáadta a fogadót, konfigurálja a fogadó **lapon.** Itt kiválaszthatja vagy létrehozhatja azt az adatkészletet, amelyet a fogadó ír. Az alábbi videó számos különböző fogadó lehetőséget mutat be a szöveges tagolt fájltípusok esetében:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

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
