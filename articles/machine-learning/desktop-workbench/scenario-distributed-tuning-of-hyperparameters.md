---
title: Elosztott hangolása az Hiperparaméterek Azure Machine Learning Workbench használatával |} A Microsoft Docs
description: Ebből a forgatókönyvből megtudhatja, hogyan teheti az Azure Machine Learning Workbench használatával hiperparaméterek elosztott hangolása
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 920b019640df9d2da174101e2b1b90dfd4da6f56
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578735"
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Az Azure Machine Learning Workbench használatával hiperparaméterek elosztott hangolása

Ebből a forgatókönyvből megtudhatja, hogyan használhatja az Azure Machine Learning Workbench horizontális felskálázás, gépi tanulási algoritmusok scikit megvalósító hiperparaméterek beállítása – ismerje meg az API-t. Bemutatjuk, hogyan konfigurálhatja és használhatja egy távoli Docker-tárolóban és a Spark-fürtöt, egy végrehajtási háttérrendszer hiperparaméterek finomhangoláshoz.

## <a name="link-of-the-gallery-github-repository"></a>Hivatkozás katalógus GitHub-adattár
Következő pedig a hivatkozás a nyilvános GitHub-adattárhoz: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Használati eset – áttekintés

Számos gépi tanulási algoritmusok hiperparaméterek nevű egy vagy több belül van. Ezek belül engedélyezése algoritmusok teljesítményének optimalizálásához jövőbeli adatokon, felhasználó által megadott metrikák alapján mérjük a finomhangolási (például a pontosság AUC, Gyökátlagos). Adattudós hiperparaméterek értékének megadására, amikor létre a modell a betanítási adatokon, és a jövőbeli Tesztadatok frissíteniük kell. Hogyan alapján az ismert betanítási adatok is beállítjuk hiperparaméterek értékeit, hogy a modellnek van egy jó teljesítményt az ismeretlen teszt adatai felett? 
    
Egy, a finomhangoláshoz hiperparaméterek népszerű módszer egy *rács keresési* kombinálva *kereszt-ellenőrzési*. Kereszt-ellenőrzés olyan módszer, amely felméri arról, hogy egy modell a betanítási készlet tanított előrejelzi a teszt csoport keresztül. Ezt a módszert használja, azt először az adatkészlet felosztása K modellrész és az algoritmus ezer alkalommal ciklikus multiplexelés majd betanításához. Ez az összes végzünk, de a modellrész egyik nevű, "tárolt kibővített modellrészek". Azt a számítási modellek K metrikáinak átlagos értékét K tárolt kibővített modellrész keresztül. Az átlagérték nevű *közötti érvényesített teljesítmény becsült*, attól függ, az értékek hiperparaméterek K modellek létrehozásakor használt. Hiperparaméterek beállítása, ha azt a területet jelölt hiperparaméter érték megkereséséhez, amelyekre a kereszt-ellenőrzés a teljesítmény optimalizálása-becslésére keresést. Rács keresése a gyakori keresési technika. A hely több hiperparaméterek jelölt értékek rács keresési, egy-készlet keresztszorzatát jelölt értékek egyes hiperparaméterek. 

