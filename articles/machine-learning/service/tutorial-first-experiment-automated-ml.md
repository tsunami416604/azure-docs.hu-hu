---
title: Az első automatizált gépi tanulási kísérlet létrehozása
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan lehet betanítani és üzembe helyezni egy besorolási modellt automatizált gépi tanulással a Azure Portalban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 30dc731efdb6016f505b7a16860e0cb9c6480333
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716584"
---
# <a name="tutorial-use-automated-machine-learning-to-train-and-deploy-your-first-classification-model-preview"></a>Oktatóanyag: Automatikus gépi tanulás használata az első besorolási modell betanításához és üzembe helyezéséhez (előzetes verzió)

Ebből az oktatóanyagból megtudhatja, hogyan hozhatja létre első automatizált gépi tanulási kísérletét a Azure Portal. Ez a példa egy besorolási modellt hoz létre, amely azt jelzi, hogy az ügyfél előfizet-e egy, a banknál befizetett időszakra. 

A szolgáltatás és a Azure Portal gépi tanulási képességeinek használatával elindítja az automatizált gépi tanulási folyamatot, és az algoritmus kiválasztása és a hiperparaméter finomhangolása is megtörténik. Az automatizált gépi tanulási technika az algoritmusok és a hiperparaméterek beállítása számos kombinációjára támaszkodik, egészen addig, amíg meg nem találja a saját feltétel alapján a legjobb modellt, mindezt anélkül, hogy egyetlen sor kódot kellene írnia.

Ez az oktatóanyag a következő feladatokat ismerteti:

