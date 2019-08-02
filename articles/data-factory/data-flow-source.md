---
title: Forrás-átalakítás beállítása a Azure Data Factory leképezési adatáramlási szolgáltatásában
description: Megtudhatja, hogyan állíthatja be a forrás-átalakítást a leképezési adatfolyamban.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 974ece9cd035ae29ada38f34b7933d86f682194f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696237"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Forrás-átalakítás a leképezési adatfolyamhoz 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A forrás-átalakítás konfigurálja az adatforrást az adatfolyamhoz. Egy adatfolyam több forrás-átalakítást is tartalmazhat. Az adatfolyamatok tervezésekor mindig a forrás-átalakítással kezdjen.

Minden adatfolyamhoz legalább egy forrás-átalakítás szükséges. Az adatátalakítások végrehajtásához tetszőleges számú forrást kell hozzáadnia. Ezeket a forrásokat összekapcsolhatja összekapcsolási átalakítással vagy Union-transzformációval.

> [!NOTE]
> Az adatfolyamatok hibakeresése során az adatok beolvasása a forrásból a mintavételezési beállítás vagy a hibakeresési forrás korlátai alapján történik. Az adatok fogadóba való írásához az adatfolyamot egy adatfolyamati tevékenységből kell futtatnia. 

![Forrás-átalakítási beállítások a forrás beállításai lapon](media/data-flow/source.png "forrás")

Társítsa az adatfolyam forrás-átalakítását pontosan egy Data Factory adatkészlettel. Az adatkészlet határozza meg az adatokat, amelyeket írni vagy olvasni szeretne. A forrásban lévő helyettesítő karakterekkel és fájlokkal egyszerre több fájllal is dolgozhat.

A **helyettesítő karakteres minta** beállítással a rendszer az ADF-et az egyes egyező mappák és fájlok egyetlen forrás-átalakításban való hurkora utasítja. Ez egy nagyon hatékony módszer több fájl feldolgozására egyetlen folyamaton belül. A jelenleg feldolgozott fájlnév nyomon követéséhez a forrás beállításai között állítsa be a "fájlnév tárolására szolgáló oszlop" mező nevét.

> [!NOTE]
> Több helyettesítő karaktert tartalmazó helyettesítő mintát is beállíthat a meglévő helyettesítő karakter melletti + jelre, hogy további helyettesítőkarakter-szabályokat adjon hozzá.

## <a name="data-flow-staging-areas"></a>Adatfolyam-előkészítési területek
Az adatfolyam olyan *előkészítési* adatkészletekkel működik, amelyek mind az Azure-ban találhatók. Ezeket az adatkészleteket használja az előkészítéshez az adatátalakításkor. 

Data Factory közel 80 natív összekötőhöz férhet hozzá. Az adatfolyamatban lévő más forrásokból származó adatok belefoglalásához használja a másolási tevékenység eszközt, hogy az adatok az adatforgalom adatkészletének egyik átmeneti területén legyenek.

## <a name="options"></a>Beállítások

Válassza ki az adataihoz tartozó séma-és mintavételi beállításokat.

### <a name="schema-drift"></a>Sémaeltérés
A [séma-eltolódás](concepts-data-flow-schema-drift.md) az ADF képes natív módon kezelni az adatfolyamatokban lévő rugalmas sémákat anélkül, hogy explicit módon meg kellene határoznia az oszlopok módosításait.

* Válassza a **séma eltolódásának engedélyezése** lehetőséget, ha a forrás oszlopai gyakran változnak. Ez a beállítás lehetővé teszi, hogy az összes bejövő forrás mező áthaladjon a fogadóra történő átalakításokon.

* A **következtetett oszlopok típusának** kiválasztásakor a rendszer az ADF-et az összes felderített oszlop adattípusának definiálására utasítja. Ha ez a funkció ki van kapcsolva, az ADF a karakterláncot fogja feltételezni.

### <a name="validate-schema"></a>Séma ellenőrzése

Ha a forrásadatok bejövő verziója nem egyezik a definiált sémával, az adatfolyam futtatása sikertelen lesz.

![A nyilvános forrás beállításai, amelyek a séma érvényesítésének lehetőségeit, a séma-eltolódás engedélyezését és a mintavételezést mutatják](media/data-flow/source1.png "nyilvános forrás 1")

### <a name="sample-the-data"></a>Az adatgyűjtés mintája
A **mintavétel** engedélyezése a forrás sorainak számának korlátozásához. Akkor használja ezt a beállítást, ha hibakeresési célból teszteli vagy felveszi az adatait a forrásból.

