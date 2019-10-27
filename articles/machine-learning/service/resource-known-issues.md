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
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 0dd0b8cf39da8039b3a59bf243284e0d5062bd78
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965595"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Ismert problémák és hibaelhárítási Azure Machine Learning

Ez a cikk segít megkeresni és kijavítani a Azure Machine Learning használatakor észlelt hibákat és hibákat.

## <a name="upcoming-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Közelgő SR-IOV frissítés NCv3-gépekre a AmlCompute-ben

Az Azure számítási szolgáltatás a november elején kezdődő NCv3 SKU-ket fogja frissíteni az összes MPI-implementáció és-verzió támogatásához, valamint a InfiniBand-alapú virtuális gépek RDMA-műveleteinek elvégzéséhez. Ehhez rövid állásidőre lesz szükség – [További információ az SR-IOV frissítéséről](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Azure Machine Learning felügyelt számítási ajánlatának (AmlCompute) ügyfeleként jelenleg nem szükséges módosítania a módosításokat. A [frissítési ütemterv](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) alapján meg kell terveznie a betanítást rövid idő alatt. A szolgáltatás a fürt csomópontjain lévő virtuálisgép-rendszerképek frissítését és a fürt automatikus skálázását végzi. Ha a frissítés befejeződik, használhatja az összes többi MPI-discibutions (például a OpenMPI és a Pytorch-t) a nagyobb InfiniBand-sávszélesség, az alacsonyabb késés és a jobb elosztott alkalmazások teljesítményének növelése mellett.

## <a name="visual-interface-issues"></a>Vizuális felülettel kapcsolatos problémák

Vizuális felület a Machine learning szolgáltatással kapcsolatos problémákhoz.

### <a name="long-compute-preparation-time"></a>Hosszú számítási előkészítési idő

Hozzon létre új számítást, vagy idézzen elő számítási időt, akár néhány percet is igénybe vehet. A csapat az optimalizáláshoz dolgozik.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Nem lehet futtatni egy kísérletet, amely csak adatkészletet tartalmaz 

Előfordulhat, hogy egy kísérletet csak olyan adatkészletet szeretne futtatni, amely megjeleníti az adatkészletet. Azonban a kísérletek futtatása nem engedélyezett, csak az adatkészletet tartalmazza. Aktívan kijavítottuk ezt a problémát.
 
A javítás előtt összekapcsolhatjuk az adatkészletet bármely Adatátalakítási modulhoz (oszlop kijelölése az adatkészletben, a metaadatok szerkesztése, adatok felosztása stb.), és futtathatja a kísérletet. Ezután megjelenítheti az adatkészletet. 

Az alábbi képen látható, hogyan: ![visulize](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>SDK-telepítési problémák

**Hibaüzenet: nem távolítható el a (z) PyYAML**

Pythonhoz készült Azure Machine Learning SDK: a PyYAML egy distutils telepített projekt. Ezért nem tudjuk pontosan meghatározni, hogy mely fájlok tartoznak hozzá, ha részleges eltávolítás van. Ha továbbra is szeretné telepíteni az SDK-t a hiba figyelmen kívül hagyásával, használja a következőt:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Hibaüzenet: `ERROR: No matching distribution found for azureml-dataprep-native`**

A anaconda Python 3.7.4-eloszlása olyan hibát tartalmaz, amely megszakítja a azureml-SDK telepítését. Ezt a problémát ebben a [GitHub-probléma](https://github.com/ContinuumIO/anaconda-issues/issues/11195) tárgyalja: ezt a parancsot a következő paranccsal hozhatja létre egy új Conda-környezet használatával:
```bash
conda create -n <env-name> python=3.7.3
```
Amely a Python 3.7.3 használatával hoz létre egy Conda-környezetet, amely nem rendelkezik a 3.7.4-ben lévő telepítési hibával.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Hiba történt Azure Machine Learning számítás létrehozásakor

Ritkán fordul elő, hogy egyes felhasználók, akik a Azure Machine Learning munkaterületet hoztak létre a Azure Portal a GA kiadása előtt, előfordulhat, hogy az adott munkaterületen nem tud létrehozni Azure Machine Learning számítást. Felvehet egy támogatási kérést a szolgáltatásra, vagy létrehozhat egy új munkaterületet a portálon vagy az SDK-ban, hogy azonnal feloldja a zárolást.

## <a name="image-building-failure"></a>Rendszerkép-létrehozási hiba

Rendszerkép-létrehozási hiba a webszolgáltatás telepítésekor. Áthidaló megoldás: "pynacl = = 1.2.1" hozzáadása pip-függőségként a Conda-fájlhoz a rendszerkép-konfigurációhoz.

## <a name="deployment-failure"></a>Üzembe helyezési hiba

Ha betartja `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, módosítsa az üzemelő példányban használt virtuális gépek SKU-jának méretét, amely több memóriával rendelkezik.

## <a name="fpgas"></a>FPGA-k

A modelleket nem fogja tudni telepíteni a FPGA, amíg nem kérelmezi és nem hagyta jóvá a FPGA-kvótát. A hozzáférés kéréséhez töltse ki a kvóta kérése űrlapot: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatizált gépi tanulás

A tenser flow automatikus gépi tanulása jelenleg nem támogatja a 1,13-es kétsebességű flow-verziót. Ennek a verziónak a telepítése a csomagok függőségeinek leállását eredményezi. Dolgozunk a probléma megoldásán egy későbbi kiadásban. 

### <a name="experiment-charts"></a>Kísérleti diagramok

A bináris besorolási diagramok (a pontosság-visszahívás, a ROC, a nyereség görbéje stb.) az automatizált ML-kísérletek iterációjában nem megfelelően jelennek meg a felhasználói felületen a 4/12 óta. A diagram ábrázolása jelenleg inverz eredményeket mutat, ahol a jobb teljesítményű modellek alacsonyabb eredményekkel jelennek meg. Egy megoldás a vizsgálat alatt áll.

## <a name="datasets-and-data-preparation"></a>Adatkészletek és adat-előkészítés

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Nem sikerült beolvasni a Parquet-fájlt a HTTP vagy a ADLS Gen 2 használatával

Létezik egy ismert probléma a AzureML Adatelőkészítés SDK verziójának 1.1.25, amely meghibásodást okoz az adatkészlet létrehozásakor, ha a HTTP vagy a ADLS Gen 2 fájlból olvassa be a parketta-fájlokat. A probléma megoldásához frissítsen a 1.1.26-nál magasabb verzióra, vagy a 1.1.24-nél alacsonyabb verzióra.

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Databricks és Azure Machine Learning problémák.

### <a name="failure-when-installing-packages"></a>Hiba a csomagok telepítésekor

Azure Machine Learning SDK telepítése sikertelen Azure Databricks Ha további csomagok vannak telepítve. Bizonyos csomagok, például a `psutil`, ütközéseket okozhatnak. A telepítési hibák elkerülése érdekében telepítse a csomagokat a könyvtár verziószámának lefagyasztásával. Ez a probléma a Databricks és nem a Azure Machine Learning SDK-val kapcsolatos. Előfordulhat, hogy ezt a problémát más kódtárak is megtapasztalják. Példa:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Azt is megteheti, hogy init-parancsfájlokat használ, ha a Python-kódtárakkal együtt tartja a telepítési problémákat. Ez a megközelítés nem támogatott hivatalosan. További információ: [fürtökre kiterjedő init-parancsfájlok](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Gépi tanulás automatikus futtatásának megszakítása

Ha Azure Databrickson automatikus gépi tanulási képességeket használ a Futtatás megszakításához és az új kísérlet futtatásának elindításához, indítsa újra a Azure Databricks-fürtöt.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iteráció az automatizált gépi tanuláshoz

Ha több mint 10 iterációja van, az automatikus gépi tanulás beállításainál állítsa a `show_output` `False`re a Futtatás elküldésekor.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget a Azure Machine Learning SDK/automatikus gépi tanuláshoz

A Azure Machine Learning SDK widget nem támogatott Databricks-jegyzetfüzetekben, mert a jegyzetfüzetek nem tudják elemezni a HTML widgeteket. A widgetet a portálon tekintheti meg a Azure Databricks notebook-cellában található Python-kód használatával:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Importálási hiba: nincs "pandák. Core. indexek" nevű modul.

Ha ezt a hibaüzenetet látja, amikor automatikus gépi tanulást használ:

1. Futtassa ezt a parancsot két csomag telepítéséhez a Azure Databricks-fürtön: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Válassza le, majd csatlakoztassa újra a fürtöt a jegyzetfüzethez. 

Ha ezek a lépések nem oldják meg a problémát, próbálja meg újraindítani a fürtöt.

### <a name="failtosendfeather"></a>FailToSendFeather

Ha Azure Databricks fürtön lévő adatolvasáskor `FailToSendFeather` hibaüzenet jelenik meg, tekintse meg a következő megoldásokat:

* `azureml-sdk[automl]` csomag frissítése a legújabb verzióra.
* Adja hozzá a `azure-dataprep` 1.1.8 vagy újabb verziót.
* Adja hozzá `pyarrow` 0,11-es vagy újabb verziót.

## <a name="azure-portal"></a>Azure Portal

Ha közvetlenül a munkaterületet tekinti meg az SDK-ból vagy a portálról, akkor nem fogja tudni megtekinteni a normál áttekintő oldalt a bővítmény előfizetési adataival. Nem válthat másik munkaterületre is. Ha meg kell tekintenie egy másik munkaterületet, a megkerülő megoldással közvetlenül a [Azure Portal](https://portal.azure.com) léphet, és megkeresheti a munkaterület nevét.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Esetenként hasznos lehet, ha a Segítség kérése során diagnosztikai adatokat is megadhat. Ha meg szeretne tekinteni néhány naplót, látogasson el [Azure Portal](https://portal.azure.com) és lépjen a munkaterületre, és válassza ki a munkaterületet **> a kísérlet > > naplók futtatása**  Ezeket az információkat a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)megjelenő **kísérletek** szakaszban tekintheti meg.

> [!NOTE]
> Azure Machine Learning a különböző forrásokból származó információkat naplózza a betanítás során, például a AutoML vagy a betanítási feladatot futtató Docker-tárolóban. A naplók közül sok nincs dokumentálva. Ha problémákat tapasztal, és felveszi a kapcsolatot a Microsoft ügyfélszolgálatával, előfordulhat, hogy a hibaelhárítás során ezeket a naplókat is használni tudja.

## <a name="activity-logs"></a>Tevékenységnaplók

A Azure Machine Learning munkaterület egyes műveletei nem naplózzák az adatokat a __tevékenység naplójába__. Például egy képzési folyamat elindítása vagy egy modell regisztrálása.

A műveletek némelyike a munkaterület __tevékenységek__ területén jelenik meg, de nem jelzi, hogy ki kezdeményezte a tevékenységet.

## <a name="resource-quotas"></a>Erőforráskvóták

Ismerje meg, hogy milyen [erőforrás-kvóták](how-to-manage-quotas.md) merülhetnek fel a Azure Machine learning használatakor.

## <a name="authentication-errors"></a>Hitelesítési hibák

Ha egy távoli feladatból származó számítási célra hajt végre felügyeleti műveletet, a következő hibák valamelyikét fogja kapni:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Ha például megpróbál létrehozni vagy csatolni egy számítási célt egy olyan ML-folyamatból, amely távoli végrehajtásra van elküldve, a rendszer hibaüzenetet küld.

## <a name="overloaded-azurefile-storage"></a>Túlterhelt AzureFile-tároló

Ha `Unable to upload project files to working directory in AzureFile because the storage is overloaded` hibaüzenetet kap, alkalmazza a következő megkerülő megoldásokat.

Ha más számítási feladatokhoz (például adatátvitelhez) használ fájlmegosztást, a javasolt a Blobok használata, hogy a fájlmegosztás díjmentesen használható legyen a futtatások elküldéséhez. A számítási feladatok felosztása két különböző munkaterület között is lehetséges.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Webszolgáltatások az Azure Kubernetes szolgáltatásban – hibák 

Az Azure Kubernetes szolgáltatásban számos webszolgáltatási hiba feloldható a fürthöz való csatlakozással `kubectl` használatával. Az Azure Kubernetes Service-fürthöz tartozó `kubeconfig.json` a következő futtatásával kérhető le:

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Azure Machine Learning összetevők frissítése az AK-fürtben

Az Azure Kubernetes Service-fürtben telepített Azure Machine Learning-összetevők frissítéseit manuálisan kell alkalmazni. 

> [!WARNING]
> Az alábbi műveletek elvégzése előtt keresse meg az Azure Kubernetes Service-fürt verzióját. Ha a fürt verziója a 1,14-as vagy annál nagyobb, akkor nem fogja tudni újra csatlakoztatni a fürtöt a Azure Machine Learning munkaterülethez.

Ezeket a frissítéseket úgy alkalmazhatja, hogy leválasztja a fürtöt a Azure Machine Learning munkaterületről, majd újra csatolja a fürtöt a munkaterülethez. Ha az SSL engedélyezve van a fürtben, meg kell adnia az SSL-tanúsítványt és a titkos kulcsot, amikor újra csatlakoztatja a fürtöt. 

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

Ha már nem rendelkezik SSL-tanúsítvánnyal és titkos kulccsal, vagy ha Azure Machine Learning által létrehozott tanúsítványt használ, lekérheti a fájlokat a fürt leválasztása előtt, ha a `kubectl` értékkel csatlakozik a fürthöz, és beolvassa a titkos `azuremlfessl`.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>A Kubernetes Base-64 kódolású formátumban tárolja a titkokat. A titkos kód `cert.pem` és `key.pem` 64-összetevőjét a `attach_config.enable_ssl`megadását megelőzően el kell végeznie. 

## <a name="recommendations-for-error-fix"></a>Hibajavítási javaslatok
Az általános megfigyelésen alapuló Azure ML-javaslatok az Azure ML gyakori hibáinak kijavítására szolgálnak.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (nincs nevű modul)
Ha a ModuleErrors-ben futtatja a kísérleteket az Azure ML-ben, az azt jelenti, hogy a betanítási parancsfájl egy telepítendő csomagot vár, de nincs hozzáadva. A csomag nevének megadása után az Azure ML a betanításhoz használt környezetben fogja telepíteni a csomagot. 

Ha a [becslések](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#estimators) -t használja a kísérletek elküldéséhez, megadhatja a csomag nevét `pip_packages` vagy `conda_packages` paraméterrel a kalkulátor alapján, attól függően, hogy melyik forrásból szeretné telepíteni a csomagot. Egy YML-fájlt is megadhat az összes függőségével `conda_dependencies_file`vagy a `pip_requirements_file` paraméter használatával egy txt-fájlban lévő összes pip-követelmény felsorolásával.

Az Azure ML a Tensorflow, a PyTorch, a Chainer és a SKLearn keretrendszer-specifikus becslések is biztosítja. Ezeknek a becslések a használata biztosítja, hogy a keretrendszer függőségei a betanításhoz használt környezetben legyenek telepítve az Ön nevében. Lehetősége van további függőségek megadására a fentiekben leírtak szerint. 
 
 Az Azure ML által karbantartott Docker-rendszerképek és azok tartalma [AzureML-tárolókban](https://github.com/Azure/AzureML-Containers)láthatók.
A keretrendszer-specifikus függőségek a megfelelő keretrendszer dokumentációs [láncában](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), a [PyTorch](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), a [TensorFlow](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)és a [SKLearn](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)listájában találhatók meg.

>[Megjegyzés!] Ha úgy gondolja, hogy egy adott csomag elég gyakori ahhoz, hogy hozzá lehessen adni az Azure ML karbantartott lemezképekhez és környezetekhez, hozzon létre GitHub-problémát a [AzureML-tárolókban](https://github.com/Azure/AzureML-Containers). 
 
 ### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (név nincs meghatározva), AttributeError (az objektumnak nincs attribútuma)
Ez a kivétel a betanítási szkriptből származik. A naplófájlokat a Azure Portalból tekintheti meg, ha további információt szeretne kapni a nem definiált névvel vagy az attribútum hibával kapcsolatban. Az SDK-ból a `run.get_details()` használatával tekintheti meg a hibaüzenetet. Ekkor a rendszer a futtatáshoz létrehozott összes naplófájlt is felsorolja. Győződjön meg arról, hogy megtekinti a betanítási szkriptet, javítsa ki a hibát, és próbálkozzon újra. 

### <a name="horovod-is-shutdown"></a>A Horovod leállítása
A legtöbb esetben ez a kivétel azt jelenti, hogy a horovod leállítását okozó folyamatok egyikében egy mögöttes kivétel történt. Az MPI-feladatok mindegyik rangsora saját dedikált naplófájlba kerül az Azure ML-ben. Ezek a naplók neve `70_driver_logs`. Elosztott képzés esetén a naplók neve a `_rank` utótaggal van ellátva, hogy könnyen megkülönböztesse a naplókat. A horovod leállítását okozó pontos hiba megtalálásához hajtsa végre az összes naplófájlt, és keresse meg `Traceback` a driver_log-fájlok végén. Ezen fájlok egyike megadja a tényleges mögöttes kivételt. 
