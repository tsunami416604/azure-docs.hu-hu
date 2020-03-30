---
title: 'Bemutató: Megjósolni autó ára a tervező'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan taníthat be, pontíthat és helyezhet üzembe egy gépi tanulási modellt egy transzvesztita felület használatával. Ez az oktatóanyag egy kétrészes sorozat első része az autóárak lineáris regresszió használatával történő előrejelzéséről.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 8b471dd509dca02ab2a66d70dbc7fac9988f1afe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472237"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Bemutató: Megjósolni autó ára a tervező (előnézet)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben a két részből álló oktatóanyagból megtudhatja, hogyan használhatja az Azure Machine Learning tervezőjét egy olyan gépi tanulási modell betanítására és üzembe helyezésére, amely bármely autó árát előre jelzi. A tervező egy drag-and-drop eszköz, amely lehetővé teszi, hogy hozzon létre gépi tanulási modellek nélkül egyetlen sornyi kódot.

Az első részben a bemutató, megtudhatja, hogyan kell:

> [!div class="checklist"]
> * Hozzon létre egy új folyamatot.
> * Adatok importálása.
> * Adatok előkészítése.
> * Gépi tanulási modell betanítása.
> * Gépi tanulási modell kiértékelése.

Az oktatóanyag [második részében](tutorial-designer-automobile-price-deploy.md) a modellt valós idejű következtetésként telepíti, hogy megjósolja az általa küldött műszaki előírások alapján bármely autó árát. 

> [!NOTE]
>Az oktatóanyag befejezett verziója mintafolyamatként érhető el.
>
>A kereséshez keresse fel a munkaterület tervezőjét. Az **Új csővezeték** szakaszban válassza **az 1.**

## <a name="create-a-new-pipeline"></a>Új folyamat létrehozása

Az Azure Machine Learning-folyamatok több gépi tanulási és adatfeldolgozási lépést egyetlen erőforrásba szerveznek. A folyamatok lehetővé teszik az összetett gépi tanulási munkafolyamatok rendszerezését, kezelését és újrafelhasználását a projektek és a felhasználók között.

Az Azure Machine Learning-folyamat létrehozásához egy Azure Machine Learning-munkaterületre van szükség. Ebben a szakaszban megtudhatja, hogyan hozhat létre mindkét erőforrást.

### <a name="create-a-new-workspace"></a>Új munkaterület létrehozása

A tervező használatához először egy Azure Machine Learning-munkaterületre van szüksége. A munkaterület az Azure Machine Learning legfelső szintű erőforrása, amely központosított helyet biztosít az Azure Machine Learningben létrehozott összes összetevővel való munkához.