## <a name="define-schema"></a>Séma definiálása

Ha a forrásfájlok nincsenek szigorúan beírva (például a parketta-fájlok helyett egyszerű fájlok), a forrás-átalakításban adja meg az egyes mezők adattípusait.  

![Forrás-átalakítási beállítások a séma definiálása lapon](media/data-flow/source2.png "2. forrás")

Később módosíthatja az oszlopok nevét egy kiválasztott átalakításban. A származtatott oszlop átalakításával módosíthatja az adattípusokat. A szigorúan beírt források esetében az adattípusokat egy későbbi kiválasztott átalakításban módosíthatja. 

![Adattípusok egy kiválasztott átalakításban](media/data-flow/source003.png "") adattípusok

### <a name="optimize-the-source-transformation"></a>A forrás átalakítás optimalizálása

A forrás-átalakítás **optimalizálása** lapján megjelenhet a **forrás** partíció típusa. Ez a beállítás csak akkor érhető el, ha a forrás Azure SQL Database. Ennek az az oka, hogy Data Factory párhuzamosan próbálkozik a csatlakozással, hogy nagy lekérdezéseket futtasson a SQL Database-forráson.

![Forrás partíció beállításai](media/data-flow/sourcepart3.png "particionálás")

Nem kell particionálnia az SQL Database-forrás adatait, de a partíciók nagy lekérdezések esetén hasznosak. A partíciót egy oszlopra vagy egy lekérdezésre alapozhatja.

### <a name="use-a-column-to-partition-data"></a>Oszlop használata az adatparticionáláshoz

A forrás táblából válassza ki a particionálni kívánt oszlopot. Állítsa be a partíciók számát is.

### <a name="use-a-query-to-partition-data"></a>Adatparticionálásra szolgáló lekérdezés használata

Dönthet úgy is, hogy egy lekérdezés alapján particionálja a kapcsolatokat. Egyszerűen adja meg egy WHERE predikátum tartalmát. Adja meg például a következőt: év > 1980.

## <a name="source-file-management"></a>Forrásfájlok kezelése

Válassza a beállítások elemet a forrásban található fájlok kezeléséhez. 

![Új forrás beállításai](media/data-flow/source2.png "Új beállítások")

* **Helyettesítő karakter elérési útja**: A forrás tárolóban válasszon egy olyan fájlt, amely megfelel a mintának. Ez a beállítás felülbírálja az adatkészlet-definícióban található összes fájlt.

Helyettesítő karakteres példák:

* ```*```A karakterek tetszőleges halmazát jelöli.
* ```**```Rekurzív könyvtár beágyazását jelöli
* ```?```Egy karakter cseréje
* ```[]```A zárójelben szereplő több karakternek felel meg.

* ```/data/sales/**/*.csv```Az összes CSV-fájl beolvasása a/Data/Sales alatt
* ```/data/sales/20??/**```Minden fájl beolvasása a huszadik században
* ```/data/sales/2004/*/12/[XY]1?.csv```Minden CSV-fájl beolvasása a 2004-ben decemberben, X vagy Y előtaggal, 2 jegyű számmal

A tárolót meg kell adni az adatkészletben. A helyettesítő karakteres elérési útnak ezért a gyökérmappa elérési útját is tartalmaznia kell.

* **Partíció gyökerének elérési útja**: Ha ```key=value``` formázott mappák vannak a fájlformátumban (azaz év = 2019), akkor a partíciós mappa fájának legfelső szintjének az adatáramlási adatfolyamban való hozzárendeléséhez megkérheti az ADF-t.

Először állítson be egy helyettesítő karaktert, amely tartalmazza az összes olyan elérési utat, amely a particionált mappák és az elolvasni kívánt levél fájlok.

![Partíciós forrásfájl beállításai](media/data-flow/partfile2.png "Partíciós fájl beállítása")

A partíció gyökerének elérési útja beállítás használatával adja meg az ADF-nek, hogy mi a mappa struktúrájának legfelső szintje. Most, hogy megtekinti az adatai tartalmát, látni fogja, hogy az ADF hozzáadja az egyes mappák szintjein talált feloldott partíciókat.

![Partíció gyökerének elérési útja](media/data-flow/partfile1.png "Partíciós gyökér elérési útjának") előnézete

