---
title: Az első automatizált gépi tanulási kísérlet létrehozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan taníthat és helyezhet üzembe egy besorolási modellt automatikus gépi tanulással Azure Machine Learning munkaterület-kezdőlapján (előzetes verzió).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 2422a4525c94f3997dd0a9a0859135e9acf59ffa
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092009"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Oktatóanyag: Az első besorolási modell létrehozása automatizált gépi tanulással

Ebből az oktatóanyagból megtudhatja, hogyan hozhatja létre első automatizált gépi tanulási kísérletét a munkaterület kezdőlapján (előzetes verzió) anélkül, hogy egyetlen sor kódot kellene írnia. Ez a példa egy besorolási modellt hoz létre, amely azt jelzi, hogy az ügyfél egy pénzügyi intézmény által rögzített értékű letétbe kerül.

Az automatizált gépi tanulás segítségével automatizálhatja az időigényes feladatokat. Az automatizált gépi tanulás gyorsan megismétli az algoritmusok és hiperparaméterek beállítása számos kombinációját, így könnyebben megtalálhatja a legjobb modellt a választott sikerességi mérőszám alapján.

Ebből az oktatóanyagból megtudhatja, hogyan hajthatja végre a következő feladatokat:

> [!div class="checklist"]
> * Az Azure Machine Learning-munkaterület létrehozása.
> * Futtasson automatizált gépi tanulási kísérletet.
> * A kísérlet részleteinek megtekintése.
> * A modell üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).

* Töltse le az [**bankmarketing_train. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) adatfájlt. Az **y** oszlop azt jelzi, hogy az ügyfél egy rögzített lejáratú befizetésre fizetett-e, amelyet később a jelen oktatóanyagban megjelenő előrejelzések céljának oszlopa azonosítottak. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning munkaterület a felhőben található alapvető erőforrás, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használható. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi a szolgáltatásban. 

A munkaterületet az Azure-erőforrások kezeléséhez használható webalapú konzolon Azure Portal segítségével hozhatja létre. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Jegyezze fel a **munkaterületet** és az **előfizetést**. Ezekre azért van szükség, hogy a megfelelő helyen hozza létre a kísérletet. 

## <a name="create-and-run-the-experiment"></a>A kísérlet létrehozása és futtatása

A következő kísérlet beállítása és futtatása lépések a munkaterület kezdőlapján, egy összevont felület, amely magában foglalja a gépi tanulási eszközöket, amelyekkel adatelemzési forgatókönyvek készíthetők az összes képzettségi szint adatelemző szakemberek számára.

