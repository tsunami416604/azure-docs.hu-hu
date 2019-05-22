---
title: 'Oktatóanyag: Vizuális felhasználói felülettel autó árát előrejelzése'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan betanításához pontszám, és üzembe helyezése egy gépi tanulási modellt, fogd és vidd vizuális felületen. Ebben az oktatóanyagban egy kétrészes sorozat első részében a lineáris regressziós autó árának előrejelzése.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 52b5291e4b56b5065b9dddd5b8908ade0c1a8387
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "66019842"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Oktatóanyag: Vizuális felhasználói felülettel autó árát előrejelzése

Ebben az oktatóanyagban azt tekintjük át az Azure Machine Learning szolgáltatás vizuális felületen prediktív megoldás fejlesztése igénybe vehet. Ez az oktatóanyag végére rendelkezni fog egy megoldás, amely előre meg tudjuk bármely küldje el a műszaki specifikációk alapján autó árát.

Ebben az oktatóanyagban [továbbra is a gyorsindítóból](ui-quickstart-run-experiment.md) és **kétrészes oktatóanyag-sorozat része**. Azonban nem kell, végezze el a rövid útmutató elindítása előtt.

Az oktatóanyag-sorozat része ismerteti, hogyan lehet:

> [!div class="checklist"]
> * Importálás és az adatok tisztítása (ugyanazokat a lépéseket a rövid útmutató)
> * A gépi tanulási modell betanítása
> * Pontszám és a egy modell értékelése

A [második](ui-tutorial-automobile-price-deploy.md) , az oktatóanyag-sorozatban megtudhatja, hogyan a prediktív modell egy Azure-webszolgáltatásként üzembe.

