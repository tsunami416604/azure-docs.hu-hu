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
ms.date: 11/04/2019
ms.openlocfilehash: d5525c02edb30eff0ee8971a382f2acb8f2e57ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455723"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Ismert problémák és az Azure Machine Learning hibaelhárítása

Ez a cikk segít megtalálni és kijavítani az Azure Machine Learning használata során észlelt hibákat és hibákat.

## <a name="sdk-installation-issues"></a>SDK telepítési problémák

**Hibaüzenet: A 'PyYAML' nem távolítható el**

Azure Machine Learning SDK python: PyYAML egy distutils telepített projekt. Ezért nem tudjuk pontosan meghatározni, hogy mely fájlok tartoznak hozzá, ha van egy részleges eltávolítás. Ha a hiba figyelmen kívül hagyása mellett folytatni szeretné az SDK telepítését, használja a következőket:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Hibaüzenet:`ERROR: No matching distribution found for azureml-dataprep-native`**

Az Anaconda Python 3.7.4-es disztribúciója hibát jelent, amely megszakítja az azureml-sdk telepítését. Ezt a problémát ez a [GitHub-probléma](https://github.com/ContinuumIO/anaconda-issues/issues/11195) tárgyalja: Ez egy új Conda környezet létrehozásával megoldható ezzel a paranccsal:
```bash
conda create -n <env-name> python=3.7.3
```
Amely létrehoz egy Conda környezetet a Python 3.7.3 használatával, amely nem rendelkezik a 3.7.4-es telepítési problémával.

## <a name="training-and-experimentation-issues"></a>Képzési és kísérletezési problémák

### <a name="metric-document-is-too-large"></a>A metrikus dokumentum túl nagy
Az Azure Machine Learning belső korlátokat a metrika objektumok, amelyek egy betanítási futtatás egyszerre naplózható mérete. Ha "A metrikus dokumentum túl nagy" hibaüzenet jelenik meg egy listaértékű metrika naplózásakor, próbálja meg a listát kisebb adattömbökre osztani, például:

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

Belsőleg az Azure ML összefűzi az azonos metrikanévmel rendelkező blokkokat egy összefüggő listába.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (Nincs elnevezett modul)
Ha az Azure ML-ben végzett kísérletek elküldése közben a ModuleErrors-ba fut be, az azt jelenti, hogy a betanítási parancsfájl egy csomag telepítését várja, de nem adja hozzá. Miután megadja a csomag nevét, az Azure ML telepíti a csomagot a betanítási futtatáshoz használt környezetben. 

Ha a [Becses eket](concept-azure-machine-learning-architecture.md#estimators) kísérletek küldésére használja, megadhatja a csomag nevét a becslőn keresztül `pip_packages` vagy `conda_packages` paraméterként, amely alapján a csomagot telepíteni szeretné. Megadhatja az yml fájlt is az `conda_dependencies_file`összes függőségével, vagy felsorolhatja `pip_requirements_file` az összes pip követelményt egy txt fájlban a paraméter használatával. Ha saját Azure ML-környezet objektummal rendelkezik, amely felül szeretné bírni a becslést, megadhatja, hogy a környezet a `environment` becsléskontor konstruktor paraméterén keresztül.

Az Azure ML keretrendszer-specifikus becsléseket is biztosít a Tensorflow, a PyTorch, a Chainer és az SKLearn számára. Ezek a becslések használatával győződjön meg arról, hogy az alapvető keretrendszer-függőségek vannak telepítve az Ön nevében a képzéshez használt környezetben. Lehetősége van további függőségek megadására a fent leírtak szerint. 
 
Az Azure ML által karbantartott docker-lemezképek és azok tartalma az [AzureML-tárolókban](https://github.com/Azure/AzureML-Containers)látható.
A keretrendszer-specifikus függőségek a megfelelő keretdokumentációban találhatók : [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

> [!Note]
> Ha úgy gondolja, hogy egy adott csomag elég gyakori ahhoz, hogy az Azure ML karbantartott lemezképeihez és környezeteihez hozzálehessen adni, kérjük, lépjen fel egy GitHub-problémát az [AzureML-tárolókban.](https://github.com/Azure/AzureML-Containers) 
 
### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (a név nincs definiálva), Attribútumhiba (az objektumnak nincs attribútuma)
Ez a kivétel a betanítási parancsfájlok származik. Tekintse meg a naplófájlokat az Azure Portalon, hogy további információt az adott név nem definiált vagy attribútum hiba. Az SDK-ból `run.get_details()` a hibaüzenet et is megnézheti. Ez a futtatáshoz létrehozott összes naplófájl listáját is felsorolja. Kérjük, győződjön meg róla, hogy vessen egy pillantást a betanítási parancsfájlt, és javítsa ki a hibát, mielőtt újra beküldi a futtatást. 

### <a name="horovod-has-been-shut-down"></a>Horovod-ot leállították.
A legtöbb esetben, ha "AbortedError: Horovod leállt" ez a kivétel azt jelenti, hogy volt egy mögöttes kivétel az egyik folyamat, ami miatt Horovod leáll. Az MPI-feladat minden egyes rangja saját dedikált naplófájlt kap az Azure ML-ben. Ezek a naplók `70_driver_logs`neve . Elosztott betanítás esetén a naplónevek et `_rank` a naplók megkülönböztetésének megkönnyítése érdekében a naplók at. Ahhoz, hogy megtalálja a pontos hiba, ami miatt Horovod, `Traceback` hogy állítsa le, menj át az összes naplófájlokat, és keresse meg a végén a driver_log fájlokat. Az egyik ilyen fájl megadja a tényleges mögöttes kivételt. 

### <a name="sr-iov-availability-on-ncv3-machines-in-amlcompute-for-distributed-training"></a>Az SR-IOV rendelkezésre állása az AmlCompute NCv3 gépein az elosztott képzéshez
Az Azure Compute az [NCv3-gépek SR-IOV frissítését](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku/) vezette be, amelyet az ügyfelek az Azure ML felügyelt számítási ajánlatával (AmlCompute) használhatnak ki. A frissítések lehetővé teszik a teljes MPI verem támogatását és az Infiniband RDMA hálózat használatát a többcsomópontos elosztott képzési teljesítmény javítása érdekében, különösen a mély tanulás érdekében.

Tekintse meg a [frissítési ütemezést,](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku/) hogy megtudja, mikor lesz elérhető a támogatás a régióban.

### <a name="run-or-experiment-deletion"></a>Futtatás vagy kísérlet törlése
A kísérletek archiválhatók a [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) metódus használatával, vagy az Azure Machine Learning studió-ügyfél Kísérlet lapján az "Archív kísérlet" gombon keresztül. Ez a művelet elrejti a kísérletet a listalekérdezésekből és nézetekből, de nem törli azt.

Az egyes kísérletek vagy futtatások végleges törlése jelenleg nem támogatott. A Munkaterület-eszközök törléséről a [Machine Learning szolgáltatás munkaterületi adatainak exportálása vagy törlése](how-to-export-delete-data.md)című témakörben talál további információt.

## <a name="azure-machine-learning-compute-issues"></a>Az Azure Machine Learning compute problémái
Ismert problémák az Azure Machine Learning Compute (AmlCompute) használatával kapcsolatban.

### <a name="trouble-creating-amlcompute"></a>Probléma az AmlCompute létrehozásakor

Ritka az esély, hogy egyes felhasználók, akik létrehozták az Azure Machine Learning-munkaterületet az Azure Portalon, mielőtt a ga-kiadás nem tudja létrehozni az AmlCompute-t az adott munkaterületen. A szolgáltatással szemben támogatási kérelmet hívhat fel, vagy létrehozhat egy új munkaterületet a portálon vagy az SDK-n keresztül, hogy azonnal feloldja saját magát.

### <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Kimaradás: Az SR-IOV frissítése NCv3 gépekre az AmlCompute-ban

Az Azure Compute 2019 november elején frissíti az NCv3 ska-kat, hogy támogassa az összes MPI-implementációt és -verziót, valamint az RDMA-műveleteket az InfiniBand-tel felszerelt virtuális gépekhez. Ez rövid állásidőt igényel - [további információ az SR-IOV frissítésről](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Az Azure Machine Learning felügyelt számítási ajánlatának (AmlCompute) ügyfeleként jelenleg nem kell módosításokat végrehajtania. A [frissítési ütemezés](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) alapján meg kell terveznie egy rövid szünetet a képzésben. A szolgáltatás vállalja a felelősséget a virtuális gép lemezképeinek frissítésére a fürtcsomópontokon, és automatikusan felskálázhatja a fürtöt. A frissítés befejezése után az összes többi MPI disztribúciót (például az OpenMPI-t a Pytorch-val) is használhatja, amellett, hogy nagyobb InfiniBand sávszélességet, alacsonyabb késleltetéseket és jobb elosztott alkalmazásteljesítményt kap.

## <a name="azure-machine-learning-designer-issues"></a>Az Azure Machine Learning tervezői problémái

Ismert problémák a tervezővel.

### <a name="long-compute-preparation-time"></a>Hosszú számítási előkészítési idő

Hozzon létre új számítási vagy idézi így számítási időt vesz igénybe, lehet, hogy néhány perc, vagy még hosszabb. A csapat az optimalizáláson dolgozik.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>Kísérlet nem futtatható, csak adatkészletet tartalmaz 

Előfordulhat, hogy csak adatkészletet szeretne futtatni az adatkészlet megjelenítéséhez. Azonban nem szabad futtatni egy kísérletet csak adatkészletet tartalmaz ma. Aktívan megoldjuk ezt a problémát.
 
A javítás előtt csatlakoztathatja az adatkészletet bármely adatátalakítási modulhoz (Oszlopok kijelölése az adatkészletben, Metaadatok szerkesztése, Adatok felosztása stb.), és futtathatja a kísérletet. Ezután megjelenítheti az adatkészletet. 

Az alábbi képen ![látható, hogyan: visulize-adatok](./media/resource-known-issues/aml-visualize-data.png)

## <a name="image-building-failure"></a>Képépítés ihibája

Lemezkép-építés i hibája a webszolgáltatás üzembe helyezésekor. A megoldás az, hogy a "pynacl==1.2.1" pip-függőséget adja hozzá a Conda fájlhoz a lemezkép konfigurációjához.

## <a name="deployment-failure"></a>Telepítési hiba

Ha megfigyeli, `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`módosítsa a központi telepítésben használt virtuális gépek termékváltozatát olyanra, amely több memóriával rendelkezik.

## <a name="fpgas"></a>FPGA-k

Az FPGA-kvótákat csak akkor telepítheti az FPGA-kvótákon, ha kérelmezte és nem hagyta jóvá az FPGA-kvótát. A hozzáférés kéréséhez töltse ki a kvótakérelem űrlapját:https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatizált gépi tanulás

Tensor Flow Automatikus gépi tanulás jelenleg nem támogatja a tensor flow 1.13-as verzióját. A verzió telepítése a csomagfüggőségek leállását eredményezi. Dolgozunk, hogy megoldja ezt a problémát egy későbbi kiadásban.

### <a name="experiment-charts"></a>Kísérleti diagramok

Az automatizált gépelési kísérlet ismétlésekben látható bináris osztályozási diagramok (precíziós visszahívás, ROC, erősítési görbe stb.) a 4/12 óta nem megfelelően jelennek meg a felhasználói felületen. A diagramdiagramok jelenleg inverz eredményeket jelenítenek meg, ahol a jobban teljesítő modellek alacsonyabb eredménnyel jelennek meg. Egy határozat kivizsgálás alatt áll.

## <a name="datasets-and-data-preparation"></a>Adatkészletek és adatelőkészítés

Ezek az Azure Machine Learning-adatkészletek ismert problémái.

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>TypeError: FileNotFound: Nincs ilyen fájl vagy könyvtár

Ez a hiba akkor fordul elő, ha a megadott fájlelérési út nem a fájl helye. Győződjön meg arról, hogy a fájlra való hivatkozás módja összhangban van azzal a ponttal, ahová csatlakoztatta az adatkészletet a számítási célhoz. Determinisztikus állapot biztosítása érdekében azt javasoljuk, hogy az absztrakt elérési út használata esetén egy adatkészlet et egy számítási célhoz. A következő kódban például az adatkészletet a számítási cél fájlrendszerének `/tmp`gyökere alá csatlakoztatjuk. 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

Ha nem adja meg a vezető perjelet, a '/', akkor előtaggal `/mnt/batch/.../tmp/dataset` kell előtagoznia a munkakönyvtárat, például a számítási célon, hogy jelezze, hol szeretné csatlakoztatni az adatkészletet.

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Nem olvasható a Parquet fájl HTTP-ről vagy ADLS Gen 2-ről

Az AzureML DataPrep SDK 1.1.25-ös verziójában ismert probléma merül fel, amely hibát okoz az adatkészlet létrehozásakor, ha http-ről vagy ADLS Gen 2-ről olvassa a Parquet-fájlokat. Ez nem `Cannot seek once reading started.`fog sikerülni . A probléma megoldásához `azureml-dataprep` frissítsen 1.1.26-nál magasabb verzióra, vagy váltson vissza 1.1.24-nél alacsonyabb verzióra.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: mount() kapott egy váratlan kulcsszó argumentum "invocation_id"

Ez a hiba akkor fordul elő, `azureml-core` `azureml-dataprep`ha a és a között nem kompatibilis verzió található. Ha ez a hiba `azureml-dataprep` jelenik meg, frissítse a csomagot egy újabb verzióra (1.1.29-nél nagyobb vagy azzal egyenlő).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Databricks és az Azure Machine Learning problémák.

### <a name="failure-when-installing-packages"></a>Hiba a csomagok telepítésekor

Az Azure Machine Learning SDK telepítése sikertelen az Azure Databricks, ha több csomag van telepítve. Egyes csomagok, például `psutil`a , ütközéseket okozhatnak. A telepítési hibák elkerülése érdekében telepítse a csomagokat a könyvtár verziójának fagyasztásával. Ez a probléma a Databricks, és nem az Azure Machine Learning SDK kapcsolatos. Előfordulhat, hogy ezt a problémát más könyvtárakkal is tapasztalja. Példa:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Azt is megteheti, hogy init parancsfájlokat használ, ha a Python-kódtárak telepítési problémáival szembesül. Ez a megközelítés hivatalosan nem támogatott. További információt a [Fürthatókör-init parancsfájlok című témakörben talál.](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)

### <a name="cancel-an-automated-machine-learning-run"></a>Automatikus gépi tanulási futtatás megszakítása

Ha automatikus gépi tanulási képességeket használ az Azure Databricks-en, hogy megszakítson egy futtatást, és új kísérletfuttatást indítson el, indítsa újra az Azure Databricks-fürtöt.

### <a name="10-iterations-for-automated-machine-learning"></a>>10 iteráció az automatizált gépi tanuláshoz

Az automatikus gépi tanulási beállítások, ha több mint 10 iterációk, állítsa be, `show_output` `False` amikor elküldi a futtatást.

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Az Azure Machine Learning SDK és az automatizált gépi tanulás widgetje

Az Azure Machine Learning SDK widget nem támogatott a Databricks notebook, mert a jegyzetfüzetek nem elemezheti a HTML-widgetek. A portálon lévő widgetet az Azure Databricks-jegyzetfüzetcellában található Python-kód használatával tekintheti meg:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-cannot-import-name-timedelta-from-pandas_libstslibs"></a>Importálási hiba: nem lehet importálni a "Timedelta" nevet a "pandas._libs.tslibs" fájlból.

Ha ez a hiba automatikus gépi tanulás használatakor jelenik meg, futtassa a jegyzetfüzet két következő sorát:
```
%sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
%sh /databricks/python/bin/pip install pandas==0.23.4
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Importálási hiba: Nincs "pandas.core.indexes" nevű modul

Ha ezt a hibát látja az automatikus gépi tanulás használatakor:

1. Futtassa ezt a parancsot két csomag telepítéséhez az Azure Databricks-fürtben:

   ```bash
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Válassza le, majd csatlakoztassa újra a fürtöt a jegyzetfüzethez.

Ha ezek a lépések nem oldják meg a problémát, próbálja meg újraindítani a fürtöt.

### <a name="failtosendfeather"></a>FailToSendFeather

Ha hibaüzenetet `FailToSendFeather` lát az Azure Databricks-fürt adatainak olvasásakor, olvassa el az alábbi megoldásokat:

* Frissítse `azureml-sdk[automl]` a csomagot a legújabb verzióra.
* Add `azureml-dataprep` 1.1.8-as vagy újabb verzió.
* Add `pyarrow` változat 0.11 vagy újabb.

## <a name="azure-portal"></a>Azure portál

Ha közvetlenül az SDK-ból vagy a portálról származó megosztási hivatkozásból tekinti meg a munkaterületet, akkor nem fogja tudni megtekinteni a bővítményben lévő előfizetési információkat tartalmazó normál áttekintési lapot. Másik munkaterületre sem tud váltani. Ha meg kell tekintenie egy másik munkaterületet, a megoldás az, hogy közvetlenül az [Azure Machine Learning-stúdióba,](https://ml.azure.com) és keresse meg a munkaterület nevét.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Néha hasznos lehet, ha diagnosztikai információkat tud megadni, amikor segítséget kér. Néhány napló megtekintéséhez keresse fel az [Azure Machine Learning stúdiót,](https://ml.azure.com) nyissa meg a munkaterületet, és válassza a Munkaterület > A kísérlet > > **naplók futtatása**lehetőséget.  

> [!NOTE]
> Az Azure Machine Learning a betanítás során különböző forrásokból származó információkat naplóz, például az AutoML-t vagy az oktatófeladatot futó Docker-tárolót. A naplók közül sok nincs dokumentálva. Ha problémákat tapasztal, és kapcsolatba lép a Microsoft támogatási szolgálatával, előfordulhat, hogy hibaelhárítás kor használhatják ezeket a naplókat.

## <a name="activity-logs"></a>Tevékenységnaplók

Az Azure Machine Learning-munkaterületen belüli egyes műveletek nem naplózzák az adatokat a __tevékenységnaplóba.__ Például egy betanítási futtatás indítása vagy egy modell regisztrálása.

Ezen műveletek némelyike a munkaterület __Tevékenységek__ területén jelenik meg, de nem jelzik, hogy ki kezdeményezte a tevékenységet.

## <a name="resource-quotas"></a>Erőforráskvóták

Ismerje meg az Azure Machine Learning használatával kapcsolatos [erőforráskvótákat.](how-to-manage-quotas.md)

## <a name="authentication-errors"></a>Hitelesítési hibák

Ha egy távoli feladatból végez egy számítási célon egy felügyeleti műveletet, a következő hibák valamelyike jelenik meg:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Például hibaüzenetet kap, ha megpróbál létrehozni vagy csatolni egy számítási célt egy távoli végrehajtásra elküldött ml-folyamatból.

## <a name="overloaded-azurefile-storage"></a>Túlterhelt AzureFile-tárhely

Ha hibaüzenetet `Unable to upload project files to working directory in AzureFile because the storage is overloaded`kap, alkalmazza a következő kerülő megoldásokat.

Ha más számítási feladatokhoz, például adatátvitelhez használ fájlmegosztást, a javaslat a blobok használata, hogy a fájlmegosztás szabadon használható a futtatások beküldéséhez. A munkaterhelést két különböző munkaterület között is feloszthatja.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Webszolgáltatások az Azure Kubernetes szolgáltatás hibáiban

Az Azure Kubernetes-szolgáltatás számos webszolgáltatási hibája hibakeresést okozhat `kubectl`a fürthöz való csatlakozással. Az `kubeconfig.json` Azure Kubernetes szolgáltatásfürt höz a futó

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Az Azure Machine Learning-összetevők frissítése Az AKS-fürtben

Az Azure Kubernetes-szolgáltatásfürtben telepített Azure Machine Learning-összetevők frissítéseit manuálisan kell alkalmazni. 

Ezeket a frissítéseket úgy alkalmazhatja, hogy leválasztja a fürtöt az Azure Machine Learning-munkaterületről, majd újra csatlakoztatja a fürtöt a munkaterülethez. Ha az SSL engedélyezve van a fürtben, a fürt újbóli csatlakoztatásakor meg kell adnia az SSL-tanúsítványt és a személyes kulcsot. 

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

Ha már nem rendelkezik az SSL-tanúsítvánnyal és a személyes kulccsal, vagy az Azure Machine Learning által létrehozott `kubectl` tanúsítványt használ, `azuremlfessl`a fürt leválasztása előtt lekérheti a fájlokat a fürthöz való csatlakozással és a titkos kulcs beolvasásával.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>A Kubernetes a titkokat base-64 kódolású formátumban tárolja. Meg kell alap-64 dekódolni a `cert.pem` és `key.pem` összetevői a titkok `attach_config.enable_ssl`at. 

## <a name="labeling-projects-issues"></a>Projektekkel kapcsolatos problémák címkézése

Ismert problémák a címkézési projektekkel.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Csak a blob adattárakon létrehozott adatkészletek használhatók

Ez az aktuális kiadás ismert korlátozása.

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>A létrehozás után a projekt hosszú ideig mutatja az "inicializálást".

A lap manuális frissítése. Inicializálás kell folytatni a nagyjából 20 adatpont másodpercenként. Az automatikus frissítés hiánya ismert probléma. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>Képek ellenőrzésekénként az újonnan címkézett képek nem jelennek meg

Az összes címkézett kép betöltéséhez kattintson az **Első** gombra. Az **Első** gomb visszavisz a lista elejére, de betölti az összes címkézett adatot.

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>Ha az Objektumészlelés címkézése közben lenyomja az Esc billentyűt, a bal felső sarokban nulla méretű címke keletkezik. Ebben az állapotban a címkék küldése sikertelen.

Törölje a címkét a mellette lévő keresztjelre kattintva.

## <a name="moving-the-workspace"></a>A munkaterület áthelyezése

> [!WARNING]
> Az Azure Machine Learning-munkaterület áthelyezése egy másik előfizetésre, vagy a tulajdonában lévő előfizetés áthelyezése egy új bérlőre, nem támogatott. Ez hibákat okozhat.
