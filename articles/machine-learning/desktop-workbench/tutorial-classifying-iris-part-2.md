---
title: Modell-létrehozási útmutató az Azure Machine Learning-szolgáltatásokhoz (előzetes verzió) | Microsoft Docs
description: Ez a részletes oktatóanyag bemutatja, hogyan használhatók ki teljeskörűen az (előzetes verziójú) Azure Machine Learning-szolgáltatások. Ez a 2. rész, amely a kísérletezést ismerteti.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/15/2018
ms.openlocfilehash: 4f9b14762332bd11fd69a855d8fabdb206e34919
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "41917569"
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>2. oktatóanyag – Írisz osztályozása: Modell létrehozása
Az (előzetes verziójú) Azure Machine Learning-szolgáltatások az adatszakértők számára készült, az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőszinten való üzembe helyezéséhez létrehozott integrált és fejlett adatelemzési megoldások.

Ez az oktatóanyag **egy háromrészes sorozat második része**. Az oktatóanyagnak ebben a részében az Azure Machine Learning-szolgáltatások segítségével a következőket hajtja végre:

> [!div class="checklist"]
> * Szkriptek megnyitása és kód áttekintése
> * Szkriptek végrehajtása helyi környezetben
> * Futtatási előzmények áttekintése
> * Szkriptek végrehajtása helyi Azure CLI ablakban
> * Szkriptek végrehajtása helyi Docker-környezetben
> * Szkriptek végrehajtása távoli Docker-környezetben
> * Szkriptek végrehajtása a felhőalapú Azure HDInsight-környezetben

