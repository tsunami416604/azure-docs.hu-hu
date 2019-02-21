---
title: 'Gyors útmutató: Adatelemzési kísérlet létrehozása'
titleSuffix: Azure Machine Learning Studio
description: A machine learning rövid útmutató bemutatja egy egyszerű adatelemezési kísérletet. Egy regressziós algoritmus használatával fogjuk előre megbecsülni egy autó árát.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: 803a52994536d2d6f39a064f97af7831af0cebb6
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453171"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Gyors útmutató: Az első adatelemzési kísérlet létrehozása az Azure Machine Learning Studióban

Ebben a rövid útmutatóban létrehozhat egy machine learning-kísérlet a [Azure Machine Learning Studióban](what-is-ml-studio.md) , amely a fedélzeti például márka, műszaki jellemzők különböző változók alapján egy autó árát.

Ha Ön a machine learning, a videósorozat a vadonatúj [adatelemzés kezdőknek](data-science-for-beginners-the-5-questions-data-science-answers.md) remek bevezetőt kínál hétköznapi nyelven és elterjedt fogalmak használatával a gépi tanulás.

Ebben a rövid útmutatóban egy kísérletet az alapértelmezett munkafolyamat követi:

1. **Modell létrehozása**
    - [Az adatok lekérése]
    - [Az adatok előkészítése]
    - [A jellemzők meghatározása]
1. **A modell betanítása**
    - [Egy algoritmus kiválasztása és alkalmazása]
1. **A modell pontozása és tesztelése**
    - [Új autó árának előrejelzése]

[Az adatok lekérése]: #get-the-data
[Az adatok előkészítése]: #prepare-the-data
[A jellemzők meghatározása]: #define-features
[Egy algoritmus kiválasztása és alkalmazása]: #choose-and-apply-an-algorithm
[Új autó árának előrejelzése]: #predict-new-automobile-prices

