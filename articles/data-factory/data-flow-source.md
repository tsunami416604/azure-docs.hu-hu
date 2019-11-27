---
title: Forrás-átalakítás a leképezési adatfolyamban
description: Megtudhatja, hogyan állíthatja be a forrás-átalakítást a leképezési adatfolyamban.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 33a63b8a887594747aba03e19c107653e438853f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217733"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Forrás-átalakítás a leképezési adatfolyamhoz 

A forrás-átalakítás konfigurálja az adatforrást az adatfolyamhoz. Az adatfolyamatok tervezésekor az első lépés mindig a forrás-átalakítás konfigurálását fogja beállítani. Forrás hozzáadásához kattintson a **forrás hozzáadása** mezőre az adatfolyam-vászonban.

Minden adatfolyamhoz szükség van legalább egy forrás-átalakításra, de az adatátalakítások végrehajtásához tetszőleges számú forrást adhat hozzá. Ezeket a forrásokat összekapcsolhatja a csatlakozással, a kereséssel vagy a Union-transzformációval együtt.

Minden forrás-átalakítás pontosan egy Data Factory adatkészlethez van társítva. Az adatkészlet határozza meg az adatokat, amelyeket írni vagy olvasni szeretne. Ha file-alapú adatkészletet használ, a forrás helyettesítő karaktereit és listáját használva egyszerre több fájllal is dolgozhat.

## <a name="supported-connectors-in-mapping-data-flow"></a>A leképezési adatfolyamban támogatott összekötők

Az adatforgalom leképezése egy kinyerési, betöltési, átalakítási (ELT) módszert követ, és az Azure-ban mind az *előkészítési* adatkészletekkel működik. A forrás-átalakítás jelenleg a következő adatkészleteket használhatja:
    
* Azure Blob Storage (JSON, Avro, szöveg, parketta)
* Azure Data Lake Storage Gen1 (JSON, Avro, szöveg, parketta)
* Azure Data Lake Storage Gen2 (JSON, Avro, szöveg, parketta)
* Azure SQL Data Warehouse
* Azure SQL Database
* Azure CosmosDB

Azure Data Factory több mint 80 natív összekötőhöz férhet hozzá. Az adatfolyamatban lévő más forrásokból származó adatok belefoglalásához használja a másolási tevékenységet az adatok betöltéséhez az egyik támogatott átmeneti területre.

## <a name="source-settings"></a>Forrásbeállítások

Miután hozzáadta a forrást, konfigurálja a **beállításokat a forrás beállításai** lapon. Itt kiválaszthatja vagy létrehozhatja a forrásként szolgáló adatkészletet. Kiválaszthatja az adataihoz tartozó séma-és mintavételi beállításokat is.

![Forrás beállításai lap](media/data-flow/source1.png "Forrás beállításai lap")

**Séma drift:** a [Schema drift](concepts-data-flow-schema-drift.md) az adatforgalomban lévő rugalmas sémák natív módon történő kezelése, anélkül, hogy explicit módon meg kellene határozni az oszlopok módosításait.

* Jelölje be a **séma drift engedélyezése** jelölőnégyzetet, ha a forrás oszlopai gyakran változnak. Ez a beállítás lehetővé teszi, hogy az összes bejövő forrás mező áthaladjon a fogadóra történő átalakításokon.

* A **következtetett oszlopok** kiválasztásakor a rendszer arra utasítja az adatelőállítót, hogy észlelje és definiálja az egyes felderített új oszlopok adattípusait. Ha ez a funkció ki van kapcsolva, az összes lebegő oszlop karakterlánc típusú lesz.

**Séma ellenőrzése:** Ha a séma ellenőrzése beállítás be van jelölve, az adatfolyam nem fog futni, ha a bejövő forrásadatok nem egyeznek az adatkészlet meghatározott sémájával.

**Sorok számának kihagyása:** A sorok kihagyása mező meghatározza, hogy az adatkészlet elején hány sort kell figyelmen kívül hagyni.

**Mintavételezés:** A mintavétel engedélyezése a forrás sorainak számának korlátozásához. Akkor használja ezt a beállítást, ha hibakeresési célból teszteli vagy felveszi az adatait a forrásból.

**Többsoros sorok:** Válassza a többsoros sorok lehetőséget, ha a forrás szövegfájl olyan karakterlánc-értékeket tartalmaz, amelyek több sorra kiterjednek, azaz egy értéken belül sortöréseket.

Ha ellenőrizni szeretné, hogy a forrás megfelelően van-e konfigurálva, kapcsolja be a hibakeresési módot, és olvassa be az adatelőnézett. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Ha a hibakeresési mód be van kapcsolva, a hibakeresési beállításokban szereplő sorok korlátozása beállítás felülírja a forrás mintavételi beállításait az adatelőnézet során.

## <a name="file-based-source-options"></a>Fájl alapú forrás beállításai

