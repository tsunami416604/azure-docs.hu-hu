---
title: Az első automatizált gépi tanulási kísérlet létrehozása
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan lehet betanítani és üzembe helyezni egy besorolási modellt automatizált gépi tanulással a Azure Portalban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 08/14/2019
ms.openlocfilehash: e53cd92a9dfd8f823918fb38e14c2b73c2ce071f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534426"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Oktatóanyag: Az első besorolási modell létrehozása automatizált gépi tanulással

Ebből az oktatóanyagból megtudhatja, hogyan hozhatja létre első automatizált gépi tanulási kísérletét a Azure Portalban (előzetes verzió) anélkül, hogy egyetlen sor kódot kellene írnia. Ez a példa egy besorolási modellt hoz létre, amely azt jelzi, hogy az ügyfél egy pénzügyi intézmény által rögzített értékű letétbe kerül.

A Azure Machine Learning szolgáltatás és a Azure Portal automatizált gépi tanulási képességeinek használatával megkezdheti az automatizált gépi tanulási folyamatot. Az algoritmus kiválasztása és a hiperparaméter finomhangolása az Ön számára történik. Az automatizált gépi tanulási módszer az algoritmusok és a hiperparaméterek beállítása számos kombinációjára támaszkodik, amíg meg nem találja a legjobb modellt a feltétele alapján.

Ez az oktatóanyag a következő feladatokat ismerteti:

> [!div class="checklist"]
> * Azure Machine Learning szolgáltatás munkaterületének konfigurálása.
> * Hozzon létre egy kísérletet.
> * Egy besorolási modell automatikus betanítása.
> * Tekintse meg a tanítási Futtatás részleteit.
> * A modell üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).

* Töltse le az [ **bankmarketing_train. csv** ](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) adatfájlt. Az **y** oszlop azt jelzi, hogy az ügyfél egy rögzített lejáratú befizetésre fizetett-e, amelyet később a jelen oktatóanyagban megjelenő előrejelzések céljának oszlopa azonosítottak. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Ezek a lépések végigvezetik az adatok kiválasztásának beállításán az elsődleges metrika és a modell típusának kiválasztásához. 

1. Nyissa meg a munkaterület bal oldali paneljét. Válassza az **automatikus gépi tanulás** lehetőséget az **authoring (előzetes verzió)** szakaszban.
Ekkor megjelenik az **automatikus Machine learning** képernyő, mivel ez az első kísérlet az automatizált Machine learning.

    ![Azure Portal navigációs ablaktábla](media/tutorial-1st-experiment-automated-ml/nav-pane.png)



1. Válassza a **kísérlet létrehozása**lehetőséget. Ezután írja be a **My-1st-automl-Experiment** nevet a kísérlet neveként.

1. Válassza a **Létrehozás új számítás** lehetőséget, és konfigurálja a számítási környezetet a kísérlethez.

    Mező| Value
    ---|---
    A számítás neve| Adjon meg egy egyedi nevet, amely azonosítja a számítási környezetet. Ebben a példában a **automl-számítást**használjuk.
    Virtuális gép mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz. **Standard_DS12_V2**használunk.
    További beállítások| *Minimális csomópont*: 1. Az adatprofilkészítés engedélyezéséhez legalább egy csomóponttal kell rendelkeznie. <br> *Csomópont maximális*száma: 6. 

    Az új számítási számítás létrehozásához válassza a **Létrehozás**lehetőséget. Ez néhány percet vesz igénybe. 

    A létrehozás befejeződése után válassza ki az új számítást a legördülő listából, majd kattintson a **tovább**gombra.

    >[!NOTE]
    >Ebben az oktatóanyagban az új számítási feladatokkal létrehozott alapértelmezett Storage-fiókot és-tárolót használjuk. Ezek automatikusan feltöltve lesznek az űrlapon.

