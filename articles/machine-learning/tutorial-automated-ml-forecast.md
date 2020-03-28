---
title: Előrejelzés kerékpár megosztási kereslet automatizált ML-kísérlet
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan taníthat be és helyezhet üzembe egy igény-előrejelzési modellt az Azure Machine Learning stúdióban az automatikus gépi tanulással.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 01/27/2020
ms.openlocfilehash: 11e0a8a0076fb2e68c379b279f471ff74846df2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77088241"
---
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>Oktatóanyag: Előrejelzés a kerékpármegosztási igényről az automatizált gépi tanulással
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben az oktatóanyagban az Azure Machine Learning stúdióban automatikus gépi tanulást vagy automatikus ml-t használ egy idősorozat-előrejelzési modell létrehozásához a kerékpármegosztási szolgáltatás bérleti igényének előrejelzéséhez.

Ebben az oktatóanyagban megtudhatja, hogyan kell elvégezni a következő feladatokat:

> [!div class="checklist"]
> * Adatkészlet létrehozása és betöltése.
> * Konfiguráljon és futtasson egy automatikus gépi tanulási kísérletet.
> * Fedezze fel a kísérlet eredményeit.
> * Telepítse a legjobb modellt.

## <a name="prerequisites"></a>Előfeltételek

* Egy Nagyvállalati kiadású Azure Machine Learning-munkaterület. Ha nem rendelkezik munkaterülettel, [hozzon létre egy Enterprise edition munkaterületet.](how-to-manage-workspace.md) 
    * Az Automatikus gépi tanulás az Azure Machine Learning stúdióban csak nagyvállalati kiadású munkaterületeken érhető el. 
* A [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) adatfájl letöltése

## <a name="get-started-in-azure-machine-learning-studio"></a>Első lépések az Azure Machine Learning stúdióban

Ebben az oktatóanyagban az automatikus ML-kísérletet az Azure Machine Learning-stúdióban hozza létre, amely egy összevont felület, amely gépi tanulási eszközöket tartalmaz az adatelemzési forgatókönyvek végrehajtásához az adatelemzési szakemberek számára minden képzettségi szinten. A stúdió nem támogatott az Internet Explorer böngészőiben.