Ha olyan fájl-alapú adatkészletet használ, mint például az Azure Blob Storage vagy Azure Data Lake Storage, a **forrás beállításai** lapon kezelheti, hogy a forrás hogyan olvassa be a fájlokat.

![Forrás beállításai](media/data-flow/sourceOPtions1.png "Forrás beállításai")

**Helyettesítő karakter elérési útja:** A helyettesítő karakterek használatakor a rendszer az ADF-et az egyes egyező mappákon és fájlokon keresztül, egyetlen forrás-átalakításon keresztüli hurokra utasítja. Ez egy hatékony módszer több fájl feldolgozására egyetlen folyamaton belül. Több helyettesítő karakterből álló mintázatot is hozzáadhat, ha a meglévő helyettesítő karakter fölé viszi a kurzort.

A forrás tárolóban válasszon egy olyan fájlt, amely megfelel a mintának. Az adatkészletben csak tároló adható meg. A helyettesítő karakteres elérési útnak ezért a gyökérmappa elérési útját is tartalmaznia kell.

Helyettesítő karakteres példák:

* ```*``` a karakterek tetszőleges halmazát jelöli
* a ```**``` rekurzív címtár-beágyazást jelöl
* ```?``` egy karaktert cserél le
* ```[]``` a zárójelben szereplő további karakterek egyikének felel meg

* ```/data/sales/**/*.csv``` lekéri az összes CSV-fájlt a/Data/Sales alatt
* ```/data/sales/20??/**``` beolvassa az összes fájlt a 20. században
* ```/data/sales/2004/*/12/[XY]1?.csv``` beolvassa az összes CSV-fájlt a 2004-as verzióban, amely egy kétjegyű számú X vagy Y előtaggal kezdődik.

**Partíció gyökerének elérési útja:** Ha a forrásban particionált mappák vannak ```key=value``` formátumban (például Year = 2019), akkor a partíciós mappa legfelső szintjét hozzárendelheti az adatfolyami adatfolyamban lévő oszlop neveként.

Először állítson be egy helyettesítő karaktert, amely tartalmazza az összes olyan elérési utat, amely a particionált mappák és az elolvasni kívánt levél fájlok.

![Partíciós forrásfájl beállításai](media/data-flow/partfile2.png "Partíciós fájl beállítása")

A partíció gyökerének elérési útja beállítással határozhatja meg, hogy a mappa szerkezete milyen felső szintű legyen. Amikor az adatelőnézet használatával tekinti meg az adatai tartalmát, látni fogja, hogy az ADF hozzáadja az egyes mappák szintjein talált feloldott partíciókat.

![Partíció gyökerének elérési útja](media/data-flow/partfile1.png "Partíciós gyökér elérési útjának előnézete")

**Fájlok listája:** Ez egy beállított fájl. Hozzon létre egy szövegfájlt, amely tartalmazza a feldolgozandó relatív elérési út fájljainak listáját. Mutasson erre a szövegfájlra.

**A fájl nevét tároló oszlop:** Tárolja a forrásfájl nevét az adataiban található oszlopban. Itt adjon meg egy új oszlopnevet a fájlnév karakterláncának tárolásához.

**Befejezés után:** Ha az adatfolyam futtatása után semmit nem kíván végrehajtani a forrásfájlban, törölje a forrásfájlt, vagy helyezze át a forrásfájlt. Az áthelyezés elérési útjai relatívak.

Ha a forrásfájlokat másik helyre szeretné áthelyezni a feldolgozás után, először válassza a "áthelyezés" lehetőséget a fájl művelethez. Ezután állítsa be a "from" könyvtárat. Ha nem használ helyettesítő karaktereket az elérési úthoz, akkor a "from" beállítás lesz a forrás mappájával megegyező mappa.

Ha a forrás elérési útja helyettesítő karakterrel rendelkezik, a szintaxis az alábbihoz hasonlóan fog kinézni:

```/data/sales/20??/**/*.csv```

A "from" lehetőséget adhatja meg

```/data/sales```

És "to"

```/backup/priorSales```

Ebben az esetben a rendszer a/Data/Sales alatt forrásozott összes fájlt áthelyezi a/backup/priorSales.

> [!NOTE]
> A fájl műveletei csak akkor futnak, amikor egy folyamaton belül indítja el az adatfolyamatot (a folyamat hibakeresési vagy végrehajtási futtatása), amely az adatfolyamok végrehajtása tevékenységet használja egy folyamaton belül. A fájl műveletei *nem* futnak adatfolyam-hibakeresési módban.

**Szűrés legutóbb módosítva:** A feldolgozás alatt álló fájlokat szűrheti, ha az utolsó módosítás dátumát megadja. Az összes dátum-idő UTC. 

### <a name="add-dynamic-content"></a>Dinamikus tartalom hozzáadása

