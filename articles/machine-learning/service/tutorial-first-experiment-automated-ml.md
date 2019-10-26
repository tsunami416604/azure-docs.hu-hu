---
title: Az első automatizált ML besorolási kísérlet létrehozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan taníthat és helyezhet üzembe egy besorolási modellt automatikus gépi tanulással Azure Machine Learning munkaterület-kezdőlapján (előzetes verzió).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/26/2019
ms.openlocfilehash: 726ac906b5f237cfc40a8399b7b1bef3071f98f3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901804"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Oktatóanyag: az első besorolási modell létrehozása automatizált gépi tanulással

Ebből az oktatóanyagból megtudhatja, hogyan hozhatja létre első automatizált gépi tanulási kísérletét a munkaterület kezdőlapján (előzetes verzió) anélkül, hogy egyetlen sor kódot kellene írnia. Ez a példa egy besorolási modellt hoz létre, amely azt jelzi, hogy az ügyfél egy pénzügyi intézmény által rögzített értékű letétbe kerül.

Az automatizált gépi tanulás segítségével automatizálhatja az időigényes feladatokat. Az automatizált gépi tanulás gyorsan megismétli az algoritmusok és hiperparaméterek beállítása számos kombinációját, így könnyebben megtalálhatja a legjobb modellt a választott sikerességi mérőszám alapján.

Ebből az oktatóanyagból megtudhatja, hogyan hajthatja végre a következő feladatokat:

> [!div class="checklist"]
> * Hozzon létre egy Azure Machine Learning munkaterületet.
> * Futtasson automatizált gépi tanulási kísérletet.
> * A kísérlet részleteinek megtekintése.
> * A modell üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).

* Töltse le az [**bankmarketing_train. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) adatfájlt. Az **y** oszlop azt jelzi, hogy az ügyfél egy rögzített lejáratú befizetésre fizetett-e, amelyet később a jelen oktatóanyagban megjelenő előrejelzések céljának oszlopa azonosítottak. 

## <a name="create-a-workspace"></a>Munkaterületek létrehozása

Az Azure Machine Learning munkaterület a felhőben található alapvető erőforrás, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használható. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi a szolgáltatásban. 

A munkaterületet az Azure-erőforrások kezeléséhez használható webalapú konzolon Azure Portal segítségével hozhatja létre. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Jegyezze fel a **munkaterületet** és az **előfizetést**. Ezekre azért van szükség, hogy a megfelelő helyen hozza létre a kísérletet. 

## <a name="create-and-run-the-experiment"></a>A kísérlet létrehozása és futtatása

A következő kísérlet beállítása és futtatása lépések a munkaterület kezdőlapján, egy összevont felület, amely magában foglalja a gépi tanulási eszközöket, amelyekkel adatelemzési forgatókönyvek készíthetők az összes képzettségi szint adatelemző szakemberek számára. Az Internet Explorer böngésző nem támogatja a munkaterület kezdőlapját.

