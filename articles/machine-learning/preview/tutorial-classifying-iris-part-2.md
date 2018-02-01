---
title: "Modell létrehozása Azure Machine Learning-szolgáltatásokoz (előzetes verzió) | Microsoft Docs"
description: "Ez a részletes oktatóanyag bemutatja, hogyan használhatók ki teljeskörűen az (előzetes verziójú) Azure Machine Learning-szolgáltatások. Ez a 2. rész, amely a kísérletezést ismerteti."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 11/06/2017
ms.openlocfilehash: 3337ba9fa6845deb98813259821f77ff713f9eb1
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="classify-iris-part-2-build-a-model"></a>Írisz osztályozása, 2. rész: Modell létrehozása
Az Azure Machine Learning-szolgáltatások (előzetes verzió) az adatszakértők számára az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőszinten való üzembe helyezéséhez létrehozott átfogó, integrált és fejlett adatelemzési megoldást kínálnak.

Ez az oktatóanyag egy háromrészes sorozat második része. Az oktatóanyagnak ebben a részében az Azure Machine Learning-szolgáltatások (előzetes verzió) segítségével a következőket hajtja végre:

> [!div class="checklist"]
> * Az Azure Machine Learning Workbench használata.
> * Szkriptek megnyitása és kód áttekintése.
> * Szkriptek végrehajtása helyi környezetben.
> * A futtatási előzmények megtekintése.
> * Szkriptek végrehajtása helyi Docker-környezetben.
> * Szkriptek végrehajtása helyi Azure CLI-ablakban.
> * Szkriptek végrehajtása távoli Docker-környezetben.
> * Szkriptek végrehajtása a felhőalapú Azure HDInsight-környezetben.