Studio-fiók nem rendelkezik, ha a [Studio kezdőlap](https://studio.azureml.net) , és válassza ki **itt regisztráció** , hozzon létre egy ingyenes fiókot. Az ingyenes munkaterületen fog ebben a rövid útmutatóban szükséges összes funkciót.

## <a name="get-the-data"></a>Az adatok lekérése

Először is szüksége van a machine Learning szolgáltatásban az adatok.
Nincsenek a Studio használható számos mintaadathalmazainak, vagy adatokat számos más forrásból is importálhat. Ebben a példában a munkaterületén megtalálható **Automobile price data (Raw)** (Nyers autóáradatok) nevű mintahalmazt fogjuk használni.
Ebben az adathalmazban számos különböző autót bemutató bejegyzés szerepel. A bejegyzések számos adatot (például márka, típus, műszaki specifikációk, ár) tartalmaznak.

> [!TIP]
> Az [Azure AI katalógusban](https://gallery.azure.ai) megtalálja az alábbi kísérlet egy működő példányát. Lépjen a **[Your first data science experiment - Automobile price prediction](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** (Az első adatelemzési kísérlet – Autó árának előrejelzése) lapra, és kattintson az **Open in Studio** (Megnyitás a Studióban) lehetőségre a kísérlet Machine Learning Studio munkaterületre való letöltéséhez.

A következőképpen vonhatja be az adathalmazt a kísérletbe.

1. Új kísérlet létrehozása gombra kattintva **+ új** a Machine Learning Studio ablakának alján. Válassza ki **kísérlet** >  **üres kísérlet**.

1. A kísérlet kap egy alapértelmezett nevet, amelyet a vászon tetején láthat. Jelölje ki ezt a szöveget, és módosítsa valami értelmesebbre, például arra, hogy **Autó árának előrejelzése**. A névnek nem kell egyedinek lennie.

    ![A kísérlet átnevezése](./media/create-experiment/rename-experiment.png)

1. A kísérletvászontól balra az adathalmazokat és modulokat tartalmazó paletta látható. A paletta tetején található keresőmezőbe gépelje be, hogy **automobile**. A rendszer megjeleníti az **Automobile price data (Raw)** (Nyers autóáradatok) nevű adathalmazt. Húzza rá az adathalmazt a kísérletvászonra.

    ![Az autókat tartalmazó adathalmaz megkeresése és a kísérleti vászonra húzása](./media/create-experiment/type-automobile.png)

Tekintse meg, mi ezeket az adatokat úgy tűnik, kattintson az autókat tartalmazó adathalmaz alsó a kimeneti portra, majd válassza ki a **Visualize**.

![Kattintson a kimeneti portra, és válassza a "Visualize"](./media/create-experiment/select-visualize.png)

> [!TIP]
> Az adathalmazok és modulok kis körökkel jelölt bemeneti és kimeneti portokkal rendelkeznek – a bemeneti portok felül, a kimeneti portok alul találhatók.
Az adatfolyam létrehozásához a kísérlet során össze fogja kötni az egyik modul kimeneti portját egy másik modul bemeneti portjával.
Ha meg szeretné tekinteni, hogyan jelennek meg az adatok az adatfolyam egy adott pontján, kattintson az adathalmaz vagy modul kimeneti portjára.

Ez az adatkészlet minden sor egy autó jelöli, és az egyes autókhoz tartozó változók oszlopokként jelennek meg. Az ár a jobb szélső oszlopban (nevű oszlophoz 26., "price") egy adott autóhoz tartozó változók használatával fogjuk előre megbecsülni.

![Az autókra vonatkozó adatok megtekintése az adatok ablakban](./media/create-experiment/visualize-auto-data.png)

A jobb felső sarokban látható „**x**” gombra kattintva zárja be a képi megjelenítési ablakot.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Az adathalmazok elemzése előtt általában némi előfeldolgozás szükséges. Talán észrevette, hogy az oszlopok számos sorából hiányoztak az értékek. Ahhoz, hogy a modell elemezni tudja az adatokat, el kell távolítani a hiányzó értékeket. Törölni fogjuk azokat a sorokat, amelyekből értékek hiányoznak. A **normalized-losses** (normalizált veszteségek) című oszlopból ráadásul rendkívül sok érték hiányzik, ezért ezt az oszlopot teljesen kizárjuk a modellből.

> [!TIP]
> A legtöbb modul használatának előfeltétele a bemeneti adatok hiányzó értékeinek törlése.

Először hozzáadunk egy modult, amely eltávolítja a **normalized-losses** oszlopot teljesen. Ezután hozzáadunk egy másik modul, amely eltávolítja az összes sort, amelyből adatok hiányoznak.

1. Típus **Oszlopválasztás** kifejezést a keresőmezőbe, a modulpaletta tetején a [Select Columns in Dataset] [ select-columns] modul. Húzza a kísérletvászonra. Ezzel a modullal kiválaszthatjuk, hogy melyik adatoszlopokat szeretnénk bevonni a modellbe, vagy éppen kizárni a modellből.

1. A kimeneti portjára, csatlakozzon a **Automobile price data (Raw)** adatkészlet a Select Columns in Dataset bemeneti portjával.

    ![A "Kiválasztása oszlopok az adatkészlet" modul hozzáadása a kísérletvászonhoz, majd összekötése](./media/create-experiment/type-select-columns.png)

1. Kattintson a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modulra, majd a **Properties** (Tulajdonságok) panelen kattintson a **Launch column selector** (Oszlopválasztó elindítása) elemre.

    - A bal oldalon kattintson a **With rules** (Szabályokkal) lehetőségre
    - A **Begin With** (Kezdés a következővel) területen kattintson az **All columns** (Minden oszlop) lehetőségre. Ezek a szabályok közvetlen [Select Columns in Dataset] [ select-columns] (kivéve azokat fogunk kizárása) az összes oszlopot.
    - A legördülő listákból válassza az **Exclude** (Kizárás) és a **column names** (oszlopnevek) lehetőséget, majd kattintson a szövegmezőbe. Megjelenik az oszlopnevek listája. Válassza a **normalized-losses** (normalizált veszteségek) lehetőséget, amely aztán bekerül a szövegdobozba.
    - Kattintson a pipa (OK) gombra (a jobb alsó) az Oszlopválasztó bezárásához.

    ![Az Oszlopválasztó elindítása és a "normalized-losses" oszlop kizárása](./media/create-experiment/launch-column-selector.png)

    Ekkor a **Select Columns in Dataset** (Adathalmaz oszlopainak kijelölése) modul Properties (Tulajdonságok) panelje jelzi, hogy a modul a **normalized-losses** ( normalizált veszteségek) kivételével az adathalmaz összes oszlopát fel fogja dolgozni.

    ![A Tulajdonságok panelen látható, hogy a "normalized-losses" oszlop ki van zárva](./media/create-experiment/showing-excluded-column.png)

    > [!TIP] 
    > A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. A jelen esetben kattintson duplán a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modulra, és írja be az „Exclude normalized losses” (A normalized-losses oszlop kizárása) szöveget.

    ![Megjegyzés hozzáadása modulhoz dupla kattintással](./media/create-experiment/add-comment.png)

1. Húzza a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modult a kísérletvászonra, és kösse össze a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modullal. A **Properties** (Tulajdonságok) panel **Cleaning mode** (Törlés módja) beállításánál válassza a **Remove entire row** (Teljes sor eltávolítása) lehetőséget. Ezeket a beállításokat a közvetlen [Clean Missing Data] [ clean-missing-data] , amely tartalmaz a hiányzó sort törölje. Kattintson duplán a modulra, és írja be a következő megjegyzést: „Hiányzó értéket tartalmazó sorok törlése”.

    !["Remove entire row eltávolítása" a "Clean Missing Data" modul törlés módjának beállítása](./media/create-experiment/set-remove-entire-row.png)

1. A kísérlet futtatásához kattintson a lap alján található **RUN** (Futtatás) parancsra.

    A kísérlet befejezését követően az összes modulnál megjelenik egy zöld pipa, amely jelzi, hogy az adott modul sikeresen lefutott. A jobb felső sarokban pedig megjelenik a **Finished running** (Futtatás befejeződött) állapot.

    ![Futtatás után a kísérlet várható megjelenése a](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Miért futtattuk a kísérletet most? A kísérlet futtatásával biztosítható, hogy az adatokhoz tartozó oszlopdefiníciók az adatkészletből áthaladnak a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modulon és a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modulon. Ez azt jelenti, hogy a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modulhoz kapcsolt modulok is megkapják ugyanezeket az adatokat.

Az adatok tisztítása várunk. Ha szeretné megtekinteni a megtisztított adathalmazt, kattintson a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modul bal oldali kimeneti portjára, és válassza a **Visualize** (Képi megjelenítés) lehetőséget. Láthatja, hogy a **normalized-losses** oszlop eltűnt, ahogy a hiányzó értékek is.

Most, hogy megtisztítottuk az adatokat, megadhatjuk, hogy mely jellemzőket szeretnénk felhasználni a prediktív modellben.

## <a name="define-features"></a>A jellemzők meghatározása

A gépi tanulásban a *jellemzők* azok a külön-külön mérhető tulajdonságok, amelyekre kíváncsiak vagyunk. Adathalmazunk minden sora egy-egy autót képvisel, az oszlopok pedig az autók különböző jellemzőit tartalmazzák.

A prediktív modellben használandó jellemzők helyes megválasztásához fontos a kísérletezés, illetve a megoldani kívánt probléma jó ismerete. Bizonyos jellemzők ugyanis hasznosabbak a cél előrejelzéséhez, mint mások. Egyes funkciók más szolgáltatások erős korrelációban és távolíthatja el. A példánkban például szorosan összefügg a city-mpg (fogyasztás városban) és highway-mpg (fogyasztás autópályán), ezért az egyiket eltávolíthatjuk anélkül, hogy lényegesen befolyásolnánk az előrejelzést.

Ideje, hogy létrehozzuk a modellt az adathalmaz jellemzőinek meghatározott részhalmaza alapján. Később visszatérhet ehhez a lépéshez, és más jellemzőket kiválasztva ismét lefuttathatja a kísérletet, ha kíváncsi rá, hogy úgy jobb eredményeket kap-e. Kezdésként azonban a következő funkciókat próbáljuk ki:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Húzzon egy újabb [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modult a kísérletvászonra. Kösse össze a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modul bal oldali kimeneti portját a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modul bemenetével.

    ![A "Kiválasztása oszlopok az adatkészlet" modul összekötése a "Clean Missing Data" modul](./media/create-experiment/connect-clean-to-select.png)

1. Kattintson duplán a modulra, és írja be: „Az előrejelzéshez használatos jellemzők kiválasztása”.

1. Kattintson a **Properties** (Tulajdonságok) panel **Launch column selector** (Oszlopválasztó indítása) elemére.

1. Kattintson a **With rules** (Szabályokkal) lehetőségre.

1. A **Begin With** (Kezdés a következővel) területen kattintson a **No columns** (Egyetlen oszlop sem) lehetőségre. A szűrősorban válassza ki az **Include** (Belefoglalás) és a **column names** (oszlopnevek) lehetőséget, és jelölje ki az oszlopnevek listáját a szövegmezőben. Ez a szűrő adjuk meg, hogy csak az általunk a modul nem továbbítja a oszlopokat (tulajdonságokat) irányítja.

1. Kattintson a pipa (OK) gombra.

    ![Az előrejelzésbe bevonni az (szolgáltatások) oszlopok](./media/create-experiment/select-columns-to-include.png)

Ez a modul egy szűrt adathalmazt, amelyben csak a következő lépésben használunk tanulási algoritmusnak továbbítani kívánt eredményez. Később visszatérhet ide, és más jellemzőkkel is elvégezheti az előrejelzést.

## <a name="choose-and-apply-an-algorithm"></a>Egy algoritmus kiválasztása és alkalmazása

Most, hogy előkészítettük az adatokat, a prediktív modell létrehozásához már csak a tanítás és a tesztelés szükséges. A következőkben az adatok segítségével elvégezzük a modell betanítását, majd a modell tesztelésével megállapítjuk, hogy milyen pontossággal képes előre jelezni az árakat.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

A *besorolás* és a *regresszió* két algoritmus, amelynek segítségével felügyelt gépi tanítás valósítható meg. Besoroláskor a válaszok előrejelzése megadott kategóriakészletből történik (például: színek (vörös, kék vagy zöld)). A rendszer a számok előrejelzésére regressziós módszert használ.

Mivel az árat szeretnénk előre jelezni, ami egy szám, regressziós algoritmust fogunk használni. Ebben a példában használjuk egy *lineáris regressziós* modell.


A modell betanításához az árat tartalmazó adathalmazt biztosítunk számára. A modell megvizsgálja adatokat, és összefüggéseket keres az autó tulajdonságai és az ára között. Ezután teszteljük a modellt. Ehhez olyan autók tulajdonságkészletét töltjük be, amelyeket ismerünk, és megnézzük, hogy mennyire sikeresen tudja a modell előre jelezni az ismert árakat.

Az adatok a modell betanítására és tesztelésére is használhatók. Ehhez két halmazra, egy tanítási és egy tesztelési halmazra osztjuk fel az adatokat.

1. Jelölje ki, majd húzza a kísérletvászonra a [Split Data][split] (Adatok felosztása) modult, majd kösse össze a legutóbb használt [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modullal.

1. Kattintással jelölje ki a [Split Data][split] (Adatok felosztása) modult. Keresse meg a **Properties** (Tulajdonságok) panelen a vászontól jobbra a **Fraction of rows in the first output dataset** (Sorok hányadosa az első kimeneti adathalmazban) beállítást, és adja meg a 0,75 értéket. Így az adatok 75 százalékát a modell betanítására, 25 százalékát pedig a modell tesztelésére használhatjuk.

    ![Felosztási értékének beállítása a "Split Data" modul meg a 0,75 értéket](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > A **Random seed** (Véletlenszám-generálás kezdőértéke) paraméter módosításával különböző véletlenszerűen kiválasztott mintákat hozhat létre, amelyeket szintén felhasználhat a modell betanítására és tesztelésére. Ez a paraméter szabályozza a pszeudo-véletlenszám-generátor kezdőértékét.

1. Futtassa a kísérletet. A kísérlet futtatásakor a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) és a [Split Data][split] (Adatok felosztása) modul átadja a következőkben hozzáadott moduloknak az oszlopdefiníciókat.  

1. A tanulási algoritmus kiválasztásához bontsa ki a vászontól balra, a modulpalettán található **Machine Learning** (Gépi tanulás) kategóriát, majd bontsa ki az **Initialize Model** (Inicializálási modell) kategóriát is. Itt számos modulkategória közül választhat, amelyek segítségével inicializálható a gépi tanulási algoritmus. Ehhez a kísérlethez válassza a **Regression** (Regresszió) kategóriában található [Linear Regression][linear-regression] (Lineáris regresszió) modult, majd húzza a kísérletvászonra. (A modult úgy is megkeresheti, ha a paletta keresőmezőjébe beírja a „linear regression” kifejezést.)

1. Keresse meg, majd húzza a kísérletvászonra a [Train Model][train-model] (Modell betanítása) modult. Kösse össze a [Linear Regression][linear-regression] (Lineáris regresszió) modul kimenetét a [Train Model][train-model] (Modell betanítása) modul bal oldali bemenetével, és kösse össze a [Split Data][split] (Adatok felosztása) modul adatbetanítási kimenetét (bal oldali port) a [Train Model][train-model] (Modell betanítása) modul jobb oldali bemenetével.

    ![A "Train Model" modul összekötése a "Linear Regression" és a "Split Data" modulokkal](./media/create-experiment/connect-train-model.png)

1. Kattintson a [Train Model][train-model] (Modell betanítása) modulra, kattintson a **Properties** (Tulajdonságok) panel **Launch column selector** (Oszlopválasztó indítása) elemére, és válassza ki a **price** (ár) oszlopot. **Ár** modellünk előre jelezni az értéket.

    Jelölje ki a **price** (ár) oszlopot az oszlopválasztóban. Ehhez helyezze át az **Available columns** (Elérhető oszlopok) listáról a **Selected columns** (Kiválasztott oszlopok) listára.

    ![A "Train Model" modul az ár oszlop kiválasztása](./media/create-experiment/select-price-column.png)

1. Futtassa a kísérletet.

Ezzel kapunk egy betanított regressziós modellt, amely képes pontszámot rendelni az új autóadatokhoz, és így előre jelezni az árakat.

![Futtatás után a kísérletet most hasonlóan kell kinéznie a](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Új autó árának előrejelzése

Most, hogy adataink 75 százalékával betanítottuk a modellt, a maradék 25 százalék pontozásával megállapíthatjuk, hogy mennyire működik jól.

1. Keresse meg, majd húzza a kísérletvászonra a [Score Model][score-model] (Modell pontozása) modult. Kösse össze a [Train Model][train-model] (Modell betanítása) modul kimenetét a [Score Model][score-model] (Modell pontozása) modul bal oldali bemeneti portjával. Kösse össze a [Split Data][split] (Adatok felosztása) modul tesztelési adatokat tartalmazó kimenetét (jobb oldali portját) a [Score Model][score-model] (Modell pontozása) modul jobb oldali bemeneti portjával.

    ![A "Score Model" modul összekötése a "Train Model" és a "Split Data" modulokkal](./media/create-experiment/connect-score-model.png)

1. A kísérlet futtatásához és eredményének megtekintéséhez a [Score Model] [ score-model] modul kimeneti portjával kattintva [Score Model] [ score-model] Válasszaki **Megjelenítheti**. A modul megjeleníti az előre jelzett árat, valamint a tesztadatokból ismert tényleges értéket.  

    ![A "Score Model" modul kimenete](./media/create-experiment/score-model-output.png)

1. Végül teszteljük az eredmény minőségét. Jelölje ki, majd húzza a kísérletvászonra az [Evaluate Model][evaluate-model] (Modell kiértékelése) modult, és kösse össze a [Score Model][score-model] (Modell pontozása) modul kimenetét az [Evaluate Model][evaluate-model] (Modell kiértékelése) bal oldali bemeneti portjával. Az elkészült kísérletnek a következőképpen kell kinéznie:

    ![Az elkészült kísérlet](./media/create-experiment/complete-linear-regression-experiment.png)

1. Futtassa a kísérletet.

Az [Evaluate Model][evaluate-model] (Modell kiértékelése) modul eredményének megtekintéséhez kattintson a kimeneti portra, majd válassza a **Visualize** (Képi megjelenítés) lehetőséget.

![A kísérlet kiértékelésének eredménye](./media/create-experiment/evaluation-results.png)

A következő statisztikák tekinthetők meg:

- **Mean Absolute Error** (MAE): A átlaga (egy *hiba* az előre jelzett érték és a tényleges érték közötti különbséget).
- **Root mean-készlet négyzet hiba** (Gyökátlagos): Az eltérések átlaga a tesztelési adathalmazon végzett előrejelzések négyzetgyökét.
- **Relatív abszolút hiba**: Abszolút hibák tényleges értékek és az összes tényleges értékek átlaga közötti különbségek abszolút eltérésének átlaga.
- **Relatív négyzet hiba**: Viszonyítva eltéréseinek és az összes tényleges értékek átlaga a tényleges értékek átlaga.
- **Coefficient of Determination**: Más néven a **R-négyzet értéke**, ez a statisztikai mérőszám azt arról, hogy a modell illik az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb értékek azt jelzik, hogy az előrejelzés közelebb van a tényleges értékekhez. A **Coefficient of Determination** (determinációs együttható) értéke minél közelebb van az egyhez (1,0-hoz), annál pontosabb az előrejelzés.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy egyszerű kísérlet egy minta-adatkészletet használja. Ismerje meg a folyamat létrehozásának és üzembe helyezéséhez részletesebben olvashat róluk, folytassa a prediktív elemzési megoldást oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: A Studio prediktív megoldás fejlesztése](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
