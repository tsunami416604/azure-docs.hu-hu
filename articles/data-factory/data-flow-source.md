---
title: Forrás-átalakítás a leképezési adatfolyamban
description: Megtudhatja, hogyan állíthatja be a forrás-átalakítást a leképezési adatfolyamban.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 6da0c56e11b8531192ba77d8f0c27fa16eea5de2
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433283"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Forrás-átalakítás a leképezési adatfolyamban 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A forrás-átalakítás konfigurálja az adatforrást az adatfolyamhoz. Az adatfolyamatok tervezésekor az első lépés mindig a forrás-átalakítás konfigurálását fogja beállítani. Forrás hozzáadásához kattintson a **forrás hozzáadása** mezőre az adatfolyam-vászonban.

Minden adatfolyamhoz szükség van legalább egy forrás-átalakításra, de az adatátalakítások végrehajtásához tetszőleges számú forrást adhat hozzá. Ezeket a forrásokat összekapcsolhatja a csatlakozással, a kereséssel vagy a Union-transzformációval együtt.

Minden forrás-átalakítás pontosan egy Data Factory adatkészlethez van társítva. Az adatkészlet határozza meg az adatokat, amelyeket írni vagy olvasni szeretne. Ha file-alapú adatkészletet használ, a forrás helyettesítő karaktereit és listáját használva egyszerre több fájllal is dolgozhat.

## <a name="inline-datasets"></a>Beágyazott adatkészletek

A forrás-átalakítás létrehozásakor létrehozott első döntés az, hogy a forrásadatok definiálva vannak-e egy adatkészlet-objektumban vagy a forrás-átalakításon belül. A legtöbb formátum csak az egyikben vagy a másikban érhető el. Adja meg a megfelelő összekötő-dokumentumot, amelyből megtudhatja, hogyan használhat egy adott összekötőt.

Ha egy formátum a beágyazott és egy adatkészlet objektumban is támogatott, akkor mindkettőnek van előnye. Az adatkészlet-objektumok újrafelhasználható entitások, amelyek más adatfolyamatokban és tevékenységekben, például másolásban is kihasználhatók. Ezek különösen akkor hasznosak, ha megerősített sémát használ. Az adatkészletek nem a Spark-alapúak, és esetenként előfordulhat, hogy felül kell bírálni bizonyos beállításokat vagy séma-kivetítést a forrás-átalakításban.

A beágyazott adatkészletek használata rugalmas sémák, egyszeri forrásoldali példányok vagy paraméteres források használata esetén ajánlott. Ha a forrás erősen paraméteres, a beágyazott adatkészletek lehetővé teszik, hogy ne hozzon létre egy "dummy" objektumot. A beágyazott adatkészletek a Spark szolgáltatáson alapulnak, és a tulajdonságaik az adatfolyamok számára natívak.

Ha egy beágyazott adatkészletet szeretne használni, válassza ki a kívánt formátumot a **forrás típusa** választóban. A forrás-adatkészlet kiválasztása helyett válassza ki azt a társított szolgáltatást, amelyhez csatlakozni szeretne.

![Beágyazott adatkészlet](media/data-flow/inline-selector.png "Beágyazott adatkészlet")

### <a name="supported-inline-dataset-formats"></a>Támogatott beágyazott adatkészlet-formátumok

