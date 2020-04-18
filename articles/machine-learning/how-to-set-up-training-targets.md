---
title: Számítási célok használata a modellbetanításhoz
titleSuffix: Azure Machine Learning
description: Konfigurálja a betanítási környezetek (számítási célok) a gépi tanulási modell betanítása. Könnyedén válthat a képzési környezetek között. Kezdje el az edzést helyben. Ha horizontális felskálázásra van szüksége, váltson felhőalapú számítási célra.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 625efcce7305cd7b1dd415a286e6b1e92682cc0a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616831"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Számítási célok beállítása és használata a modellbetanításhoz 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learning segítségével betaníthatja a modellt különböző erőforrásokon vagy környezeteken, amelyeket számítási [__céloknak__](concept-azure-machine-learning-architecture.md#compute-targets)neveznek. A számítási hely lehet egy helyi gép vagy egy felhőerőforrás, például egy Azure Machine Learning vagy Azure HDInsight-erőforrás, vagy egy távoli virtuális gép.  Számítási célokat is létrehozhat a modell üzembe helyezéséhez a ["A modellek helye és üzembe helyezése"](how-to-deploy-and-where.md)című részben leírtak szerint.

Számítási célt hozhat létre és kezelhet az Azure Machine Learning SDK, az Azure Machine Learning studio, az Azure CLI vagy az Azure Machine Learning VS Code bővítmény használatával. Ha számítási célokat hozott létre egy másik szolgáltatás (például egy HDInsight-fürt), használhatja őket az Azure Machine Learning-munkaterülethez csatolva.
 
Ebben a cikkben megtudhatja, hogyan használhatja a különböző számítási célokat a modell betanításához.  Az összes számítási cél lépései ugyanazt a munkafolyamatot követik:
1. __Hozzon létre__ egy számítási célt, ha még nem rendelkezik ilyen.
2. __Csatlakoztassa__ a számítási célt a munkaterülethez.
3. __Konfigurálja__ a számítási célt úgy, hogy tartalmazza a Python-környezetet és a parancsfájl által szükséges csomagfüggőségeket.


>[!NOTE]
> Ebben a cikkben az Azure Machine Learning SDK 1.0.74-es verziójával teszteltük a kódot.

## <a name="compute-targets-for-training"></a>Számítási célok képzéshez

Az Azure Machine Learning különböző támogatással rendelkezik a különböző számítási célok között. Egy tipikus modell fejlesztési életciklusa kezdődik dev/kísérletezés egy kis mennyiségű adat. Ebben a szakaszban azt javasoljuk, hogy a helyi környezetben. Például a helyi számítógépen vagy egy felhőalapú virtuális gép. A nagyobb adatkészletekbe való betanítás ának méretezése vagy az elosztott betanítás során azt javasoljuk, hogy az Azure Machine Learning Compute használatával hozzon létre egy egy- vagy többcsomópontos fürtöt, amely minden futtatáskor automatikusskálázható. Saját számítási erőforrást is csatolhat, bár a különböző forgatókönyvek támogatása az alábbiakban részletezhető:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Az Azure Machine Learning Compute állandó erőforrásként vagy dinamikusan hozható létre, amikor futtatást kér. A futtatásalapú létrehozás eltávolítja a számítási célt a betanítási futtatás befejezése után, így nem használhatja fel újra az így létrehozott számítási célokat.

## <a name="whats-a-run-configuration"></a>Mi az a futtatási konfiguráció?

Betanításkor gyakori, hogy a helyi számítógépen indul, és később futtassa a betanítási parancsfájlt egy másik számítási célon. Az Azure Machine Learning segítségével a parancsfájlt különböző számítási célokon futtathatja anélkül, hogy módosítania kellene a parancsfájlt.

Mindössze annyit kell tennie, hogy meghatározza a környezetet az egyes számítási cél egy **futtatási konfiguráción**belül.  Ezt követően, ha szeretné futtatni a betanítási kísérletet egy másik számítási cél, adja meg a számítási futtatási konfigurációt. A környezet megadásáról és a konfiguráció futtatásához való kötéséről a [Környezetek létrehozása és kezelése betanításhoz és telepítéshez](how-to-use-environments.md)című témakörben talál.

