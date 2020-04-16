---
title: Azure Synapse Studio (előzetes verzió) jegyzetfüzetek létrehozása, fejlesztése és karbantartása
description: Ebben a cikkben megtudhatja, hogyan hozhat létre és fejleszthet Azure Synapse Studio (előzetes verzió) jegyzetfüzeteket az adatok előkészítéséhez és vizualizálásához.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430226"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Azure Synapse Studio (előzetes verzió) jegyzetfüzetek létrehozása, fejlesztése és karbantartása

Az Azure Synapse Studio (előzetes verzió) jegyzetfüzet egy webes felület, amely élő kódot, vizualizációkat és elbeszélési szöveget tartalmazó fájlokat hozhat létre. A jegyzetfüzetek jó hely az ötletek érvényesítésére, és gyors kísérletek segítségével betekintést nyerhetnek az adatokba. Notebookok is széles körben használják az adatok előkészítése, adatvizualizáció, gépi tanulás, és más Big Data-forgatókönyvek.

Az Azure Synapse Studio notebook, a következőket teheti:

* Első lépések a nulla beállítási erőfeszítéssel.
* A beépített vállalati biztonsági funkciókkal biztonságban tarthatja az adatokat.
* Adatok elemzése nyers formátumok (CSV, txt, JSON, stb), feldolgozott fájlformátumok (parketta, Delta Lake, ORC, stb), és az SQL táblázatos adatfájlok ellen Spark és sql.
* A továbbfejlesztett szerzői lehetőségekkel és a beépített adatmegjelenítéssel hatékonyan dolgozhat.

Ez a cikk ismerteti, hogyan használhatja a jegyzetfüzetek az Azure Synapse Studio.

## <a name="create-a-notebook"></a>Jegyzetfüzet létrehozása

Jegyzetfüzetet kétféleképpen hozhat létre. Új jegyzetfüzetet hozhat létre, vagy importálhat egy meglévő jegyzetfüzetet egy Azure Synapse-munkaterületre az **Objektumkezelőből.** Az Azure Synapse Studio notebookok képes felismerni a szabványos Jupyter Notebook IPYNB fájlokat.