Az oktatóanyag a jól ismert [Iris flower adatkészletet](https://en.wikipedia.org/wiki/Iris_flower_data_set) használja. A képernyőképek Windows-specifikusak, de a macOS rendszeren szinte azonos a felhasználói élmény.

## <a name="prerequisites"></a>Előfeltételek
Végezze el az oktatóanyag első részét. Mielőtt belekezdene az oktatóanyag végrehajtásába, kövesse az [Adatok előkészítése útmutató](tutorial-classifying-iris-part-1.md) lépéseit Azure Machine Learning-erőforrások létrehozásához és az Azure Machine Learning Workbench alkalmazás telepítéséhez.

Opcionálisan kísérletezhet a szkriptek helyi Docker-tárolón történő futtatásával is. Ehhez telepíteni kell egy Docker-motort (a Community Edition is megfelelő), és azt helyileg el kell indítania Windows vagy macOS rendszerű gépén. A Docker telepítésével kapcsolatban további információkért tekintse át [a Docker telepítési utasításait](https://docs.docker.com/engine/installation/).

Ha kísérletezni kíván a szkriptek távoli Azure-beli virtuális gépen található Docker-tárolókon vagy Azure HDInsight Spark-fürtön történő futtatásával, kövesse az [Ubuntu-alapú Azure-os adatelemzési virtuális gép vagy HDInsight-fürt létrehozására vonatkozó utasításokat](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Az iris_sklearn.py és a konfigurációs fájlok áttekintése
1. Indítsa el az Azure Machine Learning Workbench alkalmazást, és nyissa meg az oktatóanyag előző részében létrehozott **myIris** projektet.

2. A projekt megnyitása után a bal szélső ablaktáblán kattintson a **Fájlok** gombra (mappa ikon) a projektmappa fájllistájának megnyitásához.

3. Válassza ki az **iris_sklearn.py** fájlt. A Workbench alkalmazásban egy új szövegszerkesztő lapon megnyílik a Python-kód.

   ![Fájl megnyitása](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >Lehet, hogy a megjelenő kód nem egyezik a fenti kóddal, mivel a mintaprojekt rendszeresen frissül.

4. Tekintse át a Python-szkriptkódot, hogy megismerkedjen a kódolási stílussal. A szkript a következő feladatokat hajtja végre:

   - Betölti az **iris.dprep** adatelőkészítési csomagot, és létrehoz egy [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)-et. 

        >[!NOTE]
        >Használja a mintaprojekthez mellékelt `iris.dprep` adatelőkészítési csomagot. Ez ugyanaz, mint az oktatóanyag 1. részében létrehozott `iris-1.dprep` fájl.

   - Véletlenszerű funkciókat ad hozzá, hogy nehezítse a probléma megoldását. A véletlenszerűségre azért van szükség, mert az Iris egy kisméretű adatkészlet, amely könnyedén osztályozható közel 100%-os pontossággal.

   - Létrehoz egy logisztikai regressziós modellt a [scikit-learn](http://scikit-learn.org/stable/index.html) Machine Learning-kódtár használatával. 

   - A [pickle](https://docs.python.org/2/library/pickle.html) kódtár beszúrásával szerializálja a modellt egy fájlba az `outputs` mappában. Ezt követően a szkript betölti azt, és deszerializálja a memóriába.

   - A deszerializált modell segítségével előrejelzést végez egy új rekordon. 

   - A [matplotlib](https://matplotlib.org/) kódtár segítségével megjelenít két gráfot (egy keveredési mátrixot és egy többcsoportos ROC-görbét), majd menti őket az `outputs` mappába.

   - A folyamat során a `run_logger` objektumot használja a regularizációs arány rögzítésére és a pontosság modellezésére a naplókban. A naplók automatikusan megjelennek a futtatási előzményekben.


## <a name="execute-irissklearnpy-script-in-a-local-environment"></a>Az iris_sklearn.py szkript végrehajtása helyi környezetben

Készüljünk fel az **iris_sklearn.py** szkript első futtatására. A szkriptnek szüksége van a **scikit-learn** és a **matplotlib** csomagra. Az Azure Machine Learning Workbench már telepítette a **scikit-learn** csomagot. A **matplotlib** csomagot azonban még telepíteni kell. 

1. A parancssor megnyitásához kattintson a **Fájl** menüre, majd a **Parancssor megnyitása** elemre az Azure Machine Learning Workbench alkalmazásban. Ezt a parancssori felületi ablakot az *Azure Machine Learning Workbench parancssori felület ablakának*, vagy röviden *CLI-ablaknak* nevezzük.

2. A **matplotlib** Python-csomag telepítéséhez írja be a következő parancsot a CLI-ablakba. Ennek végrehajtása kevesebb mint egy percet vesz igénybe.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Ha nem hajtja végre az előző `pip install` parancsot, az `iris_sklearn.py` kódja sikeresen végig fog futni. Amennyiben csak az `iris_sklearn.py` parancsot futtatja, a kód nem ugyanúgy hozza létre a keveredési mátrix kimenetét és a többcsoportos ROC-görbét, ahogyan az az előzménymegjelenítésekben látható.

3. Térjen vissza a Workbench alkalmazás ablakába. 

4. Az **iris_sklearn.py** lap tetején található eszköztárban kattintson a **Mentés** ikon melletti legördülő menü megnyitásához, majd válassza a **Konfiguráció futtatása** elemet. Végrehajtási környezetként válassza ki a **helyi** környezetet, futtatandó szkriptként pedig az `iris_sklearn.py` fájlt.

5. Ezután az eszköztár jobb oldalán adja meg a `0.01` értéket az **Argumentumok** mezőben. 

   ![Futtatás vezérlése](media/tutorial-classifying-iris/run_control.png)

6. Kattintson a **Futtatás** gombra. Azonnal be lesz ütemezve egy feladat. A feladat megjelenik a Workbench-ablak jobb oldalán található **Feladatok** panelen. 

7. A feladat állapota pár pillanat múlva **Elküldés** értékről **Fut** értékre, majd **Befejezve** értékre vált.

   ![Az sklearn futtatása](media/tutorial-classifying-iris/run_sklearn.png)

8. Válassza ki a **Befejezve** elemet a **Feladatok** panel állapotszöveg részén. Megnyílik egy előugró ablak az aktuálisan futó szkript standard kimeneti (stdout) szövegével. Az stdout szöveg bezárásához kattintson az előugró ablak jobb felső sarkában található **Bezárás** (**x**) gombra.

9. A **Feladatok** panel ugyanazon feladatállapotában jelölje ki a **Befejezve** állapot fölött látható kék színű **iris_sklearn.py [n]** (futtatás száma: _n_) feliratot és a kezdési időpontot. Az ekkor megnyíló **Futtatás tulajdonságai** ablakban az adott futtatás következő adatai láthatók:
   - A **futtatás tulajdonságaihoz** kapcsolódó információk
   - **Kimeneti** fájlok
   - **Megjelenítések** (ha van ilyen)
   - **Naplók** 

   Amikor a futtatás befejeződik, az előugró ablakban az alábbi eredmények jelennek meg:

   >[!NOTE]
   >Mivel korábban bevezettünk némi véletlenszerűsítést a gyakorlókészletbe, a pontos eredmények kissé eltérhetnek az itt bemutatottaktól.

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

11. Végezzen még néhány futtatást. 

    Adjon meg különböző, `0.001` és `10` közötti numerikus értékeket az **Argumentumok** mezőben. A **Futtatás** elemet választva futtassa a kódot még néhányszor. Az argumentumérték, amelyet minden alkalommal megváltoztat, bekerül a kód logisztikai regressziós algoritmusába, így minden alkalommal eltérő eredményt fog kapni.

## <a name="review-the-run-history-in-detail"></a>A futtatási előzmények részletes áttekintése
Az Azure Machine Learning Workbench alkalmazás minden szkriptvégrehajtást rögzít futtatási előzményrekordként. A **Futtatások** nézetet megnyitva megtekintheti egy adott szkript futtatási előzményeit.

1. A **Futtatások** listájának megnyitásához kattintson a **Futtatások** gombra (óra ikon) a bal oldali eszköztáron. Ezután az **iris_sklearn.py** elemet választva megjelenítheti az `iris_sklearn.py` **Futtatási irányítópultját**.

   ![Futtatásnézet](media/tutorial-classifying-iris/run_view.png)

2. Megnyílik a **Futtatási irányítópult** lap. Áttekintheti a több futtatás során rögzített statisztikákat. A diagramok a lap tetején jelennek meg. Mindegyik futtatás egy sorban növekvő sorszámmal rendelkezik, és a futtatások részletei a képernyő alján lévő táblázatban vannak felsorolva.

   ![Futtatási irányítópult](media/tutorial-classifying-iris/run_dashboard.png)

3. Szűrheti a táblázatot, majd az egyes diagramokra kattintva megtekintheti a futtatások állapotát, időtartamát, pontosságát és regularizációs arányát. 

4. A **Futtatások** táblázatból két vagy három futtatást kiválasztva, majd az **Összehasonlítás** gombra kattintva megnyithatja a részletes összehasonlító ablaktáblát. Tekintse át az összehasonlító táblázatot. Az **Összehasonlítás** ablaktábla bal felső részén található **Futtatási lista** gombra kattintva visszatérhet a **Futtatási irányítópultjához**.

5. Kijelölhet egy adott futtatást a futtatás részletes nézetének megtekintéséhez. Figyelje meg, hogy a **Futtatás tulajdonságai** szakaszban megjelennek a kiválasztott futtatás statisztikái. A kimeneti mappába írt fájlok a **Kimenetek** szakaszban vannak felsorolva, és innen tudja azokat letölteni.

   ![Futtatás részletei](media/tutorial-classifying-iris/run_details.png)

   A két grafikon, a keveredési mátrix és a többcsoportos ROC-görbe a **Képi megjelenítések** szakaszban látható. A naplófájlokat is megtalálhatja a **Naplók** szakaszban.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Szkriptek végrehajtása helyi Docker-környezetben

A Machine Learning segítségével könnyedén konfigurálhat további végrehajtási környezeteket, például a Dockert, és futtathatja azokban a szkriptjét. 

>[!IMPORTANT]
>Ehhez a lépéshez szüksége lesz egy helyileg telepített és elindított Docker-motorra. További információkért tekintse át a Docker telepítési utasításait.

1. A bal oldali ablaktáblán válassza ki a **Mappa** ikont a projekt **Fájlok** listájának megnyitásához. Bontsa ki az `aml_config` mappát. 

2. Több előre konfigurált környezet is létezik, például a **docker-python**, a **docker-spark** és a **helyi**. 

   Minden környezethez két fájl tartozik, például `docker-python.compute` és `docker-python.runconfig`. Az egyes fájlokat megnyitva láthatja, hogy néhány lehetőséget konfigurálni lehet a szövegszerkesztőben.  

   A megnyitott szövegszerkesztő-lapokat a **Bezárás** (**x**) gombra kattintva zárhatja be.

3. Futtassa az **iris_sklearn.py** szkriptet a **docker-python** környezetben: 

   - A bal oldali eszköztáron kattintson az **Óra** ikonra a **Futtatások** ablaktábla megnyitásához. Válassza a **Minden futtatás** elemet. 
   - A **Minden futtatás** lap tetején az alapértelmezett **helyi** cél környezet helyett válassza a **docker-python** elemet. 
   - Ezután a jobb oldalon válassza ki az **iris_sklearn.py** fájlt futtatandó szkriptként. 
   - Az **Argumentumok** mezőt hagyja üresen, mivel a szkript egy alapértelmezett értéket fog megadni. 
   - Kattintson a **Futtatás** gombra.

4. Figyelje meg, hogy a rendszer elindít egy új feladatot. Ez a Workbench-ablak jobb oldalán található **Feladatok** ablaktáblában jelenik meg.

   Ha az első alkalommal végez futtatást Docker-környezetben, a futtatás néhány perccel tovább tart. 

   Az Azure Machine Learning Workbench a háttérben létrehoz egy új docker-fájlt. 
   Az új fájl a `docker.compute` fájlban megadott alap Docker-rendszerképre és a `conda_dependencies.yml` fájlban megadott függőségi Python-csomagokra hivatkozik. 
   
   A Docker-motor a következő feladatokat hajtja végre:

    - Letölti az alaprendszerképet az Azure-ból.
    - Telepíti a `conda_dependencies.yml` fájlban megadott Python-csomagokat.
    - Elindít egy Docker-tárolót.
    - A futtatási konfigurációtól függően lemásolja a projektmappa helyi példányát vagy hivatkozik arra.      
    - Végrehajtja az `iris_sklearn.py` szkriptet.

   Végül ugyanazokat az eredményeket kell kapnia, mint amikor a **helyi** környezetet célozza meg.

5. Most pedig próbáljuk ki a Sparkot. Az alap Docker-rendszerkép tartalmaz egy előre telepített és beállított Spark-példányt. Ez a példány lehetővé teszi, hogy végrehajtson benne egy PySpark-szkriptet. Ez egy egyszerű módja annak, hogy saját Spark-programot fejlesszen és teszteljen anélkül, hogy időt kelljen szánnia a Spark telepítésére és konfigurálására. 

   Nyissa meg az `iris_spark.py` fájlt. Ez a szkript betölti az `iris.csv` adatfájlt, és a Spark Machine Learning-kódtár logisztikai regressziós algoritmusával besorolja az Írisz adatkészletet. Módosítsa a futtatási környezetet **docker-spark** környezetre, a szkriptet pedig az **iris_spark.py** fájlra, majd futtassa újra. Ez a folyamat egy kicsit több időt fog igénybe venni, mivel létre kell hozni egy Spark-munkamenetet, és el kell indítani azt a Docker-tárolóban. Azt is észreveheti, hogy az stdout eltér az `iris_spark.py` stdout kimenetétől.

6. Futtassa még néhányszor, és adjon meg különböző argumentumokat. 

7. Nyissa meg az `iris_spark.py` fájlt a Spark Machine Learning-kódtár segítségével létrehozott logisztikai regressziós modell megtekintéséhez. 

8. Használja a **Feladatok** ablaktáblát, futtasson egy előzmények listanézetet és egy részletek nézetet a különböző végrehajtási környezetekben végzett futtatásoknál.

## <a name="execute-scripts-in-the-azure-machine-learning-cli-window"></a>Szkriptek végrehajtása az Azure Machine Learning CLI-ablakában

1. Az Azure Machine Learning Workbench alkalmazásban nyissa meg a parancssori ablakot. Ehhez kattintson a **Fájl** menüre, majd a **Parancssor megnyitása** elemre. A parancsprompt a projektmappában nyílik meg, a `C:\Temp\myIris\>` prompttal.

   >[!IMPORTANT]
   >Az alábbi lépéseket a (Workbench alkalmazásból megnyitott) parancssori ablakban kell elvégeznie.

2. Jelentkezzen be az Azure-ba a parancssor használatával. 

   A Workbench alkalmazás és a CLI az Azure-erőforrások hitelesítésekor független hitelesítőadat-gyorsítótárakat használ. Ezt csak egyszer kell megtennie, amíg a gyorsítótárazott jogkivonat le nem jár. Az **az account list** parancs visszaadja a bejelentkezéséhez elérhető előfizetések listáját. Ha egynél több található, használja a kívánt előfizetés azonosítóértékét. Állítsa be az adott előfizetést alapértelmezett fiókként az **az account set -s** paranccsal, majd adja meg az előfizetés azonosítóértékét. Ezután erősítse meg a beállítást az **account show** paranccsal.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Amikor a hitelesítés befejeződik, és beállította az aktuális Azure-előfizetési környezetet, írja be az alábbi parancsokat a parancssori felület ablakába a **matplotlib** telepítéséhez, majd küldje el a Python-szkriptet futtatandó kísérletként.

   ```azurecli
   REM you don't need to run this command if you have installed matplotlib locally from the previous steps
   pip install matplotlib
   
   REM kicks off an execution of the iris_sklearn.py file against the local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Tekintse át a kimenetet. A kimenet és az eredmény ugyanaz, mint amikor a Workbench segítségével futtatta a szkriptet. 

5. Ha telepítve van a számítógépen a Docker, futtassa ugyanezt a szkriptet még egyszer a Docker végrehajtási környezetben.

   ```azurecli
   REM executes iris_sklearn.py in the local Docker container Python environment
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM executes iris_spark.py in the local Docker container Spark environment
   az ml experiment submit -c docker-spark .\iris_spark.py 0.1
   ```
6. Listázza a projektfájlokat a Workbench alkalmazásban a bal oldali ablaktáblán a **Mappa** ikonra kattintva, majd nyissa meg a **run.py** nevű Python-szkriptet. 

   Ez a szkript akkor hasznos, ha különböző regularizációs arányokat kell ismételni. Futtassa többször is a kísérletet ezekkel az arányokkal. A szkript elindít egy `iris_sklearn.py`-feladatot `10.0` regularizációs arány értékkel (ez egy túlzóan nagy szám). A szkript ezután a következő futtatáskor felezi az arányt – és így tovább, egészen addig, amíg az arány el nem éri a `0.005` értéket. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   A **run.py** szkript parancssorból való megnyitásához futtassa a következő parancsokat:

   ```cmd
   REM submits iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Amikor a `run.py` futtatása befejeződik, a Workbench futtatási előzményeket tartalmazó listanézetében megjelenik egy diagram.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Végrehajtás Docker-tárolóban egy távoli gépen
Ha egy távoli Linux gépen szeretné végrehajtani Docker-tárolóban a szkriptet, SSH-hozzáférésre (felhasználónév és jelszó) lesz szüksége azon a távoli gépen. Az is szükséges továbbá, hogy a távoli gépen a Docker-motor telepítve legyen és fusson. Egy ilyen Linux-gép beszerzésének a legegyszerűbb módja, ha létrehoz egy Ubuntu-alapú adatelemzési virtuális gépet (DSVM) az Azure-ban. Ismerje meg, [hogyan hozhat létre Ubuntu-alapú DSVM-et az Azure ML Workbenchben való használathoz](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>A CentOS-alapú DSVM használata *nem* támogatott.

1. Ha a virtuális gép létrejött, csatolhatja azt végrehajtási környezetként, ha létrehoz egy `.runconfig` és egy `.compute` fájlt. A fájlok létrehozásához használja a következő parancsot. Az új környezetnek adja a `myvm` nevet.
 
   ```azurecli
   REM creates an myvm compute target
   az ml computetarget attach remotedocker --name myvm --address <IP address> --username <username> --password <password>
   ```
   
   >[!NOTE]
   >Az IP-cím lehet nyilvánosan címezhető teljes tartománynév (FQDN) is, például: `vm-name.southcentralus.cloudapp.azure.com`. Érdemes hozzáadni az FQDN-t a DSVM-hez, és azt használni IP-cím helyett. Ezt azért érdemes megtenni, mert lehet, hogy később költséghatékonyság miatt kikapcsolja a virtuális gépet. Amikor legközelebb elindítja a virtuális gépet, előfordulhat, hogy más lesz az IP-címe.

   Ezután futtassa az alábbi parancsot, amellyel létrehozza a Docker-rendszerképet a virtuális gépen, hogy az készen álljon a szkriptek futtatására:
   
   ```azurecli
   REM prepares the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >A(z) `myvm.runconfig` fájlban módosíthatja a(z) `PrepareEnvironment` értéket az alapértelmezett `false` helyett `true` értékre. Ez a módosítás automatikusan előkészíti a Docker-tárolót az első futáskor.

2. Szerkessze a létrejött `myvm.runconfig` fájlt az `aml_config` alatt, és módosítsa a keretrendszert az alapértelmezett `PySpark` helyett a `Python` keretrendszerre:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Ha a keretrendszer-beállításnál meghagyja a PySpark keretrendszert, annak is működnie kell. Viszont kevésbé hatékony, kivéve, ha ténylegesen Spark-munkamenetre van szüksége a Python-szkript futtatásához.

3. Adja ki ugyanazt a parancsot, mint korábban a parancssori felület ablakában, de most a cél legyen a _myvm_:
   ```azurecli
   REM executes iris_sklearn.py in a remote Docker container
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   A parancsot a rendszer úgy hajtja végre, mintha `docker-python`-környezetben lenne, csak a végrehajtás egy távoli, Linux rendszerű virtuális gépen történik. A parancssori felület ablak ugyanazt a kimeneti információt jeleníti meg.

4. Próbáljuk meg a Sparkot használni a tárolóban. Nyissa meg a Fájlkezelőt. Az alábbiakat elvégezheti a parancssori felület ablakából is, ha nem okoz gondot az alapvető fájlkezelési parancsok használata. Készítsen másolatot a `myvm.runconfig` fájlról, és adja neki a `myvm-spark.runconfig` nevet. A `Framework` beállítást az új fájlt szerkesztve módosítsa `Python`ról `PySpark`ra:
   ```yaml
   "Framework": "PySpark"
   ```
   A `myvm.compute` fájlt ne módosítsa. A Spark végrehajtásához ugyanaz a Docker-rendszerkép szükséges ugyanarról a virtuális gépről. Az új `myvm-spark.runconfig` fájl `target` mezője ugyanarra a `myvm.compute` fájlra mutat a `myvm` névnek köszönhetően.

5. A következő parancsot beírva futtassa azt a távoli Docker-tároló Spark-példányában:
   ```azureli
   REM executes iris_spark.py in a Spark instance on a remote Docker container
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Szkript végrehajtása HDInsight-fürtön
Ezt a szkriptet futtathatja egy HDInsight Spark-fürtön is. Ismerje meg, [hogyan hozhat létre HDInsight Spark-fürtöt az Azure ML Workbenchben való használathoz](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>A HDInsight-fürtnek az Azure-blobot kell elsődleges tárolóként használnia. Az Azure Data Lake-tároló használata jelenleg nem támogatott.

1. Ha rendelkezik hozzáféréssel a Spark for Azure HDInsight-fürthöz, hozzon létre egy HDInsight-futtatási konfigurációs parancsot az itt látható módon. Paraméterként adja meg a HDInsight-fürt nevét, valamint a saját HDInsight-felhasználónevét és -jelszavát. Használja az alábbi parancsot:

   ```azurecli
   REM creates a compute target that points to a HDInsight cluster
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <username> --password <password>

   REM prepares the HDInsight cluster
   az ml experiment prepare -c myhdi
   ```

   A fürt fő csomópontjának teljes tartományneve (FQDN) általában `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >A `username` a fürt SSH felhasználóneve. Az alapértelmezett érték az `sshuser`, ha nem módosítja a HDInsight telepítése közben. Az érték nem `admin`, amely a másik, a telepítés során a fürt felügyeleti webhelyéhez való hozzáférés engedélyezésére létrehozott felhasználó. 

2. Futtassa az alábbi parancsot, és a szkript fut a HDInsight-fürtön:

   ```azurecli
   REM executes iris_spark on the HDInsight cluster
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Ha egy távoli HDInsight-fürtön hajtja végre, a `https://<cluster_name>.azurehdinsight.net/yarnui` helyen az `admin` felhasználói fiókkal a Yet Another Resource Negotiator (YARN) feladat-végrehajtási részleteit is megtekintheti.


## <a name="next-steps"></a>További lépések
A háromrészes oktatóanyag-sorozat jelen, második részében megtanulhatta, hogyan használhatók az Azure Machine Learning szolgáltatások a következőkre:
> [!div class="checklist"]
> * Az Azure Machine Learning Workbench használata.
> * Szkriptek megnyitása és kód áttekintése.
> * Szkriptek végrehajtása helyi környezetben.
> * A futtatási előzmények megtekintése.
> * Szkriptek végrehajtása helyi Docker-környezetben.
> * Szkriptek végrehajtása helyi Azure CLI-ablakban.
> * Szkriptek végrehajtása távoli Docker-környezetben.
> * Szkriptek végrehajtása felhőalapú HDInsight-környezetben.

Tovább léphet a sorozat harmadik részére. Most, hogy létrehoztuk a logisztikai regressziós modellt, helyezzük üzembe valós idejű webszolgáltatásként.

> [!div class="nextstepaction"]
> [Modell üzembe helyezése](tutorial-classifying-iris-part-3.md)
