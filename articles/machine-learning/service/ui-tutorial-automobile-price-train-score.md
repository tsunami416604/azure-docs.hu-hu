---
title: 'Oktatóanyag: az autó árának előrejelzése a vizuális felületen'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan lehet a gépi tanulási modellt kitanítani és üzembe helyezni egy drag and drop vizualizációs felület használatával. Ez az oktatóanyag egy kétrészes sorozat első része, amely az autók árának lineáris regresszió használatával történő előrejelzését ismerteti.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/09/2019
ms.openlocfilehash: b0c9fd85171020c9b78dc166980f85bcd89d8d67
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692319"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Oktatóanyag: az autó árának előrejelzése a vizuális felületen

Ebben a kétrészes oktatóanyagban megtudhatja, hogyan hozhat létre és helyezhet üzembe egy prediktív elemzési megoldást a Azure Machine Learning Visual Interface használatával, amely előre jelezheti a személygépkocsik árát. 

Az első részen beállíthatja a környezetét, húzhatja a modulokat egy interaktív vászonra, és összekapcsolhatja őket egy Azure Machine Learning folyamat létrehozásához.

Az oktatóanyag első részében a következőket sajátíthatja el:

> [!div class="checklist"]
> * Új folyamat létrehozása
> * Adatok importálása
> * Adatok előkészítése
> * Gépi tanulási modell betanítása
> * Gépi tanulási modell kiértékelése

Az oktatóanyag [második részében](ui-tutorial-automobile-price-deploy.md) megtudhatja, hogyan helyezheti üzembe a prediktív modelljét Azure-webszolgáltatásként, hogy az Ön által elküldött technikai specifikációk alapján előre megjósolja az autó árát. 

> [!Note]
>Az oktatóanyag befejezett verziója minta folyamatként érhető el.
>
>A kereséshez lépjen a **munkaterület vizuális felületéhez**. Az **új folyamat** szakaszban válassza az **1. minta – regresszió: autó árának előrejelzése (alapszintű)** lehetőséget.

## <a name="create-a-new-pipeline"></a>Új folyamat létrehozása

Azure Machine Learning folyamatok több, függő adatfeldolgozási lépést szerveznek egyetlen erőforrásba. A folyamatok segítségével összetett gépi tanulási munkafolyamatokat szervezheti, kezelheti és használhatja fel a projektek és a felhasználók számára. Azure Machine Learning folyamat létrehozásához Azure Machine Learning szolgáltatási munkaterületre van szükség. Ebből a szakaszból megtudhatja, hogyan hozhat létre mindkét erőforrást.

### <a name="create-a-new-workspace"></a>Új munkaterület létrehozása

