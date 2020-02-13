---
title: 'Gyors útmutató: adatelemzési kísérlet létrehozása'
titleSuffix: ML Studio (classic) - Azure
description: Ez a Machine learning-útmutató végigvezeti egy egyszerű adatelemzési kísérleten. Egy regressziós algoritmus használatával fogjuk előre megbecsülni egy autó árát.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: ebde02d222502c58536269243343b21271f19269
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168870"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-azure-machine-learning-studio-classic"></a>Rövid útmutató: az első adatelemzési kísérlet létrehozása Azure Machine Learning Studio (klasszikus)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Ebben a rövid útmutatóban egy gépi tanulási kísérletet hoz létre [Azure Machine learning Studio (klasszikus)](what-is-ml-studio.md) , amely az autó árát különböző változók, például gyártmányok és technikai specifikációk alapján Jósolja meg.

Ha teljesen új a gépi tanuláshoz, a [kezdőknek szóló videó-adatelemzési](data-science-for-beginners-the-5-questions-data-science-answers.md) sorozat a mindennapi nyelvhasználattal és fogalmakkal remek bevezetést nyújt a gépi tanuláshoz.

Ez a rövid útmutató egy kísérlet alapértelmezett munkafolyamatát követi:

1. **Modell létrehozása**
    - [Az adatlekérdezés]
    - [Az adatgyűjtés előkészítése]
    - [Funkciók definiálása]
1. **A modell betanítása**
    - [Algoritmus kiválasztása és alkalmazása]
1. **A modell pontozása és tesztelése**
    - [Új személygépkocsik árának előrejelzése]

[Az adatlekérdezés]: #get-the-data
[Az adatgyűjtés előkészítése]: #prepare-the-data
[Funkciók definiálása]: #define-features
[Algoritmus kiválasztása és alkalmazása]: #choose-and-apply-an-algorithm
[Új személygépkocsik árának előrejelzése]: #predict-new-automobile-prices

## <a name="get-the-data"></a>Az adatok lekérése

Az első dolog, amire szüksége van a Machine learning szolgáltatásban.
A Studio (klasszikus) számos minta adatkészletet tartalmaz, amelyeket használhat, vagy több forrásból is importálhat adatokat. Ebben a példában a munkaterületén megtalálható **Automobile price data (Raw)** (Nyers autóáradatok) nevű mintahalmazt fogjuk használni.
Ebben az adathalmazban számos különböző autót bemutató bejegyzés szerepel. A bejegyzések számos adatot (például márka, típus, műszaki specifikációk, ár) tartalmaznak.

