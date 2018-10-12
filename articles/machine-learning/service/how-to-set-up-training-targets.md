---
title: Állítsa be a modell betanítása és az Azure Machine Learning szolgáltatás a számítási célokhoz |} A Microsoft Docs
description: Ismerje meg, hogyan válassza ki, és a gépi tanulási modellek betanításához használja (számítási céljainak) képzési környezet. Az Azure Machine Learning szolgáltatás lehetővé teszi egyszerű kapcsoló képzési környezet. Indítsa el a helyi képzés, és ha horizontális felskálázásra van szüksége, váltson át a felhőalapú számítási célt.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 84cba0cb156e1d847c92596a9f2f6017a429b9d2
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113818"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>Válassza ki, és a egy számítási célnak használja a modell betanításához

Az Azure Machine Learning szolgáltatással akkor is számos különböző környezetekben modellje betanításához. Ezekben a környezetekben, nevű __számítási céljainak__, helyi vagy a felhőben. Ebből a dokumentumból megtanulhatja a támogatott számítási célokhoz és azok használatát.

Egy számítási célnak, hogy az erőforrás a tanítási szkriptet vagy futtató gazdagépek a modell telepítése webszolgáltatásként. Ezeket a hozhatók létre és kezeli az Azure Machine Learning SDK vagy parancssori felület használatával. Ha rendelkezik (például az Azure Portalon vagy az Azure CLI) egy másik folyamat által létrehozott számítási célnak, használhatja őket az Azure Machine Learning szolgáltatás munkaterületén csatolásával.

Indítsa el a helyi fut a gépen, és ezután más környezetekben, például a távoli Data Science virtuális gépek a GPU-n és az Azure Batch AI vertikális felskálázás és kibővítés. 

## <a name="supported-compute-targets"></a>Támogatott számítási célnak

Az Azure Machine Learning szolgáltatás a következő számítási célnak támogatja:

|Számítási célt| GPU-gyorsítás | Automatizált hiperparaméter finomhangolása | Automatizált modell kiválasztása | A folyamatokban is használható|
|----|:----:|:----:|:----:|:----:|
|[Helyi számítógép](#local)| Talán | &nbsp; | ✓ | &nbsp; |
|[Adatelemző virtuális gép (DSVM)](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[Az Azure Batch AI](#batch)| ✓ | ✓ | ✓ | ✓ | ✓ |
|[Az Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

__[Az Azure Container Instances (aci) Szolgáltatásban](#aci)__  modelleket taníthat be is használható. Egy kiszolgáló nélküli felhőalapú szolgáltatás, amely költségkímélő és könnyen hozhat létre, és dolgozhat. ACI nem támogatja a GPU-gyorsítás, hangolása, automatizált hyper paraméter, vagy az automatizált modell kiválasztása. Ezenkívül azt nem használható egy folyamatban.

A kulcs versenytársaink a számítási célokhoz között a következők:
* __GPU-gyorsítás__: gpu-k az adatelemző virtuális gép és az Azure Batch AI érhetők el. Előfordulhat, hogy a hozzáférést egy GPU a helyi számítógépen a hardver, az illesztőprogramok és a telepített függően.
* __Automatikus hiperparaméter finomhangolása__: az Azure Machine Learning automatikus hiperparaméter optimalizálási segítséget a legjobb hiperparaméterek a modellhez.
* __Modellválasztás automatikus__: az Azure Machine Learning szolgáltatás intelligensen is javasoljuk algoritmus és a hiperparaméter kijelölés, a modell létrehozását. Automatizált modell kiválasztása segítségével gyorsabban manuálisan közben a különböző kombinációkkal, lesz egy kiváló minőségű modellhez. További információkért lásd: a [oktatóanyag: automatikusan a az Azure automatikus Machine Learning egy osztályozási modell betanításához](tutorial-auto-train-models.md) dokumentumot.
* __A folyamatok__: Azure Machine Learning szolgáltatás lehetővé teszi a különböző feladatokat, köztük a képzés és üzembe helyezését egy folyamat kombinálhatja. A folyamatok a párhuzamosan vagy sorrendben kell, és a egy olyan megbízható automation mechanizmust. További információkért lásd: a [hozhat létre a machine learning-folyamatokat az Azure Machine Learning szolgáltatással](concept-ml-pipelines.md) dokumentumot.

Az Azure Machine Learning SDK, Azure CLI-vel vagy az Azure portal használatával hozzon létre a számítási célokhoz. Használhatja a meglévő számítási célnak (csatolása) hozzáadja őket a munkaterülethez.

> [!IMPORTANT]
> Meglévő Azure-tárolók példány a munkaterülethez nem csatolható. Ehelyett hozzon létre egy új példányát.
>
> Nem hozható létre egy Azure HDInsight-fürtön belül a munkaterülethez. Ehelyett egy meglévő fürthöz kell csatolnia.

## <a name="workflow"></a>Munkafolyamat

A munkafolyamat fejleszteni és üzembe helyezni az Azure Machine Learning-modell kövesse az alábbi lépéseket:

1. Gépi tanulási parancsfájlok a Python képzési fejleszthet.
1. Létrehozása és konfigurálása, vagy egy meglévő számítási célnak csatolása.
1. Küldje el a betanítási szkriptekhez, a számítási célnak.
1. Vizsgálja meg az eredményeket, és keresse meg a legjobb modellt.
1. A modell beállításjegyzék regisztrálja a modellt.
1. A modell üzembe helyezése.

> [!IMPORTANT]
> A tanítási szkriptet egy adott számítási célnak nem kötődik. Betanításához kezdetben a helyi számítógépen, majd váltson számítási célnak a tanítási szkriptet újraírása nélkül.

Egy számítási célnak átváltás egy másik magában foglalja a létrehozása egy [futtatási konfigurációt](concept-azure-machine-learning-architecture.md#run-configuration). A futtatási konfigurációtól határozza meg, futtassa a parancsfájlt a számítási célnak módját.

## <a name="training-scripts"></a>Betanítási szkriptekhez

Amikor elindítja a betanítási futtatás, a teljes címtárra, amely tartalmazza a betanítási szkriptekhez elküldésekor. Pillanatkép, és a számítási elküld. További információkért lásd: [pillanatképek](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Helyi számítógép

Ha helyi képzés, az SDK használatával küldje el a betanítási művelet. A felhasználó- vagy rendszer által felügyelt környezetben betaníthatja.

### <a name="user-managed-environment"></a>Felhasználó által felügyelt környezetben

A felhasználó által felügyelt környezetben Ön felelőssége annak biztosítása, hogy a szükséges csomagok érhetők el a szkriptet futtatta Python-környezetet. Az alábbi kódrészlet egy példa az egy felhasználó által felügyelt környezetben képzési:

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

Jupyter Notebook azt mutatja be, a felhasználó által felügyelt környezetben képzés, lásd: [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb).
  
### <a name="system-managed-environment"></a>A rendszer által felügyelt környezetben

A rendszer által felügyelt környezetekben a függőségek kezelése conda támaszkodnak. Conda létrehoz egy fájlt `conda_dependencies.yml` függőségek listáját tartalmazza. A rendszer új conda-környezet létrehozása és a szkriptek futtatása, majd kérje meg. Lehet, hogy a rendszer által felügyelt környezetekben újrafelhasznált később, a lehető leghosszabbak a `conda_dependencies.yml` fájlok változatlan marad. 

A kezdeti beállítás be egy új környezet a szükséges függőségek méretétől függően több percig is eltarthat. A következő kódrészletet bemutatja a scikit hoz létre egy rendszer által felügyelt környezetben, amelyek függ – további tudnivalók:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

Jupyter Notebook azt mutatja be, a rendszer által felügyelt környezetben képzés, lásd: [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb).

## <a id="dsvm"></a>Adatelemző virtuális gép

A helyi gépen a számítási vagy a modell betanításához szükséges GPU-erőforrások nem rendelkeznek. Ebben a helyzetben vertikálisan felskálázhatja vagy horizontális felskálázás a betanítási folyamat hozzáadásával további számítási céljainak egy adatelemzési virtuális gépek (DSVM) például.

> [!WARNING]
> Az Azure Machine Learning csak az Ubuntu rendszert futtató virtuális gépeket támogatja. Ha egy virtuális gépet hoz létre, vagy egy meglévő kiválasztásával, ki kell választania egy Ubuntu használó.

Az alábbi lépéseket az SDK segítségével betanítási célként egy adatelemzési virtuális gép (DSVM) konfigurálása:

1. Hozzon létre vagy csatlakoztasson egy virtuális gép
    
    * Egy új adatelemző virtuális GÉPET létrehozni, először ellenőrizze, hogy ha Ön egy DSVM ugyanazzal a névvel rendelkezik, ha nem egy új virtuális gép létrehozása:
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * Egy számítási célnak egy meglévő virtuális géphez csatolni kell adnia a teljes tartománynév, a bejelentkezési nevet és a jelszót a virtuális gép.  Cserélje le a példában ```<fqdn>``` nyilvános teljes tartománynévvel a virtuális Gépet, vagy a nyilvános IP-cím. Cserélje le ```<username>``` és ```<password>``` az SSH-felhasználót és a virtuális gép jelszava:

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   It takes around 5 minutes to create the DSVM instance.

1. Create a configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image from DockerHub
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

1. Amikor végzett a számítási erőforrások törléséhez használja a következő kódot:

    ```python
    dsvm_compute.delete()
    ```

Jupyter Notebook azt mutatja be, egy Data Science virtuális gépen képzési, lásd: [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb).

## <a id="batch"></a>Az Azure Batch AI

A modell betanításához egy hosszú időt vesz igénybe, ha az Azure Batch AI segítségével a képzés szét a számítási erőforrások a felhőben egy fürt. A Batch AI GPU az erőforrás is konfigurálható.

Az alábbi példa egy már meglévő Batch AI-fürtön név alapján keres. Ha nem talál egyet, akkor a rendszer létrehozza:

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
batchai_cluster_name = os.environ.get("BATCHAI_CLUSTER_NAME", ws.name + "gpu")
cluster_min_nodes = os.environ.get("BATCHAI_CLUSTER_MIN_NODES", 1)
cluster_max_nodes = os.environ.get("BATCHAI_CLUSTER_MAX_NODES", 3)
vm_size = os.environ.get("BATCHAI_CLUSTER_SKU", "STANDARD_NC6")
autoscale_enabled = os.environ.get("BATCHAI_CLUSTER_AUTOSCALE_ENABLED", True)


if batchai_cluster_name in ws.compute_targets():
    compute_target = ws.compute_targets()[batchai_cluster_name]
    if compute_target and type(compute_target) is BatchAiCompute:
        print('found compute target. just use it. ' + batchai_cluster_name)
else:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = autoscale_enabled,
                                                                cluster_min_nodes = cluster_min_nodes, 
                                                                cluster_max_nodes = cluster_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

Csatlakoztassa a számítási célként egy meglévő Batch AI-fürtöt, meg kell adnia az Azure-erőforrás azonosítója. Az Azure Portalról az erőforrás-azonosító lekéréséhez használja az alábbi lépéseket:
1. Keresse meg `Batch AI` lehetőséget **minden szolgáltatás**
1. A munkaterület nevére, amelyben a fürt tartozik
1. Válassza ki a fürt
1. Kattintson a **tulajdonságai**
1. Másolás a **azonosítója**

Az alábbi példa egy fürt csatlakoztatása a munkaterület az SDK-t használja. Cserélje le a példában `<name>` bármilyen számítási nevére. A név nem kell egyeznie a fürt nevével. Cserélje le `<resource-id>` az Azure-erőforrás-azonosító lásd fent:

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

A Batch AI fürt és a feladat állapotát az alábbi Azure CLI-parancsok használatával is ellenőrizheti:

- A fürt állapotának ellenőrzése. Láthatja, hány csomópontok futnak a `az batchai cluster list`.
- Ellenőrizze a feladat állapotát. Láthatja, hány feladatok futnak az `az batchai job list`.

A Batch AI-fürt létrehozása körülbelül 5 percig tart.

Jupyter Notebook azt mutatja be, egy Batch AI-fürtöt a képzés, lásd: [ https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb).

## <a name='aci'></a>Az Azure Container instance-példány (aci Szolgáltatásban)

Az Azure Container Instances tárolói elkülönített, amelyek gyorsabb indítási és a nem szükséges minden olyan virtuális gépek kezeléséhez. Az Azure Machine Learning szolgáltatás használ a Linux-tárolók a westus, USA keleti régiója, westeurope, northeurope, westus2 és southeastasia régióban elérhető. További információkért lásd: [régiók rendelkezésre állása](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability). 

Az alábbi példa bemutatja, hogyan hozzon létre egy ACI számítási célnak, és vele egy modell betanításához az SDK használatával: 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
#run_config.environment.docker.base_image = 'microsoft/mmlspark:plus-0.9.9'

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

Is igénybe vehet néhány másodperc, néhány perc alatt létrehozni egy ACI számítási célnak.

Jupyter Notebook azt mutatja be, az Azure-Tárolópéldányon képzés, lásd: [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb).

## <a id="hdinsight"></a>Egy HDInsight-fürt csatolása 

HDInsight egy népszerű big-data elemzési platform. Az Apache Spark, amely a modell betanításához használható biztosít.

> [!IMPORTANT]
> Létre kell hoznia a HDInsight-fürt és a modell betanításához. A Spark HDInsight-fürt létrehozásához lásd: a [a HDInsight Spark-fürt létrehozása](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) dokumentumot.
>
> A fürt létrehozásakor egy SSH-felhasználónevet és jelszót kell megadnia. Jegyezze fel ezeket az értékeket, és igény szerint, egy számítási célnak HDInsight használatakor.
>
> Ha a fürt létrejött, van egy teljesen minősített tartománynevét (FQDN) `<clustername>.azurehdinsight.net`, ahol `<clustername>` a fürt számára megadott név. Ez a cím (vagy a fürt nyilvános IP-címét) számítási célként használandó szükséges

HDInsight compute célként konfigurálásához kell adnia a teljes tartománynév, a fürt bejelentkezési neve és a jelszót a HDInsight-fürt. Az alábbi példa egy fürt csatlakoztatása a munkaterület az SDK-t használja. Cserélje le a példában `<fqdn>` a fürt vagy a nyilvános IP-cím nyilvános teljes tartománynévvel. Cserélje le `<username>` és `<password>` az SSH-felhasználót és a fürthöz tartozó jelszót:

> [!NOTE]
> Keresse meg a teljes Tartománynevet a fürt számára, az Azure Portalon, és válassza ki a HDInsight-fürtöt. Az a __áttekintése__ szakaszban, a teljes tartománynév része a __URL-cím__ bejegyzés. Csak távolítsa el a `https://` értéke kezdetétől fogva.
>
> ![Képernyőkép a HDInsight fürt áttekintése az URL-cím bejegyzés kiemelésével](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>Küldje el a betanítási Futtatás
    
A betanítási Futtatás elküldésére szolgáló kód azonos, függetlenül a számítási célt:

* Hozzon létre egy `ScriptRunConfig` objektumot használ a számítási célnak a futtatási konfigurációtól.
* Küldje el a futtatást.
* Várjon, amíg a Futtatás befejeződik.

Az alábbi példa a konfigurációt használja, a jelen dokumentum korábbi szakaszában létrehozott helyi számítási rendszer által felügyelt cél:

```pyghon
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```

Jupyter Notebook azt mutatja be, a Spark on HDInsight-képzés, lásd: [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb).

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Megtekintheti, és állítsa be a compute az Azure portal használatával

Milyen számítási célok tartoznak a munkaterület az Azure Portalon tekintheti meg. A listához lekéréséhez használja az alábbi lépéseket:

1. Látogasson el a [az Azure portal](https://portal.azure.com) , és keresse meg a munkaterülethez.
2. Kattintson a __számítási__ mellett kapcsolni a __alkalmazások__ szakaszban.

    ![Nézet számítási lap](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Hozzon létre egy számítási célnak

A fenti lépésekkel számítási célnak listájának megtekintéséhez, és a következő lépések segítségével hozzon létre egy számítási célnak:

1. Kattintson a __+__ bejelentkezés hozzáadása egy számítási célnak.

    ![Számítás hozzáadása ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Adjon meg egy nevet a számítási célnak.
1. Válassza ki a számítási csatolni a __képzési__. 
1. Válassza ki __hozzon létre új__ és az űrlap kitöltésekor. 
1. Kattintson a __Létrehozás__ elemre.
1. Megtekintheti az állapotát a listából a számítási tároló-létrehozási művelet.

    ![Compute-lista megtekintése](./media/how-to-set-up-training-targets/View_list.png) ekkor megjelenik a számítási erőforrások részleteit.
    ![Részletek megtekintése](./media/how-to-set-up-training-targets/vm_view.PNG)
1. Ezeken a célokon leírtaknak megfelelően a fenti elleni Futtatás most már küldhet.

### <a name="reuse-existing-compute-in-your-workspace"></a>Újból felhasználhatja a meglévő számítási a munkaterületen

A fenti lépésekkel számítási célnak listájának megtekintéséhez, majd kövesse az alábbi lépéseket a számítási célnak használja:

1. Kattintson a **+** bejelentkezés hozzáadása egy számítási célnak.
2. Adjon meg egy nevet a számítási célnak.
3. Válassza ki a számítási képzéshez csatolni. A Batch AI és a virtuális gépek jelenleg támogatott képzéshez a portálon.
4. Válassza ki a "Meglévő használata".
    - Batch AI-fürtök csatlakoztatása, válassza ki a számítási célnak a legördülő listából, válassza ki a Batch AI-munkaterület és a Batch AI-fürtöt, és kattintson **létrehozás**.
    - Csatlakoztatása egy virtuális gépet, adja meg az IP-címe, felhasználónév/jelszó kombináció, privát és nyilvános kulcsok és a portot, és kattintson a Létrehozás gombra.

    > [!NOTE]
    > A Microsoft javasolja, hogy SSH-kulcsokat, mivel ezek biztonságosabb, mint a jelszavak. Jelszavak védtelenek a találgatásos támadások, miközben SSH-kulcsok titkosított aláírásokra támaszkodnak. Létrehozásával kapcsolatos információkat használja az SSH-kulcsok az Azure Virtual Machines a következő dokumentumokban talál:
    >
    > * [Hozzon létre és használhat SSH-kulcsokat a Linux vagy macOS rendszeren]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Hozzon létre és SSH-kulcsok használata a Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

5. Válassza ki a számítási célnak a számítási erőforrások listáját megtekintheti a kiépítési állapota állapotát.
6. Ezeken a célokon elleni Futtatás most már küldhet.

## <a name="examples"></a>Példák
A következő notebookok a jelen cikk fogalmait bemutatása:
* [01.Getting-Started/02.train-on-Local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)
* [01.Getting-Started/04.train-on-Remote-VM/04.train-on-Remote-VM.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm)
* [01.Getting-Started/03.train-on-aci/03.train-on-aci.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci)
* [01.Getting-Started/05.train-in-Spark/05.train-in-Spark.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark)
* [tutorials/01.train-models.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)

Ezeket a notebookokat lekérése: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Az Azure Machine Learning SDK-referencia](http://aka.ms/aml-sdk)
* [Oktatóanyag: Modell betanítása](tutorial-train-models-with-aml.md)
* [Az üzembe helyezés modellek](how-to-deploy-and-where.md)
* [Hozhat létre a machine learning-folyamatokat az Azure Machine Learning szolgáltatással](concept-ml-pipelines.md)
