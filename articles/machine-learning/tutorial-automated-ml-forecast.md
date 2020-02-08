---
title: Előrejelzési kerékpár-megosztási igény automatizált ML-kísérlettel
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja be és helyezheti üzembe a kereslet-előrejelzési modellt a Azure Machine Learning Studio automatizált gépi tanulás szolgáltatásával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 01/27/2020
ms.openlocfilehash: 11e0a8a0076fb2e68c379b279f471ff74846df2e
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77088241"
---
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>Oktatóanyag: az előrejelzési kerékpár-megosztási igény automatikus gépi tanulással
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben az oktatóanyagban az automatikus gépi tanulást vagy az automatikus ML-t használja a Azure Machine Learning Studióban egy idősorozat-előrejelzési modell létrehozásához a bike Sharing szolgáltatáshoz való bérleti igény előrejelzéséhez.

Ebből az oktatóanyagból megtudhatja, hogyan hajthatja végre a következő feladatokat:

> [!div class="checklist"]
> * Adatkészlet létrehozása és betöltése.
> * Automatikus ML-kísérlet konfigurálása és futtatása.
> * A kísérlet eredményeinek megismerése.
> * A legjobb modell üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

* Enterprise Edition Azure Machine Learning munkaterület. Ha nem rendelkezik munkaterülettel, [hozzon létre egy Enterprise Edition-munkaterületet](how-to-manage-workspace.md). 
    * A Azure Machine Learning Studióban a gépi tanulás csak nagyvállalati kiadású munkaterületeken érhető el. 
* A [Bike-No. csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) adatfájl letöltése

## <a name="get-started-in-azure-machine-learning-studio"></a>Ismerkedés a Azure Machine Learning Studióval

Ebben az oktatóanyagban létrehozhatja az automatizált ML-kísérletet Azure Machine Learning Studióban, egy konszolidált felületen, amely magában foglalja a gépi tanulási eszközöket, amelyekkel adatelemzési forgatókönyveket végezhet az összes képzettségi szint adatelemző szakemberek számára. A Studio nem támogatott az Internet Explorer böngészőben.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

1. Válassza az első **lépések**lehetőséget.

1. A bal oldali panelen válassza az **automatikus ml** lehetőséget a **Szerző** szakaszban.

1. Válassza az **+ új AUTOMATIZÁLT ml Futtatás**lehetőséget. 

## <a name="create-and-load-dataset"></a>Adatkészlet létrehozása és betöltése

A kísérlet konfigurálása előtt töltse fel az adatfájlt a munkaterületre egy Azure Machine Learning adatkészlet formájában. Így biztosíthatja, hogy az adatai megfelelően legyenek formázva a kísérlethez.

1. Az **adatkészlet kiválasztása** űrlapon válassza a **helyi fájlok** lehetőséget a **+ adatkészlet létrehozása** legördülő menüből. 

    1. Az **alapszintű információ** űrlapon adja meg az adatkészlet nevét, és adjon meg egy opcionális leírást. Az adatkészlet típusának alapértelmezettnek kell lennie a **táblázatos**értéknél, mivel a Azure Machine learning Studio automatikus ml-je jelenleg csak a táblázatos adatkészleteket támogatja.
    
    1. Kattintson a **Next (tovább) gombra** a bal alsó sarokban

    1. Az **adattár és fájl kiválasztása** űrlapon válassza ki az alapértelmezett adattárat, amelyet a rendszer automatikusan beállított a munkaterület létrehozásakor, **workspaceblobstore (Azure Blob Storage)** . Ez az a tárolási hely, ahol fel kell töltenie az adatfájlt. 

    1. Válassza a **Tallózás** lehetőséget. 
    
    1. Válassza ki a **Bike-No. csv** fájlt a helyi számítógépen. Ez az [előfeltételként](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)letöltött fájl.

    1. Kattintson a **Tovább** gombra.

       A feltöltés befejezésekor a beállítások és az előnézet űrlap előre fel van töltve a fájl típusa alapján. 
       
    1. Győződjön meg arról, hogy a **beállítások és az előnézet** űrlap a következőképpen van feltöltve, majd válassza a **tovább**lehetőséget.
        
        Mező|Leírás| Az oktatóanyag értéke
        ---|---|---
        Fájl formátuma|Meghatározza a fájlban tárolt adatelrendezést és-típust.| Tagolt
        Elválasztó karakter|Egy vagy több karakter, amely&nbsp; különálló, egyszerű szöveges vagy más adatfolyamokban lévő független régiók közötti határ megadását határozza meg. |Vesszővel
        Encoding|Meghatározza, hogy az adatkészletek olvasásához milyen bitet kell használni a séma-tábla.| UTF-8
        Oszlopfejlécek| Azt jelzi, hogy a rendszer hogyan kezeli az adatkészlet fejléceit (ha van ilyen).| Fejlécek használata az első fájlból
        Sorok kihagyása | Azt jelzi, hogy az adatkészletben hány, ha van ilyen, a sorok kimaradnak.| Nincs

    1. A **séma** űrlap lehetővé teszi az adatai további konfigurálását a kísérlethez. 
    
        1. Ebben a példában válassza az **alkalmi** és a **regisztrált** oszlopok figyelmen kívül hagyása lehetőséget. Ezek az oszlopok a **CNT** oszlop részletezését jelentik, ezért nem tartalmazzák azokat.

        1. Ebben a példában a **Tulajdonságok** és a **típus**alapértelmezett értékei is megmaradnak. 
        
        1. Kattintson a **Tovább** gombra.

    1. A **részletek megerősítése** űrlapon ellenőrizze, hogy az információ megegyezik-e az **alapinformációk** és **beállítások és az előnézeti** űrlapok által korábban feltöltött adatokkal.

    1. Válassza a **Létrehozás** lehetőséget az adatkészlet létrehozásának befejezéséhez.

    1. Válassza ki az adatkészletet, amint megjelenik a listában.

    1. Kattintson a **Tovább** gombra.

