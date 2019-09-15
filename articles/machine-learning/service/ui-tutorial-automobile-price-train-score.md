---
title: 'Oktatóanyag: Az autó árának előrejelzése a vizuális felületen'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan lehet a gépi tanulási modellt kitanítani és üzembe helyezni egy drag and drop vizualizációs felület használatával. Ez az oktatóanyag egy kétrészes sorozat első része, amely az autók árának lineáris regresszió használatával történő előrejelzését ismerteti.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 08/16/2019
ms.openlocfilehash: 11c65c217ef6c150c47f387f7f80070488a8df89
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996777"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Oktatóanyag: Az autó árának előrejelzése a vizuális felületen

Ebben a kétrészes oktatóanyagban megtudhatja, hogyan használhatja a Azure Machine Learning vizuális felületét olyan prediktív elemzési megoldások fejlesztésére és üzembe helyezésére, amelyek előre jelezik a személygépkocsik árát. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

Az első részen beállíthatja a környezetét, az adatkészleteket és az elemzési modulokat egy interaktív vászonra, és összekapcsolhatja őket egy kísérlet létrehozásához. 

Az oktatóanyag első részében a következőket sajátíthatja el:

> [!div class="checklist"]
> * Új kísérlet létrehozása
> * Adatok importálása
> * Adatok előkészítése
> * Gépi tanulási modell betanítása
> * Gépi tanulási modell kiértékelése

Az oktatóanyag [második részében](ui-tutorial-automobile-price-deploy.md) megtudhatja, hogyan helyezheti üzembe a prediktív modellt Azure-webszolgáltatásként, hogy az Ön által elküldött technikai specifikációk alapján előre megjósolja az autó árát. 

Az oktatóanyag befejezett verziója minta kísérletként érhető el.

A **kísérletek lapon**válassza az **új hozzáadása**lehetőséget, majd válassza ki az **1. mintát – regresszió: Automobile Price előrejelzés (alapszintű)** kísérlet.

## <a name="create-a-new-experiment"></a>Új kísérlet létrehozása

Visual Interface-kísérlet létrehozásához először Azure Machine Learning szolgáltatás-munkaterületre van szükség. Ebből a szakaszból megtudhatja, hogyan hozhat létre mindkét erőforrást.

### <a name="create-a-new-workspace"></a>Új munkaterület létrehozása

Ha Azure Machine Learning munkaterülettel rendelkezik, ugorjon a következő szakaszra.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-an-experiment"></a>Kísérlet létrehozása

