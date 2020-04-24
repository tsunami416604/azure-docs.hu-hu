---
title: Ismert problémák & hibaelhárítás során
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning ismert problémáinak, megkerülő megoldásainak és hibaelhárítási listájának beolvasása.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: eb8e06370ecbe2b104a19c4e420b5d3ae013a00e
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116315"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Ismert problémák és hibaelhárítási Azure Machine Learning

Ez a cikk segít megkeresni és kijavítani a Azure Machine Learning használatakor felmerülő hibákat és hibákat.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Esetenként hasznos lehet, ha a Segítség kérése során diagnosztikai adatokat is megadhat. Néhány napló megtekintése: 
1. Látogasson el [Azure Machine learning studióba](https://ml.azure.com). 
1. A bal oldali oldalon válassza a **kísérlet** lehetőséget. 
1. Válasszon ki egy kísérletet.
1. Válasszon egy futtatást.
1. A felső részen válassza a **kimenetek + naplók**lehetőséget.

> [!NOTE]
> Azure Machine Learning a különböző forrásokból származó információkat naplózza a betanítás során, például a AutoML vagy a betanítási feladatot futtató Docker-tárolóban. A naplók közül sok nincs dokumentálva. Ha problémákat tapasztal, és felveszi a kapcsolatot a Microsoft ügyfélszolgálatával, előfordulhat, hogy a hibaelhárítás során ezeket a naplókat is használni tudja.


## <a name="resource-quotas"></a>Erőforráskvóták

Ismerje meg, hogy milyen [erőforrás-kvóták](how-to-manage-quotas.md) merülhetnek fel a Azure Machine learning használatakor.

## <a name="installation-and-import"></a>Telepítés és importálás

* **Hibaüzenet: nem távolítható el a (z) PyYAML**

    Pythonhoz készült Azure Machine Learning SDK: a PyYAML `distutils` egy telepített projekt. Ezért nem tudjuk pontosan meghatározni, hogy mely fájlok tartoznak hozzá, ha részleges eltávolítás van. Ha továbbra is szeretné telepíteni az SDK-t a hiba figyelmen kívül hagyásával, használja a következőt:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Databricks hiba a csomagok telepítésekor**

    Azure Machine Learning SDK telepítése sikertelen Azure Databricks Ha további csomagok vannak telepítve. Bizonyos csomagok, például a `psutil`, ütközéseket okozhatnak. A telepítési hibák elkerülése érdekében telepítse a csomagokat a könyvtár verziószámának lefagyasztásával. Ez a probléma a Databricks és nem a Azure Machine Learning SDK-val kapcsolatos. Előfordulhat, hogy ezt a problémát más kódtárak is megtapasztalják. Példa:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Azt is megteheti, hogy init-parancsfájlokat használ, ha a Python-kódtárakkal együtt tartja a telepítési problémákat. Ez a megközelítés nem támogatott hivatalosan. További információ: [fürtökre kiterjedő init-parancsfájlok](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Databricks-importálási hiba: a (z) "Timedelta" név nem importálható a következőből: "pandák. _libs. tslibs"**: Ha az automatikus gépi tanulás használatakor ezt a hibaüzenetet látja, futtassa az alábbi két sort a jegyzetfüzetben:
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

* **Databricks FailToSendFeather**: Ha Azure Databricks fürtön `FailToSendFeather` lévő adatolvasáskor hibaüzenet jelenik meg, tekintse át a következő megoldásokat:
    
    * A `azureml-sdk[automl]` csomag frissítése a legújabb verzióra.
    * Adja `azureml-dataprep` hozzá a 1.1.8 vagy újabb verziót.
    * Adja `pyarrow` hozzá a 0,11-es vagy újabb verziót.
    
* **Pip-telepítés: a dependecies nem garantálják, hogy konzisztensek legyenek az egysoros telepítéssel**: Ez a pip ismert korlátozása, mivel az egyetlen sorba való telepítésekor nem rendelkezik működő függőségi feloldóval. Az első egyedi függőség az egyetlen, amely a következőt keresi:. Ha például olyan Azure-ml-datadrift telepít, amelyhez > szükség van a 1,0-es és a azureml-Train-automl verzióra, amelyhez a < 1,2 verziója szükséges, és ha a legújabb verzió a 1,3, akkor a felhasználó az alábbi módon telepíti a csomagokat, még akkor is 1,3, ha a azureml-Train-automl csomag egy régebbi verziót igényel. 

    * Az egysoros telepítéssel inkonzisztens dependecies jelenik meg.
    ```python
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   
    * Annak érdekében, hogy a megfelelő verziók telepítve legyenek a csomagokhoz, telepítsen több sort is, például a következő kódban. A sorrend nem számít itt.
    
     ```python
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
## <a name="create-and-manage-workspaces"></a>Munkaterületek létrehozása és kezelése

> [!WARNING]
> Ha áthelyezi a Azure Machine Learning munkaterületet egy másik előfizetésbe, vagy áthelyezi a tulajdonosi előfizetést egy új bérlőre, nem támogatott. Ez hibákhoz vezethet.

* **Azure Portal**: Ha közvetlenül a munkaterületet tekinti meg az SDK-ból vagy a portálról, akkor nem fogja tudni megtekinteni a normál **áttekintő** oldalt a bővítmény előfizetési adataival. Nem válthat másik munkaterületre is. Ha meg kell tekintenie egy másik munkaterületet, lépjen közvetlenül a [Azure Machine learning studióra](https://ml.azure.com) , és keresse meg a munkaterület nevét.

## <a name="set-up-your-environment"></a>A környezet kialakítása

* **Hiba történt a AmlCompute létrehozásakor**: ritkán fordul elő, hogy néhány felhasználó létrehozta Azure Machine learning munkaterületét a Azure Portal, mielőtt a ga-kiadás nem tudja létrehozni a AmlCompute az adott munkaterületen. Felvehet egy támogatási kérést a szolgáltatásra, vagy létrehozhat egy új munkaterületet a portálon vagy az SDK-ban, hogy azonnal feloldja a zárolást.

## <a name="work-with-data"></a>Adatok használata

### <a name="overloaded-azurefile-storage"></a>Túlterhelt AzureFile-tároló

Ha hibaüzenetet `Unable to upload project files to working directory in AzureFile because the storage is overloaded`kap, alkalmazza a következő megkerülő megoldásokat.

Ha más számítási feladatokhoz (például adatátvitelhez) használ fájlmegosztást, a javasolt a Blobok használata, hogy a fájlmegosztás díjmentesen használható legyen a futtatások elküldéséhez. A számítási feladatok felosztása két különböző munkaterület között is lehetséges.

### <a name="passing-data-as-input"></a>Adatok továbbítása bemenetként

*  **TypeError: FileNotFound: nincs ilyen fájl vagy könyvtár**: Ez a hiba akkor fordul elő, ha a megadott elérési út nem az a fájl, ahol a fájl található. Meg kell győződnie arról, hogy a fájlra vonatkozó hivatkozás konzisztens, ha az adatkészletet a számítási célra csatlakoztatta. A determinisztikus állapotának biztosítása érdekében javasoljuk, hogy az absztrakt elérési utat használja az adatkészlet számítási célra való csatlakoztatásakor. A következő kódban például az adathalmazt csatlakoztatjuk a számítási cél fájlrendszerének gyökeréhez `/tmp`. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Ha nem tartalmazza a Lead Forward perjelet, a "/" előtagot kell létrehoznia a munkakönyvtárhoz, `/mnt/batch/.../tmp/dataset` például a számítási célra, hogy jelezze, hová szeretné csatlakoztatni az adatkészletet.

### <a name="data-labeling-projects"></a>Adatcímkéző projektek

|Probléma  |Megoldás:  |
|---------|---------|
|Csak a blob-adattárolók használatával létrehozott adatkészletek használhatók     |  Ez az aktuális kiadás ismert korlátozása.       |
|A létrehozást követően a projekt hosszú ideje "inicializálást" mutat.     | Manuálisan frissítse a lapot. Az inicializálásnak másodpercenként körülbelül 20 datapoints kell lennie. Az AutoFrissítés hiánya ismert probléma.         |
|Képek áttekintésekor a rendszer nem jeleníti meg az újonnan címkézett képeket     |   Az összes címkézett kép betöltéséhez válassza az **első** gombot. Az **első** gomb a lista elejére kerül, de az összes címkével ellátott adattal betöltődik.      |
|Az ESC billentyű lenyomásával az objektumok észlelése során a rendszer nulla méretű címkét hoz létre a bal felső sarokban. Az ebben az állapotban lévő címkék elküldése sikertelen.     |   Törölje a címkét a mellette lévő kereszt jelre kattintva.  |

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

Ismert problémák:

* **Hosszú számítási előkészítési idő**: lehet, hogy néhány perc vagy még hosszabb, amikor először csatlakozik vagy létrehoz egy számítási célt. 

## <a name="train-models"></a>Modellek betanítása

* **ModuleErrors (nincs nevű modul)**: Ha a ModuleErrors-ben futtatott kísérleteket az Azure ml-ben, az azt jelenti, hogy a betanítási parancsfájl egy telepítendő csomagot vár, de nincs hozzáadva. A csomag nevének megadása után az Azure ML telepíti a csomagot a betanítási futtatáshoz használt környezetben. 

    Ha a [becslések](concept-azure-machine-learning-architecture.md#estimators) -t használja a kísérletek elküldéséhez, megadhatja a `pip_packages` csomag `conda_packages` nevét a kalkulátoron keresztül vagy paraméterrel, attól függően, hogy melyik forrásból szeretné telepíteni a csomagot. Egy YML-fájlt is megadhat az összes függőségének használatával `conda_dependencies_file`, vagy listázhatja az összes pip-követelményét egy `pip_requirements_file` txt-fájlban a paraméter használatával. Ha rendelkezik saját Azure ML-környezetbeli objektummal, amellyel felül szeretné bírálni a kalkulátor által használt alapértelmezett rendszerképet, megadhatja ezt a környezetet `environment` a kalkulátor konstruktorának paraméterén keresztül.

    Az Azure ML a Tensorflow, a PyTorch, a Chainer és a SKLearn keretrendszer-specifikus becslések is biztosítja. Ezeknek a becslések a használata biztosítja, hogy az alapvető keretrendszer függőségei a betanításhoz használt környezetben legyenek telepítve az Ön nevében. Lehetősége van további függőségek megadására a fentiekben leírtak szerint. 
 
    Az Azure ML által karbantartott Docker-rendszerképek és azok tartalma [AzureML-tárolókban](https://github.com/Azure/AzureML-Containers)láthatók.
    A keretrendszer-specifikus függőségek a megfelelő keretrendszer dokumentációs [láncában](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), a [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), a [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)és a [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)listájában találhatók meg.

    > [!Note]
    > Ha úgy gondolja, hogy egy adott csomag elég gyakori ahhoz, hogy hozzá lehessen adni az Azure ML karbantartott lemezképekhez és környezetekhez, hozzon létre GitHub-problémát a [AzureML-tárolókban](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (név nincs meghatározva), AttributeError (objektum nem rendelkezik attribútummal)**: Ez a kivétel a betanítási szkriptből származik. A naplófájlokat a Azure Portalból tekintheti meg, ha további információt szeretne kapni a nem definiált névvel vagy az attribútum hibával kapcsolatban. Az SDK `run.get_details()` segítségével megtekintheti a hibaüzenetet. Ekkor a rendszer a futtatáshoz létrehozott összes naplófájlt is felsorolja. Győződjön meg arról, hogy megtekinti a betanítási szkriptet, és javítsa ki a hibát, mielőtt elküldené a futtatást. 

* A **Horovod le lett**állítva: a legtöbb esetben, ha "AbortedError: Horovod" állapotba került, akkor ez a kivétel azt jelenti, hogy a Horovod leállítását okozó egyik folyamat egy mögöttes kivételt észlelt. Az MPI-feladatok mindegyik rangsora saját dedikált naplófájlba kerül az Azure ML-ben. Ezek a naplók neve `70_driver_logs`. Elosztott képzés esetén a naplók neve utótaggal van ellátva `_rank` , hogy könnyebben megkülönböztesse a naplókat. A Horovod leállítását okozó pontos hiba megtalálásához folytassa az összes naplófájlt, és keresse meg `Traceback` a driver_log fájlok végén található fájlt. Ezen fájlok egyike megadja a tényleges mögöttes kivételt. 

* A **Run vagy a Experiment művelet törlése**: a kísérletek a [kísérlet. Archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) metódussal vagy a Azure Machine learning Studio-ügyfél kísérlet lapjának az "Archive Experiment" gomb használatával is archiválható. Ez a művelet elrejti a kísérletet a lekérdezések és nézetek listájában, de nem törli azt.

    Az egyes kísérletek vagy futtatások végleges törlése jelenleg nem támogatott. További információ a munkaterület-eszközök törléséről: [Machine learning szolgáltatás-munkaterület adatainak exportálása vagy törlése](how-to-export-delete-data.md).

* A **metrikai dokumentum túl nagy**: a Azure Machine learning belső korláttal rendelkezik azon metrikai objektumok méretén, amelyek bejelentkezhetnek a betanítási futtatás során. Ha a "metrikus dokumentum túl nagy" hibaüzenet jelenik meg egy lista értékű metrika naplózásakor, próbálja meg a lista felosztása kisebb adattömbökre, például:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Belsőleg az Azure ML ugyanazzal a metrikai névvel összefűzi a blokkokat egy összefüggő listához.

## <a name="automated-machine-learning"></a>Automatizált gépi tanulás

* **Tenser flow**: az automatizált gépi tanulás jelenleg nem támogatja a 1,13-es kétsebességű flow-verziót. Ennek a verziónak a telepítése a csomagok függőségeinek leállását eredményezi. Dolgozunk a probléma megoldásán egy későbbi kiadásban.

* **Kísérletezési diagramok**: a bináris besorolású diagramok (precíziós visszahívás, ROC, nyereség görbe stb.) az automatizált ml-kísérletek ismétlései között nem megfelelően jelennek meg a felhasználói felületen a 4/12 óta. A diagram ábrázolása jelenleg inverz eredményeket mutat, ahol a jobb teljesítményű modellek alacsonyabb eredményekkel jelennek meg. Egy megoldás a vizsgálat alatt áll.

* **Automatikus gépi tanulási Databricks megszakítása**: Ha Azure Databrickson automatikus gépi tanulási funkciót használ, a Futtatás megszakításához és az új kísérlet futtatásához indítsa újra a Azure Databricks-fürtöt.

* **Databricks >10 iteráció az automatizált gépi tanuláshoz**: az automatikus gépi tanulási beállításokban, ha több mint 10 iteráció van, akkor állítsa `show_output` `False` be a parancsot a futtatáskor.

* **A Azure Machine learning SDK és az automatizált gépi tanulás Databricks widgetje**: az Azure Machine learning SDK widget nem támogatott a Databricks-jegyzetfüzetekben, mert a jegyzetfüzetek nem tudják elemezni a HTML widgeteket. A widgetet a portálon tekintheti meg a Azure Databricks notebook-cellában található Python-kód használatával:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Modellek üzembe helyezése és kiszolgálása

Tegye a következő hibákat a műveletekhez:

|Hiba  | Megoldás:  |
|---------|---------|
|Rendszerkép-létrehozási hiba a webszolgáltatás telepítésekor     |  A "pynacl = = 1.2.1" hozzáadása pip-függőségként a Conda-fájlhoz a rendszerkép-konfigurációhoz       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   A központi telepítésben használt virtuális gépek SKU-jának módosítása több memóriával. |
|FPGA hiba     |  A modelleket nem fogja tudni telepíteni a FPGA, amíg nem kérelmezi és nem hagyta jóvá a FPGA-kvótát. A hozzáférés kéréséhez töltse ki a kvóta kérése űrlapot:https://aka.ms/aml-real-time-ai       |

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

Ha már nem rendelkezik a TLS/SSL-tanúsítvánnyal és a titkos kulccsal, vagy ha Azure Machine Learning által létrehozott tanúsítványt használ, lekérheti a fájlokat a fürt leválasztása előtt a fürthöz való csatlakozással `kubectl` és a titok `azuremlfessl`beolvasásával.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>A Kubernetes Base-64 kódolású formátumban tárolja a titkokat. Ahhoz, hogy a titkokat el tudja végezni, `cert.pem` a `key.pem` 64-es alapszintű dekódolást `attach_config.enable_ssl`és a titkok összetevőit kell megadnia. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webszolgáltatások az Azure Kubernetes szolgáltatásban – hibák

Az Azure Kubernetes szolgáltatásban számos webszolgáltatási hiba oldható fel a fürthöz való csatlakozással a `kubectl`használatával. Az `kubeconfig.json` Azure Kubernetes Service-fürtöket a következő futtatásával kérheti le:

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