> [!div class="checklist"]
> * Azure Machine Learning szolgáltatás munkaterületének konfigurálása.
> * Hozzon létre egy kísérletet.
> * Egy besorolási modell automatikus betanítása.
> * Tekintse meg a tanítási Futtatás részleteit.
> * A modell üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* A **bankmarketing_train. csv** fájl adatfájlja. Töltse le [Itt](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

## <a name="create-a-workspace"></a>Munkaterület létrehozása

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Kísérlet létrehozása

1. Navigáljon a munkaterület bal oldali ablaktáblájához. Válassza az **automatikus Machine learning** a **szerzői műveletek (előzetes verzió)** szakaszban.

    ![Azure Portal navigációs ablaktábla](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    Mivel ez az első kísérlet az automatizált Machine Learningekkel, az **automatikus Machine learning** képernyőn láthatja az üdvözlő képernyőt. 

1.  Válassza a **kísérlet létrehozása**lehetőséget. Ezután írja be a **My-1st-automl-Experiment** nevet a kísérlet neveként.

1. Válassza a **Létrehozás új számítás** lehetőséget, és konfigurálja a számítási környezetet a kísérlethez.

    Mező| Value
    ---|---
    A számítás neve| Adjon meg egy egyedi nevet, amely azonosítja a számítási környezetet. Ebben a példában a **automl-számítást**használjuk.
    Virtuális gép mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz. **Standard_DS12_V2**használunk.
    További beállítások| *Minimális csomópont*: 1. Az adatprofilkészítés engedélyezéséhez legalább 1 csomópontnak kell lennie. <br> *Csomópont maximális*száma: 6. 

    Az új számítási módszer létrehozásának megkezdéséhez válassza a **Létrehozás**lehetőséget. Ez néhány percet is igénybe vehet. 

    A létrehozás befejezése után válassza ki az új számítási lehetőséget a legördülő listából, majd kattintson a **tovább**gombra.

1. Ebben az oktatóanyagban az új számítási feladatokkal létrehozott alapértelmezett Storage-fiókot és-tárolót használjuk. Ez automatikusan kitöltődik az űrlapon.

1. Válassza a **feltöltés** lehetőséget a **bankmarketing_train. csv** fájl kiválasztásához a helyi számítógépről, majd töltse fel az alapértelmezett tárolóba. A nyilvános előzetes verzió csak a helyi fájlok feltöltését és az Azure Blob Storage fiókokat támogatja. Ha a feltöltés befejeződött, válassza ki a fájlt a listából. 

    [![Adatfájl kiválasztása](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. Az **előnézet** lapon további beállítások konfigurálhatók a kísérlethez.

    Az Előnézet lapon jelezze, hogy az adatai fejléceket tartalmaznak. A szolgáltatás alapértelmezés szerint a betanítás összes funkcióját (oszlopát) is tartalmazza. Ebben a példában görgessen jobbra, és **hagyja figyelmen kívül** a **day_of_week** funkciót.

    ![Előnézet lap konfigurációja](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > Az adatprofilkészítés nem érhető el olyan számítási feladatokkal, amelyek legalább 0 csomóponttal rendelkeznek.

1. Előrejelzési feladatként válassza a **besorolás** lehetőséget.

1. Válassza az **y** elemet a cél oszlopként, az oszlopot, amelyeken a jóslatokat szeretnénk elvégezni. Ebben az oszlopban látható, hogy az ügyfél előfizetett-e egy lejárati időszakra.

1. Bontsa ki a **speciális beállításokat** , és töltse fel a mezőket az alábbiak szerint.

    Speciális beállítások|Value
    ------|------
    Elsődleges metrika| AUC_weighted 
    Kilépési feltételek| Ha bármelyik feltétel teljesül, a betanítási feladatok a teljes befejezés előtt véget ér. <br> *Tanítási feladatok időpontja (perc)* : 5  <br> *Ismétlések maximális száma*: 10 
    Előfeldolgozás| Az automatikus gépi tanulás lehetővé teszi az előfeldolgozást. Ez magában foglalja az automatikus adattisztítást, előkészítést és átalakítást a szintetikus funkciók létrehozásához.
    Érvényesítés| Válassza a K-szor Cross validate és a 2 értéket a keresztek érvényességének számához. 
    Egyidejűség| Az egyidejű ismétlések maximális számához válassza az 5 értéket.

   >[!NOTE]
   > Ebben a kísérletben nem állítunk be mérőszám-vagy maximális iterációs küszöbértéket, és nem blokkolja az algoritmusok tesztelését.

1. A kísérlet futtatásához kattintson a **Start** gombra.

   A kísérlet elkezdése után egy üres **futtatási részlet** képernyő jelenik meg, amelyen felül a következő állapot látható. A kísérlet előkészítési folyamata néhány percet vesz igénybe. Amikor az előkészítési folyamat befejeződik, a **rendszer**futtatja az állapotjelző üzenet futtatását.

      ![Előkészítés futtatása](media/tutorial-1st-experiment-automated-ml/run-preparing.png)

##  <a name="view-experiment-details"></a>Kísérlet részleteinek megtekintése

A kísérlet előrehaladása esetén a **Futtatás részletei** képernyő frissíti az iterációs diagramot és a listát a futtatott különböző iterációkkal (modellekkel). Az iterációk listáját a mérőszámok pontszáma határozza meg, és alapértelmezés szerint a **AUC_weighted** metrika alapján a legmagasabb pontszámot eredményező modell a lista tetején található.

>[!TIP]
> A betanítási feladatok elvégzése több percet is igénybe vehet, hogy minden folyamat fusson.

[![Futtatás részletei irányítópult](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-model"></a>Modell üzembe helyezése

Ebben a kísérletben a **VotingEnsemble** a **AUC_weighted** metrika alapján a legjobb modellnek számít. A Azure Portal automatikus gépi tanulásával a modellt webszolgáltatásként helyezheti üzembe, amely egy kattintással előre jelezheti az új adatforrásokat. 

1. A **Futtatás részletei** lapon válassza a **legjobb modell üzembe helyezése** gombot.

1. Töltse fel a **legjobb modell üzembe helyezése** panelt az alábbiak szerint.

    Mező| Érték
    ----|----
    Üzemelő példány neve| My-automl – üzembe helyezés
    Központi telepítés leírása| Az első automatizált gépi tanulási kísérlet üzembe helyezése
    Pontozási parancsfájl| Autogenerate
    Környezeti parancsfájl| Autogenerate
    
1. Válassza az **Üzembe helyezés** lehetőséget. Az üzembe helyezés körülbelül 20 percet vesz igénybe.

    A telepítés sikeres befejeződése után a következő üzenet jelenik meg.

    ![A telepítés befejeződött](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    Ennyi az egész! Van egy operatív webszolgáltatás az előrejelzések létrehozásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az üzembe helyezési fájlok nagyobb méretűek, mint az adatmennyiség és a kísérlet során használt fájlok. Csak a központi telepítési fájlok törlésével csökkentheti a költségeket a fiókjához, és ha szeretné megőrizni a munkaterületet és a kísérletet. Ellenkező esetben törölje a teljes erőforráscsoportot, ha nem tervezi az egyik fájl használatát.  

### <a name="delete-deployment-instance"></a>Központi telepítési példány törlése

Ha az erőforráscsoportot és a munkaterületet más oktatóanyagok és feltárás céljából szeretné megtartani, törölje a Azure Portal csak a telepítési példányt. 

1. Navigáljon a bal oldali **eszközök** panelre, és válassza a **központi telepítések**lehetőséget. 

1. Válassza ki a törölni kívánt központi telepítést, és válassza a **Törlés**lehetőséget. 

1. Válassza a **Folytatás**lehetőséget.

### <a name="delete-resource-group"></a>Erőforráscsoport törlése

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben az automatizált gépi tanulási oktatóanyagban a Azure Portal használatával létrehozhatja és üzembe helyezheti a besorolási modellt. További információkat és további lépéseket a következő cikkekben talál.

+ [Webszolgáltatás](how-to-consume-web-service.md)használata.
+ További információ az [](how-to-create-portal-experiments.md#preprocess)előfeldolgozásról.
+ További információ az [](how-to-create-portal-experiments.md#profile)adatprofilkészítésről.
+ További információ az [automatizált gépi tanulásról](concept-automated-ml.md).

>[!NOTE]
> Ez a bank marketing-adatkészlet elérhető a [Creative Commons (CCO: Nyilvános tartomány) licenc](https://creativecommons.org/publicdomain/zero/1.0/). Az adatbázis egyéni tartalmában minden jogosultság az [adatbázis tartalma licenc](https://creativecommons.org/publicdomain/zero/1.0/) alatt található, és a [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)címen érhető el. Ez az adathalmaz eredetileg az [UCI Machine learning adatbázisban](https://archive.ics.uci.edu/ml/datasets/bank+marketing)érhető el.<br><br>
>  Adja meg a következő munkát: <br> [Moro et al., 2014] S. Moro, P. Cortez és P. Rita. Adatvezérelt megközelítés a banki telemarketing sikerességének előrejelzéséhez. Döntési támogatási rendszerek, Elsevier, 62:22-31, június 2014.
