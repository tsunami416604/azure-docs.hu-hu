---
title: "Az oktatóanyag - speciális adatok előkészítése az Azure Machine Learning-munkaterület kerékpárt-megosztás"
description: "És az Azure Machine Learning-munkaterület használata az adatok végpontok közötti előkészítése oktatóanyag"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: article
ms.date: 09/21/2017
ms.openlocfilehash: e85515c29d8f626c7eb2bfb636dc6c18da78b5c5
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>Annak-megosztás oktatóanyag: adatok előkészítése az Azure Machine Learning-munkaterület speciális
Azure Machine Learning-szolgáltatások (előzetes verzió) egy integrált, végpontok közötti adattudomány, de a professional adatszakértőkön át a készítse elő az adatokat, kísérletek fejlesztése és központi telepítése a felhőbeli skálázással modellek speciális elemzési megoldás.

Az oktatóanyag segítségével Azure Machine Learning-szolgáltatások (előzetes verzió) megtudhatja, hogyan:
> [!div class="checklist"]
> * Készítse elő az adatokat az Azure Machine Learning adatok előkészítése eszközzel interaktív módon
> * Importálása, átalakítása és egy tesztelési adatkészletnél létrehozása
> * Adatok előkészítése csomag létrehozása
> * Futtassa az előkészítési adatcsomag pythonos környezetekben
> * Képzési dataset elő újból felhasználja a bemeneti fájlok további adatok előkészítése csomagja

> [!IMPORTANT]
> Ez az oktatóanyag csak előkészíti az adatokat, nem építhető fel az előrejelzési modell.
>
> Az előkészített adatok segítségével a saját előrejelzési modell betanításához. Például előfordulhat, hogy hozzon létre egy modell előre jelezni kerékpárt igény szerint 2 órás időszak alatt történjen.

## <a name="prerequisites"></a>Előfeltételek
* Az Azure Machine Learning-munkaterület helyileg telepíteni kell. További információért hajtsa végre a [telepítési gyors üzembe helyezés](quickstart-installation.md).
* Létrehoz egy új projektet a munkaterületet üzemeltető ismeretét.