Jelenleg az egyetlen elérhető beágyazott adatkészlet-formátum a [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md)beolvasott [közös adatmodell](format-common-data-model.md#source-properties) .

## <a name="supported-source-datasets-in-mapping-data-flow"></a>Támogatott forrás-adatkészletek a leképezési adatfolyamban

Az adatforgalom leképezése egy kinyerési, betöltési, átalakítási (ELT) módszert követ, és az Azure-ban mind az *előkészítési* adatkészletekkel működik. A forrás-átalakítás jelenleg a következő adatkészleteket használhatja:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Az ezen összekötők beállításai a **forrás beállításai** lapon találhatók. ezekkel a beállításokkal kapcsolatos információk az összekötő dokumentációjában találhatók. 

Az Azure Data Factorynek több mint [90 natív összekötőhöz](connector-overview.md) van hozzáférése. Az adatfolyamatban lévő más forrásokból származó adatok belefoglalásához használja a másolási tevékenységet az adatok betöltéséhez az egyik támogatott átmeneti területre.

## <a name="source-settings"></a>Forrásbeállítások

Miután hozzáadta a forrást, konfigurálja a **beállításokat a forrás beállításai** lapon. Itt kiválaszthatja vagy létrehozhatja a forrásként szolgáló adatkészletet. Kiválaszthatja az adataihoz tartozó séma-és mintavételi beállításokat is.

![Forrás beállításai lap](media/data-flow/source1.png "Forrás beállításai lap")

A **kapcsolatok tesztelése:** Annak ellenőrzése, hogy az adatfolyam Spark-szolgáltatása sikeresen tud-e csatlakozni a forrás-adatkészletben használt társított szolgáltatáshoz. A funkció engedélyezéséhez a hibakeresési módot kell bekapcsolni.

**Séma drift:** a [Schema drift](concepts-data-flow-schema-drift.md) az adatforgalomban lévő rugalmas sémák natív módon történő kezelése, anélkül, hogy explicit módon meg kellene határozni az oszlopok módosításait.

* Jelölje be a **séma drift engedélyezése** jelölőnégyzetet, ha a forrás oszlopai gyakran változnak. Ez a beállítás lehetővé teszi, hogy az összes bejövő forrás mező áthaladjon a fogadóra történő átalakításokon.

* A **következtetett oszlopok** kiválasztásakor a rendszer arra utasítja az adatelőállítót, hogy észlelje és definiálja az egyes felderített új oszlopok adattípusait. Ha ez a funkció ki van kapcsolva, az összes lebegő oszlop karakterlánc típusú lesz.

**Séma ellenőrzése:** Ha a séma ellenőrzése beállítás be van jelölve, az adatfolyam nem fog futni, ha a bejövő forrásadatok nem egyeznek az adatkészlet meghatározott sémájával.

**Sorok számának kihagyása:** A sorok kihagyása mező meghatározza, hogy az adatkészlet elején hány sort kell figyelmen kívül hagyni.

**Mintavételezés:** A mintavétel engedélyezése a forrás sorainak számának korlátozásához. Akkor használja ezt a beállítást, ha hibakeresési célból teszteli vagy felveszi az adatait a forrásból.

**Többsoros sorok:** Válassza a többsoros sorok lehetőséget, ha a forrás szövegfájl olyan karakterlánc-értékeket tartalmaz, amelyek több sorra kiterjednek, azaz egy értéken belül sortöréseket. Ez a beállítás csak DelimitedText adatkészletekben érhető el.

Ha ellenőrizni szeretné, hogy a forrás megfelelően van-e konfigurálva, kapcsolja be a hibakeresési módot, és olvassa be az adatelőnézett. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Ha a hibakeresési mód be van kapcsolva, a hibakeresési beállításokban szereplő sorok korlátozása beállítás felülírja a forrás mintavételi beállításait az adatelőnézet során.

## <a name="projection"></a>Vetület

Az adatkészletekben lévő sémák esetében a forrás vetülete határozza meg a forrásadatok adatoszlopait, típusait és formátumait. A legtöbb adatkészlet, például az SQL és a parketta esetében a forrás kivetítése úgy van rögzítve, hogy tükrözze az adatkészletben definiált sémát. Ha a forrásfájlok nem megfelelően vannak beírva (például a sima CSV-fájlok a Parquet-fájlok helyett), megadhatja a forrás-átalakítás egyes mezőinek adattípusait.

![Beállítások a kivetítés lapon](media/data-flow/source3.png "Vetület")

Ha a szövegfájl nem rendelkezik meghatározott sémával, válassza az **adattípusok észlelése** lehetőséget, hogy Data Factory az adattípusok mintavételezését és következtetését. Válassza az **alapértelmezett formátum megadása** lehetőséget az alapértelmezett adatformátumok automatikus észleléséhez.

A **séma alaphelyzetbe állítása** visszaállítja a leképezést a hivatkozott adatkészletben definiált értékre.

Módosíthatja az oszlop adattípusait egy lefelé irányuló adatfolyamból származtatott oszlop transzformációjában. Az oszlopnevek módosításához válasszon transzformációt.

### <a name="import-schema"></a>Séma importálása

A **leképezés** lapon a **séma importálása** gomb lehetővé teszi, hogy egy aktív hibakeresési fürttel hozzon létre egy séma-kivetítést. Minden Forrástípus esetében elérhető, a séma importálásakor a rendszer felülírja az adatkészletben definiált leképezést. Az adatkészlet-objektum nem lesz módosítva.

Ez olyan adatkészletekben hasznos, mint az összetett adatstruktúrákat támogató Avro és CosmosDB, és nem igénylik a séma-definíciókat az adatkészletben.

## <a name="optimize-the-source-transformation"></a>A forrás átalakítás optimalizálása

A forrás-átalakítás **optimalizálása** lapján megjelenhet a **forrás** partíció típusa. Ez a beállítás csak akkor érhető el, ha a forrás Azure SQL Database. Ennek az az oka, hogy Data Factory párhuzamosan próbálkozik a csatlakozással, hogy nagy lekérdezéseket futtasson a SQL Database-forráson.

![Forrás partíció beállításai](media/data-flow/sourcepart3.png "particionálás")

Nem kell particionálnia az SQL Database-forrás adatait, de a partíciók nagy lekérdezések esetén hasznosak. A partíciót egy oszlopra vagy egy lekérdezésre alapozhatja.

### <a name="use-a-column-to-partition-data"></a>Oszlop használata az adatparticionáláshoz

A forrás táblából válassza ki a particionálni kívánt oszlopot. Állítsa be a partíciók számát is.

### <a name="use-a-query-to-partition-data"></a>Adatparticionálásra szolgáló lekérdezés használata

Dönthet úgy is, hogy egy lekérdezés alapján particionálja a kapcsolatokat. Adja meg egy WHERE predikátum tartalmát. Adja meg például a következőt: év > 1980.

További információ a leképezési adatfolyamon belüli optimalizálásról: [optimalizálás lap](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Következő lépések

Egy [származtatott oszlop átalakításának](data-flow-derived-column.md) és egy [kiválasztott átalakítás](data-flow-select.md)létrehozásának megkezdése.
