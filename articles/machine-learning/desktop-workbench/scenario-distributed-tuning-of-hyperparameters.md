---
title: Elosztott hangolása a Hiperparamétereket Azure Machine Learning-munkaterület használatával |} Microsoft Docs
description: Ez a forgatókönyv bemutatja, hogyan hajtsa végre az Azure Machine Learning-munkaterület használatával hiperparamétereket elosztott hangolása
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: c6eccda4329572a181b6a7e7e3870ace4bfac13b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832747"
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Elosztott, az Azure Machine Learning-munkaterület használatával hiperparaméterek beállítása

Ez a forgatókönyv bemutatja, hogyan Azure Machine Learning munkaterület használandó réteget, a gépi tanulási algoritmusok scikit megvalósító hiperparaméterek beállítása – ismerje meg az API-t. Megmutatjuk, hogyan lehet konfigurálni és használata a távoli Docker-tároló és a Spark-fürt egy végrehajtási háttér hiperparaméterek beállítása.

## <a name="link-of-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárház hivatkozás
Az alábbiakban látható a nyilvános GitHub-tárházban hivatkozásra: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Használja az eset áttekintése

Sok gépi tanulási algoritmusok rendelkezik egy vagy több forgatógombját hiperparamétereket nevezik. Ezek forgatógombját engedélyezése a jövőbeli adatokat, a felhasználó által megadott metrikák mérések való algoritmusok hangolása (például pontossága AUC, Gyökátlagos). Adatok tudósok kell megadni. hiperparamétereket értékének létrehozásakor egy modell betanítási adatok, valamint a jövőbeli vizsgálati adatok megtekintése előtt. A ismert betanítási adatok használatával hogyan alapján beállítjuk hiperparamétereket értékeit, hogy a modell a megfelelő teljesítmény rendelkezik-e az ismeretlen Tesztadatok keresztül? 
    
Egy népszerű módszer a finomhangoláshoz hiperparamétereket egy *rács keresési* együtt *kereszt-ellenőrzési*. Kereszt-ellenőrzési olyan módszer, amely értékeli a mennyire egy modell betanítása képzési megfelelő, a teszt készleten előrejelzi. Ezzel a technikával használ, azt először felosztani a dataset K modellrészt és a ciklikus multiplexelés algoritmus K alkalommal majd betanításához. Hajtanak végre az összes, de a modellrészt egyik hívása a "tárolt kibővített modellrészek". Azt számítási K modellek metrikáinak átlagértéket K tárolt kibővített modellrészt keresztül. Ez átlagérték nevű *határokon érvényesített teljesítmény becsült*, K modellek létrehozásakor használt hiperparamétereket értékének függ. Amikor hiperparaméterek beállítása, azt jelölt hyperparameter értékek található, a kereszt-ellenőrzési teljesítményének optimalizálásához gazdarendszerhez becsült lemezterület keresést. Rács keresést a keresési közös technika. A rács keresési a több hiperparamétereket jelölt értékei közül olyan-készlet keresztszorzatát egyedi hiperparamétereket jelölt értékei közül. 

