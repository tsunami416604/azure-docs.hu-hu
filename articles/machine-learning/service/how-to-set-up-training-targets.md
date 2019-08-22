---
title: Létrehozhat és használhat a számítási célokhoz modell betanítása
titleSuffix: Azure Machine Learning service
description: Konfigurálja a betanítási környezeteket (számítási célok) a Machine learning-modellek betanításához. Könnyedén válthat a képzési környezetek között. A képzés helyi elindítása. Ha vertikális felskálázásra van szüksége, váltson át egy felhőalapú számítási célra.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/12/2019
ms.custom: seodec18
ms.openlocfilehash: e73a64a9782535da31e2eacbaa2740007707c774
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872408"
---
# <a name="set-up-compute-targets-for-model-training"></a>Állítsa be a modell betanítása és számítási célnak 

A Azure Machine Learning szolgáltatással különféle erőforrásokon és környezeteken is elvégezheti a modell betanítását, amelyet együttesen [__számítási céloknak__](concept-azure-machine-learning-architecture.md#compute-targets)is nevezünk. A számítási cél lehet egy helyi gép vagy egy felhőalapú erőforrás, például egy Azure Machine Learning számítási, Azure-HDInsight vagy egy távoli virtuális gép.  Emellett számítási célokat is létrehozhat a modell telepítéséhez a ["hol és hogyan kell üzembe helyezni a modelleket"](how-to-deploy-and-where.md)című cikkben leírtak szerint.

A számítási célt a Azure Machine Learning SDK, az Azure Portal, az Azure CLI vagy a Azure Machine Learning VS Code bővítmény használatával hozhatja létre és kezelheti. Ha egy másik szolgáltatás (például egy HDInsight-fürtöt) használatával létrehozott számítási célnak, használhatja őket az Azure Machine Learning szolgáltatás munkaterületén csatolásával.
 
Ebből a cikkből megtudhatja, hogyan használhatja a különböző számítási célokat a modellek betanításához.  Az összes számítási cél lépései ugyanazt a munkafolyamatot követik:
1. __Hozzon létre__ egy számítási célt, ha még nem rendelkezik ilyennel.
2. __Csatolja__ a számítási célt a munkaterülethez.
3. __Konfigurálja__ a számítási célt úgy, hogy az tartalmazza a parancsfájl által igényelt Python-környezetet és a csomag függőségeit.


>[!NOTE]
> A cikkben ismertetett kód Azure Machine Learning SDK 1.0.39-verzióval lett tesztelve.

## <a name="compute-targets-for-training"></a>Számítási célok képzéshez

Azure Machine Learning szolgáltatás különböző számítási célokban eltérő támogatással rendelkezik. Egy tipikus modell fejlesztési életciklus kisebb mennyiségű adatot a dev/Kísérletezési kezdődik. Ezen a ponton használatát javasoljuk a helyi környezetben. Például a helyi számítógépen vagy egy felhőalapú virtuális Gépen. Vertikális felskálázás a tanítási a nagyobb adatkészletek, vagy hajtsa végre az elosztott betanítás, egy vagy több node fürtöt létrehozni, hogy az automatikus skálázást alkalmat futtató minden elküldésekor a az Azure Machine Learning Compute használatát javasoljuk. Bár a különböző forgatókönyvekben eltérőek lehetnek az alábbiakban ismertetett támogatási is hozzáadhat a saját számítási erőforrás:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning a számítás állandó erőforrásként hozható létre, vagy dinamikusan hozható létre, amikor futtatást kér. A futtatáson alapuló létrehozás eltávolítja a számítási célt a betanítási Futtatás befejezése után, így nem használhatja fel az így létrehozott számítási célokat.

## <a name="whats-a-run-configuration"></a>Mi az a futtatási konfiguráció?

A betanítás során gyakori, hogy a helyi számítógépen indul el, és később a betanítási szkriptet más számítási célra futtatja. A Azure Machine Learning szolgáltatással különböző számítási célokból futtathat parancsfájlokat anélkül, hogy módosítani kellene a parancsfájlt. 

Mindössze annyit kell tennie, hogy a futtatási konfigurációval határozza meg az egyesszámítási célkitűzések környezetét.  Ha ezt követően egy másik számítási célra szeretné futtatni a betanítási kísérletet, adja meg az adott számítás futtatási konfigurációját.

További információ a [kísérletek](#submit) elküldéséről a cikk végén.

### <a name="manage-environment-and-dependencies"></a>A környezet és a függőségek kezelése

A futtatási konfiguráció létrehozásakor el kell döntenie, hogyan kezelje a környezetet és a függőségeket a számítási célra. 

#### <a name="system-managed-environment"></a>A rendszer által felügyelt környezetben

Ha azt szeretné, hogy a [Conda](https://conda.io/docs/) kezelje a Python-környezetet és a parancsfájlok függőségeit, használja a rendszer által felügyelt környezetet. A rendszer által felügyelt környezetek alapértelmezés szerint és a leggyakoribb választás szerint vannak feltételezve. A távoli számítási célok esetében hasznos, különösen akkor, ha a cél nem konfigurálható. 

Mindössze annyit kell tennie, hogy minden csomag-függőséget a [CondaDependency osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) használatával határoz meg, majd a Conda létrehoz egy **conda_dependencies. YML** nevű fájlt a munkaterület **aml_config** könyvtárában a csomagok függőségeinek listájával és a betanítási kísérlet elküldésekor beállítja a Python-környezetet. 

Egy új környezet kezdeti beállítása több percet is igénybe vehet, a szükséges függőségek méretétől függően. Amíg a csomagok listája változatlan marad, a beállítási idő csak egyszer fordul elő.
  
A következő kód példát mutat be egy olyan rendszerfelügyelt környezetre, amely a scikit-Learn műveletet igényli:
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Felhasználó által felügyelt környezetben

A felhasználó által felügyelt környezetek esetében Ön felelős a környezet beállításában és minden csomag telepítéséhez, amelyet a képzési szkriptnek szüksége van a számítási célra. Ha a képzési környezet már konfigurálva van (például a helyi gépen), kihagyhatja a telepítési lépést a True értékre `user_managed_dependencies` állításával. A Conda nem fogja megtekinteni a környezetet, vagy semmit sem telepít Önnek.

Az alábbi kód egy, a felhasználó által felügyelt környezethez tartozó képzési futtatások konfigurálására mutat példát:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]

## <a name="whats-an-estimator"></a>Mi az a kalkulátor?

A népszerű keretrendszerek használatával történő modell-képzés elősegítése érdekében a Azure Machine Learning Python SDK egy alternatív, magasabb szintű absztrakciót, a kalkulátor osztályt biztosít. Ez az osztály lehetővé teszi a futtatási konfigurációk egyszerű összeállítását. Létrehozhat és használhat általános becslést olyan [](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) képzési parancsfájlok beküldéséhez, amelyek bármely kiválasztott tanulási keretrendszert (például scikit-Learn) használnak.

A PyTorch, a TensorFlow és a láncolási feladatok esetében a Azure Machine Learning a megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)és [láncolási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) becslések is biztosít, hogy egyszerűbbé váljon ezen keretrendszerek használata.

További információ: ML- [modellek betanítása a becslések](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Mi az a ML-folyamat?

A ML-folyamatok segítségével optimalizálhatja a munkafolyamatot egyszerűséggel, gyorsasággal, hordozhatósággal és újbóli használattal. Azure Machine Learningekkel rendelkező folyamatok létrehozásakor az infrastruktúra és az automatizálás helyett a szakértelemre, a gépi tanulásra koncentrálhat.

A ML-folyamatok több lépésbőlállnak, amelyek a folyamat különböző számítási egységei. Az egyes lépések egymástól függetlenül futtathatók, és elkülönített számítási erőforrásokat is használhatnak. Ez lehetővé teszi, hogy egyszerre több adatszakértő is működjön ugyanazon a folyamaton, és ne legyenek túlterhelt számítási erőforrások, és az egyes lépésekhez egyszerűen különböző számítási típusokat/méreteket kell használni.

> [!TIP]
> A ML-folyamatok a modellek betanításakor használhatnak futtatási konfigurációt vagy becslések.

Míg a ML-folyamatok képesek betanítani a modelleket, a betanítás előtt is előkészítheti a modelleket, és üzembe helyezheti a modelleket. A folyamatok egyik elsődleges használati esete a Batch pontozása. További információkért lásd [: folyamatok: Optimalizálja a gépi tanulási munkafolyamatokat](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Beállítás a Pythonban

Az alábbi lépésekkel konfigurálhatja ezeket a számítási célokat:

* [Helyi számítógép](#local)
* [Az Azure Machine Learning Compute](#amlcompute)
* [Távoli virtuális gépek](#vm)
* [Az Azure HDInsight](#hdinsight)


### <a id="local"></a>Helyi számítógép

1. **Létrehozás és csatolás**: Nem szükséges számítási célt létrehozni vagy csatolni a helyi számítógép betanítási környezetként való használatához.  

1. **Konfigurálás**:  Ha a helyi számítógépet számítási célként használja, a képzési kód a [fejlesztési környezetben](how-to-configure-environment.md)fut.  Ha a környezet már rendelkezik a szükséges Python-csomagokkal, használja a felhasználó által felügyelt környezetet.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](#submit).

### <a id="amlcompute"></a>Az Azure Machine Learning Compute

Azure Machine Learning a számítás egy felügyelt számítási infrastruktúra, amely lehetővé teszi, hogy a felhasználó egyszerűen hozzon létre egy vagy több csomópontos számítási módszert. A számítás a munkaterület-régión belül jön létre olyan erőforrásként, amely a munkaterület más felhasználóival is megoszthatók. A számítási feladatok automatikusan méretezhetők, ha egy feladatot elküldenek, és egy Azure-Virtual Network helyezhetők el. A számítás egy tároló környezetbe kerül, és a modell függőségeit egy Docker- [tárolóban](https://www.docker.com/why-docker)csomagolja.

Használhatja az Azure Machine Learning Compute a betanítási folyamat szét a felhőben Processzor és GPU számítási csomópontból álló fürtben. A GPU-ket tartalmazó virtuálisgép-méretekkel kapcsolatos további információkért lásd: [GPU-optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Azure Machine Learning a számítások alapértelmezett korlátai, például a lefoglalt magok száma. További információ: [Az Azure-erőforrások kezelése és kvóták igénylése](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Igény szerint létrehozhat egy Azure Machine Learning számítási környezetet, amikor futtat egy futtatást vagy állandó erőforrásként.

#### <a name="run-based-creation"></a>Futtatás-alapú létrehozása

Futtatáskor számítási célként Azure Machine Learning számítási célt is létrehozhat. A rendszer automatikusan létrehozza a számítást a futtatásához. A rendszer automatikusan törli a számítást a Futtatás befejeződése után. 

> [!NOTE]
> A használni kívánt csomópontok maximális számának megadásához általában a csomópontok számát kell beállítani `node_count` . Jelenleg (04/04/2019) egy olyan hiba, amely megakadályozza ennek működését. Áthidaló megoldásként használja `amlcompute._cluster_max_node_count` a futtatási konfiguráció tulajdonságát. Például: `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> A Azure Machine Learning számítás futtatásán alapuló létrehozása jelenleg előzetes verzióban érhető el. Ne használjon futtatáson alapuló létrehozást, ha automatikus hiperparaméter-hangolást vagy gépi tanulást használ. A hiperparaméter hangolás vagy az automatizált gépi tanulás használatához hozzon létre egy [állandó számítási](#persistent) célt.

1.  **Létrehozás, csatolás és konfigurálás**: A Futtatás-alapú létrehozás végrehajtja a számítási cél létrehozásához, csatolásához és konfigurálásához szükséges lépéseket a futtatási konfigurációval.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](#submit).

#### <a id="persistent"></a>Állandó számítás

Az állandó Azure Machine Learning számítások újra felhasználhatók a feladatok között. A számítás a munkaterület más felhasználóival is megosztható, és a feladatok között megmarad.

1. **Létrehozás és csatolás**: Állandó Azure Machine Learning számítási erőforrás Pythonban való létrehozásához a **vm_size** és a **max_nodes** tulajdonságokat kell megadnia. A Azure Machine Learning ezután az intelligens alapértelmezett értékeket használja a többi tulajdonsághoz. A számítási műveletek nem a használat során nulla csomópontra vannak lebontva.   A dedikált virtuális gépek a feladatok igény szerinti futtatásához jönnek létre.
    
    * **vm_size**: Azure Machine Learning számítás által létrehozott csomópontok virtuálisgép-családja.
    * **max_nodes**: Azon csomópontok maximális száma, amelyeknek az autoskálázása Azure Machine Learning számítási feladatok futtatásakor.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Azure Machine Learning számítás létrehozásakor több speciális tulajdonság is konfigurálható. A tulajdonságok lehetővé teszik a rögzített méretű állandó fürt vagy az előfizetéshez tartozó meglévő Azure-Virtual Network létrehozását.  A részletekért tekintse meg a [AmlCompute osztályt.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )
    
   Emellett állandó Azure Machine Learning számítási erőforrást is létrehozhat és csatolhat [a Azure Portalban](#portal-create).

1. **Konfigurálás**: Hozzon létre egy futtatási konfigurációt az állandó számítási célhoz.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](#submit).


### <a id="vm"></a>Távoli virtuális gépek

Az Azure Machine Learning a történő visszaállítását a saját számítási erőforrásokat, és a munkaterülethez csatolja azt is támogatja. Az egyik ilyen erőforrástípus egy tetszőleges távoli virtuális gép, feltéve, hogy Azure Machine Learning szolgáltatásból érhető el. Az erőforrás lehet egy Azure-beli virtuális gép, egy távoli kiszolgáló a szervezetben vagy a helyszínen. Pontosabban, mivel az IP-cím és a hitelesítő adatok (Felhasználónév és jelszó, vagy SSH-kulcs) esetében bármilyen elérhető virtuális gép használható távoli futtatáshoz.

Egy rendszer által fejlesztett conda-környezetben, egy már meglévő Python-környezetet, vagy egy Docker-tárolót is használhatja. A Docker-tárolón való végrehajtáshoz a virtuális gépen futó Docker-motorral kell rendelkeznie. Ez a funkció akkor különösen hasznos, ha egy olyan rugalmasabb, felhőalapú fejlesztési/kísérleti környezetben, mint a helyi gépen.

Ebben a forgatókönyvben az Azure Data Science Virtual Machine (DSVM) használata választható Azure-beli virtuális gép. Ez a virtuális gép egy előre konfigurált adatelemzési és AI-fejlesztési környezet az Azure-ban. A virtuális gép számos eszközt és keretrendszert kínál a teljes életciklusú gépi tanulás fejlesztéséhez. A DSVM és a Azure Machine Learning használatával kapcsolatos további információkért lásd: [fejlesztési környezet konfigurálása](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Létrehozás**: Hozzon létre egy DSVM, mielőtt a modellt betanítani. Az erőforrás létrehozásával kapcsolatban tekintse meg [a Linux (Ubuntu) Data Science Virtual Machine kiépítése](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)című témakört.

    > [!WARNING]
    > Azure Machine Learning csak az Ubuntut futtató virtuális gépeket támogatja. Amikor létrehoz egy virtuális gépet, vagy egy meglévő virtuális gépet választ, ki kell választania egy Ubuntut használó virtuális gépet.

1. **Csatolás**: Meglévő virtuális gép számítási célként való csatolásához meg kell adnia a virtuális gép teljes tartománynevét (FQDN), felhasználónevét és jelszavát. A példában cserélje le \<a teljes tartománynevet > a virtuális gép nyilvános teljes tartománynevére vagy a nyilvános IP-címére. Cserélje \<le a Felhasználónév \<> és a jelszó >t a virtuális gép SSH-felhasználónevével és jelszavával.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Vagy a [Azure Portal használatával](#portal-reuse)is csatlakoztathatja a DSVM a munkaterülethez.

1. **Konfigurálás**: Hozzon létre egy futtatási konfigurációt a DSVM számítási céljához. A Docker és a Conda a DSVM lévő képzési környezet létrehozásához és konfigurálásához használható.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](#submit).

### <a id="hdinsight"></a>Az Azure HDInsight 

Az Azure HDInsight egy népszerű platform a Big-adatelemzéshez. A platform Apache Spark biztosít, amely a modell betanítására használható.

1. **Létrehozás**:  Hozza létre a HDInsight-fürtöt, mielőtt felhasználja a modell betanításához. A Spark on HDInsight-fürt létrehozásával kapcsolatban lásd: [Spark-fürt létrehozása a HDInsight-ben](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    A fürt létrehozásakor meg kell adnia egy SSH-felhasználónevet és-jelszót. Jegyezze fel ezeket az értékeket, mert szüksége lesz rájuk a HDInsight számítási célként való használatához.
    
    A fürt létrehozása után kapcsolódjon hozzá az állomásnév \<clustername >-SSH.azurehdinsight.net, ahol \<a clustername > a fürthöz megadott név. 

1. **Csatolás**: Ahhoz, hogy egy HDInsight-fürtöt számítási célként lehessen csatolni, meg kell adnia a HDInsight-fürt nevét, felhasználónevét és jelszavát. Az alábbi példa egy fürt csatlakoztatása a munkaterület az SDK-t használja. A példában cserélje le \<a clustername > a fürt nevére. Cserélje \<le a username \<> és a Password > nevet a fürthöz tartozó SSH-felhasználónévvel és jelszóval.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   A HDInsight-fürtöt a munkaterülethez is csatlakoztathatja [a Azure Portal használatával](#portal-reuse).

1. **Konfigurálás**: Hozzon létre egy futtatási konfigurációt a HDI számítási célhoz. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](#submit).


### <a id="azbatch"></a>Azure Batch 

Azure Batch a nagy léptékű párhuzamos és nagy teljesítményű számítástechnikai (HPC) alkalmazások felhőben történő futtatására szolgál. A AzureBatchStep Azure Machine Learning folyamatokban felhasználható feladatok Azure Batch készletbe való elküldéséhez.

Azure Batch számítási célként való csatolásához a Azure Machine Learning SDK-t kell használnia, és meg kell adnia a következő információkat:

-   **Azure batch számítási név**: A munkaterületen belüli számításhoz használandó felhasználóbarát név
-   **Azure batch fiók neve**: Az Azure Batch fiók neve
-   **Erőforráscsoport**: Az Azure Batch fiókot tartalmazó erőforráscsoport.

A következő kód bemutatja, hogyan csatolhatja Azure Batch számítási célként:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-portal"></a>Beállítás a Azure Portalban

A munkaterülethez társított számítási célokat a Azure Portal érheti el.  A portálon a következőket végezheti el:

* A munkaterülethez csatolt [számítási célok megtekintése](#portal-view)
* [Számítási cél létrehozása](#portal-create) a munkaterületen
* A munkaterületen kívül létrehozott [számítási cél csatolása](#portal-reuse)

Miután létrejött a cél, és csatolva van a munkaterülethez, a futtatási konfigurációban egy `ComputeTarget` objektummal fogja használni: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Számítási célok megtekintése


A munkaterülethez tartozó számítási célok megtekintéséhez kövesse az alábbi lépéseket:

1. Navigáljon a [Azure Portal](https://portal.azure.com) , és nyissa meg a munkaterületet. 
1. Az __alkalmazások__területen válassza a __számítás__lehetőséget.

    [![Számítás lap megtekintése](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>Számítási cél létrehozása

Az előző lépések végrehajtásával tekintheti meg a számítási célok listáját. Ezután az alábbi lépéseket követve hozhat létre számítási célt: 

1. Számítási cél hozzáadásához válassza a pluszjelet (+).

    ![Számítási cél hozzáadása](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Adja meg a számítási cél nevét. 

1. Válassza a **Machine learning Compute** lehetőséget a betanításhoz használandó számítási típusként. 

    >[!NOTE]
    >Azure Machine Learning a számítás az egyetlen felügyelt számítási erőforrás, amelyet a Azure Portal hozhat létre.  Az összes többi számítási erőforrás csatolható a létrehozásuk után.

1. Töltse ki az űrlapot. Adja meg a szükséges tulajdonságokat, különösen avirtuálisgép-családot, valamint a számítás felgyorsításához használni kívánt **csomópontok maximális** értékét.  

1. Kattintson a __Létrehozás__ gombra.


1. A létrehozási művelet állapotának megtekintéséhez válassza ki a listából a számítási célt:

    ![Válasszon ki egy számítási célt a létrehozási művelet állapotának megtekintéséhez.](./media/how-to-set-up-training-targets/View_list.png)

1. Ezután megtekintheti a számítási cél részleteit: 

    ![A számítógép céljának részleteinek megtekintése](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>Számítási célok csatolása

Ha a Azure Machine Learning szolgáltatás munkaterületén kívül létrehozott számítási célokat szeretné használni, csatolnia kell őket. A számítási cél csatolása a munkaterület számára elérhetővé válik.

A számítási célok listájának megtekintéséhez kövesse a fentebb ismertetett lépéseket. Ezután a következő lépésekkel csatolhat egy számítási célt: 

1. Számítási cél hozzáadásához válassza a pluszjelet (+). 
1. Adja meg a számítási cél nevét. 
1. Válassza ki a betanításhoz csatolni kívánt számításitípust:

    > [!IMPORTANT]
    > Nem minden számítási típus csatlakoztatható a Azure Portal. A képzéshez jelenleg használható számítási típusok a következők:
    >
    > * Egy távoli virtuális gép
    > * Azure Databricks (gépi tanulási folyamatokban való használatra)
    > * Azure Data Lake Analytics (gépi tanulási folyamatokban való használatra)
    > * Azure HDInsight

1. Töltse ki az űrlapot, és adja meg a szükséges tulajdonságok értékeit.

    > [!NOTE]
    > A Microsoft azt javasolja, hogy SSH-kulcsokat használjon, amelyek biztonságosabbak a jelszavaknál. A jelszavak ki vannak téve a találgatásos támadásoknak. Az SSH-kulcsok titkosítási aláírásokra támaszkodnak. Az Azure Virtual Machines használható SSH-kulcsok létrehozásával kapcsolatos információkért tekintse meg a következő dokumentumokat:
    >
    > * [Hozzon létre és használhat SSH-kulcsokat a Linux vagy macOS rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Hozzon létre és SSH-kulcsok használata a Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Válassza a __csatolás__lehetőséget. 
1. Tekintse meg a csatolási művelet állapotát a listából válassza ki a számítási célt.

## <a name="set-up-with-cli"></a>Beállítás a parancssori felülettel

A munkaterülethez társított számítási célokat a Azure Machine Learning szolgáltatás [CLI](reference-azure-machine-learning-cli.md) -bővítményének használatával érheti el.  A CLI-vel a következőket végezheti el:

* Felügyelt számítási cél létrehozása
* Felügyelt számítási cél frissítése
* Nem felügyelt számítási cél csatolása

További információ: erőforrás- [kezelés](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Beállítás a VS Code-ban

A Azure Machine Learning szolgáltatáshoz tartozó [vs Code-bővítmény](how-to-vscode-tools.md#create-and-manage-compute-targets) használatával elérheti, létrehozhatja és kezelheti a munkaterülethez társított számítási célokat.

## <a id="submit"></a>Betanítási Futtatás beküldése

Miután létrehozta a futtatási konfigurációt, a használatával futtathatja a kísérletet.  A betanítási futtatást elküldő kód mintája megegyezik a számítási célok összes típusával:

1. Kísérlet létrehozása a futtatáshoz
1. Küldje el a futtatást.
1. Várjon, amíg a Futtatás befejeződik.

> [!IMPORTANT]
> A betanítási Futtatás elküldésekor létrejön a betanítási parancsfájlokat tartalmazó könyvtár pillanatképe, amelyet a rendszer elküld a számítási célra. A munkaterületen található kísérlet részeként is tárolja. Ha módosítja a fájlokat, és újra elküldi a futtatást, csak a módosított fájlok lesznek feltöltve.
>
> Ha meg szeretné akadályozni, hogy a fájlok szerepeljenek a pillanatképben, `.amlignore` hozzon létre egy [. gitignore](https://git-scm.com/docs/gitignore) vagy fájlt a címtárban, és adja hozzá a fájlokat. A `.amlignore` fájl ugyanazt a szintaxist és mintázatot használja, mint a [. gitignore](https://git-scm.com/docs/gitignore) fájl. Ha mindkét fájl létezik, a `.amlignore` fájl elsőbbséget élvez.
> 
> További információ: Pillanatképek [](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Először hozzon létre egy kísérletet a munkaterületen.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>A kísérlet elküldése

A kísérletet egy `ScriptRunConfig` objektummal küldje el.  Ez az objektum az alábbiakat tartalmazza:

* **forráskönyvtár**: A betanítási parancsfájlt tartalmazó forrás könyvtára
* **parancsfájl**: A betanítási parancsfájl azonosítása
* **run_config**: A futtatási konfiguráció, amely viszont meghatározza, hogy hol történjen a képzés.

A [helyi cél](#local) konfigurációjának használatához például:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Ugyanezt a kísérletet másik számítási célra is futtathatja, ha más futtatási konfigurációt használ, például a [amlcompute célt](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

Vagy a következőket teheti:

* A kísérletet egy `Estimator` olyan objektummal küldje el, amely az [ml modellek becslések-vel való](how-to-train-ml-models.md)betanítását mutatja.
* Kísérlet küldése [a CLI bővítmény használatával](reference-azure-machine-learning-cli.md#experiments).
* Kísérlet küldése a [vs Code bővítmény](how-to-vscode-tools.md#train-and-tune-models)használatával.

## <a name="github-tracking-and-integration"></a>GitHub-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. Az adattár aktuális véglegesítő AZONOSÍTÓját például az előzmények részeként naplózza a rendszer.

## <a name="notebook-examples"></a>Jegyzetfüzet-példák

Tekintse meg ezeket a jegyzetfüzeteket a különböző számítási célokból származó képzésekre:
* [útmutatóval-to-használat – azureml/képzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [oktatóanyagok és img-besorolás-1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: A modell](tutorial-train-models-with-aml.md) betanítása felügyelt számítási célt használ a modellek betanításához.
* Ismerje meg, hogy miként lehet [hatékonyan hangolni a hiperparaméterek beállítása](how-to-tune-hyperparameters.md) a jobb modellek létrehozásához.
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe](how-to-deploy-and-where.md)a modelleket.
* Tekintse meg a [RunConfiguration Class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK-referenciát.
* [Azure Machine Learning szolgáltatás használata Azure-beli virtuális hálózatokkal](how-to-enable-virtual-network.md)
