---
title: Kerékpármegosztással kapcsolatos oktatóanyag – Fejlett adat-előkészítés az Azure Machine Learning Workbench használatával
description: Ebben az oktatóanyagban az Azure Machine Learning Workbench használatával fog egy teljes körű adat-előkészítési műveletet elvégezni
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: 951ce8947d113eaad2ea0e3b5df5e9714aa33dd8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723187"
---
# <a name="tutorial-use-azure-machine-learning-workbench-for-advanced-data-preparation-bike-share-data"></a>Oktatóanyag: Az Azure Machine Learning Workbench használata haladó adat-előkészítéshez (Kerékpármegosztási adatok)
Az Azure Machine Learning (előzetes verzió) egy adatszakértőknek készült, az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőméretű üzembe helyezéséhez létrehozott átfogó, integrált és fejlett adatelemzési megoldás.

Ebben az oktatóanyagban a Machine Learning (előzetes verzió) segítségével a következőket sajátíthatja el:
> [!div class="checklist"]
> * Interaktív adat-előkészítés a Machine Learning adat-előkészítési eszközével.
> * Tesztadatkészlet importálása, átalakítása és létrehozása.
> * Adat-előkészítési csomag létrehozása.
> * Adat-előkészítési csomag futtatása a Python használatával.
> * Betanítási adatkészletet létrehozása az adat-előkészítési csomag újrafelhasználásával további bemeneti fájlokhoz.
> * Szkriptek végrehajtása helyi Azure CLI-ablakban.
> * Szkriptek végrehajtása a felhőalapú Azure HDInsight-környezetben.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning Workbench helyileg telepített példánya. További információkért kövesse a [rövid telepítési útmutatót](../service/quickstart-installation.md).
* Ha nincs telepítve az Azure CLI, kövesse az utasításokat az [Azure parancssori felület (CLI) legújabb verziójának telepítéséhez](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Egy az Azure-ban létrehozott [HDInsight Spark-fürt](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).
* Egy Azure-tárfiók.
* Az új projektek Workbenchben való létrehozásának ismerete.
* Bár ez nem szükséges, célszerű az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) telepítése, amellyel feltölthetők, letölthetők és megtekinthetők a tárfiókban található blobok.

