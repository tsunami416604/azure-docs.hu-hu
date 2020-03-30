---
title: Átalakítás elszíneződése az adatfolyam leképezésében
description: Ismerje meg, hogyan konfigurálhatja a fogadó átalakítása az adatfolyam leképezése.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: a0b9d424c1995fba075c05ffe5058e297d764775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531260"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Átalakítás elszíneződése az adatfolyam leképezésében

Az adatok átalakítása után az adatokat egy céladatkészletbe süllyesztheti. Minden adatfolyam hoz legalább egy fogadó átalakítás, de írhat annyi fogadók, mint szükséges az átalakítási folyamat befejezéséhez. További fogadókba való íráshoz hozzon létre új adatfolyamokat új ágakés feltételes felosztások segítségével.

Minden fogadó átalakítás van társítva pontosan egy Data Factory adatkészlet. Az adatkészlet határozza meg azoknak az adatoknak az alakját és helyét, amelyekbe írni szeretne.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Támogatott fogadóösszekötők az adatfolyam leképezésében

Jelenleg a következő adatkészletek használhatók a fogadó átalakítása:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Szöveg, Parketta)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Szöveg, Parketta)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Szöveg, Parketta)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Az összekötőkre vonatkozó beállítások a **Beállítások** lapon találhatók. 

Az Azure Data Factory több mint [90 natív összekötőhöz](connector-overview.md)fér hozzá. Ha az adatfolyamból szeretne adatokat írni ezekre a más forrásokba, a Másolási tevékenység segítségével töltse be az adatokat a támogatott átmeneti területek egyikéről az adatfolyam befejezése után.

## <a name="sink-settings"></a>Fogadó beállításai

Miután hozzáadta a fogadót, konfigurálja a **Fogadó** lapon keresztül. Itt választhatja ki vagy hozhatja létre azt az adatkészletet, amelybe a fogadó írja 

![Fogadó beállításai](media/data-flow/sink-settings.png "Fogadó beállításai")

**Sémaeltolódás:** [Sémadrift](concepts-data-flow-schema-drift.md) az adatgyár azon képességét, hogy natív módon kezelje a rugalmas sémák az adatfolyamok anélkül, hogy explicit módon oszlopmódosításokat. **Engedélyezze, hogy a sémaeltolódás engedélyezése** további oszlopokat írjon a fogadó adatsémájában megadott oszlopokra.

**Séma ellenőrzése:** Ha a séma ellenőrzése van kiválasztva, az adatfolyam sikertelen lesz, ha a bejövő forrásséma valamelyik oszlopa nem található a forrásvetítésben, vagy ha az adattípusok nem egyeznek. Ezzel a beállítással kényszerítheti, hogy a forrásadatok megfeleljenek a megadott vetítésszerződésnek. Az adatbázis-forrásesetekben nagyon hasznos annak jelzésére, hogy az oszlopnevek vagy -típusok megváltoztak.

## <a name="field-mapping"></a>Mezőleképezés

A Select átalakításhoz hasonlóan a **fogadó Leképezés** lapján eldöntheti, hogy mely bejövő oszlopok lesznek megírva. Alapértelmezés szerint az összes bemeneti oszlop, beleértve az elsodródott oszlopokat is, le van képezve. Ezt automatikus **leképezésnek nevezzük.**

Ha kikapcsolja az automatikus leképezést, lehetősége van rögzített oszlopalapú vagy szabályalapú hozzárendelések hozzáadására. A szabályalapú leképezések lehetővé teszik a mintaegyeztetéssel rendelkező kifejezések írását, míg a rögzített leképezés logikai és fizikai oszlopneveket képez le. A szabályalapú leképezésről az [adatfolyam leképezésének oszlopmintái](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)című témakörben talál további információt.

## <a name="custom-sink-ordering"></a>Egyéni mosogató rendelés

Alapértelmezés szerint az adatok nem determinisztikus sorrendben több fogadóba vannak írva. A végrehajtási motor az átalakítási logika befejeződésével párhuzamosan írja az adatokat, és a fogadó sorrendje minden futtatáskor változhat. A fogadó sorrendjének megadásához és pontos rendezéséhez engedélyezze az **egyéni fogadós sorrendet** az adatfolyam általános lapján. Ha engedélyezve van, a fogadók sorrendben lesznek írva növekvő sorrendben.

![Egyéni mosogató rendelés](media/data-flow/custom-sink-ordering.png "Egyéni mosogató rendelés")

## <a name="data-preview-in-sink"></a>Adatelőnézet a fogadóban

Ha hibakeresési fürtön lehív egy adatelőnézetet, a rendszer nem ír adatokat a fogadóba. Az adatok megjelenéséről készült pillanatkép visszakerül, de semmi nem lesz az úti célhoz írva. Ha tesztelni szeretné az adatokat a fogadóba, futtasson egy folyamathibakeresést a folyamatvászonról.

## <a name="next-steps"></a>További lépések
Most, hogy létrehozta az adatfolyamot, adjon hozzá egy [adatfolyam-tevékenységet a folyamathoz.](concepts-data-flow-overview.md)