## <a name="data-acquisition"></a>Adatok beszerzése
Ez az oktatóanyag használja a [Boston Hubway dataset](https://s3.amazonaws.com/hubway-data/index.html) és Boston időjárási adatok [NOAA](http://www.noaa.gov/).

1. Az adatfájlok letölthető a következő hivatkozások a helyi fejlesztési környezetet. 
   * [Boston időjárási adatok](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv). 
   * Hubway út adatok Hubway webhelyről.

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Minden egyes .zip fájl kibontása a letöltés után.

## <a name="learn-about-the-datasets"></a>További tudnivalók az adatkészletek
1. A __Boston időjárási__ fájl tartalmazza a következő időjárási kapcsolatos mezők, óránkénti alapon jelentett:
   * DÁTUM
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. A __Hubway__ rendszerezett adatokat fájlokba hónap és év. Például a nevű fájl `201501-hubway-tripdata.zip` a 2015. januári adatokat tartalmazó .csv fájlt tartalmaz. Az adatmező tartalmazza a következő mezők minden sor egy kerékpárt út képviselő:

   * Út időtartama (másodpercben)
   * Kezdő időpontja és dátuma
   * Állítsa le és időpontja
   * Indítsa el az állomás neve és azonosítója
   * Végpont állomás neve és azonosítója
   * Annak azonosítója
   * Felhasználó típusa (alkalmi = 24 órás vagy 72 óra fázis felhasználó; Tag = havi vagy éves)
   * Irányítószám (Ha a felhasználó tagja)
   * Nemét (tag önálló jelentett)

## <a name="create-a-new-project"></a>Új projekt létrehozása
1. Indítsa el a **Azure Machine Learning-munkaterület** a start menüből vagy az indító.

2. Hozzon létre egy új Azure Machine Learning-projektet.  Kattintson a  **+**  gombra a **projektek** lapon vagy **fájl** > **új**.
   - Használja a **üres projekt** sablont.
   - A projekt elnevezése **BikeShare**. 

## <a id="newdatasource"></a>Hozzon létre egy új adatforrást

1. Hozzon létre egy új adatforrást. Kattintson a **adatok** a bal oldali eszköztár (palack ikon) gombjára. Ez megjeleníti a **adatnézet**.

   ![Az adatok nézet lap képe](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Adjon hozzá egy adatforrás. Válassza ki a  **+**  ikonra, majd válassza ki **adatforrás hozzáadása**.

   ![Az adatforrás hozzáadása bejegyzés képe](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Időjárás-adatok hozzáadása

1. **Adattár**: válassza ki a tárolót, amely tartalmazza az adatokat. Mivel olyan fájlokat használnak, válassza **fájl / könyvtár**. Válassza ki **tovább** a folytatáshoz.

   ![Az (oka) t képe / könyvtár-bejegyzés](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Fájl kiválasztása**: időjárási adatokat. Keresse meg és jelölje ki a `BostonWeather.csv` korábban letöltött fájlban. Kattintson a **Tovább** gombra.

   ![A kiválasztott BostonWeater.csv fájl kijelölés képe](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **Fájl részletei**: Ellenőrizze a fájl sémát észlelt. Az Azure Machine Learning-munkaterület elemzi az adatokat a fájlban, és arra következtet használandó sémát.

   ![A fájladatok képe](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > A munkaterület bizonyos esetekben előfordulhat, hogy észleli a megfelelő sémát. Mindig ellenőrizze, hogy a paraméterek helyesek-e az adatkészlet. Az időjárási adatokat ellenőrizze, hogy a következő értékek megfelelőek-e:
   >
   > * __Fájltípus__: tagolt fájlt (csv, tsv, txt, stb.)
   > * __Elválasztó__: vesszővel [,]
   > * __Comment sor karakter__: nincs megadva érték.
   > * __Kihagyás sorok mód__: ne hagyja ki
   > * __A fájl kódolás__: utf-8
   > * __Fejlécek mód előléptetni__: fejlécek használata első fájlból

   Az adatok előzetes megjelenjen-e a következő oszlopokat:
   * **Elérési út**
   * **DÁTUM**
   * **REPORTTYPE**
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

   A folytatáshoz válasszon **következő**.

4. **Adattípusok**: tekintse át az adattípust, amelyet a rendszer automatikusan észleli. Az Azure Machine Learning-munkaterület elemzi az adatokat a fájlban, és arra következtet az adattípusok használata.

   Ezek az adatok módosítása a `DATA TYPE` az összes oszlopok `String`.

   > [!NOTE]
   > `String`Jelölje ki a munkaterületet üzemeltető képességeit az oktatóanyag későbbi részében szolgál. 

   ![Az adattípusok képe](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   A folytatáshoz válasszon __következő__. 

5. **A mintavételi**: egy mintavételi séma létrehozásához válassza a **+ új** gombra. Jelölje be az új __felső 10000__ hozzáadott, és adja sor __szerkesztése__. Állítsa be __minta stratégia__ való **teljes fájl**, majd válassza ki **alkalmaz**.

   ![Egy új mintavételi stratégia hozzáadása képe](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   Használatához a __teljes fájl__ stratégia, jelölje be a __teljes fájl__ bejegyzést, majd válassza ki __aktív beállítása__. Csillag jelenik meg a __teljes fájl__ annak jelzésére, hogy a rendszer az aktív stratégia.

   ![Az aktív stratégia teljes fájlt képe](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   A folytatáshoz válasszon **következő**.

6. **Elérési út oszlop**: A __elérési oszlop__ szakasz lehetővé teszi, hogy a fájl teljes elérési útja tartalmazza az importált adatok oszlopként. Válassza ki __nem tartalmaznak elérési oszlop__.

   > [!TIP]
   > Köztük oszlopként akkor hasznos, ha másik fájlnévvel sok fájlt egy tetszőleges mappába importál. Akkor célszerű is, ha a fájlnevek később kinyerni kívánt információkat tartalmaznak.

   ![Elérési út oszlophoz nem tartalmaznak képe](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Befejezés**: az adatforrás létrehozásának befejezéséhez válassza ki a **Befejezés** gombra.

    Egy új adatforrás lap nevű __BostonWeather__ nyílik meg. A minta az adatok táblázatos nézetben jelenik meg. A minta a korábban meghatározott aktív mintavételi rendszer alapul.

    Figyelje meg a **lépéseket** a képernyő jobb oldalán ablaktáblán megjelennek azok az egyes műveleteit, ez az adatforrás létrehozása közben.

   ![Az adatforrás, a minta és a lépéseket kép](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Nézet adatainak forrás metrikák

Válassza ki a __metrikák__ gombra az oldal tetején a lap rács nézet balra. Ez a nézet jeleníti meg, a terjesztési és egyéb összesített statisztikák a mintában szereplő adatok.

![A metrikák jelennek meg képe](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> A látható-e a statisztika konfigurálásához használja a **válassza ki a metrika** legördülő menüből. Ellenőrizze, és törölje a metrikák van a táblázatos nézet módosításához.

Vissza a __adatnézet__, jelölje be __adatok__ a lap jobb alsó.

## <a name="add-data-source-to-data-preparation-package"></a>Adatok előkészítése csomag adatforrás hozzáadása

1. Válassza ki __Prepare__ megkezdéséhez az adatok előkészítése. 

2. Amikor a rendszer kéri, adja meg az adatok előkészítése csomag nevét például `BikeShare Data Prep`. 

3. Válassza ki __OK__ folytatja.

   ![A prepare párbeszédpanel képe](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Nevű új csomag **BikeShare adatok előkészítő** jelenik meg az __adatok előkészítése__ szakasza a __adatok__ fülre. 

   A csomag megjelenítéséhez jelölje be ezt a bejegyzést. 

5. Válassza ki a  **>>**  gomb megjelenítéséhez bontsa ki a __Dataflows__ a csomagban található. Ebben a példában __BostonWeather__ van az egyetlen adatfolyamban.

   > [!IMPORTANT]
   > A csomag több Dataflows tartalmazhat.

   ![A csomagban található a dataflows képe](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Szűrje az adatokat értékkel
1. Az adatok szűrése, kattintson a jobb gombbal egy cella a megadott érték, és válassza ki __szűrő__, és ezután a szűrő típusa.

2. A jelen oktatóanyag esetében válassza ki az értéket tartalmazó cellára `FM-15` és utána állítsa be a szűrő egy szűrő, az **egyenlő**.  Most már csak a sort adja vissza az adatok szűrt ahol a __REPORTTYPE__ van `FM-15`.

   ![A Szűrés párbeszédpanel képe](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM – 15 olyan az időjárási Terminálszolgáltatások repülési rutin időjárási jelentés (METAR). Az FM – 15 jelentések empirically figyelhetők kell lennie a legtöbb befejeződött, a hiányzó adatot.

## <a name="remove-a-column"></a>Egy oszlop eltávolítása

Már nincs szüksége a __REPORTTYPE__ oszlop. Kattintson a jobb gombbal az oszlop fejlécére, és válassza ki **oszlop eltávolítása**.

   ![Az Eltávolítás oszlop lehetőség képe](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Módosítsa az adattípust, és távolítsa el a hibákat
1. Nyomja le __Ctrl (parancsának ⌘ Mac)__ míg oszlopfejlécek kiválasztásával lehetővé teszi több oszlop kijelöléséhez. Ennek segítségével válassza ki a következő oszlopfejlécek:
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. **Kattintson a jobb gombbal** egyik a kijelölt oszlop fejlécek, és válasszon **mezőtípus átalakítása numerikus**. Az oszlop adattípusát numerikus Ez alakítja.

   ![Több oszlop átalakítása numerikus](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. A hiba értékek szűrik. Egyes oszlopok írja be az átalakítás problémák adatokkal rendelkeznek. Ez a probléma a piros szín jelöli a __minőségi adatsáv__ az oszlop.

   A hibákat tartalmazó sorok eltávolításához kattintson a jobb gombbal a a **HOURLYDRYBULBTEMPF** oszlop fejlécére. Válassza ki **oszlop szűrése**. Használja az alapértelmezett **I szeretné, hogy** , **sorok megtartása**. Módosítsa a **feltételek** legördülő listán válassza ki a **nincs hiba**. Válassza ki **OK** a szűrőt.

4. Elkerülése érdekében a többi hiba sor a többi oszlop, ismételje meg a szűrő folyamatot a **HOURLYRelativeHumidity** és **HOURLYWindSpeed** oszlopok.

## <a name="use-by-example-transformations"></a>Példa átalakítások használatát

A két órás blokkok előrejelzéshez adatok használatához ki kell számítani két órás időszak átlagos időjárási feltételeit. Ehhez a következő műveletek használhatja:

* Vegyes a **dátum** különálló oszlop **dátum** és **idő** oszlopok. A részletes lépéseket a következő szakaszban talál.

* Származtatni egy **Hour_Range** oszlopát a **idő** oszlop. A következő részben részletes lépéseit.

* Származtatni egy **dátum\_óra\_tartomány** oszlopát a **dátum** és **Hour_Range** oszlopok. A következő részben részletes lépéseit.

### <a name="split-column-by-example"></a>Oszlopok felosztása példa alapján

1. Vegyes a **dátum** külön dátum és idő oszlopokba oszlop. Kattintson a jobb gombbal a **dátum** oszlop fejlécére, és válassza **osztott oszlop példa alapján**.

   ![Példabejegyzés a felosztás oszlop képe](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Az Azure Machine Learning-munkaterület automatikusan egy jelentéssel bíró elválasztó azonosítja, és két oszlop létrehozza az adatokat a dátum- és időértékek rendezze. 

3. Válassza ki __OK__ fogadására a felosztás a művelet eredménye.

   ![A felosztott oszlopok DATE_1 és DATE_2 képe](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Oszlopok származtatása példa alapján

1. Egy két órás tartomány kapcsolattípusokból, kattintson a jobb gombbal a __dátum\_2__ oszlop fejlécére, és válassza **Célosztályából oszlop példa alapján**.

   ![Példabejegyzés a származtatott oszlop képe](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Új üres oszlop null értékekkel kerül.

2. Kattintson az első üres cella oszlopot. Adjon meg egy példát, írja be a kívánt időtartomány `12AM-2AM` a új oszlopot, és nyomja meg.

   ![Az új oszlopot, de 12 - hajnali 2 óra értékkel képe](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Az Azure ML munkaterület synthesizes egy programot az Ön által megadott példák alapján, és ugyanez a program fennmaradó sorok alkalmazza. Az összes többi sort automatikusan fel van töltve, a megadott példa alapján. Munkaterület is elemzi az adatokat, és megpróbál peremhálózati esetek azonosításához. 
  
3. A szöveg **adatok elemzése** fent a rács azt jelzi, hogy munkaterület próbál biztonsági esetek észlelése. Ha befejezte, az állapota **felülvizsgálati következő javasolt sor** vagy **nincs javaslat**. Ebben a példában **felülvizsgálati következő javasolt sor** adja vissza.

4. A javasolt módosításokat, jelölje ki **felülvizsgálati következő javasolt sor**. A cella, át kell tekintenie, és javítsa ki a (ha szükséges) ki van jelölve, a képernyőjén.

   ![Felülvizsgálati sor képe](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Válassza ki __OK__ az átalakítás fogadásához.
 
5. A táblázatos nézet adatainak ismét __BostonWeather__. A rács a korábban hozzáadott három oszlopot tartalmaz.

   ![Hozzáadott sorokból rácsnézethez képe](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  Az összes végrehajtott módosítások megmaradjanak a a **lépéseket** ablaktáblán. Nyissa meg a létrehozott lépéssel a **lépéseket** ablaktáblán kattintson a lefelé mutató nyílra, majd kattintson **szerkesztése**. A speciális ablakának **Célosztályából oszlop példa alapján** jelenik meg. A példák a itt megmaradnak. Példák manuálisan is hozzáadhat az alábbi rácsban soron duplán kattintva. Válassza ki **Mégse** visszatérhet a fő rácsvonalak módosítások alkalmazása nélkül. Emellett ez a nézet kiválasztásával **Speciális üzemmód** végrehajtása közben egy **Célosztályából oszlop példa alapján** átalakító.

6. Kattintson duplán az oszlop átnevezése, az oszlop fejlécére, és írja be **óra tartomány**. Nyomja le az **Enter** menteni a módosítást.

   ![Oszlop átnevezése](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. A dátum- és óra tartomány, többszörös kiválasztási kapcsolattípusokból a **dátum\_1** és **óra tartomány** oszlopok, kattintson a jobb gombbal, és válassza **származtatott oszlop példa alapján**.

   ![Oszlop származtatás – példa](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Típus `Jan 01, 2015 12AM-2AM` szemben az első sort, majd kattintson a példaként **Enter**.

   A munkaterület az átalakítás megadta a példa alapján határozza meg. Ebben a példában az eredménye, hogy a dátumformátum megváltozott, és a két órát vesz összefűzéséhez.

   ![A példa képe "2015. január 01. de 12 - hajnali 2 óra](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)


8. Várjon, amíg az állapot nem **adatok elemzése** való **felülvizsgálati következő javasolt sor**. Ez eltarthat néhány másodpercig. Kattintson a lehetőségre, és navigáljon a javasolt sor állapota hivatkozásra. 

   ![Tekintse át a javasolt sor képe](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   A sor null értéket tartalmaz, mert a forrás dátumérték lehet nn/hh/éééé vagy a hh/nn/éééé. Írja be a megfelelő értéket a `Jan 13, 2015 2AM-4AM` nyomja le az ENTER **Enter**. A munkaterület két példa a Származtatás a fennmaradó sorok javítására használja.

   ![Megfelelően formázott adatok képe](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Válassza ki **OK** az átalakítás fogadásához.

   ![A befejezett átalakítása rács képe](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Használja a speciális módot **származtatott oszlop példa alapján** ebben a lépésben a lefelé mutató nyílra kattintva a **lépéseket** ablaktáblán. A program az adatrács, az oszlopnevek melletti jelölőnégyzeteket nincsenek **dátum\_1** és **óra tartomány** oszlopok. A törölje a jelet a jelölőnégyzetből a **óra tartomány** oszlopban tekintheti meg, hogyan változik a kimenetet. Hiányában a **óra tartomány** oszlop bemenetként, **12 óra - hajnali 2 óra** állandó a rendszer, és a származtatott értékek a rendszer hozzáfűzi. Válassza ki **Mégse** visszatérhet a fő rácsvonalak a módosítások alkalmazása nélkül.

10. Az oszlop átnevezése, kattintson duplán a fejlécet. Módosítsa a nevét, hogy **órában dátumtartomány** , és nyomja le az **Enter**.

11. Jelölje ki a **dátum**, **dátum\_1**, **dátum\_2**, és **óra tartomány** oszlopok. Kattintson a jobb gombbal, majd válassza ki **eltávolítása oszlop**.

## <a name="summarize-data-mean"></a>Adatösszegzés (közepes)

A következő lépés, hogy összesítse a időjárási feltételek alapul véve a értékek óra tartomány szerint csoportosítva középértékét.

1. Válassza ki a **órában dátumtartomány** oszlop, és válassza **Summarize** a a **átalakítja** menü.

    ![Átalakítja az menü](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Összefoglalva az adatokat, akkor húzza oszlopok az oldal alján a rácsban a bal és jobb oldali ablaktábla tetején. A bal oldali panelen a szöveget tartalmaz **húzzon ide oszlopokat replikációscsoport-adatok**. A jobb oldali ablaktáblában a szöveget tartalmaz **húzzon ide oszlopokat adatainak összefoglalója**. 

    Húzza a **órában dátumtartomány** bal oldali alján a rács oszlopát. A csomóponthúzási **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity**, és **HOURLYWindSpeed** a jobb oldali ablaktáblába. 

    A jobb oldali ablaktáblában jelölje ki a **jelenti** , a **összesített** mérték minden egyes oszlophoz. Kattintson a **OK** az összegzési befejezéséhez.

   ![Összesített adatok képernyő](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>Átalakítás adatfolyam-parancsfájl használatával

A numerikus oszlopokban szereplő adatok módosítása a 0-1 számos lehetővé teszi, hogy egyes modellek, amelyekkel gyorsan. Jelenleg nincs a átalakítása általános ehhez beépített átalakítás nélkül, de egy Python-parancsfájl segítségével ehhez a művelethez.

1. Az a **átalakítási** menüjében válassza **adatfolyam átalakítása**.

2. Írja be a következő kódot a szövegmezőben, amely akkor jelenik meg. Ha a használt az oszlopok neveit, a kód módosítás nélkül kell működnie. Egy egyszerű minimális-maximális normalizálási logika ír a Python.

    > [!WARNING]
    > A parancsfájl az oszlopok neveit, ebben az oktatóanyagban korábban használt vár. Ha másik oszlopnevek, módosítania kell a parancsfájlban szereplő neve.

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
    > A Python-parancsfájl kell visszaadnia `df` végén. Ez az érték a rács feltöltéséhez használható.
    
    ![Átalakítási adatok folyamata parancsfájl párbeszédpanel](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Válassza ki __OK__ a parancsfájl használatát. A numerikus oszlopai mostantól tartalmazza a tartományban, 0-1 érték.

    ![0 és 1 közötti értékeket tartalmazó rács](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Befejezte az időjárási adatok előkészítése. Ezt követően készítse elő a út adatokat.

## <a name="load-trip-data"></a>Út adatok betöltése

1. Importálhatja a `201701-hubway-tripdata.csv` fájlt, az alábbi témakörben található lépésekkel a [hozzon létre egy új adatforrást](#newdatasource) szakasz. Az alábbi beállításokat használják az importálási folyamat során:

    * __Mintavételi rendszer__: **teljes fájl** mintavételi séma, ellenőrizze a minta aktív, és 
    * __Adattípus__: fogadja el az alapértelmezett beállításokat.

2. Az adatok importálása után válassza ki a __Prepare__ gombra kattintva megkezdheti az adatok előkészítése. Válasszon a meglévő **BikeShare adatok Prep.dprep** csomagot, majd válassza ki __OK__.

    Ez a folyamat hozzáadja egy **Adatfolyamblokk** a meglévő **adatok előkészítése** fájl helyett egy új létrehozásával.

    ![A következő létező csomagba kiválasztásával képe](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Miután a rács be van töltve, bontsa ki a __DATAFLOWS__. Most már nincsenek két dataflows: **BostonWeather** és **201701-hubway-tripdata**. Válassza ki a **201701-hubway-tripdata** bejegyzés.

    ![A 201701-hubway-tripdata bejegyzés képe](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>Térkép inspector használata

Adatok előkészítése, a számos hasznos képi megjelenítést nevű **ellenőrök** karakterlánc, szám és földrajzi adatok, amelyek segítenek az adatok jobb megértése és a kiugró azonosítása. A térkép inspector használandó tegye a következőket:

1. Jelölje ki a **állomás szélesség start** és **állomás hosszúság start** oszlopok. Kattintson a jobb gombbal az oszlopok, és válassza ki **térkép**.

    > [!TIP]
    > Ahhoz, hogy a többszörös kijelöléssel, tartsa lenyomva a __Ctrl (parancsának ⌘ Mac)__ válassza ki a fejlécet minden egyes oszlophoz.

    ![A térkép képi megjelenítés képe](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. A térkép képi megjelenítés maximalizálása érdekében válassza ki a **teljes méret** ikonra. Az ablak a térképen megfelelően, válassza ki a **E** a képi megjelenítés legfelső bal oldalán látható ikonra.

    ![Teljes méretű kép](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Kattintson a **kis méret** gombra kattintva visszatérhet a táblázatos nézet.

## <a name="use-column-statistics-inspector"></a>Használja az oszlop statisztikai Inspector

Az oszlop statisztikai inspector használatához kattintson a jobb gombbal a a __tripduration__ oszlop, és válassza ki __oszlop statisztikai adatainak__.

![Oszlop statisztika bejegyzés](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Ez a folyamat hozzáadja az új képi megjelenítés című __tripduration statisztika__ a a __ellenőrök__ ablaktáblán.

![A tripduration statisztika inspector képe](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> A út időtartama maximális értéke **961,814 perc**, amely körülbelül két év van meghatározva. Úgy tűnik, hogy az adatkészlet egyes kiugró szerepelnek.

## <a name="use-histogram-inspector"></a>Hisztogram inspector használata

Sikertelen bejelentkezési kísérletet a kiugró azonosítására, kattintson a jobb gombbal a __tripduration__ oszlop, és válassza ki __hisztogram__.

![Hisztogram inspector](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

A hisztogram nincs esetén hasznos, a kiugró vannak döntés a diagramon.

## <a name="add-column-using-script"></a>Parancsfájl használata oszlop hozzáadása

1. Kattintson a jobb gombbal a **tripduration** oszlop, és válassza ki **oszlop hozzáadása (parancsfájl)**.

    ![A Hozzáadás oszlop (parancsfájl) menü](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. Az a __oszlop hozzáadása (parancsfájl)__ párbeszédpanelen a következő értékeket használja:

    * __Új oszlop neve__: logtripduration
    * __Helyezze be az új oszlop után__: tripduration
    * __Új oszlop kód__:`math.log(row.tripduration)`
    * __Kód blokktípus__: kifejezés

   ![Az oszlop (parancsfájl) hozzáadása párbeszédpanel](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Válassza ki __OK__ hozzáadása a **logtripduration** oszlop.

4. Kattintson a jobb gombbal az oszlop, és válassza ki a **hisztogram**.

    ![Hisztogram logtripduration oszlop](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  A hisztogram vizuálisan, úgy tűnik, például az olyan rendellenes utóhívás normális eloszlás.

## <a name="use-advanced-filter"></a>Speciális szűrő használata

A felügyelők szűrő használatával az adatok frissítése az új terjesztési. Kattintson a jobb gombbal a **logtripduration** oszlop, és válassza ki **Szűrőoszlopokat**. Az a __szerkesztése__ párbeszédpanelen a következő értékeket használja:

* __A szám oszlop szűrése__: logtripduration
* __Szeretném__: sorainak a megtartása
* __Amikor__: az alábbi feltételek bármelyike igaz (logikai vagy)
* __Ha ez az oszlop__: kisebb, mint
* __Az érték__: 9

![A szűrőbeállítások](media/tutorial-bikeshare-dataprep/loftripfilter.png)

Válassza ki __OK__ a szűrőt.

![Frissített hisztogram után a szűrő alkalmazása](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>A halo hatása

1. Maximalizálja a **logtripduration** hisztogram. A szürke hisztogram átfedett kék hisztogram van. A megjelenített neve a **Halo hatás**:

    * A **szürke hisztogram** (ebben az esetben, a szűrési művelet) a művelet előtt terjesztési jelöli.
    * A **kék hisztogram** a hisztogram jelenti. a művelet után. 

   A halo hatás megjeleníteni az adatokat egy művelet hatása a segítségével.

   ![A halo hatás képe](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Figyelje meg, hogy rövidebb megjelenik-e a kék hisztogram az előzőre képest. Ez az az oka, hogy automatikus ismételt bucketing az adatok az új tartományon.

2. A halo eltávolításához jelölje ki __szerkesztése__ és törölje a jelet __megjelenítése halo__.

    ![A hisztogram beállításai](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Válassza ki **OK** halo hatásának letiltani, és ezután minimalizálása érdekében a hisztogram.

### <a name="remove-columns"></a>Oszlopok eltávolítása

Minden egyes sorára út adatok kerékpárt felvételi esemény jelöli. Ebben az oktatóanyagban csak van szüksége a **starttime** és **állomás azonosítója start** oszlopok. Távolítsa el a többi oszlop szerint több kiválasztásával a két oszlop, kattintson a jobb gombbal az oszlopfejlécre, és válassza **oszlop megtartása**. Más oszlopokat is törlődnek.

![A tárolás során is garantálják column beállítás képe](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>(Darabszám) adatainak összefoglalója

Összefoglalva kerékpárt igény szerint a 2 órás időszakban, származtatott oszlopokat használja.

1. Kattintson a jobb gombbal a **starttime** oszlop, és válassza ki **származnia oszlop példa alapján**

    ![Példa beállítás által célosztályából képe](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. A például adjon meg egy értéket, a `Jan 01, 2017 12AM-2AM` első sorában.

    > [!IMPORTANT]
    > Az előző példában való származtatás oszlopok akkor használható több lépést egy oszlopot, amely tartalmazza a dátum és idő időszak. Ebben a példában láthatja, hogy ez a művelet is végezhető el, egyetlen lépésben azáltal, hogy a végső kimenetet példát.

    > [!NOTE]
    > Egy példa elleni bármely sor biztosíthat. Ehhez a példához értékének `Jan 01, 2017 12AM-2AM` adatok első sora érvényes.

    ![A példaadatokat képe](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)
   
3. Várjon, amíg az alkalmazás kiszámítja az értékeket, szemben az összes sor. Ez eltarthat néhány másodpercig. Miután elemzése befejeződött, a a __felülvizsgálati következő javasolt sor__ hivatkozás adatok áttekintéséhez.

   ![Tekintse át a hivatkozással Befejezett elemzési képe](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Győződjön meg arról, hogy helyesek-e a számított érték. Ha nem, frissítse az értéket a várt értékkel, és nyomja le az enter. Várjon, amíg befejeződik az elemzést. Fejezze be a **felülvizsgálati következő javasolt sor** feldolgozni, amíg megjelenik **nincs javaslat**. Miután látja **nincs javaslat**, az alkalmazás a peremhálózati esetekben megvizsgálta és elégedett a szintetizált program. Akkor hajtsa végre az átalakított adatok vizuális ellenőrzése az átalakítás elfogadása előtt ajánlott eljárás. 

4. Válassza ki **OK** az átalakítás fogadásához. Az újonnan létrehozott oszlop átnevezése **órában dátumtartomány**.

    ![Az átnevezett oszlop képe](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Kattintson a jobb gombbal a **starttime** oszlop fejlécére, és válassza **eltávolítása oszlop**.

6. Összefoglalva az adatokat, válassza ki a __Summarize__ a a __átalakítási__ menü. A transzformáció létrehozásához hajtsa végre a következő műveleteket:

    * A csomóponthúzási __órában dátumtartomány__ és __állomás azonosítója start__ balra (csoportosítás) megjelenítő.
    * A csomóponthúzási __indítsa el az állomás azonosítója__ jobb (adatainak összefoglalója) panelen.

   ![Az összegzési beállítások képe](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Válassza ki **OK** összefoglaló eredménye fogadásához.

## <a name="join-dataflows"></a>Csatlakozás dataflows

Csatlakozás az időjárási adatok út adatokkal, használja az alábbi lépéseket:

1. Válassza ki __csatlakozás__ a a __átalakítja__ menü.

2. __Táblák__: válasszon **BostonWeather** , a bal oldali adatfolyamblokk és **201701-hubway-tripdata** , a jobb oldali adatfolyamban. A folytatáshoz válasszon **következő**.

    ![A táblák beállításokat képe](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Olyan oszlop__: válassza ki a **órában dátumtartomány** mind a táblákat, és válassza ki azt az oszlop __következő__.

    ![A csatlakoztatása kulcsfontosságú oszlopok képe](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Összekapcsolás típusa__: válasszon __megfelelő sorok__ illesztés típusát, és válassza ki azt __Befejezés__.

    ![Egyező sorok összekapcsolás típusa](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Ez a folyamat létrehoz egy új adatfolyamblokk nevű __csatlakozás eredmény__.

## <a name="create-additional-features"></a>További funkciók létrehozása

1. A hét napját tartalmazó oszlop létrehozása, kattintson a jobb gombbal a a **órában dátumtartomány** oszlop, és válassza ki **származtatott oszlop példa alapján**. Az érték __Sun__ eltérő vasárnap történt. Például **2017. január 01. de 12 - hajnali 2 óra**. Nyomja le az **Enter** majd **OK**. Nevezze át ezt az oszlopot __hétköznap__.

    ![A hét napját tartalmazó új oszlop létrehozása képe](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Egy sor az adott időszakban tartalmazó oszlop létrehozása, kattintson a jobb gombbal a a **órában dátumtartomány** oszlop, és válassza ki **származtatott oszlop példa alapján**. Az érték **12 óra - hajnali 2 óra** a sort tartalmazó **2017. január 01. de 12 - hajnali 2 óra**. Nyomja le az **Enter** majd **OK**. Nevezze át ezt az oszlopot **időszak**.

    ![A rendszeridőtartam-oszlopába képe](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Eltávolítja a **órában dátumtartomány** és **rDate óra tartomány** oszlopok, nyomja le az ENTER **Ctrl (parancsának ⌘ Mac)** minden oszlop fejlécére, majd. Kattintson a jobb gombbal, és válassza ki **oszlop eltávolítása**.

## <a name="read-data-from-python"></a>A Python-adatok olvasása

Futtassa egy adatok előkészítése csomagot, Python vagy PySpark, és lekérése az eredmény egy **adatok keret**.

Példa Python-parancsfájl létrehozásához kattintson a jobb gombbal a __BikeShare adatok előkészítő__ válassza __készítése adatfájl hozzáférési kód__. A példa Python-fájl jön létre a **projektmappa**, és be töltve, egy lapon a munkaterületen is. A következő Python-parancsfájl látható egy példa a generált kódot:

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

Ebben az oktatóanyagban a fájl neve nem `BikeShare Data Prep.py`. Ezt a fájlt az oktatóanyag későbbi részében használja.

## <a name="save-test-data-as-a-csv-file"></a>Vizsgálati adatok mentése a CSV-fájlként

Menti a **csatlakozás eredmény** az adatfolyam egy. CSV-fájl, módosítania kell a `BikeShare Data Prep.py` parancsfájl. Frissítés a Python-parancsfájl a következő kódot:

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

Válassza ki **futtatása** a képernyő tetején. A parancsfájl nyújtják, mint egy **feladat** a helyi számítógépen. Ha a feladat állapota __befejezve__, a fájl van írva a megadott helyre.

## <a name="substitute-data-sources"></a>Helyettesítő adatforrások

Az előző lépésben használt a `201701-hubway-tripdata.csv` és `BostonWeather.csv` készíti elő a Tesztadatok tárolt adatforrások. A csomag használata a más út adatfájlok, tegye a következőket:

1. Hozzon létre egy új **adatforrás** megadott korábban, a folyamat következő módosításainak lépések alapján:

    * __Fájl kiválasztása__: egy fájlt, a többszörös kiválasztási hat út tripdata fennmaradó lehetőséget választva. CSV-fájlt.

        ![A hat többi fájl betöltése](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > A __+ 5__ a bejegyzés azt jelzi, hogy vannak-e túl, amely szerepel a öt további fájlokat.

    * __Fájl részletei__: beállítása __lépteti elő a fejlécek mód__ való **minden fájlnál le az azonos fejlécek**. Ez az érték azt jelzi, hogy minden fájl a azonos fejlécet tartalmaz.

        ![Fájl részleteinek kiválasztása](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Ez az adatforrás neve mentse a későbbi lépésekben használatban van.

2. Válassza ki a mappa ikonra kattintva megtekintheti a fájlokat a projekt. Bontsa ki a __aml\_config__ könyvtárába, és jelölje ki a `local.runconfig` fájlt.

    ![Local.runconfig helyét képe](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. Adja hozzá a következő sorokat végén a `local.runconfig` fájlt, és válassza ki a lemez ikonra kattintva mentse a fájlt.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Ez a változás a hat út adatfájljait tartalmazó lecseréli az eredeti adatforrás.

## <a name="save-training-data-as-a-csv-file"></a>Mentse a betanítási adatok CSV-fájlként

Keresse meg a Python fájlt `BikeShare Data Prep.py` korábbi szerkeszteni, és adjon meg egy másik fájl elérési útját a betanítási adatok mentése.

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

Új feladat elküldése, használja a **futtatása** ikonra az oldal tetején. A **feladat** nyújtják az új konfigurációval. Ez a feladat eredménye a betanítási adatok. Adatok előkészítése lépéseket kell korábban létrehozott. Ezek az adatok létre. A feladat befejezéséhez néhány percig is eltarthat.

## <a name="next-steps"></a>Következő lépések
A megosztás kerékpárt adatok előkészítése az oktatóanyag befejeződött. Ebben az oktatóanyagban használt Azure Machine Learning-szolgáltatások (előzetes verzió) megtudhatja, hogyan:
> [!div class="checklist"]
> * Készítse elő az adatokat az Azure Machine Learning adatok előkészítése eszközzel interaktív módon
> * Importálása, átalakítása és egy tesztelési adatkészletnél létrehozása
> * Adatok előkészítése csomag létrehozása
> * Futtassa az előkészítési adatcsomag pythonos környezetekben
> * Képzési dataset elő újból felhasználja a bemeneti fájlok további adatok előkészítése csomagja

A következő további adatok előkészítésével kapcsolatban:
> [!div class="nextstepaction"]
> [Adatok előkészítése felhasználói útmutatója](data-prep-user-guide.md)