1. Nyissa meg a munkaterületet a [Azure Portalban](https://portal.azure.com/).

1. A munkaterületen válassza a **vizuális felület**lehetőséget. Ezután válassza a **Visual Interface indítása**lehetőséget. 

    ![Képernyőkép a Azure Portalről, amely bemutatja, hogyan érheti el a vizuális felületet egy Machine Learning szolgáltatás munkaterületről](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

1. Hozzon létre egy új kísérletet a vizualizációs felület ablakának alján található **+ új** lehetőség kiválasztásával.

1. Válassza az **üres kísérlet**lehetőséget.

1. Válassza ki a vászon tetején található **"kísérlet létrehozva..** ." nevű alapértelmezett kísérlet nevét, és nevezze át valami értelmesre. Például: **"autó árának előrejelzése"** . A névnek nem kell egyedinek lennie.

## <a name="import-data"></a>Adatok importálása

A gépi tanulás az adatoktól függ. Szerencsére a kezelőfelületen több minta adatkészletet is elérhetővé tehet a kipróbáláshoz. Ebben az oktatóanyagban használja a minta adatkészletet **(nyers)** . 

1. A kísérletvászontól balra az adathalmazokat és modulokat tartalmazó paletta látható. Válassza a **mentett** adatkészletek lehetőséget, majd válassza a **minták** lehetőséget az elérhető minta-adatkészletek megtekintéséhez.

1. Válassza ki az adatkészletet, az **autó árát (RAW)** , majd húzza rá a vászonra.

   ![Az adathúzás vászonra](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

1. Válassza ki, hogy mely adatoszlopok működjenek együtt a szolgáltatásban. Írja be a **Kiválasztás elemet** a paletta tetején található keresőmezőbe, és keresse meg az **Oszlopok kiválasztása az adatkészlet** modulban.

1. Kattintson és húzza az **Oszlopok kiválasztása az adatkészlet** modulban a vászonra. Dobja el a modult az adatkészlet modul alatt.

1. A korábban hozzáadott adatkészlet összekapcsolásához kattintson a és a húzás elemre az **adatkészlet kijelölése oszlopban** . Húzza az adathalmaz kimeneti portjáról, amely a vászonon található adatkészlet alján lévő kis kör, egészen az adatkészletben lévő **oszlopok**kiválasztásának bemeneti portjához, amely a modul tetején lévő kis kör.

    > [!TIP]
    > A kísérlet során adatáramlást hozhat létre, ha egy modul kimeneti portját egy másik bemeneti porthoz kapcsolja.
    >

    ![Modulok összekapcsolása](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    A piros felkiáltójel azt jelzi, hogy még nem állította be a modul tulajdonságait.

1. Válassza az **Oszlopok kiválasztása az adatkészlet** modulban lehetőséget.

1. A vászontól jobbra található **Tulajdonságok** ablaktáblán válassza az **Oszlopok szerkesztése**lehetőséget.

    Az **Oszlopok kiválasztása** párbeszédpanelen válassza az **összes oszlop** lehetőséget, és adja meg az **összes funkciót**. A párbeszédpanelnek így kell kinéznie:

     ![oszlop – választó](./media/ui-tutorial-automobile-price-train-score/select-all.gif)

1. A jobb alsó sarokban kattintson az **OK** gombra az oszlop választójának bezárásához.

### <a name="run-the-experiment"></a>Kísérlet futtatása

Bármikor rákattinthat egy adatkészlet vagy modul kimeneti portjára, és megtekintheti, hogy az adat milyen módon néz ki az adatfolyamatban. Ha a **Megjelenítés** lehetőség le van tiltva, először futtatnia kell a kísérletet.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

A számítási cél rendelkezésre állása után a kísérlet lefut. Ha a Futtatás befejeződött, egy zöld pipa jelenik meg minden modulon.


### <a name="visualize-the-data"></a>Az adatok vizualizációja

Most, hogy futtatta a kezdeti kísérletet, megjelenítheti az adatokat, hogy jobban megértse az Ön által használt adatkészletet.

1. Válassza ki a kimeneti portot az **adatkészlet Select oszlopainak** alján, majd válassza a **Megjelenítés**lehetőséget.

1. Az oszlop adatainak megtekintéséhez kattintson a különböző oszlopokra az adatablakban.

    Ebben az adatkészletben minden sor egy automobilt jelöl, és az egyes autókhoz tartozó változók oszlopként jelennek meg. Ebben az adatkészletben 205 sor és 26 oszlop található.

    Minden alkalommal, amikor egy adatoszlopra kattint, a bal oldalon megjelenik az oszlop **statisztikai** információi és **vizualizációs** képe.

    [![Az adatelőnézet](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)](./media/ui-tutorial-automobile-price-train-score/preview-data.gif#lightbox)

1. Az egyes oszlopokra kattintva megismerheti az adatkészletet, és azt is megtudhatja, hogy ezek az oszlopok hasznosak-e az autó árának előrejelzéséhez.

## <a name="prepare-data"></a>Adatok előkészítése

Az adatkészlet általában némi előfeldolgozást igényel az elemzéshez. Előfordulhat, hogy néhány hiányzó értéket észlelt az adatkészlet megjelenítésekor. Ahhoz, hogy a modell elemezni tudja az adatokat, el kell távolítani a hiányzó értékeket. A hiányzó értékekkel rendelkező sorok törlődnek. A normalizált **veszteségek** oszlop a hiányzó értékek nagy hányadát is tartalmazza, így a modellből kizárja ezt az oszlopot.

> [!TIP]
> A legtöbb modul használatának előfeltétele a bemeneti adatok hiányzó értékeinek törlése.

### <a name="remove-column"></a>Oszlop eltávolítása

Először távolítsa el teljesen a normalizált veszteségek oszlopot.

1. Válassza az **Oszlopok kiválasztása az adatkészlet** modulban lehetőséget.

1. A vászontól jobbra található **Tulajdonságok** ablaktáblán válassza az **Oszlopok szerkesztése**lehetőséget.

    * Hagyja **a szabályokat** és **az összes kijelölt oszlopot** .

    * A legördülő listákból válassza az **Exclude** (Kizárás) és a **column names** (oszlopnevek) lehetőséget, majd kattintson a szövegmezőbe. Írja be a **normalizált-veszteségek**értéket.

    * A jobb alsó sarokban kattintson az **OK** gombra az oszlop választójának bezárásához.

    ![Oszlop kizárása](./media/ui-tutorial-automobile-price-train-score/exclude-column.gif)
        
    Az adatkészletben az oszlopok kijelölésekor a Tulajdonságok ablaktábla azt jelzi, hogy az adatkészlet összes oszlopán átfog haladni a normalizált veszteségek kivételével.
        
    A Tulajdonságok panelen látható, hogy a normalizált veszteségek oszlop ki van zárva.

1. Kattintson duplán az **adatkészlet kiválasztása** elemre, és írja be a "normalizált veszteségek kizárása" megjegyzést. 
    
    A Megjegyzés beírása után kattintson a modulon kívülre. Megjelenik egy lefelé mutató nyíl, amely azt mutatja, hogy a modul tartalmaz egy megjegyzést.

1. A Megjegyzés megjelenítéséhez kattintson a lefelé mutató nyílra.

    A modul ekkor egy felfelé mutató nyilat jelenít meg a Megjegyzés elrejtéséhez.
        
    ![Megjegyzések](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Hiányzó adatok tisztítása

A modellek betanításakor meg kell tennie valamit a hiányzó információkkal kapcsolatban. Ebben az esetben hozzáadhat egy modult, amely eltávolítja az összes hiányzó sort, amelyből hiányzik az adathalmaz.

1. A **tiszta hiányzó** adatmodul megtalálásához a **keresőmezőbe írja be** a törlés kifejezést.

1. Húzza a **tiszta hiányzó adat** modult a kísérleti vászonra, és kapcsolja össze az **adathalmaz-modul Select oszlopaival** . 

1. A Tulajdonságok panelen válassza a **teljes sor eltávolítása** a **tisztítási mód**alatt lehetőséget.

1. Kattintson duplán a modulra, és írja be a következő megjegyzést: „Hiányzó értéket tartalmazó sorok törlése”.

    A kísérletnek ekkor az alábbihoz hasonlóan kell kinéznie:
    
    ![oszlop kijelölése](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="train-a-machine-learning-model"></a>Gépi tanulási modell betanítása

Most, hogy az adatgyűjtés elkészült, létrehozhat egy prediktív modellt. Az adatait a modell betanításához fogja használni. Ezt követően tesztelje a modellt, hogy megtudja, milyen mértékben képes előre jelezni az árakat.

### <a name="select-an-algorithm"></a>Algoritmus kiválasztása

A **besorolás** és a **regresszió** két algoritmus, amelynek segítségével felügyelt gépi tanítás valósítható meg. A **besorolás** egy adott kategóriájú (például vörös, kék vagy zöld) kategóriából származó választ jósol meg. A **regresszió** egy szám megbecslésére szolgál.

Mivel előre jelezni szeretné az árat, ami egy szám, regressziós algoritmust is használhat. Ebben a példában egy lineáris regressziós modellt fog használni.

### <a name="split-the-data"></a>Az adatfelosztás

Az adatokat a modell betanítására és tesztelésére is használhatja azáltal, hogy az adatokat külön betanítási és tesztelési adatkészletekre bontja.

1. Az adatfelosztási modul megkereséséhez írja be a feldarabolt adatelemet a keresőmezőbe, és kapcsolódjon a **tiszta hiányzó** adatmodul bal oldali portjához.

1. Válassza ki az adatfelosztási modult. A Tulajdonságok ablaktáblában állítsa az első kimeneti adatkészletben lévő sorok töredékét 0,7-re. Így a modell betanításához az adatmennyiség 70 százalékát fogjuk használni, a teszteléshez pedig 30 százalékot kell visszatartani.

1. Kattintson duplán az **adat felosztása** elemre, és írja be a következő megjegyzést: "az adatkészlet felosztása a betanítási készletbe (0.7) és a test set (0.3)"

### <a name="train-the-model"></a>A modell betanítása

A modell betanításához adjon meg egy olyan adathalmazt, amely tartalmazza az árat. A modell megkeresi az adatforrásokat, és összefüggéseket keres az autó funkciói és az ára között.

1. A tanulási algoritmus kiválasztásához törölje a modul-paletta keresési mezőjét.

1. Bontsa ki a **Machine learning** majd a **modell inicializálása**elemet. Itt számos modulkategória közül választhat, amelyek segítségével inicializálható a gépi tanulási algoritmus.

1. Ehhez a kísérlethez válassza a **regresszió** > **lineáris regressziót** , és húzza a kísérlet vászonra.

1. Keresse meg és húzza a **Train Model** modult a kísérleti vászonra. Kapcsolja össze a lineáris regressziós modul kimenetét a Train Model modul bal oldali bemenetével, és kapcsolja össze az adategységek kiosztási moduljának betanítási adat kimenetét (bal oldali port) a **Train Model** modul jobb oldali bemenetével.

    ![Képernyőfelvétel a Train Model modul helyes konfigurációjának megjelenítéséről. A lineáris regressziós modul a betanítási modell moduljának bal oldali portjához csatlakozik, és a felosztott adatmodul csatlakozik a betanítási modell jobb portjához.](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Válassza ki a **Train Model** modult. A Tulajdonságok ablaktáblán válassza ki az oszlop kiválasztó indítása lehetőséget, **majd írja be** a következőt: az oszlopnevek belefoglalása. Az ár az az érték, amelyet a modell előre meg fog jósolni

    ![Képernyőfelvétel: az oszlop kiválasztó moduljának megfelelő konfigurációja. A szabályokkal > tartalmaz oszlopnevek > "Price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    A kísérletnek így kell kinéznie:

    ![Képernyőfelvétel: a kísérlet helyes konfigurációjának megjelenítése a Train Model modul hozzáadása után.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Gépi tanulási modell kiértékelése

Most, hogy az adatok 70 százalékával lett kitanítva a modellre, használhatja az adatok további 30 százalékát, hogy megtudja, milyen jól működik a modellje.

1. Írja **be** a pontszám Model kifejezést a keresőmezőbe a **pontszám modell** modul megkereséséhez, majd húzza a modult a kísérleti vászonra. A **Train Model** modul kimenetének összekötése a **score Model**bal oldali bemeneti portjával. Az adatforráshoz tartozó adat kimenetének (jobb oldali portjának) összekötése a **score Model**megfelelő bemeneti portjára.

1. Írja **be** a kiértékelés kifejezést a keresőmezőbe a **kiértékelési modell** megkereséséhez, majd húzza a modult a kísérleti vászonra. A **pontszám modell** modul kimenetének összekötése a **modell**kiértékelésének bal oldali bemenetével. Az elkészült kísérletnek a következőképpen kell kinéznie:

    ![A kísérlet utolsó helyes konfigurációját bemutató képernyőkép.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Futtassa a kísérletet a korábban létrehozott számítási erőforrás használatával.

1. Tekintse meg a **pontszám modell** modul kimenetét a **pontszám modell** kimeneti portjának kiválasztásával, majd válassza a **Megjelenítés**lehetőséget. A modul megjeleníti az előre jelzett árat, valamint a tesztadatokból ismert tényleges értéket.

    ![Képernyőkép a kimeneti vizualizációról, kiemelve a "mutatós címke" oszlopot](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. A **modell** kiértékelése modul kimenetének megtekintéséhez válassza ki a kimeneti portot, majd válassza a **Megjelenítés**lehetőséget.

    ![Képernyőfelvétel a végső kísérlet kiértékelésének eredményeiről.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

A modellhez a következő statisztikák láthatók:

* **Átlagos abszolút hiba (Mae)** : Az abszolút hibák átlaga (a hiba a várt érték és a tényleges érték közötti különbség).
* **Legfelső szintű négyzetes hiba (gyökátlagos)** : A tesztelési adatkészleten végrehajtott előrejelzések alapján megjelenő, négyzetes hibák átlaga.
* **Relatív abszolút hiba**: A tényleges értékek és az összes tényleges érték átlaga közötti abszolút különbséghez viszonyított abszolút hibák átlaga.
* **Relatív négyzetes hiba**: A tényleges értékek és az összes tényleges érték átlaga közötti négyzetes különbséghez viszonyított átlagosan mért hibák átlaga.
* **Meghatározási együttható**: Más néven az R négyzetes érték, ez egy statisztikai metrika, amely azt jelzi, hogy a modell milyen jól illeszkedik az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb értékek azt jelzik, hogy az előrejelzés közelebb van a tényleges értékekhez. A meghatározási együttható esetében minél közelebb van az értéke egy (1,0), annál jobb az előrejelzések.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Az oktatóanyag első részében a következő lépéseket végezte el:

* Kísérlet létrehozva
* Az adatok előkészítése
* A modell betanítása
* A modell pontszáma és kiértékelése

A második részből megtudhatja, hogyan helyezheti üzembe a modellt Azure-webszolgáltatásként.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezésének folytatása](ui-tutorial-automobile-price-deploy.md)