1. Jelentkezzen be az [Azure Machine Learning stúdióba.](https://ml.azure.com)

1. Válassza ki az előfizetést és a létrehozott munkaterületet.

1. Válassza **az Első lépések lehetőséget.**

1. A bal oldali ablaktáblában válassza az **Automatikus hibahelyreállítás** lehetőséget a **Szerző** szakaszban.

1. Válassza a **+Új automatikus gépelési futtatás lehetőséget.** 

## <a name="create-and-load-dataset"></a>Adatkészlet létrehozása és betöltése

A kísérlet konfigurálása előtt töltse fel az adatfájlt a munkaterületre egy Azure Machine Learning-adatkészlet formájában. Ezzel biztosíthatja, hogy az adatok a kísérletnek megfelelően legyenek formázva.

1. Az **Adatkészlet kiválasztása** űrlapon válassza a Helyi **fájlokból** lehetőséget a **+Adatkészlet létrehozása** legördülő menüből. 

    1. Az **Alapszintű információ** űrlapon adjon nevet az adatkészletnek, és adjon meg egy nem kötelező leírást. Az adatkészlet típusa alapértelmezés szerint **táblázatos,** mivel az automatikus ML-t az Azure Machine Learning studio jelenleg csak támogatja a táblázatos adatkészletek.
    
    1. Válassza a **Tovább** gombot a bal alsó sarokban

    1. Az **Adattár és a fájlkijelölési** űrlapon válassza ki azt az alapértelmezett adattalapot, amelyet a munkaterület létrehozása során automatikusan beállított, **a workspaceblobstore (Azure Blob Storage) szolgáltatást.** Ez az a tárolóhely, ahol fel szeretné tölteni az adatfájlt. 

    1. Válassza a **Tallózás** lehetőséget. 
    
    1. Válassza ki a **kerékpár-no.csv** fájlt a helyi számítógépen. Ez az a fájl, amelyet [előfeltételként](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)töltött le.

    1. Válassza a **Tovább lehetőséget**

       Amikor a feltöltés befejeződött, a Beállítások és az előnézeti űrlap a fájltípus alapján előre ki van töltve. 
       
    1. Ellenőrizze, hogy a **Beállítások és az előnézeti** űrlap az alábbiak szerint van-e feltöltve, és válassza a **Tovább**gombot.
        
        Mező|Leírás| Az oktatóanyag értéke
        ---|---|---
        Fájlformátum|A fájlban tárolt adatok elrendezését és típusát határozza meg.| Tagolt
        Elválasztó|Egy vagy több karakter a&nbsp; különálló, független területek közötti határ megadásához egyszerű szövegben vagy más adatfolyamokban. |Vessző
        Encoding|Azt határozza meg, hogy milyen karaktersématáblát használjon az adatkészlet olvasásához.| UTF-8
        Oszlopfejlécek| Azt jelzi, hogy az adatkészlet fejléceit (ha vannak ilyenek) hogyan kezeli a rendszer.| Az első fájlfejlécek használata
        Sorok kihagyása | Azt jelzi, hogy hány, ha van ilyen sor kimarad az adatkészletből.| None

    1. A **Séma** űrlap lehetővé teszi az adatok további konfigurálását ehhez a kísérlethez. 
    
        1. Ebben a példában válassza az **alkalmi** és **a regisztrált** oszlopok figyelmen kívül hagyását. Ezek az oszlopok a **cnt** oszlop lebontását képezik, ezért nem vesszük fel őket.

        1. Ebben a példában hagyja meg a **Tulajdonságok** és a **Típus**alapértelmezett beállításait is. 
        
        1. Válassza a **Tovább lehetőséget.**

    1. A **Részletek megerősítése** képernyőn ellenőrizze, hogy az adatok megegyeznek-e az **Alapszintű adatok** és **beállítások és előnézeti** űrlapokon korábban kitöltött adatokkal.

    1. Az adatkészlet létrehozásának befejezéséhez válassza a **Létrehozás** gombot.

    1. Jelölje ki az adatkészletet, ha az megjelenik a listában.

    1. Válassza a **Tovább lehetőséget.**

## <a name="configure-experiment-run"></a>Kísérletfuttatás konfigurálása

Az adatok betöltése és konfigurálása után állítsa be a távoli számítási célt, és válassza ki, hogy az adatok melyik oszlopát szeretné előre jelezni.

1. A **Konfigurálás űrlap** feltöltése a következőképpen:
    1. Adja meg a kísérlet nevét:`automl-bikeshare`

    1. Válassza ki a **cnt-t** céloszlopként, hogy mit szeretne előre jelezni. Ez az oszlop az összes kerékpármegosztás-kölcsönzés számát mutatja.

    1. Válassza **az Új számítás létrehozása lehetőséget,** és konfigurálja a számítási célt. Az automatikus ml csak az Azure Machine Learning-számítást támogatja. 

        Mező | Leírás | Az oktatóanyag értéke
        ----|---|---
        Számítási név |Egyedi név, amely azonosítja a számítási környezetet.|kerékpár-számítás
        Virtuális&nbsp;&nbsp;gép mérete| Válassza ki a virtuális gép méretét a számítási.|Standard_DS12_V2
        Min / Max csomópontok (speciális beállítások)| A profiladatok hoz meg kell adnia 1 vagy több csomópontot.|Min csomópontok: 1<br>Maximális csomópontok: 6
  
        1. Válassza a **Létrehozás** lehetőséget a számítási cél leszámításához. 

            **Ez néhány percet vesz igénybe.** 

        1. A létrehozás után válassza ki az új számítási célt a legördülő listából.

    1. Válassza a **Tovább lehetőséget.**

## <a name="select-task-type-and-settings"></a>Tevékenységtípus és -beállítások kiválasztása

Végezze el az automatikus gépi tanulási kísérlet beállítását a gépi tanulási feladat típusának és konfigurációs beállításainak megadásával.

1. A **Feladat típusa és beállításai** képernyőn válassza az **Idősorozat-előrejelzés lehetőséget** gépi tanulási feladattípusként.

1. Válassza ki a **dátumot** **időoszlopként,** és hagyja üresen **a Csoportosítás oszlop(ok)ot.** 

    1. Válassza **a További konfigurációs beállítások megtekintése** lehetőséget, és a következőképpen kell felnagyítania a mezőket. Ezek a beállítások a képzési feladat jobb vezérlésére. Ellenkező esetben az alapértelmezett értékek a kísérlet kiválasztása és az adatok alapján kerülnek alkalmazásra.

  
        További&nbsp;konfigurációk|Leírás|Az&nbsp;&nbsp;oktatóanyag értéke
        ------|---------|---
        Elsődleges mutató| Kiértékelési metrika, amely a gépi tanulási algoritmus mérik.|Normalizált gyökér középérték négyzetes hiba
        Automatikus featurization| Engedélyezi az előfeldolgozást. Ez magában foglalja az automatikus adattisztítást, előkészítést és átalakítást a szintetikus funkciók létrehozásához.| Bekapcsolás
        A legjobb modell magyarázata (előzetes verzió)| Automatikusan mutatja a magyarázhatóság a legjobb modell által létrehozott automatizált ML.| Bekapcsolás
        Blokkolt algoritmusok | A képzési feladatból kizárni kívánt algoritmusok| Extrém véletlenszerű fák
        További előrejelzési beállítások| Ezek a beállítások javítják a modell pontosságát <br><br> _**Előrejelzési horizont**_: az előre megjósolandó jövőbeli idő hossza <br> _**Előrejelzés cél elmarad:**_ milyen messze vissza szeretné építeni a lemaradása a cél változó <br> _**Cél gördülő ablak:**_ megadja a gördülő ablak méretét, amely felett a rendszer a következő jellemzőket hozza létre, mint például a *max, a min* és az *összeg.* |Előrejelzési horizont: 14 <br> Előrejelzés&nbsp;&nbsp;cél elmarad: Nincs <br> Cél&nbsp;&nbsp;gördülő&nbsp;ablak mérete: Nincs
        Kilépési feltétel| Ha egy feltétel teljesül, a képzési feladat leáll. |Képzési&nbsp;&nbsp;munka ideje (óra): 3 <br> Metrikapontszám&nbsp;&nbsp;küszöbértéke: Nincs
        Ellenőrzés | Válassza ki a keresztérvényesítés típusát és a tesztek számát.|Érvényesítés típusa:<br>&nbsp;k-szeres&nbsp;keresztérvényesítés <br> <br> Ellenőrzések száma: 5
        Egyidejűség| Az iterációnként végrehajtott párhuzamos ismétlések maximális száma| Maximális&nbsp;egyidejű&nbsp;ismétlések: 6
        
        Kattintson a **Mentés** gombra.

## <a name="run-experiment"></a>Kísérlet futtatása

A kísérlet futtatásához válassza a **Befejezés gombot.** A **Futtatás részletei** képernyő a futtatási szám tetején a Futtatás **állapottal** nyílik meg. Ez az állapot a kísérlet előrehaladtával frissül.

>[!IMPORTANT]
> Az előkészítés **10-15 percet** vesz igénybe a kísérlet futásának előkészítése során.
> Ha fut, tart **2-3 perc alatt több minden iteráció**.  <br> <br>
> A termelés, akkor valószínűleg sétára egy kicsit, mivel ez a folyamat időt vesz igénybe. Amíg várakozik, javasoljuk, hogy a **modellek** lapon kezdje el a tesztelt algoritmusok feltárását, amint azok befejeződnek. 

##  <a name="explore-models"></a>Fedezze fel a modelleket

A **Modellek** lapon megtekintheti a tesztelt algoritmusokat (modelleket). Alapértelmezés szerint a modellek metrikapontszám szerint vannak rendezve, ahogy befejeződnek. Ebben az oktatóanyagban a modell, amely a legmagasabb pontszámot a kiválasztott **normalizált gyökér átlagos négyzetes hiba** metrika alapján a lista tetején található.

Amíg megvárja, amíg az összes kísérletmodell befejeződik, válassza ki a befejezett modell **algoritmus nevét** a teljesítmény részleteinek megismeréséhez. 

A következő példa végighalad a **Modell részletei** és a **Vizualizációk** lapon a kiválasztott modell tulajdonságainak, mutatóinak és teljesítménydiagramjainak megtekintéséhez. 

![Részletek futtatása](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Az Azure Machine Learning stúdióban automatikus gépi tanulás lehetővé teszi, hogy a legjobb modellt webszolgáltatásként néhány lépésben telepítse. A központi telepítés a modell integrációja, így előre jelezheti az új adatokat, és azonosíthatja a lehetőségek lehetséges területeit. 

Ehhez a kísérlethez a webes szolgáltatásüzembe helyezés azt jelenti, hogy a kerékpármegosztási vállalat rendelkezik egy iteratív és skálázható webes megoldással a kerékpármegosztási igény előrejelzéséhez. 

A futtatás befejezése után lépjen vissza a **Részletek futtatása** lapra, és válassza a **Modellek** lapot.

Ebben a kísérleti környezetben **StackEnsemble** tartják a legjobb modell, a **normalizált gyökér átlagos négyzetes hiba** metrika alapján.  Ezt a modellt üzembe helyezjük, de figyelemszerint az üzembe helyezés körülbelül 20 percet vesz igénybe. A telepítési folyamat több lépést foglal magában, beleértve a modell regisztrálását, az erőforrások generálását és a webszolgáltatáshoz való konfigurálását.

1. Válassza a **Legjobb modell telepítése** gombot a bal alsó sarokban.

1. A Modell **telepítése** ablaktábla feltöltése az alábbiak szerint:

    Mező| Érték
    ----|----
    Központi telepítés neve| bikeshare-telepítés
    A telepítés leírása| kerékpár megosztási igény telepítése
    Számítási típus | Az Azure Compute Instance (ACI) kiválasztása
    Hitelesítés engedélyezése| Letiltás lehetőséget. 
    Egyéni központi telepítési eszközök használata| Letiltás lehetőséget. A letiltás lehetővé teszi az alapértelmezett illesztőprogram-fájl (pontozási parancsfájl) és a környezeti fájl automatikus generálását. 
    
    Ebben a példában a *Speciális* menüben megadott alapértelmezett értékeket használjuk. 

1. Válassza az **Üzembe helyezés** lehetőséget.  

    A **Futtatás** képernyő tetején egy zöld sikerüzenet jelenik meg, amely szerint a telepítés sikeresen elindult. A telepítés előrehaladása megtalálható  
    az **Ajánlott modell** ablaktáblában a **Telepítés állapot csoportban.**
    
Miután az üzembe helyezés sikeres, egy működő webszolgáltatás előrejelzések létrehozásához. 

Folytassa a [**következő lépésekkel,**](#next-steps) ha többet szeretne megtudni az új webszolgáltatás felhasználásáról, és tesztelheti az előrejelzéseket az Azure Machine Learning beépített támogatásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A központi telepítési fájlok nagyobbak, mint az adatok és a kísérleti fájlok, így többe kerülnek a tárolásukhoz. Csak a központi telepítési fájlokat törölje a fiók költségeinek minimalizálása érdekében, vagy ha meg szeretné tartani a munkaterületet és a kísérleti fájlokat. Ellenkező esetben törölje a teljes erőforráscsoportot, ha nem tervezi a fájlok használatát.  

### <a name="delete-the-deployment-instance"></a>A központi telepítési példány törlése

Csak a központi telepítési példányt az Azure Machine Learning stúdióból törölje, ha meg szeretné tartani az erőforráscsoportot és a munkaterületet más oktatóanyagokés feltáráshoz. 

1. Nyissa meg az [Azure Machine Learning stúdiót.](https://ml.azure.com/) Nyissa meg a munkaterületet, és az **Eszközök** ablaktábla bal oldalán válassza a **Végpontok**lehetőséget. 

1. Jelölje ki a törölni kívánt központi telepítést, és válassza a **Törlés**lehetőséget. 

1. Válassza a **Folytatás lehetőséget.**

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Machine Learning-stúdióban automatikus ml-t használt egy idősorozat-előrejelzési modell létrehozásához és üzembe helyezéséhez, amely előre jelzi a kerékpármegosztás-kölcsönzési igényt. 

Ebből a cikkből megtudhatja, hogyan hozhat létre power BI-támogatott sémát az újonnan üzembe helyezett webszolgáltatás felhasználásának megkönnyítése érdekében:

> [!div class="nextstepaction"]
> [Webszolgáltatás felhasználása](how-to-consume-web-service.md#consume-the-service-from-power-bi)


>[!NOTE]
> Ez a kerékpármegosztási adatkészlet az oktatóanyaghoz módosult. Ezt az adatkészletet egy [Kaggle-verseny](https://www.kaggle.com/c/bike-sharing-demand/data) részeként bocsátották rendelkezésre, és eredetileg a [Capital Bikeshare-en](https://www.capitalbikeshare.com/system-data)keresztül volt elérhető. Az [UCI Machine Learning adatbázisban](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)is megtalálható.<br><br>
> Forrás: Fanaee-T, Hadi, és Gama, Joao, Esemény címkézés ötvözi együttes detektorok és a háttér ismeretek, Progress in Artificial Intelligence (2013): pp. 1-15, Springer Berlin Heidelberg.
