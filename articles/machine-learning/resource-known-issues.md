---
title: Ismert problémák & hibaelhárítás
titleSuffix: Azure Machine Learning
description: Az Azure Machine Learning ismert problémáinak, kerülő megoldásainak és hibaelhárításának listája.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 2db7a25f3f463e9210544354395c9d33a75f633c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619378"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Ismert problémák és az Azure Machine Learning hibaelhárítása

Ez a cikk segít megtalálni és kijavítani az Azure Machine Learning használata során előforduló hibákat és hibákat.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Néha hasznos lehet, ha diagnosztikai információkat tud megadni, amikor segítséget kér. Néhány napló megtekintése: 
1. Látogasson el [az Azure Machine Learning stúdióba.](https://ml.azure.com) 
1. A bal oldalon válassza a **Kísérlet lehetőséget** 
1. Válasszon ki egy kísérletet.
1. Válasszon egy futtatást.
1. A tetején válassza a **Kimenetek + naplók**lehetőséget.

> [!NOTE]
> Az Azure Machine Learning a betanítás során különböző forrásokból származó információkat naplóz, például az AutoML-t vagy az oktatófeladatot futó Docker-tárolót. A naplók közül sok nincs dokumentálva. Ha problémákat tapasztal, és kapcsolatba lép a Microsoft támogatási szolgálatával, előfordulhat, hogy hibaelhárítás kor használhatják ezeket a naplókat.


## <a name="resource-quotas"></a>Erőforráskvóták

Ismerje meg az Azure Machine Learning használatával kapcsolatos [erőforráskvótákat.](how-to-manage-quotas.md)

## <a name="installation-and-import"></a>Telepítés és importálás

* **Hibaüzenet: A 'PyYAML' nem távolítható el**

    Azure Machine Learning SDK python: PyYAML egy `distutils` telepített projekt. Ezért nem tudjuk pontosan meghatározni, hogy mely fájlok tartoznak hozzá, ha van egy részleges eltávolítás. Ha a hiba figyelmen kívül hagyása mellett folytatni szeretné az SDK telepítését, használja a következőket:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Databricks hiba csomagok telepítésekor**

    Az Azure Machine Learning SDK telepítése sikertelen az Azure Databricks, ha több csomag van telepítve. Egyes csomagok, például `psutil`a , ütközéseket okozhatnak. A telepítési hibák elkerülése érdekében telepítse a csomagokat a könyvtár verziójának fagyasztásával. Ez a probléma a Databricks, és nem az Azure Machine Learning SDK kapcsolatos. Előfordulhat, hogy ezt a problémát más könyvtárakkal is tapasztalja. Példa:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Azt is megteheti, hogy init parancsfájlokat használ, ha a Python-kódtárak telepítési problémáival szembesül. Ez a megközelítés hivatalosan nem támogatott. További információt a [Fürthatókör-init parancsfájlok című témakörben talál.](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)

* **Databricks importálási hiba: nem lehet importálni a "Timedelta" nevet a "pandas._libs.tslibs" fájlból:** Ha ezt a hibát látja az automatikus gépi tanulás használatakor, futtassa a jegyzetfüzet két következő sorát:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks importálási hiba: Nincs "pandas.core.indexes" nevű modul:** Ha ezt a hibát látja az automatizált gépi tanulás használatakor:

    1. Futtassa ezt a parancsot két csomag telepítéséhez az Azure Databricks-fürtben:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Válassza le, majd csatlakoztassa újra a fürtöt a jegyzetfüzethez.
    
    Ha ezek a lépések nem oldják meg a problémát, próbálja meg újraindítani a fürtöt.

* **Databricks FailToSendFeather**: Ha `FailToSendFeather` hibaüzenetet lát az Azure Databricks-fürt adatainak olvasásakor, olvassa el az alábbi megoldásokat:
    
    * Frissítse `azureml-sdk[automl]` a csomagot a legújabb verzióra.
    * Add `azureml-dataprep` 1.1.8-as vagy újabb verzió.
    * Add `pyarrow` változat 0.11 vagy újabb.

## <a name="create-and-manage-workspaces"></a>Munkaterületek létrehozása és kezelése

> [!WARNING]
> Az Azure Machine Learning-munkaterület áthelyezése egy másik előfizetésre, vagy a tulajdonában lévő előfizetés áthelyezése egy új bérlőre, nem támogatott. Ez hibákat okozhat.

* **Azure Portal:** Ha közvetlenül az SDK-ból vagy a portálról származó megosztási hivatkozásból tekinti meg a munkaterületet, nem fogja tudni megtekinteni a normál **áttekintési** lapot az előfizetési információkkal a bővítményben. Másik munkaterületre sem tud váltani. Ha meg kell tekintenie egy másik munkaterületet, közvetlenül az [Azure Machine Learning-stúdióba,](https://ml.azure.com) és keresse meg a munkaterület nevét.

## <a name="set-up-your-environment"></a>A környezet kialakítása

* **AmlCompute létrehozása:** Ritka az esélye, hogy egyes felhasználók, akik létrehozták az Azure Machine Learning-munkaterületet az Azure Portalon, mielőtt a GA-kiadás nem tudja létrehozni amlCompute az adott munkaterületen. A szolgáltatással szemben támogatási kérelmet hívhat fel, vagy létrehozhat egy új munkaterületet a portálon vagy az SDK-n keresztül, hogy azonnal feloldja saját magát.

## <a name="work-with-data"></a>Adatok használata

### <a name="overloaded-azurefile-storage"></a>Túlterhelt AzureFile-tárhely

Ha hibaüzenetet `Unable to upload project files to working directory in AzureFile because the storage is overloaded`kap, alkalmazza a következő kerülő megoldásokat.

Ha más számítási feladatokhoz, például adatátvitelhez használ fájlmegosztást, a javaslat a blobok használata, hogy a fájlmegosztás szabadon használható a futtatások beküldéséhez. A munkaterhelést két különböző munkaterület között is feloszthatja.

### <a name="passing-data-as-input"></a>Adatok átadása bemenetként

*  **TypeError: FileNotFound: Nincs ilyen fájl vagy könyvtár**: Ez a hiba akkor fordul elő, ha a megadott fájlelérési út nem ott található, ahol a fájl található. Győződjön meg arról, hogy a fájlra való hivatkozás módja összhangban van azzal a ponttal, ahová csatlakoztatta az adatkészletet a számítási célhoz. Determinisztikus állapot biztosítása érdekében azt javasoljuk, hogy az absztrakt elérési út használata esetén egy adatkészlet et egy számítási célhoz. A következő kódban például az adatkészletet a számítási cél fájlrendszerének `/tmp`gyökere alá csatlakoztatjuk. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Ha nem adja meg a vezető perjelet, a '/', akkor előtaggal `/mnt/batch/.../tmp/dataset` kell előtagoznia a munkakönyvtárat, például a számítási célon, hogy jelezze, hol szeretné csatlakoztatni az adatkészletet.

### <a name="data-labeling-projects"></a>Adatcímkézési projektek

|Probléma  |Megoldás:  |
|---------|---------|
|Csak a blob adattárakon létrehozott adatkészletek használhatók     |  ez az aktuális kiadás ismert korlátozása.       |
|A létrehozás után a projekt hosszú ideig mutatja az "inicializálást".     | A lap manuális frissítése. Inicializálás kell folytatni a nagyjából 20 adatpont másodpercenként. Az automatikus frissítés hiánya ismert probléma.         |
|Képek ellenőrzésekénként az újonnan címkézett képek nem jelennek meg     |   Az összes címkézett kép betöltéséhez kattintson az **Első** gombra. Az **Első** gomb visszavisz a lista elejére, de betölti az összes címkézett adatot.      |
|Ha az Objektumészlelés címkézése közben lenyomja az Esc billentyűt, a bal felső sarokban nulla méretű címke keletkezik. Ebben az állapotban a címkék küldése sikertelen.     |   Törölje a címkét a mellette lévő keresztjelre kattintva.  |

## <a name="azure-machine-learning-designer"></a>Az Azure Machine Learning tervezője

Ismert problémák:

* **Hosszú számítási előkészítési idő:** Lehet, hogy néhány perc, vagy még hosszabb, amikor először csatlakozik, vagy hozzon létre egy számítási cél. 

## <a name="train-models"></a>Modellek betanítása

* **ModuleErrors (Nincs modul nevű)**: Ha az Azure ML-ben végzett kísérletek elküldése közben a ModuleErrors-ba fut, az azt jelenti, hogy a betanítási parancsfájl egy csomag telepítését várja, de nem adja hozzá. Miután megadja a csomag nevét, az Azure ML telepíti a csomagot a betanítási futtatáshoz használt környezetben. 

    Ha a [Becses eket](concept-azure-machine-learning-architecture.md#estimators) kísérletek küldésére használja, megadhatja a csomag nevét a becslőn keresztül `pip_packages` vagy `conda_packages` paraméterként, amely alapján a csomagot telepíteni szeretné. Megadhatja az yml fájlt is az `conda_dependencies_file`összes függőségével, vagy felsorolhatja `pip_requirements_file` az összes pip követelményt egy txt fájlban a paraméter használatával. Ha saját Azure ML-környezet objektummal rendelkezik, amely felül szeretné bírni a becslést, megadhatja, hogy a környezet a `environment` becsléskontor konstruktor paraméterén keresztül.

    Az Azure ML keretrendszer-specifikus becsléseket is biztosít a Tensorflow, a PyTorch, a Chainer és az SKLearn számára. Ezek a becslések használatával győződjön meg arról, hogy az alapvető keretrendszer-függőségek vannak telepítve az Ön nevében a képzéshez használt környezetben. Lehetősége van további függőségek megadására a fent leírtak szerint. 
 
    Az Azure ML által karbantartott docker-lemezképek és azok tartalma az [AzureML-tárolókban](https://github.com/Azure/AzureML-Containers)látható.
    A keretrendszer-specifikus függőségek a megfelelő keretdokumentációban találhatók : [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Ha úgy gondolja, hogy egy adott csomag elég gyakori ahhoz, hogy az Azure ML karbantartott lemezképeihez és környezeteihez hozzálehessen adni, kérjük, lépjen fel egy GitHub-problémát az [AzureML-tárolókban.](https://github.com/Azure/AzureML-Containers) 
 
* **NameError (Név nincs definiálva), AttributeError (objektum nincs attribútum)**: Ez a kivétel a betanítási parancsfájlok származik. Tekintse meg a naplófájlokat az Azure Portalon, hogy további információt az adott név nem definiált vagy attribútum hiba. Az SDK-ból `run.get_details()` a hibaüzenet et is megnézheti. Ez a futtatáshoz létrehozott összes naplófájl listáját is felsorolja. Kérjük, győződjön meg róla, hogy vessen egy pillantást a betanítási parancsfájlt, és javítsa ki a hibát, mielőtt újra beküldi a futtatást. 

* **Horovod leállt**: A legtöbb esetben, ha találkozik "AbortedError: Horovod leállt" ez a kivétel azt jelenti, hogy volt egy mögöttes kivétel az egyik folyamat, ami miatt Horovod leáll. Az MPI-feladat minden egyes rangja saját dedikált naplófájlt kap az Azure ML-ben. Ezek a naplók `70_driver_logs`neve . Elosztott betanítás esetén a naplónevek et `_rank` a naplók megkülönböztetésének megkönnyítése érdekében a naplók at. Ahhoz, hogy megtalálja a pontos hiba, ami miatt Horovod, `Traceback` hogy állítsa le, menj át az összes naplófájlokat, és keresse meg a végén a driver_log fájlokat. Az egyik ilyen fájl megadja a tényleges mögöttes kivételt. 

* **Futtatás vagy kísérlet törlése:** A kísérletek archiválhatók a [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) metódussal, vagy az Azure Machine Learning studió-ügyfél Kísérlet lapjának nézetében az "Archiválási kísérlet" gombon keresztül. Ez a művelet elrejti a kísérletet a listalekérdezésekből és nézetekből, de nem törli azt.

    Az egyes kísérletek vagy futtatások végleges törlése jelenleg nem támogatott. A Munkaterület-eszközök törléséről a [Machine Learning szolgáltatás munkaterületi adatainak exportálása vagy törlése](how-to-export-delete-data.md)című témakörben talál további információt.

* **Metrikadokumentum túl nagy:** Az Azure Machine Learning belső korlátokat a metrika objektumok, amelyek naplózhatók egyszerre egy betanítási futtatás. Ha "A metrikus dokumentum túl nagy" hibaüzenet jelenik meg egy listaértékű metrika naplózásakor, próbálja meg a listát kisebb adattömbökre osztani, például:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Belsőleg az Azure ML összefűzi az azonos metrikanévmel rendelkező blokkokat egy összefüggő listába.

## <a name="automated-machine-learning"></a>Automatizált gépi tanulás

* **Tenzor-folyamat:** Az automatizált gépi tanulás jelenleg nem támogatja az 1.13-as tensor flow verziót. A verzió telepítése a csomagfüggőségek leállását eredményezi. Dolgozunk, hogy megoldja ezt a problémát egy későbbi kiadásban.

* **Kísérlet diagramok**: Bináris osztályozási diagramok (precíziós visszahívás, ROC, nyereség görbe stb) látható automatizált ML kísérlet iterációk nem teszik megfelelően a felhasználói felület en 4/12 óta. A diagramdiagramok jelenleg inverz eredményeket jelenítenek meg, ahol a jobban teljesítő modellek alacsonyabb eredménnyel jelennek meg. Egy határozat kivizsgálás alatt áll.

* **Databricks megszakítása egy automatikus gépi tanulási futtatás:** Ha az Azure Databricks automatikus gépi tanulási képességeit használja, a futtatás megszakításához és egy új kísérlet futtatásának elindításához indítsa újra az Azure Databricks-fürtöt.

* **Databricks >10 iterációk automatizált gépi tanulás:** Az automatizált gépi tanulási beállítások, `show_output` ha `False` több mint 10 iterációk, állítsa be, amikor elküldi a futtatást.

* **Databricks widget az Azure Machine Learning SDK és az automatizált gépi tanulás:** Az Azure Machine Learning SDK widget nem támogatott a Databricks notebook, mert a jegyzetfüzetek nem tudja elemezni a HTML-widgetek. A portálon lévő widgetet az Azure Databricks-jegyzetfüzetcellában található Python-kód használatával tekintheti meg:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Modellek üzembe helyezése és kiszolgálása

A következő hibák esetén végrehajtja az alábbi műveleteket:

|Hiba  | Megoldás:  |
|---------|---------|
|Lemezkép-építés ihibája a webszolgáltatás üzembe helyezésekor     |  Adja hozzá pip-függőséget pip-függőségként a Conda fájlhoz a lemezkép-konfigurációhoz       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Módosítsa a központi telepítésben használt virtuális gépek termékváltozatát olyanra, amely több memóriával rendelkezik. |
|FPGA hiba     |  Az FPGA-kvótákat csak akkor telepítheti az FPGA-kvótákon, ha kérelmezte és nem hagyta jóvá az FPGA-kvótát. A hozzáférés kéréséhez töltse ki a kvótakérelem űrlapját:https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Az Azure Machine Learning-összetevők frissítése Az AKS-fürtben

Az Azure Kubernetes-szolgáltatásfürtben telepített Azure Machine Learning-összetevők frissítéseit manuálisan kell alkalmazni. 

Ezeket a frissítéseket úgy alkalmazhatja, hogy leválasztja a fürtöt az Azure Machine Learning-munkaterületről, majd újra csatlakoztatja a fürtöt a munkaterülethez. Ha a TLS engedélyezve van a fürtben, a fürt újbóli csatlakoztatásakor meg kell adnia a TLS/SSL tanúsítványt és a személyes kulcsot. 

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

Ha már nem rendelkezik a TLS/SSL-tanúsítvánnyal és a személyes kulccsal, vagy az Azure Machine Learning által létrehozott `kubectl` tanúsítványt használ, `azuremlfessl`a fürt leválasztása előtt lekérheti a fájlokat a fürthöz való csatlakozással a titkos kulcs használatával és beolvasásával.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>A Kubernetes a titkokat base-64 kódolású formátumban tárolja. Meg kell alap-64 dekódolni a `cert.pem` és `key.pem` összetevői a titkok `attach_config.enable_ssl`at. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webszolgáltatások az Azure Kubernetes szolgáltatás hibáiban

Az Azure Kubernetes-szolgáltatás számos webszolgáltatási hibája hibakeresést okozhat `kubectl`a fürthöz való csatlakozással. Az `kubeconfig.json` Azure Kubernetes szolgáltatásfürt höz a futó

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Hitelesítési hibák

Ha egy távoli feladatból végez egy számítási célon egy felügyeleti műveletet, a következő hibák valamelyike jelenik meg:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Például hibaüzenetet kap, ha megpróbál létrehozni vagy csatolni egy számítási célt egy távoli végrehajtásra elküldött ml-folyamatból.
