---
title: Forrásátalakítása az adatfolyam leképezésében
description: Ismerje meg, hogyan állíthat be forrásátalakítást az adatfolyam leképezésében.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 128b15bd5b3ba3c3ac891719bf5c3ec8e5137cce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023514"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Forrásátalakítása az adatfolyam leképezésében 

A forrásátalakítás konfigurálja az adatforrást az adatfolyamhoz. Az adatfolyamok tervezésekor az első lépés mindig a forrásátalakítás konfigurálása lesz. Forrás hozzáadásához kattintson a **Forrás hozzáadása** mezőre az adatfolyamvásznon.

Minden adatfolyam hoz legalább egy forrás átalakítása, de adhat hozzá annyi forrást, mint szükséges az adatátalakítások befejezéséhez. Ezeket a forrásokat összekapcsolhatja egy illesztéssel, a keresményrel vagy az egyesítő átalakítással.

Minden forrásátalakítás pontosan egy Data Factory adatkészlethez van társítva. Az adatkészlet határozza meg azoknak az adatoknak az alakját és helyét, amelyekbe írni vagy olvasni szeretne. Ha fájlalapú adatkészletet használ, a forrásban lévő helyettesítő kártyák és fájllisták segítségével egyszerre több fájllal is dolgozhat.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Támogatott forrásösszekötők az adatfolyam leképezésében

Az adatfolyam leképezése egy kinyerési, betöltési, átalakítási (ELT) megközelítést követ, és az Azure-ban található *átmeneti* adatkészletekkel működik. Jelenleg a következő adatkészletek használhatók forrásátalakításesetén:
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Szöveg, Parketta)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Szöveg, Parketta)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Szöveg, Parketta)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Az összekötőkre vonatkozó beállítások a **Forrás beállítások** lapon találhatók. 

Az Azure Data Factory több mint [90 natív összekötőhöz](connector-overview.md)fér hozzá. Ha az egyéb forrásokból származó adatokat szeretné felvenni az adatfolyamba, a Másolási tevékenység segítségével töltse be az adatokat a támogatott átmeneti területek egyikébe.

## <a name="source-settings"></a>Forrásbeállítások

Miután hozzáadott egy forrást, konfigurálja a **Forrásbeállítások** lapon. Itt választhatja ki vagy hozhatja létre a forráspontokat. Az adatokhoz sémát és mintavételezési beállításokat is választhat.

![Forrásbeállítások lap](media/data-flow/source1.png "Forrásbeállítások lap")

**Tesztkapcsolat:** Ellenőrizze, hogy az adatfolyam szikraszolgáltatása sikeresen tud-e csatlakozni a forrásadatkészletben használt csatolt szolgáltatáshoz. A hibakeresési módnak be kapcsolva kell lennie a szolgáltatás engedélyezéséhez.

**Sémaeltolódás:** [Sémadrift](concepts-data-flow-schema-drift.md) az adatgyár azon képességét, hogy natív módon kezelje a rugalmas sémák az adatfolyamok anélkül, hogy explicit módon oszlopmódosításokat.

* Jelölje be a **Sémaeltolódás engedélyezése** jelölőnégyzetet, ha a forrásoszlopok gyakran változnak. Ez a beállítás lehetővé teszi, hogy az összes bejövő forrásmező átfolyjon a fogadóba irányuló átalakításokon.

* Ha **az Infer sodródott oszloptípusokat** választja, az adatgyár at arra utasítja, hogy minden új felderített oszlophoz észlelje és határozza meg az adattípusokat. Ha ez a funkció ki van kapcsolva, az összes elsodródott oszlop karakterlánc típusú lesz.

**Séma ellenőrzése:** Ha a séma ellenőrzése van kiválasztva, az adatfolyam nem fog futni, ha a bejövő forrásadatok nem egyeznek meg az adatkészlet meghatározott sémájával.

**Átugrási sorok száma:** A sorszám kihagyása mező azt adja meg, hogy hány sort kell figyelmen kívül hagyni az adatkészlet elején.

**Mintavétel:** A mintavételezés engedélyezése a forrásból származó sorok számának korlátozásához. Ezt a beállítást akkor használja, ha hibakeresési célokra teszteli a forrásból származó adatokat, vagy mintaadatokat használ.

