---
title: Automatikus ml besorolási modellek létrehozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan taníthat be & a besorolási modelleket az Azure Machine Learning automatizált gépi tanulási (automated ML) felületével.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 03/04/2020
ms.openlocfilehash: b5a335a3f215ad5883b1b223245ca9d3f9967c3b
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366517"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Oktatóanyag: Hozzon létre egy besorolási modellt az Azure Machine Learning automatikus ml-es használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy alapvető besorolási modellt anélkül, hogy egyetlen kódsort írna az Azure Machine Learning automatizált gépi tanulási felületével. Ez a besorolási modell előre jelzi, ha az ügyfél fizet egy fix lejáratú betét egy pénzügyi intézmény.

Az automatizált gépi tanulással automatizálhatja az időigényes feladatokat. Az automatizált gépi tanulás gyorsan iterál az algoritmusok és a hiperparaméterek számos kombinációján, hogy segítsen megtalálni a legjobb modellt az Ön által választott sikeres ségi mutató alapján.

Ebben az oktatóanyagban megtudhatja, hogyan kell elvégezni a következő feladatokat:

> [!div class="checklist"]
> * Hozzon létre egy Azure Machine Learning-munkaterületet.
> * Automatizált gépi tanulási kísérlet futtatása.
> * A kísérlet részleteinek megtekintése.
> * A modell üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://aka.ms/AMLFree)

* Töltse le a [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) adatfájlt. Az **y** oszlop azt jelzi, hogy az ügyfél előfizetett-e egy rögzített lejáratú letétre, amelyet később az oktatóanyag előrejelzéseinek céloszlopaként azonosítanak. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning-munkaterület egy alapvető erőforrás a felhőben, amely et a kísérletezés, a betanítás és a gépi tanulási modellek üzembe helyezése. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz köti a szolgáltatásban. 

Hozzon létre egy munkaterületet az Azure Portalon keresztül, egy webalapú konzol az Azure-erőforrások kezelésére.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Vegye figyelembe **munkaterületét** és **előfizetését.** Ezekre szüksége lesz, hogy a kísérletet a megfelelő helyen hozza létre. 

## <a name="create-and-run-the-experiment"></a>A kísérlet létrehozása és futtatása

A következő kísérletbeállítási és -futtatási lépéseket https://ml.azure.comaz Azure Machine learning segítségével végezheti el, amely egy konszolidált webes felületen, amely gépi tanulási eszközöket tartalmaz az adatelemzési forgatókönyvek végrehajtásához az adatelemzési szakemberek számára, minden képzettségi szinten. Ez a felület az Internet Explorer böngészőiben nem támogatott.

