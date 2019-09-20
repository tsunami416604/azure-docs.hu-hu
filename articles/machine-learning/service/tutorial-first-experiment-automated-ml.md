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
ms.openlocfilehash: 8d91768d46d3e4a793982418da91f2d1877c5a79
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162547"
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

1. Adja meg a kísérlet nevét:`my-1st-automl-experiment`

1. Válassza az **új számítás létrehozása** és a számítási cél konfigurálása lehetőséget. A számítási cél egy helyi vagy felhőalapú erőforrás-környezet, amely a betanítási parancsfájl futtatására vagy a szolgáltatás központi telepítésének üzemeltetésére szolgál. Ehhez a kísérlethez felhőalapú számítást használunk. 

   Mező | Leírás | Az oktatóanyag értéke
   ----|---|---
   A számítás neve |A számítási környezet azonosítására szolgáló egyedi név.|automl – számítás
   Virtuális&nbsp;gép&nbsp;mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz.|Standard_DS12_V2
   Csomópontok minimális/maximális száma (speciális beállításokban)| A profilhoz legalább 1 csomópontot kell megadnia.|Minimális csomópontok: 1<br>Csomópontok maximális száma: 6

   >[!NOTE]
   >Ebben az oktatóanyagban az új számítási feladatokkal létrehozott alapértelmezett Storage-fiókot és-tárolót fogja használni. Ezek automatikusan feltöltve lesznek az űrlapon.
    
1. A számítási cél beszerzéséhez válassza a **Létrehozás** lehetőséget. 
   **Ez eltarthat néhány percet.** 

1. A létrehozás után válassza ki az új számítási célt a legördülő listából, és kattintson a **tovább**gombra.

