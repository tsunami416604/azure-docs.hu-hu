---
title: "Modell létrehozása Azure Machine Learning-szolgáltatásokoz (előzetes verzió) | Microsoft Docs"
description: "Ez a részletes oktatóanyag bemutatja, hogyan használhatók ki teljes körűen az (előzetes verziójú) Azure Machine Learning-szolgáltatások. Ez a 2. rész, amely a kísérletezést ismerteti."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f1f270b95a60e847ce7d29c2d7d60c57891924cc
ms.contentlocale: hu-hu
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-2-build-a-model"></a>Írisz osztályozása, 2. rész: Modell létrehozása
Az Azure Machine Learning-szolgáltatások (előzetes verzió) az adatszakértők számára az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőszinten való üzembe helyezéséhez létrehozott átfogó, integrált és fejlett adatelemzési megoldás.

Ez az oktatóanyag egy háromrészes sorozat második része. Az oktatóanyagnak ebben a részében az Azure Machine Learning-szolgáltatások (előzetes verzió) segítségével a következőket sajátíthatja el:

> [!div class="checklist"]
> * Az Azure Machine Learning Workbench használata
> * Szkriptek megnyitása és kód áttekintése
> * Szkriptek végrehajtása helyi környezetben
> * Futtatási előzmények áttekintése
> * Szkriptek végrehajtása helyi Docker-környezetben
> * Szkriptek végrehajtása helyi Azure CLI ablakban
> * Szkriptek végrehajtása távoli Docker-környezetben
> * Szkriptek végrehajtása a felhőben HDInsight-környezetben