1. Válassza a **feltöltés** lehetőséget, és válassza ki a **bankmarketing_train. csv** fájlt a helyi számítógépről, hogy feltöltse az alapértelmezett tárolóba. A nyilvános előzetes verzió csak a helyi fájlok feltöltését és az Azure Blob Storage-fiókokat támogatja. Ha a feltöltés befejeződött, válassza ki a fájlt a listából. 

    [![Adatfájl kiválasztása](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. Az **előnézet** lapon további beállítások konfigurálhatók a kísérlethez.

    Az **előnézet** lapon jelezze, hogy az adatai fejléceket tartalmaznak. A szolgáltatás alapértelmezés szerint tartalmazza a betanítás összes funkcióját (oszlopát). Ebben a példában görgessen jobbra, és **hagyja figyelmen kívül** a **day_of_week** funkciót.

    ![Előnézet lap konfigurációja](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > Az adatprofilkészítés nem érhető el olyan számítási feladatokkal, amelyek nulla minimális csomóponttal rendelkeznek.

1. Előrejelzési feladatként válassza a **besorolás** lehetőséget.

1. Válassza az **y** elemet a cél oszlopként, ahol a jóslatokat szeretnénk elvégezni. Ebben az oszlopban látható, hogy az ügyfél előfizetett-e egy lejárati időszakra.

1. Bontsa ki a **Speciális beállítások** elemet, és töltse fel a mezőket az alábbiak szerint.

    Speciális beállítások|Value
    ------|------
    Elsődleges metrika| AUC_weighted 
    Kilépési feltételek| Ha bármelyik feltétel teljesül, a betanítási feladatok a teljes befejezés előtt véget ér: <br> *Tanítási feladatok időpontja (perc)* : 5  <br> *Ismétlések maximális száma*: 10 
    Előfeldolgozás| Lehetővé teszi az automatikus gépi tanulás által végzett előfeldolgozást. Ez magában foglalja az automatikus adattisztítást, előkészítést és átalakítást a szintetikus funkciók létrehozásához.
    Érvényesítés| Válassza a K-fold kereszt-érvényesítés lehetőséget, és a **2** értéket a kereszthivatkozások számához. 
    Egyidejűség| Az egyidejű ismétlések maximális számának kiválasztásához válassza az **5** értéket.

   >[!NOTE]
   > Ebben a kísérletben nem állítunk be mérőszámok vagy Max magok másodpercenkénti küszöbértékét. Nem tiltjuk le az algoritmusok tesztelését is.

1. A kísérlet futtatásához kattintson a **Start** gombra.

   A kísérlet elindulásakor megjelenik egy üres **futtatási részlet** képernyő, amelyen felül a következő állapot látható. 

      ![Előkészítés futtatása](media/tutorial-1st-experiment-automated-ml/run-preparing.png)
      
A kísérlet előkészítési folyamata néhány percet vesz igénybe. Ha a folyamat befejeződik, a **rendszer**futtatja az állapotjelző üzenet futtatását.

##  <a name="view-experiment-details"></a>Kísérlet részleteinek megtekintése

A kísérlet előrehaladása esetén a **Futtatás részletei** képernyő frissíti az iterációs diagramot és a listát a futtatott különböző iterációkkal (modellekkel). Az iterációk listája metrikai pontszám szerint van megrendezve. Alapértelmezés szerint a **AUC_weighted** metrika alapján a legmagasabb pontszám a lista tetején található.

>[!TIP]
> A betanítási feladatok elvégzése több percet is igénybe vehet, hogy minden folyamat fusson.

[![Futtatás részletei irányítópult](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>A modell üzembe helyezése

A Azure Portal automatizált gépi tanulás alkalmazásával a legjobb modellt üzembe helyezheti webszolgáltatásként, amely előre jelezheti az új adatmennyiséget, és azonosíthatja a lehetséges területeket. Ebben a kísérletben az üzembe helyezés azt jelenti, hogy a pénzügyi intézmény most már rendelkezik egy iterációs és méretezhető megoldással a lehetséges rögzített lejáratú ügyfelek azonosításához.

Ebben a kísérleti kontextusban a **VotingEnsemble** a **AUC_weighted** metrika alapján a legjobb modellnek számít.  Ezt a modellt üzembe helyezjük, de javasoljuk, hogy az üzembe helyezés körülbelül 20 percet vesz igénybe.

1. A **Futtatás részletei** lapon válassza a **legjobb modell üzembe helyezése** gombot.

1. Töltse fel a **legjobb modell üzembe helyezése** panelt az alábbiak szerint:

    Mező| Value
    ----|----
    Üzemelő példány neve| My-automl – üzembe helyezés
    Központi telepítés leírása| Az első automatizált gépi tanulási kísérlet üzembe helyezése
    Pontozási parancsfájl| Autogenerate
    Környezeti parancsfájl| Autogenerate
    
1. Válassza az **Üzembe helyezés** lehetőséget.

    Az üzembe helyezés sikeres befejeződése után a következő üzenet jelenik meg:

    ![Az üzembe helyezés befejeződött](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    Most már rendelkezik egy operatív webszolgáltatással előrejelzések létrehozásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az üzembe helyezési fájlok nagyobb méretűek, mint az adatmennyiség és a kísérlet során használt fájlok. Csak a központi telepítési fájlokat törölje, hogy csökkentse a költségeket a fiókjához, vagy ha meg szeretné őrizni a munkaterületet és a kísérletet. Ellenkező esetben törölje a teljes erőforráscsoportot, ha nem tervezi az egyik fájl használatát.  

### <a name="delete-the-deployment-instance"></a>A központi telepítési példány törlése

Ha az erőforráscsoportot és a munkaterületet más oktatóanyagok és feltárás céljából szeretné megtartani, törölje a Azure Portal csak a telepítési példányt. 

1. Nyissa meg a bal oldali **eszközök** ablaktáblát, és válassza a **központi telepítések**lehetőséget. 

1. Válassza ki a törölni kívánt központi telepítést, és válassza a **Törlés**lehetőséget. 

1. Válassza a **Folytatás**lehetőséget.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben az automatizált gépi tanulási oktatóanyagban a Azure Portal használatával létrehozhatja és üzembe helyezheti a besorolási modellt. További információkat és további lépéseket a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Webszolgáltatás felhasználása](how-to-consume-web-service.md)


+ További információ az [](how-to-create-portal-experiments.md#preprocess)előfeldolgozásról.
+ További információ az [](how-to-create-portal-experiments.md#profile)adatprofilkészítésről.
+ További információ az [automatizált gépi tanulásról](concept-automated-ml.md).

>[!NOTE]
> Ez a bank marketing-adatkészlet elérhető a [Creative Commons (CCO: Nyilvános tartomány) licenc](https://creativecommons.org/publicdomain/zero/1.0/). Az adatbázis egyéni tartalmában minden jogosultság az [adatbázis tartalma licenc](https://creativecommons.org/publicdomain/zero/1.0/) alatt érhető el, és elérhető a [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)-on. Ez az adatkészlet eredetileg az [UCI Machine learning adatbázisban](https://archive.ics.uci.edu/ml/datasets/bank+marketing)volt elérhető.<br><br>
> Adja meg a következő munkát: <br> [Moro et al., 2014] S. Moro, P. Cortez és P. Rita. Adatvezérelt megközelítés a banki telemarketing sikerességének előrejelzéséhez. Döntési támogatási rendszerek, Elsevier, 62:22-31, június 2014.
