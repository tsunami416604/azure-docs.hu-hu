---
title: Számítási célok létrehozása és használata a modell betanításához
titleSuffix: Azure Machine Learning
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
ms.openlocfilehash: 95ded3c184836ac58a0f97d1bf30dd2e3c123ccb
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755969"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Számítási célok beállítása és használata a modell betanításához 

A Azure Machine Learning segítségével különböző erőforrásokra és környezetekre is betaníthatja a modellt, együttesen [__számítási célok__](concept-azure-machine-learning-architecture.md#compute-targets)néven. A számítási cél lehet egy helyi gép vagy egy felhőalapú erőforrás, például egy Azure Machine Learning számítási, Azure-HDInsight vagy egy távoli virtuális gép.  Emellett számítási célokat is létrehozhat a modell telepítéséhez a ["hol és hogyan kell üzembe helyezni a modelleket"](how-to-deploy-and-where.md)című cikkben leírtak szerint.

A számítási célt a Azure Machine Learning SDK, a Azure Portal, a munkaterület kezdőlapja (előzetes verzió), az Azure CLI vagy a Azure Machine Learning VS Code bővítmény használatával hozhatja létre és kezelheti. Ha olyan számítási célokat használ, amelyek egy másik szolgáltatáson (például egy HDInsight-fürtön) lettek létrehozva, akkor ezeket a Azure Machine Learning-munkaterülethez csatolva használhatja.
 
Ebből a cikkből megtudhatja, hogyan használhatja a különböző számítási célokat a modellek betanításához.  Az összes számítási cél lépései ugyanazt a munkafolyamatot követik:
1. __Hozzon létre__ egy számítási célt, ha még nem rendelkezik ilyennel.
2. __Csatolja__ a számítási célt a munkaterülethez.
3. __Konfigurálja__ a számítási célt úgy, hogy az tartalmazza a parancsfájl által igényelt Python-környezetet és a csomag függőségeit.


>[!NOTE]
> A cikkben ismertetett kód Azure Machine Learning SDK 1.0.39-verzióval lett tesztelve.

## <a name="compute-targets-for-training"></a>Számítási célok képzéshez

A Azure Machine Learning különböző számítási célok esetében eltérő támogatással rendelkezik. Egy tipikus modell fejlesztési életciklusa kis mennyiségű adaton kezdi meg a fejlesztést és kísérletezést. Ebben a szakaszban a helyi környezet használatát javasoljuk. Például a helyi számítógép vagy egy felhőalapú virtuális gép. A nagyobb adatkészletek betanításának felskálázása vagy az elosztott képzések használatakor javasoljuk, hogy Azure Machine Learning számítás használatával hozzon létre egy önálló vagy több csomópontos fürtöt, amely minden egyes futtatásakor elküldi az egyes műveleteket. Saját számítási erőforrást is csatolhat, bár a különböző forgatókönyvek támogatása az alábbiakban részletezett módon változhat:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning a számítás állandó erőforrásként hozható létre, vagy dinamikusan hozható létre, amikor futtatást kér. A futtatáson alapuló létrehozás eltávolítja a számítási célt a betanítási Futtatás befejezése után, így nem használhatja fel az így létrehozott számítási célokat.

## <a name="whats-a-run-configuration"></a>Mi az a futtatási konfiguráció?

A betanítás során gyakori, hogy a helyi számítógépen indul el, és később a betanítási szkriptet más számítási célra futtatja. A Azure Machine Learning használatával különböző számítási célokból futtathat parancsfájlokat anélkül, hogy módosítani kellene a parancsfájlt.

Mindössze annyit kell tennie, hogy a **futtatási konfiguráción**belül minden számítási cél esetében meghatározza a környezetet.  Ha ezt követően egy másik számítási célra szeretné futtatni a betanítási kísérletet, adja meg az adott számítás futtatási konfigurációját. A környezet megadásának és a konfiguráció futtatásához való kötésének részleteiért lásd: [környezetek létrehozása és kezelése a betanításhoz és üzembe helyezéshez](how-to-use-environments.md).

További információ a [kísérletek elküldéséről](#submit) a cikk végén.

## <a name="whats-an-estimator"></a>Mi az a kalkulátor?

A népszerű keretrendszerek használatával történő modell-képzés elősegítése érdekében a Azure Machine Learning Python SDK egy alternatív, magasabb szintű absztrakciót, a kalkulátor osztályt biztosít. Ez az osztály lehetővé teszi a futtatási konfigurációk egyszerű összeállítását. Létrehozhat és használhat általános [becslést](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) olyan képzési parancsfájlok beküldéséhez, amelyek bármely kiválasztott tanulási keretrendszert (például scikit-Learn) használnak.

A PyTorch, a TensorFlow és a láncolási feladatok esetében a Azure Machine Learning a megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)és [láncolási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) becslések is biztosít, hogy egyszerűbbé váljon ezen keretrendszerek használata.

További információ: ML- [modellek betanítása a becslések](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Mi az a ML-folyamat?

A ML-folyamatok segítségével optimalizálhatja a munkafolyamatot egyszerűséggel, gyorsasággal, hordozhatósággal és újbóli használattal. Azure Machine Learningekkel rendelkező folyamatok létrehozásakor az infrastruktúra és az automatizálás helyett a szakértelemre, a gépi tanulásra koncentrálhat.

A ML-folyamatok több **lépésből**állnak, amelyek a folyamat különböző számítási egységei. Az egyes lépések egymástól függetlenül futtathatók, és elkülönített számítási erőforrásokat is használhatnak. Ez lehetővé teszi, hogy egyszerre több adatszakértő is működjön ugyanazon a folyamaton, és ne legyenek túlterhelt számítási erőforrások, és az egyes lépésekhez egyszerűen különböző számítási típusokat/méreteket kell használni.

> [!TIP]
> A ML-folyamatok a modellek betanításakor használhatnak futtatási konfigurációt vagy becslések.

Míg a ML-folyamatok képesek betanítani a modelleket, a betanítás előtt is előkészítheti a modelleket, és üzembe helyezheti a modelleket. A folyamatok egyik elsődleges használati esete a Batch pontozása. További információt a [folyamatok: a gépi tanulási munkafolyamatok optimalizálása](concept-ml-pipelines.md)című témakörben talál.

## <a name="set-up-in-python"></a>Beállítás a Pythonban

Az alábbi lépésekkel konfigurálhatja ezeket a számítási célokat:

* [Helyi számítógép](#local)
* [Azure Machine Learning számítás](#amlcompute)
* [Távoli virtuális gépek](#vm)
* [Azure-HDInsight](#hdinsight)


### <a id="local"></a>Helyi számítógép

1. **Létrehozás és csatolás**: nem szükséges számítási célt létrehozni vagy csatolni ahhoz, hogy a helyi számítógépet a képzési környezetként használhassa.  

1. **Konfigurálás**: Ha a helyi számítógépet számítási célként használja, a képzési kód a [fejlesztési környezetben](how-to-configure-environment.md)fut.  Ha a környezet már rendelkezik a szükséges Python-csomagokkal, használja a felhasználó által felügyelt környezetet.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](#submit).

### <a id="amlcompute"></a>Azure Machine Learning számítás

Azure Machine Learning a számítás egy felügyelt számítási infrastruktúra, amely lehetővé teszi, hogy a felhasználó egyszerűen hozzon létre egy vagy több csomópontos számítási módszert. A számítás a munkaterület-régión belül jön létre olyan erőforrásként, amely a munkaterület más felhasználóival is megoszthatók. A számítási feladatok automatikusan méretezhetők, ha egy feladatot elküldenek, és egy Azure-Virtual Network helyezhetők el. A számítás egy tároló környezetbe kerül, és a modell függőségeit egy [Docker-tárolóban](https://www.docker.com/why-docker)csomagolja.

A betanítási folyamat a felhőben lévő CPU-vagy GPU-alapú számítási csomópontok fürtön keresztüli elosztásához Azure Machine Learning számítást is használhat. A GPU-ket tartalmazó virtuálisgép-méretekkel kapcsolatos további információkért lásd: [GPU-optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Azure Machine Learning a számítások alapértelmezett korlátai, például a lefoglalt magok száma. További információ: [Az Azure-erőforrások kezelése és kvóták igénylése](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Igény szerint létrehozhat egy Azure Machine Learning számítási környezetet, amikor futtat egy futtatást vagy állandó erőforrásként.

#### <a name="run-based-creation"></a>Futtatás alapú létrehozás

Futtatáskor számítási célként Azure Machine Learning számítási célt is létrehozhat. A rendszer automatikusan létrehozza a számítást a futtatásához. A rendszer automatikusan törli a számítást a Futtatás befejeződése után. 

> [!NOTE]
> A használni kívánt csomópontok maximális számának megadásához általában `node_count`t kell beállítania a csomópontok számához. Jelenleg (04/04/2019) egy olyan hiba, amely megakadályozza ennek működését. Áthidaló megoldásként használja a futtatási konfiguráció `amlcompute._cluster_max_node_count` tulajdonságát. Például: `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> A Azure Machine Learning számítás futtatásán alapuló létrehozása jelenleg előzetes verzióban érhető el. Ne használjon futtatáson alapuló létrehozást, ha automatikus hiperparaméter-hangolást vagy gépi tanulást használ. A hiperparaméter hangolás vagy az automatizált gépi tanulás használatához hozzon létre egy [állandó számítási](#persistent) célt.

1.  **Létrehozás, csatolás és konfigurálás**: a futtatáson alapuló létrehozás végrehajtja az összes szükséges lépést a számítási cél létrehozásához, csatolásához és konfigurálásához a futtatási konfigurációval.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](#submit).

#### <a id="persistent"></a>Állandó számítás

Az állandó Azure Machine Learning számítások újra felhasználhatók a feladatok között. A számítás a munkaterület más felhasználóival is megosztható, és a feladatok között megmarad.

1. **Létrehozás és csatolás**: állandó Azure Machine learning számítási erőforrás létrehozása Pythonban, a **vm_size** és a **max_nodes** tulajdonságainak megadása. A Azure Machine Learning ezután az intelligens alapértelmezett értékeket használja a többi tulajdonsághoz. A számítási műveletek nem a használat során nulla csomópontra vannak lebontva.   A dedikált virtuális gépek a feladatok igény szerinti futtatásához jönnek létre.
    
    * **vm_size**: Azure Machine learning számítás által létrehozott csomópontok virtuálisgép-családja.
    * **max_nodes**: azoknak a csomópontoknak a maximális száma, amelyeknek az autoskálázása Azure Machine learning számítási feladatok futtatásakor.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Azure Machine Learning számítás létrehozásakor több speciális tulajdonság is konfigurálható. A tulajdonságok lehetővé teszik a rögzített méretű állandó fürt vagy az előfizetéshez tartozó meglévő Azure-Virtual Network létrehozását.  A részletekért tekintse meg a [AmlCompute osztályt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) .
    
   Emellett állandó Azure Machine Learning számítási erőforrást is létrehozhat és csatolhat [a Azure Portalban](#portal-create).

1. **Konfigurálás**: hozzon létre egy futtatási konfigurációt az állandó számítási célhoz.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](#submit).


### <a id="vm"></a>Távoli virtuális gépek

A Azure Machine Learning a saját számítási erőforrásait is támogatja, és csatolja azt a munkaterülethez. Az egyik ilyen erőforrástípus egy tetszőleges távoli virtuális gép, feltéve, hogy az Azure Machine Learningről érhető el. Az erőforrás lehet egy Azure-beli virtuális gép, egy távoli kiszolgáló a szervezetben vagy a helyszínen. Pontosabban, mivel az IP-cím és a hitelesítő adatok (Felhasználónév és jelszó, vagy SSH-kulcs) esetében bármilyen elérhető virtuális gép használható távoli futtatáshoz.

Használhat egy rendszer által készített Conda-környezetet, egy már meglévő Python-környezetet vagy egy Docker-tárolót. A Docker-tárolón való végrehajtáshoz a virtuális gépen futó Docker-motorral kell rendelkeznie. Ez a funkció különösen akkor hasznos, ha rugalmasabb, felhőalapú fejlesztési/kísérletezési környezetet szeretne használni, mint a helyi gépen.

Ebben a forgatókönyvben az Azure Data Science Virtual Machine (DSVM) használata választható Azure-beli virtuális gép. Ez a virtuális gép egy előre konfigurált adatelemzési és AI-fejlesztési környezet az Azure-ban. A virtuális gép számos eszközt és keretrendszert kínál a teljes életciklusú gépi tanulás fejlesztéséhez. A DSVM és a Azure Machine Learning használatával kapcsolatos további információkért lásd: [fejlesztési környezet konfigurálása](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Létrehozás**: hozzon létre egy DSVM, mielőtt a modellt betanítani. Az erőforrás létrehozásával kapcsolatban tekintse meg [a Linux (Ubuntu) Data Science Virtual Machine kiépítése](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)című témakört.

    > [!WARNING]
    > Azure Machine Learning csak az Ubuntut futtató virtuális gépeket támogatja. Amikor létrehoz egy virtuális gépet, vagy egy meglévő virtuális gépet választ, ki kell választania egy Ubuntut használó virtuális gépet.

1. **Csatolás**: Ha egy meglévő virtuális gépet számítási célként szeretne csatolni, meg kell adnia a virtuális gép teljes tartománynevét (FQDN), felhasználónevét és jelszavát. A példában cserélje le a \<fqdn > a virtuális gép nyilvános teljes tartománynevére vagy a nyilvános IP-címére. Cserélje le a \<username > és a \<Password > a virtuális gép SSH-felhasználónevére és jelszavára.

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

1. **Konfigurálás**: hozzon létre egy futtatási konfigurációt a DSVM számítási célhoz. A Docker és a Conda a DSVM lévő képzési környezet létrehozásához és konfigurálásához használható.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](#submit).

### <a id="hdinsight"></a>Azure-HDInsight 

Az Azure HDInsight egy népszerű platform a Big-adatelemzéshez. A platform Apache Spark biztosít, amely a modell betanítására használható.

1. **Létrehozás**: hozza létre a HDInsight-fürtöt, mielőtt felhasználja a modell betanításához. A Spark on HDInsight-fürt létrehozásával kapcsolatban lásd: [Spark-fürt létrehozása a HDInsight-ben](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    A fürt létrehozásakor meg kell adnia egy SSH-felhasználónevet és-jelszót. Jegyezze fel ezeket az értékeket, mert szüksége lesz rájuk a HDInsight számítási célként való használatához.
    
    A fürt létrehozása után kapcsolódjon hozzá az állomásnév \<clustername >-ssh.azurehdinsight.net, ahol a \<clustername > a fürthöz megadott név. 

1. **Csatolás**: Ha egy HDInsight-fürtöt számítási célként kíván csatolni, meg kell adnia a HDInsight-fürt nevét, felhasználónevét és jelszavát. A következő példa az SDK használatával csatlakoztat egy fürtöt a munkaterülethez. A példában cserélje le a \<clustername > a fürt nevére. Cserélje le a \<username > és a \<Password > a fürthöz tartozó SSH-felhasználónévre és jelszóra.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azurehdinsight.net', 
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

1. **Konfigurálás**: hozzon létre egy futtatási konfigurációt a HDI számítási célhoz. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](#submit).


### <a id="azbatch"></a>Azure Batch 

Azure Batch a nagy léptékű párhuzamos és nagy teljesítményű számítástechnikai (HPC) alkalmazások felhőben történő futtatására szolgál. A AzureBatchStep Azure Machine Learning folyamatokban felhasználható feladatok Azure Batch készletbe való elküldéséhez.

Azure Batch számítási célként való csatolásához a Azure Machine Learning SDK-t kell használnia, és meg kell adnia a következő információkat:

-   **Azure batch számítási név**: a munkaterületen belüli számításhoz használandó felhasználóbarát név
-   **Azure batch fiók neve**: a Azure batch fiók neve
-   **Erőforráscsoport**: az Azure batch fiókot tartalmazó erőforráscsoport.

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


A cél létrehozása és a munkaterülethez való csatolása után a futtatási konfigurációban `ComputeTarget` objektummal fogja használni: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Számítási célok megtekintése


A munkaterülethez tartozó számítási célok megtekintéséhez kövesse az alábbi lépéseket:

1. Navigáljon a [Azure Portal](https://portal.azure.com) , és nyissa meg a munkaterületet. Ugyanezeket a lépéseket a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)is elérheti, bár az alábbi rendszerképek mutatják a Azure Portal.
 
1. Az __alkalmazások__területen válassza a __számítás__lehetőséget.

    [![View számítás lap](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>Számítási cél létrehozása

Az előző lépések végrehajtásával tekintheti meg a számítási célok listáját. Ezután az alábbi lépéseket követve hozhat létre számítási célt: 

1. Számítási cél hozzáadásához válassza a pluszjelet (+).

    ![Számítási cél hozzáadása](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Adja meg a számítási cél nevét. 

1. Válassza a **Machine learning Compute** lehetőséget a __betanításhoz__használandó számítási típusként. 

    >[!NOTE]
    >Azure Machine Learning a számítás az egyetlen felügyelt számítási erőforrás, amelyet a Azure Portal hozhat létre.  Az összes többi számítási erőforrás csatolható a létrehozásuk után.

1. Töltse ki az űrlapot. Adja meg a szükséges tulajdonságokat, különösen a virtuálisgép- **családot**, valamint a számítás felgyorsításához használni kívánt **csomópontok maximális** értékét.  

1. Kattintson a __Létrehozás__ gombra.


1. A létrehozási művelet állapotának megtekintéséhez válassza ki a listából a számítási célt:

    ![Válasszon ki egy számítási célt a létrehozási művelet állapotának megtekintéséhez.](./media/how-to-set-up-training-targets/View_list.png)

1. Ezután megtekintheti a számítási cél részleteit: 

    ![A számítógép céljának részleteinek megtekintése](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>Számítási célok csatolása

Az Azure Machine Learning munkaterületen kívül létrehozott számítási célok használatához csatolni kell őket. A számítási cél csatolása a munkaterület számára elérhetővé válik.

A számítási célok listájának megtekintéséhez kövesse a fentebb ismertetett lépéseket. Ezután a következő lépésekkel csatolhat egy számítási célt: 

1. Számítási cél hozzáadásához válassza a pluszjelet (+). 
1. Adja meg a számítási cél nevét. 
1. Válassza ki a __betanításhoz__csatolni kívánt számítási típust:

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
    > * [SSH-kulcsok létrehozása és használata Linux vagy macOS rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [SSH-kulcsok létrehozása és használata Windows rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Válassza a __csatolás__lehetőséget. 
1. Tekintse meg a csatolási művelet állapotát a listából válassza ki a számítási célt.

## <a name="set-up-with-cli"></a>Beállítás a parancssori felülettel

A munkaterülethez társított számítási célokat a Azure Machine Learning [CLI-bővítményének](reference-azure-machine-learning-cli.md) használatával érheti el.  A CLI-vel a következőket végezheti el:

* Felügyelt számítási cél létrehozása
* Felügyelt számítási cél frissítése
* Nem felügyelt számítási cél csatolása

További információ: erőforrás- [kezelés](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Beállítás a VS Code-ban

A Azure Machine Learninghoz tartozó [vs Code-bővítmény](how-to-vscode-tools.md#create-and-manage-compute-targets) használatával elérheti, létrehozhatja és kezelheti a munkaterülethez társított számítási célokat.

## <a id="submit"></a>Betanítási Futtatás beküldése a Azure Machine Learning SDK-val

Miután létrehozta a futtatási konfigurációt, a használatával futtathatja a kísérletet.  A betanítási futtatást elküldő kód mintája megegyezik a számítási célok összes típusával:

1. Kísérlet létrehozása a futtatáshoz
1. Küldje el a futtatást.
1. Várjon, amíg a Futtatás befejeződik.

> [!IMPORTANT]
> A betanítási Futtatás elküldésekor létrejön a betanítási parancsfájlokat tartalmazó könyvtár pillanatképe, amelyet a rendszer elküld a számítási célra. A munkaterületen található kísérlet részeként is tárolja. Ha módosítja a fájlokat, és újra elküldi a futtatást, csak a módosított fájlok lesznek feltöltve.
>
> Ha meg szeretné akadályozni, hogy a fájlok szerepeljenek a pillanatképben, hozzon létre egy [. gitignore](https://git-scm.com/docs/gitignore) vagy `.amlignore` fájlt a könyvtárban, és adja hozzá a fájlokat. A `.amlignore` fájl ugyanazt a szintaxist és mintázatot használja, mint a [. gitignore](https://git-scm.com/docs/gitignore) fájl. Ha mindkét fájl létezik, a `.amlignore` fájl elsőbbséget élvez.
> 
> További információ: [Pillanatképek](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Először hozzon létre egy kísérletet a munkaterületen.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>A kísérlet elküldése

A kísérletet `ScriptRunConfig` objektummal küldje el.  Ez az objektum az alábbiakat tartalmazza:

* **forráskönyvtár**: a betanítási parancsfájlt tartalmazó forrás könyvtára
* **parancsfájl**: a betanítási parancsfájl azonosítása
* **run_config**: a futtatási konfiguráció, amely azt határozza meg, hogy hol történjen a képzés.

A [helyi cél](#local) konfigurációjának használatához például:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Ugyanezt a kísérletet másik számítási célra is futtathatja, ha más futtatási konfigurációt használ, például a [amlcompute célt](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Ez a példa alapértelmezés szerint csak a számítási cél egy csomópontját használja a betanításhoz. Ha egynél több csomópontot szeretne használni, állítsa a futtatási konfiguráció `node_count` a kívánt számú csomópontra. A következő kód például beállítja a négy tanításhoz használt csomópontok számát:
>
> ```python
> src.run_config.node_count = 4
> ```

Vagy a következőket teheti:

* Küldje el a kísérletet egy `Estimator` objektummal, ahogy az [ml-modellek becslések-vel való betanítása](how-to-train-ml-models.md)is látható.
* HyperDrive-Futtatás küldése a [hiperparaméter finomhangolásához](how-to-tune-hyperparameters.md).
* Kísérlet küldése a [vs Code bővítmény](how-to-vscode-tools.md#train-and-tune-models)használatával.

További információkért tekintse meg a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) és a [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) dokumentációját.

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Futtatási konfiguráció létrehozása és futtatásának elküldése Azure Machine Learning CLI használatával

Használhatja az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t és a [Machine learning CLI-bővítményt](reference-azure-machine-learning-cli.md) a futtatási konfigurációk létrehozásához és a különböző számítási célokból való beküldéséhez. Az alábbi példák azt feltételezik, hogy van egy meglévő Azure Machine Learning-munkaterület, és `az login` CLI-paranccsal jelentkezett be az Azure-ba. 

### <a name="create-run-configuration"></a>Futtatási konfiguráció létrehozása

A futtatási konfiguráció létrehozásának legegyszerűbb módja a Machine learning Python-szkripteket tartalmazó mappa navigálása, valamint a CLI-parancs használata

```azurecli
az ml folder attach
```

Ez a parancs egy `.azureml` almappát hoz létre, amely tartalmazza a sablon futtatására szolgáló konfigurációs fájlokat a különböző számítási célokhoz. Ezen fájlok másolásával és szerkesztésével testreszabhatja a konfigurációt, például a Python-csomagok hozzáadásához vagy a Docker-beállítások módosításához.  

### <a name="structure-of-run-configuration-file"></a>A futtatási konfigurációs fájl szerkezete

A futtatási konfigurációs fájl YAML van formázva, a következő szakaszokkal
 * A futtatandó szkript és az argumentumai
 * A számítási cél neve, a "helyi" vagy a munkaterületen található számítás neve.
 * A Futtatás: keretrendszer, a Communicator az elosztott futtatásokhoz, a maximális időtartam és a számítási csomópontok számának végrehajtásához szükséges paraméterek.
 * Környezet szakasz. A jelen szakasz mezőinek részleteiért tekintse meg a [környezetek létrehozása és kezelése a képzéshez és üzembe helyezéshez](how-to-use-environments.md) című szakaszt.
   * A futtatáshoz telepítendő Python-csomagok, a Conda- [környezeti fájl](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)létrehozása és a __condaDependenciesFile__ mező megadása.
 * Futtatási előzmények részletei a naplófájl mappájának megadásához, valamint a kimeneti gyűjtemények és a futtatási előzmények pillanatképének engedélyezéséhez vagy letiltásához.
 * A kiválasztott keretrendszerre vonatkozó konfigurációs részletek.
 * Az adathivatkozás és az adattár részletei.
 * Az új fürt létrehozásához Machine Learning Computera vonatkozó konfigurációs részletek.

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Első lépésként hozzon létre egy kísérletet a futtatásokhoz

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Parancsfájl futtatása

Parancsfájl futtatásának elküldéséhez hajtson végre egy parancsot.

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive futtatása

A HyperDrive és az Azure CLI használatával is végrehajthatja a paraméterek hangolási futtatását. Először hozzon létre egy HyperDrive-konfigurációs fájlt a következő formátumban. A hiperparaméter hangolási paramétereinek részletes ismertetését lásd: [a modell Hiperparaméterek beállítása finomhangolása](how-to-tune-hyperparameters.md) .

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Adja hozzá ezt a fájlt a konfigurációs fájlok futtatása mellett. Ezután küldje el a HyperDrive futtatását a paranccsal:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Jegyezze fel az *argumentumok* szakaszt a runconfig és a *paraméter* területen a HyperDrive konfigurációban. Ezek tartalmazzák a betanítási parancsfájlnak átadandó parancssori argumentumokat. A runconfig értéke minden iteráció esetében azonos marad, míg a HyperDrive config tartománya megismétli a tartományt. Ne ugyanazt az argumentumot válassza mindkét fájlban.

Ezekről a ```az ml``` CLI-parancsokról és az argumentumok teljes készletéről a [dokumentációban](reference-azure-machine-learning-cli.md)talál további információt.

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Jegyzetfüzet-példák

Tekintse meg ezeket a jegyzetfüzeteket a különböző számítási célokból származó képzésekre:
* [használati útmutató – azureml/képzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [oktatóanyagok/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: a betanítási modell](tutorial-train-models-with-aml.md) felügyelt számítási célt használ a modellek betanításához.
* Ismerje meg, hogy miként lehet [hatékonyan hangolni a hiperparaméterek beállítása](how-to-tune-hyperparameters.md) a jobb modellek létrehozásához.
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe a modelleket](how-to-deploy-and-where.md).
* Tekintse meg a [RunConfiguration Class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK-referenciát.
* [Azure Machine Learning használata az Azure Virtual Networks használatával](how-to-enable-virtual-network.md)
