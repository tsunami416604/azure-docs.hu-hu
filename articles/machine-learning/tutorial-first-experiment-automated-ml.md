---
title: Automatizált ML besorolási modellek létrehozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan taníthatja & a besorolási modellek üzembe helyezését Azure Machine Learning automatikus gépi tanulási (automatikus ML) felületével.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 02/04/2020
ms.openlocfilehash: 84d539f35919293522f05abdeabeca936138c140
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081624"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Oktatóanyag: besorolási modell létrehozása automatizált ML-vel Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy alapszintű besorolási modellt anélkül, hogy egyetlen sor kódot kellene írnia a Azure Machine Learning automatikus gépi tanulási felületén keresztül. Ez a besorolási modell azt jelzi, hogy az ügyfél előfizet-e a pénzügyi intézménnyel kötött, rögzített időre szóló befizetésre.

Az automatizált gépi tanulás segítségével automatizálhatja az időigényes feladatokat. Az automatizált gépi tanulás gyorsan megismétli az algoritmusok és hiperparaméterek beállítása számos kombinációját, így könnyebben megtalálhatja a legjobb modellt a választott sikerességi mérőszám alapján.

Ebből az oktatóanyagból megtudhatja, hogyan hajthatja végre a következő feladatokat:

> [!div class="checklist"]
> * Az Azure Machine Learning-munkaterület létrehozása.
> * Futtasson automatizált gépi tanulási kísérletet.
> * A kísérlet részleteinek megtekintése.
> * A modell üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).

* Töltse le a [**bankmarketing_train. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) adatfájlt. Az **y** oszlop azt jelzi, hogy az ügyfél egy rögzített lejáratú befizetésre fizetett-e, amelyet később a jelen oktatóanyagban megjelenő előrejelzések céljának oszlopa azonosítottak. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning munkaterület a felhőben található alapvető erőforrás, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használható. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi a szolgáltatásban. 

A munkaterületet az Azure-erőforrások kezeléséhez használható webalapú konzolon Azure Portal segítségével hozhatja létre.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Jegyezze fel a **munkaterületet** és az **előfizetést**. Ezekre azért van szükség, hogy a megfelelő helyen hozza létre a kísérletet. 

## <a name="create-and-run-the-experiment"></a>A kísérlet létrehozása és futtatása

A következő kísérletet az Azure Machine learning szolgáltatásban, a https://ml.azure.comon, egy összevont webes felületen végezheti el, amely a gépi tanulási eszközöket tartalmazza, amelyekkel adatelemzési forgatókönyvek készíthetők az összes képzettségi szint adatelemző szakemberek számára. Ez az interfész nem támogatott az Internet Explorer böngészőben.

1. Jelentkezzen be Azure Machine Learningra https://ml.azure.comcímen.

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

1. Válassza az első **lépések**lehetőséget.