## <a name="configure-experiment-run"></a>Kísérlet futtatásának konfigurálása

Miután betöltötte és konfigurálta az adatait, állítsa be a távoli számítási célt, és válassza ki, hogy az adatok mely oszlopát szeretné előre jelezni.

1. Töltse fel a **Run (Futtatás** ) űrlapot a következőképpen:
    1. Adja meg a kísérlet nevét: `automl-bikeshare`

    1. Válassza a **CNT** lehetőséget a cél oszlopként, amit előre meg kíván jeleníteni. Ez az oszlop a kerékpáros megosztások teljes bérletének számát jelzi.

    1. Válassza az **új számítás létrehozása** és a számítási cél konfigurálása lehetőséget. Az automatikus ML csak Azure Machine Learning számítást támogatja. 

        Mező | Leírás | Az oktatóanyag értéke
        ----|---|---
        Számítási név |A számítási környezet azonosítására szolgáló egyedi név.|Bike – számítás
        Virtuális&nbsp;gép&nbsp;mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz.|Standard_DS12_V2
        Csomópontok minimális/maximális száma (speciális beállításokban)| A profilhoz legalább 1 csomópontot kell megadnia.|Minimális csomópontok: 1<br>Csomópontok maximális száma: 6
  
        1. A számítási cél beszerzéséhez válassza a **Létrehozás** lehetőséget. 

            **Ez eltarthat néhány percet.** 

        1. A létrehozás után válassza ki az új számítási célt a legördülő listából.

    1. Kattintson a **Tovább** gombra.

## <a name="select-task-type-and-settings"></a>Feladat típusának és beállításainak kiválasztása

A Machine learning-feladattípus és a konfigurációs beállítások megadásával fejezze be az automatikus ML-kísérlet beállítását.

1. A feladattípus **és beállítások** űrlapon válassza az **idősorozat-előrejelzés** lehetőséget a Machine learning feladattípusként.