Az oktatóanyag az egyszerűség kedvéért a jól ismert [Iris flower adatkészletet](https://en.wikipedia.org/wiki/Iris_flower_data_set) használja. A képernyőképek Windows-specifikusak, de a macOS rendszeren tapasztalható felhasználói élmény közel azonos.

## <a name="prerequisites"></a>Előfeltételek
Ajánlott elvégezni az oktatóanyag első részét. Mielőtt belekezdene az oktatóanyag végrehajtásába, kövesse az [Adatok előkészítése útmutató](tutorial-classifying-iris-part-1.md) lépéseit Azure Machine Learning-erőforrások létrehozásához és az Azure Machine Learning Workbench alkalmazás telepítéséhez.

## <a name="review-irissklearnpy-and-configuration-files"></a>Az iris_sklearn.py és a konfigurációs fájlok áttekintése
1. Indítsa el az **Azure Machine Learning Workbench** alkalmazást, és nyissa meg az oktatóanyag előző részében létrehozott **myIris** projektet.

2. A projekt megnyitása után az Azure Machine Learning Workbench bal oldali eszköztárán kattintson a **Fájlok** gombra (mappa ikon) a projektmappa fájllistájának megnyitásához.

3. Válassza ki az **iris_sklearn.py** fájlt. A Workbench alkalmazásban egy új szövegszerkesztő lapon megnyílik a Python-kód.

   ![fájl megnyitása](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >Lehet, hogy a megjelenő kód nem egyezik a fenti kóddal, mivel a mintaprojekt rendszeresen frissül.

4. Tekintse át a Python-szkriptkódot, hogy megismerkedjen a kódolási stílussal. A szkript a következő feladatokat hajtja végre:

   - Betölti az **iris.dprep** adat-előkészítési csomagot, és létrehoz egy [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)-et. 

        >[!NOTE]
        >Mi a mintaprojekthez mellékelt `iris.dprep` adat-előkészítési csomagot használjuk. Ez ugyanaz, mint az oktatóanyag 1. részében létrehozott `iris-1.dprep` fájl.

   - Véletlenszerű funkciókat ad hozzá, hogy nehezítse a probléma megoldását. (A véletlenszerűségre azért van szükség, mert az Iris egy kisméretű adatkészlet, amelyet könnyedén lehet osztályozni közel 100%-os pontossággal.)

   - Létrehoz egy egyszerű logisztikai regressziós modellt a [scikit-learn](http://scikit-learn.org/stable/index.html) Machine Learning-könyvtár használatával. 

   - A [pickle](https://docs.python.org/2/library/pickle.html) könyvtár használatával szerializálja a modellt egy fájlba az `outputs` mappában, majd betölti azt, és deszerializálja a memóriába.

   - A deszerializált modell segítségével előrejelzést végez egy új rekordon. 

   - A [matplotlib](https://matplotlib.org/) könyvtár segítségével megjelenít két gráfot (egy keveredési mátrixot és egy többcsoportos ROC-görbét), majd menti őket az `outputs` mappába.

   - A folyamat során a `run_logger` objektumot használja a regularizációs arány és a modell pontosságának naplózására. A naplók automatikusan megjelennek a futtatási előzményekben.


## <a name="execute-irissklearnpy-script-in-local-environment"></a>Az iris_sklearn.py szkript végrehajtása helyi környezetben

Készüljünk fel az **iris_sklearn.py** szkript első futtatására. A szkriptnek szüksége van a scikit-learn és matplotlib csomagokra. Az Azure ML Workbench már telepítette a **scikit-learn** csomagot. A **matplotlib** csomagot azonban telepíteni kell. 

1. Az Azure Machine Learning Workbench alkalmazásban kattintson a **Fájl** menüre, majd a parancssor indításához válassza ki a **Parancssor megnyitása** lehetőséget. Ezt a parancssori felület ablakot az Azure Machine Learning Workbench parancssori felület ablakának, vagy röviden CLI ablaknak nevezzük.

2. A **matplotlib** Python-csomag telepítéséhez írja be a következő parancsot a parancssori felület ablakába. Ez kevesebb mint egy percet vesz igénybe.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Ha nem hajtja végre a fenti `pip install` parancsot, az `iris_sklearn.py` kódja sikeresen végig fog futni, de nem ugyanúgy hozza létre a keveredési mátrix kimenetét és a többcsoportos ROC-görbét, ahogyan az az előzménymegjelenítésekben látható.

3. Térjen vissza a Workbench alkalmazás ablakába. 

4. Az **iris_sklearn.py** lap bal felső részén, a mentés ikon mellett kattintson a legördülő listára, és válassza ki a **Konfiguráció futtatása** lehetőséget.  Végrehajtási környezetként válassza ki a **helyi** környezetet, futtatandó szkriptként pedig az `iris_sklearn.py` fájlt.

5. Ezután a lap jobb oldalán található **Argumentumok** mezőben adja meg a `0.01` értéket. 

   ![kép](media/tutorial-classifying-iris/run_control.png)

6. Kattintson a **Futtatás** gombra. Azonnal be lesz ütemezve egy feladat. A feladat megjelenik a Workbench-ablak jobb oldalán található **Feladatok** panelen. 

7. A feladat állapota pár pillanat múlva **Elküldés** értékről **Fut** értékre, végül röviddel ezután **Befejezve** értékre vált.

   ![az sklearn futtatása](media/tutorial-classifying-iris/run_sklearn.png)

8. Kattintson a **Befejezve** szóra a Feladatok panel állapotszöveg részén. Megnyílik egy előugró ablak az aktuálisan futó szkript standard kimeneti (stdout) szövegével. A stdout szöveg bezárásához kattintson az előugró ablak jobb felső sarkában található **X** gombra.

9. A Feladatok panel ugyanazon feladatállapotában kattintson a **Befejezve** állapot és a kezdési időpont fölött látható kék színű **iris_sklearn.py [1]** feliratra. Megnyílik a **Futtatás tulajdonságai** oldal, és megjeleníti a futtatás tulajdonságait, a **kimeneti** fájlokat, illetve az adott futtatás **képi megjelenítéseit** és **naplóit**. 

   Amikor a futtatás befejeződik, az előugró ablakban az alábbi eredmények jelennek meg:

   >[!NOTE]
   >Mivel korábban bevezettünk némi véletlenszerűsítést a gyakorlókészletbe, a pontos eredmények kissé el fognak térni.

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
   Confusion matrix and ROC curve plotted. See them in Run History details page.
   ```

10. Zárja be a **Futtatás tulajdonságai** lapot, és térjen vissza az **iris_sklearn.py** lapra. 

11. Végezzen még néhány futtatást. 

    Adjon meg különböző, `0.001` és `10` közötti numerikus értékeket az **Argumentumok** mezőben. A **Futtatás** gombra kattintva futtassa a kódot még néhányszor. Az argumentumérték, amelyet minden alkalommal megváltoztat, bekerül a kód logisztikai regressziós algoritmusába, így minden alkalommal különböző eredményt fog kapni.

## <a name="review-run-history-in-detail"></a>A futtatási előzmények részletes áttekintése
Az Azure Machine Learning Workbench alkalmazás minden szkriptvégrehajtást rögzít futtatási előzményrekordként. Egy adott szkript futtatási előzményeit a **Futtatások** nézetben tekintheti meg.

1. Kattintson a **Futtatások** gombra (óra ikon) a bal oldali eszköztáron a **Futtatások** listájának megnyitásához. Ezután kattintson az **iris_sklearn.py** elemre az `iris_sklearn.py` **Futtatási irányítópultjának** megjelenítéséhez.

   ![kép](media/tutorial-classifying-iris/run_view.png)

2. Megnyílik a **Futtatási irányítópult** lap. Tekintse át a több futtatás során rögzített statisztikákat. A gráfok a lap tetején jelennek meg, a számozott futtatások és a részleteik pedig a lap alján, egy táblázatban vannak felsorolva.

   ![kép](media/tutorial-classifying-iris/run_dashboard.png)

3. Szűrje a táblázatot, és interaktív módon kattintson a gráfokra az egyes futtatások állapotának, időtartamának, pontosságának és regularizációs arányának megtekintéséhez. 

4. A **Futtatások** táblázatból válasszon ki két vagy három futtatást, majd kattintson az **Összehasonlítás** gombra a részletes összehasonlító oldal megnyitásához. Tekintse át az összehasonlító táblázatot. A **Futtatási irányítópultra** való visszatéréshez kattintson az összehasonlító oldal bal felső részén található **Futtatási lista** gombra.

5. Kattintson egy futtatásra a futtatás részletei nézet megtekintéséhez. Figyelje meg, hogy a _Futtatás tulajdonságai_ szakaszban megjelennek a kiválasztott futtatás statisztikái. A kimeneti mappába írt fájlok a **Kimenet** szakaszban vannak felsorolva, és le lehet őket tölteni.

   ![kép](media/tutorial-classifying-iris/run_details.png)

   A két grafikon, a keveredési mátrix és a többcsoportos ROC-görbe a **Képi megjelenítések** szakaszban látható. A naplófájlokat is megtalálhatja a **Naplók** szakaszban.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Szkriptek végrehajtása helyi Docker-környezetben

Az Azure ML segítségével könnyedén konfigurálhat további végrehajtási környezeteket, például a Dockert, és futtathatja azokban a szkriptjeit. 

>[!IMPORTANT]
>Ehhez a lépéshez szüksége lesz egy helyileg telepített és elindított Docker Engine-re. További részletekért tekintse meg a telepítési útmutatót.

1. A bal oldali eszköztáron válassza ki a mappa ikont a projekt **Fájlok** listájának megnyitásához. Bontsa ki az `aml_config` mappát. 

2. Figyelje meg, hogy több előre konfigurált környezet is létezik, például a **docker-python**, a **docker-spark** és a **helyi**. 

   Minden környezethez két fájl tartozik, például a `docker-python.compute` és a `docker-python.runconfig`. Nyissa meg az összes különböző fájltípust, és figyelje meg, hogy néhány lehetőséget konfigurálni lehet a szövegszerkesztőben.  

   Zárja be (X) a megnyitott szövegszerkesztő-lapokat.

3. Futtassa az **iris_sklearn.py** szkriptet a **docker-python** környezetben. 

   - A bal oldali eszköztáron kattintson az óra ikonra a **Futtatások** panel megnyitásához. Kattintson a **Minden futtatás** elemre. 
   - A **Minden futtatás** lap tetején az alapértelmezett **helyi** cél környezet helyett válassza ki a **docker-python** lehetőséget. 
   - Ezután a jobb oldalon válassza ki az **iris_sklearn.py** fájlt futtatandó szkriptként. 
   - Az **Argumentumok** mezőt hagyja üresen, mivel a szkript egy alapértelmezett értéket fog megadni. 
   - Kattintson a **Futtatás** gombra.

4. Figyelje meg, hogy a Workbench-ablak jobb oldalán lévő **Feladatok** panelen elindul egy új feladat.

   Ha az első alkalommal futtat Docker-környezetben, a futtatás néhány perccel tovább tart. 

   Az Azure Machine Learning Workbench a háttérben létrehoz egy új docker-fájlt, amely a `docker.compute` fájlban megadott alap Docker-rendszerképre, és a `conda_dependencies.yml` fájlban megadott függőségi Python-csomagokra hivatkozik. A Docker Engine ezután letölti az alap rendszerképet az Azure-ból, telepíti a `conda_dependencies.yml` fájlban megadott Python-csomagokat, majd elindít egy Docker-tárolót. Ezt követően lemásolja a projektmappa helyi példányát (vagy hivatkozik rá, a futtatási konfigurációtól függően), és végrehajtja az `iris_sklearn.py` szkriptet. Végeredményképp ugyanazokat az eredményeket kell kapnia, mint amikor a **helyi** környezetet célozza meg.

5. Most pedig próbáljuk ki a Sparkot. Az alap Docker-rendszerkép tartalmaz egy előre telepített és beállított Spark-példányt. Ez lehetővé teszi, hogy végrehajtson benne egy PySpark-szkriptet. Ez egy egyszerű módja annak, hogy saját Spark-programot fejlesszen és teszteljen anélkül, hogy időt kelljen szánnia a Spark telepítésére és konfigurálására. 

   Nyissa meg az `iris_pyspark.py` fájlt. Ez a szkript betölti az `iris.csv` adatfájlt, és a Spark ML-könyvtár logisztikai regressziós algoritmusával besorolja az Írisz adatkészletet. Módosítsa a futtatási környezetet **docker-spark** környezetre, a szkriptet pedig az **iris_pyspark.py** fájlra, majd futtassa újra. Ez egy kicsit több időt fog igénybe venni, mivel létre kell hozni egy Spark-munkamenetet, és el kell indítani azt a Docker-tárolóban. Azt is észreveheti, hogy az stdout eltér az `iris_pyspark.py` stdout kimenetétől.

6. Futtassa még néhányszor, és adjon meg különböző argumentumokat. 

7. Nyissa meg az `iris_pyspark.py` fájlt a Spark ML-könyvtár segítségével létrehozott egyszerű logisztikai regressziós modell megtekintéséhez. 

8. Használja a **Feladatok** panelt, a futtatási előzmények listanézetet és a futtatás részletei nézetet a különböző végrehajtási környezetekben végzett futtatásoknál.

## <a name="execute-scripts-in-the-azure-ml-cli-window"></a>Szkriptek végrehajtása az Azure ML CLI ablakban

1. Az Azure Machine Learning Workbench alkalmazásban a **Fájl** menüre, majd a **Parancssor megnyitása** lehetőségre kattintva nyissa meg a parancssori ablakot. A parancsprompt a projektmappában nyílik meg, a `C:\Temp\myIris\>` prompttal.

   >[!Important]
   >Az alábbi lépéseket a (Workbench alkalmazásból indított) parancssori ablakban kell elvégeznie:

2. Jelentkezzen be az Azure-ba a parancssor (CLI) használatával. 

   A Workbench alkalmazás és a CLI az Azure-erőforrások hitelesítésekor független hitelesítőadat-gyorsítótárakat használ. Ezt csak egyszer kell megtennie, amíg a gyorsítótárazott token le nem jár. Az **az account list** parancs visszaadja a bejelentkezéséhez elérhető előfizetések listáját. Ha egynél több található, használja a kívánt előfizetés azonosítóértékét, és állítsa be alapértelmezett fiókként az **az set account -s** paranccsal, amelyben megadja az előfizetés azonosítóértékét. Ezután erősítse meg a beállítást az account show paranccsal.

   ```azurecli
   REM login using aka.ms/devicelogin site.
   az login
   
   REM list all Azure subscriptions you have access to. 
   az account list -o table
   
   REM set the current Azure subscription to the one you want to use.
   az set account -s <subscriptionId>
   
   REM verify your current subscription is set correctly
   az account show
   ```

3. Amikor végzett a hitelesítéssel és beállította az aktuális Azure előfizetési környezetet, írja be az alábbi parancsokat a parancssori felület ablakába a matplotlib telepítéséhez, és küldje el a python-szkriptet futtatandó kísérletként.

   ```azurecli
   REM You don't need to do this if you have installed matplotlib locally from the previous steps.
   pip install matplotlib
   
   REM Kick off an execution of the iris_sklearn.py file against local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Tekintse át a kimenetet. Figyelje meg, hogy a kimenet és az eredmény ugyanaz, mint az oktatóanyag korábban végzett futtatásánál, amikor a Workbench segítségével futtatta a szkriptet. 

5. Ha telepítve van a számítógépen a Docker, futtassa ugyanezt a szkriptet a Docker végrehajtási környezetben.

   ```azurecli
   REM Execute iris_sklearn.py in local Docker container Python environment.
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM Execute iris_pyspark.py in local Docker container Spark environment.
   az ml experiment submit -c docker-spark .\iris_pyspark.py 0.1
   ```
6. Az Azure Machine Learning Workbench alkalmazásban kattintson a bal oldali eszköztáron található Mappa ikonra a projektfájlok listázásához, majd nyissa meg a **run.py** nevű Python-szkriptet. 

   Ez a szkript akkor hasznos, ha különböző regularizációs arányokat kell ismételni, és többször is futtatni kell a kísérletet ezekkel az arányokkal. A szkript elindít egy `iris_sklearn.py` feladatot `10.0` regularizációs arány értékkel (nevetségesen nagy szám), majd a következő futtatáskor felezi az arányt, és így tovább, egészen addig, amíg az arány el nem éri a `0.005` értéket. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   A **run.py** szkript parancssorból való indításához futtassa a következő parancsokat:

   ```cmd
   REM Submit iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Amikor a `run.py` futtatása befejeződik, az Azure Machine Learning Workbench futtatási előzmények listanézetében meg fog jelenni egy gráf.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Végrehajtás Docker-tárolóban egy távoli gépen
Ha egy távoli Linux gépen szeretné végrehajtani Docker-tárolóban a szkriptet, SSH-hozzáférésre (felhasználónév és jelszó) lesz szüksége azon a távoli gépen. Az is szükséges továbbá, hogy a távoli gépen a Docker Engine telepítve legyen és fusson. Egy ilyen Linux gép beszerzésének a legegyszerűbb módja, ha létrehoz egy [Ubuntu-alapú adatelemzési virtuális gépet (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) az Azure-ban. (Ügyeljen arra, hogy a CentOS-alapú DSVM NEM támogatott.) 

1. Ha a virtuális gép létrejött, csatolhatja azt végrehajtási környezetként, ha létrehoz egy `.runconfig` és egy `.compute` fájlt az alábbi paranccsal. Az új környezetnek adja a `myvm` nevet.
 
   ```azurecli
   REM create myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >Az IP-cím terület lehet nyilvánosan címezhető teljes tartománynév (FQDN) is, például `vm-name.southcentralus.cloudapp.azure.com`. Érdemes hozzáadni az FQDN-t a DSVM-hez, és azt használni az IP-cím helyett, mert lehet, hogy később költséghatékonyság miatt kikapcsolja a virtuális gépet. Amikor legközelebb elindítja a virtuális gépet, előfordulhat, hogy más lesz az IP-címe.

   Ezután futtassa az alábbi parancsot, amellyel létrehozza a Docker rendszerképet a virtuális gépen, hogy az készen álljon a szkriptek futtatására.
   
   ```azurecli
   REM prepare the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >A `myvm.runconfig` fájlban módosíthatja a `PrepareEnvironment` értéket az alapértelmezett `false` helyett `true` értékre. Ezzel automatikusan előkészíti a Docker-tárolót az első futáskor.

2. Szerkessze a létrejött `myvm.runconfig` fájlt az `aml_config` területen, és módosítsa a keretrendszert az alapértelmezett `PySpark`ról `Python`ra:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Az sem okozhat gondot, ha a keretrendszer PySpark marad. Viszont nem annyira hatékony, kivéve, ha ténylegesen Spark munkamenetre van szüksége a Python szkript futtatásához.

3. Adja ki ugyanazt a parancsot, mint korábban a parancssori felület ablakban, de most a cél legyen a _myvm_:
   ```azurecli
   REM execute iris_sklearn.py in remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   A parancsot a rendszer úgy hajtja végre, mintha `docker-python` környezetet használna, csak a végrehajtás egy távoli Linux rendszerű virtuális gépen történik. A parancssori felület ablak ugyanazt a kimeneti információt jeleníti meg.

4. Próbáljuk ki a Sparkot a tárolóban. Nyissa meg a Fájlkezelőt (ezt megteheti a parancssori felület ablakából is, ha nem okoz gondot az alapvető fájlkezelési parancsok használata). Készítsen másolatot a `myvm.runconfig` fájlról, és adja neki a `myvm-spark.runconfig` nevet. A `Framework` beállítást az új fájlt szerkesztve módosítsa `Python`ról `PySpark`ra:
   ```yaml
   "Framework": "PySpark"
   ```
   A `myvm.compute` fájlt ne módosítsa. A Spark végrehajtásához ugyanaz a Docker rendszerkép szükséges ugyanarról a virtuális gépről. Az új `myvy-spark.runconfig` fájl `target` mezője ugyanarra a `myvm.compute` fájlra mutat a `myvm` névnek köszönhetően.

5. Az alábbi parancsot beírva futtassa azt a távoli Docker-tároló Spark-példányában:
   ```azureli
   REM execute iris_pyspark.py in Spark instance on remote Docker container
   az ml experiment submit -c myvm-spark .\iris_pyspark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Szkript végrehajtása HDInsight-fürtön
Ezt a szkriptet futtathatja egy tényleges Spark-fürtön is. 

1. Ha rendelkezik hozzáféréssel a Spark for Azure HDInsight-fürthöz, hozzon létre egy HDI futtatási konfigurációs parancsot a látható módon. Paraméterként adja meg a HDInsight-fürt nevét, valamint a saját HDInsight felhasználónevét és jelszavát. Használja az alábbi parancsot:

   ```azurecli
   REM create a compute target that points to a HDI cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepare the HDI cluster
   az ml experiment prepare -c myhdi
   ```

   A fürt fő csomópontjának teljes tartományneve (FQDN) általában `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >A `username` a fürt SSH felhasználóneve. Az alapértelmezett érték `sshuser`, ha nem módosítja a HDI kiépítése közben. Nem `admin`, ami a másik, kiépítés alatt a fürt felügyeleti webhelyéhez való hozzáférés engedélyezésére létrehozott felhasználó. 

2. Futtassa az alábbi parancsot, és a szkript fut a HDInsight-fürtön:

   ```azurecli
   REM execute iris_pyspark on the HDI cluster
   az ml experiment submit -c myhdi .\iris_pyspark.py
   ```

   >[!NOTE]
   >Ha egy távoli HDI-fürtön hajtja végre, a `https://<cluster_name>.azurehdinsight.net/yarnui` helyen az `admin` felhasználói fiókkal a YARN feladat-végrehajtási részleteit is megtekintheti.


## <a name="next-steps"></a>Következő lépések
A három részes oktatóanyag-sorozat jelen, második részében megtanulhatta, hogyan használhatók az Azure Machine Learning szolgáltatások a következőkre:
> [!div class="checklist"]
> * Az Azure Machine Learning Workbench használata
> * Szkriptek megnyitása és kód áttekintése
> * Szkriptek végrehajtása helyi környezetben
> * Futtatási előzmények áttekintése
> * Szkriptek végrehajtása helyi Docker-környezetben
> * Szkriptek végrehajtása helyi Azure CLI ablakban
> * Szkriptek végrehajtása távoli Docker-környezetben
> * Szkriptek végrehajtása a felhőben HDInsight-környezetben

Tovább léphet a sorozat harmadik részére. Most, hogy létrehoztuk a logisztikai regresszió modellt, telepítsük valós idejű webszolgáltatásként.

> [!div class="nextstepaction"]
> [Modell üzembe helyezése](tutorial-classifying-iris-part-3.md)