## <a name="data-acquisition"></a>Adatgyűjtés
Ez az oktatóanyag a [Boston Hubway adatkészletet](https://s3.amazonaws.com/hubway-data/index.html), valamint a [NOAA](http://www.noaa.gov/) Bostonra vonatkozó időjárási adatait használja.

1. Töltse le az adatfájlokat a helyi fejlesztési környezetbe az alábbi hivatkozásokra kattintva:

   * [Boston időjárási adatai](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)

   * Hubway-útadatok a Hubway webhelyéről:

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. A letöltés után csomagolja ki egyesével a .zip fájlokat.

## <a name="upload-data-files-to-azure-blob-storage"></a>Adatfájlok feltöltése az Azure Blob Storage-ba
Az Azure Blob Storage használható az adatfájlok tárolására.

1. Használja ugyanazt a tárfiókot, amelyet az Ön által használt HDInsight fürthöz használ.

    ![A HDInsight-fürt tárfiókja](media/tutorial-bikeshare-dataprep/hdinsightstorageaccount.png)

2. Hozzon létre egy új tárolót **data-files** néven, amelyben a **BikeShare**-adatfájlokat tárolja majd.

3. Töltse fel az adatfájlokat. Töltse fel a `BostonWeather.csv` fájlt egy `weather` nevű mappába. Töltse fel az útadatfájlokat egy `tripdata` nevű mappába.

    ![Adatfájlok feltöltése](media/tutorial-bikeshare-dataprep/azurestoragedatafile.png)

> [!TIP]
> A blobfeltöltéshez használhatja a Storage Explorert is. Ezt az eszközt használhatja akkor is, ha meg szeretné tekinteni az oktatóanyagban létrehozott fájlok tartalmát.

## <a name="learn-about-the-datasets"></a>Tudnivalók az adatkészletekről
1. A __Boston weather__ nevű fájl az alábbi, időjárással kapcsolatos mezőket tartalmazza órákra lebontott jelentésekkel:

   * **DATE**

   * **REPORTTPYE**

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. A __hubway__-adatok év és hónap szerint vannak fájlokba rendezve. Például a `201501-hubway-tripdata.zip` nevű fájl egy .csv fájlt tartalmaz, amelyben a 2015. januári adatok szerepelnek. Az adatok a következő mezőket tartalmazzák (minden sor egy kerékpárutat jelöl):

   * **Út időtartama (másodpercben)**

   * **Indulás dátuma és időpontja**

   * **Érkezés dátuma és időpontja**

   * **Kiindulási állomás neve és azonosítója**

   * **Célállomás neve és azonosítója**

   * **Kerékpár azonosítója**

   * **Felhasználó típusa (Alkalmi = 24 vagy 72 órás bérlettel rendelkező felhasználó; Tag = éves vagy havi tagsággal rendelkező felhasználó)**

   * **Irányítószám (ha a felhasználó tag)**

   * **Nem (a tag felhasználó saját bevallása alapján)**

## <a name="create-a-new-project"></a>Új projekt létrehozása
1. Indítsa el a **Machine Learning Workbench** alkalmazást a Start menüből vagy a gyorsindítóból.

2. Hozzon létre egy új Machine Learning-projektet. Kattintson a **+** gombra a **Projektek** lapon, vagy válassza a **Fájl** > **Új** lehetőséget.

   * Használja a **BikeShare** sablont.

   * Adja a projektnek a **BikeShare** nevet. 

## <a id="newdatasource"></a>Új adatforrás létrehozása

1. Hozzon létre egy új adatforrást. Kattintson az **Adat** gombra (henger ikon) a bal oldali eszköztáron az **Adatnézet** megjelenítéséhez.

   ![Adatnézet lap](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Adjon hozzá egy adatforrást. Kattintson a **+** ikonra, és válassza az **Adatforrás hozzáadása** lehetőséget.

   ![Adatforrás hozzáadása lehetőség](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Időjárási adatok hozzáadása

1. **Adattár**: Válassza ki az adattárat, amely az adatokat tartalmazza. Mivel ebben az esetben fájlokat használ, válassza a **Fájl(ok)/Mappa** lehetőséget. A folytatáshoz kattintson a **Tovább** gombra.

   ![Fájl(ok)/Mappa bejegyzés](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Fájl kiválasztása**: Adja hozzá az időjárási adatokat. Keresse meg és válassza ki a Blob Storage-ra korábban feltöltött `BostonWeather.csv` fájlt. Kattintson a **Tovább** gombra.

   ![Fájlkiválasztás, kiválasztva a BostonWeather.csv](media/tutorial-bikeshare-dataprep/azureblobpickweatherdatafile.png)

3. **Fájl részletei**: Ellenőrizze az észlelt fájlsémát. A Machine Learning Workbench elemzi a fájlban lévő adatokat, és kikövetkezteti a használandó sémát.

   ![Fájlok részleteinek ellenőrzése](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > A Workbench egyes esetekben nem képes észlelni a megfelelő sémát. Minden esetben ellenőrizze, hogy a paraméterek megfelelőek-e az adatkészlethez. Az időjárási adatok esetében ellenőrizze, hogy a paraméterek a következő értékekre vannak-e beállítva:
   >
   > * __Fájltípus__: Tagolt fájl (csv, tsv, txt stb.)
   > * __Elválasztó__: Vessző [,]
   > * __Megjegyzéssort jelző karakter__: Nincs érték beállítva.
   > * __Sorkihagyás módja__: Nincs kihagyás
   > * __Fájlkódolás__: UTF-8
   > * __Fejléc-előléptetési mód__: Első fájl fejléceinek használata

   Az adatok előnézetében a következő oszlopoknak kell megjelenniük:

   * **Elérési út**

   * **DATE**

   * **REPORTTYPE**

   * **HOURLYDRYBULBTEMPF**
   
   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

   A folytatáshoz kattintson a **Tovább** gombra.

4. **Adattípusok**: Tekintse át az automatikusan észlelt adattípusokat. A Machine Learning Workbench elemzi a fájlban lévő adatokat, és kikövetkezteti a használandó adattípusokat.

   a. Ezen adatok esetében minden oszlop **ADATTÍPUS** tulajdonságát módosítsa **Sztring** értékre.

   > [!NOTE]
   > A Sztring adattípus még szerepelni fog az oktatóanyag későbbi részében a Workbench funkcióinak bemutatásához. 

   ![Az adattípusok áttekintése](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   b. A folytatáshoz kattintson a __Tovább__ gombra. 

5. **Mintavételezés**: Mintavételezési séma létrehozásához válassza a **Szerkesztés** lehetőséget. Válassza a hozzáadott új __Első 10 000__ sort, majd a __Szerkesztés__ lehetőséget. A __Mintavételezési stratégia__ esetében válassza a **Teljes fájl** lehetőséget, majd kattintson az **Alkalmaz** gombra.

   ![Új mintavételezési stratégia hozzáadása](media/tutorial-bikeshare-dataprep/weatherdatasamplingfullfile.png)

   A __Teljes fájl__ stratégia használatához válassza ki a __Teljes fájl__ bejegyzést, majd a __Beállítás aktívként__ lehetőséget. A __Teljes fájl__ lehetőség mellett megjelenik egy csillag, amely jelzi, hogy ez az aktív stratégia.

   ![A Teljes fájl beállítva aktív stratégiának](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   A folytatáshoz kattintson a **Tovább** gombra.

6. **Elérési út oszlop**: Az __Elérési út oszlop__ szakaszban megadhatja a fájl teljes elérési útját az importált adatok egyik oszlopaként. Válassza az __Elérési út oszlop kihagyása__ lehetőséget.

   > [!TIP]
   > Az elérési út oszlopként való hozzáadása akkor hasznos, ha egy olyan mappát importál, amelyben számos fájl található eltérő fájlnevekkel. Emellett akkor is hasznos, ha a fájlnevek olyan információkat tartalmaznak, amelyeket később szeretne kinyerni.

   ![Az Elérési út oszlop kihagyásra állítva](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Befejezés**: Az adatforrás létrehozásának befejezéséhez válassza a **Befejezés** lehetőséget.

    Ekkor megnyílik egy __BostonWeather__ nevű új adatforráslap. Az adatokból vett minta rácsnézetben jelenik meg. A minta a korábban meghatározott aktív mintavételezési séma alapján jön létre.

    A képernyő jobb oldalán található **Lépések** panel az adatforrás létrehozása során elvégzett egyes műveleteket jeleníti meg.

   ![Az adatforrás, a minta és a lépések megjelenítése](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Az adatforrás mérőszámainak megtekintése

Kattintson a lap rácsnézetének bal felső sarkában található __Metrikák__ gombra. Ez a nézet jeleníti meg a mintavételezett adatok elosztását és egyéb összesített statisztikáit.

![Metrikák megjelenítése](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> A statisztikák láthatóságának konfigurálásához használja a **Metrikák kiválasztása** legördülő listát. A rácsnézet módosításához jelölje ki a metrikákat, vagy törölje azok jelölését.

Az __Adatnézethez__ való visszatéréshez kattintson a lap bal felső sarkában található __Adat__ gombra.

## <a name="add-a-data-source-to-the-data-preparation-package"></a>Adatforrás hozzáadása az adat-előkészítési csomaghoz

1. Az adatok előkészítésének megkezdéséhez kattintson az __Előkészítés__ gombra. 

2. Amikor a rendszer arra kéri, írja be az adat-előkészítési csomag nevét, például a **BikeShare Data Prep** nevet. 

3. A folytatáshoz kattintson az __OK__ gombra.

   ![Előkészítés párbeszédpanel](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Ekkor megjelenik egy **BikeShare Data Prep** nevű új csomag az __Adat__ fül __Adatok előkészítése__ szakaszában. 

   A csomag megjelenítéséhez válassza ki a bejegyzést. 

5. Kattintson a **>>** gombra az __Adatfolyamok__ kibontásához és a csomagban található adatfolyamok megjelenítéséhez. Ebben a példában a __BostonWeather__ az egyetlen adatfolyam.

   > [!IMPORTANT]
   > Egy csomag több adatfolyamot is tartalmazhat.

   ![Adatfolyamok a csomagban](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Adatok szűrése érték alapján
1. Az adatok szűréséhez kattintson a jobb gombbal egy adott értéket tartalmazó cellára, majd kattintson a __Szűrő__ lehetőségre. Ezután válassza ki a szűrő típusát.

2. Ehhez az oktatóanyaghoz válasszon egy olyan cellát, amely az `FM-15` értéket tartalmazza. Ezután állítsa a szűrőt **egyenlő** beállításra.  Az adatok közül most csak azok a sorok jelennek meg, amelyekben a __REPORTTYPE__ értéke `FM-15`.

   ![Szűrés párbeszédpanel](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > Az FM-15 egy METAR időjárásjelentés-típus. Az FM-15 jelentések a tapasztalati megfigyelések alapján a legteljesebb körű jelentések kevés hiányzó adattal.

## <a name="remove-a-column"></a>Oszlop eltávolítása

A __REPORTTYPE__ oszlopra már nincs szüksége. Kattintson a jobb gombbal az oszlop fejlécére, és válassza az **Oszlop eltávolítása** lehetőséget.

   ![Oszlop eltávolítása lehetőség](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Adattípusok módosítása és a hibák eltávolítása
1. A Ctrl (Mac gépeken a Command ⌘) billentyűt nyomva tartva egyszerre több oszlopot is kiválaszthat. Ezzel a módszerrel jelölje ki a következő oszlopfejléceket:

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. Kattintson a jobb gombbal a kiválasztott oszlopfejlécek egyikére, majd válassza a **Mezőtípus átalakítása numerikus típusúvá** lehetőséget. Ez a lehetőség numerikusra konvertálja az oszlopok adattípusát.

   ![Több oszlop numerikus típusúvá konvertálása](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Szűrje ki a hibaértékeket. Egyes oszlopok esetén problémák merültek fel az adattípus konvertálásakor. Ezt a problémát az oszlop __Adatminőséget jelző sávjában__ megjelenő piros szín jelzi.

   A hibákat tartalmazó sorok eltávolításához kattintson a jobb gombbal az **HOURLYDRYBULBTEMPF** oszlopfejlécre. Kattintson az **Oszlop szűrése** lehetőségre. Használja a **Cél** lehetőségnél az alapértelmezett **Sorok megtartása** értéket. A **Feltételek** legördülő listában válassza a **nem hiba** lehetőséget. Kattintson az **OK** gombra a szűrő alkalmazásához.

   ![Hibaértékek szűrése](media/tutorial-bikeshare-dataprep/filtererrorvalues.png)

4. A többi oszlopban található hibás sorok kiküszöböléséhez ismételje meg ezt a szűrési folyamatot az **HOURLYRelativeHumidity** és az **HOURLYWindSpeed** oszlopok esetén.

## <a name="use-by-example-transformations"></a>Példa alapján végzett átalakítások használata

Ha az adatokat egy kétórás időszakokra vonatkozó előrejelzésben szeretné használni, ki kell számítania az átlagos időjárást két órás időszakokra. Ehhez a következő műveletek szükségesek:

* Ossza fel a **DATE** oszlopot külön **Date** és **Time** oszlopra. A részletes lépések a következő szakaszban olvashatók.

* Származtasson egy **Hour_Range** oszlopot a **Time** oszlopból. A részletes lépések a következő szakaszban olvashatók.

* Származtasson egy **Date\_Hour\_Range** oszlopot a **DATE** és az **Hour_Range** oszlopokból. A részletes lépések a következő szakaszban olvashatók.

### <a name="split-column-by-example"></a>Oszlopok felosztása példa alapján

1. Ossza fel a **DATE** oszlopot külön **Date** és **Time** oszlopra. Kattintson a jobb gombbal a **DATE** oszlop fejlécére, majd válassza az **Oszlopok felosztása példa alapján** lehetőséget.

   ![Oszlopok felosztása példa alapján bejegyzés](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. A Machine Learning Workbench automatikusan meghatároz egy észszerű elválasztó karaktert, majd két oszlopot hoz létre az adatok dátum- és időértékekre való felosztásával. 

3. Kattintson az __OK__ gombra a felosztási művelet eredményeinek elfogadásához.

   ![Felosztott DATE_1 és DATE_2 oszlopok](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Oszlopok származtatása példa alapján

1. Kétórás időtartam származtatásához kattintson a jobb gombbal a __DATE\_2__ oszlop fejlécére, és válassza az **Oszlopok származtatása példa alapján** lehetőséget.

   ![Oszlopok származtatása példa alapján bejegyzés](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Ekkor egy új üres oszlop jön létre nullértékekkel.

2. Válassza az új oszlop első üres celláját. Adjon meg egy példát a kívánt időtartományra. Ehhez írja be a **12AM-2AM** értéket az új oszlopba, majd nyomja le az Enter billentyűt.

   ![A 12AM-2AM értéket tartalmazó új oszlop](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > A Machine Learning Workbench a megadott példák alapján szintetizál egy programot, és alkalmazza azt a többi sorra is. A rendszer a megadott példa alapján automatikusan kitölti az összes többi sort. A Workbench emellett elemzi az adatokat, és megkísérli a szélsőséges esetek azonosítását. 

   > [!IMPORTANT]
   > A szélsőséges esetek azonosítása a Workbench jelenlegi verziójában nem feltétlenül működik Mac számítógépeken. Mac számítógép esetén ugorja át a 3. és 4. lépéseket. Ehelyett kattintson az __OK__ gombra, miután az összes sor fel lett töltve származtatott értékekkel.
   
3. A rács fölötti **Adatok elemzése** szöveg azt jelzi, hogy a Workbench a szélsőséges esetek észlelésén dolgozik. Ha ezzel elkészült, az állapot a következők egyikére változik: **Következő javasolt sor felülvizsgálata** vagy **Nincsenek javaslatok**. Ebben a példában az állapot a **Következő javasolt sor felülvizsgálata**.

4. A javasolt változások felülvizsgálatához válassza a **Következő javasolt sor felülvizsgálata** lehetőséget. Ekkor a felülvizsgálandó és szükség esetén javítandó cella kiemelve jelenik meg.

   ![Következő javasolt sor felülvizsgálata](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Kattintson az __OK__ gombra az átalakítás elfogadásához.
 
5. Ezután a rendszer visszalépteti a __BostonWeather__ adatainak rácsnézetéhez. A rács most az előzőleg hozzáadott három oszlopot tartalmazza.

   ![A hozzáadott sorokat tartalmazó rácsnézet](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   > A rendszer az elvégzett módosításokat megőrzi a **Lépések** panelen. Keresse meg az Ön által létrehozott lépést a **Lépések** panelen, kattintson a lefelé mutató nyílra és válassza a **Szerkesztés** lehetőséget. Ekkor megjelenik az **Oszlopok származtatása példa alapján** speciális ablak. A rendszer minden példát itt őriz meg. Emellett manuálisan is hozzáadhat példákat, ha duplán kattint a következő rács egyik sorára. Ha a módosítások alkalmazása nélkül szeretne visszatérni a fő rácshoz, kattintson a **Mégse** gombra. Ezt a nézetet úgy is elérheti, ha egy **Oszlopok származtatása példa alapján** átalakítás során a **Speciális mód** lehetőséget választja.

6. Az oszlop átnevezéséhez kattintson duplán az oszlopfejlécre, és írja be az **Hour Range** kifejezést. Nyomja le az Enter billentyűt a módosítás mentéséhez.

   ![Az oszlop átnevezése](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. A dátum- és óratartomány származtatásához válassza ki egyszerre a **Date\_1** és az **Hour Range** oszlopot, kattintson a jobb gombbal, majd válassza az **Oszlopok származtatása példa alapján** lehetőséget.

   ![Oszlopok származtatása példa alapján](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Írja be a **Jan 01, 2015 12AM-2AM** értéket példaként az első sorhoz, és nyomja le az Enter billentyűt.

   A Workbench a megadott példa alapján meghatározza az átalakítást. Ebben a példában eredményként a dátumformátum megváltozik, és össze lesz fűzve a kétórás időtartammal.

   ![A Jan 01, 2015 12AM-2AM példa](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > Mac számítógépen a 8. lépés helyett a következő lépést hajtsa végre:
   >
   > * Lépjen az első olyan cellára, amely a **Feb 01, 2015 12AM-2AM** értéket tartalmazza. Ez nagy valószínűséggel a 15. sor. Javítsa ki az értéket **Jan 02, 2015 12AM-2AM** értékre, majd nyomja le az Enter billentyűt. 
   

8. Várjon, amíg az állapot megváltozik **Adatok elemzése** értékről **Következő javasolt sor felülvizsgálata** értékre. Ez a módosítás néhány másodpercig is eltarthat. Kattintson az állapothivatkozásra a javasolt sorhoz való ugráshoz. 

   ![A felülvizsgálatra javasolt sor](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   A sor nullértékkel rendelkezik, mert a forrásdátum értéke lehet nn/hh/éééé vagy hh/nn/éééé is. Írja be a helyes értéket (**Jan 13, 2015 2AM-4AM**), majd nyomja le az Enter billentyűt. A Workbench a két példa használatával javítja a fennmaradó sorok származtatását.

   ![A megfelelően formázott adatok](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Kattintson az **OK** gombra az átalakítás elfogadásához.

   ![Az elkészült átalakítási rács](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Az **Oszlopok származtatása példa alapján** művelet **Speciális módjának** használatához ehhez a lépéshez. válassza a lefelé mutató nyilat a **Lépések** panelen. Az adatrácson jelölőnégyzetek találhatók a **DATE\_1** és az **Hour Range** oszlopok mellett. Törölje a jelölést az **Hour Range** oszlop melletti jelölőnégyzetből, és figyelje meg, hogyan változik a kimenet. Az **Hour Range** oszlop mint bemenet hiányában a rendszer állandóként kezeli a **12AM-2AM** értéket, és azt fűzi hozzá a származtatott értékekhez. Ha a módosítások alkalmazása nélkül szeretne visszatérni a fő rácshoz, kattintson a **Mégse** gombra.
   ![Speciális mód](media/tutorial-bikeshare-dataprep/derivedcolumnadvancededitdeselectcolumn.png)

10. Az oszlop átnevezéséhez kattintson duplán a fejlécre. Módosítsa a nevet **Date Hour Range** értékűre, majd nyomja le az Enter billentyűt.

11. Válassza ki együtt a **DATE**, a **DATE\_1**, a **DATE\_2** és az **Hour Range** oszlopot. Kattintson a jobb gombbal, majd válassza az **Oszlop eltávolítása** lehetőséget.

## <a name="summarize-data-mean"></a>Adatok összegzése (középérték)

A következő lépés az időjárás összegzése az óratartomány szerint csoportosított értékek középértékének kiszámítása alapján.

1. Válassza ki a **Date Hour Range** oszlopot, majd az **Átalakítások** menüben kattintson az **Összegzés** lehetőségre.

    ![Átalakítások menü](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Az adatok összegzéséhez húzza az oszlopokat az oldal alján található rácsról a fenti jobb és bal oldali panelre. A bal oldali panelen a **Húzzon ide oszlopokat az adatok csoportosításához** szöveg látható. A jobb oldali panelen a **Húzzon ide oszlopokat az adatok összegzéséhez** szöveg látható. 

    a. Húzza a **Date Hour Range** oszlopot a lenti rácsról a bal oldali panelre. Húzza az **HOURLYDRYBULBTEMPF**, az **HOURLYRelativeHumidity** és az **HOURLYWindSpeed** oszlopot a jobb oldali panelre. 

    b. A jobb oldali panelen válassza a **Középérték** lehetőséget az **Összesítés** módszereként minden oszlop esetében. Az összegzés befejezéséhez kattintson az **OK** gombra.

    ![Összegzett adatok képernyő](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-by-using-script"></a>Adatfolyam átalakítása szkript használatával

Ha a numerikus oszlopok adatait 0–1 tartományúra módosítja, azzal felgyorsíthatja egyes modellek átszerveződését. Jelenleg nincs ennek a feladatnak az általános elvégzésére szolgáló beépített átalakítás. A művelet elvégzéséhez használjon Python-szkriptet.

1. Az **Átalakítás** menüben kattintson az **Adatfolyam átalakítása (Szkript)** lehetőségre.

2. A megjelenő szövegmezőbe írja be a következő kódot. Ha a megadott oszlopneveket használta, a kódnak változtatás nélkül is működnie kell. Egy egyszerű min-max normalizálási logikát kell írnia a Pythonban.

    > [!WARNING]
    > A szkript az oktatóanyag korábbi részeiben használt oszlopnevek megadását várja. Ha ettől eltérő oszlopneveket adott meg, módosítania kell őket a szkriptben.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > A Python-szkriptnek a következő eredményt kell visszaadnia: `df`. Ez az érték a rács feltöltésére szolgál.
    
   ![Adatfolyam átalakítása (szkript) párbeszédpanel](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. A szkript használatához kattintson az __OK__ gombra. A rács numerikus oszlopai most már a 0–1 tartományban lévő értékeket tartalmaznak.

    ![0 és 1 közötti értékeket tartalmazó rács](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Elkészült az időjárási adatok előkészítésével. A következő lépésben előkészítjük az útadatokat.

## <a name="load-trip-data"></a>Útadatok betöltése

1. Importálja a `201701-hubway-tripdata.csv` fájlt az [Új adatforrás létrehozása](#newdatasource) szakaszban szereplő lépések segítségével. Az importálási folyamat során használja a következő beállításokat:

    * __Fájl kiválasztása__: Válassza az **Azure Blob** lehetőséget, amikor tallózással kiválasztja a fájlt.

    * __Mintavételezési séma__: Válassza a **Teljes fájl** mintavételezési sémát, és állítsa be a mintát aktívként.

    * __Adattípus__: fogadja el az alapértelmezett beállításokat.

2. Az adatok importálása után kattintson az __Előkészítés__ gombra az adatok előkészítésének megkezdéséhez. Válassza ki a meglévő **BikeShare Data Prep.dprep** csomagot, majd kattintson az __OK__ gombra.

    Ez a folyamat egy **adatfolyamot** ad a meglévő **adat-előkészítési** fájlhoz, nem hoz létre újat.

    ![A meglévő csomag kiválasztása](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Miután a rendszer betölti a rácsot, bontsa ki az __ADATFOLYAMOK__ lehetőséget. Most már két adatfolyamunk van: a **BostonWeather** és a **201701-hubway-tripdata**. Válassza ki a **201701-hubway-tripdata** bejegyzést.

    ![201701-hubway-tripdata bejegyzés](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-the-map-inspector"></a>A Térkép vizsgáló használata

Adatok előkészítésekor a sztring, szám és földrajzi adatokhoz vizsgálónak nevezett vizualizáció érhetők el. Ezek segítenek jobban megérteni az adatokat, és azonosítani a kiugró értékeket. Kövesse a következő lépéseket a Térkép vizsgáló használatához.

1. Válassza ki együtt a **start station latitude** és a **start station longitude** oszlopot. Kattintson a jobb gombbal az egyik oszlopra, majd válassza a **Térkép** lehetőséget.

    > [!TIP]
    > Több elem kiválasztásához tartsa lenyomva a Ctrl (Mac gépeken a Command ⌘) billentyűt, és közben jelölje ki az egyes oszlopok fejlécét.

    ![Térkép-vizualizáció](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. A térkép vizualizáció teljes méretűvé tételéhez kattintson a **Teljes méret** ikonra. A térképet az ablak méretéhez igazíthatja a vizualizáció bal felső részén található **E** ikon segítségével.

    ![Teljes méretű kép](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. A **Kis méret** gombra kattintva térjen vissza a rácsnézethez.

## <a name="use-the-column-statistics-inspector"></a>Az Oszlopstatisztika vizsgáló használata

Az Oszlopstatisztika vizsgáló használatához kattintson a jobb gombbal a __tripduration__ oszlopra, és válassza az __Oszlopstatisztika__ lehetőséget.

![Az Oszlopstatisztika bejegyzés](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Ez a folyamat egy __tripduration Statistics__ nevű új vizualizációt ad hozzá a __VIZSGÁLÓK__ panelhez.

![A tripduration Statistics vizsgáló](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> Az utak időtartamának maximális értéke 961 814 perc, amely nagyjából két évnek felel meg. Úgy tűnik, hogy az adatkészlet tartalmaz néhány kiugró értéket.

## <a name="use-the-histogram-inspector"></a>A hisztogram vizsgáló használata

A kiugró értékek azonosításához kattintson a jobb gombbal a __tripduration__ oszlopra, majd válassza a __Hisztogram__ lehetőséget.

![A Hisztogram vizsgáló](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

A hisztogram nem hasznos, mert a kiugró értékek elferdítik a diagramot.

## <a name="add-a-column-by-using-script"></a>Oszlop hozzáadása szkript használatával

1. Kattintson a jobb gombbal a **tripduration** oszlopra, és válassza az **Oszlop hozzáadása (szkript)** lehetőséget.

    ![Oszlop hozzáadása (szkript) menü](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. Az __Oszlop hozzáadása (szkript)__ párbeszédablakban a következő értékeket használja:

    * __Új oszlop neve__: logtripduration

    * __Új oszlop beszúrása a következő után__: tripduration

    * __Új oszlop kódja__: `math.log(row.tripduration)`

    * __Kódblokk típusa__: Kifejezés

   ![Oszlop hozzáadása (szkript) párbeszédpanel](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Kattintson az __OK__ gombra a **logtripduration** oszlop hozzáadásához.

4. Kattintson a jobb gombbal az oszlopra, és válassza a **Hisztogram** lehetőséget.

    ![A logtripduration oszlop hisztogramja](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

    A hisztogram megjelenése egy rendellenes végű normál eloszláshoz hasonlít.

## <a name="use-an-advanced-filter"></a>Speciális szűrő használata

Ha szűrőt alkalmaz az adatokra, a vizsgálók frissülnek az új elosztással. 

1. Kattintson a jobb gombbal a **logtripduration** oszlopra, majd válassza az **Oszlop szűrése** lehetőséget. 

2. A __Szerkesztés__ párbeszédablakban használja a következő értékeket:

    * __Numerikus oszlop szűrése__: logtripduration

    * __Cél__: Sorok megtartása

    * __Mikor__: Ha az alábbi feltételek bármelyike igaz (logikai OR)

    * __Ha ez az oszlop__: kisebb, mint

    * __Az érték__: 9

    ![Szűrési lehetőségek](media/tutorial-bikeshare-dataprep/loftripfilter.png)

3. Kattintson az __OK__ gombra a szűrő alkalmazásához.

    ![A frissített hisztogramok a szűrő alkalmazása után](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>A hatás effektus

1. Állítsa teljes méretűre a **logtripduration** hisztogramot. Egy kék hisztogram átfedésben van egy szürke hisztogrammal. Ennek a megjelenítésnek a neve **hatás effektus**:

    * A szürke hisztogram a művelet (ebben az esetben a szűrési művelet) előtti eloszlást mutatja.

    * A kék hisztogram pedig a művelet utáni hisztogramot jelöli. 

   A hatás effektus azt mutatja meg, hogy egy adott műveletnek milyen hatása van az adatokra.

   ![Hatás effektus](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Figyelje meg, hogy a kék hisztogram rövidebbnek tűnik az előzőhöz képest. Ez a különbség az adatok az új tartományban való újragyűjtésének köszönhető.

2. A Hatás effektus eltávolításához kattintson a __Szerkesztés__ gombra, és törölje a __Hatás megjelenítése__ lehetőség jelölését.

    ![A hisztogram beállításai](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Kattintson az **OK** gombra a hatás effektus letiltásához. Ezután állítsa a hisztogramot kis méretűre.

### <a name="remove-columns"></a>Oszlopok eltávolítása

Az útadatok minden egyes sora egy kerékpárfelvételt jelöl. Ebben az oktatóanyagban csak a **starttime** és a **start station id** oszlopra lesz szükség. A többi oszlop eltávolításához jelölje ki együtt ezt a két oszlopot, kattintson a jobb gombbal a fejlécre, majd válassza az **Oszlop megtartása** lehetőséget. A többi oszlop el lesz távolítva.

![Oszlop megtartása lehetőség](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Adatok összegzése (darabszám)

A kerékpárigény két órás periódusra való összegzéséhez használjon származtatott oszlopokat.

1. Kattintson a jobb gombbal a **starttime** oszlopra, és válassza az **Oszlopok származtatása példa alapján** lehetőséget.

    ![Oszlopok származtatása példa alapján lehetőség](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Ebben a példában adja meg a **Jan 01, 2017 12AM-2AM** értéket az első sorhoz.

    > [!IMPORTANT]
    > Az oszlopok származtatásának előző példájában több lépésben származtatott egy oszlopot, amely a dátumot és az időszakot tartalmazta. Ebben a példában láthatja, hogy ez a művelet egyetlen lépésben is elvégezhető a végső kimenet példájának megadásával.

    > [!NOTE]
    > Példát bármely sorhoz megadhat. Ebben a példában a **Jan 01, 2017 12AM-2AM** érték az adatok első sorára érvényes.

    ![Példaadatok](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > Mac számítógépen a 3. lépés helyett a következő lépést hajtsa végre:
   >
   > * Lépjen az első olyan cellára, amely a **Jan 01, 2017 1AM-2AM** értéket tartalmazza. Ez nagy valószínűséggel a 14. sor. Javítsa ki az értéket **Jan 01, 2017 12AM-2AM** értékre, majd nyomja le az Enter billentyűt. 

3. Várjon, amíg az alkalmazás kiszámítja a összes sorhoz tartozó értékeket. Ez a folyamat néhány másodpercig is eltarthat. Miután az elemzés befejeződött, kattintson a __Következő javasolt sor felülvizsgálata__ hivatkozásra az adatok felülvizsgálatához.

   ![Az elkészült elemzés felülvizsgálati hivatkozással](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Ellenőrizze, hogy a kiszámított értékek helyesek-e. Ha nem, frissítse az értéket a várt értékkel, és nyomja le az Enter billentyűt. Ezután várjon, amíg az elemzés befejeződik. Folytassa a **Következő javasolt sor felülvizsgálata** folyamatot, amíg meg nem jelenik a **Nincsenek javaslatok** állapot. A **Nincsenek javaslatok** azt jelenti, hogy az alkalmazás megvizsgálta a szélsőséges eseteket, és megfelelőnek találja a szintetizált programot. Ajánlott eljárás az átalakított adatok vizuális ellenőrzése az átalakítás elfogadása előtt. 

4. Kattintson az **OK** gombra az átalakítás elfogadásához. Nevezze át az újonnan létrehozott oszlopot a következőre: **Date Hour Range**.

    ![Átnevezett oszlop](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Kattintson a jobb gombbal a **starttime** oszlop fejlécére, majd válassza az **Oszlop eltávolítása** lehetőséget.

6. Az adatok összegzéséhez válassza az __Átalakítás__ menü __Összegzés__ lehetőségét. Az átalakítás létrehozásához kövesse a következő lépéseket:

    * Húzza a __Date Hour Range__ és a __start station id__ oszlopot a bal oldali **Csoportosítási szempont** panelre.

    * Húzza a __start station id__ oszlopot a jobb oldali **Adatok összegzése** panelre.

   ![Összegzési lehetőségek](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Kattintson az **OK** gombra az összegzés eredményének elfogadásához.

## <a name="join-dataflows"></a>Adatfolyamok összekapcsolása

Az időjárási adatok és az útadatok összekapcsolásához kövesse az alábbi lépéseket:

1. Az __Átalakítások__ menüben válassza az __Összekapcsolás__ lehetőséget.

2. __Táblák__: **Bal** oldali adatfolyamként válassza a **BostonWeather** adatfolyamot, a **jobb** oldaliként pedig a **201701-hubway-tripdata** adatfolyamot. A folytatáshoz kattintson a **Tovább** gombra.

    ![A táblák kiválasztásai](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Kulcsoszlopok__: Válassza ki a **Date Hour Range** oszlopot mindkét táblázatban, majd kattintson a __Tovább__ lehetőségre.

    ![A kulcsoszlopok kiválasztásai](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Összekapcsolás típusa__: Válassza az __Egyező sorok__ lehetőséget az összekapcsolás típusaként, majd kattintson a __Befejezés__ lehetőségre.

    ![Az Egyező sorok összekapcsolási típus](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Ez a folyamat egy __Összekapcsolás eredménye__ nevű új adatfolyamot hoz létre.

## <a name="create-additional-features"></a>További funkciók létrehozása

1. A hét napját tartalmazó oszlop létrehozásához kattintson a jobb gombbal a **Date Hour Range** oszlopra, és válassza az **Oszlopok származtatása példa alapján** lehetőséget. Használja a __Sun__ értéket vasárnapi dátumhoz. Például: **Jan 01, 2017 12AM-2AM**. Nyomja le az Enter billentyűt, majd kattintson az **OK** gombra. Nevezze át az oszlopot a következőre: __Weekday__.

    ![Új oszlop létrehozása a hét napjához](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Ha az adott sorhoz tartozó időszakot tartalmazó oszlopot szeretne létrehozni, kattintson a jobb gombbal a **Date Hour Range** oszlopra, és válassza az **Oszlopok származtatása példa alapján** lehetőséget. Használja a **12AM-2AM** értéket a **Jan 01, 2017 12AM-2AM** értéket tartalmazó sorhoz. Nyomja le az Enter billentyűt, majd kattintson az **OK** gombra. Nevezze át az oszlopot a következőre: **Period**.

    ![A Period oszlop](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. A **Date Hour Range** és az **r_Date Hour Range** oszlop eltávolításához tartsa lenyomva a Ctrl (Mac gépen a Command ⌘) billentyűt, és közben jelölje ki az egyes oszlopfejléceket. Kattintson a jobb gombbal, majd válassza az **Oszlop eltávolítása** lehetőséget.

## <a name="read-data-from-python"></a>Adatok beolvasása a Pythonból

Az adat-előkészítési csomagot a Pythonból vagy a PySparkból is futtathatja, és az eredményt lekérheti **adatkeretként**.

Példa Python-szkript létrehozásához kattintson a jobb gombbal a __BikeShare Data Prep__ csomagra, és válassza az __Adathozzáférési kódfájl létrehozása__ lehetőséget. A rendszer létrehozza a példa Python-fájlt a **projektmappában**, és a Workbench egyik lapján is betölti. A következő Python-szkript egy példa a létrehozott kódra:

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

Ebben az oktatóanyagban a fájl neve `BikeShare Data Prep.py`. A fájlra az oktatóanyag későbbi részében lesz szükség.

## <a name="save-test-data-as-a-csv-file"></a>Tesztadatok mentése CSV-fájlként

Ha az **Összekapcsolás eredményei** adatfolyamot egy .CSV fájlba szeretné menteni, módosítsa a `BikeShare Data Prep.py` szkriptet. 

1. Nyissa meg a mappát a Visual Studio Code-ban való szerkesztéshez.

    ![Megnyitott projekt a Visual Studio Code-ban](media/tutorial-bikeshare-dataprep/openprojectinvscode.png)

2. Frissítse a Python-szkriptet a `BikeShare Data Prep.py` fájlban az alábbi kód használatával:

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/testata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

3. Cserélje le a `Your Azure Storage blob path` elemet a létrehozandó kimeneti fájl elérési útjára. Cserélje le a `blobfolder` és a `csvfiles` változókat is.

## <a name="create-an-hdinsight-run-configuration"></a>HDInsight futtatási konfiguráció létrehozása

1. A Machine Learning Workbench alkalmazásban nyissa meg a parancssori ablakot. Ehhez kattintson a **Fájl** menüre, majd a **Parancssor megnyitása** elemre. A parancsprompt a projektmappában nyílik meg, a `C:\Projects\BikeShare>` prompttal.

    ![A parancssor megnyitása](media/tutorial-bikeshare-dataprep/opencommandprompt.png)

   >[!IMPORTANT]
   >Az alábbi lépéseket a (a Workbench alkalmazásból megnyitott) parancssori ablakban kell elvégeznie.

2. Jelentkezzen be az Azure-ba a parancssor használatával. 

   A Workbench alkalmazás és a parancssori felület az Azure-erőforrások hitelesítésekor független hitelesítőadat-gyorsítótárakat használ. Ezt csak egyszer kell megtennie, amíg a gyorsítótárazott jogkivonat le nem jár. Az `az account list` parancs visszaadja a bejelentkezéséhez elérhető előfizetések listáját. Ha egynél több található, használja a kívánt előfizetés azonosítóértékét. Állítsa be az adott előfizetést alapértelmezett fiókként az `az account set -s` paranccsal, majd adja meg az előfizetés azonosítóértékét. Ezután erősítse meg a beállítást az account `show` paranccsal.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Hozza létre a HDInsight futtatási konfigurációt. Ehhez a fürtje nevére és az `sshuser` jelszavára lesz szüksége.

    ```azurecli
    az ml computetarget attach cluster --name hdinsight --address <yourclustername>.azurehdinsight.net --username sshuser --password <your password>
    az ml experiment prepare -c hdinsight
    ```
> [!NOTE]
> Üres projekt létrehozásakor az alapértelmezett futtatási konfigurációk a **local** és a **docker**. Ez a lépés létrehoz egy új futtatási-konfigurációt, amely a szkriptek futtatása esetén érhető el a Workbenchben. 

## <a name="run-in-an-hdinsight-cluster"></a>Futtatás HDInsight-fürtben

A szkript HDInsight-fürtben való futtatásához lépjen vissza a Machine Learning Workbench alkalmazásba.

1. Lépjen vissza a projekt kezdőlapjára a bal oldali **Kezdőlap** ikonra kattintva.

2. Válassza a **hdinsight** lehetőséget a legördülő listából a szkript HDInsight-fürtben való futtatásához.

3. Válassza a **Futtatás** lehetőséget. A rendszer ekkor egy feladat formájában elküldi a szkriptet. Ha a feladat __Befejezve__ állapotúra változik, miután a rendszer a fájlt a tárolón belül megadott helyre írja.

    ![HDInsight futtatási szkript](media/tutorial-bikeshare-dataprep/hdinsightrunscript.png)


## <a name="substitute-data-sources"></a>Adatforrások behelyettesítése

Az előző lépésekben a `201701-hubway-tripdata.csv` és a `BostonWeather.csv` adatforrást használta a tesztadatok előkészítéséhez. Ha a csomagot a többi útadatfájllal kívánja használni, kövesse az alábbi lépéseket:

1. Hozzon létre egy új adatforrást a korábban ismertetett lépésekkel, az alábbi eltérésekkel:

    * __Fájl kiválasztása__: Fájl kiválasztásakor válassza ki együtt a hat fennmaradó útadatokat tartalmazó .csv fájlt.

    ![Hat további fájl betöltése](media/tutorial-bikeshare-dataprep/browseazurestoragefortripdatafiles.png)

    > [!NOTE]
     > A __+5__ bejegyzés azt jelzi, hogy a felsorolton kívül öt további fájl található.

    * __Fájl részletei__: Állítsa a __Fejléc-előléptetési mód__ beállítást **Minden fájl azonos fejléccel rendelkezik** értékűre. Ez az érték azt jelzi, hogy a fájlok mindegyike ugyanazt a fejlécet tartalmazza.

    ![A fáj részleteinek kiválasztása](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Mentse az adatforrás nevét, mivel arra a későbbi lépések során is szükség lesz.

2. Válassza ki a mappa ikont a projekt fájljainak megtekintéséhez. Bontsa ki az __aml\_config__ könyvtárat, és válassza a `hdinsight.runconfig` fájlt.

    ![A hdinsight.runconfig helye](media/tutorial-bikeshare-dataprep/hdinsightsubstitutedatasources.png) 

3. Kattintson a **Szerkesztés** gombra a fájl Visual Studio Code-ban való megnyitásához.

4. Adja hozzá az alábbi sorokat a `hdinsight.runconfig` fájl végéhez, majd a fájl mentéséhez kattintson a lemez ikonra.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Ez a módosítás lecseréli az eredeti adatforrást arra, amely a hat útadatfájlt tartalmazza.

## <a name="save-training-data-as-a-csv-file"></a>Betanítási adatok mentése CSV-fájlként

1. Keresse meg a korábban szerkesztett `BikeShare Data Prep.py` Python-fájlt. Adjon meg egy másik fájlelérési utat a betanítási adatok mentéséhez.

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/traindata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

2. Használja a `traindata` nevű mappát a tanítási adatokat tartalmazó kimenethez.

3. Új feladat létrehozásához válassza a **Futtatás** lehetőséget. Győződjön meg arról, hogy a **hdinsight** van kiválasztva. A rendszer elküld egy feladatot az új konfigurációval. A feladat kimenetei a betanítási adatok. Ezek az adatok a korábban használt adat-előkészítési lépések használatával lesznek létrehozva. A feladat néhány percet vehet igénybe.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések
Ezzel elvégezte a kerékpármegosztással kapcsolatos adat-előkészítési oktatóanyagot. Ebben az oktatóanyagban a Machine Learning (előzetes verzió) segítségével a következőket sajátíthatta el:
> [!div class="checklist"]
> * Interaktív adat-előkészítés a Machine Learning adat-előkészítési eszközével.
> * Tesztadatkészlet importálása, átalakítása és létrehozása.
> * Adat-előkészítési csomag létrehozása.
> * Adat-előkészítési csomag futtatása a Python használatával.
> * Betanítási adatkészletet létrehozása az adat-előkészítési csomag újrafelhasználásával további bemeneti fájlokhoz.

További tudnivalók az adatok előkészítéséről:
> [!div class="nextstepaction"]
> [Adatok előkészítésének használati útmutatója](data-prep-user-guide.md)
