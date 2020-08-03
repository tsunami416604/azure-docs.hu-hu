---
title: Szinapszis Studio-jegyzetfüzetek
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és fejleszthet Azure szinapszis Studio (előzetes verzió) jegyzetfüzeteket az adatelőkészítés és a vizualizációk elvégzéséhez.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: tracking-python
ms.openlocfilehash: a7dc0fcae9a6fea789d30bac10511007454ecc5f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504023"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>A szinapszis Studio (előzetes verzió) jegyzetfüzetek létrehozása, fejlesztése és karbantartása az Azure szinapszis Analyticsben

A szinapszis Studio (előzetes verzió) jegyzetfüzet egy webes kezelőfelület, amellyel élő kódokat, vizualizációkat és elbeszélési szöveget tartalmazó fájlokat hozhat létre. A jegyzetfüzetek jó kiindulópontot nyújtanak az ötletek érvényesítéséhez és a gyors kísérletekhez az adatokból származó elemzések megszerzéséhez. A jegyzetfüzeteket is széles körben használják az adatok előkészítésében, az adatvizualizációban, a gépi tanulásban és más Big-adatokban.

Az Azure szinapszis Studio notebook használatával a következőket teheti:

* Első lépések a nulla telepítési erőfeszítéssel.
* A beépített nagyvállalati biztonsági funkciókkal megőrizheti az adatvédelmet.
* Elemezheti az adatokat nyers formátumokban (CSV, txt, JSON stb.), a feldolgozott fájlformátumokat (a parketta, a Delta Lake, az ork stb.) és az SQL táblázatos adatfájlokat a Spark és az SQL között.
* Legyen hatékony a fejlett szerzői képességek és beépített adatvizualizációk révén.

Ez a cikk bemutatja, hogyan használhatók a jegyzetfüzetek az Azure szinapszis Studióban.

## <a name="create-a-notebook"></a>Jegyzetfüzet létrehozása

Kétféleképpen hozhat létre jegyzetfüzetet. Létrehozhat egy új jegyzetfüzetet, vagy importálhat egy meglévő jegyzetfüzetet egy Azure-beli szinapszis-munkaterületre a **Object Explorerból**. Az Azure szinapszis Studio notebookok felismerik a standard Jupyter Notebook IPYNB-fájlokat.