1. Jelentkezzen be a [munkaterület](https://ml.azure.com/workspaceportal/)kezdőlapján.

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

1. Válassza az első **lépések**lehetőséget.

1. A bal oldali panelen válassza az **automatikus ml** lehetőséget a **Szerző** szakaszban.

   Mivel ez az első automatizált ML-kísérlet, megjelenik a kezdeti lépések képernyő.

   ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Válassza a **kísérlet létrehozása**lehetőséget. 

1. Adja meg a kísérlet nevét: `my-1st-automl-experiment`

1. Válassza az **új számítás létrehozása** és a számítási cél konfigurálása lehetőséget. A számítási cél egy helyi vagy felhőalapú erőforrás-környezet, amely a betanítási parancsfájl futtatására vagy a szolgáltatás központi telepítésének üzemeltetésére szolgál. Ehhez a kísérlethez felhőalapú számítást használunk. 

   Mező | Leírás | Az oktatóanyag értéke
   ----|---|---
   Számítási név |A számítási környezet azonosítására szolgáló egyedi név.|automl – számítás
   Virtuális &nbsp;machine &nbsp;size| Válassza ki a virtuális gép méretét a számítási feladatokhoz.|Standard_DS12_V2
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
    
       A feltöltés befejezésekor a beállítások és az előnézet űrlap előre fel van töltve a fájl típusa alapján. 
       
    1. Győződjön meg arról, hogy a **beállítások és az előnézet** űrlap a következőképpen van feltöltve, majd válassza a **tovább**lehetőséget.
        
        Mező|Leírás| Az oktatóanyag értéke
        ---|---|---
        Fájl formátuma|Meghatározza a fájlban tárolt adatelrendezést és-típust.| Tagolt
        Elválasztó|Egy vagy több karakter, amely &nbsp; különálló, egyszerű szöveges vagy más adatfolyamokban lévő független régiók közötti határ megadását határozza meg. |Vesszővel
        Encoding|Meghatározza, hogy az adatkészletek olvasásához milyen bitet kell használni a séma-tábla.| UTF-8
        Oszlopfejlécek| Azt jelzi, hogy a rendszer hogyan kezeli az adatkészlet fejléceit (ha van ilyen).| Minden fájlnak azonos fejléce van
        Sorok kihagyása | Azt jelzi, hogy az adatkészletben hány, ha van ilyen, a sorok kimaradnak.| None
    
        ![Előnézet lap konfigurációja](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. Előrejelzési feladatként válassza a **besorolás** lehetőséget.

1. Válassza az **y** elemet a cél oszlopként, amit meg szeretne jósolni. Ebben az oszlopban látható, hogy az ügyfél előfizetett-e egy lejárati időszakra.

1. Bontsa ki a **Speciális beállítások** elemet, és töltse fel a mezőket az alábbiak szerint. Ezek a beállítások hatékonyabban szabályozzák a betanítási feladatot. Ellenkező esetben a rendszer az alapértelmezett értékeket a kísérletezés és az adatértékek alapján alkalmazza.

   >[!NOTE]
   > Ebben az oktatóanyagban nem fog mérőszám-pontszámot vagy maximális magot megadni iterációs küszöbértékként. És nem fogja letiltani az algoritmusok tesztelését.
   
   Speciális &nbsp;settings|Leírás|Érték &nbsp;for &nbsp;tutorial
   ------|---------|---
   Elsődleges metrika| Az értékelési metrika, amelyet a Machine learning algoritmusa fog mérni.|AUC_weighted
   Kilépési feltételek| Ha teljesülnek a feltételek, a betanítási feladatok leállnak. |Képzés &nbsp;job &nbsp;time: 5 <br> <br> Max &nbsp; # &nbsp;of &nbsp;iterations&#58;10
   Előfeldolgozás| Lehetővé teszi az automatikus gépi tanulás által végzett előfeldolgozást. Ez magában foglalja az automatikus adattisztítást, előkészítést és átalakítást a szintetikus funkciók létrehozásához.| Engedélyezés
   Érvényesítés típusa | Válasszon egy több ellenőrzési típust.|K-szeres keresztek ellenőrzése
   Érvényességek száma | Tesztek száma. | 2 érvényességi értékek 
   Párhuzamosság| Az egyidejű ismétlések maximális száma.|5
   
1. A kísérlet futtatásához kattintson a **Start** gombra. A kísérlet előkészítésének megkezdéséhez egy képernyő jelenik meg egy állapotjelző üzenettel.

>[!IMPORTANT]
> Az előkészítés **10-15 percet** vesz igénybe a kísérlet futtatásának előkészítése érdekében. A futása után **az egyes iterációk esetében 2-3 percet**vesz igénybe.  
>
> Éles környezetben valószínűleg egy kicsit. Ebben az oktatóanyagban azonban javasoljuk, hogy kezdje el megvizsgálni az iteráció eredményét, miközben a többiek még futnak. 

##  <a name="explore-iteration-results"></a>Iteráció eredményeinek megismerése

A kísérlet előrehaladása esetén a képernyő frissíti az **iterációs diagramot** és az **iterációs listát** a befejezésük során létrehozott különböző iterációkkal (modellekkel). Alapértelmezés szerint az ismétléseket a mérőszámok pontszáma alapján rendezi a rendszer. Ebben az oktatóanyagban a legjobb modell, amely a kiválasztott **AUC_weighted** -metrika alapján szerzi be a legmagasabb értékeket a lista elejére.

Amíg az összes kísérleti iteráció befejeződik, a teljesítmény részleteinek megismeréséhez válassza ki a befejezett iteráció **nevét** . 

A következő táblázat a diagramokat és a futtatási mérőszámokat mutatja be az egyes ismétlésekhez, például a pontosság-visszahívási görbe, a zavart mátrix, a súlyozott pontossági pontszámok stb. 

![Iteráció részleteinek futtatása](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>A modell rendszerbe állítása

Az automatizált gépi tanulás a munkaterület kezdőlapján lehetővé teszi a legjobb modell üzembe helyezését webszolgáltatásként néhány lépésben. Az üzembe helyezés a modell integrációja, így előre jelezhető az új adatmennyiség, és azonosíthatók a lehetséges lehetőségek is. Ebben a kísérletben a webszolgáltatások üzembe helyezése azt jelenti, hogy a pénzügyi intézmény immár egy iterációs és méretezhető webes megoldást kínál a lehetséges rögzített lejáratú ügyfelek azonosítására. 

A Futtatás befejezése után térjen vissza az **iterációs diagram** és az **Ismétlések listája** részletei lapra. 

Ebben a kísérleti kontextusban a **VotingEnsemble** a **AUC_weighted** metrika alapján a legjobb modellnek számít.  Ezt a modellt üzembe helyezjük, de javasoljuk, hogy az üzembe helyezés körülbelül 20 percet vesz igénybe. Az üzembe helyezési folyamat több lépést is magában foglal, beleértve a modell regisztrálását, az erőforrások létrehozását és a webszolgáltatás konfigurálását.

1. Válassza a **legjobb modell telepítése** gombot a jobb felső sarokban.

1. Töltse fel a **legjobb modell üzembe helyezése** panelt az alábbiak szerint:

    Mező| Value (Díj)
    ----|----
    Központi telepítés neve| My-automl – üzembe helyezés
    Központi telepítés leírása| Az első automatizált gépi tanulási kísérlet üzembe helyezése
    Pontozási parancsfájl| Autogenerate
    Környezeti parancsfájl| Autogenerate
    
1. Válassza az **Üzembe helyezés** lehetőséget.  

    Az üzembe helyezés sikeres befejeződése után megjelenik egy üzembe helyezési teljes üzenet.
    
Most már rendelkezik egy operatív webszolgáltatással előrejelzések létrehozásához.

Folytassa a [**következő lépésekkel**](#next-steps) , ha többet szeretne megtudni az új webszolgáltatás használatáról, és tesztelje a jóslatokat a Power BI beépített Azure Machine learning-támogatásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az üzembe helyezési fájlok nagyobb méretűek, mint az adatmennyiség és a kísérlet során használt fájlok. Csak a központi telepítési fájlokat törölje, hogy csökkentse a költségeket a fiókjához, vagy ha meg szeretné őrizni a munkaterületet és a kísérletet. Ellenkező esetben törölje a teljes erőforráscsoportot, ha nem tervezi az egyik fájl használatát.  

### <a name="delete-the-deployment-instance"></a>A központi telepítési példány törlése

Ha az erőforráscsoportot és a munkaterületet más oktatóanyagok és feltárás céljából szeretné megtartani, törölje a Azure Portal csak a telepítési példányt. 

1. Nyissa meg az [Azure Portal](https://portal.azure.com//). Navigáljon a munkaterülethez, és a bal oldalon az **eszközök** ablaktáblán válassza a **központi telepítések**lehetőséget. 

1. Válassza ki a törölni kívánt központi telepítést, és válassza a **Törlés**lehetőséget. 

1. Válassza a **Folytatás**lehetőséget.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az automatizált gépi tanulási oktatóanyagban a munkaterület kezdőlapját használta egy besorolási modell létrehozásához és üzembe helyezéséhez. További információkat és további lépéseket a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Webszolgáltatás felhasználása](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ További információ az [előfeldolgozásról](how-to-create-portal-experiments.md#preprocess).
+ További információ az [adatprofilkészítésről](how-to-create-portal-experiments.md#profile).
+ További információ az [automatizált gépi tanulásról](concept-automated-ml.md).
+ A besorolási mérőszámokkal és diagramokkal kapcsolatos további információkért tekintse meg az [automatikus gépi tanulás eredményeinek megismerése](how-to-understand-automated-ml.md#classification) című cikket.

>[!NOTE]
> Ezt a bank marketing-adatkészletet a [Creative Commons (CCO: Public Domain) licence](https://creativecommons.org/publicdomain/zero/1.0/)keretében lehet elérni. Az adatbázis egyéni tartalmában minden jogosultság az [adatbázis tartalma licenc](https://creativecommons.org/publicdomain/zero/1.0/) alatt érhető el, és elérhető a [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)-on. Ez az adatkészlet eredetileg az [UCI Machine learning adatbázisban](https://archive.ics.uci.edu/ml/datasets/bank+marketing)volt elérhető.<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez és P. Rita. Adatvezérelt megközelítés a banki telemarketing sikerességének előrejelzéséhez. Döntési támogatási rendszerek, Elsevier, 62:22-31, június 2014.