> [!TIP]
> Az [Azure AI katalógusban](https://gallery.azure.ai) megtalálja az alábbi kísérlet egy működő példányát. Nyissa meg az **[első adatelemzési kísérletet – az autó árának előrejelzését](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** , és kattintson a **Megnyitás a Studióban** lehetőségre a kísérlet másolatának letöltéséhez a Machine learning Studio (klasszikus) munkaterületre.

A következőképpen vonhatja be az adathalmazt a kísérletbe.

1. Hozzon létre egy új kísérletet az Machine Learning Studio (klasszikus) ablak alján található **+ új** elemre kattintva. Válassza a **kísérlet** >  az **üres kísérlet**lehetőséget.

1. A kísérlet kap egy alapértelmezett nevet, amelyet a vászon tetején láthat. Jelölje ki ezt a szöveget, és módosítsa valami értelmesebbre, például arra, hogy **Autó árának előrejelzése**. A névnek nem kell egyedinek lennie.

    ![A kísérlet átnevezése](./media/create-experiment/rename-experiment.png)

1. A kísérletvászontól balra az adathalmazokat és modulokat tartalmazó paletta látható. A paletta tetején található keresőmezőbe gépelje be, hogy **automobile**. A rendszer megjeleníti az **Automobile price data (Raw)** (Nyers autóáradatok) nevű adathalmazt. Húzza rá az adathalmazt a kísérletvászonra.

    ![Keresse meg az autó adatkészletét, és húzza rá a kísérlet vászonra](./media/create-experiment/type-automobile.png)

Ha szeretné megtekinteni az adatokat, kattintson az autó adatkészletének alján található kimeneti portra, majd válassza a **Megjelenítés**lehetőséget.

![Kattintson a kimeneti portra, és válassza a "Megjelenítés" lehetőséget.](./media/create-experiment/select-visualize.png)

> [!TIP]
> Az adathalmazok és modulok kis körökkel jelölt bemeneti és kimeneti portokkal rendelkeznek – a bemeneti portok felül, a kimeneti portok alul találhatók.
Az adatfolyam létrehozásához a kísérlet során össze fogja kötni az egyik modul kimeneti portját egy másik modul bemeneti portjával.
Ha meg szeretné tekinteni, hogyan jelennek meg az adatok az adatfolyam egy adott pontján, kattintson az adathalmaz vagy modul kimeneti portjára.

Ebben az adatkészletben minden sor egy automobilt jelöl, és az egyes autókhoz tartozó változók oszlopként jelennek meg. Az árat a jobb szélső oszlopban fogjuk megjósolni (26. oszlop, "Price" címmel) egy adott autó változóit használva.

![Az alapértékek megtekintése az adatvizualizációs ablakban](./media/create-experiment/visualize-auto-data.png)

A jobb felső sarokban látható „**x**” gombra kattintva zárja be a képi megjelenítési ablakot.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Az adathalmazok elemzése előtt általában némi előfeldolgozás szükséges. Talán észrevette, hogy az oszlopok számos sorából hiányoztak az értékek. Ahhoz, hogy a modell elemezni tudja az adatokat, el kell távolítani a hiányzó értékeket. Töröljük a hiányzó értékeket tartalmazó sorokat. A **normalized-losses** (normalizált veszteségek) című oszlopból ráadásul rendkívül sok érték hiányzik, ezért ezt az oszlopot teljesen kizárjuk a modellből.

> [!TIP]
> A legtöbb modul használatának előfeltétele a bemeneti adatok hiányzó értékeinek törlése.

Először hozzáadunk egy modult, amely teljesen eltávolítja a **normalizált veszteségek** oszlopot. Ezután hozzáadunk egy másik modult, amely eltávolítja a hiányzó adatsorokat.

1. A modul paletta tetején található keresőmezőbe írja be a Select Columns ( **Oszlopok kiválasztása** ) elemet, hogy megtalálja a [Select Columns elemet az adatkészlet][select-columns] modulban. Ezután húzza a kísérlet vászonra. Ezzel a modullal kiválaszthatjuk, hogy melyik adatoszlopokat szeretnénk bevonni a modellbe, vagy éppen kizárni a modellből.

1. Az **Automobile Price (nyers)** adathalmaz kimeneti portjának csatlakoztatása az adatkészlet Select oszlopainak bemeneti portjához.

    ![Adja hozzá az "Oszlopok kiválasztása az adatkészletben" modult a kísérleti vászonhoz, és kapcsolja össze](./media/create-experiment/type-select-columns.png)

1. Kattintson az [Oszlopok kiválasztása az adatkészlet][select-columns] -modulban elemre, majd a **Tulajdonságok** ablaktáblán kattintson az **oszlop kiválasztásának indítása** lehetőségre.

   - A bal oldalon kattintson a **With rules** (Szabályokkal) lehetőségre
   - A **Begin With** (Kezdés a következővel) területen kattintson az **All columns** (Minden oszlop) lehetőségre. Ezek a szabályok közvetlenül [kiválasztja az adatkészlet oszlopait az összes oszlop átadásához][select-columns] (kivéve a kizárni kívánt oszlopokat).
   - A legördülő listákból válassza az **Exclude** (Kizárás) és a **column names** (oszlopnevek) lehetőséget, majd kattintson a szövegmezőbe. Megjelenik az oszlopnevek listája. Válassza a **normalized-losses** (normalizált veszteségek) lehetőséget, amely aztán bekerül a szövegdobozba.
   - Kattintson a pipa (OK) gombra az oszlop választójának bezárásához (a jobb alsó sarokban).

     ![Az oszlop választójának elindítása és a "normalizált veszteségek" oszlop kizárása](./media/create-experiment/launch-column-selector.png)

     Ekkor a **Select Columns in Dataset** (Adathalmaz oszlopainak kijelölése) modul Properties (Tulajdonságok) panelje jelzi, hogy a modul a **normalized-losses** ( normalizált veszteségek) kivételével az adathalmaz összes oszlopát fel fogja dolgozni.

     ![A Tulajdonságok panelen látható, hogy a "normalizált veszteségek" oszlop ki van zárva](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. Ebben az esetben kattintson duplán az [Oszlopok kiválasztása az adatkészlet-][select-columns] modulban elemre, és írja be a "normalizált veszteségek kizárása" megjegyzést.

     ![Megjegyzés hozzáadásához kattintson duplán a modulra](./media/create-experiment/add-comment.png)

1. Húzza a [tiszta hiányzó adat][clean-missing-data] modult a kísérleti vászonra, és kapcsolja össze az [adathalmaz-modul Select oszlopaival][select-columns] . A **Properties** (Tulajdonságok) panel **Cleaning mode** (Törlés módja) beállításánál válassza a **Remove entire row** (Teljes sor eltávolítása) lehetőséget. Ezek a beállítások közvetlenül [törlik a hiányzó adatokból][clean-missing-data] az adatok törlését a hiányzó értékeket tartalmazó sorok eltávolításával. Kattintson duplán a modulra, és írja be a következő megjegyzést: „Hiányzó értéket tartalmazó sorok törlése”.

    ![A tisztítási mód beállítása "teljes sor eltávolítása" értékre a "hiányzó elemek törlése" modulhoz](./media/create-experiment/set-remove-entire-row.png)

1. A kísérlet futtatásához kattintson a lap alján található **RUN** (Futtatás) parancsra.

    A kísérlet befejezését követően az összes modulnál megjelenik egy zöld pipa, amely jelzi, hogy az adott modul sikeresen lefutott. A jobb felső sarokban pedig megjelenik a **Finished running** (Futtatás befejeződött) állapot.

    ![A kísérletnek a futtatása után a következőhöz hasonlónak kell lennie:](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Miért futtattuk a kísérletet most? A kísérlet futtatásával az adatkészletből származó adatokhoz tartozó oszlop-definíciók az adatkészletek [kiválasztása az adatkészlet][select-columns] modulban, a [tiszta hiányzó adat][clean-missing-data] modulon keresztül. Ez azt jelenti, hogy a [tiszta hiányzó adatokhoz][clean-missing-data] csatlakozó modulok is ugyanezeket az adatokat fogják tartalmazni.

Most már tiszta adattal rendelkezünk. Ha meg szeretné tekinteni a megtisztított adathalmazt, kattintson a [tiszta hiányzó adat][clean-missing-data] modul bal oldali kimeneti portjára, **és válassza a**vizualizáció lehetőséget. Láthatja, hogy a **normalized-losses** oszlop eltűnt, ahogy a hiányzó értékek is.

Most, hogy megtisztítottuk az adatokat, megadhatjuk, hogy mely jellemzőket szeretnénk felhasználni a prediktív modellben.

## <a name="define-features"></a>Funkciók definiálása

A gépi tanulásban a *jellemzők* azok a külön-külön mérhető tulajdonságok, amelyekre kíváncsiak vagyunk. Adathalmazunk minden sora egy-egy autót képvisel, az oszlopok pedig az autók különböző jellemzőit tartalmazzák.

A prediktív modellben használandó jellemzők helyes megválasztásához fontos a kísérletezés, illetve a megoldani kívánt probléma jó ismerete. Bizonyos jellemzők ugyanis hasznosabbak a cél előrejelzéséhez, mint mások. Egyes funkciók erős korrelációt mutatnak más funkciókkal, és eltávolíthatók. A példánkban például szorosan összefügg a city-mpg (fogyasztás városban) és highway-mpg (fogyasztás autópályán), ezért az egyiket eltávolíthatjuk anélkül, hogy lényegesen befolyásolnánk az előrejelzést.

Ideje, hogy létrehozzuk a modellt az adathalmaz jellemzőinek meghatározott részhalmaza alapján. Később visszatérhet ehhez a lépéshez, és más jellemzőket kiválasztva ismét lefuttathatja a kísérletet, ha kíváncsi rá, hogy úgy jobb eredményeket kap-e. Kezdésként azonban a következő funkciókat próbáljuk ki:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Húzzon egy másik [Select oszlopot az adatkészlet][select-columns] modulban a kísérlet vászonra. A [tiszta hiányzó][clean-missing-data] adatmodul bal oldali kimeneti portját a [Select Columns (adatkészlet][select-columns] ) modulban lévő oszlopok bemenetéhez kapcsolja.

    ![Az "Oszlopok kiválasztása az adatkészletben" modul "a hiányzó adat törlése" moduljának csatlakoztatása](./media/create-experiment/connect-clean-to-select.png)

1. Kattintson duplán a modulra, és írja be: „Az előrejelzéshez használatos jellemzők kiválasztása”.

1. Kattintson a **Properties** (Tulajdonságok) panel **Launch column selector** (Oszlopválasztó indítása) elemére.

1. Kattintson a **With rules** (Szabályokkal) lehetőségre.

1. A **Begin With** (Kezdés a következővel) területen kattintson a **No columns** (Egyetlen oszlop sem) lehetőségre. A szűrősorban válassza ki az **Include** (Belefoglalás) és a **column names** (oszlopnevek) lehetőséget, és jelölje ki az oszlopnevek listáját a szövegmezőben. Ez a szűrő arra utasítja a modult, hogy ne továbbítson semmilyen oszlopot (funkciót), kivéve a megadott beállításokat.

1. Kattintson a pipa (OK) gombra.

    ![Válassza ki az előrejelzésbe felvenni kívánt oszlopokat (szolgáltatásokat)](./media/create-experiment/select-columns-to-include.png)

Ez a modul olyan szűrt adatkészletet hoz létre, amely csak azokat a funkciókat tartalmazza, amelyeket a következő lépésben fogunk használni. Később visszatérhet ide, és más jellemzőkkel is elvégezheti az előrejelzést.

## <a name="choose-and-apply-an-algorithm"></a>Algoritmus kiválasztása és alkalmazása

Most, hogy előkészítettük az adatokat, a prediktív modell létrehozásához már csak a tanítás és a tesztelés szükséges. A következőkben az adatok segítségével elvégezzük a modell betanítását, majd a modell tesztelésével megállapítjuk, hogy milyen pontossággal képes előre jelezni az árakat.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

A *besorolás* és a *regresszió* két algoritmus, amelynek segítségével felügyelt gépi tanítás valósítható meg. Besoroláskor a válaszok előrejelzése megadott kategóriakészletből történik (például: színek (vörös, kék vagy zöld)). A rendszer a számok előrejelzésére regressziós módszert használ.

Mivel az árat szeretnénk előre jelezni, ami egy szám, regressziós algoritmust fogunk használni. Ebben a példában egy *lineáris regressziós* modellt fogunk használni.


A modell betanításához az árat tartalmazó adathalmazt biztosítunk számára. A modell megvizsgálja adatokat, és összefüggéseket keres az autó tulajdonságai és az ára között. Ezután teszteljük a modellt. Ehhez olyan autók tulajdonságkészletét töltjük be, amelyeket ismerünk, és megnézzük, hogy mennyire sikeresen tudja a modell előre jelezni az ismert árakat.

Az adatok a modell betanítására és tesztelésére is használhatók. Ehhez két halmazra, egy tanítási és egy tesztelési halmazra osztjuk fel az adatokat.

1. Jelölje ki és húzza az [Adatfelosztási][split] modult a kísérleti vászonra, és kapcsolja össze az adatkészlet modul utolsó [Select oszlopával][select-columns] .

1. Kattintson az [Adatfelosztási][split] modulra a kiválasztásához. Keresse meg a **Properties** (Tulajdonságok) panelen a vászontól jobbra a **Fraction of rows in the first output dataset** (Sorok hányadosa az első kimeneti adathalmazban) beállítást, és adja meg a 0,75 értéket. Így az adatok 75 százalékát a modell betanítására, 25 százalékát pedig a modell tesztelésére használhatjuk.

    ![Az "adatfelosztás" modul felosztott részének beállítása 0,75-re](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > A **Random seed** (Véletlenszám-generálás kezdőértéke) paraméter módosításával különböző véletlenszerűen kiválasztott mintákat hozhat létre, amelyeket szintén felhasználhat a modell betanítására és tesztelésére. Ez a paraméter szabályozza a pszeudo-véletlenszám-generátor kezdőértékét.

1. Futtassa a kísérletet. A kísérlet futtatásakor az [adatkészletben lévő oszlopok kiválasztása][select-columns] és az [adategységek felosztása][split] oszlop-definíciók átadása a következőként felvenni kívánt modulokra.  

1. A tanulási algoritmus kiválasztásához bontsa ki a vászontól balra, a modulpalettán található **Machine Learning** (Gépi tanulás) kategóriát, majd bontsa ki az **Initialize Model** (Inicializálási modell) kategóriát is. Itt számos modulkategória közül választhat, amelyek segítségével inicializálható a gépi tanulási algoritmus. Ehhez a kísérlethez válassza a **regresszió** kategóriába tartozó [lineáris regressziós][linear-regression] modult, majd húzza a kísérlet vászonra. (A modult úgy is megkeresheti, ha a paletta keresőmezőjébe beírja a „linear regression” kifejezést.)

1. Keresse meg és húzza a [Train Model][train-model] modult a kísérleti vászonra. Kapcsolja össze a [lineáris regressziós][linear-regression] modul kimenetét a [Train Model][train-model] modul bal oldali bemenetével, és kapcsolja össze az adategységek [kiosztási][split] moduljának betanítási adat kimenetét (bal oldali port) a [Train Model][train-model] modul jobb oldali bemenetével.

    ![A "Train Model" modul összekötése a "lineáris regresszió" és az "osztott adategységek" moduljaival](./media/create-experiment/connect-train-model.png)

1. Kattintson a [betanítási modell][train-model] modulra, majd a **Tulajdonságok** ablaktáblán található **Oszlop kiválasztása** lehetőségre, majd válassza ki a **Price (ár** ) oszlopot. Az **Ár** az az érték, amelyet a modellünk előre fog jelezni.

    Jelölje ki a **price** (ár) oszlopot az oszlopválasztóban. Ehhez helyezze át az **Available columns** (Elérhető oszlopok) listáról a **Selected columns** (Kiválasztott oszlopok) listára.

    ![Válassza ki a "Train Model" modul Price (ár) oszlopát.](./media/create-experiment/select-price-column.png)

1. Futtassa a kísérletet.

Ezzel kapunk egy betanított regressziós modellt, amely képes pontszámot rendelni az új autóadatokhoz, és így előre jelezni az árakat.

![A kísérletnek ekkor a következőhöz hasonlóan kell kinéznie:](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Új személygépkocsik árának előrejelzése

Most, hogy adataink 75 százalékával betanítottuk a modellt, a maradék 25 százalék pontozásával megállapíthatjuk, hogy mennyire működik jól.

1. Keresse meg és húzza a [pontszám modell][score-model] modult a kísérleti vászonra. A [Train Model][train-model] modul kimenetének összekötése a [score Model][score-model]bal oldali bemeneti portjával. Az [adatforráshoz][split] tartozó adat kimenetének (jobb oldali portjának) összekötése a [score Model][score-model]megfelelő bemeneti portjára.

    ![A "pontszám modell" modul összekötése a "Train Model" és az "osztott adategység" modulok használatával](./media/create-experiment/connect-score-model.png)

1. Futtassa a kísérletet, és tekintse meg a [pontszám modell][score-model] modul kimenetét úgy, hogy a [pontszám modell][score-model] kimeneti portjára kattint, **és kiválasztja a**vizualizáció lehetőséget. A modul megjeleníti az előre jelzett árat, valamint a tesztadatokból ismert tényleges értéket.  

    ![A "pontszám modell" modul kimenete](./media/create-experiment/score-model-output.png)

1. Végül teszteljük az eredmény minőségét. Jelölje ki és húzza a [modell kiértékelése][evaluate-model] modult a kísérleti vászonra, és a [pontszám modell][score-model] modul kimenetét a [modell kiértékelésének][evaluate-model]bal oldali bemenetéhez kapcsolja. Az elkészült kísérletnek a következőképpen kell kinéznie:

    ![A végső kísérlet](./media/create-experiment/complete-linear-regression-experiment.png)

1. Futtassa a kísérletet.

A [modell kiértékelése][evaluate-model] modul kimenetének megtekintéséhez kattintson a kimeneti portra, majd válassza a **Megjelenítés**lehetőséget.

![A kísérlet kiértékelésének eredményei](./media/create-experiment/evaluation-results.png)

A következő statisztikák tekinthetők meg:

- **Mean Absolute Error** (átlagos abszolút eltérés, MAE): az abszolút eltérések átlaga (*eltérésnek* az előre jelzett érték és a tényleges érték közötti különbséget nevezzük).
- **Root Mean Squared Error** (gyökátlagos négyzetes eltérés, RMSE): a tesztelési adathalmazon végzett előrejelzések eltéréseinek négyzetéből számított átlag négyzetgyöke.
- **Relative Absolute Error** (relatív abszolút eltérés): a tényleges értékek és az összes tényleges értékek átlaga közötti különbségek abszolút eltérésének átlaga.
- **Relative Squared Error** (relatív négyzetes eltérés): a négyzetes eltérések átlaga a tényleges értékek és az összes tényleges érték átlaga közötti különbség négyzetes értékéhez viszonyítva.
- **Coefficient of Determination** (determinációs együttható): ez az **R-négyzet értéke** néven is ismert statisztikai mérőszám azt mutatja, hogy a modell mennyire illik az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb értékek azt jelzik, hogy az előrejelzés közelebb van a tényleges értékekhez. A **Coefficient of Determination** (determinációs együttható) értéke minél közelebb van az egyhez (1,0-hoz), annál pontosabb az előrejelzés.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy egyszerű kísérletet egy minta adatkészlet használatával. Ha szeretné megvizsgálni, hogyan hozhat létre és helyezhet üzembe egy modellt részletesebben, folytassa a prediktív megoldás oktatóanyagával.

> [!div class="nextstepaction"]
> [Oktatóanyag: prediktív megoldás fejlesztése a Studióban (klasszikus)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