Az oktatóanyag a jól ismert [Iris flower adatkészletet](https://en.wikipedia.org/wiki/Iris_flower_data_set) használja. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:
- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
- Az ebben a [rövid útmutatóban](../service/quickstart-installation.md) ismertetett kísérletezési fiók és telepített Azure Machine Learning Workbench.
- Az [oktatóanyag 1. részében](tutorial-classifying-iris-part-1.md) szereplő projekt és előkészített Iris-adatok.
- Egy helyben telepített és futtatott Docker-motor. A Docker Community Edition változata is megfelel. A Docker telepítésének módjáról lásd: https://docs.docker.com/engine/installation/.

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Az iris_sklearn.py és a konfigurációs fájlok áttekintése

1. Indítsa el az Azure Machine Learning Workbench alkalmazást.

1. Nyissa meg az [oktatóanyag-sorozat 1. részében](tutorial-classifying-iris-part-1.md) létrehozott **myIris** projektet.

2. A megnyitott projektben a bal szélső ablaktáblán kattintson a **Fájlok** gombra (mappa ikon) a projektmappa fájllistájának megnyitásához.

   ![Az Azure Machine Learning Workbench-projekt megnyitása](media/tutorial-classifying-iris/2-project-open.png)

3. Válassza ki az **iris_sklearn.py** Python-szkriptfájlt. 

   ![Szkript kiválasztása](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   A Workbench alkalmazásban egy új szövegszerkesztő lapon megnyílik a kód. Ez az a szkript, amelyet az oktatóanyag jelen szakaszában használni fog. 

   >[!NOTE]
   >Lehet, hogy a megjelenő kód nem egyezik a fenti kóddal, mivel a mintaprojekt rendszeresen frissül.
   
   ![Fájl megnyitása](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. Vizsgálja meg közelebbről a Python-szkriptkódot, hogy megismerkedjen a kódolási stílussal. 

   Az **iris_sklearn.py** szkript a következő feladatokat hajtja végre:

   * Betölti az alapértelmezett **iris.dprep** adatelőkészítési csomagot, és létrehoz egy [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)-et. 

   * Véletlenszerű funkciókat ad hozzá, hogy nehezítse a probléma megoldását. A véletlenszerűségre azért van szükség, mert az Iris egy kisméretű adatkészlet, amely könnyedén osztályozható közel 100%-os pontossággal.

   * Létrehoz egy logisztikai regressziós modellt a [scikit-learn](http://scikit-learn.org/stable/index.html) Machine Learning-kódtár használatával.  Ez a kódtár jár az Azure Machine Learning Workbench mellé.

   * A [pickle](https://docs.python.org/3/library/pickle.html) kódtár segítségével szerializálja a modellt egy fájlba, az `outputs` mappába. 
   
   * Betölti a szerializált modellt, majd deszerializálja a memóriába.

   * A deszerializált modell segítségével előrejelzést végez egy új rekordon. 

   * A [matplotlib](https://matplotlib.org/) kódtár segítségével megjelenít két gráfot (egy keveredési mátrixot és egy többcsoportos ROC-görbét), majd menti őket az `outputs` mappába. Ezt a kódtárat, ha még nem telepítette, telepítheti a környezetében.

   * Automatikusan megjeleníti a szabályozási arányt és a modell pontosságát a futtatási előzményekben. A folyamat során a `run_logger` objektumot használja a regularizációs arány rögzítésére és a pontosság modellezésére a naplókban. 


## <a name="run-irissklearnpy-in-your-local-environment"></a>Az iris_sklearn.py futtatása a helyi környezetben

1. Az Azure Machine Learning parancssori felületének (CLI) elindítása:
   1. Indítsa el az Azure Machine Learning Workbenchet.

   1. A Workbench menüben válassza a **Fájl** > **Parancssor megnyitása** elemet. 
   
   Az Azure Machine Learning parancssori felületének (CLI) ablaka Windows rendszeren a `C:\Temp\myIris\>` projektmappában indul. Ez az oktatóanyag 1. részében létrehozott projekt.

   >[!IMPORTANT]
   >A következő lépéseket ezzel a parancssori felülettel kell elvégeznie.

1. A parancssori felület ablakában telepítse a **matplotlib** nevű Python megjelenítési kódtárat, ha még nincs telepítve.

   Az **iris_sklearn.py** szkript két Python-csomagban rendelkezik függőségekkel, amelyek a **scikit-learn** és a **matplotlib**.  Az Azure Machine Learning Workbench a kényelmes használat érdekében magától telepíti a **scikit-learn** csomagot, de a **matplotlib** csomagot is telepítenie kell, ha még nincs telepítve.

   Ha a **matplotlib** telepítése nélkül folytatja, a jelen oktatóanyagban szereplő kód továbbra is sikeresen futtatható, de nem lesz képes létrehozni a keveredési mátrix kimenetét és a többcsoportos ROC-görbét az előzménymegjelenítésekben láthatóak szerint.

   ```azurecli
   pip install matplotlib
   ```

   A telepítése körülbelül egy percig tart.

1. Térjen vissza a Workbench alkalmazásba. 

1. Keresse meg az **iris_sklearn.py** nevű lapot. 

   ![Lap megkeresése szkripttel](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. A lap tetején található eszköztárban válassza a **local** végrehajtási környezetet és az `iris_sklearn.py` szkriptet a végrehajtáshoz. Előfordulhat, hogy már ki vannak választva.

   ![Local és szkript kiválasztása](media/tutorial-classifying-iris/2-local-script.png)

1. Az eszköztár jobb oldalán adja meg a `0.01` értéket az **Argumentumok** mezőben. 

   Ez az érték megfelel a logisztikai regressziós modell regularizációs arányának.

   ![Local és szkript kiválasztása](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. Kattintson a **Futtatás** gombra. Azonnal be lesz ütemezve egy feladat. A feladat megjelenik a Workbench-ablak jobb oldalán található **Feladatok** panelen. 

   ![Local és szkript kiválasztása](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   A feladat állapota pár pillanat múlva **Elküldés** értékről **Fut** értékre, végül **Befejezve** értékre vált.

1. Válassza ki a **Befejezve** elemet a **Feladatok** panel állapotszöveg részén. 

   ![Az sklearn futtatása](media/tutorial-classifying-iris/2-completed.png)

   Megnyílik egy előugró ablak az aktuális futtatás standard kimenetre (stdout) küldött szövegével. Az stdout szöveg bezárásához kattintson az előugró ablak jobb felső sarkában található **Bezárás** (**x**) gombra.

   ![Standard kimenet](media/tutorial-classifying-iris/2-standard-output.png)

9. A **Feladatok** panel ugyanazon feladatállapotában jelölje ki a **Befejezve** állapot fölött látható kék színű **iris_sklearn.py [n]** (_n_ futtatás száma) feliratot és a kezdési időpontot. Az ekkor megnyíló **Futtatás tulajdonságai** ablakban az adott futtatás következő adatai láthatók:
   - A **futtatás tulajdonságaihoz** kapcsolódó információk
   - **Kimenetek**
   - **Metrikák**
   - **Megjelenítések** (ha van ilyen)
   - **Naplók** 

   Amikor a futtatás befejeződik, az előugró ablakban az alábbi eredmények jelennek meg:

   >[!NOTE]
   >Mivel korábban az oktatóanyag bevezetett némi véletlenszerűsítést a gyakorlókészletbe, a pontos eredmények kissé eltérhetnek az itt bemutatottaktól.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```
    
10. Zárja be a **Futtatás tulajdonságai** lapot, majd térjen vissza az **iris_sklearn.py** lapra. 

11. A további futtatásoknál ismételje ezt meg. 

    Adjon meg `0.001` és `10` közötti értékeket az **Argumentumok** mezőben. A **Futtatás** elemet választva futtassa a kódot még néhányszor. Az argumentumérték, amelyet minden alkalommal megváltoztat, bekerül a kód logisztikai regressziós modelljébe, így minden alkalommal eltérő eredményt fog kapni.

## <a name="review-the-run-history-in-detail"></a>A futtatási előzmények részletes áttekintése
Az Azure Machine Learning Workbench alkalmazás minden szkriptvégrehajtást rögzít futtatási előzményrekordként. A **Futtatások** nézetet megnyitva megtekintheti egy adott szkript futtatási előzményeit.

1. A **Futtatások** listájának megnyitásához kattintson a **Futtatások** gombra (óra ikon) a bal oldali eszköztáron. Ezután az **iris_sklearn.py** elemet választva megjelenítheti az `iris_sklearn.py` **Futtatási irányítópultját**.

   ![Futtatásnézet](media/tutorial-classifying-iris/run_view.png)

1. Megnyílik a **Futtatási irányítópult** lap. 

   Áttekintheti a több futtatás során rögzített statisztikákat. A diagramok a lap tetején jelennek meg. Mindegyik futtatás egy sorban növekvő sorszámmal rendelkezik, és a futtatások részletei a képernyő alján lévő táblázatban vannak felsorolva.

   ![Futtatási irányítópult](media/tutorial-classifying-iris/run_dashboard.png)

1. Szűrheti a táblázatot, majd az egyes diagramokra kattintva megtekintheti a futtatások állapotát, időtartamát, pontosságát és regularizációs arányát. 

1. A **Futtatások** táblázatban jelölje be két vagy több futtatás jelölőnégyzetét. Válassza az **Összehasonlítás** gombot a részletes összehasonlító ablaktábla megnyitásához. Tekintse át az összehasonlító táblázatot. 

1. Az **Összehasonlítás** ablaktábla bal felső részén található **Futtatási lista** gombra kattintva térjen vissza a **futtatási irányítópulthoz**.

   ![Vissza a Futtatási listához](media/tutorial-classifying-iris/2-compare-back.png)

1. Kijelölhet egy adott futtatást a futtatás részletes nézetének megtekintéséhez. Figyelje meg, hogy a **Futtatás tulajdonságai** szakaszban megjelennek a kiválasztott futtatás statisztikái. A kimeneti mappába írt fájlok a **Kimenetek** szakaszban vannak felsorolva, és innen tudja azokat letölteni.

   ![Futtatás részletei](media/tutorial-classifying-iris/run_details.png)

   A két grafikon, a keveredési mátrix és a többcsoportos ROC-görbe a **Képi megjelenítések** szakaszban látható. A naplófájlokat is megtalálhatja a **Naplók** szakaszban.


## <a name="run-scripts-in-local-docker-environments"></a>Szkriptek futtatása helyi Docker-környezetekben

Opcionálisan kísérletezhet a szkriptek helyi Docker-tárolón történő futtatásával is. További végrehajtási környezeteket is konfigurálhat, például a Dockert, és azokban is futtathatja a szkriptjét. 

>[!NOTE]
>Ha kísérletezni kíván a szkriptek távoli Azure-beli virtuális gépen található Docker-tárolókon vagy Azure HDInsight Spark-fürtön történő futtatásával, kövesse az [Ubuntu-alapú Azure-os adatelemzési virtuális gép vagy HDInsight-fürt létrehozására vonatkozó utasításokat](how-to-create-dsvm-hdi.md).

1. Ha még nem tette meg, telepítse és indítsa el a Dockert helyileg Windows vagy MacOS rendszerű gépén. További információkért tekintse át a Docker telepítési utasításait: https://docs.docker.com/install/. A Community Edition változat is megfelel.

1. A bal oldali ablaktáblán válassza ki a **Mappa** ikont a projekt **Fájlok** listájának megnyitásához. Bontsa ki az `aml_config` mappát. 

2. Több előre konfigurált környezet is létezik: a **docker-python**, a **docker-spark** és a **local**. 

   Minden környezethez két fájl tartozik, például `docker.compute` (**docker-python** és **docker-spark** esetében is) és `docker-python.runconfig`. Az egyes fájlokat megnyitva láthatja, hogy néhány lehetőséget konfigurálni lehet a szövegszerkesztőben.  

   A megnyitott szövegszerkesztő-lapokat a **Bezárás** (**x**) gombra kattintva zárhatja be.

3. Futtassa az **iris_sklearn.py** szkriptet a **docker-python** környezetben: 

   - A bal oldali eszköztáron kattintson az **Óra** ikonra a **Futtatások** ablaktábla megnyitásához. Válassza a **Minden futtatás** elemet. 

   - A **Minden futtatás** lap tetején az alapértelmezett **helyi** cél környezet helyett válassza a **docker-python** elemet. 

   - Ezután a jobb oldalon válassza ki az **iris_sklearn.py** fájlt futtatandó szkriptként. 

   - Az **Argumentumok** mezőt hagyja üresen, mivel a szkript egy alapértelmezett értéket fog megadni. 

   - Kattintson a **Futtatás** gombra.

4. Figyelje meg, hogy a rendszer elindít egy új feladatot. Ez a Workbench-ablak jobb oldalán található **Feladatok** ablaktáblában jelenik meg.

   Ha az első alkalommal végez futtatást Docker-környezetben, a feladat befejezése néhány perccel tovább tart. 

   Az Azure Machine Learning Workbench a háttérben létrehoz egy új Docker-fájlt. 
   Az új fájl a `docker.compute` fájlban megadott alap Docker-rendszerképre és a `conda_dependencies.yml` fájlban megadott függőségi Python-csomagokra hivatkozik. 
   
   A Docker-motor a következő feladatokat hajtja végre:

    - Letölti az alaprendszerképet az Azure-ból.
    - Telepíti a `conda_dependencies.yml` fájlban megadott Python-csomagokat.
    - Elindít egy Docker-tárolót.
    - A futtatási konfigurációtól függően lemásolja a projektmappa helyi példányát vagy hivatkozik arra.      
    - Végrehajtja az `iris_sklearn.py` szkriptet.

   Végül ugyanazokat az eredményeket kell kapnia, mint amikor a **local** környezetet célozza meg.

5. Most pedig próbáljuk ki a Sparkot. Az alap Docker-rendszerkép tartalmaz egy előre telepített és beállított Spark-példányt, amelyet használhat egy PySpark-szkript végrehajtására. Ezen alapszintű rendszerkép használata egyszerű módja annak, hogy saját Spark-programot fejlesszen és teszteljen anélkül, hogy időt kelljen szánnia a Spark telepítésére és konfigurálására. 

   Nyissa meg az `iris_spark.py` fájlt. Ez a szkript betölti az `iris.csv` adatfájlt, és a Spark Machine Learning-kódtár logisztikai regressziós algoritmusával besorolja az Írisz adatkészletet. Módosítsa a futtatási környezetet **docker-spark** környezetre, a szkriptet pedig az **iris_spark.py** fájlra, majd futtassa újra. Ez a folyamat egy kicsit több időt fog igénybe venni, mivel létre kell hozni egy Spark-munkamenetet, és el kell indítani azt a Docker-tárolóban. Azt is észreveheti, hogy az stdout eltér az `iris_spark.py` stdout kimenetétől.

6. Futtassa még néhányszor, és adjon meg különböző argumentumokat. 

7. Nyissa meg az `iris_spark.py` fájlt a Spark Machine Learning-kódtár segítségével létrehozott logisztikai regressziós modell megtekintéséhez. 

8. Használja a **Feladatok** ablaktáblát, futtasson egy előzmények listanézetet és egy részletek nézetet a különböző végrehajtási környezetekben végzett futtatásoknál.

## <a name="run-scripts-in-the-cli-window"></a>Szkriptek futtatása a parancssori felület ablakában

1. Az Azure Machine Learning parancssori felületének (CLI) elindítása:
   1. Indítsa el az Azure Machine Learning Workbenchet.

   1. A Workbench menüben válassza a **Fájl** > **Parancssor megnyitása** elemet. 
   
   A parancssori felület Windows rendszeren a `C:\Temp\myIris\>` projektmappában indul el. Ez az oktatóanyag 1. részében létrehozott projekt.

   >[!IMPORTANT]
   >A következő lépéseket ezzel a parancssori felülettel kell elvégeznie.

1. A parancssori felület ablakában jelentkezzen be az Azure-ba. [További információk az „az login” parancsról](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   Előfordulhat, hogy már be van jelentkezve. Ebben az esetben kihagyhatja ezt a lépést.

   1. A parancssorba írja be a következőt:
      ```azurecli
      az login
      ```

      Ez a parancs visszaad egy kódot, amelyet a https://aka.ms/devicelogin címen használhat a böngészőben.

   1. A böngészőben lépjen a https://aka.ms/devicelogin oldalra.

   1. Amikor a rendszer kéri, írja be a parancssori felületen kapott kódot a böngészőbe.

   A Workbench alkalmazás és a parancssori felület az Azure-erőforrások hitelesítésekor független hitelesítőadat-gyorsítótárakat használ. Miután bejelentkezett, nem kell ismét hitelesítenie magát, amíg a gyorsítótárazott lexikális elem le nem jár. 

1. Ha a cég vagy intézmény több Azure-előfizetéssel rendelkezik (vállalati környezetben), meg kell adnia a használni kívánt előfizetést. Keresse meg az előfizetést, állítsa be az előfizetés azonosítójával, majd tesztelje.

   1. Ezzel a paranccsal listázhatja az összes olyan Azure-előfizetést, amelyhez hozzáféréssel rendelkezik:
   
      ```azurecli
      az account list -o table
      ```

      Az **az account list** parancs visszaadja a bejelentkezéséhez elérhető előfizetések listáját. 
      Ha több is van, azonosítsa a használni kívánt előfizetés előfizetés-azonosítóját.

   1. Állítsa be az alapértelmezett fiókként használni kívánt Azure-előfizetést:
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      ahol a \<your-subscription-id\> a használni kívánt előfizetés azonosítója. A zárójeleket hagyja el.

   1. Az aktuális előfizetés részleteinek lekérésével ellenőrizze az új előfizetés beállítását. 

      ```azurecli
      az account show
      ```    

1. A parancssori felület ablakában telepítse a **matplotlib** nevű Python megjelenítési kódtárat, ha még nincs telepítve.

   ```azurecli
   pip install matplotlib
   ```

1. A parancssori felület ablakában próbaképpen küldje el az **iris_sklearn.py** szkriptet.

   A rendszer az iris_sklearn.py szkriptet a helyi számítási környezetben futtatja.

   + Windows rendszeren:
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + MacOS rendszeren:
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   A kimenet az alábbihoz hasonló lesz:
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
              intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
              penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
              verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679
        
    ==========================================
    Serialize and deserialize using the outputs folder.
    
    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
     [ 1 37 12]
     [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. Tekintse át a kimenetet. A kimenet és az eredmény ugyanaz, mint amikor a Workbench segítségével futtatta a szkriptet. 

1. A parancssori felület ablakában futtassa ismét az **iris_sklearn.py** Python-szkriptet egy Docker végrehajtási környezetben (ha telepítve van a Docker a gépén).

   + Ha a tárolója Windows rendszeren van: 
     |Futtatási<br/>environment|Parancs Windowson|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + Ha a tárolója MacOS rendszeren van: 
     |Futtatási<br/>environment|Parancs Windowson|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. Térjen vissza a Workbenchbe, ahol:
   1. Válassza ki a bal oldali ablaktáblán a mappa ikont a projektfájlok felsorolásához.
   
   1. Nyissa meg a **run.py** nevű Python-szkriptet. Ez a szkript akkor hasznos, ha különböző regularizációs arányokat kell ismételni. 

   ![Vissza a Futtatási listához](media/tutorial-classifying-iris/2-runpy.png)

1. Futtassa többször is a kísérletet ezekkel az arányokkal. 

   A szkript elindít egy `iris_sklearn.py`-feladatot `10.0` regularizációs arány értékkel (ez egy túlzóan nagy szám). A szkript ezután a következő futtatáskor felezi az arányt – és így tovább, egészen addig, amíg az arány el nem éri a `0.005` értéket. 

   A szkript a következő kódot tartalmazza:

   ![Vissza a Futtatási listához](media/tutorial-classifying-iris/2-runpy-code.png)

1. Futtassa a **run.py** szkriptet a parancssorból a következőképpen:

   ```cmd
   python run.py
   ```

   Ez a parancs többször, különböző szabályozási arányokkal küldi el az iris_sklearn.py szkriptet.

   Amikor a `run.py` futtatása befejeződik, a Workbench futtatási előzményeket tartalmazó listanézetében megjelennek különböző mérőszámokat mutató diagramok.

## <a name="run-scripts-in-a-remote-docker-container"></a>Szkriptek futtatása távoli Docker-tárolóban
Ha egy távoli Linux gépen szeretné végrehajtani Docker-tárolóban a szkriptet, SSH-hozzáférésre (felhasználónév és jelszó) lesz szüksége azon a távoli gépen. Az is szükséges továbbá, hogy a gépen a Docker-motor telepítve legyen és fusson. Egy ilyen Linux-gép beszerzésének a legegyszerűbb módja, ha létrehoz egy Ubuntu-alapú adatelemzési virtuális gépet (DSVM) az Azure-ban. Ismerje meg, [hogyan hozhat létre Ubuntu-alapú DSVM-et az Azure ML Workbenchben való használathoz](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>A CentOS-alapú DSVM használata *nem* támogatott.

1. Ha a virtuális gép létrejött, csatolhatja azt végrehajtási környezetként, ha létrehoz egy `.runconfig` és egy `.compute` fájlt. A fájlok létrehozásához használja a következő parancsot. 

 Az új számítási célnak adja a `myvm` nevet.
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >Az IP-cím lehet nyilvánosan címezhető teljes tartománynév (FQDN) is, például: `vm-name.southcentralus.cloudapp.azure.com`. Érdemes hozzáadni az FQDN-t a DSVM-hez, és azt használni IP-cím helyett. Ezt azért érdemes megtenni, mert lehet, hogy később költséghatékonyság miatt kikapcsolja a virtuális gépet. Amikor legközelebb elindítja a virtuális gépet, előfordulhat, hogy más lesz az IP-címe.

   >[!NOTE]
   >Felhasználónév- és jelszóalapú hitelesítésen felül megadhat egy titkos kulcsot és a megfelelő jelszót (ha van ilyen) a `--private-key-file` és (opcionálisan) a `--private-key-passphrase` beállítások segítségével. Amennyiben a DSVM létrehozásakor használt privát kulcsot szeretné használni, a `--use-azureml-ssh-key` beállítást kell megadnia.

   Ezután ezen parancs futtatásával készítse elő a **myvm** számítási célt.
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   Az előző parancs létrehozza a Docker-rendszerképet a virtuális gépen, hogy az készen álljon a szkriptek futtatására.
   
   >[!NOTE]
   >A `myvm.runconfig` fájlban módosíthatja a `PrepareEnvironment` értéket is az alapértelmezett `false` helyett `true` értékre. Ez a módosítás automatikusan előkészíti a Docker-tárolót az első futás részeként.

2. Szerkessze a létrejött `myvm.runconfig` fájlt az `aml_config` alatt, és módosítsa a keretrendszert az alapértelmezett `PySpark` helyett a `Python` keretrendszerre:

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >Noha a PySparknak is működnie kell, a Python használata hatékonyabb, ha valójában nincs Spark-munkamenetre szüksége a Python-szkript futtatásához.

3. Adja ki ugyanazt a parancsot, mint korábban a parancssori felület ablakában, de a cél most legyen a _myvm_, hogy az iris_sklearn.py szkriptet egy távoli Docker-tárolóban hajtsa végre:
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   A parancsot a rendszer úgy hajtja végre, mintha `docker-python`-környezetben lenne, csak a végrehajtás egy távoli, Linux rendszerű virtuális gépen történik. A parancssori felület ablak ugyanazt a kimeneti információt jeleníti meg.

4. Próbáljuk meg a Sparkot használni a tárolóban. Nyissa meg a Fájlkezelőt. Készítsen másolatot a `myvm.runconfig` fájlról, és adja neki a `myvm-spark.runconfig` nevet. A `Framework` beállítást az új fájlt szerkesztve módosítsa `Python`ról `PySpark`ra:
   ```yaml
   Framework: PySpark
   ```
   A `myvm.compute` fájlt ne módosítsa. A Spark végrehajtásához ugyanaz a Docker-rendszerkép szükséges ugyanarról a virtuális gépről. Az új `myvm-spark.runconfig` fájl `Target` mezője ugyanarra a `myvm.compute` fájlra mutat a `myvm` névnek köszönhetően.

5. A következő parancsot beírva futtassa az **iris_spark.py** szkriptet a távoli Docker-tárolóban futó Spark-példányában:
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>Szkriptek futtatása HDInsight-fürtökben
Ezt a szkriptet futtathatja egy HDInsight Spark-fürtön is. Ismerje meg, [hogyan hozhat létre HDInsight Spark-fürtöt az Azure ML Workbenchben való használathoz](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>A HDInsight-fürtnek az Azure-blobot kell elsődleges tárolóként használnia. Az Azure Data Lake-tároló használata jelenleg nem támogatott.

1. Ha rendelkezik hozzáféréssel a Spark for Azure HDInsight-fürthöz, hozzon létre egy HDInsight-futtatási konfigurációs parancsot az itt látható módon. Paraméterként adja meg a HDInsight-fürt nevét, valamint a saját HDInsight-felhasználónevét és -jelszavát. 

   A következő paranccsal hozzon létre egy HDInsight-fürtre mutató számítási célt:

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   A HDInsight-fürt előkészítéséhez futtassa ezt a parancsot:

   ```
   az ml experiment prepare -c myhdi
   ```

   A fürt fő csomópontjának teljes tartományneve (FQDN) általában `<your_cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >A `username` a fürt SSH-felhasználóneve, amely meghatározására a HDInsight telepítése során kerül sor. Alapértelmezés szerint az érték az `sshuser`. Az érték nem `admin`, amely a másik, a telepítés során a fürt felügyeleti webhelyéhez való hozzáférés engedélyezésére létrehozott felhasználó. 

2. Futtassa az **iris_spark.py** szkriptet a HDInsight-fürtön ezzel a paranccsal:

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Ha egy távoli HDInsight-fürtön hajtja végre, a `https://<your_cluster_name>.azurehdinsight.net/yarnui` helyen az `admin` felhasználói fiókkal a Yet Another Resource Negotiator (YARN) feladat-végrehajtási részleteit is megtekintheti.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések
A háromrészes oktatóanyag-sorozat második részében a következőkkel ismerkedhetett meg:
> [!div class="checklist"]
> * Szkriptek megnyitása és a kód áttekintése a Workbenchben
> * Szkriptek végrehajtása helyi környezetben
> * A futtatási előzmények megtekintése
> * Szkriptek végrehajtása helyi Docker-környezetben

Most kipróbálhatja az oktatóanyag-sorozat harmadik részét, amelyben üzembe helyezheti a valós idejű webszolgáltatásként létrehozott logisztikai regressziós modellt.

> [!div class="nextstepaction"]
> [3. oktatóanyag – Modellek üzembe helyezése](tutorial-classifying-iris-part-3.md)