1. Jelentkezzen be a [munkaterület](https://ml.azure.com/workspaceportal/)kezdőlapján.

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

1. Válassza az első **lépések**lehetőséget.

1.  A bal oldali ablaktábla **authoring (szerzői műveletek** ) szakaszában válassza az **automatikus ml** lehetőséget.
Ekkor megjelenik a **kezdeti lépések** képernyő, mivel ez az első kísérlet az automatizált Machine learning.

    ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Válassza a **kísérlet létrehozása**lehetőséget. 

1. A kísérlet neveként adja meg **a My-1st-automl-Experiment** értéket.

1. Válassza **az új számítás létrehozása**lehetőséget. A számítási módszer egy helyi vagy felhőalapú erőforrás-környezet, amely a betanítási parancsfájl futtatására vagy a szolgáltatás központi telepítésének üzemeltetésére szolgál. Ehhez a kísérlethez felhőalapú számítást használunk. 

    1. A számítási környezet konfigurálása ehhez a kísérlethez.
        
        Mező | Value
        ----|---
        A számítás neve |  Adjon meg egy egyedi nevet, amely azonosítja a számítási környezetet. Ebben a példában a **automl-számítást**használjuk.
        Virtuális gép mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz. Használja az alapértelmezett **Standard_DS12_V2**.
        További beállítások| *Minimális csomópont*: 1. Az adatprofilkészítés engedélyezéséhez legalább egy csomóponttal kell rendelkeznie. <br> *Csomópont maximális*száma: 6.
 
    1. Az új számítási számítás létrehozásához válassza a **Létrehozás**lehetőséget. Ez eltarthat néhány percet. 

    1. A létrehozás befejeződése után válassza ki az új számítást a legördülő listából, majd kattintson a **tovább**gombra.

    >[!NOTE]
    >Ebben az oktatóanyagban az új számítási feladatokkal létrehozott alapértelmezett Storage-fiókot és-tárolót fogja használni. Ezek automatikusan feltöltve lesznek az űrlapon.

1. Válassza **a feltöltés helyi fájlból**lehetőséget. Innen létrehoz egy új adatkészletet az oktatóanyaghoz korábban letöltött **bankmarketing_train. csv** fájllal. 

    1. Válassza a **Tallózás** lehetőséget, majd válassza ki a **bankmarketing_train. csv** fájlt a helyi számítógépen. 

    1. Adjon egyedi nevet az adatkészletnek, és adjon meg egy opcionális leírást. 

    1. Válassza a **tovább** lehetőséget a munkaterület létrehozása során automatikusan beállított alapértelmezett tárolóba való feltöltéshez. A nyilvános előzetes verzió csak a helyi fájlok feltöltését támogatja. 

    1. A feltöltés befejezésekor a rendszer a fájltípus alapján intelligens módon tölti ki a **beállításokat és az előnézet** űrlapot. Győződjön meg arról, hogy az űrlap a következőképpen van feltöltve.
        
        Mező|Value
        ---|---
        Fájl formátuma| Tagolt
        Elválasztó karakter| Vessző
        Encoding| UTF-8
        Oszlopfejlécek| Minden fájlnak azonos fejléce van
        Sorok kihagyása | Nincsenek

        >[!NOTE]
        > Ha az űrlap bármelyik beállítása frissül, az előzetes verzió frissítése ennek megfelelően történik.

        Kattintson a **Tovább** gombra.
    

    1. A **séma** űrlap lehetővé teszi az adatai további konfigurálását a kísérlethez. Ebben a példában válassza ki a **day_of_week** funkció váltási kapcsolóját, hogy ne tartalmazza azt a kísérlethez. Válassza a **kész**lehetőséget, hogy elvégezze a fájl feltöltését és a kísérlethez tartozó adatkészlet létrehozását.

        ![Előnézet lap konfigurációja](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

        
1. Előrejelzési feladatként válassza a **besorolás** lehetőséget.

1. Válassza az **y** elemet a cél oszlopként, amit meg szeretne jósolni. Ebben az oszlopban látható, hogy az ügyfél előfizetett-e egy lejárati időszakra.

1. Bontsa ki a **Speciális beállítások** elemet, és töltse fel a mezőket az alábbiak szerint.

    Speciális beállítások|Value
    ------|------
    Elsődleges metrika| AUC_weighted 
    Kilépési feltételek| Ha bármelyik feltétel teljesül, a betanítási feladatok a teljes befejezés előtt véget ér: <br> *Tanítási feladatok időpontja (perc)* : 5  <br> *Ismétlések maximális száma*: 10 
    Előfeldolgozás| Lehetővé teszi az automatikus gépi tanulás által végzett előfeldolgozást. Ez magában foglalja az automatikus adattisztítást, előkészítést és átalakítást a szintetikus funkciók létrehozásához.
    Érvényesítés| Válassza a K-fold kereszt-érvényesítés lehetőséget, és a **2** értéket a kereszthivatkozások számához. 
    Egyidejűség| Az egyidejű ismétlések maximális számának kiválasztásához válassza az **5** értéket.

   >[!NOTE]
   > Ebben a kísérletben nem állítanak be mérőszámok vagy Max magok másodpercenkénti küszöbértékét. Nem blokkolja az algoritmusok tesztelését is.

1. A kísérlet futtatásához kattintson a **Start** gombra.

   A kísérlet elindulásakor egy üres képernyő jelenik meg, amelyen az állapotjelző üzenet jelenik meg felül.

A kísérlet előkészítési folyamata több percet is igénybe vehet. Ha ez a folyamat befejeződik, a rendszer futtatja az állapotjelző üzenet **futtatását**.

##  <a name="view-experiment-details"></a>Kísérlet részleteinek megtekintése

A kísérlet előrehaladásakor a képernyő frissíti az **iterációs diagramot** és az **iterációs listát** a futtatott különböző iterációkkal (modellekkel). Az iterációk listája metrikai pontszám szerint van megrendezve. Alapértelmezés szerint a **AUC_weighted** metrika alapján a legmagasabb pontszám a lista tetején található.

>[!WARNING]
> A betanítási feladatok elvégzése több percet is igénybe vehet, hogy minden folyamat fusson.

[![Futtatás részletei irányítópult](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Az automatizált gépi tanulás a munkaterület kezdőlapján a legjobb modellt webszolgáltatásként helyezheti üzembe néhány lépésben. Az üzembe helyezés a modell integrációja, így előre jelezhető az új adatmennyiség, és azonosíthatók a lehetséges lehetőségek is. Ebben a kísérletben a webszolgáltatások üzembe helyezése azt jelenti, hogy a pénzügyi intézmény immár egy iterációs és méretezhető webes megoldást kínál a lehetséges rögzített lejáratú ügyfelek azonosítására. 

Ebben a kísérleti kontextusban a **VotingEnsemble** a **AUC_weighted** metrika alapján a legjobb modellnek számít.  Ezt a modellt üzembe helyezjük, de javasoljuk, hogy az üzembe helyezés körülbelül 20 percet vesz igénybe. Az üzembe helyezési folyamat több lépést is magában foglal, beleértve a modell regisztrálását, az erőforrások létrehozását és a webszolgáltatás konfigurálását.

1. A **Futtatás részletei** lapon válassza a **legjobb modell üzembe helyezése** gombot a jobb felső sarokban.

1. Töltse fel a **legjobb modell üzembe helyezése** panelt az alábbiak szerint:

    Mező| Value
    ----|----
    Üzemelő példány neve| My-automl – üzembe helyezés
    Központi telepítés leírása| Az első automatizált gépi tanulási kísérlet üzembe helyezése
    Pontozási parancsfájl| Autogenerate
    Környezeti parancsfájl| Autogenerate
    
1. Válassza az **Üzembe helyezés** lehetőséget.  

    Az üzembe helyezés sikeres befejeződése után megjelenik egy üzembe helyezési teljes üzenet.
    
Most már rendelkezik egy operatív webszolgáltatással előrejelzések létrehozásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az üzembe helyezési fájlok nagyobb méretűek, mint az adatmennyiség és a kísérlet során használt fájlok. Csak a központi telepítési fájlokat törölje, hogy csökkentse a költségeket a fiókjához, vagy ha meg szeretné őrizni a munkaterületet és a kísérletet. Ellenkező esetben törölje a teljes erőforráscsoportot, ha nem tervezi az egyik fájl használatát.  

### <a name="delete-the-deployment-instance"></a>A központi telepítési példány törlése

Ha az erőforráscsoportot és a munkaterületet más oktatóanyagok és feltárás céljából szeretné megtartani, törölje a Azure Portal csak a telepítési példányt. 

1. Nyissa meg az [Azure Portal](https://portal.azure.com//). Navigáljon a munkaterülethez, és a bal oldalon az **eszközök** ablaktáblán válassza a **központi telepítések**lehetőséget. 

1. Válassza ki a törölni kívánt központi telepítést, és válassza a **Törlés**lehetőséget. 

1. Válassza a **Folytatás**lehetőséget.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben az automatizált gépi tanulási oktatóanyagban a munkaterület kezdőlapját használta egy besorolási modell létrehozásához és üzembe helyezéséhez. További információkat és további lépéseket a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Webszolgáltatás felhasználása](how-to-consume-web-service.md)

+ További információ az [előfeldolgozásról](how-to-create-portal-experiments.md#preprocess).
+ További információ az [adatprofilkészítésről](how-to-create-portal-experiments.md#profile).
+ További információ az [automatizált gépi tanulásról](concept-automated-ml.md).

>[!NOTE]
> Ez a bank marketing-adatkészlet elérhető a [Creative Commons (CCO: Nyilvános tartomány) licenc](https://creativecommons.org/publicdomain/zero/1.0/). Az adatbázis egyéni tartalmában minden jogosultság az [adatbázis tartalma licenc](https://creativecommons.org/publicdomain/zero/1.0/) alatt érhető el, és elérhető a [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)-on. Ez az adatkészlet eredetileg az [UCI Machine learning adatbázisban](https://archive.ics.uci.edu/ml/datasets/bank+marketing)volt elérhető.<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez és P. Rita. Adatvezérelt megközelítés a banki telemarketing sikerességének előrejelzéséhez. Döntési támogatási rendszerek, Elsevier, 62:22-31, június 2014.