![szinapszis-létrehozás-importálás-jegyzetfüzet](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Jegyzetfüzetek fejlesztése

A jegyzetfüzetek cellákból állnak, amelyek egyedi kód- vagy szövegblokkok, amelyek önállóan vagy csoportként futtathatók.

### <a name="add-a-cell"></a>Cella hozzáadása

Többféleképpen is hozzáadhat egy új cellát a jegyzetfüzethez.

1. Bontsa ki a bal felső **+ Cella** gombot, és válassza **a Kódcella hozzáadása** vagy a **Szövegcella hozzáadása**lehetőséget.

    ![cella hozzáadása cella-gombbal](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Mutasson az egérrel a két cella közötti szóközre, és válassza **a Kód hozzáadása** vagy szöveg **hozzáadása**lehetőséget.

    ![cella-tér közötti hozzáadása](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Parancsmódban használja [a billentyűparancsokat.](#shortcut-keys-under-command-mode) **Az A** billentyű lenyomásával szúrjon be egy cellát az aktuális cella fölé. A **B** billentyű lenyomásával szúrjon be egy cellát az aktuális cella alá.

### <a name="set-a-primary-language"></a>Elsődleges nyelv beállítása

Az Azure Synapse Studio notebookok négy spark nyelvet támogatnak:

* pyspark (piton)
* szikra (Scala)
* szikraSQL
* Spark.NET (C#)

A felső parancssáv legördülő listájából beállíthatja az új hozzáadott cellák elsődleges nyelvét.

   ![alapértelmezett-szinapszid-nyelvű](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Több nyelv használata

Egy jegyzetfüzetben több nyelvet is használhat, ha a cella elején megadja a megfelelő nyelvi mágikus parancsot. Az alábbi táblázat a sejtnyelvek közötti váltásra leplezési parancsokat sorolja fel.

|Magic parancs |Nyelv | Leírás |  
|---|------|-----|
|%%pyspark| Python | **Python-lekérdezés** végrehajtása a Spark-környezetben.  |
|%%szikra| Scala | **Scala-lekérdezés** végrehajtása a Spark-környezetben.  |  
|%%sql| SparkSQL | **SparkSQL-lekérdezés** végrehajtása a Spark-környezetben.  |
|%%éles | Spark.NET C # | A **Spark-környezet Spark.NET C#** lekérdezés végrehajtása. |

Az alábbi képen egy példa látható, hogyan írhat PySpark-lekérdezést a **%%pyspark** magic paranccsal vagy egy SparkSQL-lekérdezést a **(%%sql** magic paranccsal egy **Spark(Scala)** jegyzetfüzetben. Figyelje meg, hogy a jegyzetfüzet elsődleges nyelve Scala.

   ![szinapszis-szikra-varázslatok](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Ideiglenes táblák használata nyelvek közötti adatokhivatkozáshoz

A Synapse Studio-jegyzetfüzetekben nem hivatkozhat közvetlenül a különböző nyelveken lévő adatokra vagy változókra. A Sparkban egy ideiglenes tábla is hivatkozhat a nyelvek között. Íme egy példa a `Scala` DataFrame olvasására `PySpark` és `SparkSQL` a Spark hőmérséklet-táblájának megkerülő módon történő használatára.

1. Az 1 cellában olvassa el a DataFrame-et az SQL-készlet összekötőből a Scala használatával, és hozzon létre egy ideiglenes táblát.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. A 2 cellában a Spark SQL használatával lekérdezi az adatokat.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. A 3-as cellában használja a PySpark adatait.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-stílusú IntelliSense

Az Azure Synapse Studio notebookok integrálva vannak a Monaco szerkesztővel, hogy ide-stílusú IntelliSense-t vigyenek a cellaszerkesztőbe. A szintaxis kiemelése, a hibakészítő és az automatikus kódkiegészítés segít a kód írásában és a problémák gyorsabb azonosításában.

Az IntelliSense funkciói különböző érettségi szinteken vannak elérhetők a különböző nyelvekesetében. Az alábbi táblázat segítségével megtekintheti, hogy mi támogatott.

|Nyelvek| Szintaxis kiemelése | Szintaktikai hibajelölő  | Szintaktikai kód kiegészítése | Változókód befejezése| Rendszerfüggvény-kód befejezése| Felhasználói függvény kódjának befejezése| Intelligens behúzás | Kód hajtogatás|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Szikra (Scala)|Igen|Igen|Igen|Igen|-|-|-|Igen|
|SparkSQL|Igen|Igen|-|-|-|-|-|-|
|Spark.NET (C#)|Igen|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Szövegcella formázása eszköztárgombokkal

A szövegcellák eszköztárának formátumgombjaival általános jelölési műveleteket tehet ünk. Ez magában foglalja a félkövér szöveg, dőlt szöveg, beszúrása kódrészletek, beszúrása rendezetlen lista, beszúrása rendezett lista és beszúrása képet URL-t.

  ![szinapszis-szöveg-cella-eszköztár](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Cellaműveletek visszavonása
Kattintson a **visszavonás** gombra, vagy nyomja le a **Ctrl+Z billentyűkombinációt** a legutóbbi cellaművelet visszavonásához. Most már visszavonhatja a legutóbbi 20 történelmi cellaműveletet. 

   ![szinapszis-undo-sejtek](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Cella áthelyezése

A jobb szélen található további cellaműveletek menüjének eléréséhez válassza a három pontot (...). Ezután válassza **a Cella felfelé mozgatása** vagy a **Cella lefelé mozgatása** lehetőséget az aktuális cella áthelyezéséhez. 

A [billentyűparancsokat parancsmódban is használhatjuk.](#shortcut-keys-under-command-mode) Nyomja **le a Ctrl+Alt+↑ billentyűkombinációt** az aktuális cella feljebb lépéséhez. Nyomja **le a Ctrl+Alt+® billentyűkombinációt** az aktuális cella lefelé mozgatásához.

   ![cella mozgatása](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Cella törlése

Cella törléséhez jelölje ki a három pontot (...) a jobb szélen található további cellaműveletek menüjének eléréséhez, majd válassza a **Cella törlése parancsot.** 

A [billentyűparancsokat parancsmódban is használhatjuk.](#shortcut-keys-under-command-mode) Az aktuális cella törléséhez nyomja le a **D, D** billentyűt.
  
   ![törlés-a-cella](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Cellabevitel összecsukása
Az összecsukáshoz kattintson az aktuális cella alján lévő nyílgombra. Kibontásához kattintson a nyílgombra, miközben a cella össze van csukva.

   ![összeomlás-cella-bemenet](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Cellakimenet összecsukása

Az **összecsukáshoz** kattintson az aktuális cellakimenet bal felső részén található összecsukási kimenet gombra. Kibontásához kattintson a **Cellakimenet megjelenítése** elemre, miközben a cellakimenet össze van csukva.

   ![összeomlás-cella-kimenet](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Jegyzetfüzetek futtatása

A jegyzetfüzet kódcelláit egyenként vagy egyszerre is futtathatja. Az egyes sejtek állapota és előrehaladása megjelenik a jegyzetfüzetben.

### <a name="run-a-cell"></a>Cella futtatása

A kódot többféleképpen is futtathatod egy cellában.

1. Mutasson a futtatni kívánt cellára, és jelölje ki a **Cella futtatása** gombot, vagy nyomja le a **Ctrl+Enter billentyűkombinációt.**

   ![futtatás-cella-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. A jobb szélen található további cellaműveletek menüjének eléréséhez jelölje ki a három pontot (**...**). Ezután válassza a **Cella futtatása**lehetőséget.

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Parancsmódban használja [a billentyűparancsokat.](#shortcut-keys-under-command-mode) Az aktuális cella futtatásához és az alábbi cella kijelöléséhez nyomja le a **Shift+Enter** billentyűkombinációt. Az **Alt+Enter billentyűkombinációval** futtassa az aktuális cellát, és szúrjon be egy új cellát alatta.


### <a name="run-all-cells"></a>Az összes cella futtatása
Az **Összes futtatása** gombra kattintva az aktuális jegyzetfüzet összes celláját egymás után futtatja.

   ![minden cellás futtatás](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Az összes cella futtatása felett vagy alatt

A jobb szélen található további cellaműveletek menüjének eléréséhez jelölje ki a három pontot (**...**). Ezután válassza a **Cellák futtatása lehetőséget** a fenti cellák futtatásához az aktuális szint feletti összes cella sorrendben futtatásához. Válassza **az alábbi Cellák futtatása** lehetőséget az aktuális cella alatt egymás után futtatva.

   ![run-sejtek felett vagy alatta](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Cella állapotjelzője

A cella alatt lépésről lépésre megjelenő cella-végrehajtási állapot jelenik meg, amely segít az aktuális állapot megtekintésében. Miután a cella futtatása befejeződött, a teljes időtartamú és befejezési idővel rendelkező végrehajtási összegzés jelenik meg, és a későbbi hivatkozáshoz ott marad.

![cella állapota](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Szikraállapot-jelző

Az Azure Synapse Studio notebook tisztán Spark alapú. A kódcellák távolról kerülnek végrehajtásra a Spark-készleten. A Spark-feladat előrehaladási jelző jegeli a valós idejű folyamatjelző jelenik meg, amely segít megérteni a feladat végrehajtási állapotát.


![szikra-haladás-mutató](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark-munkamenet konfigurációja

Megadhatja az időtúlérték időtartamát, a számát és a végrehajtók méretét, akiket az aktuális Spark-munkamenetnek a **munkamenet konfigurálása című szakaszban**kell megadnia. Indítsa újra a Spark-munkamenet a konfigurációs módosítások érvénybe léptetéséhez. Az összes gyorsítótárazott jegyzetfüzetváltozó törlődik.

![munkamenet-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Adatok jegyzetfüzetbe vitele

Adatokat tölthet be az Azure Blob Storage, az Azure Data Lake Store Gen 2 és az SQL-készletből, ahogy az az alábbi kódmintákban látható.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>CsV olvasása az Azure Data Lake Store Gen2-ből Spark DataFrame-ként

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>CsV olvasása az Azure Blob Storage-ból Spark DataFrame-ként

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Adatok olvasása az elsődleges tárfiókból

Az elsődleges tárfiókban lévő adatok közvetlenül is hozzáférhetnek. Nem kell megadni a titkos kulcsokat. Az Adatkezelőben kattintson a jobb gombbal egy fájlra, és válassza **az Új jegyzetfüzet** lehetőséget az automatikusan létrehozt adatkibontó új jegyzetfüzet megtekintéséhez.

![adat-cellába](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Jegyzetfüzetben lévő adatok megjelenítése

### <a name="display"></a>Megjelenítés()

A táblázatos eredménynézet sávdiagram, vonaldiagram, kördiagram, pontdiagram és területdiagram létrehozásának lehetősége. Az adatokat kód írása nélkül is megjelenítheti. A diagramok testre szabhatók a **Diagram beállításai**párbeszédpanelen. 

**A(z) %%sql** magic parancsok kimenete alapértelmezés szerint a renderelt tábla nézetben jelenik meg. A Spark DataFrames vagy a Rugalmas elosztott adatkészletek (RDD) **függvény megjelenítés(`<DataFrame name>`)** hívásával a renderelt táblanézet létrehozásához.

   ![beépített grafikonok](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

A HTML vagy interaktív könyvtárakat, például **a bokeh-t**a **displayHTML()** segítségével jelenítheti meg.

Az alábbi képen egy példa a karakterjelek térképen történő ábrázolására **a bokeh**használatával.

   ![bokeh-példa](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Futtassa a következő mintakódot a fenti kép rajzolásához.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>Jegyzetfüzetek mentése

Egyetlen jegyzetfüzetet vagy az összes jegyzetfüzetet mentheti a munkaterületre.

1. Ha egyetlen jegyzetfüzeten végzett módosításokat szeretne menteni, kattintson a jegyzetfüzet **parancssávján** található Közzététel gombra.

   ![közzétételi jegyzetfüzet](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Ha az összes jegyzetfüzetet a munkaterületre szeretné menteni, jelölje be az **Összes közzététele** gombot a munkaterület parancssávján. 

   ![közzététel-minden](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

A jegyzetfüzet tulajdonságaiban beállíthatja, hogy a cellakimenet et is belefoglalja-e a cellakimenetbe mentéskor.

   ![jegyzetfüzet-tulajdonságok](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Mágikus parancsok
Az Azure Synapse Studio-jegyzetfüzetekben használhatja a jól ismert Jupyter mágikus parancsokat. Ellenőrizze az alábbi listát, mint az aktuálisrendelkezésre álló mágikus parancsokat. Mondja el a használati eseteket a GitHubon, hogy továbbra is további mágikus parancsokat építhessünk ki az Ön igényeinek megfelelően.

Elérhető vonalvarázslatok: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Elérhető cellavarázslatok: [%%idő,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Billentyűparancsok

A Jupyter-jegyzetfüzetekhez hasonlóan az Azure Synapse Studio-jegyzetfüzetek modális felhasználói felülettel rendelkeznek. A billentyűzet attól függően, hogy a notebookcella melyik üzemmódban van, különböző dolgokat tesz meg. A Synapse Studio notebookok a következő két módot támogatják egy adott kódcellához: parancsmód és szerkesztési mód.

1. A cella parancsmódban van, ha nincs szövegkurzor, amely a gépelést kéri. Ha egy cella parancs módban van, a jegyzetfüzet egészét szerkesztheti, de nem írhat be az egyes cellákba. Adja meg a `ESC` parancsmódot az egér rel vagy egérrel a cella szerkesztőterületén kívülre való kattintáshoz.

   ![parancs-mód](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. A szerkesztési módot egy szöveges kurzor jelzi, amely a szerkesztőterületre való beírást kéri. Ha egy cella szerkesztési módban van, nem írhat be a cellába. Adja meg a `Enter` szerkesztési módot az egér gombnyomásával, hogy kattintson a cella szerkesztőterületén.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Billentyűparancsok parancsmódban

A következő billentyűleütés-billentyűparancsokkal könnyebben navigálhat és futtathat kódot az Azure Synapse-jegyzetfüzetekben.

| Műveletek |Synapse Studio notebook parancsikonok  |
|--|--|
|Az aktuális cella futtatása és az alábbi kijelölés | Shift+Enter |
|Az aktuális cella futtatása és beszúrása | Alt+Enter |
|Cella kijelölése felett| Fel |
|Cella kijelölése| Le |
|Cella beszúrása felett| A |
|Cella beszúrása alatt| B |
|Kijelölt cellák kiterjesztése felett| Shift+Fel |
|Kijelölt cellák kiterjesztése alatt| Shift+Le|
|Cella feljebb mozgatása| Ctrl+Alt+↑ |
|Cella mozgatása lefelé| Ctrl+Alt+▼ |
|Kijelölt cellák törlése| D, D |
|Váltás szerkesztési módra| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>Billentyűparancsok szerkesztési módban

A következő billentyűleütés-billentyűparancsokkal könnyebben navigálhat és futtathat kódot az Azure Synapse-jegyzetfüzetekben Szerkesztés módban.

| Műveletek |Synapse Studio notebook parancsikonok  |
|--|--|
|A kurzor feljebb mozgatása | Fel |
|A kurzor lefelé mozgatása|Le|
|Visszavonás|Ctrl + Z|
|Ismétlés|Ctrl + Y|
|Megjegyzés/Megjegyzés elmegjegyzést ásása|Ctrl + /|
|Szó törlése előtte|Ctrl + Backspace|
|Szó törlése után|Ctrl + Törlés|
|Ugrás a cella indításakor|Ctrl + Kezdőlap|
|Ugrás a cella végére |Ctrl + Befejezés|
|Menj egy szó balra|Ctrl + Bal|
|Egy szóval jobbra|Ctrl + Jobb|
|Az összes kijelölése|Ctrl + A|
|Behúzás| Ctrl + ]|
|Elszegényedés|Ctrl + [|
|Váltás parancsmódra| Esc |

## <a name="next-steps"></a>További lépések

- [Az Apache Spark dokumentációja .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