További információ a kísérletek nek a cikk végén [történő elküldéséről.](#submit)

## <a name="whats-an-estimator"></a>Mi az a becslő?

A modellbetanítás megkönnyítése érdekében a népszerű keretrendszerek, az Azure Machine Learning Python SDK egy alternatív magasabb szintű absztrakció, a estimator osztály.  Ez az osztály lehetővé teszi a futtatási konfigurációk egyszerű összeállítását. Létrehozhat és használhat egy általános [becseseket,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) hogy olyan képzési parancsfájlokat küldjön be, amelyek bármilyen választott tanulási keretrendszert használnak (például scikit-learn). Javasoljuk, hogy egy estimator a betanítás, mert automatikusan épít beágyazott objektumok, például egy környezet vagy RunConfiguration objektumok az Ön számára. Ha azt szeretné, hogy jobban szabályozhatja, hogyan jönnek létre ezek az objektumok, és adja meg, milyen csomagokat kell telepíteni a kísérlet futtatásához, kövesse [az alábbi lépéseket,](#amlcompute) hogy küldje el a betanítási kísérletek egy Azure Machine Learning-objektum használatával.

A PyTorch, TensorFlow és Chainer feladatok hoz, az Azure Machine Learning is rendelkezik a megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)és [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) becslések egyszerűsítése ezen keretrendszerek használatával.

További információ: [A belélegezőkkel rendelkező ML-modellek betanítása.](how-to-train-ml-models.md)

## <a name="whats-an-ml-pipeline"></a>Mi az az ML-csővezeték?

Az ML-folyamatok segítségével optimalizálhatja a munkafolyamatot egyszerűséggel, gyorsasággal, hordozhatósággal és újrafelhasználással. Amikor az Azure Machine Learning teljében folyamatokat hoz létre, az infrastruktúrára és az automatizálásra, hanem a szakértelemre, a gépi tanulásra összpontosíthat.

A darabolt műszerek futókörnyezet-folyamatok több **lépésből**épülnek fel, amelyek a csővezetékben különböző számítási egységek. Minden lépés egymástól függetlenül futtatható, és elszigetelt számítási erőforrásokat használhat. Ez lehetővé teszi, hogy több adattudós dolgozzon ugyanazon a folyamaton egy időben anélkül, hogy túlterhelő számítási erőforrásokat, és azt is megkönnyíti a különböző számítási típusok/méretek használata minden lépésnél.

> [!TIP]
> Az ML-folyamatok futtatási konfigurációt vagy becsléseket használhatnak a betanítási modellekbe.

Míg az ML-folyamatok betaníthatják a modelleket, az adatokat is előkészíthetik a betanítás előtt, és a betanítás után modelleket telepíthetnek. A folyamatok elsődleges használati eseteinek egyike a kötegelt pontozás. További információ: [Folyamatok: Gépi tanulási munkafolyamatok optimalizálása.](concept-ml-pipelines.md)

## <a name="set-up-in-python"></a>Beállítás a Pythonban

Az alábbi szakaszok segítségével konfigurálhatja a számítási célokat:

* [Helyi számítógép](#local)
* [Azure Machine Learning Compute](#amlcompute)
* [Távoli virtuális gépek](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Helyi számítógép

1. **Hozzon létre és csatolja:** Nincs szükség számítási cél létrehozására vagy csatolására a helyi számítógép betanítási környezetként való használatához.  

1. **Konfigurálás:** Ha a helyi számítógépet számítási célként használja, a betanítási kód a [fejlesztői környezetben](how-to-configure-environment.md)fut.  Ha a környezet már rendelkezik a szükséges Python-csomagokkal, használja a felhasználó által felügyelt környezetet.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Most, hogy csatolta a számítási és konfigurálta a futtatást, a következő lépés [a betanítási futtatás beküldése.](#submit)

### <a name="azure-machine-learning-compute"></a><a id="amlcompute"></a>Azure Machine Learning Compute

Az Azure Machine Learning Compute egy felügyelt számítási infrastruktúra, amely lehetővé teszi a felhasználó számára, hogy egyszerűen hozzon létre egy vagy több csomópontos számítási. A számítási a munkaterületi területen belül jön létre erőforrásként, amely megosztható a munkaterület más felhasználóival. A számítási rendszer automatikusan skálázódik, amikor egy feladat elküldésre kerül, és egy Azure virtuális hálózatba helyezhető. A számítási végrehajtása egy tárolóba adott környezetben, és a modell függőségek egy [Docker-tárolóban.](https://www.docker.com/why-docker)

Az Azure Machine Learning Compute segítségével eloszthatja a betanítási folyamatot a felhőben lévő CPU- vagy GPU-számítási csomópontok fürtje között. A GPU-kat tartalmazó virtuális gépméretekkel kapcsolatos további információkért lásd: [GPU-ra optimalizált virtuálisgép-méretek.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)

Az Azure Machine Learning Compute alapértelmezett korlátokkal rendelkezik, például a leosztható magok száma. További információ: [Manage and request quotas for Azure resources](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).


Igény szerint létrehozhat egy Azure Machine Learning-számítási környezetet, amikor futást ütemez, vagy állandó erőforrásként.

#### <a name="run-based-creation"></a>Futtatásalapú létrehozás

Az Azure Machine Learning Compute-t futási időben számítási célként hozhatja létre. A számítási automatikusan létrejön a futtatáshoz. A futtatás befejezése után a rendszer automatikusan törli a számítást. 

> [!IMPORTANT]
> Az Azure Machine Learning-számítás futtatásalapú létrehozása jelenleg előzetes verzióban érhető el. Ne használjon run-alapú létrehozást, ha automatikus hiperparaméter-hangolást vagy automatikus gépi tanulást használ. A hiperparaméter-hangolás vagy az automatikus gépi tanulás használatához hozzon létre egy [állandó számítási](#persistent) célt.

1.  **Létrehozása, csatolása és konfigurálása:** A futtatásalapú létrehozás minden szükséges lépést végrehajt a számítási cél létrehozásához, csatolásához és konfigurálásához a futtatási konfigurációval.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Most, hogy csatolta a számítási és konfigurálta a futtatást, a következő lépés [a betanítási futtatás beküldése.](#submit)

#### <a name="persistent-compute"></a><a id="persistent"></a>Állandó számítás

Az állandó Azure Machine Learning-számítás újra felhasználható a feladatok között. A számítási lehet osztani más felhasználókkal a munkaterületen, és a feladatok között marad.

1. **Hozzon létre és csatolja:** Hozzon létre egy állandó Azure Machine Learning Compute erőforrás Pythonban, adja meg a **vm_size** és **max_nodes** tulajdonságok. Az Azure Machine Learning ezután intelligens alapértelmezett beállításokat használ a többi tulajdonsághoz. A számítási automatikus skálázható le nullára csomópontok, ha nincs használatban.   Dedikált virtuális gépek jönnek létre a feladatok futtatásához, ha szükséges.
    
    * **vm_size**: Az Azure Machine Learning Compute által létrehozott csomópontok virtuálisgép-családja.
    * **max_nodes:** Az automatikus skálázáshoz csak akkor, ha az Azure Machine Learning Compute-on futtat egy feladatot.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Az Azure Machine Learning Compute létrehozásakor számos speciális tulajdonságot is konfigurálhat. A tulajdonságok lehetővé teszik, hogy hozzon létre egy állandó fürt rögzített méretű, vagy egy meglévő Azure virtuális hálózat az előfizetésben.  A részleteket lásd az [AmlCompute osztályban.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )
    
   Vagy létrehozhat és csatolhat egy állandó Azure Machine Learning Compute erőforrást az [Azure Machine Learning stúdióban.](#portal-create)

1. **Konfigurálás**: Hozzon létre egy futtatási konfigurációt az állandó számítási célhoz.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Most, hogy csatolta a számítási és konfigurálta a futtatást, a következő lépés [a betanítási futtatás beküldése.](#submit)


### <a name="remote-virtual-machines"></a><a id="vm"></a>Távoli virtuális gépek

Az Azure Machine Learning azt is támogatja, hogy saját számítási erőforrást hozzon létre, és csatolja azt a munkaterülethez. Az egyik ilyen erőforrástípus egy tetszőleges távoli virtuális gép, mindaddig, amíg elérhető az Azure Machine Learning. Az erőforrás lehet egy Azure virtuális gép, egy távoli kiszolgáló a szervezetben, vagy a helyszíni. Pontosabban, mivel az IP-cím és a hitelesítő adatok (felhasználónév és jelszó, vagy SSH-kulcs), használhatja bármely elérhető virtuális gép a távoli futtatások.

Használhat egy rendszer által alapú conda környezetet, egy már meglévő Python-környezetet vagy egy Docker-tárolót. Docker-tárolón való végrehajtáshoz a virtuális gépen futó Docker-motorral kell rendelkeznie. Ez a funkció különösen akkor hasznos, ha rugalmasabb, felhőalapú fejlesztési/kísérletezési környezetet szeretne, mint a helyi gép.

Használja az Azure Data Science virtuális gép (DSVM) az Azure virtuális gép a választás ebben a forgatókönyvben. Ez a virtuális gép egy előre konfigurált adatelemzési és AI-fejlesztési környezet az Azure-ban. A virtuális gép a teljes életciklusra szóló gépi tanulás fejlesztéséhez szükséges eszközök és keretrendszerek válogatott választékát kínálja. A DSVM Azure Machine Learning szolgáltatással való használatáról a [Fejlesztői környezet konfigurálása](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)című témakörben talál további információt.

1. **Hozzon létre:** Hozzon létre egy DSVM használata előtt, hogy a modell betanítása. Az erőforrás létrehozásához [lásd: Az adatelemzési virtuális gép kiépítése Linux (Ubuntu) számára.](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

    > [!WARNING]
    > Az Azure Machine Learning csak az Ubuntut futtató virtuális gépeket támogatja. Amikor virtuális gép, vagy válasszon egy meglévő virtuális gép, ki kell választania egy virtuális gép, amely ubuntut használ.

1. **Csatolás:** Meglévő virtuális gép számítási célként való csatolásához meg kell adnia az erőforrás-azonosítót, a felhasználónevet és a virtuális gép jelszavát. A virtuális gép erőforrás-azonosítója az előfizetés-azonosító, az erőforráscsoport neve és a virtuális gép nevének használatával a következő karakterláncformátum használatával alakítható ki:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
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

   Vagy csatolhatja a DSVM-et a munkaterülethez az [Azure Machine Learning studio használatával.](#portal-reuse)

1. **Konfigurálás**: Hozzon létre egy futtatási konfigurációt a DSVM számítási célhoz. A Docker és a conda a DSVM betanítási környezetének létrehozásához és konfigurálásához használatos.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Most, hogy csatolta a számítási és konfigurálta a futtatást, a következő lépés [a betanítási futtatás beküldése.](#submit)

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Az Azure HDInsight a big data-elemzések népszerű platformja. A platform apache sparkot biztosít, amely a modell betanítására használható.

1. **Létrehozás:** Hozza létre a HDInsight-fürtöt, mielőtt betanítanák a modellt. Spark létrehozása A HDInsight-fürtön: [Spark-fürt létrehozása a HDInsightban című témakörben.](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) 

    A fürt létrehozásakor meg kell adnia egy SSH-felhasználónevet és -jelszót. Vegye figyelembe ezeket az értékeket, mivel a HDInsight számítási célként való használatához szükség van rájuk.
    
    A fürt létrehozása után csatlakozzon hozzá \<a>-ssh.azurehdinsight.net állomásnévvel, ahol \<a fürtnév> a fürthöz megadott név. 

1. **Csatolás:** HdInsight-fürt számítási célként való csatolásához meg kell adnia a HDInsight-fürt erőforrás-azonosítóját, felhasználónevét és jelszavát. A HDInsight-fürt erőforrásazonosítója az előfizetés-azonosító, az erőforráscsoport neve és a HDInsight-fürtneve használatával a következő karakterláncformátumban alakítható ki:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
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

   Vagy csatolhatja a HDInsight-fürtöt a munkaterülethez az [Azure Machine Learning studio használatával.](#portal-reuse)

1. **Konfigurálás**: Hozzon létre egy futtatási konfigurációt a HDI számítási célhoz. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Most, hogy csatolta a számítási és konfigurálta a futtatást, a következő lépés [a betanítási futtatás beküldése.](#submit)


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Az Azure Batch segítségével hatékonyan futtathatók a nagyméretű párhuzamos és nagy teljesítményű számítástechnikai (HPC) alkalmazások a felhőben. Az AzureBatchStep egy Azure Machine Learning-folyamatban használható feladatok küldése egy Azure Batch-készlet gépek.

Az Azure Batch számítási célként való csatolásához az Azure Machine Learning SDK-t kell használnia, és a következő információkat kell megadnia:

-    **Azure Batch számítási név:** A munkaterületen belüli számítási célokra használandó rövid név
-    **Azure Batch-fiók neve:** Az Azure Batch-fiók neve
-    **Erőforráscsoport:** Az Azure Batch-fiókot tartalmazó erőforráscsoport.

A következő kód bemutatja, hogyan csatolhatja az Azure Batch-et számítási célként:

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

## <a name="set-up-in-azure-machine-learning-studio"></a>Beállítás az Azure Machine Learning stúdióban

Az Azure Machine Learning-stúdióban a munkaterülethez társított számítási célok eléréséhez.  A stúdió segítségével:

* [A](#portal-view) munkaterülethez csatolt számítási célok megtekintése
* [Számítási cél létrehozása](#portal-create) a munkaterületen
* A munkaterületen kívül létrehozott [számítási cél csatolása](#portal-reuse)


Miután létrehozott egy célt, és csatolta a munkaterülethez, akkor `ComputeTarget` azt a futtatási konfigurációban egy objektummal fogja használni: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Számítási célok megtekintése


A munkaterület számítási céljainak megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Machine Learning stúdióját.](https://ml.azure.com)
 
1. Az __Alkalmazások csoportban__válassza __a Számítás__lehetőséget.

    [![Számítási lap megtekintése](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Számítási cél létrehozása

Kövesse az előző lépéseket a számítási célok listájának megtekintéséhez. Ezután az alábbi lépésekkel hozzon létre egy számítási célt: 

1. Válassza ki a pluszjelet (+) a számítási cél hozzáadásához.

    ![Számítási cél hozzáadása](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Adja meg a számítási cél nevét. 

1. Válassza a **Machine Learning Compute** lehetőséget a __betanításhoz__használandó számítási típusként. 

    >[!NOTE]
    >Az Azure Machine Learning Compute az egyetlen felügyelt számítási erőforrás, amelyet létrehozhat az Azure Machine Learning stúdióban.  Az összes többi számítási erőforrás a létrehozásuk után csatolható.

1. Töltse ki az űrlapot. Adja meg a szükséges tulajdonságok, különösen a **virtuális gép család,** és a **maximális csomópontok** a számítási felpörgetéshez használható értékeket.  

1. Kattintson a __Létrehozás__ gombra.


1. A létrehozási művelet állapotának megtekintése a számítási cél kiválasztásával a listából:

    ![Számítási cél kiválasztása a létrehozási művelet állapotának megtekintéséhez](./media/how-to-set-up-training-targets/View_list.png)

1. Ezután láthatja a számítási cél részleteit: 

    ![A számítógép céladatainak megtekintése](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Számítási célok csatolása

Az Azure Machine Learning-munkaterületen kívül létrehozott számítási célok használatához csatolnia kell őket. A számítási cél csatolása elérhetővé teszi azt a munkaterület számára.

Kövesse a korábban leírt lépéseket a számítási célok listájának megtekintéséhez. Ezután az alábbi lépésekkel csatolhat egy számítási célt: 

1. Válassza ki a pluszjelet (+) a számítási cél hozzáadásához. 
1. Adja meg a számítási cél nevét. 
1. Válassza ki a __képzéshez__csatolni kívánt számítás típusát:

    > [!IMPORTANT]
    > Nem minden számítási típus csatolható az Azure Machine Learning stúdióból. A jelenleg képzéshez csatolható számítási típusok a következők:
    >
    > * Távoli virtuális gép
    > * Azure Databricks (gépi tanulási folyamatokban való használatra)
    > * Azure Data Lake Analytics (gépi tanulási folyamatokban való használatra)
    > * Azure HDInsight

1. Töltse ki az űrlapot, és adja meg a szükséges tulajdonságok értékeit.

    > [!NOTE]
    > A Microsoft azt javasolja, hogy használjon SSH-kulcsokat, amelyek biztonságosabbak, mint a jelszavak. A jelszavak ki vannak téve a találgatásos támadásoknak. Az SSH-kulcsok kriptográfiai aláírásokra támaszkodnak. Az Azure virtuális gépekhez használható SSH-kulcsok létrehozásáról az alábbi dokumentumokban talál további információt:
    >
    > * [SSH-kulcsok létrehozása és használata Linux vagy macOS rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [SSH-kulcsok létrehozása és használata Windows rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Válassza __a Csatolás__lehetőséget. 
1. A csatolási művelet állapotának megtekintése a számítási cél kiválasztásával a listából.

## <a name="set-up-with-cli"></a>Beállítás a CLI-vel

Az Azure Machine Learning [CLI-bővítményével](reference-azure-machine-learning-cli.md) elérheti a munkaterülethez társított számítási célokat.  A CLI segítségével:

* Felügyelt számítási cél létrehozása
* Felügyelt számítási cél frissítése
* Nem felügyelt számítási cél csatolása

További információ: [Erőforrás-kezelés](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Beállítás a VS-kóddal

Az Azure Machine Learning [VS Code bővítményével](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) elérheti, létrehozhatja és kezelheti a munkaterülethez társított számítási célokat.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Képzési futtatás küldése az Azure Machine Learning SDK használatával

Miután létrehozott egy futtatási konfigurációt, használja azt a kísérlet futtatásához.  A betanítási futtatás elküldéséhez a kódminta minden típusú számítási cél esetében megegyezik:

1. Futtatásra irányuló kísérlet létrehozása
1. Küldje el a futtatást.
1. Várja meg, amíg befejeződik a futás.

> [!IMPORTANT]
> Amikor elküldi a betanítási futtatás, a betanítási parancsfájlokat tartalmazó könyvtár pillanatképe jön létre, és elküldi a számítási cél. A kísérlet részeként is tárolja a munkaterületen. Ha módosítja a fájlokat, és újra elküldi a futtatást, csak a módosított fájlok töltődnek fel.
>
> Ha meg szeretné akadályozni, hogy a fájlok bekerüljenek a pillanatképbe, hozzon létre egy [.gitignore](https://git-scm.com/docs/gitignore) vagy `.amlignore` fájlt a könyvtárban, és adja hozzá a fájlokat. A `.amlignore` fájl ugyanazt a szintaxist és mintázatot használja, mint a [.gitignore](https://git-scm.com/docs/gitignore) fájl. Ha mindkét fájl `.amlignore` létezik, a fájl élvez elsőbbséget.
> 
> További információ: [Pillanatképek](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Először hozzon létre egy kísérletet a munkaterületen.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>A kísérlet elküldése

Küldje el a `ScriptRunConfig` kísérletet egy objektummal.  Ez az objektum a következőket tartalmazza:

* **source_directory:** A betanítási parancsfájlt tartalmazó forráskönyvtár
* **script**: A betanítási parancsfájl azonosítása
* **run_config**: A futtatási konfiguráció, amely viszont meghatározza, ahol a betanítás fog bekövetkezni.

Például a [helyi célkonfiguráció](#local) használatához:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Váltson át ugyanazzal a kísérlettel, ha egy másik számítási célban szeretne futni egy másik futtatási konfigurációhasználatával, például az [amlcompute cél használatával:](#amlcompute)

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Ez a példa alapértelmezés szerint csak a számítási cél egyik csomópontját használja a betanításhoz. Egynél több csomópont használatához állítsa `node_count` a futtatási konfiguráció t a kívánt számú csomópontra. A következő kód például négyre állítja a képzéshez használt csomópontok számát:
>
> ```python
> src.run_config.node_count = 4
> ```

Vagy:

* Küldje el a `Estimator` kísérletet egy objektummal a [Train ML modellek ben látható módon.](how-to-train-ml-models.md)
* HyperDrive-futtatás küldése [a hiperparaméter-hangoláshoz](how-to-tune-hyperparameters.md).
* Nyújtson be egy kísérletet a [VS Code kiterjesztésen](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)keresztül.

További információt a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) és a [RunConfiguration dokumentációjában](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) talál.

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Futtatási konfiguráció létrehozása és futtatás elküldése az Azure Machine Learning CLI használatával

[Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és [a Machine Learning CLI bővítmény](reference-azure-machine-learning-cli.md) használatával futtatott konfigurációkat hozhat létre, és különböző számítási célokon futtathatja a futtatásokat. A következő példák feltételezik, hogy rendelkezik egy meglévő Azure `az login` Machine Learning Workspace, és a CLI paranccsal bejelentkezett az Azure-ba. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>Futtatási konfiguráció létrehozása

A futtatási konfiguráció létrehozásának legegyszerűbb módja a gépi tanulási Python-parancsfájlokat tartalmazó mappában való navigálás és a CLI parancs használata

```azurecli
az ml folder attach
```

Ez a parancs `.azureml` létrehoz egy almappát, amely különböző számítási célok sablonfuttatási konfigurációs fájljait tartalmazza. Ezeket a fájlokat másolhatja és szerkesztheti a konfiguráció testreszabásához, például Python-csomagok hozzáadásához vagy a Docker-beállítások módosításához.  

### <a name="structure-of-run-configuration-file"></a>A futtatáskonfigurációs fájl szerkezete

A futtatási konfigurációs fájl YAML formátumú, a következő szakaszok
 * A futtatandó parancsfájl és argumentumai
 * Számítási célnév, vagy "helyi" vagy egy számítási neve a munkaterületen.
 * A futtatás végrehajtásának paraméterei: keretrendszer, elosztott futtatások kommunikátora, maximális időtartam és a számítási csomópontok száma.
 * Környezet rész. Ebben a szakaszban található mezők részleteiért tekintse meg a [Környezetek létrehozása és kezelése betanításhoz és üzembe helyezéshez](how-to-use-environments.md) című témakört.
   * A futtatáshoz telepítandó Python-csomagok megadásához hozzon létre [conda környezeti fájlt,](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)és állítsa be __a condaDependenciesFile__ mezőt.
 * Az előzmények részleteinek futtatása a naplófájl mappa megadásához, valamint a kimeneti gyűjtemény engedélyezéséhez vagy letiltásához, valamint az előzmények pillanatképeinek futtatásához.
 * A kiválasztott keretrendszerre jellemző konfigurációs részletek.
 * Adathivatkozás és adattár adatai.
 * A Machine Learning Compute konfigurációs részletei új fürt létrehozásához.

Tekintse meg a példa [JSON-fájlt](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) egy teljes runconfig sémát.

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Először hozzon létre egy kísérletet a futtatások

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Parancsfájl futtatása

Parancsfájl futtatásának elküldéséhez hajtson végre egy parancsot

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive futtatása

A HyperDrive és az Azure CLI segítségével paraméterek hangolási futtatásokat hajthat végre. Először hozzon létre egy HyperDrive konfigurációs fájlt a következő formátumban. A hyperparameter tuning paraméterekkel kapcsolatos részletekért lásd: [Hyperparameters for your model](how-to-tune-hyperparameters.md) article.

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

Adja hozzá ezt a fájlt a futtatott konfigurációs fájlok mellé. Ezután küldje el a HyperDrive-futtatást a következő használatával:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Jegyezze fel a HyperDrive config runconfig és *paraméterterületének* *argumentumait.* Ezek tartalmazzák a betanítási parancsfájlnak átadandó parancssori argumentumokat. A runconfig értéke minden iterációnál ugyanaz marad, míg a HyperDrive-konfigurációtartomány értéke iterálva van. Ne adja meg ugyanazt az argumentumot mindkét fájlban.

A ```az ml``` CLI-parancsokról és az érvek teljes készletéről a [referenciadokumentációban](reference-azure-machine-learning-cli.md)olvashat bővebben.

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git-követés és integráció

Amikor elindítja a betanítási futtatás, ahol a forráskönyvtár egy helyi Git-tárház, a tárház adatait a futtatási előzmények tárolja. További információ: [Git integration for Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="notebook-examples"></a>Példák jegyzetfüzetre

Tekintse meg ezeket a jegyzetfüzeteket a különböző számítási célokkal kapcsolatos betanítások példáiért:
* [hogyan használható-azureml/képzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [oktatóanyagok/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: A modell betanítása](tutorial-train-models-with-aml.md) egy felügyelt számítási cél egy modell betanításához.
* Ismerje meg, hogyan [lehet hatékonyan hangolni a hiperparamétereket](how-to-tune-hyperparameters.md) a jobb modellek létrehozásához.
* Miután rendelkezik egy betanított modellel, ismerje meg, [hogyan és hol kell telepíteni a modelleket.](how-to-deploy-and-where.md)
* Tekintse meg a [RunConfiguration osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK-hivatkozását.
* [Az Azure Machine Learning használata az Azure virtuális hálózatokkal](how-to-enable-virtual-network.md)