**Többsoros sorok:** Jelölje ki a többsoros sorokat, ha a forrásszövegfájl több sorra kiterjedő karakterláncértékeket tartalmaz, azaz egy értéken belüli új sorokat. Ez a beállítás csak a DelimitedText adatkészletekben érhető el.

A forrás megfelelő konfigurálásának ellenőrzéséhez kapcsolja be a hibakeresési módot, és olvassa be az adatok előnézetét. További információt a [Hibakeresési mód című témakörben](concepts-data-flow-debug-mode.md)talál.

> [!NOTE]
> Ha a hibakeresési mód be van kapcsolva, a hibakeresési beállítások sorkorlát-konfigurációja felülírja a mintavételi beállítást a forrásban az adatok előnézete során.

## <a name="projection"></a>Vetület

Az adatkészletekben lévő sémákhoz hasonlóan a forrásban lévő vetület is meghatározza a forrásadatok adatoszlopait, típusait és formátumait. A legtöbb adatkészlet-típusok, például az SQL és a Parkquet esetében a forrás vetülete úgy van rögzítve, hogy tükrözze az adatkészletben definiált sémát. Ha a forrásfájlok nem erősen gépeltek be (például sima csv-fájlok az Alaptfájlok helyett), megadhatja a forrástranszformáció egyes mezőinek adattípusait.

![A Vetítés lap beállításai](media/data-flow/source3.png "Vetület")

Ha a szövegfájl nem rendelkezik definiált sémával, válassza **az Adattípus észlelése** lehetőséget, hogy a Data Factory mintát vegyen és kikövetkeztetje az adattípusokat. Az alapértelmezett adatformátumok automatikus észleléséhez válassza az **Alapértelmezett formátum megadása** lehetőséget.

**A séma alaphelyzetbe állításával** a vetület visszaállítja a hivatkozott adatkészletben meghatározott at.

Az oszlopadattípusokat egy lefelé adatfolyam-származtatott oszlop-transzformációban módosíthatja. Az oszlopnevek módosításához használjon kijelölési átalakítást.

### <a name="import-schema"></a>Séma importálása

A **Vetítés** lap **Séma importálása** gombja lehetővé teszi, hogy aktív hibakeresési fürtsegítségével sémavetületet hozzon létre. Minden forrástípusban elérhető, így a séma importálása itt felülírja az adatkészletben definiált vetületet. Az adatkészlet-objektum nem módosul.

Ez olyan adatkészletekben hasznos, mint az Avro és a CosmosDB, amelyek összetett adatstruktúrákat támogatnak, nem igényelnek sémadefiníciókat az adatkészletben.

## <a name="optimize-the-source-transformation"></a>A forrástranszformáció optimalizálása

A forrásátalakítás **Optimalizálás** lapján megjelenhet egy **Forrás** partíciótípus. Ez a beállítás csak akkor érhető el, ha a forrás az Azure SQL Database. Ennek az az oka, hogy a Data Factory megpróbál párhuzamos kapcsolatokat létesíteni, hogy nagy lekérdezéseket futtasson az SQL Database-forráson.

![Forráspartíció beállításai](media/data-flow/sourcepart3.png "Particionálás")

Az SQL Database-forrás adatait nem kell particionálnia, de a partíciók nagy lekérdezések esetén hasznosak. A partíciót oszlopra vagy lekérdezésre alapozhatja.

### <a name="use-a-column-to-partition-data"></a>Adatok felosztása oszloppal

A forrástáblából jelöljön ki egy partíciót tartalmazó oszlopot. Állítsa be a partíciók számát is.

### <a name="use-a-query-to-partition-data"></a>Adatok particionálása lekérdezéssel

Választhat, hogy a kapcsolatokat lekérdezés alapján particionálja. Adja meg a WHERE predikátum tartalmát. Írja be például az 1980-as évet >.

A leképezési adatfolyamon belüli optimalizálásról az [Optimalizálás lapon](concepts-data-flow-overview.md#optimize)talál további információt.

## <a name="next-steps"></a>További lépések

Kezdje meg [a származtatott oszlop átalakításának](data-flow-derived-column.md) és egy kiválasztott átalakításnak az [felépítését.](data-flow-select.md)