* **Fájlok listája**: Ez egy beállított fájl. Hozzon létre egy szövegfájlt, amely tartalmazza a feldolgozandó relatív elérési út fájljainak listáját. Mutasson erre a szövegfájlra.
* **A fájl nevét tároló oszlop**: Tárolja a forrásfájl nevét az adataiban található oszlopban. Adja meg a fájlnév karakterláncának tárolására szolgáló új nevet.
* **Befejezés után**: Ha az adatfolyam futtatása után semmit nem kíván végrehajtani a forrásfájlban, törölje a forrásfájlt, vagy helyezze át a forrásfájlt. Az áthelyezés elérési útjai relatívak.

Ha a forrásfájlokat másik helyre szeretné áthelyezni a feldolgozás után, először válassza a "áthelyezés" lehetőséget a fájl művelethez. Ezután állítsa be a "from" könyvtárat. Ha nem használ helyettesítő karaktert az elérési úthoz, akkor a "from" beállítás lesz a forrás mappájával megegyező mappa.

Ha a forrás elérési útja helyettesítő karakterrel rendelkezik, a szintaxis az alábbihoz hasonlóan fog kinézni:

```/data/sales/20??/**/*.csv```

A "from" lehetőséget adhatja meg

```/data/sales```

És "to"

```/backup/priorSales```

Ebben az esetben a rendszer a/Data/Sales alatt forrásként használt összes fájlt áthelyezi a/backup/priorSales.

### <a name="sql-datasets"></a>SQL-adatkészletek

Ha a forrás SQL Database vagy SQL Data Warehouse, akkor további lehetőségek állnak rendelkezésre a forrásfájlok kezeléséhez.

* **Lekérdezés**: Adja meg a forráshoz tartozó SQL-lekérdezést. Ez a beállítás felülbírálja az adatkészletben kiválasztott összes táblát. Vegye figyelembe, hogy az **Order by** záradékok itt nem támogatottak, de a teljes select from utasítással is megadható. A felhasználó által definiált Table functions is használható. a **select * from udfGetData ()** egy olyan UDF az SQL-ben, amely egy táblázatot ad vissza. Ez a lekérdezés létrehoz egy forrástábla, amelyet az adatfolyamatában használhat.
* **Köteg mérete**: Adjon meg egy batch-méretet, amely nagy mennyiségű adatokat olvas be.
* **Elkülönítési szint**: Az ADF-leképezési adatfolyamatokban lévő SQL-források alapértelmezett értéke nem véglegesített. A következő értékek egyikére módosíthatja az elkülönítési szintet:
* Olvasás véglegesítve
* Nem véglegesített olvasás
* Ismételhető olvasás
* Szerializálható
* Nincs (elkülönítési szint figyelmen kívül hagyása)

![Elkülönítési szint](media/data-flow/isolationlevel.png "Elkülönítési szint")

> [!NOTE]
> A fájl műveletei csak akkor futnak, amikor egy folyamaton belül indítja el az adatfolyamatot (a folyamat hibakeresési vagy végrehajtási futtatása), amely az adatfolyamok végrehajtása tevékenységet használja egy folyamaton belül. A fájl műveletei *nem* futnak adatfolyam-hibakeresési módban.

### <a name="projection"></a>Vetület

Az adatkészletekben lévő sémák esetében a forrás vetülete határozza meg a forrásadatok adatoszlopait, típusait és formátumait. 

![Beállítások a kivetítés lapon](media/data-flow/source3.png "Kivetítés")

Ha a szövegfájl nem rendelkezik meghatározott sémával, válassza az adattípusok észlelése lehetőséget, hogy Data Factory az adattípusok mintavételezését és következtetését. Válassza az **alapértelmezett formátum megadása** lehetőséget az alapértelmezett adatformátumok automatikus észleléséhez. 

Az oszlop adattípusait egy későbbi származtatott oszlop-átalakításban módosíthatja. Az oszlopnevek módosításához válasszon transzformációt.

![Alapértelmezett adatformátumok beállításai](media/data-flow/source2.png "Alapértelmezett formátumok")

### <a name="add-dynamic-content"></a>Dinamikus tartalom hozzáadása
Ha a beállítások panelen a mezőkön belülre kattint, megjelenik a "dinamikus tartalom hozzáadása" hivatkozás. Ha a Kifejezésszerkesztő elindítását választja, a kifejezéseket, a statikus literál értékeket vagy a paramétereket dinamikusan kell beállítania.

![Paraméterek](media/data-flow/params6.png "Paraméterek")

## <a name="next-steps"></a>További lépések

Egy származtatott [oszlop átalakításának](data-flow-derived-column.md) és egy [kiválasztott átalakítás](data-flow-select.md)létrehozásának megkezdése.
