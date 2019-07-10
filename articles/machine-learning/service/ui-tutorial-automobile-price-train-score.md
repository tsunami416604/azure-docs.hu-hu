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
ms.openlocfilehash: 21f5a2d93b708e93f124bd44177bb7852dfbd86a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720513"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Oktatóanyag: Vizuális felhasználói felülettel autó árát előrejelzése

Ebben az oktatóanyagban azt tekintjük át az Azure Machine Learning szolgáltatás vizuális felületen prediktív megoldás fejlesztése igénybe vehet. Ez az oktatóanyag végére rendelkezni fog egy megoldás, amely előre meg tudjuk bármely küldje el a műszaki specifikációk alapján autó árát.

Része az oktatóanyag bemutatja, hogyan:

> [!div class="checklist"]
> * Importálás és az adatok tisztítása
> * A gépi tanulási modell betanítása
> * Pontszám és a egy modell értékelése

A [második](ui-tutorial-automobile-price-deploy.md) az oktatóanyag segítségével elsajátíthatja a prediktív modell egy Azure-webszolgáltatásként üzembe.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

Ebben az oktatóanyagban a befejezett verzióját, a mintakísérlet érhető el.

Keresésére, a **kísérletek lap**válassza **új hozzáadása**, majd válassza a **minta 1 – regressziós: Autó árát Prediction(Basic)** kísérletezhet.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Ha rendelkezik egy Azure Machine Learning szolgáltatás munkaterületén, ugorjon a [következő szakasz](#open-the-visual-interface-webpage).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Nyissa meg a vizuális felhasználói felületet weblapra

1. Nyissa meg a munkaterület a [az Azure portal](https://portal.azure.com/).

1. A munkaterületen válassza ki a **vizuális felhasználói felületet**. Válassza ki **indítási vizuális felhasználói felületet**. 

    ![Az Azure portal eléréséhez a vizuális felhasználói felületet egy Machine Learning szolgáltatás munkaterületről bemutató képernyőkép](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

## <a name="create-your-first-experiment"></a>Az első kísérlet létrehozása

A vizuális felhasználói felületet eszköz Itt adható meg egy interaktív, vizuális hozhat létre prediktív elemzési modellek. Fogd és vidd adatkészleteket és az elemzési modulok egy interaktív vászonra, és az létrehozása egy *kísérletezhet*.

1. Új kísérlet létrehozása kiválasztásával **+ új** a vizuális felhasználói felületet ablak alján.

    ![Új kísérlet hozzáadása](./media/ui-tutorial-automobile-price-train-score/add-new.png)

1. Válassza ki **üres kísérlet**.

1. Válassza ki a kísérlet alapértelmezett nevét **"végzett kísérletezést létrehozott az...** "a vászon tetején, és módosítsa valami értelmesebbre. Ha például **autó árának előrejelzése**. A névnek nem kell egyedinek lennie.

## <a name="add-data"></a>Adatok hozzáadása

Az első lépésben, a machine Learning van szüksége az adatok. Nincsenek több mintaadathalmazainak ezen a felületen, amelyet használhat. Meglévő forrásból is importálhat adatokat. Ebben az oktatóanyagban a minta adatkészlet használata **Automobile price data (Raw)** . 

1. A kísérletvászontól balra az adathalmazokat és modulokat tartalmazó paletta látható. Válassza ki **mentett adatkészletek** majd **minták** a rendelkezésre álló mintaadatkészletek megtekintéséhez.

1. Válassza ki az adatkészletet **Automobile price data (raw)** , és húzza a vászonra.

   ![Húzza a vászonra adatok](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

## <a name="select-columns"></a>Oszlopok kiválasztása

Válassza ki, melyik adatoszlopokat dolgozhat. Először konfigurálja a modul az összes elérhető oszlopok megjelenítéséhez.

> [!TIP]
> Ha ismeri az adatok vagy a kívánt modul neve, segítségével a keresősávba a paletta tetején gyorsan megtalálhatja. Az oktatóanyag további részeinek fogja használni a helyi.


1. Típus **kiválasztása** a keresőmezőbe keresése a **Select Columns in Dataset** modul.

1. Kattintással és húzással vigye a **Select Columns in Dataset** a vászonra. A modul a korábban hozzáadott adatkészlet alatti legördülő menü.

1. Az adatkészlet csatlakoztatása a **Select Columns in Dataset**: kattintson a kimeneti portra, az adatkészlet, és húzza át a bemeneti portjával **Select Columns in Dataset**, majd engedje el az egér gombját. Az adatkészlet és a modul is csatlakoztatott, még akkor is, ha áthelyezi vagy a vásznon.

    > [!TIP]
    > Az adathalmazok és modulok kis körökkel jelölt bemeneti és kimeneti portokkal rendelkeznek – a bemeneti portok felül, a kimeneti portok alul találhatók. Az adatfolyam létrehozásához a kísérlet egy modul kimeneti portját egy másik bemeneti porthoz való csatlakozáskor.
    >

    ![Csatlakozás a modulok](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    A vörös felkiáltójelből azt jelzi, hogy a tulajdonságok a modul még nem állított be.

1. Válassza ki a **Select Columns in Dataset** modul.

1. Az a **tulajdonságok** panelen a vásznon, kattintson a jobbra **oszlopok szerkesztése**.

    Az a **oszlopok kijelölése** párbeszédpanelen válassza **minden oszlop** és **minden funkció**. A párbeszédpanel kell kinéznie:

     ![oszlop-választó](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. Válassza ki a jobb alsó **OK** az Oszlopválasztó bezárásához.

## <a name="run-the-experiment"></a>Kísérlet futtatása

Bármikor kattintson az adatkészlet vagy megtekintheti az adatok néz ki ezen a ponton az adatfolyam a modul kimeneti portjával. Ha a **Visualize** lehetőség le van tiltva, először futtassa a kísérletet.

Egy kísérletet futtat egy számítási célt, amely egy olyan számítási erőforrásra, amely a munkaterülethez van csatolva. Miután létrehozott egy számítási célnak, későbbi futtatások felhasználhatja azt.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

A számítási célnak érhető el, miután a kísérletet futtat. Ha a Futtatás befejeződött, egy zöld pipa jelenik meg az egyes modulok.


## <a name="preview-the-data"></a>Az adatok előnézete

Most, hogy az első kísérlet futtatása jelenítheti meg az adatokat, hogy az adatkészlet együttműködve kell többet.

1. A kimeneti portra, alsó részén válassza ki a **Select Columns in Dataset** majd **Visualize**.

1. Kattintson az adott oszlop kapcsolatos információk megtekintéséhez időszak különböző oszlopból.

    Ez az adatkészlet minden sor egy autó jelöli, és az egyes autókhoz tartozó változók oszlopokként jelennek meg. Nincsenek 205 sorok és ez az adatkészlet 26 oszlopok.

     Minden alkalommal, amikor rákattint egy oszlopot az adatokat, a **statisztika** információkat és **Vizualizáció** képet annak az oszlopnak a bal oldalon fog megjelenni. Például, amikor rákattint a **num-az-ajtók** 2 egyedi értékeket és a hiányzó értékek 2 látja. Görgessen lefelé a szereplő értékek: két és négy módjait tette lehetővé.

     ![Az adatok előnézete](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. Kattintson az egyes oszlopok ismerje meg jobban az adatkészlet, és gondolja, hogy ezeket az oszlopokat egy autó árának előrejelzése hasznos lesz.

## <a name="prepare-data"></a>Adatok előkészítése

Az adathalmazok elemzése előtt általában némi előfeldolgozás szükséges. Talán észrevette néhány hiányzó értéket, amikor megjelenítik az adatkészletet. Ahhoz, hogy a modell elemezni tudja az adatokat, el kell távolítani a hiányzó értékeket. Akkor eltávolítja azokat a sorokat, amelyekből értékek hiányoznak. Emellett a **normalized-losses** oszlop rendelkezik hiányzó nagy részét, ezért a fog kizár Ez az oszlop a modellből hozhassanak.

> [!TIP]
> A legtöbb modul használatának előfeltétele a bemeneti adatok hiányzó értékeinek törlése.

### <a name="remove-column"></a>Oszlop eltávolítása

Először távolítsa el a **normalized-losses** oszlopot teljesen.

1. Válassza ki a **Select Columns in Dataset** modul.

1. Az a **tulajdonságok** panelen a vásznon, kattintson a jobbra **oszlopok szerkesztése**.

    * Hagyja **szabályokkal** és **minden oszlop** kiválasztott.

    * A legördülő listákból válassza az **Exclude** (Kizárás) és a **column names** (oszlopnevek) lehetőséget, majd kattintson a szövegmezőbe. Típus **normalized-losses**.

    * Válassza ki a jobb alsó **OK** az Oszlopválasztó bezárásához.

    ![Egy oszlop kizárása](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Most a Tulajdonságok panelen a Select Columns in Dataset jelzi, hogy ez lesz az összes oszlop kivételével az adathalmaz **normalized-losses**.
        
    A Tulajdonságok panelen látható, amely a **normalized-losses** oszlop ki van zárva.
        
    ![Tulajdonság ablaktábla](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. 

1. Kattintson duplán a **Select Columns in Dataset** modul, és írja be a "normalized losses oszlop kizárása." 
    
    A Megjegyzés, kattintson a modul kívül. A lefelé mutató nyílra jelenik meg, hogy a modul tartalmazza a Megjegyzés megjelenítése.

1. Kattintson a lefelé mutató nyilat a megjegyzés megjelenítéséhez.

    A modul most egy felfelé mutató nyílra a Megjegyzés elrejtése jeleníti meg.
        
    ![Megjegyzések](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Hiányzó adatok tisztítása

Amikor egy modell betanításához kell valamit a hiányzó adatokra vonatkozó. Ebben az esetben fogja hozzáadni egy modul az összes többi sort, amelyből adatok hiányoznak.

1. Típus **tisztaként** a keresőmezőbe keresése a **Clean Missing Data** modul.

1. Húzza a **Clean Missing Data** modult a kísérletvászonra, és kösse össze a **Select Columns in Dataset** modul. 

1. A Tulajdonságok panelen válassza ki a **teljes sor eltávolítása** alatt **Remove Entire Row mód**.

    Ezeket a beállításokat a közvetlen **Clean Missing Data** , amely tartalmaz a hiányzó sort törölje.

1. Kattintson duplán a modulra, és írja be a következő megjegyzést: „Hiányzó értéket tartalmazó sorok törlése”.
 
    ![Sorok eltávolítása](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    A kísérlet kell kinéznie ehhez hasonló:
    
    ![Oszlop kiválasztása](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="visualize-the-results"></a>Az eredmények megjelenítése

Végrehajtott módosítások a modulok a kísérlet során, mivel a állapota megváltozott "A vázlat".  Az új tiszta adatainak megjelenítése, először kell ismét lefuttathatja a kísérletet.

1. Válassza ki **futtatása** alján futtathatja a kísérletet.

    Ezúttal újból felhasználhatja a számítási célnak a korábban létrehozott.

1. Válassza ki **futtatása** a párbeszédpanelen.

   ![Kísérlet futtatása](./media/ui-tutorial-automobile-price-train-score/select-compute.png)

1. Ha a Futtatás befejeződött, kattintson a jobb gombbal a **Clean Missing Data** modul új tiszta adatok vizualizálásához.

    ![Az adatok tisztítása megjelenítése](./media/ui-tutorial-automobile-price-train-score/visualize-cleaned.png)

1. Kattintson a különböző oszlopból a megtisztított adatok ablakban megtekintheti, hogyan adatok megváltoztak.

    ![Az adatok tisztítása megjelenítése](media/ui-tutorial-automobile-price-train-score/visualize-result.png)

    Jelenleg 193 sorok és oszlopok 25.

    Amikor rákattint a **num-az-ajtók** még 2 egyedi értékeket, de a hiányzó értékek 0 most már megjelenik. Kattintson végig a többi megtekintéséhez, hogy nincsenek-e hiányzó értékek az adatkészletet a bal oldali oszlop. 

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

    A kísérlet kell kinéznie:

    ![Képernyőfelvétel: a megfelelő konfigurációt a tanítási modell modulhoz hozzáadása után a kísérlet.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>A tanítási kísérlet futtatása

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Pontszám, és a modell értékelése

Most, hogy a modell segítségével az adatok a 70 %-os már betanított, ezzel pontszámot rendelni az más 30 százalék pontozásával megállapíthatjuk arról, hogy a modell függvények.

1. Típus **pontszám modell** a keresőmezőbe keresése a **Score Model** modul, és húzza a modult a kísérletvászonra. Csatlakozzon a kimenetét a **tanítási modell** modul bal oldali bemeneti portjával **Score Model**. A tesztelési adatokat tartalmazó kimenetét (jobb oldali portját) a csatlakozás a **Split Data** modul jobb oldali bemeneti portjával **Score Model**.

1. Típus **kiértékelése** a keresőmezőbe keresése a **Evaluate Model** és húzza a modult a kísérletvászonra. Csatlakozzon a kimenetét a **Score Model** modul bal oldali bemenetével **Evaluate Model**. Az elkészült kísérletnek a következőképpen kell kinéznie:

    ![Képernyőfelvétel: a kísérletet az utolsó konfigurációját.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Futtassa a kísérletet, az azonos számítási célnak a korábban használt használatával.

1. Eredményének megtekintéséhez a **Score Model** modul kimeneti portjával kiválasztásával **Score Model** válassza **Visualize**. A modul megjeleníti az előre jelzett árat, valamint a tesztadatokból ismert tényleges értéket.

    ![Képernyőkép a kimeneti képi megjelenítés, a "Pontozását címke" oszlop kiemelése](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. A modell kiértékelése modul eredményének megtekintéséhez válassza ki a kimeneti portra, és válassza ki a képi megjelenítés opcióra.

    ![Képernyőkép az elkészült kísérletnek a következőképpen a kiértékelésének eredménye.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

A modell a következő statisztikai adatok jelennek meg:

* **Mean Absolute Error (MAE)** : A átlaga (hiba az előre jelzett érték és a tényleges érték közötti különbséget).
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

* Kísérlet létrehozása
* Az adatok előkészítése
* A modell betanítása
* Pontszám, és a modell értékelése

A második részben megismerheti, hogyan való üzembe helyezését az Azure-webszolgáltatásként fogja.

> [!div class="nextstepaction"]
> [Folytassa a modellek üzembe helyezéséhez](ui-tutorial-automobile-price-deploy.md)
