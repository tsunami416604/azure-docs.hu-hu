---
title: Forrás-átalakítás a leképezési adatfolyamban
description: Megtudhatja, hogyan állíthatja be a forrás-átalakítást a leképezési adatfolyamban.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 128b15bd5b3ba3c3ac891719bf5c3ec8e5137cce
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023514"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Forrás-átalakítás a leképezési adatfolyamban 

A forrás-átalakítás konfigurálja az adatforrást az adatfolyamhoz. Az adatfolyamatok tervezésekor az első lépés mindig a forrás-átalakítás konfigurálását fogja beállítani. Forrás hozzáadásához kattintson a **forrás hozzáadása** mezőre az adatfolyam-vászonban.

Minden adatfolyamhoz szükség van legalább egy forrás-átalakításra, de az adatátalakítások végrehajtásához tetszőleges számú forrást adhat hozzá. Ezeket a forrásokat összekapcsolhatja a csatlakozással, a kereséssel vagy a Union-transzformációval együtt.

Minden forrás-átalakítás pontosan egy Data Factory adatkészlethez van társítva. Az adatkészlet határozza meg az adatokat, amelyeket írni vagy olvasni szeretne. Ha file-alapú adatkészletet használ, a forrás helyettesítő karaktereit és listáját használva egyszerre több fájllal is dolgozhat.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Támogatott forrás-összekötők a leképezési adatfolyamban

Az adatforgalom leképezése egy kinyerési, betöltési, átalakítási (ELT) módszert követ, és az Azure-ban mind az *előkészítési* adatkészletekkel működik. A forrás-átalakítás jelenleg a következő adatkészleteket használhatja:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure szinapszis-elemzés](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Az ezen összekötők beállításai a **forrás beállításai** lapon találhatók. ezekkel a beállításokkal kapcsolatos információk az összekötő dokumentációjában találhatók. 

Azure Data Factory több mint [90 natív összekötőhöz](connector-overview.md)férhet hozzá. Az adatfolyamatban lévő más forrásokból származó adatok belefoglalásához használja a másolási tevékenységet az adatok betöltéséhez az egyik támogatott átmeneti területre.

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
