<properties
    pageTitle="Egyszerű kísérlet létrehozása a Machine Learning Studióban | Microsoft Azure"
    description="Ebből a kezdő szintű gépi tanulási oktatóanyagból elsajátíthatja, hogyan hozhat létre egy egyszerű kísérletet, és miképpen taníthat be és tesztelhet egy lineáris regressziós modellt az Azure Machine Learning Studióban."
    keywords="experiment,linear regression,machine learning algorithms,machine learning tutorial,predictive modeling techniques"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="03/09/2016"
    ms.author="garye"/>

# Machine Learning oktatóanyag: Az első kísérlet létrehozása az Azure Machine Learning Studióban

Ebben a kezdő szintű Machine Learning oktatóanyagban létrehozunk egy lineáris regressziós modellt, amely képes különböző változók (például a márka és a műszaki adatok) alapján előre jelezni az autók árát. Ehhez az Azure Machine Learning Studióban létrehozunk egy egyszerű prediktív elemzési kísérletet, amelyet aztán megismételhet.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

A Machine Learning Studio-kísérlet keretében a komponenseket a megfelelő helyre kell húzni a vásznon, majd összekapcsolni őket, és így *létrehozni a modellt*, *betanítani a modellt*, majd *pontozni és tesztelni a modellt*. A kísérletben a Machine Learning Studio moduljai által képviselt prediktív modellezési módszereket használjuk, amelyek betöltik az adatokat, ezek alapján elvégzik a modell tanítását, majd alkalmazzák a modellt az új adatokra. Ezenfelül más modellek segítségével elvégezheti az adatok előfeldolgozását, vagy jellemzőket választhat ki, tanítási és tesztelési halmazokra oszthatja az adatokat, valamint kiértékelheti és ellenőrizheti a modell minőségét.  