1. A bal oldali panelen válassza az **automatikus ml** lehetőséget a **Szerző** szakaszban.

   Mivel ez az első automatizált ML-kísérlet, egy üres lista jelenik meg, amely a dokumentációra mutató hivatkozásokat tartalmaz.

   ![Első lépések lap](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Válassza az **új automatikus ml Futtatás**lehetőséget. 

1. Hozzon létre egy új adatkészletet a **helyi fájlok** lehetőség kiválasztásával a **+ adatkészlet létrehozása** legördülő menüből. 

    1. Az **alapszintű információ** űrlapon adja meg az adatkészlet nevét, és adjon meg egy opcionális leírást. Az automatikus ml felület jelenleg csak a TabularDatasets támogatja, ezért az adatkészlet típusának alapértelmezett értékének *táblázatos*kell lennie.

    1. Kattintson a **Next (tovább) gombra** a bal alsó sarokban

    1. Az **adattár és fájl kiválasztása** űrlapon válassza ki az alapértelmezett adattárat, amelyet a rendszer automatikusan beállított a munkaterület létrehozásakor, **workspaceblobstore (Azure Blob Storage)** . Itt töltheti fel az adatfájlt, hogy elérhető legyen a munkaterületen.

    1. Válassza a **Tallózás** lehetőséget.
    
    1. Válassza ki a **bankmarketing_train. csv** fájlt a helyi számítógépen. Ez az [előfeltételként](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)letöltött fájl.

    1. Adjon egyedi nevet az adatkészletnek, és adjon meg egy opcionális leírást. 

    1. A bal alsó sarokban kattintson a **tovább** gombra, hogy feltöltse azt az alapértelmezett tárolóba, amelyet a rendszer automatikusan beállított a munkaterület létrehozása során.  
    
       A feltöltés befejezésekor a beállítások és az előnézet űrlap előre fel van töltve a fájl típusa alapján. 
       
    1. Győződjön meg arról, hogy a **beállítások és az előnézet** űrlap a következőképpen van feltöltve, majd válassza a **tovább**lehetőséget.
        
        Mező|Leírás| Az oktatóanyag értéke
        ---|---|---
        Fájl formátuma|Meghatározza a fájlban tárolt adatelrendezést és-típust.| Tagolt
        Elválasztó karakter|Egy vagy több karakter, amely&nbsp; különálló, egyszerű szöveges vagy más adatfolyamokban lévő független régiók közötti határ megadását határozza meg. |Vesszővel
        Encoding|Meghatározza, hogy az adatkészletek olvasásához milyen bitet kell használni a séma-tábla.| UTF-8
        Oszlopfejlécek| Azt jelzi, hogy a rendszer hogyan kezeli az adatkészlet fejléceit (ha van ilyen).| Minden fájlnak azonos fejléce van
        Sorok kihagyása | Azt jelzi, hogy az adatkészletben hány, ha van ilyen, a sorok kimaradnak.| Nincs

    1. A **séma** űrlap lehetővé teszi az adatai további konfigurálását a kísérlethez. Ehhez a példához válassza ki a **day_of_week** funkció váltási kapcsolóját, hogy ne tartalmazza azt a kísérlethez. Kattintson a **Tovább** gombra.

        ![Előnézet lap konfigurációja](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. A **részletek megerősítése** űrlapon ellenőrizze, hogy az információ megegyezik-e az **alapinformációk** és **beállítások és az előnézeti** űrlapok által korábban feltöltött adatokkal.
    1. Válassza a **Létrehozás** lehetőséget az adatkészlet létrehozásának befejezéséhez.
    1. Válassza ki az adatkészletet, amint megjelenik a listában.
    1. Tekintse át az **adatelőnézett** , és győződjön meg arról, hogy nem tartalmaz **day_of_week** , majd kattintson **az OK gombra**.

    1. Kattintson a **Tovább** gombra.

1. Töltse fel a **Run (Futtatás** ) űrlapot a következőképpen:
    1. Adja meg a kísérlet nevét: `my-1st-automl-experiment`

    1. Válassza az **y** elemet a cél oszlopként, amit meg szeretne jósolni. Ebben az oszlopban látható, hogy az ügyfél előfizetett-e egy lejárati időszakra.
    1. Válassza az **új számítás létrehozása** és a számítási cél konfigurálása lehetőséget. A számítási cél egy helyi vagy felhőalapú erőforrás-környezet, amely a betanítási parancsfájl futtatására vagy a szolgáltatás központi telepítésének üzemeltetésére szolgál. Ebben a kísérletben felhőalapú számítást használunk. 

        Mező | Leírás | Az oktatóanyag értéke
        ----|---|---
        Számítási név |A számítási környezet azonosítására szolgáló egyedi név.|automl – számítás
        Virtuális&nbsp;gép&nbsp;mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz.|Standard_DS12_V2
        Csomópontok minimális/maximális száma (speciális beállításokban)| A profilhoz legalább 1 csomópontot kell megadnia.|Minimális csomópontok: 1<br>Csomópontok maximális száma: 6
  
        1. A számítási cél beszerzéséhez válassza a **Létrehozás** lehetőséget. 

            **Ez eltarthat néhány percet.** 

        1. A létrehozás után válassza ki az új számítási célt a legördülő listából.

    1. Kattintson a **Tovább** gombra.

1. A feladattípus **és beállítások** űrlapon válassza a **besorolás** lehetőséget a Machine learning feladattípusként.

    1. Válassza a **további konfigurációs beállítások megtekintése** lehetőséget, és töltse fel a mezőket az alábbiak szerint. Ezek a beállítások hatékonyabban szabályozzák a betanítási feladatot. Ellenkező esetben a rendszer az alapértelmezett értékeket a kísérletezés és az adatértékek alapján alkalmazza.

        >[!NOTE]
        > Ebben az oktatóanyagban nem fog mérőszám-pontszámot vagy maximális magot megadni iterációs küszöbértékként. És nem fogja letiltani az algoritmusok tesztelését.
   
        További&nbsp;konfigurációk|Leírás|&nbsp;oktatóanyag értéke&nbsp;
        ------|---------|---
        Elsődleges metrika| Az értékelési metrika, amelyet a Machine learning algoritmusa fog mérni.|AUC_weighted
        Automatikus featurization| Az előfeldolgozás engedélyezése. Ez magában foglalja az automatikus adattisztítást, előkészítést és átalakítást a szintetikus funkciók létrehozásához.| Bekapcsolás
        Letiltott algoritmusok | A betanítási feladatokból kizárni kívánt algoritmusok| Nincs
        Kilépési feltétel| Ha teljesülnek a feltételek, a betanítási feladatok leállnak. |&nbsp;feladatok betanítása&nbsp;idő (óra): 1 <br> Metrika&nbsp;pontszám&nbsp;küszöbérték: nincs
        Ellenőrzés | Válasszon egy több ellenőrzési típust és a tesztek számát.|Érvényesítés típusa:<br>&nbsp;k-szor&nbsp;kereszt-ellenőrzési <br> <br> Érvényességek száma: 2
        Egyidejűség| A másodpercenként végrehajtott párhuzamos ismétlések maximális száma| &nbsp;párhuzamos&nbsp;ismétlések maximális száma: 5
        
        Kattintson a **Mentés** gombra.

1. A kísérlet futtatásához kattintson a **Befejezés** gombra. Megnyílik a **futtatási részletek** képernyő, amelyen a kísérlet előkészítésének megkezdése után a **Futtatás állapota** látható.

>[!IMPORTANT]
> Az előkészítés **10-15 percet** vesz igénybe a kísérlet futtatásának előkészítése érdekében.
> A futása után **az egyes iterációk esetében 2-3 percet**vesz igénybe.  
> Válassza a rendszeres **frissítés** lehetőséget, hogy megtekintse a kísérlet előrehaladásának állapotát.
>
> Éles környezetben valószínűleg egy kicsit. Ebben az oktatóanyagban azonban azt javasoljuk, hogy kezdje el a tesztelt algoritmusok vizsgálatát a **models (modellek** ) lapon, amíg a többiek még futnak. 

##  <a name="explore-models"></a>Modellek megismerése

Navigáljon a **modellek** lapra, és tekintse meg a tesztelt algoritmusokat (modelleket). Alapértelmezés szerint a modelleket metrikai pontszám szerint rendezi a rendszer. Ebben az oktatóanyagban a legfelső szinten a kiválasztott **AUC_weighted** metrika alapján elért modell a lista tetején található.

Amíg megvárja az összes kísérleti modell befejeződését, válassza ki a befejezett modell **algoritmusának nevét** a teljesítmény részleteinek megismeréséhez. 

A következő lépésekkel navigálhat a **modell részletein** és a **vizualizációk** lapjain a kiválasztott modell tulajdonságainak, metrikáinak és teljesítmény-diagramjainak megtekintéséhez. 

![Iteráció részleteinek futtatása](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>A legjobb modell üzembe helyezése

Az automatizált gépi tanulási felület lehetővé teszi a legjobb modell üzembe helyezését webszolgáltatásként néhány lépésben. Az üzembe helyezés a modell integrációja, így előre jelezhető az új adatmennyiség, és azonosíthatók a lehetséges lehetőségek is. 

Ebben a kísérletben a webszolgáltatások üzembe helyezése azt jelenti, hogy a pénzügyi intézmény immár egy iterációs és méretezhető webes megoldást kínál a lehetséges rögzített lejáratú ügyfelek azonosítására. 

A Futtatás befejezése után térjen vissza a **Futtatás részletei** lapra, és válassza a **modellek** fület.

Ebben a kísérleti kontextusban a **VotingEnsemble** a **AUC_weighted** metrika alapján a legjobb modellnek számít.  Ezt a modellt üzembe helyezjük, de javasoljuk, hogy az üzembe helyezés körülbelül 20 percet vesz igénybe. Az üzembe helyezési folyamat több lépést is magában foglal, beleértve a modell regisztrálását, az erőforrások létrehozását és a webszolgáltatás konfigurálását.

1. Kattintson a **legjobb modell telepítése** gombra a bal alsó sarokban.

1. Töltse fel a **modell üzembe helyezése** panelt az alábbiak szerint:

    Mező| Érték
    ----|----
    Központi telepítés neve| My-automl – üzembe helyezés
    Központi telepítés leírása| Az első automatizált gépi tanulási kísérlet üzembe helyezése
    Számítási típus | Azure számítási példány kiválasztása (ACI)
    Hitelesítés engedélyezése| Megbénít. 
    Egyéni központi telepítések használata| Megbénít. Lehetővé teszi az alapértelmezett illesztőprogram-fájl (pontozási parancsfájl) és a környezeti fájl számára az automatikusan generált értéket. 
    
    Ebben a példában a *speciális* menüben megadott alapértékeket használjuk. 

1. Válassza az **Üzembe helyezés** lehetőséget.  

    A **Run (Futtatás** ) képernyő felső részén megjelenik egy zöld sikert jelző üzenet, és az **ajánlott modell** ablaktáblán megjelenik egy állapotjelző üzenet a **telepítés állapota**területen. A központi telepítés állapotának megtekintéséhez válassza a rendszeres **frissítés** lehetőséget.
    
Most már rendelkezik egy operatív webszolgáltatással előrejelzések létrehozásához. 

Folytassa a [**következő lépésekkel**](#next-steps) , ha többet szeretne megtudni az új webszolgáltatás használatáról, és tesztelje a jóslatokat a Power BI beépített Azure Machine learning-támogatásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az üzembe helyezési fájlok nagyobb méretűek, mint az adatmennyiség és a kísérlet során használt fájlok. Csak a központi telepítési fájlokat törölje, hogy csökkentse a költségeket a fiókjához, vagy ha meg szeretné őrizni a munkaterületet és a kísérletet. Ellenkező esetben törölje a teljes erőforráscsoportot, ha nem tervezi az egyik fájl használatát.  

### <a name="delete-the-deployment-instance"></a>A központi telepítési példány törlése

Csak a központi telepítési példányt törölje Azure Machine Learningról https://ml.azure.com/, ha meg szeretné őrizni az erőforráscsoportot és a munkaterületet más oktatóanyagok és feltárás céljából. 

1. Lépjen Azure Machine Learning a https://ml.azure.com/címen. Navigáljon a munkaterülethez, és a bal oldalon az **eszközök** ablaktáblán válassza a **végpontok**lehetőséget. 

1. Válassza ki a törölni kívánt központi telepítést, és válassza a **Törlés**lehetőséget. 

1. Válassza a **Folytatás**lehetőséget.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az automatizált gépi tanulási oktatóanyagban a besorolási modell létrehozásához és üzembe helyezéséhez Azure Machine Learning automatikus ML-interfészét használta. További információkat és további lépéseket a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Webszolgáltatás felhasználása](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ További információ az [automatizált gépi tanulásról](concept-automated-ml.md).
+ A besorolási metrikákkal és diagramokkal kapcsolatos további információkért tekintse meg az [automatizált gépi tanulás eredményeinek megismerése](how-to-understand-automated-ml.md#classification) című cikket. További információ a [featurization](how-to-create-portal-experiments.md#featurization).
+ További információ az [adatprofilkészítésről](how-to-create-portal-experiments.md#profile).


>[!NOTE]
> Ezt a bank marketing-adatkészletet a [Creative Commons (CCO: Public Domain) licence](https://creativecommons.org/publicdomain/zero/1.0/)keretében lehet elérni. Az adatbázis egyéni tartalmában minden jogosultság az [adatbázis tartalma licenc](https://creativecommons.org/publicdomain/zero/1.0/) alatt érhető el, és elérhető a [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)-on. Ez az adatkészlet eredetileg az [UCI Machine learning adatbázisban](https://archive.ics.uci.edu/ml/datasets/bank+marketing)volt elérhető.<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez és P. Rita. Adatvezérelt megközelítés a banki telemarketing sikerességének előrejelzéséhez. Döntési támogatási rendszerek, Elsevier, 62:22-31, június 2014.