Kereszt-ellenőrzési használatával rács keresési időigényes lehet. Ha egy algoritmus öt hiperparamétereket minden öt jelölt értékekkel, K = 5 modellrészt használjuk. Azt fejezze be a rács keresés 5 betanítása<sup>6</sup>= 15625 modellek. Szerencsére rács keresési kereszt-ellenőrzési használata egy embarrassingly párhuzamos eljárást, és ezek a modellek párhuzamosan is szükség.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).
* Egy telepített példánya [Azure Machine Learning-munkaterület](../service/overview-what-is-azure-ml.md) következő a [telepítése és gyors üzembe helyezés létrehozása](../service/quickstart-installation.md) telepíteni a munkaterületet üzemeltető és fiókokat létrehozni.
* Ez a forgatókönyv azt feltételezi, hogy futtatja Azure ML munkaterület a Windows 10 vagy MacOS a helyileg telepített Docker-motorhoz. 
* A forgatókönyv olyan távoli Docker-tároló futtatni, kiépítése Ubuntu adatok tudományos virtuális gép (DSVM) követve a [utasításokat](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Legalább 8 maggal és 28 Gb memóriát a virtuális gép használatát javasoljuk. Virtuális gépek példányait D4 rendelkezik ilyen kapacitással. 
* Ebben a forgatókönyvben egy Spark-fürt futtatja, a Spark HDInsight-fürt kiépítése követve ezek [utasításokat](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Azt javasoljuk, hogy a fürt a következő beállításokkal rendelkező fejléc és a feldolgozó csomópontok:
    - négy munkavégző csomópontokhoz
    - nyolc processzormaggal
    - 28 Gb memória  
      
  Virtuális gépek példányait D4 rendelkezik ilyen kapacitással. 

     **Hibaelhárítási**: az Azure-előfizetés előfordulhat, hogy rendelkezik a kvóta az használható magok száma. Az Azure-portál nem teszi lehetővé a fürt létrehozása a kvóta túllépését magok teljes száma. Kvóta található, nyissa meg az Azure-portálon az előfizetések szakaszhoz, kattintson a fürt telepítéséhez használt előfizetés, majd kattintson a **használati + kvóták**. Általában kvóták meghatározott / Azure-régiót, és egy régióban, ahol elegendő szabad mag van a Spark-fürt telepítése választható. 

* Hozzon létre egy Azure storage-fiók, amely a dataset tárolására szolgál. Kövesse a [utasításokat](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) tárfiók létrehozásához.

## <a name="data-description"></a>Adatok leírása

Használjuk [TalkingData dataset](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Ehhez az adatkészlethez az alkalmazásokból események mobiltelefonok van. A cél, hogy előre jelezni a mobiltelefon felhasználó által megadott típusát a figyelmet, hogy a felhasználó nemrég létrehozott és a telefon nem és életkor kategóriáját.  

## <a name="scenario-structure"></a>A forgatókönyv struktúra
Ebben a forgatókönyvben a GitHub-tárházban több mappát tartalmaz. Kód és a konfigurációs fájlok **kód** mappa összes dokumentáció áll a **Docs** mappa és az összes lemezkép **képek** mappát. A gyökérmappában található információs fájl, amely tartalmazza az ebben a forgatókönyvben rövid összefoglalása rendelkezik.

### <a name="getting-started"></a>Első lépések
Kattintson az Azure Machine Learning-munkaterület futtatásához, és az "Elosztott hangolása a Hiperparamétereket" sablonból-projekt létrehozása az Azure Machine Learning-munkaterület ikonra. Az új projekt létrehozása a részletes utasításokat talál [telepítse, és hozzon létre a gyors üzembe helyezés](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Végrehajtási környezet konfigurálása
Megmutatjuk, hogyan futtathatók a kód egy távoli Docker-tároló és a Spark-fürt. Először a beállítások, amelyek közösek mindkét környezetben leírása. 

Használjuk [scikit-további](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost), és [azure-tároló](https://pypi.python.org/pypi/azure-storage) csomagok, amelyek nem szerepelnek az alapértelmezett Azure Machine Learning-munkaterület Docker-tároló. Azure-tároló csomag telepítése szükséges [titkosítás](https://pypi.python.org/pypi/cryptography) és [azure](https://pypi.python.org/pypi/azure) csomagok. A csomagok telepítéséhez a Docker-tároló és a Spark-fürt csomópontja, azt conda_dependencies.yml fájl módosításához:

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

A következő lépésben nem csatlakozni végrehajtási környezet Azure-fiók. Fájl menü kattintson a bal felső sarokból AML munkaterület. És kattintson a "nyissa meg a parancssort". Futtassa a parancssori felület

    az login

Egy üzenetet kap

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Nyissa meg a weblap, írja be a kódot, és jelentkezzen be az Azure-fiókjával. Ez a lépés után futtassa a parancssori felület

    az account list -o table

és az Azure-előfizetés-azonosító, amely rendelkezik a AML munkaterület munkaterület fiók található. Végül futtassa a parancssori felület

    az account set -s <subscription ID>

a kapcsolat az Azure-előfizetéshez befejezéséhez.

A következő két szakasz megmutatjuk, hogyan hajthatja végre a távoli docker és Spark-fürt konfigurálását.

#### <a name="configuration-of-remote-docker-container"></a>Távoli Docker-tároló konfigurálása

 Egy távoli Docker-tároló beállításához futtassa a parancssori felület

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

az IP-cím, felhasználónevet és DSVM jelszót. IP-címe DSVM Azure-portálon az a DSVM lap-áttekintés szakaszban talál:

![Virtuális gép IP-címe](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Spark-fürt konfigurálása

A parancssori felület futtatása Spark környezet beállítása

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

a fürt, a fürt SSH-felhasználónév és jelszó nevével. Az alapértelmezett érték az SSH-felhasználónév `sshuser`, kivéve, ha megváltozott a fürt kiépítése során. A fürt neve az Azure portálon található a fürt lap Tulajdonságok részében:

![Fürt neve](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Spark-sklearn csomag használatával lehet a Spark on az hiperparamétereket elosztott hangolása végrehajtási környezetet. Azt a módosított spark_dependencies.yml fájlt kell telepítenie a csomagot, Spark végrehajtási környezet használata esetén:

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
Ebben a forgatókönyvben a kód azt feltételezi, hogy az adatok tárolását az Azure blob storage. Azt szemléltetik kezdetben Kaggle helyről származó adatok letöltése a számítógépre, és töltse fel a blob Storage tárolóban. Ezután megmutatjuk, hogyan beolvasni az adatokat a blob storage. 

Adatok Kaggle letöltéséhez keresse fel [dataset lap](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) , és kattintson a Letöltés gombra. Jelentkezzen be Kaggle fog kérni. A bejelentkezés után irányítja a dataset oldalra. Ezután töltse le a hét első fájlokat a bal oldali oszlopban jelölje ki őket, kattintson a Letöltés gombra. A letöltött fájlok összesített mérete 289 Mb. Töltse fel ezeket a fájlokat a blob-tároló, hozzon létre blob storage tárolót "adatkészlet" a tárfiók. Nyissa meg a storage-fiókot, kattintson a Blobok majd + tároló Azure oldalán, amely teheti meg. Adja meg a "adatkészlet" néven, és kattintson az OK gombra. Az alábbi képek ezeket a lépéseket mutatja be:

![Nyissa meg a blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![tároló megnyitása](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Ezt követően tároló adatkészlet válasszon a listából, majd kattintson a Feltöltés gombra. Azure-portál lehetővé teszi, hogy egyszerre több fájl feltöltése. "Feltöltése a blob" szakaszban mappa gombra, válassza ki az összes fájlt a dataset adatkészletből, kattintson a Megnyitás gombra, és kattintson feltöltése. Az alábbi képernyőfelvételen ezeket a lépéseket mutatja be:

![Blob feltöltése](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

A fájlok feltöltése néhány percig, attól függően, hogy az internetkapcsolat. 

A kódban, használjuk [Azure Storage szolgáltatás SDK](https://azure-storage.readthedocs.io/en/latest/) töltheti le a dataset blob-tároló az aktuális végrehajtási környezetnek. A letöltés megtörténik a terhelés\_data() függvény load_data.py fájlból. Ez a kód használatához ki kell cserélni < fióknév > és < ACCOUNT_KEY > nevű és a tároló adatkészlet tárfiók elsődleges kulcs. A fiók nevét a a tárfiók Azure lap bal felső sarokban látható. Fiók lekérése a kulcs, jelölje be a tárolási Azure oldalán elérési kulcsok fiók (lásd az első képernyőfelvételen adatfeldolgozást szakaszban), és másolja a hosszú karakterlánc kulcsként megadott oszlop első sorában:
 
![A hozzáférési kulcsot](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Load_data() függvény a következő kód egy fájl tölti le:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Figyelje meg, hogy nem kell manuálisan load_data.py fájl futtatásához. Neve az egyéb fájlok később.

### <a name="feature-engineering"></a>Jellemzőkiemelés
Szolgáltatás összes funkciójának számítástechnikai kódjának van\_engineering.py fájlt. Nem kell manuálisan futtassa a feature_engineering.py fájlt. Később az neve más fájlokból.

Több szolgáltatáskészletek létrehozhatunk:
* Egy közbeni kódolását, a márka és a mobiltelefon típusú (egy\_forró\_brand_model függvény)
* Azon részét, felhasználó által előállított, a munkanapokhoz (hét napja\_hour_features függvény)
* Azon részét, felhasználó által előállított, minden órában (hét napja\_hour_features függvény)
* Azon részét, az egyes kombinációkban hétköznap és óra felhasználó által létrehozott eseményeket (hét napja\_hour_features függvény)
* Egyes alkalmazások a felhasználó által létrehozott eseményeket hányada (egy\_forró\_app_labels függvény)
* Minden alkalmazás címke felhasználó által létrehozott eseményeket hányada (egy\_forró\_app_labels függvény)
* Azon részét, alkalmazás kategóriánkénti felhasználó által létrehozott eseményeket (szöveges\_category_features függvény)
* Kijelző szolgáltatások volt használt alkalmazások kategóriákban események létrehozása (egy\_hot_category függvény)

Ezek a funkciók volt ösztönző Kaggle kernel [alkalmazások és a feliratok lineáris modell](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels).

Ezeket a szolgáltatásokat a számítási jelentős mennyiségű memóriát igényel. Kezdetben megpróbáltuk számítási 16 GB RAM memóriával rendelkező helyi környezetben. Rendszer volt a szolgáltatásai első négy különböző számítási, de a "nincs elegendő szabad memória" hibaüzenetet kapta, a ötödik szolgáltatáskészlete számításakor. Az első négy szolgáltatás készlet számítási singleVMsmall.py fájl és végrehajtható legyen a helyi környezet futtatásával 

     az ml experiment submit -c local .\singleVMsmall.py   

a parancssori ablakban.

Mivel a helyi környezet túl kicsi az összes szolgáltatáskészletek a számítástechnikai, azt, amely nagyobb memóriával rendelkezik távoli DSVM vált. A végrehajtás DSVM belül AML munkaterület által kezelt Docker-tároló belül történik. A DSVM használatával azt is összes funkciójának számítási és modellek betanítása és hangolására hiperparamétereket (lásd a következő szakaszt). singleVM.py fájl szolgáltatások teljes számítási és a kód modellezési rendelkezik. A következő szakaszban bemutatjuk a távoli DSVM singleVM.py futtatása. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Távoli DSVM használatával hiperparaméterek beállítása
Használjuk [xgboost](https://anaconda.org/conda-forge/xgboost) végrehajtása [1] átmenetes fa kiemelése. Is [scikit-további](http://scikit-learn.org/) csomag hangolására hiperparamétereket, xgboost. Bár xgboost nem scikit része-ismerje meg a csomag, végrehajtja az scikit-API ismerje meg, és ezért hyperparameter hangolása scikit feladatai együtt használható-ismerje meg. 

Xgboost rendelkezik nyolc hiperparamétereket, leírt [Itt](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md):
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* részminta
* cél  
 
Kezdetben távoli DSVM felhasználása és hangolására jelölt értékek kis rácsban hiperparamétereket:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

A rács hiperparamétereket értékei közül négy kombinációk rendelkezik. 5-fold keresztellenőrzési használjuk a xgboost 4 x 5 = 20 eredményezve futtatja. A modellek teljesítményének méréséhez, negatív napló adatvesztés metrika használjuk. A következő kódot a rácsban hiperparamétereket, amely maximalizálja a negatív napló közötti érvényesített adatvesztés értékének megkeresése. A kódot is használja ezeket az értékeket a végső modell betanításához a teljes gyakorlókészlethez keresztül:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Miután létrehozta a modell, elmentjük a hyperparameter hangolása eredményeit. Használjuk a AML munkaterület API-naplózás a legjobb hiperparamétereket és a negatív napló adatvesztés megfelelő határokon érvényesített becslése mentéséhez:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Nem is a rácsban hyperparameter értékek összes kombinációinak határokon érvényesített, negatív napló veszteségek sweeping_results.txt fájlt létrehozni.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Ez a fájl egy speciális van tárolva. / kimeneti könyvtár. Később a megmutatjuk, hogyan lehet letölteni.  

 SingleVM.py először távoli DSVM fut, mielőtt futtatásával létrehozhatunk egy Docker-tároló létezik 

    az ml experiment prepare -c dsvm

a parancssori felület windows. Létrehozása a Docker-tároló több percet vesz igénybe. Ezt követően DSVM singleVM.py futtassa azt:

    az ml experiment submit -c dsvm .\singleVM.py

Ez a parancs 1 óra 38 perc ha DSVM 8 maggal és 28 Gb memóriával rendelkezik-e a befejezését. A naplózott értékek futtatása előzmények ablak AML munkaterület tekintheti meg:

![futtatási előzményei](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Alapértelmezés szerint futtatni előzmények ablak értékek és diagramokat az első 1-2 naplózott értékeket mutatja. A kiválasztott hiperparamétereket közül teljes lista megtekintéséhez kattintson az előző képernyőképen látható piros kör jelölésű beállítások ikonra. Válassza ki a táblázatban megjelenítendő hiperparamétereket. Is jelölje be a diagramokon a Futtatás előzmények ablak felső részén látható, kattintson a kék kör jelölésű beállítás ikonra, és válassza ki a diagramokon a listából. 

Hiperparamétereket választott értékeit is megvizsgálhatók futtató tulajdonságai ablakban: 

![Futtassa a Tulajdonságok](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

A futtató tulajdonságai ablak jobb felső sarkában esetében szakasz kimeneti fájlok listáját tartalmazó létrehozott fájlok '. \output "mappa. abszolút\_eredmény.txt letölthető innen jelölje ki azt, kattintson a Letöltés gombra. sweeping_results.txt rendelkeznie kell a következő az alábbiakat:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Spark-fürt használatával hiperparaméterek beállítása
Horizontális felskálázás hiperparaméterek beállítása, és nagyobb rács használata Spark-fürt használjuk. Az új rács

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

A rács hiperparamétereket értékének 16 kombinációi rendelkezik. Mivel 5-fold keresztellenőrzési használjuk, xgboost 16 x 5 = 80 Futtatás alkalommal.

scikit – ismerje meg a csomag nem rendelkezik egy natív támogatás a Spark-fürt használatával hiperparaméterek beállítása. Szerencsére [spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) Databricks csomagot tölti ki a térközét. Ez a csomag biztosít, amely rendelkezik majdnem azonos GridSearchCV függvényében API scikit GridSearchCV függvény – ismerje meg. Spark-sklearn használja, és hangolja használata Spark hiperparamétereket kell a Spark-környezet létrehozása

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Ezután azt cseréje 

    from sklearn.model_selection import GridSearchCV

a következővel: 

    from spark_sklearn import GridSearchCV

Azt a scikit GridSearchCV hívása cserélni-megismerheti, hogyan kell helyreállítani a spark-sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

A Spark használatával hiperparaméterek beállítása végleges kódját van szétosztva\_sweep.py fájlt. SingleVM.py és distributed_sweep.py közötti különbség a rács meghatározását és további négy sornyi kód. Figyelje meg is, hogy AML munkaterület szolgáltatások miatt, a naplózás kódja nem változik a végrehajtási környezet módosítása az távoli DSVM Spark-fürt.

Először futtatása Spark-fürt distributed_sweep.py, előtt nincs Python-csomagok telepítése szükséges. Ez a futtatásával érhető el 

    az ml experiment prepare -c spark

a parancssori felület windows. A telepítés több percet vesz igénybe. Ezt követően Spark-fürt distributed_sweep.py futtassa azt:

    az ml experiment submit -c spark .\distributed_sweep.py

Ez a parancs percben 1 óra 6 Ha Spark-fürt rendelkezik a 6 munkavégző csomópontokhoz 28 Gb memóriával rendelkező befejeződik. Hyperparameter hangolása eredményeit elérhető Azure Machine Learning munkaterület DSVM távoli végrehajtás azonos módon. (azaz naplózza, ajánlott értékek, a hiperparamétereket és sweeping_results.txt fájl)

### <a name="architecture-diagram"></a>Architektúradiagram

Az alábbi ábrán látható, az általános munkafolyamat: ![architektúrája](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Összegzés 

Ebben a forgatókönyvben azt bemutatta Azure Machine Learning-munkaterület használata a távoli virtuális gépek és a Spark-fürtök hiperparamétereket hangolására. Látott végrehajtási környezetnek könnyű konfigurálása Azure Machine Learning-munkaterület eszközöket is biztosít. Azt is lehetővé teszi, hogy könnyen váltás. 

## <a name="references"></a>Referencia

[1] T. Chen és c Guestrin. [XGBoost: Méretezhető fát rendszer kiemelése](https://arxiv.org/abs/1603.02754). KDD 2016.




