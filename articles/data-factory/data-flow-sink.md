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
ms.date: 12/12/2019
ms.openlocfilehash: 4b10a4c98abd6bec4074bf35764a9cbb85d5b157
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605969"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Fogadó átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az adatátalakítást követően az adatokat egy célként megadott adatkészletbe lehet elosztani. Minden adatfolyamhoz szükség van legalább egy fogadó átalakításra, de az átalakítási folyamat befejezéséhez annyi süllyedés szükséges, amennyit csak lehet. További mosogatók írásához hozzon létre új streameket új ágak és feltételes felosztások segítségével.

Minden fogadó transzformáció pontosan egy Data Factory adatkészlethez van társítva. Az adatkészlet meghatározza a írni kívánt adat alakját és helyét.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Támogatott fogadó összekötők a leképezési adatfolyamban

Jelenleg a következő adatkészletek használhatók a fogadó átalakításban:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, szöveg, parketta)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure-CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Az ezekhez az összekötőhöz tartozó beállítások a **Beállítások** lapon találhatók. ezekkel a beállításokkal kapcsolatos információk az összekötő dokumentációjában találhatók. 

Azure Data Factory több mint [90 natív összekötőhöz](connector-overview.md)férhet hozzá. Ha az adatfolyamatból más forrásoknak is szeretne adatírást készíteni, a másolási tevékenységgel betöltheti az adatfolyamat befejezése után a támogatott átmeneti területekről származó adatok egyikét.

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

## <a name="next-steps"></a>További lépések
Most, hogy létrehozta az adatfolyamatot, adjon hozzá egy [adatfolyam-tevékenységet a folyamathoz](concepts-data-flow-overview.md).