Ha Azure Machine Learning munkaterülettel rendelkezik, ugorjon a következő szakaszra.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Jelentkezzen be a [ml.Azure.com](https://ml.azure.com) -be, és válassza ki a munkaterületet, amellyel dolgozni szeretne.

1. Válassza ki a **vizuális felületet**.

    ![Képernyőkép a vizuális munkaterület eléréséről, amely bemutatja, hogyan érheti el a vizuális felületet](./media/ui-tutorial-automobile-price-train-score/launch-visual-interface.png)

1. Válassza az **üres folyamat**elemet.

1. Válassza ki az alapértelmezett **"folyamat – létrehozva..."** nevű folyamatot a vászon tetején, és nevezze át valami értelmesre. Például: **"autó árának előrejelzése"** . A névnek nem kell egyedinek lennie.

## <a name="import-data"></a>Adatok importálása

A vizuális felületen több minta adatkészletet is megadhat, amelyekkel kísérletezhet. Ebben az oktatóanyagban használja a minta adatkészletet **(nyers)** . 

1. Az adatkészletek és modulok palettájának bal oldalán található. Válassza az **adatkészletek** lehetőséget, majd tekintse meg a **minták** szakaszt az elérhető minta-adatkészletek megtekintéséhez.

1. Válassza ki az adatkészletet, az **autó árát (RAW)** , majd húzza rá a vászonra.

   ![Az adathúzás vászonra](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

1. Válassza ki, hogy mely adatoszlopok működjenek együtt a szolgáltatásban. Írja be a **Kiválasztás elemet** a paletta tetején található keresőmezőbe, és keresse meg az **Oszlopok kiválasztása az adatkészlet** modulban.

1. Kattintson és húzza az **Oszlopok kiválasztása az adatkészlet** modulban a vászonra. Dobja el a modult az adatkészlet modul alatt.

1. A korábban hozzáadott adatkészlet összekapcsolásához kattintson a és a húzás elemre az **adatkészlet kijelölése oszlopban** . Húzza a mutatót az adatkészlet kimeneti portjáról, amely a vászonon található adatkészlet alján lévő kis kör, az **adatkészletben lévő oszlopok kiválasztása**bemeneti portjára, amely a modul tetején lévő kis kör.

    > [!TIP]
    > Az adatáramlás a folyamaton keresztül jön létre, amikor egy modul kimeneti portját egy másik bemeneti porthoz kapcsolja.
    >

    ![Modulok összekapcsolása](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Válassza az **Oszlopok kiválasztása az adatkészlet** modulban lehetőséget.

1. A vászontól jobbra található **Tulajdonságok** ablaktáblán válassza az **oszlop szerkesztése**lehetőséget.

    Az **Oszlopok kiválasztása** párbeszédpanelen válassza az **összes oszlop** lehetőséget, és adja meg az **összes funkciót**.

1. A jobb alsó sarokban kattintson a **Mentés** gombra az oszlop választójának bezárásához.

### <a name="run-the-pipeline"></a>A folyamat futtatása

Bármikor rákattinthat egy adatkészlet vagy modul kimeneti portjára, és megtekintheti, hogy az adat milyen módon néz ki az adatfolyamatban. Ha a **kimenetek** lap nem jelenik meg, először futtatnia kell a folyamatot.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="visualize-the-data"></a>Az adatok vizualizációja

Megjelenítheti az adatokat, hogy megértse a használni kívánt adatkészletet.

1. Válassza ki a **személygépkocsi-árlista (RAW)** modult.

1. A vászontól jobbra található **Tulajdonságok** ablaktáblán válassza a **kimenetek**lehetőséget.

1. Válassza ki a Graph ikont az adatmegjelenítéshez.

    ![Az adatok vizualizációja](./media/ui-tutorial-automobile-price-train-score/visualize-data.png)

1. Válassza ki az adatablak különböző oszlopait az egyes adatok megtekintéséhez.

    Minden sor egy személygépkocsit jelöl, és az egyes autókhoz társított változók oszlopként jelennek meg. Ebben az adatkészletben 205 sor és 26 oszlop található.

## <a name="prepare-data"></a>Adatok előkészítése

Az adatkészletek általában némi előfeldolgozást igényelnek az elemzés előtt. Előfordulhat, hogy néhány hiányzó értéket észlelt az adatkészlet megjelenítésekor. Ahhoz, hogy a modell elemezni tudja az adatokat, el kell távolítani a hiányzó értékeket. A hiányzó értékekkel rendelkező sorok törlődnek.

1. Írja be a **Kiválasztás elemet** a paletta tetején található keresőmezőbe, és keresse meg az **Oszlopok kiválasztása az adatkészlet** modulban.

1. Kattintson és húzza az **Oszlopok kiválasztása az adatkészlet** modulban a vászonra. Dobja el a modult az adatkészlet modul alatt.

1. A korábban hozzáadott adatkészlet összekapcsolásához kattintson a és a húzás elemre az **adatkészlet kijelölése oszlopban** . Húzza a mutatót az adatkészlet kimeneti portjáról, amely a vászonon található adatkészlet alján lévő kis kör, az **adatkészletben lévő oszlopok kiválasztása**bemeneti portjára, amely a modul tetején lévő kis kör.

    ![Modulok összekapcsolása](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Válassza az **Oszlopok kiválasztása az adatkészlet** modulban lehetőséget.

1. A vászontól jobbra található **Tulajdonságok** ablaktáblán válassza az **oszlop szerkesztése**lehetőséget.

    Az **Oszlopok kiválasztása** párbeszédpanelen válassza az **összes oszlop** lehetőséget, és adja meg az **összes funkciót**.

1. A jobb alsó sarokban kattintson a **Mentés** gombra az oszlop választójának bezárásához.

> [!TIP]
> A bemeneti adatokból hiányzó értékek tisztítása előfeltétel a legtöbb modul a vizualizációs felületen való használatához.

### <a name="remove-column"></a>Oszlop eltávolítása

A modellek betanításakor meg kell tennie valamit a hiányzó információkkal kapcsolatban. Ebben az adatkészletben a **normalizált veszteségek** oszlop nagy számú hiányzó értékkel rendelkezik, ezért a modellből teljes egészében ki kell zárnia ezt az oszlopot.

1. Válassza az **Oszlopok kiválasztása az adatkészlet** modulban lehetőséget.

1. A vászontól jobbra található **Tulajdonságok** ablaktáblán válassza a **Paraméterek**  > **oszlop szerkesztése**lehetőséget.

1. Új szabály hozzáadásához válassza ki a **+** .

1. A legördülő menüben válassza a **kizárás** és **oszlopnevek**lehetőséget.
    
1. Adja meg a **normalizált – veszteségeket** a szövegmezőbe.

1. A jobb alsó sarokban kattintson a **Mentés** gombra az oszlop választójának bezárásához.

    ![Oszlop kizárása](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    A Tulajdonságok panelen látható, hogy a **normalizált veszteségek** oszlop ki van zárva.

1. Válassza az **Oszlopok kiválasztása az adatkészlet** modulban lehetőséget. 

1. A **Tulajdonságok**területen válassza a **Paraméterek**  > **Megjegyzés** elemet, és írja be a "normalizált veszteségek kizárása" kifejezést.

### <a name="clean-missing-data"></a>Hiányzó adatértékek törlése

A **normalizált veszteségek** oszlop eltávolítása után az adatkészlet továbbra is hiányzó értékeket tartalmaz. A fennmaradó hiányzó adatelemet a **tiszta hiányzó** adatmodul használatával távolíthatja el.

1. A **tiszta hiányzó** adatmodul megtalálásához a **keresőmezőbe írja be a** törlés kifejezést.

1. Húzza a **tiszta hiányzó** adatmodult a folyamat vászonra, és kapcsolja össze az **adathalmaz-modul Select oszlopaival** . 

1. A Tulajdonságok panelen válassza a **teljes sor eltávolítása** a **tisztítási mód**alatt lehetőséget.

1. A Tulajdonságok ablaktáblában írja be a "hiányzó érték sorok eltávolítása" kifejezést. a **Megjegyzés** mezőbe.  

    A folyamatnak ekkor a következőhöz hasonlóan kell kinéznie:
    
    ![oszlop kijelölése](./media/ui-tutorial-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Gépi tanulási modell betanítása

Most, hogy az adatfeldolgozás előre feldolgozva van, prediktív modellt hozhat létre. Az adatait a modell betanításához fogja használni. Ezt követően tesztelje a modellt, hogy megtudja, milyen mértékben képes előre jelezni az árakat.

### <a name="select-an-algorithm"></a>Algoritmus kiválasztása

A **besorolás** és a **regresszió** két algoritmus, amelynek segítségével felügyelt gépi tanítás valósítható meg. A **besorolás** egy adott kategóriájú (például vörös, kék vagy zöld) kategóriából származó választ jósol meg. A **regresszió** egy szám megbecslésére szolgál.

Mivel előre jelezni szeretné az árat, ami egy szám, regressziós algoritmust is használhat. Ebben a példában egy lineáris regressziós modellt fog használni.

### <a name="split-the-data"></a>Az adatfelosztás

Az adatokat mind a modell betanításához, mind a teszteléshez használhatja két különálló adatkészletbe.

1. Az **Adatfelosztási** modul megkereséséhez írja be a **feldarabolt** adatelemet a keresőmezőbe, és kapcsolódjon a **tiszta hiányzó** adatmodul bal oldali portjához.

1. Válassza ki az **Adatfelosztási** modult.

1. A Tulajdonságok ablaktáblában állítsa az **első kimeneti adatkészletben lévő sorok töredékét** 0,7-re.

    Ez az adatmennyiség 70 százalékát osztja ki a modell betanításához, és 30%-ot tart vissza a teszteléshez.

1. A Tulajdonságok panelen írja be az "adathalmaz felosztása a betanítási készletbe (0.7) és a test set (0.3)" értéket. a **Megjegyzés** mezőbe.

### <a name="train-the-model"></a>A modell tanítása

A modell betanításához adjon meg egy olyan adathalmazt, amely tartalmazza az árat. A modell megkeresi az adatforrásokat, és összefüggéseket keres az autó funkciói és az ára között.

1. A tanulási algoritmus kiválasztásához törölje a modul-paletta keresési mezőjét.

1. **Machine learning algoritmusok**kibontása.
    
    Itt számos modulkategória közül választhat, amelyek segítségével inicializálható a gépi tanulási algoritmus.

1. Ehhez a folyamathoz válassza a **regresszió**  >  a**lineáris regresszió** lehetőséget, majd húzza a folyamat vászonra.

1. Keresse meg és húzza a **Train Model** modult a folyamat vászonra. 

1. Kapcsolja össze a lineáris regressziós modul kimenetét a Train Model modul bal oldali bemenetével.

1. Kapcsolja össze az **adatok felosztása** modul betanítási adat kimenetét (bal oldali port) a **Train Model** modul jobb oldali bemenetével.

    ![Képernyőfelvétel a Train Model modul helyes konfigurációjának megjelenítéséről. A lineáris regressziós modul a betanítási modell moduljának bal oldali portjához csatlakozik, és a felosztott adatmodul csatlakozik a betanítási modell jobb portjához.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-model.png)

1. Válassza ki a **Train Model** modult.

1. A Tulajdonságok ablaktáblán válassza az **oszlop szerkesztése** lehetőséget.

1. A **címke oszlop** párbeszédpanelen bontsa ki a legördülő menüt, és válassza az **oszlopnevek**lehetőséget. A szövegmezőbe írja be az **Ár**értéket. Az ár az az érték, amelyet a modell előre meg fog jósolni.

    A folyamatnak így kell kinéznie:

    ![Képernyőfelvétel: a folyamat helyes konfigurációjának megjelenítése a Train Model modul hozzáadása után.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Gépi tanulási modell kiértékelése

Most, hogy az adatok 70 százalékával lett kitanítva a modellre, használhatja az adatok további 30 százalékát, hogy megtudja, milyen jól működik a modellje.

1. Írja **be a pontszám Model** kifejezést a keresőmezőbe a **pontszám modell** modul megkereséséhez, majd húzza a modult a folyamat vászonra. 

1. A **Train Model** modul kimenetének összekötése a **score Model**bal oldali bemeneti portjával. Az **adatforráshoz** tartozó adat kimenetének (jobb oldali portjának) összekötése a **score Model**megfelelő bemeneti portjára.

1. A **kiértékelési modell** megkereséséhez **írja be a kiértékelés kifejezést** a keresőmezőbe, és húzza a modult a folyamat vászonra. 

1. A **pontszám modell** modul kimenetének összekötése a **modell kiértékelésének**bal oldali bemenetével. 

    Az utolsó folyamatnak a következőképpen kell kinéznie:

    ![A folyamat helyes konfigurációját ábrázoló képernyőfelvétel.](./media/ui-tutorial-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>A folyamat futtatása

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Eredmények megtekintése

A Futtatás után megtekintheti a folyamat futtatásának eredményét. 

1. Tekintse meg a **pontszám modell** modul kimenetét a **pontszám modell** modul kiválasztásával.

1. A **Tulajdonságok** ablaktáblán válassza a **kimenetek**  > **Megjelenítés**elemet. A modul megjeleníti az előre jelzett árat, valamint a tesztadatokból ismert tényleges értéket.

    ![Képernyőkép a kimeneti vizualizációról, kiemelve a "mutatós címke" oszlopot](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Tekintse meg a **modell kiértékelése** modul kimenetét a **pontszám modell** modul kiválasztásával.

1. A **Tulajdonságok** ablaktáblán válassza ki a **kimenet**  > **Megjelenítés**elemet, majd válassza a **Megjelenítés**lehetőséget.

A modellhez a következő statisztikák láthatók:

* **Átlagos abszolút hiba (Mae)** : az abszolút hibák átlaga (a hiba a várt érték és a tényleges érték közötti különbség).
* **Legfelső szintű, négyzetes hiba (gyökátlagos)** : a tesztelési adatkészleten végrehajtott jóslatok átlagának négyzetes gyökere.
* **Relative Absolute Error** (relatív abszolút eltérés): a tényleges értékek és az összes tényleges értékek átlaga közötti különbségek abszolút eltérésének átlaga.
* **Relative Squared Error** (relatív négyzetes eltérés): a négyzetes eltérések átlaga a tényleges értékek és az összes tényleges érték átlaga közötti különbség négyzetes értékéhez viszonyítva.
* **Meghatározási együttható**: az R négyzetes értékként is ismert, ez egy statisztikai metrika, amely azt jelzi, hogy a modell milyen jól illeszkedik az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb értékek azt jelzik, hogy az előrejelzés közelebb van a tényleges értékekhez. A meghatározási együttható esetében minél közelebb van az értéke egy (1,0), annál jobb az előrejelzések.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag első részében a következő lépéseket végezte el:

* Folyamat létrehozva
* Az adatfeldolgozás előkészítése
* A modell képzése
* A modell pontozása és kiértékelése

A második részből megtudhatja, hogyan helyezheti üzembe a modellt folyamat-végpontként.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezésének folytatása](ui-tutorial-automobile-price-deploy.md)