Rács search használatával a kereszt-ellenőrzés időigényes lehet. Ha egy olyan algoritmust öt hiperparaméterek egyes öt jelölt értékekkel, K = 5 modellrész használjuk. Hogy végezze el a rács keresés alapján 5 képzési<sup>6</sup>= 15625 modellek. Szerencsére a rács keresési kereszt-ellenőrzés használata egy zavaróan párhuzamos eljárást, és ezek a modellek képes tartani a párhuzamosan.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverziók érhető el).
* Egy telepített példánya [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) következő a [telepítési és létrehozási rövid útmutató](../service/quickstart-installation.md) a Workbench telepítése és-fiókok létrehozásához.
* Ebben a forgatókönyvben azt feltételezi, hogy futtatja az Azure Machine Learning Workbench Windows 10-es vagy MacOS rendszeren a helyileg telepített Docker-motor. 
* A forgatókönyv egy távoli Docker-tárolót futtathat, üzembe helyezése az Ubuntu adatelemzési virtuális gép (DSVM) a következő a [utasításokat](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Legalább 8 maggal és 28 Gb memóriával rendelkező virtuális gép használatát javasoljuk. Virtuális gépek példányainak D4 rendelkezik ilyen kapacitással. 
* Ebben a forgatókönyvben a Spark-fürt futtatásához a Spark HDInsight-fürt üzembe helyezése a következő [utasításokat](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Javasoljuk, hogy a fürt a következő beállításokkal rendelkező fejlécet és a feldolgozó csomópontok:
    - négy munkavégző csomópontok
    - nyolc processzormaggal
    - 28 Gb memória  
      
  Virtuális gépek példányainak D4 rendelkezik ilyen kapacitással. 

     **Hibaelhárítási**: az Azure-előfizetésre lehet kvóta felhasználható magok számát. Az Azure Portalon nem engedélyezi a fürt létrehozása és a kvóta túllépése magok teljes száma. Kvóta találja, keresse fel az Azure Portalon, az előfizetések szakaszhoz, kattintson arra az előfizetésre, a fürt üzembe helyezéséhez használt, és kattintson a **használat + kvóták**. Általában a kvóták az Azure-régió-nként vannak meghatározva, és Ön kiválaszthatja, egy régióban, ahol van elég szabad magja a Spark-fürt üzembe helyezéséhez. 

* Hozzon létre egy Azure storage-fiókot, amely az adatkészlet tárolására szolgál. Kövesse a [utasításokat](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) , hozzon létre egy tárfiókot.

## <a name="data-description"></a>Adatok leírása

Használjuk a [TalkingData adatkészlet](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Ez az adatkészlet mobiltelefonok az alkalmazásokból eseményeket tartalmaz. A cél, hogy előre jelezni a mobiltelefon felhasználó által megadott típusát a telefonszám és az eseményeket, a felhasználó legutóbb létrehozott nemek és a korszűrő kategóriáját.  

## <a name="scenario-structure"></a>A forgatókönyv-struktúra
Ebben a forgatókönyvben rendelkezik több mappát a GitHub-adattárban. Kód és a konfigurációs fájlok **kód** mappában van az összes dokumentáció **Docs** mappa és az összes rendszerkép **lemezképek** mappát. A gyökérmappa rendelkezik információs fájl, amely tartalmazza az ebben a forgatókönyvben rövid összefoglalása.

### <a name="getting-started"></a>Első lépések
Kattintson az Azure Machine Learning Workbench futtatása, és hozzon létre egy projektet az "Elosztott hangolása az Hiperparaméterek" sablon az Azure Machine Learning Workbench ikonra. Az új projekt létrehozásával is találhat részletes tájékoztatást [telepítése és létrehozási rövid útmutató](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>A végrehajtási környezetek konfigurálása
Bemutatjuk, hogyan kód futtatása távoli Docker-tárolóban és a egy Spark-fürtön. A beállítást, akkor mindkét környezetben leírással kezdődik. 

Használjuk [scikit-további](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost), és [azure-storage](https://pypi.python.org/pypi/azure-storage) csomagok, amelyek nem szerepelnek az alapértelmezett Docker-tárolót az Azure Machine Learning Workbench alkalmazásban. Azure-storage csomag telepítése szükséges [titkosítás](https://pypi.python.org/pypi/cryptography) és [azure](https://pypi.python.org/pypi/azure) csomagokat. A csomagok telepítéséhez, a Docker-tároló és a Spark-fürt csomópontjai, módosítjuk conda_dependencies.yml fájlt:

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

A módosított conda\_dependencies.yml fájl oktatóanyag aml_config könyvtárban tárolja. 

A következő lépésben a végrehajtási környezet Azure-fiókjába csatlakozunk. Kattintson a Fájl menü AML Workbench bal felső sarkában. És válassza a "nyissa meg a parancssort". Ezután futtassa a CLI-ben

    az login

Egy üzenetet kap

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Nyissa meg a weblapot, adja meg a kódot, és jelentkezzen be az Azure-fiókjával. Ebben a lépésben után futtassa a CLI-ben

    az account list -o table

és az Azure-előfizetés-azonosító, amely már AML Workbench munkaterület fiókja található. Végezetül futtassa a CLI-ben

    az account set -s <subscription ID>

a kapcsolat az Azure-előfizetéshez befejezéséhez.

A következő két szakasz bemutatjuk, hogyan végezheti el a távoli docker- és Spark-fürt konfigurációját.

#### <a name="configuration-of-remote-docker-container"></a>Távoli Docker-tároló konfigurációja

 Egy távoli Docker-tároló beállításához futtassa a CLI-ben

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

az IP címe, felhasználónév és jelszó a dsvm-hez. DSVM IP-címét az Azure Portalon található áttekintés szakasz a DSVM-lap:

![Virtuális gép IP-címe](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Spark-fürt konfigurálása

A Spark környezet beállítását, futtassa a CLI-ben

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

a fürt, a fürt SSH-felhasználónév és jelszó nevére. Az alapértelmezett érték az SSH-felhasználónév `sshuser`, kivéve, ha megváltozott a fürt üzembe helyezésekor. A fürt nevét az Azure Portalon található tulajdonságok szakaszában a fürt lapján:

![Fürt neve](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Spark-sklearn csomag Spark rendelkezik, mint az hiperparaméterek elosztott hangolása végrehajtási környezetet használjuk. A csomag telepítéséhez, ha Spark-végrehajtási környezetben történik a spark_dependencies.yml fájl módosítását:

    configuration: 
      #"spark.driver.cores": "8"
      #"spark.driver.memory": "5200m"
      #"spark.executor.instances": "128"
      #"spark.executor.memory": "5200m"  
      #"spark.executor.cores": "2"
  
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7.91"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

A módosított spark\_dependencies.yml fájl oktatóanyag aml_config könyvtárban tárolja. 

### <a name="data-ingestion"></a>Adatfeldolgozás
Ebben a forgatókönyvben a kód azt feltételezi, hogy az adatok Azure blob storage-ban tárolja. Bemutatjuk, először töltse le az adatok Kaggle helyről a számítógépre, és töltse fel a blob Storage. Ezután bemutatjuk, hogyan olvashatja be az adatokat blob storage-ból. 

Adatokat letölteni a Kaggle, lépjen a [adatkészlet lap](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) , és kattintson a Letöltés gombra. Jelentkezzen be Kaggle kell adnia. A bejelentkezés után irányítja vissza az adatkészlet lapra. Ezután töltse le az első hét fájlt a bal oldali oszlopban jelölje ki őket, és a Letöltés gombra kattintva. A letöltött fájlok összesített mérete 289 Mb. Töltse fel ezeket a fájlokat a blob storage-ba, hozzon létre blob storage-tároló 'dataset' a tárfiókban található. Nyissa meg a storage-fiókot, kattintson a Blobok és kattintson + tároló oldala az Azure, amely teheti meg. Adja meg a 'dataset' neveként, és kattintson az OK gombra. A következő képernyőfelvételek bemutatják, ezeket a lépéseket:

![Nyissa meg a blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![Open container](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Ezt követően adatkészlet tároló kiválasztása a listából, majd kattintson a Feltöltés gombra. Az Azure portal segítségével egyszerre több fájl tölthető fel. "Blob feltöltése" szakaszban kattintson a mappa gombra, az összes fájl kijelölése az adatkészletből, kattintson a Megnyitás gombra, és kattintson feltöltése. A következő képernyőkép ezeket a lépéseket mutatja be:

![Blob feltöltése](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

A fájlok feltöltése az internetkapcsolat sebességétől függően néhány percet vesz igénybe. 

A kódban, használjuk [Azure Storage SDK](https://docs.microsoft.com/python/azure/) letöltési blob storage-ból az adatkészletet az aktuális végrehajtási környezetnek megfelelően. A letöltés megtörténik a terhelés\_data() függvény load_data.py fájlból. Ez a kód használatához ki kell cserélni < fióknév > és < ACCOUNT_KEY > nevére, illetve a storage-fiókot, amelyen az adatkészlet az elsődleges kulcsa. A fiók nevét, a storage-fiók oldala az Azure bal felső sarokban látható. -Fiók létrehozása kulcs, jelölje be Tárelérési kulcsok oldala az Azure Storage-fiók (lásd az első képernyőképen adatbetöltés szakaszban), és majd másolja ki a hosszú karakterlánc elsődlegeskulcs-oszlop első sorában:
 
![Hozzáférési kulcs](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Load_data() függvény a következő kód letölti az egyetlen fájl:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Figyelje meg, hogy nem kell manuálisan futtassa load_data.py fájlt. Neve a más fájlokból később.

### <a name="feature-engineering"></a>Jellemzőkiemelés
Minden funkció számítástechnika a kódja: a szolgáltatás\_engineering.py fájlt. Nem kell manuálisan futtassa feature_engineering.py fájlt. Később, neve lesz más fájlokból.

Több szolgáltatáskészleteket hozunk létre:
* Egy gyakori márka és a modell a mobiltelefon-kódolás (egy\_gyakori elérésű\_brand_model függvény)
* Az első név a felhasználó által létrehozott események töredék (hét napja\_hour_features függvény)
* Minden egyes óráért a felhasználó által létrehozott eseményeket hányada (hét napja\_hour_features függvény)
* Hét napja és az óra, az egyes kombinációkban szereplő felhasználó által létrehozott eseményeket hányada (hét napja\_hour_features függvény)
* Minden alkalmazás a felhasználó által létrehozott eseményeket hányada (egy\_gyakori elérésű\_app_labels függvény)
* Minden alkalmazás címke a felhasználó által létrehozott eseményeket hányada (egy\_gyakori elérésű\_app_labels függvény)
* Az egyes alkalmazás a felhasználó által létrehozott eseményeket hányada (szöveges\_category_features függvény)
* Kijelző funkciók kategóriákba tartozó alkalmazásokat, amelyek korábban használt generált események (egy\_hot_category függvény)

Ezek a funkciók inspirációt Kaggle kernel [lineáris modellt az alkalmazások és a címkék](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels).

A számítás, ezeket a szolgáltatásokat jelentős mennyiségű memóriát igényel. Kezdetben megpróbáltuk számítási szolgáltatások a helyi környezetben, 16 GB RAM memóriával rendelkező. Sikerült a szolgáltatások első négy különböző számítási egyelőre "kevés a memória" hibaüzenetet kapta, az ötödik szolgáltatáskészlet kiszámításakor. Az első négy szolgáltatáskészleteket belül a számításnak singleVMsmall.py fájlt, és végrehajtható legyen a helyi környezetben futtatja 

     az ml experiment submit -c local .\singleVMsmall.py   

parancssori felület ablakába.

Mivel helyi környezetben túl kicsi a számítástechnika összes szolgáltatáskészletek, hogy váltson távoli dsvm-hez, amely rendelkezik a nagyobb memória. A végrehajtás belül DSVM AML Workbench által felügyelt Docker-tárolón belül történik. A DSVM használata tudjuk összes számítási és modelleket taníthat be és finomhangolása hiperparaméterek (lásd a következő szakaszban). singleVM.py fájl rendelkezik, a szolgáltatások teljes kiszámításához és a modellezés kódot. A következő szakaszban bemutatjuk a távoli DSVM singleVM.py futtatása. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Hangolási hiperparaméterek távoli DSVM használata
Használjuk a [xgboost](https://anaconda.org/conda-forge/xgboost) végrehajtása [1] átmenetes fa kiemelése. Is [scikit-további](http://scikit-learn.org/) csomag hangolására vonatkozó xgboost hiperparaméterek. Bár xgboost nem része a scikit-ismerje meg a csomag, scikit valósítja meg – ismerje meg az API-t, és ezért együtt is használhatók a hiperparaméter finomhangolása scikit funkcióit – ismerje meg. 

Xgboost rendelkezik nyolc hiperparaméterek leírt [Itt](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md):
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* részminta
* Cél  
 
Kezdetben hogy távoli DSVM használata, és finomhangolási feladat hiperparaméterek egy kis rács jelölt értékek a:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

A rács négy kombinációit hiperparaméterek értékeket tartalmaz. Használjuk a keresztellenőrzési 5-fold, 4 x 5 = 20 eredményez, xgboost futtatja. A modellek teljesítményének mérésére, negatív log adatveszteség metrika használjuk. A következő kódot az értékeket a rácsból hiperparaméterek, amely maximalizálja a negatív napló közötti érvényesített adatveszteség talál. A kód is használja ezeket az értékeket a kész modell betanításához a teljes gyakorlókészlethez keresztül:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Miután létrehozta a modellt, hogy a hiperparaméter finomhangolása eredményeinek mentésére. Használjuk a naplózás API AML Workbench hiperparaméterek ajánlott értékeit és a negatív log veszteség megfelelő közötti érvényesített becslés mentéséhez:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Azt is létrehozhat sweeping_results.txt fájlt a határokon érvényesítve, negatív log veszteségeinek hiperparaméter érték a rács minden kombinációja.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Ez a fájl tárolva van egy speciális. / directory adja vissza. Később bemutatjuk, hogyan töltheti le.  

 Mielőtt futtatná singleVM.py távoli DSVM az első alkalommal, hozunk létre van egy Docker-tároló futtatásával 

    az ml experiment prepare -c dsvm

a parancssori felület windows. Létrehozás a Docker-tároló több percig tart. Ezt követően DSVM singleVM.py futtassa azt:

    az ml experiment submit -c dsvm .\singleVM.py

Ez a parancs befejeződik, 1 óra 38 perc múlva Ha DSVM 8 magos, 28 Gb memóriával rendelkezik-e. Futtatási előzmények ablakában az AML-munkaterület az a naplózott értékek tekinthet meg:

![Futtatási előzmények](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Alapértelmezés szerint futtatási előzmények ablak értékeket és a gráfok az első 1-2 naplózott értékeket mutatja. A kiválasztott értékek hiperparaméterek teljes listájának megtekintéséhez, kattintson a jelölése piros körben az előző képernyőképen a beállítások ikonra. Ezután válassza ki a hiperparaméterek jelennek meg a táblázatban. Ezenkívül válassza ki a futtatási előzmények ablak felső részén látható grafikonok, kattintson a világoskék körre jelölt beállítás ikonra, és a gráfok válasszon a listából. 

A kiválasztott értékek hiperparaméterek is megvizsgálhatók Futtatás tulajdonságai ablakban: 

![futtatási tulajdonságok](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

Futtatás tulajdonságai ablak jobb felső sarkában lévő nincs egy szakasz kimeneti fájlok a listát az összes fájl, a létrehozott '. \output "mappában. abszolút\_eredmény.txt innen tölthető ki, és kattintson a Letöltés gombra. sweeping_results.txt rendelkeznie kell a következő kimenet:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>A Spark használatával hiperparaméterek hangolása
Spark-fürt horizontális felskálázása hiperparaméterek beállítása, és használhatja a nagyobb rács használjuk. Az új rács

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

A rács hiperparaméterek értékek 16 kombinációit rendelkezik. 5-fold keresztellenőrzési használjuk, mivel a futtatott xgboost 16 x 5 = 80 alkalommal.

scikit-ismerje meg a csomag nem rendelkezik egy natív támogatását hangolása hiperparaméterek Spark-fürt használatával. Szerencsére a [spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) Databricks-csomagot a gap tölti ki. Ez a csomag biztosít GridSearchCV-függvény, amely GridSearchCV funkciót, majdnem a azonos API van a scikit-megtudhatja. Spark-sklearn használhatja, és létre kell hozni egy Spark-környezet Spark használatával hiperparaméterek hangolása

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Ezután lecseréljük 

    from sklearn.model_selection import GridSearchCV

a következővel: 

    from spark_sklearn import GridSearchCV

Továbbá azt cserélje le a scikit-GridSearchCV hívás – ismerje meg, a spark-sklearn egy:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

A Spark használatával hiperparaméterek beállítása végleges kódját az elosztott\_sweep.py fájlt. Az singleVM.py és distributed_sweep.py közötti különbség a rács definíció- és további négy kódsort. Figyelje meg, amely miatt a AML Workbench-szolgáltatások, a naplózás kódot nem változik, amikor a végrehajtási környezetben történő távoli DSVM módosítása a Spark-fürthöz.

Distributed_sweep.py futó Spark-fürt első alkalommal, mielőtt szükségünk van a Python-csomagok telepítéséhez. Ez a futtatásával érhető el 

    az ml experiment prepare -c spark

a parancssori felület windows. Ez a telepítés néhány percet vesz igénybe. Ezt követően Spark-fürt distributed_sweep.py futtassa azt:

    az ml experiment submit -c spark .\distributed_sweep.py

Ezzel a paranccsal az 1 óra 6 perc ha Spark-fürt rendelkezik a 6 feldolgozó csomópontokat, 28 Gb memóriával rendelkező befejeződik. A hiperparaméter finomhangolása eredményeit elérhetők az Azure Machine Learning Workbenchben ugyanúgy, mint a távoli DSVM végrehajtás. (azaz naplók, ajánlott értékek hiperparaméterek és sweeping_results.txt fájl)

### <a name="architecture-diagram"></a>Architektúradiagram

Az alábbi ábrán látható a teljes munkafolyamat: ![architektúra](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Összegzés 

Ebben a forgatókönyvben megmutattuk hangolására hiperparaméterek távoli virtuális gépek és a Spark-fürtök az Azure Machine Learning Workbench használata. Azt láttuk, hogy az Azure Machine Learning Workbench eszközöket biztosít a végrehajtási környezetek egyszerű konfigurációval. Azt is lehetővé teszi, hogy könnyen váltás. 

## <a name="references"></a>Referencia

[1] T. Chen és c Guestrin. [XGBoost: Méretezhető fa rendszer kiemelési](https://arxiv.org/abs/1603.02754). KDD 2016.