1. Jelentkezzen be az [Azure Machine Learning be.](https://ml.azure.com)

1. Válassza ki az előfizetést és a létrehozott munkaterületet.

1. Válassza **az Első lépések lehetőséget.**

1. A bal oldali ablaktáblában válassza az **Automatikus hibahelyreállítás** lehetőséget a **Szerző** szakaszban.

   Mivel ez az első automatikus gépi tanulási kísérlet, megjelenik egy üres lista és a dokumentációra mutató hivatkozások.

   ![Első lépések lap](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Válassza **az Új automatikus hibamódszer-futtatás lehetőséget.** 

1. Hozzon létre egy új adatkészletet a **+Create adatkészlet** legördülő menü **Helyi fájlokból** lehetőséget választva. 

    1. Az **Alapszintű információ** űrlapon adjon nevet az adatkészletnek, és adjon meg egy nem kötelező leírást. Az automatikus ml-kapcsolat jelenleg csak a TabularDatasets-et támogatja, így az adatkészlet típusának alapértelmezés szerint *táblázatos*nak kell lennie.

    1. Válassza a **Tovább** gombot a bal alsó sarokban

    1. Az **Adattár és a fájlkijelölési** űrlapon válassza ki azt az alapértelmezett adattalapot, amelyet a munkaterület létrehozása során automatikusan beállított, **a workspaceblobstore (Azure Blob Storage) szolgáltatást.** Itt töltheti fel az adatfájlt, hogy elérhetővé tegye a munkaterület számára.

    1. Válassza a **Tallózás** lehetőséget.
    
    1. Válassza ki a **bankmarketing_train.csv** fájlt a helyi számítógépen. Ez az a fájl, amelyet [előfeltételként](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)töltött le.

    1. Adjon egyedi nevet az adatkészletnek, és adjon meg egy nem kötelező leírást. 

    1. Válassza a **Tovább** gombot a bal alsó sarokban, és töltse fel a munkaterület létrehozása során automatikusan beállított alapértelmezett tárolóba.  
    
       Amikor a feltöltés befejeződött, a Beállítások és az előnézeti űrlap a fájltípus alapján előre ki van töltve. 
       
    1. Ellenőrizze, hogy a **Beállítások és az előnézeti** űrlap az alábbiak szerint van-e feltöltve, és válassza a **Tovább**gombot.
        
        Mező|Leírás| Az oktatóanyag értéke
        ---|---|---
        Fájlformátum|A fájlban tárolt adatok elrendezését és típusát határozza meg.| Tagolt
        Elválasztó|Egy vagy több karakter a&nbsp; különálló, független területek közötti határ megadásához egyszerű szövegben vagy más adatfolyamokban. |Vessző
        Encoding|Azt határozza meg, hogy milyen karaktersématáblát használjon az adatkészlet olvasásához.| UTF-8
        Oszlopfejlécek| Azt jelzi, hogy az adatkészlet fejléceit (ha vannak ilyenek) hogyan kezeli a rendszer.| Minden fájlnak ugyanaz a fejléce
        Sorok kihagyása | Azt jelzi, hogy hány, ha van ilyen sor kimarad az adatkészletből.| None

    1. A **Séma** űrlap lehetővé teszi az adatok további konfigurálását ehhez a kísérlethez. Ebben a példában jelölje ki a **kapcsolót** a day_of_week funkcióhoz, hogy ne tartalmazza azt ehhez a kísérlethez. Válassza a **Tovább lehetőséget.**

        ![Az Előnézet lap konfigurációja](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. A **Részletek megerősítése** képernyőn ellenőrizze, hogy az adatok megegyeznek-e az **Alapszintű adatok** és **beállítások és előnézeti** űrlapokon korábban kitöltött adatokkal.
    1. Az adatkészlet létrehozásának befejezéséhez válassza a **Létrehozás** gombot.
    1. Jelölje ki az adatkészletet, ha az megjelenik a listában.
    1. Tekintse át az **adatok előnézetét,** és győződjön meg arról, hogy nem vette fel **day_of_week,** majd **kattintson az OK gombra.**

    1. Válassza a **Tovább lehetőséget.**

1. A Futtatás **konfigurálása** képernyő feltöltése az alábbiak szerint:
    1. Írja be a kísérlet nevét:`my-1st-automl-experiment`

    1. Válassza ki az **y-t** céloszlopként, hogy mit szeretne előre jelezni. Ez az oszlop azt jelzi, hogy az ügyfél előfizetett-e lekötött betétre vagy sem.
    1. Válassza **az Új számítás létrehozása lehetőséget,** és konfigurálja a számítási célt. A számítási cél egy helyi vagy felhőalapú erőforrás-környezet, amely a betanítási parancsfájl futtatásához vagy a szolgáltatás központi telepítésének üzemeltetéséhez szolgál. Ehhez a kísérlethez felhőalapú számítási. 

        Mező | Leírás | Az oktatóanyag értéke
        ----|---|---
        Számítási név |Egyedi név, amely azonosítja a számítási környezetet.|automl-számítás
        Virtuális&nbsp;&nbsp;gép mérete| Válassza ki a virtuális gép méretét a számítási.|Standard_DS12_V2
        Min / Max csomópontok (speciális beállítások)| A profiladatok hoz meg kell adnia 1 vagy több csomópontot.|Min csomópontok: 1<br>Maximális csomópontok: 6
  
        1. Válassza a **Létrehozás** lehetőséget a számítási cél leszámításához. 

            **Ez néhány percet vesz igénybe.** 

        1. A létrehozás után válassza ki az új számítási célt a legördülő listából.

    1. Válassza a **Tovább lehetőséget.**

1. A **Feladat típusa és beállításai** képernyőn válassza **a Besorolás** gépi tanulási feladattípusként lehetőséget.

    1. Válassza **a További konfigurációs beállítások megtekintése** lehetőséget, és a következőképpen kell felnagyítania a mezőket. Ezek a beállítások a képzési feladat jobb vezérlésére. Ellenkező esetben az alapértelmezett értékek a kísérlet kiválasztása és az adatok alapján kerülnek alkalmazásra.

        >[!NOTE]
        > Ebben az oktatóanyagban nem állít be metrikapontszámot vagy maximális magok iterációs küszöbértéket. Az algoritmusokat sem fogja megakadályozni a tesztelésben.
   
        További&nbsp;konfigurációk|Leírás|Az&nbsp;&nbsp;oktatóanyag értéke
        ------|---------|---
        Elsődleges mutató| Kiértékelési metrika, amely a gépi tanulási algoritmus mérik.|AUC_weighted
        Automatikus featurization| Engedélyezi az előfeldolgozást. Ez magában foglalja az automatikus adattisztítást, előkészítést és átalakítást a szintetikus funkciók létrehozásához.| Bekapcsolás
        Blokkolt algoritmusok | A képzési feladatból kizárni kívánt algoritmusok| None
        Kilépési feltétel| Ha egy feltétel teljesül, a képzési feladat leáll. |Képzési&nbsp;&nbsp;munka ideje (óra): 1 <br> Metrikapontszám&nbsp;&nbsp;küszöbértéke: Nincs
        Ellenőrzés | Válassza ki a keresztérvényesítés típusát és a tesztek számát.|Érvényesítés típusa:<br>&nbsp;k-szeres&nbsp;keresztérvényesítés <br> <br> Ellenőrzések száma: 2
        Egyidejűség| Az iterációnként végrehajtott párhuzamos ismétlések maximális száma| Maximális&nbsp;egyidejű&nbsp;ismétlések: 5
        
        Kattintson a **Mentés** gombra.

1. A kísérlet futtatásához válassza a **Befejezés** lehetőséget. A **Futtatás részletei** képernyő a kísérlet előkészítésének kezdetekor a Futtatás **állapottal** nyílik meg.

>[!IMPORTANT]
> Az előkészítés **10-15 percet** vesz igénybe a kísérlet futásának előkészítése során.
> Ha fut, tart **2-3 perc alatt több minden iteráció**.  
> Válassza **a Rendszeres frissítés** lehetőséget a futtatás állapotának megtekintéséhez a kísérlet előrehaladtával.
>
> A termelés, akkor valószínűleg sétára egy kicsit. De ebben az oktatóanyagban azt javasoljuk, hogy kezdje el felfedezni a tesztelt algoritmusokat a **Modellek** lapon, ahogy azok befejeződnek, miközben a többiek még futnak. 

##  <a name="explore-models"></a>Fedezze fel a modelleket

A **Modellek** lapon megtekintheti a tesztelt algoritmusokat (modelleket). Alapértelmezés szerint a modellek metrikapontszám szerint vannak rendezve, ahogy befejeződnek. Ebben az oktatóanyagban a modell, amely a legmagasabb pontszámot a kiválasztott **AUC_weighted** mérőszám alapján a lista tetején található.

Amíg megvárja, amíg az összes kísérletmodell befejeződik, válassza ki a befejezett modell **algoritmus nevét** a teljesítmény részleteinek megismeréséhez. 

A következő navigálás a **modell részletei** és a **Vizualizációk** lapon a kiválasztott modell tulajdonságainak, mutatóinak és teljesítménydiagramjainak megtekintéséhez. 

![Iteráció részleteinek futtatása](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>A legjobb modell üzembe helyezése

Az automatizált gépi tanulási felület lehetővé teszi, hogy a legjobb modellt webszolgáltatásként néhány lépésben telepítse. A központi telepítés a modell integrációja, így előre jelezheti az új adatokat, és azonosíthatja a lehetőségek lehetséges területeit. 

Ehhez a kísérlethez a webszolgáltatásba való telepítés azt jelenti, hogy a pénzügyi intézmény most már rendelkezik egy iteratív és skálázható webes megoldással a potenciális rögzített lejáratú betéti ügyfelek azonosítására. 

A futtatás befejezése után lépjen vissza a **Részletek futtatása** lapra, és válassza a **Modellek** lapot.

Ebben a kísérletkörnyezetben **a VotingEnsemble** a legjobb modellnek tekinthető a **AUC_weighted** metrika alapján.  Ezt a modellt üzembe helyezjük, de figyelemszerint az üzembe helyezés körülbelül 20 percet vesz igénybe. A telepítési folyamat több lépést foglal magában, beleértve a modell regisztrálását, az erőforrások generálását és a webszolgáltatáshoz való konfigurálását.

1. Válassza a **Legjobb modell telepítése** gombot a bal alsó sarokban.

1. A Modell **telepítése** ablaktábla feltöltése az alábbiak szerint:

    Mező| Érték
    ----|----
    Központi telepítés neve| my-automl-telepítés
    A telepítés leírása| Az első automatikus gépi tanulási kísérlet üzembe helyezése
    Számítási típus | Az Azure Compute Instance (ACI) kiválasztása
    Hitelesítés engedélyezése| Letiltás lehetőséget. 
    Egyéni központi telepítések használata| Letiltás lehetőséget. Lehetővé teszi az alapértelmezett illesztőprogram-fájl (pontozási parancsfájl) és a környezeti fájl automatikus generálását. 
    
    Ebben a példában a *Speciális* menüben megadott alapértelmezett értékeket használjuk. 

1. Válassza az **Üzembe helyezés** lehetőséget.  

    Egy zöld sikerüzenet jelenik meg a **Futtatás** képernyő tetején, és az **Ajánlott modell** ablaktáblán egy állapotüzenet jelenik meg a Telepítés **állapot csoportban.** A központi telepítés állapotának ellenőrzéséhez válassza a **Rendszeres frissítés** lehetőséget.
    
Most már rendelkezik egy operatív webszolgáltatás előrejelzések létrehozásához. 

Folytassa a [**következő lépésekkel,**](#next-steps) ha többet szeretne megtudni az új webszolgáltatás felhasználásáról, és tesztelheti az előrejelzéseket az Azure Machine Learning beépített támogatásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A központi telepítési fájlok nagyobbak, mint az adatok és a kísérleti fájlok, így többe kerülnek a tárolásukhoz. Csak a központi telepítési fájlokat törölje a fiók költségeinek minimalizálása érdekében, vagy ha meg szeretné tartani a munkaterületet és a kísérleti fájlokat. Ellenkező esetben törölje a teljes erőforráscsoportot, ha nem tervezi a fájlok használatát.  

### <a name="delete-the-deployment-instance"></a>A központi telepítési példány törlése

Csak a központi telepítési példányt törölje\/az Azure Machine Learning ből https: /ml.azure.com/, ha meg szeretné tartani az erőforráscsoportot és a munkaterületet más oktatóanyagokés feltáráshoz. 

1. Nyissa meg az [Azure Machine Learning et.](https://ml.azure.com/) Nyissa meg a munkaterületet, és az **Eszközök** ablaktábla bal oldalán válassza a **Végpontok**lehetőséget. 

1. Jelölje ki a törölni kívánt központi telepítést, és válassza a **Törlés**lehetőséget. 

1. Válassza a **Folytatás lehetőséget.**

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben az automatikus gépi tanulási oktatóanyagban az Azure Machine Learning automatikus ML-felületét használta egy besorolási modell létrehozásához és üzembe helyezéséhez. További információt és további lépéseket az alábbi cikkekben talál:

> [!div class="nextstepaction"]
> [Webszolgáltatás felhasználása](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ További információ az [automatizált gépi tanulásról.](concept-automated-ml.md)
+ A besorolási mutatókról és diagramokról az [Automatikus gépi tanulási eredmények ismertetése](how-to-understand-automated-ml.md#classification) című cikkben olvashat bővebben.+ További információ a [featurization](how-to-use-automated-ml-for-ml-models.md#featurization)ről.
+ További információ az [adatprofilozásról.](how-to-use-automated-ml-for-ml-models.md#profile)


>[!NOTE]
> Ez a Bank Marketing adatkészlet a [Creative Commons (CCO: Public Domain) licenc](https://creativecommons.org/publicdomain/zero/1.0/)alatt érhető el. Az adatbázis egyes tartalmaira vonatkozó jogok az [adatbázis tartalomlicence](https://creativecommons.org/publicdomain/zero/1.0/) alapján vannak licencelve, és a [Kaggle-on](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)érhetők el. Ez az adatkészlet eredetileg az [UCI Machine Learning adatbázisában](https://archive.ics.uci.edu/ml/datasets/bank+marketing)volt elérhető.<br><br>
> [Moro és mtsai, 2014] S. Moro, P. Cortez és P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. (Adatvezérelt megközelítés a banki telemarketing sikerességének előrejelzéséhez.) Decision Support Systems, Elsevier, 62:22-31, 2014. június