Minden Forrástípus megadható kifejezésként a [leképezési adatfolyam átalakítási kifejezésének nyelve](data-flow-expression-functions.md)alapján. Dinamikus tartalom hozzáadásához kattintson vagy vigye a kurzort a mezők között a beállítások panelen. Kattintson a hivatkozásra a **dinamikus tartalom hozzáadásához**. Ez elindítja a Kifejezésszerkesztő kifejezést, ahol a kifejezéseket, a statikus literál értékeket vagy a paramétereket dinamikusan állíthatja be.

![Paraméterek](media/data-flow/params6.png "Paraméterek")

## <a name="sql-source-options"></a>SQL-forrás beállításai

Ha a forrás SQL Database vagy SQL Data Warehouse, a **forrás beállításai** lapon további SQL-specifikus beállítások érhetők el. 

**Bemenet:** Válassza ki, hogy a forrást egy táblán (```Select * from <table-name>```), vagy egy egyéni SQL-lekérdezést szeretne megadni.

**Lekérdezés**: Ha a beviteli mezőben a lekérdezés lehetőséget választotta, adjon meg egy SQL-lekérdezést a forráshoz. Ez a beállítás felülbírálja az adatkészletben kiválasztott összes táblát. Az **Order by** záradékok itt nem támogatottak, de a teljes select from utasítással is megadható. A felhasználó által definiált Table functions is használható. a **select * from udfGetData ()** egy olyan UDF az SQL-ben, amely egy táblázatot ad vissza. Ez a lekérdezés létrehoz egy forrástábla, amelyet az adatfolyamatában használhat. A lekérdezések használata nagyszerű lehetőséget nyújt a sorok tesztelésre vagy keresésekre való csökkentésére is. Például: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Köteg mérete**: adjon meg egy batch-méretet, amely nagy mennyiségű adatokat olvas be.

**Elkülönítési szint**: a leképezési adatfolyamban található SQL-források alapértelmezett értéke nem véglegesítve. A következő értékek egyikére módosíthatja az elkülönítési szintet:
* Olvasás véglegesítve
* Nem véglegesített olvasás
* Ismételhető olvasás
* Szerializálható
* Nincs (elkülönítési szint figyelmen kívül hagyása)

![Elkülönítési szint](media/data-flow/isolationlevel.png "Elkülönítési szint")

## <a name="projection"></a>Vetület

Az adatkészletekben lévő sémák esetében a forrás vetülete határozza meg a forrásadatok adatoszlopait, típusait és formátumait. A legtöbb adatkészlet, például az SQL és a parketta esetében a forrás kivetítése úgy van rögzítve, hogy tükrözze az adatkészletben definiált sémát. Ha a forrásfájlok nem megfelelően vannak beírva (például a sima CSV-fájlok a Parquet-fájlok helyett), megadhatja a forrás-átalakítás egyes mezőinek adattípusait.

![Beállítások a kivetítés lapon](media/data-flow/source3.png "Vetület")

Ha a szövegfájl nem rendelkezik meghatározott sémával, válassza az **adattípusok észlelése** lehetőséget, hogy Data Factory az adattípusok mintavételezését és következtetését. Válassza az **alapértelmezett formátum megadása** lehetőséget az alapértelmezett adatformátumok automatikus észleléséhez. 

Módosíthatja az oszlop adattípusait egy lefelé irányuló adatfolyamból származtatott oszlop transzformációjában. Az oszlopnevek módosításához válasszon transzformációt.

### <a name="import-schema"></a>Séma importálása

Az összetett adatstruktúrákat támogató adatkészletek, például a Avro és a CosmosDB nem igénylik a séma-definíciókat az adatkészletben. Ezért a "séma importálása" gombra kattintva megtekintheti az ilyen típusú források kivetítés lapját.

## <a name="cosmosdb-specific-settings"></a>CosmosDB-specifikus beállítások

A CosmosDB használatakor a következő szempontokat érdemes figyelembe venni:

* Rendszeroszlopok belefoglalása: Ha bejelöli ezt a lehetőséget, ```id```, ```_ts```és más rendszeroszlopok is szerepelni fognak a CosmosDB-ból származó adatfolyam-metaadatokban. Gyűjtemények frissítésekor fontos, hogy a meglévő sor azonosítóját is megragadja.
* Oldalméret: a lekérdezés eredményének egy oldalára vonatkozó dokumentumok száma. Az alapértelmezett érték az "-1", amely a szolgáltatás dinamikus oldalát használja akár 1000-ig.
* Átviteli sebesség: állítsa be a nem kötelező értéket a CosmosDB-gyűjteményre alkalmazni kívánt RUs számára az olvasási művelet során az adott adatfolyam minden egyes végrehajtásához. Minimális értéke 400.
* Előnyben részesített régiók: kiválaszthatja a folyamat előnyben részesített olvasási régióit.

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