Ha rendelkezik egy Nagyvállalati kiadással rendelkező Azure Machine Learning-munkaterülettel, [ugorjon a következő szakaszra.](#create-the-pipeline)

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>A folyamat létrehozása

1. Jelentkezzen be <a href="https://ml.azure.com?tabs=jre" target="_blank">a ml.azure.com,</a>és válassza ki azt a munkaterületet, amelyen dolgozni szeretne.

1. Válassza **a Tervező**lehetőséget.

    ![Képernyőkép a vizuális munkaterületről, amely bemutatja, hogyan érheti el a tervezőt](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Válassza **a Könnyen használható előre elkészített modulokat.**

1. A vászon tetején válassza ki a **Pipeline-Created-on**alapértelmezett folyamatnevet. Nevezze át *az Automobile árelőrejelzése*. A névnek nem kell egyedinek lennie.

## <a name="set-the-default-compute-target"></a>Az alapértelmezett számítási cél beállítása

A folyamat egy számítási célon fut, amely egy számítási erőforrás, amely a munkaterülethez van csatolva. Miután létrehozott egy számítási célt, újra felhasználhatja azt a jövőbeli futtatásokhoz.

Beállíthat egy **alapértelmezett számítási célt** a teljes folyamathoz, amely minden modult megad, hogy alapértelmezés szerint ugyanazt a számítási célt használja. A számítási célokat azonban modulonként is megadhatja.

1. A folyamat neve mellett válassza a **Fogaskerék ikon** ![képernyőképét a vászon tetején a](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) **fogaskerék** ikononra a Beállítások ablaktábla megnyitásához.

1. A **Beállítások** ablaktáblán, a vászon tól jobbra válassza a **Számítási cél kiválasztása lehetőséget.**

    Ha már rendelkezik egy rendelkezésre álló számítási cél, kiválaszthatja azt a folyamat futtatásához.

    > [!NOTE]
    > A tervező csak az Azure Machine Learning számítási célokon futtathat kísérleteket. Más számítási célok nem jelennek meg.

1. Adja meg a számítási erőforrás nevét.

1. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Számítási erőforrás létrehozása körülbelül öt percet vesz igénybe. Az erőforrás létrehozása után újra felhasználhatja, és kihagyhatja ezt a várakozási időt a jövőbeli futtatásokhoz.
    >
    > A számítási erőforrás automatikus skálázása nulla csomópontok, ha a költségek megtakarítása tétlen. Ha egy késleltetés után újra használja, körülbelül öt perc várakozási időt tapasztalhat, miközben biztonsági másolatot kell kapnia.

## <a name="import-data"></a>Adatok importálása

A tervező számos mintaadatkészletet tartalmaz, amelyekkel kísérletezhet. Ehhez az oktatóanyaghoz használja **az Automobile áradatait (Raw).** 

1. A folyamatvászontól balra adatkészletek és modulok palettája található. Válassza **az Adatkészletek**lehetőséget, majd tekintse meg a **Minták** szakaszt a rendelkezésre álló mintaadatkészletek megtekintéséhez.

1. Jelölje ki az **Automobile áradatadatait (Raw)** adatkészletet, és húzza a vászonra.

   ![Adatok húzása vászonra](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Az adatok vizualizációja

Az adatok megjelenítésével megismerheti a használni kívánt adatkészletet.

1. Válassza ki az **Automobile áradat-adatok (nyers)** modult.

1. A modul részletei ablaktáblában a vászon jobb oldalán válassza a **Kimenetek**lehetőséget.

1. Az adatok megjelenítéséhez kattintson a diagram ikonra.

    ![Az adatok vizualizációja](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Az egyes adatok megtekintéséhez jelölje ki az adatablak különböző oszlopait.

    Minden sor egy autót jelöl, és az egyes autókhoz társított változók oszlopként jelennek meg. Ebben az adatkészletben 205 sor és 26 oszlop található.

## <a name="prepare-data"></a>Adatok előkészítése

Az adatkészletek általában némi előfeldolgozást igényelnek az elemzés előtt. Előfordulhat, hogy az adatkészlet vizsgálatakor néhány hiányzó értéket észlelt. Ezeket a hiányzó értékeket meg kell tisztítani, hogy a modell megfelelően elemezhesse az adatokat.

### <a name="remove-a-column"></a>Oszlop eltávolítása

Amikor betanít egy modellt, tennie kell valamit a hiányzó adatokkal. Ebben az adatkészletben a **normalizált veszteségek** oszlopban sok érték hiányzik, így teljesen ki fogja zárni ezt az oszlopot a modellből.

1. A vászontól balra lévő modulpalettán bontsa ki az **Adatátalakítás** szakaszt, és keresse meg az **Oszlopok kijelölése** az adatkészletben modult.

1. Húzza az Oszlopok kijelölése az **Adatkészlet modulban** elemet a vászonra. Dobja a modult az adatkészletmodul alá.

1. Csatlakoztassa az **Automobile áradat-adathalmazát (Raw)** az **Adatkészlet oszlopok kijelölése** modulhoz. Húzza az alakzatot az adatkészlet kimeneti portjáról, amely a vászon adatkészletének alján lévő kis kör, az **Adatkészlet Oszlopok kiválasztása**bemeneti portjára, amely a modul tetején lévő kis kör.

    > [!TIP]
    > Az egyik modul kimeneti portját egy másik bemeneti portjához csatlakoztatva hozza létre az adatok áramlását a folyamaton keresztül.
    >

    ![Modulok csatlakoztatása](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Jelölje ki az **Oszlopok kijelölése az Adatkészlet modulban jelölőnégyzetet.**

1. A modul részletei ablaktáblában, a vászontól jobbra válassza az **Oszlop szerkesztése**lehetőséget.

1. Bontsa ki az **Oszlopnevek** legördülő menüt a **Belefoglalás**csoportban, és válassza a **Minden oszlop lehetőséget.**

1. Jelölje **+** be az új szabály hozzáadásához.

1. A legördülő menüben válassza a **Kizárás** és **oszlopnevek lehetőséget.**
    
1. Írja be a *normalizált veszteségeket* a szövegmezőbe.

1. A jobb alsó sarokban válassza a **Mentés** lehetőséget az oszlopkijelölő bezárásához.

    ![Oszlop kizárása](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Jelölje ki az **Oszlopok kijelölése az Adatkészlet modulban jelölőnégyzetet.** 

1. A modul részletei ablaktáblában a vászon tól jobbra jelölje be a **Megjegyzés** szöveget, és írja be *a Normalizált veszteségek kizárása parancsot.*

    A megjegyzések megjelennek a grafikonon, amelyek segítenek a folyamat rendszerezésében.

### <a name="clean-missing-data"></a>Hiányzó adatok tisztítása

Az adatkészlet továbbra is hiányzó értékeket, miután eltávolította a **normalizált veszteségek** oszlop eltávolítása után. A hiányzó adatokat a **Hiányzó adatok tisztítása** modul lal távolíthatja el.

> [!TIP]
> A hiányzó értékek tisztítása a bemeneti adatokból előfeltétele a tervező legtöbb moduljának használatának.

1. A vászontól balra lévő modulpalettán bontsa ki az **Adatátalakítás**szakaszt, és keresse meg a **Hiányzó adatok tisztítása modult.**

1. Húzza a **Hiányzó adatok tisztítása** modult a folyamatvászonra. Csatlakoztassa az **Adatkészlet oszlopainak kijelölése** modulhoz. 

1. Válassza a **Hiányzó adatok tisztítása modult.**

1. A modul részletei ablaktáblában, a vászontól jobbra válassza az **Oszlop szerkesztése**lehetőséget.

1. A **megtisztítandó oszlopok** ablakban bontsa ki a **Felezés**menü melletti legördülő menüt. Kijelölés, **Minden oszlop**

1. **Mentés** kiválasztása

1. A modul részletei ablaktáblában, a vászon tól jobbra, válassza a **Teljes sor eltávolítása** lehetőséget a **Tisztítási mód**csoportban.

1. A modul részletei ablaktáblában, a vászon tól jobbra, jelölje ki a **Megjegyzés jelölőnégyzetet,** és írja be *a Hiányzó értéksorok eltávolítása parancsot*. 

    A csővezetéknek most valahogy így kell kinéznie:
    
    ![Kijelölés-oszlop](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Gépi tanulási modell betanítása

Most, hogy már rendelkezik az adatok feldolgozásához a modulok, beállíthatja a betanítási modulokat.

Mivel meg szeretné jósolni az árat, amely egy szám, regressziós algoritmust használhat. Ebben a példában lineáris regressziós modellt használ.

### <a name="split-the-data"></a>Az adatok felosztása

Az adatok felosztása gyakori feladat a gépi tanulásban. Az adatokat két külön adatkészletre osztja fel. Az egyik adatkészlet betanítja a modellt, a másik pedig azt, hogy a modell milyen jól teljesített.

1. A modulpalettán bontsa ki az **Adatátalakítás** szakaszt, és keresse meg a **Split Data modult.**

1. Húzza a **Split Data** modult a folyamatvászonra.

1. Csatlakoztassa a Hiányzó **adatok tisztítása** modul bal oldali portját a **Split Data** modulhoz.

    > [!IMPORTANT]
    > Győződjön meg arról, hogy a **Tiszta hiányzó adatok** bal oldali kimeneti portjai a Split **Data**-hoz csatlakoznak. A bal oldali port tartalmazza a megtisztított adatokat. A jobb oldali port tartalmazza a különálló adatokat.

1. Válassza az **Adatok felosztása modult.**

1. A modul részletei ablaktáblában a vászon jobb oldalán állítsa az **első kimeneti adatkészlet sorainak törtrészét** 0.7-re.

    Ez a beállítás az adatok 70 százalékát osztja fel a modell betanításához és 30 százalékát a teszteléshez. A 70 százalékos adatkészlet a bal oldali kimeneti porton keresztül érhető el. A fennmaradó adatok a megfelelő kimeneti porton keresztül lesznek elérhetők.

1. A modul részletei ablaktáblában a vászon jobb oldalán jelölje be a **Megjegyzés** mezőt, és írja be *az Adatkészlet felosztása betanítási (0.7) és tesztkészlet (0.3) parancsot.*

### <a name="train-the-model"></a>A modell betanítása

A modell betanítása egy adatkészletet, amely tartalmazza az árat. Az algoritmus egy modellt hoz létre, amely elmagyarázza a funkciók és a betanítási adatok által bemutatott ár közötti kapcsolatot.

1. A modulpalettán bontsa ki a **Machine Learning Algorithms (Machine Learning Algorithms) csomópontot.**
    
    Ez a beállítás a modulok több kategóriáját jeleníti meg, amelyek segítségével inicializálhatja a tanulási algoritmusokat.

1. Válassza **a Regressziós** > **lineáris regresszió lehetőséget,** és húzza a csővezeték vászonra.

1. Csatlakoztassa a **lineáris regressziós** modul kimenetét a **Train Model** modul bal oldali bemenetéhez.

1. A modulpalettán bontsa ki a **Modul betanítása**szakaszt, és húzza a **Betanítási modell** modult a vászonra.

1. Jelölje ki a **Modell betanítása** modult, és húzza a csővezeték vászonra.

1. Csatlakoztassa a **Split Data** modul betanítási adatok kimenetét (bal oldali portját) a **Train Model** modul jobb bemenetével.
    
    > [!IMPORTANT]
    > Győződjön meg arról, hogy a **Split Data** bal oldali kimeneti portjai a **betanítási modellhez**csatlakoznak. A bal oldali port tartalmazza a betanítási készletet. A jobb oldali port tartalmazza a tesztkészletet.

    ![Képernyőkép a Train Model modul helyes konfigurációjával. A lineáris regressziós modul a Train Model modul bal oldali portjához csatlakozik, a Split Data modul pedig a Train Model jobb oldali portjához.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Válassza ki a **Vonatmodell** modult.

1. A modul részletei ablaktáblában a vászontól jobbra válassza az **Oszlopválasztó szerkesztése** lehetőséget.

1. A **Címke oszlop** párbeszédpanelen bontsa ki a legördülő menüt, és válassza **az Oszlopnevek lehetőséget.** 

1. A szövegmezőbe írja be az *árat* a modell által előre jelezni kívánt érték megadásához.

    >[!IMPORTANT]
    > Győződjön meg róla, hogy pontosan megadta az oszlop nevét. Ne használja ki az **árat**. 

    A folyamatnak így kell kinéznie:

    ![Képernyőkép a folyamat helyes konfigurációjával a Train Model modul hozzáadása után.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

### <a name="add-the-score-model-module"></a>A Score Model modul hozzáadása

Miután betanítja a modellt az adatok 70 százalékának használatával, a többi 30 százalék pontozásával megtekintheti, hogy a modell milyen jól működik.

1. Írja be a *pontszámmodellt* a keresőmezőbe a **Score Model** modul megkereséséhez. Húzza a modult a csővezeték vászonra. 

1. Kösse össze a **Train Model** (Modell betanítása) modul kimenetét a **Score Model** (Modell pontozása) modul bal oldali bemeneti portjával. Kösse össze a **Split Data** (Adatok felosztása) modul tesztelési adatokat tartalmazó kimenetét (jobb oldali portját) a **Score Model** (Modell pontozása) modul jobb oldali bemeneti portjával.

### <a name="add-the-evaluate-model-module"></a>A Modell kiértékelése modul hozzáadása

A **Modell kiértékelése** modul segítségével kiértékelheti, hogy a modell mennyire pontozta a tesztadatkészletet.

1. Írja be a *kiértékelés* kifejezést a keresőmezőbe a **Modell kiértékelése** modul megkereséséhez. Húzza a modult a csővezeték vászonra. 

1. Csatlakoztassa a **Score Model** modul kimenetét a **Modell kiértékelése**bal oldali bemenetéhez. 

    A végső csővezeték nek valahogy így kell kinéznie:

    ![Képernyőkép a folyamat helyes konfigurációjával.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="submit-the-pipeline"></a>A folyamat beküldése

Most, hogy a folyamat minden beállítás, beküldhet egy folyamat fut a gépi tanulási modell betanításához. A folyamatfuttatást bármikor elküldheti, miközben folyamatokat épít a tervezőben. Ezt megteheti, hogy ellenőrizze a munkát menet emiközben ellenőrizze a folyamat működik a várt módon.

1. A vászon tetején válassza a **Küldés lehetőséget.**

1. A **Folyamat futtatása beállítása** párbeszédpanelen válassza az **Új létrehozása lehetőséget.**

    > [!NOTE]
    > A hasonló folyamatokat csoportosító kísérletek együtt futnak. Ha egy folyamatot többször futtat, ugyanazt a kísérletet választhatja ki az egymást követő futtatásokhoz.

    1. Adja meg az Új **kísérlet neve**leíró nevét.

    1. Válassza a **Küldés** lehetőséget.
    
    A futtatási állapotot és a részleteket a vászon jobb felső részén tekintheti meg.

### <a name="view-scored-labels"></a>Pontozott címkék megtekintése

A futtatás befejezése után megtekintheti a folyamat futtatásának eredményeit. Először tekintse meg a regressziós modell által generált előrejelzéseket.

1. Válassza ki a **Score Model** modult a kimenet megtekintéséhez.

1. A modul részletei ablaktáblában a vászon jobb oldalán ![válassza a](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) **Kimenetek** > grafikon ikon megjelenítéséhez ikont az eredmények megtekintéséhez.

    Itt láthatja az előre jelzett árakat és a tényleges árakat a tesztelési adatokból.

    ![Képernyőkép a pontozott címke oszlopot kiemelő kimeneti vizualizációról](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Modellek kiértékelése

A **Kiértékelés modell segítségével** megtekintheti, hogy a betanított modell milyen jól teljesített a teszt adatkészleten.

1. Válassza ki a **Modell kiértékelése** modult a kimenet megtekintéséhez.

1. A modul részletei ablaktáblában a vászon jobb oldalán ![válassza](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) a **Kimenet >** grafikon ikon megjelenítéséhez ikont az eredmények megtekintéséhez.

A modellhez a következő statisztikák jelennek meg:

* **Átlagos abszolút hiba (MAE)**: Az abszolút hibák átlaga. A hiba az előre jelzett érték és a tényleges érték közötti különbség.
* **Négyzetes négyzetes hiba (RMSE)**: A tesztadatkészleten végzett előrejelzések négyzetes hibáinak átlagának négyzetgyöke.
* **Relative Absolute Error** (relatív abszolút eltérés): a tényleges értékek és az összes tényleges értékek átlaga közötti különbségek abszolút eltérésének átlaga.
* **Relative Squared Error** (relatív négyzetes eltérés): a négyzetes eltérések átlaga a tényleges értékek és az összes tényleges érték átlaga közötti különbség négyzetes értékéhez viszonyítva.
* **Meghatározási együttható:** R négyzetes értékként is ismert, ez a statisztikai mutató azt jelzi, hogy a modell mennyire illeszkedik az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb érték azt jelzi, hogy az előrejelzések közelebb állnak a tényleges értékekhez. A meghatározási együttható esetében minél közelebb van az értéke egyhez (1.0), annál jobbak az előrejelzések.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Hagyja ki ezt a szakaszt, ha folytatni szeretné az oktatóanyag [2.](tutorial-designer-automobile-price-deploy.md)

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

A második részben megtudhatja, hogyan telepítheti a modellt valós idejű végpontként.

> [!div class="nextstepaction"]
> [Modellek telepítésének folytatása](tutorial-designer-automobile-price-deploy.md)
