---
title: Állítsa be az Azure Data Factory az adatfolyam-leképezés funkció a forrás-átalakítás
description: Ismerje meg, hogyan állítható be egy adatfolyam-leképezés forrás átalakítási műveletet.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 4f77eafd3309d7c1d679c126b1a5eb1ff0e9a28d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490095"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Forrás átalakításában adatfolyam-leképezés 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Egy adatforrás-átalakítás konfigurálja az adatforrást az adatfolyamhoz. Egy adatfolyamot is tartalmazhat egynél több adatforrás-átalakítás. Folyamatok tervezése az adatokat, amikor kezdő mindig egy adatforrás-átalakítás.

Minden adat folyamat szükséges legalább egy adatforrás-átalakítás. Az adatátalakítások végrehajtásához szükség szerint adjon hozzá a legtöbb forrásból. Csatlakozhat egy illesztési átalakítási mód vagy a union átalakítás együtt forrásokat.

> [!NOTE]
> Hibakeresése az adatfolyamot, az adatok beolvasva a forrás a mintavételi beállítást vagy a hibakeresési forrás korlátozások használatával. Fogadóba másolt adatokat írni, futtatnia kell az adatfolyam egy folyamatból, az adatfolyam-tevékenység. 

![Az adatforrás-beállítások lapon átalakítási beállítások a forrás](media/data-flow/source.png "forrás")

Társítsa a forrás adatfolyam átalakítása pontosan egy Data Factory-adatkészlet. Az adatkészlet határozza meg, az alakzat és írni vagy olvasni az adatok helyének. A forrás és a fájl helyettesítő karakterek listák segítségével egyszerre több fájl használatához.

## <a name="data-flow-staging-areas"></a>Data Flow átmeneti területekből

Az adatfolyam együttműködik *átmeneti* adatkészleteket, amelyek az összes Azure-ban. Az adatok átalakításakor használt átmeneti ezeket az adatkészleteket használja. 

A Data Factory csaknem 80 natív összekötőket hozzáféréssel rendelkezik. Olyan más forrásból származó adatok szerepeljenek az adatfolyamot, a másolási tevékenység eszközzel, hogy az adatfolyam adatkészlet átmeneti terület egyikének az adatok előkészítéséhez.

## <a name="options"></a>Beállítások

Válassza ki a sémát és a mintavételi lehetőség az adatok.

### <a name="allow-schema-drift"></a>Séma eltéréseket engedélyezése
Válassza ki **séma eltéréseket engedélyezése** , ha a forrás oszlopok gyakran változik. Ezzel a beállítással minden bejövő forrásmezőket a fogadó átalakítások keresztül érhetők el.

### <a name="validate-schema"></a>Séma érvényesítése

Ha a forrásadatok bejövő verziója nem egyezik a megadott séma, az adatfolyam nem fogja tudni futtatni.

![Nyilvános adatforrás-beállítások a beállítások ellenőrzése séma, az engedélyezési séma eltéréseket és a mintavételi megjelenítő](media/data-flow/source1.png "1 nyilvános adatforrás")

### <a name="sample-the-data"></a>Az adatok
Engedélyezése **mintavételi** a forrásból sorok számának korlátozására. Használja ezt a beállítást, ha a teszt, vagy az adatokat a forrásból, hibakeresési célra.

## <a name="define-schema"></a>Séma megadása

Ha a forrásfájl nem listaobjektum (a példában, egybesimított fájlok helyett Parquet-fájlokat), itt minden mező esetében az adattípusok definiálása az az adatforrás-átalakítás.  

![A séma megadása lapon átalakítási beállítások a forrás](media/data-flow/source2.png "2 forrás")

Válassza ki az átalakítás az oszlopnevek később módosíthatja. A származtatott oszlop átalakítása használatával az adattípusok módosítása. Típusos forrásokhoz módosíthatja az adattípusok később válassza átalakításban. 

![Adattípusok a select átalakítás](media/data-flow/source003.png "adattípusok")

### <a name="optimize-the-source-transformation"></a>Az adatforrás-átalakítás optimalizálása

Az a **optimalizálása** lapon láthatja a forrás átalakításkor az egy **forrás** partíció típusa. Ez a beállítás csak akkor, ha a forrás az Azure SQL Database érhető el. Ennek az az oka a Data Factory megpróbál kapcsolatok párhuzamos, nagy lekérdezések futtatásához az SQL Database-forrás.

![Forrás partícióbeállítások](media/data-flow/sourcepart3.png "particionálása")

Nem kell az adatok particionálása az SQL Database-forrás, de hasznos, ha nagy lekérdezések partíciókat. A partíció alapja egy olyan oszlop vagy egy lekérdezés.

### <a name="use-a-column-to-partition-data"></a>Használja az adatok particionálására egy oszlop

A forrás táblából válasszon ki egy oszlopot, partícióhoz a. A partíciók számát is megadhatja.

### <a name="use-a-query-to-partition-data"></a>Az adatok particionálása lekérdezés használata

Kiválaszthatja, hogy a lekérdezés alapján kapcsolatok particionálásához. Egyszerűen adja meg a WHERE predikátum tartalmát. Adja meg például az 1980 >.

## <a name="source-file-management"></a>Forrás fájlok kezelése

Válassza ki a beállítások a forrás található fájlok kezeléséhez. 

![Az új forrásbeállítások](media/data-flow/source2.png "új beállításai")

* **Helyettesítő karaktert tartalmazó elérési út**: A forrásmappából válasszon, amely egy mintának megfelelő fájlok. Ez a beállítás felülbírál bármely fájl az adatkészlet-definícióban.