1. Válassza ki a **Date (dátum** ) **oszlopot az idő oszlopban** , és hagyja üresen a **Group By oszlop (ok)** értékét. 

    1. Válassza a **további konfigurációs beállítások megtekintése** lehetőséget, és töltse fel a mezőket az alábbiak szerint. Ezek a beállítások hatékonyabban szabályozzák a betanítási feladatot. Ellenkező esetben a rendszer az alapértelmezett értékeket a kísérletezés és az adatértékek alapján alkalmazza.

  
        További&nbsp;konfigurációk|Leírás|&nbsp;oktatóanyag értéke&nbsp;
        ------|---------|---
        Elsődleges metrika| Az értékelési metrika, amelyet a Machine learning algoritmusa fog mérni.|Normalizált legfelső szintű, négyzetes hiba
        Automatikus featurization| Az előfeldolgozás engedélyezése. Ez magában foglalja az automatikus adattisztítást, előkészítést és átalakítást a szintetikus funkciók létrehozásához.| Bekapcsolás
        A legjobb modell magyarázata (előzetes verzió)| A automatikusan mutatja az automatizált ML által létrehozott legjobb modell magyarázatát.| Bekapcsolás
        Letiltott algoritmusok | A betanítási feladatokból kizárni kívánt algoritmusok| Extrém véletlenszerű fák
        További előrejelzési beállítások| Ezek a beállítások segítenek a modell pontosságának javításában <br><br> _**Előrejelzési horizont**_ : a jövőben várhatóan mennyi idő elteltével érdemes <br> _**Előrejelzési cél késései:**_ milyen messzire kívánja állítani a cél változó késéseit <br> _**Cél gördülő ablak**_ : megadja a gördülő ablak méretét, amely felett a funkciók, például a *Max, a min* és a *Sum*érték lesz létrehozva. |Előrejelzési horizont: 14 <br> Előrejelzés&nbsp;cél&nbsp;késések: nincs <br> Cél&nbsp;gördülő&nbsp;ablak&nbsp;mérete: nincs
        Kilépési feltétel| Ha teljesülnek a feltételek, a betanítási feladatok leállnak. |&nbsp;feladatok betanítása&nbsp;idő (óra): 3 <br> Metrika&nbsp;pontszám&nbsp;küszöbérték: nincs
        Ellenőrzés | Válasszon egy több ellenőrzési típust és a tesztek számát.|Érvényesítés típusa:<br>&nbsp;k-szor&nbsp;kereszt-ellenőrzési <br> <br> Érvényességek száma: 5
        Egyidejűség| A másodpercenként végrehajtott párhuzamos ismétlések maximális száma| &nbsp;egyidejű&nbsp;iterációk maximális száma: 6
        
        Kattintson a **Mentés** gombra.

## <a name="run-experiment"></a>Kísérlet futtatása

A kísérlet futtatásához válassza a **Befejezés**lehetőséget. Megnyílik a futtatási **részletek** képernyő, amelyen a Futtatás **állapota** látható a futtatási szám mellett. Ez az állapot frissíti a kísérlet előrehaladását.

>[!IMPORTANT]
> Az előkészítés **10-15 percet** vesz igénybe a kísérlet futtatásának előkészítése érdekében.
> A futása után **az egyes iterációk esetében 2-3 percet**vesz igénybe.  <br> <br>
> Éles környezetben valószínű, hogy ez a folyamat időt vesz igénybe. Várakozás közben javasoljuk, hogy a kipróbált algoritmusokat a kész **modellek** lapon vizsgálja meg. 

##  <a name="explore-models"></a>Modellek megismerése

Navigáljon a **modellek** lapra, és tekintse meg a tesztelt algoritmusokat (modelleket). Alapértelmezés szerint a modelleket metrikai pontszám szerint rendezi a rendszer. Ebben az oktatóanyagban a legmagasabbra értékelt modell, amely a kiválasztott **normalizált legfelső szintű, négyzetes hiba** mérőszáma alapján a legmagasabb, a lista tetején található.

Amíg megvárja az összes kísérleti modell befejeződését, válassza ki a befejezett modell **algoritmusának nevét** a teljesítmény részleteinek megismeréséhez. 

A következő példa a **modell részletei** és a **vizualizációk** lapfülek segítségével jeleníti meg a kiválasztott modell tulajdonságait, metrikáit és teljesítmény-diagramját. 

