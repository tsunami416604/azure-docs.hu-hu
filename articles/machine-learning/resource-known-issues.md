---
title: Ismert problémák & hibaelhárítás során
titleSuffix: Azure Machine Learning
description: Segítség kérése a Azure Machine Learning hibák vagy hibák kereséséhez és javításához. Ismerje meg az ismert problémákat, a hibaelhárítást és a megkerülő megoldásokat.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4
ms.date: 08/13/2020
ms.openlocfilehash: 71457be4e572a0e04dfffd0689bfbd458f7c2622
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190503"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Ismert problémák és hibaelhárítás a Azure Machine Learningban

Ez a cikk segítséget nyújt a Azure Machine Learning használatakor felmerülő ismert problémák elhárításában. 

A hibaelhárítással kapcsolatos további információkért tekintse meg a cikk végén található [következő lépéseket](#next-steps) .

> [!TIP]
> A hibák vagy egyéb problémák a Azure Machine Learning használatakor felmerülő [erőforrás-kvóták](how-to-manage-quotas.md) eredményei lehetnek. 

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

Esetenként hasznos lehet, ha a Segítség kérése során diagnosztikai adatokat is megadhat. Néhány napló megtekintése: 
1. Látogasson el [Azure Machine learning studióba](https://ml.azure.com). 
1. A bal oldali oldalon válassza a **kísérlet** lehetőséget. 
1. Válasszon ki egy kísérletet.
1. Válasszon egy futtatást.
1. A felső részen válassza a **kimenetek + naplók**lehetőséget.

> [!NOTE]
> Azure Machine Learning a különböző forrásokból származó információkat naplózza a betanítás során, például a AutoML vagy a betanítási feladatot futtató Docker-tárolóban. A naplók közül sok nincs dokumentálva. Ha problémákat tapasztal, és felveszi a kapcsolatot a Microsoft ügyfélszolgálatával, előfordulhat, hogy a hibaelhárítás során ezeket a naplókat is használni tudja.


## <a name="installation-and-import"></a>Telepítés és importálás
                           
* **Pip-telepítés: a függőségek nem garantáltak, hogy konzisztensek legyenek az egysoros telepítéssel:** 

   Ez a pip ismert korlátozása, mivel nem rendelkezik működő függőségi feloldóval, ha egyetlen vonalként telepíti. Az első egyedi függőség az egyetlen, amely a következőt keresi:. 

   A következő kódban `azureml-datadrift` , és `azureml-train-automl` mindkettő egysoros pip-telepítéssel van telepítve. 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   Ebben a példában tegyük fel, hogy `azureml-datadrift` > 1,0-es verzióra van szükség, és a `azureml-train-automl` < 1,2-es verzióra van szükség. Ha a legújabb verziója a `azureml-datadrift` 1,3, akkor mindkét csomag a 1,3-re frissül, függetlenül a `azureml-train-automl` korábbi verzióra vonatkozó csomag követelményeitől. 

   Annak érdekében, hogy a megfelelő verziók telepítve legyenek a csomagokhoz, telepítsen több sort is, például a következő kódban. A megrendelés nem jelent problémát, mivel a pip explicit módon visszakerül a következő sor hívásának részeként. Így a megfelelő verzió-függőségek is érvényesek.
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **A azureml-Train-automl-Client telepítésekor a rendszer nem garantálja a magyarázó csomag telepítését:** 
   
   Ha engedélyezve van egy távoli AutoML futtatása a modell magyarázatával, a következő hibaüzenet jelenik meg: "Kérjük, telepítse a azureml-magyarázni-Model csomagot a modell magyarázata érdekében." Ez egy ismert probléma. Áthidaló megoldásként kövesse az alábbi lépések egyikét:
  
  1. Telepítse a azureml-magyarázza a modellt helyileg.
   ```
      pip install azureml-explain-model
   ```
  2. A magyarázatot teljes mértékben tiltsa le a AutoML-konfigurációban model_explainability = False érték beírásával.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Panda-hibák: általában a AutoML kísérlet során látható:**
   
   Ha a környezetet a pip használatával manuálisan állítja be, előfordulhat, hogy az attribútummal kapcsolatos hibákat (különösen a pandákből) észleli, mert a csomagok telepítése nem támogatott. Az ilyen hibák megelőzése érdekében [telepítse a AUTOML SDK-t a automl_setup. cmd fájl használatával](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md):
   
    1. Nyisson meg egy Anaconda-parancssort, és a GitHub-tárházat klónozással hozzon létre egy minta típusú jegyzetfüzetet.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. CD a azureml/automata-Machine-learning mappához, ahol a minta jegyzetfüzeteket kibontották és futtatták:
    
    ```bash
    automl_setup
    ```
    
* **Hiba: "Brand" a AutoML helyi számítási vagy Azure Databricks fürtön való futtatásakor**

    Ha egy új környezet 2020. június 10. után lett létrehozva az SDK-1.7.0 vagy a korábbi verzióban, a betanítás sikertelen lehet, mert a rendszer egy, a cpuinfo-csomagban található frissítést használ. (Az 2020. június 10. előtt vagy azt megelőzően létrehozott környezetek nem érintettek, mivel a kísérletek távoli számításokon futnak, mert a rendszer gyorsítótárazott képzési lemezképeket használ.) A probléma megkerüléséhez végezze el a következő két lépés valamelyikét:
    
    * Frissítse az SDK-verziót a 1.8.0 vagy újabb verzióra (ez a következőt is visszaminősíti: cpuinfo-5.0.0):
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * A cpuinfo és a 5.0.0 telepített verziójának visszaminősítése:
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* **Hibaüzenet: nem távolítható el a (z) PyYAML**

    Pythonhoz készült Azure Machine Learning SDK: a PyYAML egy `distutils` telepített projekt. Ezért nem tudjuk pontosan meghatározni, hogy mely fájlok tartoznak hozzá, ha részleges eltávolítás van. Ha továbbra is szeretné telepíteni az SDK-t a hiba figyelmen kívül hagyásával, használja a következőt:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Databricks hiba a csomagok telepítésekor**

    Azure Machine Learning SDK telepítése sikertelen Azure Databricks Ha további csomagok vannak telepítve. Bizonyos csomagok, például a `psutil` , ütközéseket okozhatnak. A telepítési hibák elkerülése érdekében telepítse a csomagokat a könyvtár verziószámának lefagyasztásával. Ez a probléma a Databricks és nem a Azure Machine Learning SDK-val kapcsolatos. Előfordulhat, hogy ezt a problémát más kódtárak is megtapasztalják. Példa:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Azt is megteheti, hogy init-parancsfájlokat használ, ha a Python-kódtárakkal együtt tartja a telepítési problémákat. Ez a megközelítés nem támogatott hivatalosan. További információ: [fürtökre kiterjedő init-parancsfájlok](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Databricks-importálási hiba: a név nem importálható `Timedelta` innen `pandas._libs.tslibs` **: Ha az automatikus gépi tanulást használja, futtassa a következő két sort a jegyzetfüzetben:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks-importálási hiba: nincs "pandák. Core. Indexes" nevű modul**: Ha ezt a hibaüzenetet látja, amikor automatikus gépi tanulást használ:

    1. Futtassa ezt a parancsot két csomag telepítéséhez a Azure Databricks-fürtön:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Válassza le, majd csatlakoztassa újra a fürtöt a jegyzetfüzethez.
    
    Ha ezek a lépések nem oldják meg a problémát, próbálja meg újraindítani a fürtöt.

* **Databricks FailToSendFeather**: Ha `FailToSendFeather` Azure Databricks fürtön lévő adatolvasáskor hibaüzenet jelenik meg, tekintse át a következő megoldásokat:
    
    * `azureml-sdk[automl]`A csomag frissítése a legújabb verzióra.
    * Adja hozzá a `azureml-dataprep` 1.1.8 vagy újabb verziót.
    * Adja hozzá a `pyarrow` 0,11-es vagy újabb verziót.
    
## <a name="create-and-manage-workspaces"></a>Munkaterületek létrehozása és kezelése

> [!WARNING]
> Ha áthelyezi a Azure Machine Learning munkaterületet egy másik előfizetésbe, vagy áthelyezi a tulajdonosi előfizetést egy új bérlőre, nem támogatott. Ez hibákhoz vezethet.

* **Azure Portal**: Ha közvetlenül a munkaterületet tekinti meg az SDK-ból vagy a portálról, akkor nem fogja tudni megtekinteni a normál **áttekintő** oldalt a bővítmény előfizetési adataival. Nem válthat másik munkaterületre is. Ha meg kell tekintenie egy másik munkaterületet, lépjen közvetlenül a [Azure Machine learning studióra](https://ml.azure.com) , és keresse meg a munkaterület nevét.

* A **Azure Machine learning Studio webportálon támogatott böngészők**: javasoljuk, hogy az operációs rendszerével kompatibilis legújabb böngészőt használja. A következő böngészők támogatottak:
  * Microsoft Edge (az új Microsoft Edge, legújabb verzió. Nem a Microsoft Edge örökölt)
  * Safari (csak Mac, legújabb verzió)
  * Chrome (legújabb verzió)
  * Firefox (legújabb verzió)

## <a name="set-up-your-environment"></a>A környezet kialakítása

* **Hiba történt a AmlCompute létrehozásakor**: ritkán fordul elő, hogy néhány felhasználó létrehozta Azure Machine learning munkaterületét a Azure Portal, mielőtt a ga-kiadás nem tudja létrehozni a AmlCompute az adott munkaterületen. Felvehet egy támogatási kérést a szolgáltatásra, vagy létrehozhat egy új munkaterületet a portálon vagy az SDK-ban, hogy azonnal feloldja a zárolást.

## <a name="work-with-data"></a>Adatok használata

### <a name="overloaded-azurefile-storage"></a>Túlterhelt AzureFile-tároló

Ha hibaüzenetet kap `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , alkalmazza a következő megkerülő megoldásokat.

Ha más számítási feladatokhoz (például adatátvitelhez) használ fájlmegosztást, a javasolt a Blobok használata, hogy a fájlmegosztás díjmentesen használható legyen a futtatások elküldéséhez. A számítási feladatok felosztása két különböző munkaterület között is lehetséges.

### <a name="passing-data-as-input"></a>Adatok továbbítása bemenetként

*  **TypeError: FileNotFound: nincs ilyen fájl vagy könyvtár**: Ez a hiba akkor fordul elő, ha a megadott elérési út nem az a fájl, ahol a fájl található. Meg kell győződnie arról, hogy a fájlra vonatkozó hivatkozás konzisztens, ha az adatkészletet a számítási célra csatlakoztatta. A determinisztikus állapotának biztosítása érdekében javasoljuk, hogy az absztrakt elérési utat használja az adatkészlet számítási célra való csatlakoztatásakor. A következő kódban például az adathalmazt csatlakoztatjuk a számítási cél fájlrendszerének gyökeréhez `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Ha nem tartalmazza a Lead Forward perjelet, a "/" előtagot kell létrehoznia a munkakönyvtárhoz, például a `/mnt/batch/.../tmp/dataset` számítási célra, hogy jelezze, hová szeretné csatlakoztatni az adatkészletet.

### <a name="data-labeling-projects"></a>Adatcímkéző projektek

|Probléma  |Feloldás  |
|---------|---------|
|Csak a blob-adattárolók által létrehozott adatkészletek használhatók.     |  Ez az aktuális kiadás ismert korlátozása.       |
|A létrehozást követően a projekt hosszú ideig az "inicializálás" kifejezést jeleníti meg.     | Manuálisan frissítse a lapot. Az inicializálásnak másodpercenként körülbelül 20 datapoints kell lennie. Az AutoFrissítés hiánya ismert probléma.         |
|Képek áttekintésekor az újonnan címkézett képek nem jelennek meg.     |   Az összes címkézett kép betöltéséhez válassza az **első** gombot. Az **első** gomb a lista elejére kerül, de az összes címkével ellátott adattal betöltődik.      |
|Az ESC billentyű lenyomásával az objektumok észlelése során a rendszer nulla méretű címkét hoz létre a bal felső sarokban. Az ebben az állapotban lévő címkék elküldése sikertelen.     |   Törölje a címkét a mellette lévő kereszt jelre kattintva.  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> Adatdrift figyelők

Az adatdrift figyelőkkel kapcsolatos korlátozások és ismert problémák:

* A korábbi adatok elemzésének időtartománya a figyelő gyakorisági beállításának 31 intervallumára korlátozódik. 
* A 200 funkcióinak korlátozása, kivéve, ha nincs megadva szolgáltatási lista (az összes funkció használatban van).
* A számítás méretének elég nagynak kell lennie az adatok kezeléséhez.
* Győződjön meg arról, hogy az adatkészlet egy adott figyelő futtatásának kezdési és befejezési dátumán belüli adatokat tartalmaz.
* Az adatkészlet-figyelők csak a 50 vagy több sort tartalmazó adatkészleteken működnek.
* Az adatkészletben lévő oszlopok vagy szolgáltatások a következő táblázatban szereplő feltételek alapján kategorikusnak vagy numerikusnak minősülnek. Ha a szolgáltatás nem felel meg az alábbi feltételeknek – például egy karakterlánc típusú, >100 egyedi értékekkel rendelkező oszlop – a szolgáltatás el lesz dobva az adateltolódási algoritmusból, de a rendszer még mindig felhasználható. 

    | Szolgáltatás típusa | Adattípus | Feltétel | Korlátozások | 
    | ------------ | --------- | --------- | ----------- |
    | Kategorikus | karakterlánc, bool, int, float | A szolgáltatásban található egyedi értékek száma kevesebb, mint 100, és a sorok száma kevesebb, mint 5%. | A Null érték a saját kategóriája. | 
    | Numerikus | int, float | A szolgáltatás értékei numerikus adattípussal rendelkeznek, és nem felelnek meg a kategorikus funkció feltételének. | A szolgáltatás el lett dobva, ha az értékek 15%-a null értékű >. | 

* Ha [létrehozott egy adatdrift-figyelőt](how-to-monitor-datasets.md) , de nem látja az adatokat a Azure Machine learning Studio **adatkészlet-figyelők** lapján, próbálkozzon a következőkkel.

    1. Ellenőrizze, hogy a megfelelő dátumtartomány van-e kiválasztva az oldal tetején.  
    1. Az **adatkészlet-figyelők** lapon válassza a kísérlet hivatkozást a Futtatás állapotának vizsgálatához.  Ez a hivatkozás a tábla jobb szélén található.
    1. Ha a Futtatás sikeresen befejeződött, ellenőrizze az illesztőprogram-naplókat, hogy hány mérőszám lett létrehozva, vagy hogy van-e figyelmeztető üzenet.  A kísérletre való kattintás után keresse meg az illesztőprogram-naplókat a **kimenet + naplók** lapon.

* Ha az SDK `backfill()` -függvény nem hozza ki a várt kimenetet, előfordulhat, hogy egy hitelesítési probléma okozza.  Amikor létrehozza a számítást, hogy átadja ezt a függvényt, ne használja `Run.get_context().experiment.workspace.compute_targets` .  Ehelyett használja a [ServicePrincipalAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) , például az alábbiakat a függvénybe beadott számítási számítás létrehozásához `backfill()` : 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

* **Hosszú számítási előkészítési idő:**

Előfordulhat, hogy néhány perc vagy még ennél is több van, amikor először csatlakozik vagy létrehoz egy számítási célt. 

A modell adatgyűjtője akár 10 percet is igénybe vehet, amíg az adatok megérkeznek a blob Storage-fiókba. Várjon 10 percet, hogy az alábbi cellák biztosan fussanak.

```python
import time
time.sleep(600)
```

* **Valós idejű végpontok naplója:**

A valós idejű végpontok naplófájljai a vásárlói adatmennyiség. A valós idejű végpontok hibaelhárításához a következő kódot használhatja a naplók engedélyezéséhez. 

További részletek a webszolgáltatási végpontok figyeléséről [ebben a cikkben](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights#query-logs-for-deployed-models).

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```
Ha több Bérlővel rendelkezik, előfordulhat, hogy a következő hitelesítő kódot kell megadnia, mielőtt `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

## <a name="train-models"></a>Modellek betanítása

* **ModuleErrors (nincs nevű modul)**: Ha a ModuleErrors-ben futtatott kísérleteket az Azure ml-ben, az azt jelenti, hogy a betanítási parancsfájl egy telepítendő csomagot vár, de nincs hozzáadva. A csomag nevének megadása után az Azure ML telepíti a csomagot a betanítási futtatáshoz használt környezetben. 

    Ha a [becslések](concept-azure-machine-learning-architecture.md#estimators) -t használja a kísérletek elküldéséhez, megadhatja a csomag nevét `pip_packages` `conda_packages` a kalkulátoron keresztül vagy paraméterrel, attól függően, hogy melyik forrásból szeretné telepíteni a csomagot. Egy YML-fájlt is megadhat az összes függőségének használatával, `conda_dependencies_file` vagy listázhatja az összes pip-követelményét egy txt-fájlban a `pip_requirements_file` paraméter használatával. Ha rendelkezik saját Azure ML-környezetbeli objektummal, amellyel felül szeretné bírálni a kalkulátor által használt alapértelmezett rendszerképet, megadhatja ezt a környezetet a `environment` kalkulátor konstruktorának paraméterén keresztül.

    Az Azure ML a TensorFlow, a PyTorch, a Chainer és a SKLearn keretrendszer-specifikus becslések is biztosítja. Ezeknek a becslések a használata biztosítja, hogy az alapvető keretrendszer függőségei a betanításhoz használt környezetben legyenek telepítve az Ön nevében. Lehetősége van további függőségek megadására a fentiekben leírtak szerint. 
 
    Az Azure ML által karbantartott Docker-rendszerképek és azok tartalma [AzureML-tárolókban](https://github.com/Azure/AzureML-Containers)láthatók.
    A keretrendszer-specifikus függőségek a megfelelő keretrendszer dokumentációs [láncában](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), a [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), a [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)és a [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)listájában találhatók meg.

    > [!Note]
    > Ha úgy gondolja, hogy egy adott csomag elég gyakori ahhoz, hogy hozzá lehessen adni az Azure ML karbantartott lemezképekhez és környezetekhez, hozzon létre GitHub-problémát a [AzureML-tárolókban](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (név nincs meghatározva), AttributeError (objektum nem rendelkezik attribútummal)**: Ez a kivétel a betanítási szkriptből származik. A naplófájlokat a Azure Portalból tekintheti meg, ha további információt szeretne kapni a nem definiált névvel vagy az attribútum hibával kapcsolatban. Az SDK segítségével `run.get_details()` megtekintheti a hibaüzenetet. Ekkor a rendszer a futtatáshoz létrehozott összes naplófájlt is felsorolja. Győződjön meg arról, hogy megtekinti a betanítási szkriptet, és javítsa ki a hibát, mielőtt elküldené a futtatást. 

* A **Horovod le lett**állítva: a legtöbb esetben, ha "AbortedError: Horovod" állapotba került, akkor ez a kivétel azt jelenti, hogy a Horovod leállítását okozó egyik folyamat egy mögöttes kivételt észlelt. Az MPI-feladatok mindegyik rangsora saját dedikált naplófájlba kerül az Azure ML-ben. Ezek a naplók neve `70_driver_logs` . Elosztott képzés esetén a naplók neve utótaggal van ellátva, `_rank` hogy könnyebben megkülönböztesse a naplókat. A Horovod leállítását okozó pontos hiba megtalálásához folytassa az összes naplófájlt, és keresse meg a `Traceback` driver_log fájlok végén található fájlt. Ezen fájlok egyike megadja a tényleges mögöttes kivételt. 

* A **Run vagy a Experiment művelet törlése**: a kísérletek a [kísérlet. Archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) metódussal vagy a Azure Machine learning Studio-ügyfél kísérlet lapjának az "Archive Experiment" gomb használatával is archiválható. Ez a művelet elrejti a kísérletet a lekérdezések és nézetek listájában, de nem törli azt.

    Az egyes kísérletek vagy futtatások végleges törlése jelenleg nem támogatott. További információ a munkaterület-eszközök törléséről: [Machine learning szolgáltatás-munkaterület adatainak exportálása vagy törlése](how-to-export-delete-data.md).

* A **metrikai dokumentum túl nagy**: a Azure Machine learning belső korláttal rendelkezik azon metrikai objektumok méretén, amelyek bejelentkezhetnek a betanítási futtatás során. Ha a "metrikus dokumentum túl nagy" hibaüzenet jelenik meg egy lista értékű metrika naplózásakor, próbálja meg a lista felosztása kisebb adattömbökre, például:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Belsőleg az Azure ML ugyanazzal a metrikai névvel összefűzi a blokkokat egy összefüggő listához.

## <a name="automated-machine-learning"></a>Automatizált gépi tanulás

* **TensorFlow**: az SDK-ból származó 1.5.0-es verziótól kezdve az automatikus gépi tanulás nem telepíti a TensorFlow-modelleket alapértelmezés szerint. A TensorFlow telepítéséhez és az automatikus ML-kísérletekhez való használatához telepítse a TensorFlow = = 1.12.0-et a CondaDependecies-n keresztül. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **Kísérletezési diagramok**: a bináris besorolású diagramok (precíziós visszahívás, ROC, nyereség görbe stb.) az automatizált ml-kísérletek ismétlései között nem megfelelően jelennek meg a felhasználói felületen a 4/12 óta. A diagram ábrázolása jelenleg inverz eredményeket mutat, ahol a jobb teljesítményű modellek alacsonyabb eredményekkel jelennek meg. Egy megoldás a vizsgálat alatt áll.

* **Automatikus gépi tanulási Databricks megszakítása**: Ha Azure Databrickson automatikus gépi tanulási funkciót használ, a Futtatás megszakításához és az új kísérlet futtatásához indítsa újra a Azure Databricks-fürtöt.

* **Databricks >10 iteráció az automatizált gépi tanuláshoz**: az automatikus gépi tanulási beállításokban, ha több mint 10 iteráció van, akkor állítsa `show_output` be `False` a parancsot a futtatáskor.

* **A Azure Machine learning SDK és az automatizált gépi tanulás Databricks widgetje**: az Azure Machine learning SDK widget nem támogatott a Databricks-jegyzetfüzetekben, mert a jegyzetfüzetek nem tudják elemezni a HTML widgeteket. A widgetet a portálon tekintheti meg a Azure Databricks notebook-cellában található Python-kód használatával:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup sikertelen**: 
    * Windows rendszeren futtasson automl_setup egy Anaconda-parancssorból. A Miniconda telepítéséhez kattintson [ide](https://docs.conda.io/en/latest/miniconda.html).
    * A parancs futtatásával győződjön meg arról, hogy a 64 bites Conda telepítve van, és nem 32 bites `conda info` . A legyen `platform` `win-64` Windows vagy Mac rendszerű `osx-64` .
    * Győződjön meg arról, hogy a Conda 4.4.10 vagy újabb verziója telepítve van. A verziót a paranccsal lehet megtekinteni `conda -V` . Ha telepítve van egy korábbi verziója, a paranccsal frissítheti a parancsot: `conda update conda` .
    * Linux `gcc: error trying to exec 'cc1plus'`
      *  Ha a `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` hiba előfordul, telepítse a Build Essentials-t a ther parancs használatával `sudo apt-get install build-essential` .
      * Új Conda-környezet létrehozásához adjon meg egy új nevet az első paraméterként automl_setup. Megtekintheti a meglévő Conda-környezeteket `conda env list` , és eltávolíthatja őket a használatával `conda env remove -n <environmentname>` .
      
* a **automl_setup_linux. sh sikertelen**: ha a automl_setup_linus. sh sikertelen Ubuntu Linux a következő hibával:`unable to execute 'gcc': No such file or directory`-
  1. Győződjön meg arról, hogy a 53-es és a 80-es kimenő portok engedélyezve vannak. Egy Azure-beli virtuális gépen ezt megteheti az Azure Portalon, ha kiválasztja a virtuális gépet, majd a hálózat lehetőségre kattint.
  2. Futtassa a parancsot: `sudo apt-get update`
  3. Futtassa a parancsot: `sudo apt-get install build-essential --fix-missing`
  4. Futtatás `automl_setup_linux.sh` újra

* a **Configuration. ipynb sikertelen**:
  * A helyi Conda esetében először győződjön meg arról, hogy a automl_setup susccessfully fut.
  * Győződjön meg arról, hogy a subscription_id helyes. Keresse meg az subscription_id az Azure Portalon a minden szolgáltatás, majd az előfizetések lehetőség kiválasztásával. A (z) "<" és a ">" karakterek nem szerepelhetnek a subscription_id értékben. Például `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` érvényes a formátuma.
  * Győződjön meg arról, hogy a közreműködő vagy a tulajdonos hozzáférése van az előfizetéshez.
  * Győződjön meg arról, hogy a régió a támogatott régiók egyike:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` , `southcentralus` .
  * Győződjön meg arról, hogy az Azure Portalon keresztül férhet hozzá a régióhoz.
  
* **sikertelen volt az importálás AutoMLConfig**: a Machine learning 1.0.76-verziójában módosult a csomag, amely az új verzióra való frissítés előtt el kell távolítani a korábbi verziót. Ha a `ImportError: cannot import name AutoMLConfig` rendszer a v 1.0.76 v 1.0.76 vagy újabb verzióra történő frissítés után észlelt, hárítsa el a hibát a következő parancs futtatásával: `pip uninstall azureml-train automl` és `pip install azureml-train-auotml` . A automl_setup. cmd parancsfájl ezt automatikusan elvégzi. 

* **munkaterület. from_config sikertelen**: Ha a WS = workspace. from_config () metódus hívása sikertelen –
  1. Győződjön meg arról, hogy a Configuration. ipynb jegyzetfüzet sikeresen futott.
  2. Ha a jegyzetfüzet olyan mappából fut, amely nem a `configuration.ipynb` futtatott mappában található, másolja a aml_config mappát, és a fájl config.jsaz új mappába. Munkaterület. from_config beolvassa a config.jsa jegyzetfüzet mappájába vagy a szülőmappa mappájába.
  3. Ha új előfizetést, erőforráscsoportot, munkaterületet vagy régiót használ, győződjön meg arról, hogy a `configuration.ipynb` jegyzetfüzetet újra futtatja. config.jsközvetlen módosítása csak akkor működik, ha a munkaterület már létezik a megadott erőforrás-csoportban a megadott előfizetésben.
  4. Ha módosítani szeretné a régiót, módosítsa a munkaterületet, az erőforráscsoportot vagy az előfizetést. `Workspace.create` a nem hoz létre vagy frissít egy munkaterületet, ha már létezik, még akkor is, ha a megadott régió eltér.
  
* A **minta jegyzetfüzet sikertelen**: Ha egy minta jegyzetfüzet hibát jelez, a metódus vagy a könyvtár nem létezik:
  * Győződjön meg arról, hogy a correctcorrect kernel ki van választva a jupyter jegyzetfüzetben. A kernel a notebook oldal jobb felső sarkában jelenik meg. Az alapértelmezett érték azure_automl. Vegye figyelembe, hogy a rendszer a rendszermagot a jegyzetfüzet részeként menti. Ha tehát új Conda-környezetre vált, ki kell választania az új kernelt a jegyzetfüzetben.
      * Azure Notebooks esetén a Python 3,6-es értéknek kell lennie. 
      * Helyi Conda-környezetek esetén az automl_setup-ben megadott Conda-envioronment kell lennie.
  * Győződjön meg arról, hogy a jegyzetfüzet a használt SDK-verzióhoz készült. Az SDK verziójának ellenőrzéséhez futtassa a `azureml.core.VERSION` jupyter jegyzetfüzet-cellát. A minta jegyzetfüzetek előző verzióját a GitHubról töltheti le, ha a `Branch` gombra kattint, majd kiválasztja a `Tags` fület, majd kiválasztja a verziót.

* A **NumPy importálása sikertelen a Windows**rendszerben: egyes Windows-környezetek a NumPy betöltésével kapcsolatos hibát látnak a legújabb Python-verzió 3.6.8. Ha ezt a problémát látja, próbálkozzon a Python-verzió 3.6.7.

* A **NumPy importálása sikertelen**: az automatikus ml Conda-környezetben keresse meg a tensorflow verzióját. A támogatott verziók a következők: < 1,13. Távolítsa el a tensorflow a környezetből, ha a verziószáma >= 1,13 a tensorflow és az Eltávolítás verzióját a következőképpen is megtekintheti:
  1. Indítsa el a parancssort, aktiválja a Conda-környezetet, amelyben az automatikus ml-csomagok telepítve vannak.
  2. Adja meg `pip freeze` és keresse meg `tensorflow` , ha található, a felsorolt verziónak < 1,13
  3. Ha a felsorolt verzió nem támogatott verzió, a `pip uninstall tensorflow` parancs-rendszerhéjban írja be az y értéket a megerősítéshez.

## <a name="deploy--serve-models"></a>Modellek üzembe helyezése és kiszolgálása

Tegye a következő hibákat a műveletekhez:

|Hiba  | Feloldás  |
|---------|---------|
|Rendszerkép-létrehozási hiba a webszolgáltatás telepítésekor     |  A "pynacl = = 1.2.1" hozzáadása pip-függőségként a Conda-fájlhoz a rendszerkép-konfigurációhoz       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   A központi telepítésben használt virtuális gépek SKU-jának módosítása több memóriával. |
|FPGA hiba     |  A modelleket nem fogja tudni telepíteni a FPGA, amíg nem kérelmezi és nem hagyta jóvá a FPGA-kvótát. A hozzáférés kéréséhez töltse ki a kvóta kérése űrlapot: https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Azure Machine Learning összetevők frissítése az AK-fürtben

Az Azure Kubernetes Service-fürtben telepített Azure Machine Learning-összetevők frissítéseit manuálisan kell alkalmazni. 

Ezeket a frissítéseket úgy alkalmazhatja, hogy leválasztja a fürtöt a Azure Machine Learning munkaterületről, majd újra csatolja a fürtöt a munkaterülethez. Ha a TLS engedélyezve van a fürtben, a fürt újbóli csatolásakor meg kell adnia a TLS/SSL-tanúsítványt és a titkos kulcsot. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Ha már nem rendelkezik a TLS/SSL-tanúsítvánnyal és a titkos kulccsal, vagy ha Azure Machine Learning által létrehozott tanúsítványt használ, lekérheti a fájlokat a fürt leválasztása előtt a fürthöz való csatlakozással `kubectl` és a titok beolvasásával `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>A Kubernetes Base-64 kódolású formátumban tárolja a titkokat. Ahhoz, hogy a titkokat el tudja végezni, a 64-es alapszintű dekódolást `cert.pem` és a `key.pem` titkok összetevőit kell megadnia `attach_config.enable_ssl` . 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webszolgáltatások az Azure Kubernetes szolgáltatásban – hibák

Az Azure Kubernetes szolgáltatásban számos webszolgáltatási hiba oldható fel a fürthöz való csatlakozással a használatával `kubectl` . Az `kubeconfig.json` Azure Kubernetes Service-fürtöket a következő futtatásával kérheti le:

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Hitelesítési hibák

Ha egy távoli feladatból származó számítási célra hajt végre felügyeleti műveletet, a következő hibák valamelyikét fogja kapni: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Ha például megpróbál létrehozni vagy csatolni egy számítási célt egy olyan ML-folyamatból, amely távoli végrehajtásra van elküldve, a rendszer hibaüzenetet küld.

## <a name="missing-user-interface-items-in-studio"></a>Hiányzó felhasználói felületi elemek a Studióban

Az Azure szerepköralapú hozzáférés-vezérléssel korlátozható a Azure Machine Learningkal végrehajtható műveletek korlátozása. Ezek a korlátozások megakadályozhatják a felhasználói felület elemeinek megjelenítését a Azure Machine Learning Studióban. Ha például olyan szerepkört rendelt hozzá, amely nem tud számítási példányt létrehozni, akkor a számítási példány létrehozásának lehetősége nem fog megjelenni a Studióban.

További információ: [felhasználók és szerepkörök kezelése](how-to-assign-roles.md).

## <a name="next-steps"></a>Következő lépések

További hibaelhárítási cikkek a Azure Machine Learning:

* [A Docker üzembe helyezésének hibaelhárítása Azure Machine Learning](how-to-troubleshoot-deployment.md)
* [Gépi tanulási folyamatok hibakeresése](how-to-debug-pipelines.md)
* [A ParallelRunStep osztály hibakeresése az Azure Machine Learning SDK-val](how-to-debug-parallel-run-step.md)
* [Machine learning számítási példány interaktív hibakeresése a VS Code-ban](how-to-debug-visual-studio-code.md)
* [A Application Insights használata a gépi tanulási folyamatok hibakereséséhez](how-to-debug-pipelines-application-insights.md)