> [!NOTE]
> Ebben az oktatóanyagban a befejezett verzióját, a mintakísérlet érhető el.
> A kísérletek lapon lépjen a **új hozzáadása** > **minta 1 – regressziós: Autó árát Prediction(Basic)**


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Ha rendelkezik egy Azure Machine Learning szolgáltatás munkaterületén, ugorjon a [következő szakasz](#open-the-visual-interface-webpage). Ellenkező esetben hozzon létre egyet most.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Nyissa meg a vizuális felhasználói felületet weblapra

1. Nyissa meg a munkaterület a [az Azure portal](https://portal.azure.com/).  

1. A munkaterületen válassza ki a **vizuális felhasználói felületet**.  Válassza ki **indítási vizuális felhasználói felületet**.  

    ![Az Azure portal eléréséhez a vizuális felhasználói felületet egy Machine Learning szolgáltatás munkaterületről bemutató képernyőkép](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    A felület weblap nyílik meg egy új böngészőlap.  

## <a name="import-and-clean-your-data"></a>Az adatok importálása és

Először is szüksége az adatok tisztítása. Ha ez a rövid útmutató elvégezte, újból felhasználhatja az adat-előkészítési kísérlet itt. Ha ez a rövid útmutató még nem végezte el, hagyja ki a következő szakaszban, és [kezdhet egy új kísérlet](#start-from-a-new-experiment).

### <a name="reuse-the-quickstart-experiment"></a>Újból felhasználhatja a rövid útmutató kísérlet

1. Nyissa meg a rövid útmutató kísérlet során.

1. Válassza ki **Mentés másként** az ablak alján.

1. Adjon meg egy új nevet a megjelenő előugró párbeszédpanelen.

    ![Képernyőfelvétel: a "Oktatóanyag – előrejelzése Automobile Price" kísérlet átnevezése](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. A kísérlet kell kinéznie ehhez hasonló:

    ![A kísérlet várható állapotát megjelenítő képernyőkép. Az autókat tartalmazó adathalmaz csatlakozik a Select Columns modul, amely kapcsolódik a Clean Missing Data](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

Ha sikeresen újra felhasználhatók a rövid útmutató kísérletét, hagyja ki a következő szakaszban a kezdéshez [a modell tanítása](#train-the-model).

### <a name="start-from-a-new-experiment"></a>Indítsa el az új kísérlet

Ha ez a rövid útmutató nem fejeződött be, kövesse az alábbi lépéseket gyorsan létrehozhat egy új kísérlet, amely importálja, és törli az autókat tartalmazó adathalmaz.

1. Új kísérlet létrehozása kiválasztásával **+ új** a vizuális felhasználói felületet ablak alján.

1. Válassza ki **kísérlet** >  **üres kísérlet**.

1. Válassza ki a kísérlet alapértelmezett nevét **"végzett kísérletezést létrehozott az...** "a vászon tetején, és módosítsa valami értelmesebbre. Ha például **autó árának előrejelzése**. A névnek nem kell egyedinek lennie.

1. A kísérletvászontól balra az adathalmazokat és modulokat tartalmazó paletta látható. Modulok megkereséséhez használja a keresőmezőt a modulpaletta tetején. Típus **autó** a keresőmezőbe keresése nevű adathalmazt **Automobile price data (Raw)**. Húzza rá az adathalmazt a kísérletvászonra.

    ![Képernyőkép az autó árát adathalmaz megkeresése](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    Most, hogy az adatok, hozzáadhat egy modult, amely eltávolítja a **normalized-losses** oszlopot teljesen. Ezután adjon hozzá egy másik modul, amely eltávolítja az összes sort, amelyből adatok hiányoznak.

1. Típus **Oszlopválasztás** a keresőmezőbe keresése a **Select Columns in Dataset** modul. Húzza a kísérletvászonra. Ez a modul lehetővé teszi, hogy válassza ki, melyik adatoszlopokat vagy kizárja a modellben.

1. A kimeneti portjára, csatlakozzon a **Automobile price data (Raw)** adatkészlet a Select Columns in Dataset bemeneti portjával.

    ![Animált gif: hogyan kell a Automobile Price Data modul összekötése a Select Columns modul](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Válassza ki a Select-Columns adatkészlet modulban, majd **Oszlopválasztás indítása** a a **tulajdonságok** ablaktáblán.

   1. A bal oldalon válassza ki a **szabályokkal**

   1. A **megkezdése a**válassza **minden oszlop**. Ezek a szabályok közvetlen **Select Columns in Dataset** (kivéve azokat fogunk kizárása) az összes oszlopot.

   1. A legördülő listákból válassza **kizárása** és **oszlopnevek**, majd írja be **normalized-losses** a szövegdobozba.

   1. Válassza ki az OK gombra kattintva zárja be az Oszlopválasztó (az a jobb alsó sarokban).

     Ekkor a **Select Columns in Dataset** (Adathalmaz oszlopainak kijelölése) modul Properties (Tulajdonságok) panelje jelzi, hogy a modul a **normalized-losses** ( normalizált veszteségek) kivételével az adathalmaz összes oszlopát fel fogja dolgozni.

1. A Megjegyzés hozzáadása a **Select Columns in Dataset** modult. Ehhez kattintson duplán a modult, és írja be az "Exclude normalized losses.". Ez segíthet, egy pillantással, a modul mire a kísérlet során.

    ![Képernyőkép – bemutató helyes-e a modul konfigurációját, oszlopok kiválasztása](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. Típus **tisztaként** a keresőmezőbe keresése a **Clean Missing Data** modul. Húzza a **Clean Missing Data** modult a kísérletvászonra, és kösse össze a **Select Columns in Dataset** modul.

1. A **Properties** (Tulajdonságok) panel **Cleaning mode** (Törlés módja) beállításánál válassza a **Remove entire row** (Teljes sor eltávolítása) lehetőséget. Ezeket a beállításokat a közvetlen **Clean Missing Data** , amely tartalmaz a hiányzó sort törölje. Kattintson duplán a modulra, és írja be a következő megjegyzést: „Hiányzó értéket tartalmazó sorok törlése”.

![Képernyőkép – bemutató helyes-e a modul konfigurációját, Clean Missing Data](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

## <a name="train-the-model"></a>A modell betanítása

Most, hogy az adatok készen áll, akkor egy prediktív modellt hozhatnak létre. A modell betanításához az adatokat fogja használni. Ezután tesztelni fogjuk a modell tesztelésével megállapítjuk hogy milyen pontossággal képes előre jelezni az árakat.

A **besorolás** és a **regresszió** két algoritmus, amelynek segítségével felügyelt gépi tanítás valósítható meg. **Besorolási** előrejelzi a kategóriák, például különböző színeken (piros, kék vagy zöld) meghatározott készletéből választ. **Regresszió** számok előrejelzésére szolgál.

Mivel a díj, ami egy szám, előre jelezni kívánt használhatja egy regressziós algoritmus. Ebben a példában egy lineáris regressziós modellt fog használni.

A modell betanítását, így azt egy adatkészletet, amely az ár tartalmazza. A modell megvizsgálja az adatokat, és a egy autó tulajdonságai és az ára közötti összefüggéseket keres. Ezután teszteli a modellt, így azt szolgáltatások tulajdonságkészletét, hogy tisztában van-e, és tekintse meg, hogyan zárja be a modell előre jelezni az ismert árakat.

Használja az adatok a modell tanítása és tesztelésére is az adatok egy tanítási és egy tesztelési halmazra is.

1. Típus **adatok** a keresőmezőbe keresése a **Split Data** modul, és csatlakoztassa a bal oldali portjával a **Clean Missing Data** modul.

1. Válassza ki a **Split Data** modul imént csatlakozott a kiválasztásához. A Tulajdonságok panelen beállítása az első kimeneti adatkészletnél a sorok re 0,7. Ezzel a módszerrel azt fogja használatával 70 százalék pontozásával betanítja a modellt, valamint tesztelési 30 százalékát.

    ![Képernyőfelvétel: a Tulajdonságok panelen helyes konfigurációjával. A "Split Data" értékeket, érdemes lehet "Felosztási sorok", 0,7, véletlenszerű osztott, 0, False.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Kattintson duplán a **Split Data** , és írja be a "Ossza fel az adatkészlet set(0.7) és tesztelési set(0.3) képzési" Megjegyzés

1. A tanulási algoritmus kiválasztásához, törölje a modul paletta keresőmezőjébe.

1. Bontsa ki a **Machine Learning** majd **modell inicializálása**. Itt számos modulkategória közül választhat, amelyek segítségével inicializálható a gépi tanulási algoritmus.

1. Ehhez a kísérlethez válassza **regressziós** > **lineáris regressziós** , és húzza a kísérletvászonra.

    ![Képernyőfelvétel: a Tulajdonságok panelen helyes konfigurációjával. A "Split Data" értékeket, érdemes lehet "Felosztási sorok", 0,7, véletlenszerű osztott, 0, False.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Keresse meg és húzza a **tanítási modell** modult a kísérletvászonra. A lineáris regressziós modul kimenete csatlakozás a tanítási modell modulhoz bal oldali bemenetével, és csatlakozás a tanítási adatokat tartalmazó kimenetével (bal oldali port), a **Split Data** modul jobb oldali bemenetével a **tanítási modell**modul.

    ![Képernyőfelvétel: a tanítási modell modulhoz helyes konfigurációjával. A lineáris regressziós modul bal oldali port a tanítási modell modulhoz csatlakozik, és a felosztás adatmodul kapcsolódik a megfelelő port a tanítási modell](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Válassza ki a **tanítási modell** modul. A Tulajdonságok panelen válassza ki a Oszlopválasztás indítása, és írja be **ár** melletti **oszlopneveket tartalmaznak**. A szolgáltatás díja, amelyet a modell előre jelezni az érték

    ![Képernyőfelvétel: a megfelelő konfiguráció column selector modul. A szabályok > oszlopneveket tartalmaznak > "price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    A kísérletet most hasonlóan kell kinéznie.
    ![Képernyőfelvétel: a megfelelő konfigurációt a tanítási modell modulhoz hozzáadása után a kísérlet.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>A tanítási kísérlet futtatása

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Pontszám, és a modell értékelése

Most, hogy a modell segítségével az adatok a 70 %-os már betanított, ezzel pontszámot rendelni az más 30 százalék pontozásával megállapíthatjuk arról, hogy a modell függvények.

1. Típus **pontszám modell** a keresőmezőbe keresése a **Score Model** modul, és húzza a modult a kísérletvászonra. Csatlakozzon a kimenetét a **tanítási modell** modul bal oldali bemeneti portjával **Score Model**. A tesztelési adatokat tartalmazó kimenetét (jobb oldali portját) a csatlakozás a **Split Data** modul jobb oldali bemeneti portjával **Score Model**.

1. Típus **kiértékelése** a keresőmezőbe keresése a **Evaluate Model** , és húzza az informatikai modult a kísérletvászonra. Csatlakozzon a kimenetét a **Score Model** modul bal oldali bemenetével **Evaluate Model**. Az elkészült kísérletnek a következőképpen kell kinéznie:

    ![Képernyőfelvétel: a kísérletet az utolsó konfigurációját.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Futtassa a kísérletet, az azonos számítási célnak a korábban használt használatával.

1. Eredményének megtekintéséhez a **Score Model** modul kimeneti portjával kiválasztásával **Score Model** válassza **Visualize**. A modul megjeleníti az előre jelzett árat, valamint a tesztadatokból ismert tényleges értéket.

    ![Képernyőkép a kimeneti képi megjelenítés, a "Pontozását címke" oszlop kiemelése](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. A modell kiértékelése modul eredményének megtekintéséhez válassza ki a kimeneti portra, és válassza ki a képi megjelenítés opcióra.

    ![Képernyőkép az elkészült kísérletnek a következőképpen a kiértékelésének eredménye.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

A modell a következő statisztikai adatok jelennek meg:

* **Mean Absolute Error (MAE)**: A átlaga (hiba az előre jelzett érték és a tényleges érték közötti különbséget).
* **Root Mean-hiba (Gyökátlagos) négyzet**: Az eltérések átlaga a tesztelési adathalmazon végzett előrejelzések négyzetgyökét.
* **Relatív abszolút hiba**: Abszolút hibák tényleges értékek és az összes tényleges értékek átlaga közötti különbségek abszolút eltérésének átlaga.
* **Relatív négyzet hiba**: Viszonyítva eltéréseinek és az összes tényleges értékek átlaga a tényleges értékek átlaga.
* **Coefficient of Determination**: Más néven az R-négyzet értéke, ez a statisztikai mérőszám azt arról, hogy a modell illik az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb értékek azt jelzik, hogy az előrejelzés közelebb van a tényleges értékekhez. Coefficient of Determination, minél közelebb a értéke egy (1.0), annál pontosabb az előrejelzés.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Az Azure Machine Learning szolgáltatás munkaterületen kísérletek kezelése

A kísérletek hoz létre a vizuális felületen kezelheti az Azure Machine Learning szolgáltatás munkaterületről. A munkaterület segítségével például a felhasználók kísérlet fut, a diagnosztikai naplók, végrehajtási diagramokat és egyéb további részletes információk megjelenítéséhez.

1. Nyissa meg a munkaterület a [az Azure portal](https://portal.azure.com/).  

1. A munkaterületen válassza ki a **kísérletek**. Ezután válassza ki a létrehozott kísérlet.

    ![Képernyőfelvétel: hogyan navigálhat a kísérleteket az Azure Portalon](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    Ezen az oldalon látni fogja a kísérletet, és a legújabb futtatások áttekintése.

    ![Kísérlet statisztikák az Azure Portal képernyőképe ábrázoló áttekintése](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Válassza ki a Futtatás száma egy adott végrehajtás részletes adatainak megtekintéséhez.

    ![Képernyőkép – részletes futtassa a jelentést](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Futtassa a jelentést valós időben frissül. Ha egy **Python-szkript végrehajtására** modul a kísérletben, megadhatja a kimeneti szkriptek naplói a **naplók** fülre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Az első rész a jelen oktatóanyag elvégezte ezeket a lépéseket:

* A kísérlet, a rövid útmutatóban létrehozott újrafelhasználása
* Az adatok előkészítése
* A modell betanítása
* Pontszám, és a modell értékelése

A második részben megismerheti, hogyan való üzembe helyezését az Azure-webszolgáltatásként fogja.

> [!div class="nextstepaction"]
> [Folytassa a modellek üzembe helyezéséhez](ui-tutorial-automobile-price-deploy.md)