1. Új adatkészlet létrehozásának megkezdéséhez válassza **a feltöltés helyi fájlból** lehetőséget. 

    1. Válassza a **Tallózás** lehetőséget.
    
    1. Válassza ki a **bankmarketing_train. csv** fájlt a helyi számítógépen. Ez az [előfeltételként](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)letöltött fájl.

    1. Adjon egyedi nevet az adatkészletnek, és adjon meg egy opcionális leírást. 

    1. A bal alsó sarokban kattintson a **tovább** gombra, hogy feltöltse azt az alapértelmezett tárolóba, amelyet a rendszer automatikusan beállított a munkaterület létrehozása során. A nyilvános előzetes verzió csak a helyi fájlok feltöltését támogatja. 

    1. A feltöltés befejezésekor a rendszer a fájltípus alapján intelligens módon tölti ki a **beállításokat és az előnézet** űrlapot. Győződjön meg arról, hogy az űrlap a következőképpen van feltöltve.
        
        Mező|Value
        ---|---
        Fájlformátum| Elválasztott
        Elválasztó karakter| Vessző
        Encoding| UTF-8
        Oszlopfejlécek| Minden fájl azonos fejléccel rendelkezik
        Sorok kihagyása | Nincsenek

        Kattintson a **Tovább** gombra.
    
    1. A **séma** űrlap lehetővé teszi az adatai további konfigurálását a kísérlethez. Ebben a példában válassza ki a **day_of_week** funkció váltási kapcsolóját, hogy ne tartalmazza azt a kísérlethez. Válassza a **kész**lehetőséget, hogy elvégezze a fájl feltöltését és a kísérlethez tartozó adatkészlet létrehozását.

        ![Előnézet lap konfigurációja](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. Előrejelzési feladatként válassza a **besorolás** lehetőséget.

1. Válassza az **y** elemet a cél oszlopként, amit meg szeretne jósolni. Ebben az oszlopban látható, hogy az ügyfél előfizetett-e egy lejárati időszakra.

1. Bontsa ki a **Speciális beállítások** elemet, és töltse fel a mezőket az alábbiak szerint.

   >[!NOTE]
   > Ebben a kísérletben nem állít be mérőszámok küszöbértékét vagy a maximális magok számát. Nem blokkolja az algoritmusok tesztelését is.
   
    Speciális&nbsp;beállítások|Leírás|&nbsp;Az&nbsp;oktatóanyag értéke
    ------|---------|---
    Elsődleges metrika| Az értékelési metrika, amelyet a Machine learning algoritmusa fog mérni.|**AUC_weighted** 
    Kilépési feltételek| Ha bármelyik feltétel teljesül, a betanítási feladatok akkor is lejárnak, ha nem teljesen teljesek. |&nbsp;Tanítási&nbsp;feladatokidőpontja&nbsp;(perc): **5**  <br> <br> &nbsp;Iterációk&#58; maximális#száma&nbsp;10&nbsp; 
    Előfeldolgozás| Lehetővé teszi az automatikus gépi tanulás által végzett előfeldolgozást. Ez magában foglalja az automatikus adattisztítást, előkészítést és átalakítást a szintetikus funkciók létrehozásához.| Engedélyezés
    Érvényesítés| Az érvényesítés típusa és a tesztek száma. | **K-szeres** keresztek ellenőrzése<br><br>  több érvényesítés: **2** 
    Egyidejűség| Az egyidejű ismétlések maximális száma.|**5**

1. A kísérlet futtatásához kattintson a **Start** gombra. A kísérlet előkészítésének megkezdéséhez egy képernyő jelenik meg egy állapotjelző üzenettel.

>[!IMPORTANT]
> Az előkészítés **10-15 percet** vesz igénybe a kísérlet futtatásának előkészítése érdekében. A futása után **az egyes iterációk esetében 2-3 percet**vesz igénybe.  
>
> Éles környezetben valószínűleg egy kicsit. Ebben az oktatóanyagban azonban javasoljuk, hogy kezdje el megvizsgálni az iteráció eredményét, miközben a többiek még futnak. 

##  <a name="explore-iteration-results"></a>Iteráció eredményeinek megismerése

A kísérlet előrehaladása esetén a képernyő frissíti az **iterációs diagramot** és az **iterációs listát** a befejezésük során létrehozott különböző iterációkkal (modellekkel), és metrikai pontszám szerint rendeli azokat. Alapértelmezés szerint a kiválasztott **AUC_weighted** mérőszám alapján a legmagasabb pontszám a lista tetején található.

Amíg az összes kísérleti iteráció befejeződik, a teljesítmény részleteinek megismeréséhez válassza ki a befejezett iteráció **nevét** . 
   
A következő táblázat a diagramokat és a futtatási mérőszámokat mutatja be az egyes ismétlésekhez, például a pontosság-visszahívási görbe, a zavart mátrix, a súlyozott pontossági pontszámok stb. 

![Iteráció részleteinek futtatása](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Az automatizált gépi tanulás a munkaterület kezdőlapján lehetővé teszi a legjobb modell üzembe helyezését webszolgáltatásként néhány lépésben. Az üzembe helyezés a modell integrációja, így előre jelezhető az új adatmennyiség, és azonosíthatók a lehetséges lehetőségek is. Ebben a kísérletben a webszolgáltatások üzembe helyezése azt jelenti, hogy a pénzügyi intézmény immár egy iterációs és méretezhető webes megoldást kínál a lehetséges rögzített lejáratú ügyfelek azonosítására. 

A Futtatás befejezése után térjen vissza az **iterációs diagram** és az **Ismétlések listája** részletei lapra. 

Ebben a kísérleti kontextusban a **VotingEnsemble** a **AUC_weighted** metrika alapján a legjobb modellnek számít.  Ezt a modellt üzembe helyezjük, de javasoljuk, hogy az üzembe helyezés körülbelül 20 percet vesz igénybe. Az üzembe helyezési folyamat több lépést is magában foglal, beleértve a modell regisztrálását, az erőforrások létrehozását és a webszolgáltatás konfigurálását.

1. Válassza a **legjobb modell telepítése** gombot a jobb felső sarokban.

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