Nyissa meg a Machine Learning Studiót a következő címen: [https://studio.azureml.net](https://studio.azureml.net), majd kattintson a **Get started** (Első lépések) gombra. Válassza a Guest Access (Vendéghozzáférés) lehetőséget, vagy jelentkezzen be a Microsoft-fiókjával.

A Machine Learning Studio általános bemutatását a [What is Machine Learning Studio?](machine-learning-what-is-ml-studio.md) (Mire használható a Machine Learning Studio?) című cikk tartalmazza.

>[AZURE.TIP] Ha szeretne letölteni egy kinyomtatható diagramot, amely bemutatja a Machine Learning Studio funkcióit, nyissa meg az [Overview diagram of Azure Machine Learning Studio capabilities](machine-learning-studio-overview-diagram.md) (Az Azure Machine Learning Studio funkcióit bemutató diagram) című cikket.


## A kísérlet létrehozásának öt lépése

Ebben a Machine Learning oktatóanyagban öt lépés végrehajtásával fogjuk megalkotni a Machine Learning Studio-kísérletet, amely elvégzi a modell létrehozását, betanítását és pontozását. Ezek:

- Modell létrehozása
    - [1. lépés: Az adatok beszerzése]
    - [2. lépés: Az adatok előfeldolgozása]
    - [3. lépés: A jellemzők meghatározása]
- A modell betanítása
    - [4. lépés: Tanulási algoritmus kiválasztása és alkalmazása]
- A modell pontozása és tesztelése
    - [5. lépés: Új autó árának előrejelzése]

[1. lépés: Az adatok beszerzése]: #step-1-get-data
[2. lépés: Az adatok előfeldolgozása]: #step-2-preprocess-data
[3. lépés: A jellemzők meghatározása]: #step-3-define-features
[4. lépés: Tanulási algoritmus kiválasztása és alkalmazása]: #step-4-choose-and-apply-a-learning-algorithm
[5. lépés: Új autó árának előrejelzése]: #step-5-predict-new-automobile-prices


## 1. lépés: Az adatok beszerzése

A Machine Learning Studio számos mintaként használható adathalmazt tartalmaz, de számos más forrásból is importálhat adatokat. Ebben a bemutatóban a szolgáltatásban található **Automobile price data (Raw)** (Nyers autóáradatok) nevű mintahalmazt fogjuk használni.
Ebben az adathalmazban számos különböző autót bemutató bejegyzés szerepel. A bejegyzések számos adatot (például márka, típus, műszaki specifikációk, ár) tartalmaznak.

1. Új kísérlet indításához kattintson a Machine Learning Studio ablakának alsó részén található **+NEW** (Új létrehozása) gombra, és válassza az **EXPERIMENT** (Kísérlet), majd a **Blank Experiment** (Üres kísérlet) lehetőséget. Jelölje ki a kísérlet alapértelmezett nevét a vászon tetején, és módosítsa valami értelmesebbre, például arra, hogy **Autó árának előrejelzése**.

2. A kísérletvászontól balra az adathalmazokat és modulokat tartalmazó paletta látható. A paletta tetején található keresőmezőbe gépelje be, hogy **automobile**. A rendszer megjeleníti az **Automobile price data (Raw)** (Nyers autóáradatok) nevű adathalmazt.

    ![Palet zoeken][screen1a]

3. Húzza rá az adathalmazt a kísérletvászonra.

    ![Adathalmaz][screen1]

Ha szeretné megtekinteni az adatokat, kattintson az autókat tartalmazó adathalmaz alsó részén látható kimeneti portra, és válassza a **Visualize** (Képi megjelenítés) elemet. Megjelennek az adathalmazban található változókat tartalmazó oszlopok, az egyes autóknak egy-egy sor felel meg. A jobb szélső oszlop (azaz a 26-os, „price” (ár) című oszlop) a célváltozó, amelyet meg szeretnénk kapni.

![Az adathalmaz képi megjelenítése][screen1b]

A jobb felső sarokban látható „**x**” gombra kattintva zárja be a képi megjelenítési ablakot.

## 2. lépés: Az adatok előfeldolgozása

Az adathalmazok elemzése előtt általában némi előfeldolgozás szükséges. Talán észrevette, hogy az oszlopok számos sorából hiányoztak az értékek. Ahhoz, hogy a modell elemezni tudja az adatokat, el kell távolítani a hiányzó értékeket. Ebben az esetben törölni fogjuk azokat a sorokat, amelyekből értékek hiányoznak. A **normalized-losses** (normalizált veszteségek) című oszlopból ráadásul rendkívül sok érték hiányzik, ezért ezt az oszlopot teljesen kizárjuk a modellből.

> [AZURE.TIP] A legtöbb modul használatának előfeltétele a bemeneti adatok hiányzó értékeinek törlése.

Először eltávolítjuk a **normalized-losses** oszlopot, majd az összes sort, amelyből adatok hiányoznak.

1. A modulpaletta tetején található keresőmezőbe gépelje be a **select columns** (oszlopok kijelölése) kifejezést. A rendszer megjeleníti a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modult. Húzza ezt a kísérletvászonra, és kösse össze az **Automobile price data (Raw)** (Nyers autóáradatok) című adathalmaz kimeneti portjával. Ezzel a modullal kiválaszthatjuk, hogy melyik adatoszlopokat szeretnénk bevonni a modellbe, vagy éppen kizárni a modellből.

2. Jelölje ki a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modult, majd a **Properties** (Tulajdonságok) panelen kattintson a **Launch column selector** (Oszlopválasztó elindítása) elemre.

    - A **Begin With** (Kezdés) szűrő alatti legördülő listában válassza az **All columns** (Összes oszlop) lehetőséget. Ezzel megadja, hogy a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modul az összes oszlopot feldolgozza (kivéve azokat, amelyeket hamarosan ki fogunk zárni).
    - A következő sorban válassza az **Exclude** (Kizárás) **column names** (oszlopnév alapján) lehetőséget, majd kattintson a szövegdobozba. Megjelenik az oszlopnevek listája. Válassza a **normalized-losses** lehetőséget, amely aztán bekerül a szövegdobozba.
    - Az oszlopválasztó bezárásához kattintson a pipa (OK) gombra.

    ![Oszlopok kiválasztása][screen3]

    A **Select Columns in Dataset** (Adathalmaz oszlopainak kijelölése) modul Properties (Tulajdonságok) panelje jelzi, hogy a modul az adathalmaz összes oszlopát fel fogja dolgozni, a **normalized-losses** kivételével.

    ![Oszlopok kiválasztása az adathalmaz tulajdonságaiban][screen4]

    > [AZURE.TIP] A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. A jelen esetben kattintson a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modulra, és írja be: „A normalized-losses oszlop kizárása”.

3. Húzza a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modult a kísérletvászonra, és kösse össze a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modullal. A **Properties** (Tulajdonságok) panel **Cleaning mode** (Törlés módja) beállításánál válassza a **Remove entire row** (Teljes sor eltávolítása) lehetőséget. Ha a szolgáltatás hiányzó adatot talál, az egész sort törli. Kattintson duplán a modulra, és írja be a következő megjegyzést: „Hiányzó értéket tartalmazó sorok törlése”.

    ![Hiányzó adatok törlése][screen4a]

4. A kísérlet futtatásához kattintson a kísérletvászon alatt található **RUN** (Futtatás) gombra.

A kísérlet befejezését követően az összes modulnál megjelenik egy zöld pipa, amely jelzi, hogy az adott modul sikeresen lefutott. A jobb felső sarokban pedig megjelenik a **Finished running** (Futtatás befejeződött) állapot.

![Első kísérlet futtatása][screen5]

Eddig még csupán az adatok megtisztítását végeztük el. Ha szeretné megtekinteni a megtisztított adathalmazt, kattintson a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modul bal oldali kimeneti portjára („megtisztított adatok”), és válassza a **Visualize** (Képi megjelenítés) lehetőséget. Láthatja, hogy a **normalized-losses** oszlop eltűnt, ahogy a hiányzó értékek is.

Most, hogy megtisztítottuk az adatokat, megadhatjuk, hogy mely jellemzőket szeretnénk felhasználni a prediktív modellben.

## 3. lépés: A jellemzők meghatározása

A gépi tanulásban a *jellemzők* azok a külön-külön mérhető tulajdonságok, amelyekre kíváncsiak vagyunk. Adathalmazunk minden sora egy-egy autót képvisel, az oszlopok pedig az autók különböző jellemzőit tartalmazzák. A prediktív modellben használandó jellemzők helyes megválasztásához fontos a kísérletezés, illetve a megoldani kívánt probléma jó ismerete. Bizonyos jellemzők ugyanis hasznosabbak a cél előrejelzéséhez, mint mások. Ráadásul egyes jellemzők erős korrelációban állnak más jellemzőkkel – példánkban ilyen például a city-mpg (fogyasztás városban) és highway-mpg (fogyasztás autópályán). Ezek nem sok újat adnak a modellhez, ezért eltávolíthatók.

Ideje, hogy létrehozzuk a modellt az adathalmaz jellemzőinek meghatározott részhalmaza alapján. Később visszatérhet ehhez a lépéshez, és más jellemzőket kiválasztva ismét lefuttathatja a modellt, ha kíváncsi rá, hogy úgy jobb eredményeket kap-e. Először próbálkozzunk meg a következő jellemzőkkel (oszlopokkal). Válassza ki ezeket a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modul segítségével. Ne feledje, hogy a modell betanításához mindenképpen ki kell választanunk a *price* (ár) értéket, hiszen ezt szeretnénk előre jelezni.

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Húzzon egy újabb [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modult a kísérletvászonra, és ezt kösse össze a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modul bal oldali kimeneti portjával. Kattintson duplán a modulra, és írja be: „Az előrejelzéshez használatos jellemzők kiválasztása”.

2. Kattintson a **Properties** (Tulajdonságok) panel **Launch column selector** (Oszlopválasztó indítása) elemére.

3. Az oszlopválasztóban a **Begin With** (Kezdés) résznél válassza a **No columns** (Oszlopok nélkül) lehetőséget, majd a szűrősorban válassza az **Include** (Bevonás) és **column names** (oszlopnevek) lehetőségeket. Adja meg a kiválasztott oszlopok nevét. Ezzel arra utasítja a modult, hogy csak a megadott oszlopokat dolgozza fel.

    > [AZURE.TIP] Mivel már egyszer lefuttattuk a kísérletet, az adatainkra vonatkozó oszlopdefiníciók az eredeti adathalmazból átkerültek a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modulba. Mivel összekötötte a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) és a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modult, a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modul megkapja az adatokban található oszlopdefiníciókat. Kattintson a **column names** (oszlopnevek) mezőre. Megjelenik az oszlopnevek listája, amelyből kiválaszthatja, hogy melyeket szeretné a listához adni.

4. Kattintson a pipa (OK) gombra.

![Oszlopok kiválasztása][screen6]

Ezzel létrehozza az adathalmazt, amelyet a következő lépésben említett tanulási algoritmus fel fog használni. Később visszatérhet ide, és más jellemzőkkel is elvégezheti az előrejelzést.

## 4. lépés: Tanulási algoritmus kiválasztása és alkalmazása

Most, hogy előkészítettük az adatokat, a prediktív modell létrehozásához már csak a tanítás és a tesztelés szükséges. A következőkben az adatok segítségével elvégezzük a modell betanítását, majd a modell tesztelésével megállapítjuk, hogy milyen pontossággal képes előre jelezni az árakat.

A *besorolás* és a *regresszió* két módszer, amelynek segítségével felügyelt gépi tanítás valósítható meg. A besorolás értékek meghatározott készletén, például különböző színeken (piros, kék, zöld) alapuló előrejelzésekre használható. A regresszió segítségével ellenben folytonos értékek (például egy személy életkora) alapján lehet előrejelzéseket végezni.

Mivel mi egy autó árára vagyunk kíváncsiak, ami bármekkora érték lehet, ezért a regresszív modellt fogjuk alkalmazni. Ebben a példában egy egyszerű *lineáris regressziós* modellt tanítunk be, amelyet aztán a következő lépésben le is tesztelünk.

1. Az adatok betanításra és tesztelésre is használhatók. Ehhez két halmazra, egy tanítási és egy tesztelési halmazra osztjuk fel az adatokat. Jelölje ki, majd húzza a kísérletvászonra a [Split Data][split] (Adatok felosztása) modult, majd kösse össze a legutóbb használt [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modul kimenetével. A **Fraction of rows in the first output dataset** (Oszlopok hányadosa az első kimeneti adathalmazban) beállításnál adja meg a 0,75 értéket. Így az adatok 75 százalékát a modell betanítására, 25 százalékát pedig a modell tesztelésére használhatjuk.

    > [AZURE.TIP] A **Random seed** (Véletlenszám-generálás kezdőértéke) paraméter módosításával különböző véletlenszerűen kiválasztott mintákat hozhat létre, amelyeket szintén felhasználhat a modell betanítására és tesztelésére. Ez a paraméter szabályozza a pszeudo-véletlenszám-generátor kezdőértékét.

2. Futtassa a kísérletet. Így a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) és a [Split Data][split] (Adatok felosztása) modul képes lesznek átadni a következőkben hozzáadott moduloknak az oszlopdefiníciókat.  

3. A tanulási algoritmus kiválasztásához bontsa ki a vászontól balra, a modulpalettán található **Machine Learning** (Gépi tanulás) kategóriát, majd bontsa ki az **Initialize Model** (Inicializálási modell) kategóriát is. Itt számos modulkategória közül választhat, amelyek segítségével inicializálható a gépi tanulási algoritmus.

    Ehhez a kísérlethez válassza a **Regression** (Regresszió) kategóriában található [Linear Regression][linear-regression] (Lineáris regresszió) modult, majd húzza a kísérletvászonra. A modult úgy is megkeresheti, ha a paletta keresőmezőjébe beírja a „linear regression” kifejezést.

4. Keresse meg, majd húzza a kísérletvászonra a [Train Model][train-model] (Modell betanítása) modult. Kösse össze ennek bal oldali kimeneti portját a [Linear Regression][linear-regression] (Lineáris regresszió) modul kimenetével. Kösse össze ennek jobb bemeneti portját a [Split Data][split] (Adatok felosztása) modul tanítási adatokat tartalmazó kimenetével (bal portjával).

5. Jelölje ki a [Train Model][train-model] (Modell tanítása) modult, kattintson a **Properties** (Tulajdonságok) panel **Launch column selector** (Oszlopválasztó indítása) elemére, és válassza ki a **price** (ár) oszlopot. Ez az az érték, amelyet a modellünk megpróbál előre jelezni.

    ![A „price” oszlop kiválasztása][screen7]

6. Futtassa a kísérletet.

Az eredmény egy betanított regressziós modell lesz, amely képes pontszámot rendelni az új mintákhoz, és elvégezni az előrejelzéseket.

![A gépi tanulási algoritmus alkalmazása][screen8]

## 5. lépés: Új autó árának előrejelzése

Most, hogy adataink 75 százalékával betanítottuk a modellt, a maradék 25 százalék pontozásával megállapíthatjuk, hogy mennyire működik jól.

1. Keresse meg, majd húzza a kísérletvászonra a [Score Model][score-model] (Modell pontozása) modult, majd kösse össze ennek bal bemeneti portját a [Train Model][train-model] (Modell betanítása) modul kimenetével. Kösse össze ennek jobb oldali bemeneti portját a [Split Data][split] (Adatok felosztása) modul tesztelési adatokat tartalmazó kimenetével (jobb oldali portjával).  

    ![A Score model (Modell montozása) modul][screen8a]

2. A kísérlet futtatásához és a [Score Model][score-model] (Modell pontozása) modul eredményének megtekintéséhez kattintson a kimeneti portra, majd válassza a **Visualize** (Képi megjelenítés) lehetőséget. A modul megjeleníti az előre jelzett árat, valamint a tesztadatokból ismert tényleges értéket.  

3. Végül az eredmények minőségének ellenőrzéséhez jelölje ki, majd húzza a kísérletvászonra az [Evaluate Model][evaluate-model] (Modell kiértékelése) modult, és kösse össze ennek bal oldali bemeneti portját a [Score Model][score-model] (Modell pontozása) modul kimenetével. (Itt két bemeneti port szerepel, mivel az [Evaluate Model][evaluate-model] (Modell kiértékelése) modul két modell összehasonlítására is használható).

4. Futtassa a kísérletet.

Az [Evaluate Model][evaluate-model] (Modell kiértékelése) modul eredményének megtekintéséhez kattintson a kimeneti portra, majd válassza a **Visualize** (Képi megjelenítés) lehetőséget. A következő statisztikák tekinthetők meg:

- **Mean Absolute Error** (átlagos abszolút eltérés, MAE): az abszolút eltérések átlaga (*eltérésnek* az előre jelzett érték és a tényleges érték közötti különbséget nevezzük).
- **Root Mean Squared Error** (gyökátlagos négyzetes eltérés, RMSE): a tesztelési adathalmazon végzett előrejelzések eltéréseinek négyzetéből számított átlag négyzetgyöke.
- **Relative Absolute Error** (relatív abszolút eltérés): a tényleges értékek és az összes tényleges értékek átlaga közötti különbségek abszolút eltérésének átlaga.
- **Relative Squared Error** (relatív négyzetes eltérés): a négyzetes eltérések átlaga a tényleges értékek és az összes tényleges érték átlaga közötti különbség négyzetes értékéhez viszonyítva.
- **Coefficient of Determination** (determinációs együttható): ez az **R-négyzet értéke** néven is ismert statisztikai mérőszám azt mutatja, hogy a modell mennyire illik az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb értékek azt jelzik, hogy az előrejelzés közelebb van a tényleges értékekhez. A **Coefficient of Determination** (determinációs együttható) értéke minél közelebb van az egyhez (1,0-hoz), annál pontosabb az előrejelzés.

![A kiértékelés eredménye][screen9]

Az elkészült kísérletnek a következőképpen kell kinéznie:

![Machine Learning oktatóanyag: Prediktív modellezési módszereket használó lineáris regressziós kísérlet kész.][screen10]

## További lépések

Most, hogy az első Machine Learning oktatóanyag végére ért, és beállította kísérletét, megismételheti a műveletet, és kipróbálhatja, hogy tud-e javítani a modellen. Például módosíthatja az előrejelzéshez használt jellemzők körét. Vagy megváltoztathatja a [Linear Regression][linear-regression] (Lineáris regresszió) algoritmus tulajdonságait, vagy akár egy teljesen más algoritmust is kipróbálhat. Akár két különböző gépi tanulási algoritmus segítségével is futtathatja a kísérletet, majd az [Evaluate Model][evaluate-model] (Modell kiértékelése) modul használatával összehasonlíthatja az eredményeket.

> [AZURE.TIP] Az ismétlések kísérletbe mentéséhez használja a kísérletvászon alatt található **SAVE AS** (Mentés másként) gombot. A kísérlet összes ismétlésének megtekintéséhez kattintson a vászon alatti **VIEW RUN HISTORY** (Futtatási előzmények megtekintése) elemre. További információk: [Manage experiment iterations in Azure Machine Learning Studio][runhistory] (Kísérlet ismétléseinek kezelése az Azure Machine Learning Studióban).

[runhistory]: machine-learning-manage-experiment-iterations.md

Ha már elégedett a modellel, helyezze üzembe webszolgáltatásként, amely új adatok alapján képes előre jelezni az autók árát. További információk: [Deploy an Azure Machine Learning web service][publish] (Azure Machine Learning-webszolgáltatás üzembe helyezése).

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Ha szeretné részletesebben megismerni a modellek létrehozásához, tanításához, pontozásához és üzembe helyezéséhez használható prediktív modellezési módszereket, olvassa el a [Develop a predictive solution by using Azure Machine Learning][walkthrough] (Prediktív megoldás létrehozása az Azure Machine Learning segítségével) című cikket.

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[képernyő1]:./media/machine-learning-create-experiment/screen1.png
[képernyő1a]:./media/machine-learning-create-experiment/screen1a.png
[képernyő1b]:./media/machine-learning-create-experiment/screen1b.png
[képernyő2]:./media/machine-learning-create-experiment/screen2.png
[képernyő3]:./media/machine-learning-create-experiment/screen3.png
[képernyő4]:./media/machine-learning-create-experiment/screen4.png
[képernyő4a]:./media/machine-learning-create-experiment/screen4a.png
[képernyő5]:./media/machine-learning-create-experiment/screen5.png
[képernyő6]:./media/machine-learning-create-experiment/screen6.png
[képernyő7]:./media/machine-learning-create-experiment/screen7.png
[képernyő8]:./media/machine-learning-create-experiment/screen8.png
[képernyő8a]:./media/machine-learning-create-experiment/screen8a.png
[képernyő9]:./media/machine-learning-create-experiment/screen9.png
[képernyő10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/



<!--HONumber=Jun16_HO2-->