![szinapszis-Create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Jegyzetfüzetek fejlesztése

A jegyzetfüzetek cellákból állnak, amelyek a kód vagy a szöveg különálló, egymástól függetlenül vagy csoportként futtatható blokkai.

### <a name="add-a-cell"></a>Cella hozzáadása

Több módon is hozzáadhat új cellákat a jegyzetfüzethez.

1. Bontsa ki a bal felső **+ cella** gombot, és válassza a **kódlap hozzáadása** vagy a **szöveg hozzáadása cellát**.

    ![cella hozzáadása a cellához – gomb](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Vigye a kurzort a két cella közötti területre, majd válassza a **kód hozzáadása** vagy a **szöveg hozzáadása**lehetőséget.

    ![cella – térköz hozzáadása](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [Billentyűparancsok használata a parancs módban](#shortcut-keys-under-command-mode). Az **a** billentyű lenyomásával szúrhat be egy cellát az aktuális cella fölé. A **B** billentyű lenyomásával szúrjon be egy cellát az aktuális cella alá.

### <a name="set-a-primary-language"></a>Elsődleges nyelv beállítása

Az Azure szinapszis Studio notebookok négy Apache Spark nyelvet támogatnak:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET Apache Sparkhoz (C#)

Az új hozzáadott cellák elsődleges nyelvét a felső parancssáv legördülő listájából állíthatja be.

   ![alapértelmezett – szinapszis – nyelv](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Több nyelv használata

Több nyelvet is használhat egy jegyzetfüzetben a cella elején található megfelelő nyelvi mágikus parancs megadásával. A következő táblázat a cella nyelveinek váltására szolgáló mágikus parancsokat sorolja fel.

|Magic parancs |Nyelv | Leírás |  
|---|------|-----|
|%% pyspark| Python | **Python** -lekérdezés végrehajtása a Spark-környezettel.  |
|%% Spark| Scala | **Scala** -lekérdezés végrehajtása a Spark-környezettel.  |  
|%% SQL| SparkSQL | **SparkSQL** -lekérdezés végrehajtása a Spark-környezettel.  |
|%% csharp | .NET a Spark C-hez # | .Net- **keretrendszer** végrehajtása Spark C#-lekérdezéshez Spark-környezettel. |

Az alábbi ábrán egy példa látható arra, hogyan írhat PySpark-lekérdezést a ( **z)%% PySpark** Magic paranccsal vagy egy SparkSQL-lekérdezéssel a (z)% **% SQL** Magic paranccsal a **Spark (Scala)** jegyzetfüzetben. Figyelje meg, hogy a jegyzetfüzet elsődleges nyelve a pySpark értékre van állítva.

   ![szinapszis – Spark-Magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Az adatnyelvek közötti hivatkozáshoz használjon ideiglenes táblázatokat

Az adat-és változók nem hivatkozhatnak közvetlenül különböző nyelveken a szinapszis Studio jegyzetfüzetben. A Sparkban egy ideiglenes tábla is szerepelhet a nyelvek között. Íme egy példa arra, hogyan lehet beolvasni egy `Scala` DataFrame a `PySpark` és a `SparkSQL` Spark Temp-tábla használatával megkerülő megoldásként.

1. Az 1. cellában olvassa el a DataFrame az SQL Pool-összekötőről a Scala használatával, és hozzon létre egy ideiglenes táblát.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. A 2. cellában a Spark SQL használatával kérdezheti le az adatgyűjtést.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. A 3. cellában használja a PySpark-ben tárolt adatfájlokat.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-Style IntelliSense

Az Azure szinapszis Studio notebookok integrálva vannak a Monaco-szerkesztővel, hogy IDE-stílusú IntelliSense kerüljön a cellás szerkesztőbe. A szintaxis kiemelése, a hiba jelölője és az automatikus kód befejezése segít a kód írásához és a problémák gyorsabb azonosításához.

Az IntelliSense-funkciók a különböző nyelveken a lejárat különböző szintjein vannak. Az alábbi táblázat segítségével megtekintheti, hogy mi támogatott.

|Nyelvek| Szintaxis kiemelése | Szintaktikai hiba jelölője  | Szintaxis kód befejezése | Változó kód befejezése| Rendszerfunkció kódja – Befejezés| Felhasználói függvény kódjának befejezése| Intelligens behúzás | Kód összecsukható|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Spark (Scala)|Igen|Igen|Igen|Igen|-|-|-|Igen|
|SparkSQL|Igen|Igen|-|-|-|-|-|-|
|.NET for Spark (C#)|Igen|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Szöveg cellájának formázása eszköztár gombjaival

Az általános Markdown műveletek végrehajtásához használhatja a Text Cells eszköztár Format (formázás) gombját. Félkövér szöveggel, italicizing szöveggel, kódrészletek beszúrásával, Rendezetlen lista beszúrásával, rendezett lista beszúrásával és a rendszerkép URL-címének beszúrásával is magában foglalja.

  ![szinapszis – szöveg-cella-eszköztár](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Cella műveleteinek visszavonása
Kattintson a **Visszavonás** gombra, vagy nyomja le a **CTRL + Z** billentyűkombinációt a legutóbbi cella művelet visszavonásához. Most visszavonhatja a legutóbbi 20 korábbi cella műveleteit. 

   ![szinapszis-visszavonás – cellák](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Cella áthelyezése

Kattintson a három pontra (...), és nyissa meg a jobb szélen a további cella műveletek menüt. Ezután válassza a **cella áthelyezése felfelé** vagy a **cella lejjebb** lehetőséget az aktuális cella áthelyezéséhez. 

[A billentyűparancsokat a parancs módban](#shortcut-keys-under-command-mode)is használhatja. Nyomja le a **CTRL + ALT + ↑** billentyűkombinációt az aktuális cella mozgatásához. Nyomja le a **CTRL + ALT + ↓** billentyűkombinációt az aktuális cella lefelé való áthelyezéséhez.

   ![cella áthelyezése](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Cella törlése

Ha törölni szeretne egy cellát, válassza a három pontot (...), majd a jobb szélen kattintson a **cella törlése**lehetőségre. 

[A billentyűparancsokat a parancs módban](#shortcut-keys-under-command-mode)is használhatja. Az aktuális cella törléséhez nyomja le a **d, d** billentyűt.
  
   ![cella törlése](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Cella bemenetének összecsukása
Az aktuális cella alján található nyíl gombra kattintva összecsukhatja azt. A kibontásához kattintson a nyíl gombra a cella összecsukása közben.

   ![összecsukás – cella bemenet](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Cella kimenetének összecsukása

Kattintson a **kimenet összecsukása** gombra az aktuális cella kimenetének bal felső részén. A kibontásához kattintson a **cella kimenetének megjelenítése** gombra a cella kimenetének összecsukása közben.

   ![összeomlás – cella-kimenet](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Jegyzetfüzetek futtatása

A kódok celláit egyenként vagy egyszerre is futtathatja a jegyzetfüzetben. Az egyes cellák állapota és előrehaladása a jegyzetfüzetben jelenik meg.

### <a name="run-a-cell"></a>Cella futtatása

A kód több módon is futtatható a cellában.

1. Vigye a kurzort a futtatni kívánt cellára, és válassza a **cella futtatása** gombot, vagy nyomja le a **CTRL + ENTER**billentyűkombinációt.

   ![Futtatás – 1. cella](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. A jobb szélen található további cellahivatkozások menü eléréséhez válassza a három pontot (**..**.). Ezután válassza a **cella futtatása**lehetőséget.

   ![Futtatás – 2. cella](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. [Billentyűparancsok használata a parancs módban](#shortcut-keys-under-command-mode). Nyomja le a **SHIFT + ENTER** billentyűkombinációt az aktuális cella futtatásához, és válassza ki az alábbi cellát. Nyomja le az **ALT + ENTER** billentyűkombinációt az aktuális cella futtatásához és az alábbi új cella beszúrásához.


### <a name="run-all-cells"></a>Az összes cella futtatása
Kattintson az **összes futtatása** gombra az aktuális jegyzetfüzetben lévő összes cella sorba állításához.

   ![Futtatás – összes cella](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Az összes fenti vagy alatti cella futtatása

A jobb szélen található további cellahivatkozások menü eléréséhez válassza a három pontot (**..**.). Ezután válassza a **fenti cellák futtatása** lehetőséget, hogy az összes cellát az aktuálisan futó sorban futtassa. Válassza az **alábbi cellák futtatása** lehetőséget az összes cella az aktuális szakasz alatti futtatásához.

   ![futtatási cellák – fent vagy lent](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Az összes futó cella megszakítása
Az **összes megszakítása** gombra kattintva szakítsa meg a várólistán várakozó futó cellákat vagy cellákat. 
   ![összes cella megszakítása](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

### <a name="cell-status-indicator"></a>Cella állapotának jelzője

Egy lépésenkénti cella-végrehajtási állapot jelenik meg a cella alatt, hogy megtekintse a jelenlegi folyamatát. A cella futásának befejezése után a rendszer megjeleníti a végrehajtás összegzését a teljes időtartammal és a befejezési időponttal, és a későbbiekben is megőrzi őket.

![cella állapota](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark folyamatjelzője

Az Azure szinapszis Studio notebook kizárólag Spark-alapú. A cellákat a Spark-készleten távolról hajtja végre a rendszer. A Spark-feladatok folyamatjelzője egy valós idejű folyamatjelzővel jelenik meg, amely a feladatok végrehajtási állapotának megértéséhez nyújt segítséget.
Az egyes feladatokhoz vagy fázisokhoz tartozó feladatok száma segít a Spark-feladat párhuzamos szintjének azonosításában. Egy adott feladathoz (vagy fázishoz) tartozó Spark felhasználói felületen mélyebben is megtekintheti a feladatot (vagy a szakasz) a hivatkozásra kattintva.


![Spark – folyamat – kijelző](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark-munkamenet konfigurációja

Megadhatja az időtúllépés időtartamát, a számot és a végrehajtók számát, hogy az aktuális Spark-munkamenetet adja meg a **konfigurálási munkamenetben**. Indítsa újra a Spark-munkamenetet a konfigurációs módosítások érvénybe léptetéséhez. Az összes gyorsítótárazott jegyzetfüzet-változó törlődik.

[![munkamenet-kezelés](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png#lightbox)

A Spark munkamenet-ajánló mostantól elérhető a Spark munkamenet-konfiguráció paneljén. Közvetlenül a munkamenet-konfiguráció panelen választhatja ki a Spark-készletet, és megtekintheti, hogy hány csomópontot használ, és hogy hány végrehajtót kíván elérhetővé. Ezek az adatok segíthetnek a munkamenetek méretének megfelelő beállításában, és nem módosítják azt vissza.

![munkamenet – ajánlott](./media/apache-spark-development-using-notebooks/synapse-spark-session-recommender.png)


## <a name="bring-data-to-a-notebook"></a>Adat beolvasása egy jegyzetfüzetbe

Adatok tölthetők be az Azure Blob Storageból, Azure Data Lake Store Gen 2 és az SQL-készletből, ahogy az alábbi kódban is látható.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>CSV-fájl olvasása Azure Data Lake Store Gen2 Spark-DataFrame

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>CSV-fájl olvasása az Azure Blob Storageból Spark-DataFrame

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

### <a name="read-data-from-the-primary-storage-account"></a>Adatok beolvasása az elsődleges Storage-fiókból

Az elsődleges Storage-fiókban lévő adatelérést közvetlenül is elérheti. Nincs szükség a titkos kulcsok megadására. A Adatkezelőban kattintson a jobb gombbal egy fájlra, és válassza az **új jegyzetfüzet** lehetőséget, hogy megjelenjen egy új, automatikusan generált kivonó jegyzetfüzet.

![adatcella](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Az adat megjelenítése egy jegyzetfüzetben

### <a name="produce-rendered-table-view"></a>Megjelenített táblázatos nézet létrehozása

Táblázatos eredményeket tartalmazó nézetet biztosítunk a sávdiagram, a diagram, a tortadiagram, a pontdiagram és a diagramterület létrehozásához. Az adatait anélkül is megjelenítheti, hogy kódot kellene írnia. A diagramok testreszabhatók a **diagram beállításaiban**. 

A **(z)%% SQL** Magic parancsok kimenete alapértelmezés szerint a megjelenített tábla nézetben jelenik meg. Meghívhatja <code>display(df)</code> a Spark DataFrames vagy a rugalmasan elosztott adatkészletek (RDD) függvényt a megjelenített tábla nézet létrehozásához.

   [![beépített diagramok](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png#lightbox)

### <a name="visualize-built-in-charts-from-large-scale-dataset"></a>Beépített diagramok megjelenítése nagy méretű adatkészletből 

Alapértelmezés szerint a <code>display(df)</code> függvény csak az adat első 1000 sorát fogja felvenni a diagramok megjelenítéséhez. Tekintse meg az **összesítést az összes eredménynél** , majd kattintson az **alkalmaz** gombra, a diagramot a teljes adatkészletből fogja alkalmazni. A diagram beállításainak módosításakor a Spark-feladatok elindulnak, és eltarthat egy ideig a diagram kiszámításának és megjelenítésének befejezéséhez. 
    [![beépített diagramok – összesítés – mind](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png#lightbox)


### <a name="visualize-data-statistic-information"></a>Adatstatisztikai adatok megjelenítése
A <code>display(df, summary = true)</code> segítségével megtekintheti egy adott Spark-DataFrame statisztikai összegzését, amely tartalmazza az oszlop nevét, az oszlop típusát, az egyedi értékeket és az egyes oszlopok hiányzó értékeit. Kiválaszthat egy adott oszlopot is a minimális érték, a maximális érték, a középérték és a szórás megjelenítéséhez.
    [![beépített diagramok – összefoglalás ](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png#lightbox)

### <a name="render-html-or-interactive-libraries"></a>HTML-vagy interaktív könyvtárak renderelése

A **displayHTML ()** használatával HTML-vagy interaktív kódtárakat (például **bokeh**) is megjelenítheti.

Az alábbi ábrán egy példa látható a karakterjelek ábrázolására a **bokeh**használatával.

   ![bokeh – példa](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Futtassa a következő mintakód a fenti rendszerkép rajzolásához.

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

A munkaterületen egyetlen jegyzetfüzet vagy az összes jegyzetfüzet is menthető.

1. Egyetlen jegyzetfüzeten végrehajtott módosítások mentéséhez kattintson a **Közzététel** gombra a jegyzetfüzet parancssáv sávján.

   ![közzététel – jegyzetfüzet](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Ha a munkaterületen lévő összes jegyzetfüzetet menteni szeretné, válassza az **összes közzététele** gombot a munkaterület parancssáv területén. 

   ![közzététel – mind](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

A jegyzetfüzet tulajdonságainál beállíthatja, hogy a cella kimenete megjelenjen-e a mentéskor.

   ![Jegyzetfüzet – tulajdonságok](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Mágikus parancsok
Használhatja az ismerős Jupyter Magic-parancsokat az Azure szinapszis Studio jegyzetfüzetben. Az alábbi listában tekintse meg az aktuálisan elérhető Magic-parancsokat. Ismertesse a GitHubon a használati eseteit, hogy továbbra is felépíthetjük az igényeinek megfelelő Magic-parancsokat.

Elérhető vonali varázslatok: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%)](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Elérhető cella-varázslatok: [%% idő](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% csharp](#use-multiple-languages)


## <a name="orchestrate-notebook"></a>Jegyzetfüzetek összehangolása

### <a name="add-a-notebook-to-a-pipeline"></a>Jegyzetfüzet hozzáadása egy folyamathoz

Kattintson a jobb felső sarokban található **Hozzáadás a folyamathoz** gombra a jegyzetfüzet meglévő folyamathoz való hozzáadásához, vagy egy új folyamat létrehozásához.

![Hozzáadás a folyamathoz](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Paraméterek cellájának kijelölése

A parametrizáljaéhez kattintson a három pontra (...), és nyissa meg a jobb szélen a további cella műveletek menüt. Ezután válassza a **paraméter cellájának váltása** lehetőséget a cella paraméterként való kijelöléséhez.

![váltás – paraméter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

Azure Data Factory megkeresi a parameters (paraméterek) cellát, és ezt a cellát a végrehajtási időpontban átadott paraméterek alapértelmezett értékeiként kezeli. A végrehajtó motor egy új cellát ad hozzá a paraméterek cellához a bemeneti paraméterekkel, hogy felülírja az alapértelmezett értékeket. Ha nincs megadva paraméter cella, a rendszer beszúrja az injektált cellát a jegyzetfüzet elejére.

### <a name="assign-parameters-values-from-a-pipeline"></a>Paraméterek értékének kiosztása egy folyamatból

Miután létrehozott egy jegyzetfüzetet a paraméterekkel, végrehajthatja azt egy olyan folyamatból, amely az Azure szinapszis notebook-tevékenységét futtatja. Miután hozzáadta a tevékenységet a csővezeték-vászonhoz, a **Beállítások** lap **alapparaméterek** szakaszában állíthatja be a paraméterek értékét. 

![hozzárendelés – paraméter](./media/apache-spark-development-using-notebooks/assign-parameter.png)

A paraméterek értékének kiosztásakor használhatja a [folyamat kifejezésének nyelvét](../../data-factory/control-flow-expression-language-functions.md) vagy a [rendszerváltozókat](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Billentyűparancsok

A Jupyter-jegyzetfüzetekhez hasonlóan az Azure szinapszis Studio notebookok modális felhasználói felülettel rendelkeznek. A billentyűzet különböző műveleteket végez, attól függően, hogy a jegyzetfüzet-cella milyen módban van. A szinapszis Studio notebookok az alábbi két módot támogatják egy adott kódú cellához: parancs mód és szerkesztési mód.

1. A cella parancs módban van, ha nincs beírni kívánt szöveg. Ha egy cella parancs módban van, a jegyzetfüzetet teljes egészében szerkesztheti, de nem lehet egyéni cellákba írni. A parancs mód megadásához nyomja le `ESC` vagy az egér használatával kattintson a cella szerkesztő területén kívülre.

   ![parancssori üzemmód](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. A szerkesztési módot egy szöveges kurzor jelzi, amely arra kéri, hogy írja be a szerkesztőt. Ha egy cella szerkesztési módban van, beírhatja a cellába. Adja meg a szerkesztési módot úgy, `Enter` hogy az egérrel kattint a cella szerkesztői területeire, vagy az egér használatával rákattint.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>A parancssori üzemmód alatti billentyűparancsok

A következő billentyűleütés-billentyűparancsokkal könnyebben navigálhat és futtathat kódot az Azure szinapszis jegyzetfüzetekben.

| Művelet |A szinapszis Studio notebook parancsikonjai  |
|--|--|
|Futtassa az aktuális cellát, és válassza az alábbi lehetőséget. | SHIFT + ENTER |
|Az aktuális cella futtatása és az alábbi beszúrása | ALT + ENTER |
|Cella kijelölése| Fel |
|Válassza ki az alábbi cellát| Le |
|Cella beszúrása a fenti fölé| A |
|Cella beszúrása lent| B |
|A fent kijelölt cellák kiterjesztése| SHIFT + fel |
|A kiválasztott cellák kiterjesztése lejjebb| SHIFT + le|
|Cella mozgatása felfelé| CTRL + ALT + ↑ |
|Cella mozgatása lefelé| CTRL + ALT + ↓ |
|Kijelölt cellák törlése| D, D |
|Váltás szerkesztési módra| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>Szerkesztési mód alatti billentyűparancsok

A következő billentyűleütés-billentyűparancsokkal könnyebben navigálhat és futtathat kódot az Azure szinapszis-jegyzetfüzetekben szerkesztési módban.

| Művelet |A szinapszis Studio notebook parancsikonjai  |
|--|--|
|Kurzor mozgatása felfelé | Fel |
|Kurzor mozgatása lefelé|Le|
|Visszavonás|CTRL + Z|
|Ismétlés|CTRL + Y|
|Megjegyzés/Megjegyzés|CTRL +/|
|A Word törlése előtt|Ctrl + Backspace|
|Szó törlése a következő után|CTRL + DELETE|
|Ugrás a cella elejére|CTRL + Kezdőlap|
|Ugrás a cella végére |Ctrl + End|
|Ugrás egy szóval balra|CTRL + balra|
|Egy szó jobbra|Ctrl + jobb|
|Az összes kijelölése|CTRL + A|
|Behúzás| Ctrl +]|
|Kihúzás|CTRL + [|
|Váltás parancs módba| Esc |

## <a name="next-steps"></a>További lépések
- [Tekintse meg a szinapszis-minta jegyzetfüzeteket](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Rövid útmutató: Apache Spark készlet (előzetes verzió) létrehozása az Azure szinapszis Analytics szolgáltatásban webes eszközök használatával](../quickstart-apache-spark-notebook.md)
- [Az Azure szinapszis Analytics Apache Spark](apache-spark-overview.md)
- [A .NET használata Apache Sparkhoz az Azure szinapszis Analytics használatával](spark-dotnet.md)
- [.NET Apache Spark dokumentációhoz](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