Helyettesítő példák:

* ```*``` Bármely karakter jelöl
* ```**``` A rekurzív directory beágyazási jelöli
* ```?``` Egy karaktert váltja fel
* ```[]``` Több karaktert a zárójelek között szerepel

* ```/data/sales/**/*.csv``` Az összes csv-fájlok alatt /data/sales beolvasása
* ```/data/sales/20??/**``` Lekérdezi a 20 századi minden fájl
* ```/data/sales/2004/*/12/[XY]1?.csv``` Az összes csv-fájlok lekéri az 2004. december kezdve az X vagy Y 2 számjegyű előtagja

A kötettárolóhoz kell adni az adatkészletben. A helyettesítő karaktereket tartalmazó cím ezért is tartalmaznia kell abból a gyökérmappából, a mappa elérési útja.

* **Fájlok listája**: Ez egy olyan fájlt. Hozzon létre egy szövegfájlt, amely relatív elérési út feldolgozandó fájlok listáját tartalmazza. A szövegfájl mutasson.
* **Tároló neve oszlop**: A forrásfájl neve Store az adatok egy oszlopban. Adjon meg egy új nevet a fájl karakterlánc tárolására.
* **A művelet befejezését követően**: Kiválasztása után az adatok folyamatfuttatási jogosultságot, törölje a forrás-fájlt, vagy helyezze át a forrásfájl nem történik semmi. a forrás-fájllal. Az áthelyezés utakat relatív.

Forrásfájljainak áthelyezése egy másik hely utófeldolgozás, először válassza ki "Áthelyezése" file művelet. Ezután adja meg a "feladó" könyvtár. Ha nem használ semmilyen helyettesítő karakterek az elérési úthoz, majd a "feladó" beállítás lesz a forrásmappa ugyanabban a mappában.

Ha például van egy helyettesítő karakterrel megadott forrás elérési útja:

```/data/sales/20??/**/*.csv```

Megadhatja "feladó"

```/data/sales```

És a "záró", mint

```/backup/priorSales```

Ebben az esetben minden alkönyvtár /data/sales alkatrészekből, amelyek alapján kerülnek /backup/priorSales viszonyítva.

### <a name="sql-datasets"></a>SQL-adatkészletek

Ha a forrás SQL-adatbázis vagy az SQL Data Warehouse, a forrás Fájlkezelés további lehetősége van.

* **Lekérdezés**: Adja meg a forrás SQL-lekérdezést. Ez a beállítás felülbírál bármely táblázat, amely a kiválasztott adatkészlet. Vegye figyelembe, hogy **Order By** záradékok itt nem támogatottak, de beállíthatja, hogy egy teljes válassza ki az utasítást. Felhasználó által definiált táblafüggvények is használható. **Válassza ki * a udfGetData()** van egy SQL-táblát visszaadó UDF. Ezt a lekérdezést, amelyet használhat az adatfolyam a forrástábla állítja elő.
* **Kötegméret**: Adja meg a Köteg mérete nagy mennyiségű adat álló olvasott be.
* **Elkülönítési szintet**: SQL-források az ADF-leképezés adatfolyamok alapértelmezés olvassa el a nem véglegesített. Az elkülönítési szint Itt módosíthatja az alábbi értékek egyikére:
* Zárolásalapú
* Olvassa el a nem véglegesített
* Megismételhető olvasása
* A szerializálható
* Egyik sem (figyelmen kívül hagyása elkülönítési szint)

![Elkülönítési szintet](media/data-flow/isolationlevel.png "elkülönítési szint")

> [!NOTE]
> Fájlműveletek futtassa, csak akkor, ha az adatok az adatfolyam kezdhet egy (a folyamat hibakeresési vagy a Futtatás végrehajtási) futtatása, a folyamat a végrehajtása az adatfolyam tevékenységeit használó folyamatot. A fájl az operations *nem* adatfolyam hibakeresési módban futtatja.

### <a name="projection"></a>Vetület

Sémák adatkészletekben, mint például a leképezésben az olyan adatforrások az adatokat tartalmazó oszlopok, típusok és a forrásadatok formátumok határozza meg. 

![A leképezés lap beállításai](media/data-flow/source3.png "leképezése")

Ha a szöveges fájl nincs meghatározott sémája rendelkezik, válassza ki a **észlelje az adattípusokat** , hogy a Data Factory fog minta és az adattípusok kikövetkeztetni. Válassza ki **definiálása alapértelmezett formátuma** az automatikus észlelés alapértelmezett fájlformátum. 

Módosíthatja az oszlop adattípusait később származtatott oszlopot átalakításban. Válassza ki az átalakítás használatával módosíthatja az oszlopok neveit.

![Alapértelmezett adatformátumok a célnyelven beállításainak](media/data-flow/source2.png "alapértelmezett formátumok")

### <a name="add-dynamic-content"></a>Dinamikus tartalom hozzáadása

Ha a beállítás panel mezők belül kattint, megjelenik egy "Dinamikus tartalom hozzáadása" hivatkozást. Ide kattintva, elindul a Kifejezésszerkesztőt. Ez a, amelyen beállíthatja értékek beállítások segítségével dinamikusan kifejezések, statikus konstansértékekkel vagy paramétereit.

![Paraméterek](media/data-flow/params6.png "paraméterek")

## <a name="next-steps"></a>További lépések

Épület megkezdése egy [származtatott oszlop átalakítása](data-flow-derived-column.md) és a egy [válassza ki az átalakítási](data-flow-select.md).