![Futtatás részletei](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>A modell üzembe helyezése

A Azure Machine Learning Studióban található automatizált gépi tanulás lehetővé teszi a legjobb modell üzembe helyezését webszolgáltatásként néhány lépésben. Az üzembe helyezés a modell integrációja, így előre jelezhető az új adatmennyiség, és azonosíthatók a lehetséges lehetőségek is. 

Ebben a kísérletben a webszolgáltatások üzembe helyezése azt jelenti, hogy a bike Share vállalat most már rendelkezik egy iterációs és méretezhető webes megoldással, amellyel előre jelezheti a kerékpárok megosztását. 

A Futtatás befejezése után térjen vissza a **Futtatás részletei** lapra, és válassza a **modellek** fület.

Ebben a kísérleti kontextusban a **StackEnsemble** a legjobb modellnek számít a **normalizált legfelső szintű, négyzetes hiba** mérőszáma alapján.  Ezt a modellt üzembe helyezjük, de javasoljuk, hogy az üzembe helyezés körülbelül 20 percet vesz igénybe. Az üzembe helyezési folyamat több lépést is magában foglal, beleértve a modell regisztrálását, az erőforrások létrehozását és a webszolgáltatás konfigurálását.

1. Kattintson a **legjobb modell telepítése** gombra a bal alsó sarokban.

1. Töltse fel a **modell üzembe helyezése** panelt az alábbiak szerint:

    Mező| Érték
    ----|----
    Központi telepítés neve| bikeshare – üzembe helyezés
    Központi telepítés leírása| a bike Share kereslet üzembe helyezése
    Számítási típus | Azure számítási példány kiválasztása (ACI)
    Hitelesítés engedélyezése| Megbénít. 
    Egyéni központi telepítési eszközök használata| Megbénít. A Letiltás beállítás megadása esetén az alapértelmezett illesztőprogram-fájl (pontozási parancsfájl) és a környezeti fájl automatikusan létrejön. 
    
    Ebben a példában a *speciális* menüben megadott alapértékeket használjuk. 

1. Válassza az **Üzembe helyezés** lehetőséget.  

    A **futtatási** képernyő felső részén megjelenik egy zöld sikert jelző üzenet, amely szerint a központi telepítés sikeresen elindult. A központi telepítés előrehaladása a következő lehet  
    az **ajánlott modell** ablaktáblán az **üzembe helyezés állapota**alatt.
    
Miután az üzembe helyezés sikeres volt, rendelkezik egy operatív webszolgáltatással az előrejelzések létrehozásához. 

Folytassa a [**következő lépésekkel**](#next-steps) , ha többet szeretne megtudni az új webszolgáltatás használatáról, és tesztelje a jóslatokat a Power BI beépített Azure Machine learning-támogatásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az üzembe helyezési fájlok nagyobb méretűek, mint az adatmennyiség és a kísérlet során használt fájlok. Csak a központi telepítési fájlokat törölje, hogy csökkentse a költségeket a fiókjához, vagy ha meg szeretné őrizni a munkaterületet és a kísérletet. Ellenkező esetben törölje a teljes erőforráscsoportot, ha nem tervezi az egyik fájl használatát.  

### <a name="delete-the-deployment-instance"></a>A központi telepítési példány törlése

Törölje a központi telepítési példányt a Azure Machine Learning studióból, ha meg szeretné tartani az erőforráscsoportot és a munkaterületet más oktatóanyagok és feltárás céljából. 

1. Nyissa meg a [Azure Machine learning studiót](https://ml.azure.com/). Navigáljon a munkaterülethez, és a bal oldalon az **eszközök** ablaktáblán válassza a **végpontok**lehetőséget. 

1. Válassza ki a törölni kívánt központi telepítést, és válassza a **Törlés**lehetőséget. 

1. Válassza a **Folytatás**lehetőséget.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az automatikus ML-t használta a Azure Machine Learning Studióban egy idősorozat-előrejelzési modell létrehozásához és üzembe helyezéséhez, amely előre jelezi a bike Share bérleti igényét. 

Ebből a cikkből megtudhatja, hogyan hozhat létre Power BI támogatott sémát az újonnan telepített webszolgáltatás felhasználásának megkönnyítésére:

> [!div class="nextstepaction"]
> [Webszolgáltatás felhasználása](how-to-consume-web-service.md#consume-the-service-from-power-bi)


>[!NOTE]
> Ez a kerékpáros megosztási adatkészlet módosult ehhez az oktatóanyaghoz. Ezt az adatkészletet egy [Kaggle-verseny](https://www.kaggle.com/c/bike-sharing-demand/data) részeként tették elérhetővé, és eredetileg a [Capital Bikeshare](https://www.capitalbikeshare.com/system-data)keresztül adták ki. Az [UCI Machine learning adatbázisban](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)is megtalálható.<br><br>
> Forrás: Fanaee-T, hadi és Gama, Joao, esemény címkézése az Ensemble-érzékelők és a háttérbeli ismeretek kombinálásával, a mesterséges intelligencia előrehaladásával (2013): PP. 1-15, Springer Berlin Heidelberg.
