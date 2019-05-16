---
title: 'Gyors útmutató: Készítse elő és kód írása nélkül az adatok megjelenítése'
titleSuffix: Azure Machine Learning service
description: Hozzon létre a machine learning-kísérletek előkészítéséhez és az adatok vizualizálása a fogd és vidd felhasználói felületet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 0c492424e67853f7cb4a017fb4215d38a555a8a4
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545045"
---
# <a name="quickstart-prepare-and-visualize-data-without-writing-code-in-azure-machine-learning"></a>Gyors útmutató: Készítse elő és az adatok megjelenítése az Azure Machine Learning kódírás nélkül

Készítse elő az adatok és megjelenítése a fogd és vidd vizuális felületen (előzetes verzió) az Azure Machine Learning. A használni kívánt adatok különböző autót adatokat, például márkája, típusa, műszaki specifikációk, és ár-bejegyzéseket tartalmaz.  

Ebben a rövid útmutatóban fog ismerje meg, és előkészíti az adatokat:

- Adja hozzá, és az adatok előnézetének megtekintéséhez az első kísérlet létrehozása
- Eltávolítja a hiányzó értékeket az adatok előkészítése
- Kísérlet futtatása
- Az eredményül kapott adatok megjelenítése

Ha Ön a machine learning, a videósorozat a vadonatúj [adatelemzés kezdőknek](https://docs.microsoft.com/azure/machine-learning/studio/data-science-for-beginners-the-5-questions-data-science-answers) Machine learning remek bevezetőt.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

### <a name="create-a-workspace"></a>Munkaterület létrehozása

Ha rendelkezik egy Azure Machine Learning szolgáltatás munkaterületén, ugorjon a [következő szakasz](#start). Ellenkező esetben hozzon létre egyet most.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="start"></a> Nyissa meg a vizuális felhasználói felületet weblapra

1. Nyissa meg a munkaterület a [az Azure portal](https://portal.azure.com/).  

1. A munkaterületen válassza ki a **vizuális felhasználói felületet**.  Válassza ki **indítási vizuális felhasználói felületet**.  
 
    ![Indítsa el a vizuális felhasználói felületet](./media/ui-quickstart-run-experiment/launch-ui.png)

    A felület weblap nyílik meg egy új böngészőlap.  

## <a name="create-your-first-experiment"></a>Az első kísérlet létrehozása

A vizuális felhasználói felületet eszköz Itt adható meg egy interaktív, vizuális egyszerűen hozhatók létre, tesztelheti és prediktív elemzési modellek ismételt futtatásával. Fogd és vidd adatkészleteket és az elemzési modulok egy interaktív vászonra, együtt való csatlakoztatással űrlap egy _kísérletezhet_.  Most már az első kísérlet létrehozása.

1. A bal alsó sarokban válassza **új hozzáadása**.
![Új kísérlet hozzáadása](./media/ui-quickstart-run-experiment/add-new.png)

1. Válassza ki **üres kísérlet**.

1. A kísérlet kap egy alapértelmezett nevet. Válassza ki ezt a szöveget, és nevezze át "a rövid útmutató – Ismerkedés a data." Ez a név nem kell egyedinek lennie.

1. A **Mini térkép** a képernyő alján akkor hasznos, ha nagy kísérletek megtekintéséhez.  Ebben a rövid útmutatóban nem kell kattintson a felső minimalizálása érdekében, hogy a nyílra.  

    ![Kísérlet átnevezése](./media/ui-quickstart-run-experiment/rename.png)

## <a name="add-data"></a>Adatok hozzáadása

Az első lépésben, a machine Learning van szüksége az adatok. Több mintaadathalmazainak ezen a felületen, amelyet használhat, vagy adatokat számos más forrásból is importálhat. Ebben a példában a mintaadatkészlettel használni kívánt **Automobile price data (Raw)**. 

1. A kísérletvászontól balra az adathalmazokat és modulokat tartalmazó paletta látható. Válassza ki **mentett adatkészletek** majd **minták** a rendelkezésre álló mintaadatkészletek megtekintéséhez.

1. Válassza ki az adatkészletet **Automobile price data (raw)**, és húzza a vászonra.

   ![Húzza a vászonra adatok](./media/ui-quickstart-run-experiment/drag-data.png)


## <a name="select-columns"></a>Oszlopok kiválasztása

Válassza ki, melyik adatoszlopokat dolgozhat.  Először konfigurálja a modul az összes elérhető oszlopok megjelenítéséhez.

> [!TIP]
> Ha ismeri az adatok vagy a kívánt modul neve, segítségével a keresősávba a paletta tetején gyorsan megtalálhatja.  Ez a rövid útmutató a többi fogja használni a helyi.

1. Típus **kiválasztása** a keresőmezőbe keresése a **Select Columns in Dataset** modul.

1. Kattintással és húzással vigye a **Select Columns in Dataset** a vászonra. A modul a korábban hozzáadott adatkészlet alatti legördülő menü.

1. Az adatkészlet csatlakoztatása a **Select Columns in Dataset**: kattintson a kimeneti portra, az adatkészlet, és húzza át a bemeneti portjával **Select Columns in Dataset**, majd engedje el az egér gombját. Az adatkészlet és a modul is csatlakoztatott, még akkor is, ha áthelyezi vagy a vásznon.

    > [!TIP]
    > Az adathalmazok és modulok kis körökkel jelölt bemeneti és kimeneti portokkal rendelkeznek – a bemeneti portok felül, a kimeneti portok alul találhatók. Az adatfolyam létrehozásához a kísérlet egy modul kimeneti portját egy másik bemeneti porthoz való csatlakozáskor.
    >
    > Ha problémákat észlel a modulok, próbálja meg egészen húz a csomópont csatlakozik.

    ![Csatlakozás a modulok](./media/ui-quickstart-run-experiment/connect-modules.gif) 

    A vörös felkiáltójelből azt jelzi, hogy a tulajdonságok a modul még nem állított be. Azt fogjuk tenni.
   
1. Válassza ki a **Select Columns in Dataset** modul.

1. Az a **tulajdonságok** panelen a vásznon, kattintson a jobbra **oszlopok szerkesztése**.

    Az a **oszlopok kijelölése** párbeszédpanelen válassza **minden oszlop** és **minden funkció**. A párbeszédpanel kell kinéznie:

     ![oszlop-választó](./media/ui-quickstart-run-experiment/select-all.png)

1. Válassza ki a jobb alsó **OK** az Oszlopválasztó bezárásához.

## <a name="run-the-experiment"></a>Kísérlet futtatása

Bármikor kattintson az adatkészlet vagy megtekintheti az adatok néz ki ezen a ponton az adatfolyam a modul kimeneti portjával.  Ha a **Visualize** lehetőség le van tiltva, először futtassa a kísérletet.  Azt fogjuk tenni.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

A számítási célnak érhető el, miután a kísérletet futtat. A Futtatás befejeződése után egy zöld pipa jelenik meg az egyes modulok.

![Állapot megtekintése](./media/ui-quickstart-run-experiment/status1.png)

## <a name="preview-the-data"></a>Az adatok előnézete

Most, hogy az első kísérlet futtatása jelenítheti meg az adatokat, hogy többet kell dolgozni az adatokat.

1. A kimeneti portra, alsó részén válassza ki a **Select Columns in Dataset** majd **Visualize**.

1. Kattintson az adott oszlop kapcsolatos információk megtekintéséhez időszak különböző oszlopból.  

    Ez az adatkészlet minden sor egy autó jelöli, és az egyes autókhoz tartozó változók oszlopokként jelennek meg.    Nincsenek 205 sorok és ez az adatkészlet 26 oszlopok.

     Minden alkalommal, amikor rákattint egy oszlopot az adatokat, a **statisztika** információkat és **Vizualizáció** képet annak az oszlopnak a bal oldalon fog megjelenni.  Például, amikor rákattint a **num-az-ajtók** 2 egyedi értékeket és a hiányzó értékek 2 látja.  Görgessen lefelé a szereplő értékek: két és négy módjait tette lehetővé.

     ![Az adatok előnézete](./media/ui-quickstart-run-experiment/preview-data.gif)

1. Kattintson az egyes oszlopok, hogy az adatkészlet többet.

## <a name="prepare-data"></a>Adatok előkészítése

Az adathalmazok elemzése előtt általában némi előfeldolgozás szükséges. Talán észrevette, hogy az oszlopok számos sorából hiányoztak az értékek. Ahhoz, hogy a modell elemezni tudja az adatokat, el kell távolítani a hiányzó értékeket. Akkor eltávolítja azokat a sorokat, amelyekből értékek hiányoznak. Emellett a **normalized-losses** oszlop rendelkezik hiányzó nagy részét, ezért a fog kizár Ez az oszlop a modellből hozhassanak.

> [!TIP]
> A legtöbb modul használatának előfeltétele a bemeneti adatok hiányzó értékeinek törlése.  

### <a name="remove-column"></a>Oszlop eltávolítása

Először távolítsa el a **normalized-losses** oszlopot teljesen.

1. Válassza ki a **Select Columns in Dataset** modul.

1. Az a **tulajdonságok** panelen a vásznon, kattintson a jobbra **oszlopok szerkesztése**.

    * Hagyja **szabályokkal** és **minden oszlop** kiválasztott.

    * A legördülő listákból válassza az **Exclude** (Kizárás) és a **column names** (oszlopnevek) lehetőséget, majd kattintson a szövegmezőbe. Típus **normalized-losses**.

    * Válassza ki a jobb alsó **OK** az Oszlopválasztó bezárásához.

    ![Egy oszlop kizárása](./media/ui-quickstart-run-experiment/exclude-column.png)
        
    Most a Tulajdonságok panelen a Select Columns in Dataset jelzi, hogy ez lesz az összes oszlop kivételével az adathalmaz **normalized-losses**.
        
    A Tulajdonságok panelen látható, amely a **normalized-losses** oszlop ki van zárva.
        
    ![Tulajdonság ablaktábla](./media/ui-quickstart-run-experiment/property-pane.png)
        
    A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. 

1. Kattintson duplán a **Select Columns in Dataset** modul, és írja be a "normalized losses oszlop kizárása." 
    
    A Megjegyzés, kattintson a modul kívül.  A lefelé mutató nyílra jelenik meg, hogy a modul tartalmazza a Megjegyzés megjelenítése.

1. Kattintson a lefelé mutató nyilat a megjegyzés megjelenítéséhez.

    A modul most egy felfelé mutató nyílra a Megjegyzés elrejtése jeleníti meg.
        
    ![Megjegyzések](./media/ui-quickstart-run-experiment/comments.png)

### <a name="clean-missing-data"></a>Hiányzó adatok tisztítása

Adjon hozzá egy másik modul, amely eltávolítja az összes többi sort, amelyből adatok hiányoznak.

1. Típus **tisztaként** a keresőmezőbe keresése a **Clean Missing Data** modul.

1. Húzza a **Clean Missing Data** modult a kísérletvászonra, és kösse össze a **Select Columns in Dataset** modul. 

1. A Tulajdonságok panelen válassza ki a **teljes sor eltávolítása** alatt **Remove Entire Row mód**.

    Ezeket a beállításokat a közvetlen **Clean Missing Data** , amely tartalmaz a hiányzó sort törölje.

1. Kattintson duplán a modulra, és írja be a következő megjegyzést: „Hiányzó értéket tartalmazó sorok törlése”.
 
    ![Sorok eltávolítása](./media/ui-quickstart-run-experiment/remove-rows.png)

    A kísérlet kell kinéznie ehhez hasonló:
    
    ![Oszlop kiválasztása](./media/ui-quickstart-run-experiment/experiment-clean.png)

## <a name="visualize-the-results"></a>Az eredmények megjelenítése

Végrehajtott módosítások a modulok a kísérlet során, mivel a állapota megváltozott "A vázlat".  Az új tiszta adatainak megjelenítése, először kell ismét lefuttathatja a kísérletet.

1. Válassza ki **futtatása** alján futtathatja a kísérletet.

    Ezúttal újból felhasználhatja a számítási célnak a korábban létrehozott.  

1. Válassza ki **futtatása** a párbeszédpanelen.

   ![Kísérlet futtatása](./media/ui-quickstart-run-experiment/select-compute.png)

1. Ha a Futtatás befejeződött, kattintson a jobb gombbal a **Clean Missing Data** modul új tiszta adatok vizualizálásához.  

    ![Az adatok tisztítása megjelenítése](./media/ui-quickstart-run-experiment/visualize-cleaned.png)

1. Kattintson a különböző oszlopból a megtisztított adatok ablakban megtekintheti, hogyan adatok megváltoztak.  

    ![Az adatok tisztítása megjelenítése](media/ui-quickstart-run-experiment/visualize-result.png)

    Jelenleg 193 sorok és oszlopok 25.

    Amikor rákattint a **num-az-ajtók** még 2 egyedi értékeket, de a hiányzó értékek 0 most már megjelenik.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

- Adja hozzá, és az adatok előnézetének megtekintéséhez az első kísérlet létrehozása
- Eltávolítja a hiányzó értékeket az adatok előkészítése
- Az eredményül kapott adatok megjelenítése

Továbbra is az oktatóanyag az adatok felhasználásához előrejelzésére egy autó árát.

> [!div class="nextstepaction"]
> [Oktatóanyag: Vizuális felhasználói felülettel autó árát előrejelzése](ui-tutorial-automobile-price-train-score.md)
