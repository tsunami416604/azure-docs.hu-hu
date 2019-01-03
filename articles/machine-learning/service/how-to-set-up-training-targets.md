---
title: Számítási céljainak modell betanítása
titleSuffix: Azure Machine Learning service
description: Konfigurálja a machine learning-modell betanítása (számítási céljainak) képzési környezet. Könnyedén válthat képzési környezetek között. Indítsa el a helyi képzés. Ha horizontális felskálázásra van szüksége, váltson a felhőalapú számítási célt.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794464"
---
# <a name="set-up-compute-targets-for-model-training"></a>Állítsa be a modell betanítása és számítási célnak

Az Azure Machine Learning szolgáltatással betaníthatja a modell a különböző számítási erőforrásokon. Ezek a számítási erőforrásokat, nevű __számítási céljainak__, helyi vagy a felhőben. Ebben a cikkben megismerkedhet a támogatott számítási célokhoz és azok használatát.

Egy számítási célnak egy olyan erőforrás futtatásához a tanítási szkriptet, vagy Önhöz a modellt webszolgáltatásként telepítésekor. Hozzon létre, és a egy számítási célnak kezelése az Azure Machine Learning SDK, az Azure Portalon vagy az Azure CLI használatával. Ha egy másik szolgáltatás (például Azure HDInsight-fürt) segítségével létrehozott számítási célnak, használhatja a ezeken a célokon az Azure Machine Learning szolgáltatás munkaterületén csatolásával.

Számítási célokhoz, amely támogatja az Azure Machine Learning három tág kategóriába sorolhatók:

* __Helyi__: A helyi gépen, vagy a felhő alapú virtuális gép (VM) a fejlesztés és kísérletezés környezet használni. 
* __Felügyelt számítási__: Az Azure Machine Learning Compute egy számítási ajánlat, amely az Azure Machine Learning szolgáltatás kezeli. Az ajánlat lehetővé teszi, hogy egyszerűen hozzon létre egy egyetlen vagy több csomópontos számítási képzés, a tesztelés és a batch következtetési.
* __Csatolt számítási__: Is használata a saját Azure-felhő számítási és csatlakoztassa azt az Azure Machine Learning. Tudjon meg többet a számítási típusok és azok használatát a következő szakaszokban támogatott.


## <a name="supported-compute-targets"></a>Támogatott számítási célnak

Az Azure Machine Learning szolgáltatás különböző támogatással rendelkezik a különböző számítási céloknak között. Egy tipikus modell fejlesztési életciklus fejlesztési és a egy kisebb mennyiségű adatot a Kísérletezési kezdődik. Ezen a ponton javasoljuk, hogy a helyi környezetben használhatja például a helyi számítógépen vagy egy felhőalapú virtuális Gépen. Nagyobb adatkészletekhez a képzési vertikális elosztott betanítás, használja az Azure Machine Learning számítási környezetet hozhat létre egy vagy több csomópontos fürt, hogy az automatikus skálázást alkalmat minden alkalommal, amikor egy Futtatás küldje el. Bár a különböző forgatókönyvekben eltérőek lehetnek az alábbi táblázatban ismertetett támogatási is hozzáadhat a saját számítási erőforrás:

|Számítási célt| GPU-gyorsítás | Automatikus<br/> hiperparaméter finomhangolása | Automatikus</br> gépi tanulás | Folyamatok elvégzésére barátságos|
|----|:----:|:----:|:----:|:----:|
|[Helyi számítógép](#local)| Talán | &nbsp; | ✓ | &nbsp; |
|[Az Azure Machine Learning Compute](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Távoli virtuális Gépen](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Az Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Az Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _Az Azure Databricks és az Azure Data Lake Analytics egy folyamatban csak használható._<br/>
> A folyamatok további információkért lásd: [folyamatokat az Azure Machine Learning](concept-ml-pipelines.md).
>
> Az Azure Machine Learning számítási környezetet a munkaterületen belül kell létrehozni. Meglévő példányok egy munkaterülethez nem csatolható.
>
> Más számítási célnak kívül az Azure Machine Learning létrehozott legyen, és ezután a munkaterülethez csatolja.
>
> Amikor egy modell betanításához az egyes számítási tárolók Docker-tárolórendszerképekhez támaszkodnak. A GPU-alaprendszerképet csak a Microsoft Azure-szolgáltatásokra kell használni. A modell betanítása és a szolgáltatások a következők:
> * Az Azure Machine Learning Compute
> * Azure Kubernetes Service
> * Windows adatelemző virtuális gép (DSVM)

## <a name="workflow"></a>Munkafolyamat

A munkafolyamat fejleszteni és üzembe helyezni az Azure Machine Learning-modell kövesse az alábbi lépéseket:

1. A machine learning-betanítási szkriptekhez Python nyelven fejleszthet.
1. Hozzon létre és konfigurálja a számítási célnak, vagy egy meglévő számítási célnak csatolása.
1. Küldje el a betanítási szkriptekhez, a számítási célnak.
1. Vizsgálja meg az eredményeket, és keresse meg a legjobb modellt.
1. A modell beállításjegyzék regisztrálja a modellt.
1. A modell üzembe helyezése.

> [!NOTE]
> A tanítási szkriptet egy adott számítási célnak nem kötődik. Betanításához kezdetben a helyi számítógépen, majd váltson számítási célnak a tanítási szkriptet újraírása nélkül.
> 
> Társításakor egy számítási célnak a munkaterület létrehozásával egy felügyelt számítási, vagy egy meglévő számítási csatlakoztatásával adjon meg egy nevet, a számítási erőforrásokat. A név legyen között két és 16 karakter hosszúságú.

Átállás több számítási célnak a a másikra, kell egy [futtatási konfigurációt](concept-azure-machine-learning-architecture.md#run-configuration). A futtatási konfigurációtól határozza meg, futtassa a parancsfájlt a számítási célnak módját.

## <a name="training-scripts"></a>Betanítási szkriptekhez

Amikor elindítja a betanítási futtatás, a címtár, amely tartalmazza a betanítási szkriptekhez, és elküldi azokat a számítási célnak pillanatképet hoz létre. További információkért lásd: [pillanatképek](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Helyi számítógép

Ha helyileg betanítást, az SDK segítségével nyújt a betanítási művelet. Egy felhasználó vagy rendszer-felügyelt környezetben segítségével betaníthatja.

### <a name="user-managed-environment"></a>Felhasználó által felügyelt környezetben

Felhasználó által felügyelt környezetben ellenőrizze a szükséges csomagok mindegyikét érhetők el a Python-környezetet, ahol a szkript futtatása. A következő példa bemutatja, hogyan konfigurálhatja a felhasználó által felügyelt környezetben képzési a következő kódrészletet:

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>A rendszer által felügyelt környezetben

A rendszer által felügyelt környezetekben a függőségek kezelése conda támaszkodnak. Conda létrehoz egy fájlt **conda_dependencies.yml** függőségek listáját tartalmazza. Megkérheti, hogy a rendszer hozhat létre egy új conda-környezetben, és ott a szkriptek futtatása. A rendszer által felügyelt környezetekben használható fel újra később, mindaddig, amíg a conda_dependencies.yml fájl esetén is változatlan marad. 

A kezdeti beállítás be egy új környezet több percig is eltarthat végrehajtásához mérete a szükséges függőségek alapján. A következő kódrészletet bemutatja, hogyan hozhat létre egy rendszer által felügyelt környezetben, amely attól függ, a scikit-ismerje meg:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Az Azure Machine Learning Compute

Az Azure Machine Learning Compute egy felügyelt számítási infrastruktúra, amely lehetővé teszi a felhasználó hozhat létre egyszerűen egy egyetlen vagy több csomópontos számítási. A számítási erőforrásai, amelyek megoszthatók más felhasználókkal a munkaterületen a munkaterület régión belül jön létre. A számítási felskálázással automatikusan egy feladat elküldésekor, és elhelyezheti egy Azure virtuális hálózatban. A számítási végrehajtja a tárolóalapú környezetben, és a Docker-tárolóban a modell függőségek csomagok.

Használhatja az Azure Machine Learning Compute a betanítási folyamat szét a felhőben Processzor és GPU számítási csomópontból álló fürtben. További információ a GPU-kat tartalmazó Virtuálisgép-méretek: [GPU-optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> Az Azure Machine Learning Compute esetében alapértelmezett korlátozások, például, amely kiosztható magok számát. További információkért lásd: [az Azure-erőforrások kezelése és a kérés kvóták](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

Az Azure Machine Learning számítási környezetet hozhat létre, ha ütemez egy Futtatás igény szerint, vagy állandó erőforrásként.

### <a name="run-based-creation"></a>Futtatás-alapú létrehozása

Az Azure Machine Learning számítási környezetet hozhat létre egy számítási célnak, futásidőben. A számítási futtatás automatikusan létrejön, és száma felskálázással **max_nodes** , amelyeket a futtatási konfiguráció. A Futtatás után a rendszer automatikusan törli a számítást.

> [!IMPORTANT]
> Futtatás-alapú létrehozása az Azure Machine Learning számítási környezetet jelenleg előzetes verzióban érhető el. Ne használja a run-alapú létrehozása, ha automatizált hiperparaméter finomhangolása használja, vagy az automatizált a machine learning. Hiperparaméter finomhangolása vagy automatizált a machine learning használatához hozzon létre az Azure Machine Learning számítási környezetet futtató mentése előtt.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Állandó számítási: Alapszintű

Egy állandó Azure Machine Learning számítási környezetet a feladatok felhasználhatók. A számítási megoszthatók más felhasználókkal a munkaterületen, és a feladatok között megőrzi.

Az Azure Machine Learning számítási környezetet állandó erőforrás létrehozásához, adja meg a **vm_size** és **max_nodes** tulajdonságait. Az Azure Machine Learning intelligens alapértelmezett beállítások ezután használja a többi tulajdonság esetén. A számítási automatikus skálázást alkalmat lefelé nulla csomópontokat, amikor nem használja a rendszer, és létrehozza a dedikált virtuális gépek igény szerint a feladatok végrehajtásához. 

* **vm_size**: A hozott létre az Azure Machine Learning COMPUTE számítási csomópontok Virtuálisgép-család.
* **max_nodes**: Az automatikus méretezés akár amikor futtat egy feladatot az Azure Machine Learning COMPUTE számítási csomópontok maximális száma.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Állandó számítási: Extra szintű

Néhány speciális tulajdonságok is konfigurálhatja, amikor létrehoz egy Azure Machine Learning számítási környezetet. A tulajdonságok lehetővé teszik a rögzített méretű, vagy egy meglévő Azure virtuális hálózaton belüli állandó fürt létrehozása az előfizetésében.

Az a **vm_size** és **max_nodes** tulajdonságai között is használhatja az alábbi tulajdonságokat:

* **min_nodes**: Linearity amikor az Azure Machine Learning számítási környezetben futtat egy feladatot a csomópontok minimális száma. Az alapértelmezett, minimális érték nulla (0) csomópontra.
* **vm_priority**: A virtuális gép használja, amikor létrehoz egy Azure Machine Learning számítási környezetet erőforrás típusa. Választhat **dedikált** (alapértelmezett), és **lowpriority**. Alacsony prioritású virtuális gépek a kapacitás használata az Azure-ban. Ezek a virtuális gépek olcsóbb, de futtatások is pre-empted, amikor ezek a virtuális gépek használják.
* **idle_seconds_before_scaledown**: Az összeg üresjárati időt kell várni egy Futtatás befejeződése után, és legfeljebb hány automatikus skálázási előtt **min_nodes**. Mennyi ideig lehet tétlen alapértelmezett érték 120 másodperc.
* **vnet_resourcegroup_name**: Az erőforráscsoport, a __meglévő__ virtuális hálózatot. Az Azure Machine Learning számítási környezetet a virtuális hálózaton belül jön létre.
* **vnet_name**: A virtuális hálózat neve. A virtuális hálózat az Azure Machine Learning-munkaterület ugyanabban a régióban kell lennie.
* **subnet_name**: A virtuális hálózatban lévő alhálózat neve. Az Azure Machine Learning számítási környezetet erőforrások rendelt IP-cím az alhálózat címtartományából.

> [!TIP]
> Állandó Azure Machine Learning számítási környezetet erőforrás létrehozásakor frissítheti a tulajdonságokat, mint a számát **min_nodes** vagy **max_nodes**. Vlastnost frissítéséhez hívja meg a `update()` függvény a tulajdonsághoz.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>Távoli virtuális Gépen

Az Azure Machine Learning a történő visszaállítását a saját számítási erőforrásokat, és a munkaterülethez csatolja azt is támogatja. Egy ilyen erőforrástípus egy tetszőleges távoli virtuális gép, mindaddig, amíg az Azure Machine Learning szolgáltatásból származó érhető el. Az erőforrás lehet egy Azure virtuális Gépen, a szervezet, vagy a helyszínen egy távoli kiszolgálóra. Pontosabban adja meg az IP-cím és a hitelesítő adatok (felhasználónév és jelszó vagy SSH-kulcs), használhatja bármely elérhető virtuális gép távoli futtatások.
Egy rendszer által fejlesztett conda-környezetben, egy meglévő Python-környezettel vagy egy Docker-tárolót is használhatja. Docker-tároló használatával hajtható végre, amikor szüksége lesz a virtuális gépen futó Docker-motor. A távoli virtuális gép funkció különösen hasznos, ha azt szeretné, hogy a felhőalapú fejlesztés és kísérletezés környezet, amely rugalmasabb, mint a helyi gépen.

> [!TIP]
> A DSVM használata az Azure virtuális gépként választott ehhez a forgatókönyvhöz. Ez a virtuális gép egy előre konfigurált adatelemzési és AI fejlesztési környezetet az Azure a virtuális Gépen egy válogatott választott eszközök és a teljes-életciklus machine learning-fejlesztési keretrendszerek kínál. A dsvm-hez az Azure Machine Learning használatával további információkért lásd: [a fejlesztési környezet konfigurálása](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> Az Azure Machine Learning csak az Ubuntu rendszerű virtuális gépeket támogatja. Hozzon létre egy virtuális Gépet, vagy válasszon egy meglévő virtuális Gépet, ki kell választania egy Ubuntu használó virtuális gép.

Az alábbi lépéseket az SDK segítségével betanítási célként egy DSVM konfigurálása:

1. Egy számítási célnak egy meglévő virtuális géphez csatolni kell adnia a teljesen minősített tartománynevét (FQDN), a felhasználónév és a jelszót a virtuális gép. Cserélje le a példában \<teljesen minősített tartományneve > az a virtuális gép vagy a nyilvános IP-cím nyilvános teljes Tartománynevét. Cserélje le \<username > és \<jelszó > SSH-felhasználónevet és jelszót a virtuális gép.

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. A DSVM számítási célnak a konfiguráció létrehozásához. A docker és a conda létrehozása és a képzési környezet konfigurálása a DSVM szolgálnak.

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Az Azure Databricks

Az Azure Databricks egy Apache Spark-alapú környezetben az Azure-felhőben. A környezetben is használható egy számítási célnak, ha Ön egy Azure Machine Learning-folyamat modelleket taníthat be.

> [!IMPORTANT]
> Az Azure Databricks számítási célnak csak akkor használható a Machine Learning folyamat.
>
> A modell betanításához használata előtt létre kell hoznia egy Azure Databricks-munkaterület. Ezek az erőforrás létrehozásához lásd: [Spark-feladatok futtatása Azure Databricksen](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Csatlakoztassa az Azure Databricks, egy számítási célnak, az Azure Machine Learning SDK, és adja meg a következő információkat:

* __Számítási neve__: A neve, a számítási erőforrás hozzárendelése.
* __Databricks-munkaterület neve__: Az Azure Databricks-munkaterület neve.
* __Hozzáférési jogkivonat__: A hozzáférési jogkivonatot az Azure databricks hitelesítéséhez. Hozzáférési jogkivonat létrehozásához, lásd: [hitelesítési](https://docs.azuredatabricks.net/api/latest/authentication.html).

A következő kód bemutatja, hogyan csatlakoztathat Azure Databricks, egy számítási célnak mutat be:

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create the attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Az Azure Data Lake Analytics

Az Azure Data Lake Analytics az Azure felhőben a big data-elemzési platform. A platform is használható egy számítási célnak, ha Ön egy Azure Machine Learning-folyamat modelleket taníthat be.

> [!IMPORTANT]
> Az Azure Data Lake Analytics számítási célnak csak akkor használható a Machine Learning folyamat.
>
> A modell betanításához használata előtt létre kell hoznia egy Azure Data Lake Analytics-fiókot. Ez az erőforrás létrehozásához lásd: [Ismerkedés az Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Csatlakoztassa a Data Lake Analytics számítási célként, az Azure Machine Learning SDK, és adja meg a következő információkat:

* __Számítási neve__: A neve, a számítási erőforrás hozzárendelése.
* __Erőforráscsoport__: A Data Lake Analytics-fiókot tartalmazó erőforráscsoportot.
* __Fiók neve__: A Data Lake Analytics-fiók nevét.

A következő kód bemutatja, hogyan csatlakoztathat a Data Lake Analytics számítási célként mutat be:

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Az Azure Machine Learning-folyamatok csak a Data Lake Analytics-fiók alapértelmezett data tárolójában tárolt adatok a használata. Ha a szükséges adatokat a nem alapértelmezett tároló van, használhatja a [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) másolja az adatokat, mielőtt a modell betanítását művelet.

## <a id="hdinsight"></a>Az Azure HDInsight 

Az Azure HDInsight egy népszerű big-data elemzési platform. A platform az Apache Spark, amely a modell betanításához használható biztosítja.

> [!IMPORTANT]
> A modell betanításához használata előtt létre kell hoznia a HDInsight-fürt. A Spark HDInsight-fürt létrehozásához lásd: [a HDInsight Spark-fürt létrehozása](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> A fürt létrehozásakor egy SSH-felhasználónevet és jelszót kell megadnia. Jegyezze fel ezeket az értékeket, és igény szerint HDInsight számítási célként használandó.
>
> A fürt létrehozása után a teljes tartománynév van \<clustername >. azurehdinsight.NET formátumban van, ahol \<clustername > a neve, a fürt számára megadott. FQDN cím (vagy a fürt nyilvános IP-címét) a fürt egy számítási célként használandó szükséges.

HDInsight compute célként konfigurálásához kell adnia a teljes Tartománynevet, felhasználónevet és jelszót a HDInsight-fürt számára. Az alábbi példa egy fürt csatlakoztatása a munkaterület az SDK-t használja. Cserélje le a példában \<teljesen minősített tartományneve > az a fürt vagy a nyilvános IP-cím nyilvános teljes Tartománynevét. Cserélje le \<username > és \<jelszó > az SSH-felhasználónév és a fürthöz tartozó jelszót.

> [!NOTE]
> Keresse meg a teljes Tartománynevet a fürt számára, nyissa meg az Azure Portalon, és válassza ki a HDInsight-fürtöt. Alatt __áttekintése__, láthatja, hogy a teljes Tartománynevét a __URL-cím__ bejegyzés. Az FQDN első, távolítsa el a https:\// előtag a bejegyzés kezdetétől fogva.

![A teljes tartománynév lekérése egy HDInsight-fürtön az Azure Portalon](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>Küldje el a betanítási Futtatás

A betanítási Futtatás elküldése két módja van:

* Küldje el a betanítási használatával futtassa a `ScriptRunConfig` objektum.
* Küldje el a betanítási használatával futtassa a `Pipeline` objektum.

> [!IMPORTANT]
> Az Azure Databricks és az Azure-Datalake elemzési számítási céljainak csak akkor használható a folyamat.
>
> A helyi számítási célnak a folyamat nem használható.

### <a name="scriptrunconfig-object"></a>ScriptRunConfig objektum

Futtassa a kód a minta képzési elküldeni a `ScriptRunConfig` objektum megegyezik a számítási tárolók összes típusára vonatkozóan:

1. Hozzon létre egy `ScriptRunConfig` objektum a futtatási konfigurációtól a compute-cél használatával.
1. Küldje el a futtatást.
1. Várjon, amíg a Futtatás befejeződik.

Az alábbi példában a konfigurációt a korábban létrehozott helyi számítási rendszer által felügyelt cél:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>Folyamat objektum

Futtassa a kód a minta képzési elküldeni egy `Pipeline` objektum megegyezik a számítási tárolók összes típusára vonatkozóan:

1. Adjon hozzá egy lépéssel a `Pipeline` objektum a számítási erőforrás.
1. A folyamat használatával küldjön el egy Futtatás.
1. Várjon, amíg a Futtatás befejeződik.

Az alábbi példa a korábban létrehozott Azure Databricks számítási célnak használja:

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

További információ a machine learning-folyamatokat: [folyamatok és az Azure Machine Learning](concept-ml-pipelines.md).

Például tekintse meg, amelyek bemutatják, hogyan lehet a modell betanításához egy folyamatot, a Jupyter notebookok [ https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline ](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="access-computes-in-the-azure-portal"></a>Hozzáférés kiszámítja az Azure Portalon

Elérheti a számítási célokhoz, amely az Azure Portalon a munkaterülethez lesznek társítva. 

### <a name="view-compute-targets"></a>Nézet számítási célnak

A számítási célokhoz, a munkaterület megtekintéséhez használja az alábbi lépéseket:

1. Keresse meg a [az Azure portal](https://portal.azure.com) , és nyissa meg a munkaterületet.
1. A __alkalmazások__válassza __számítási__.

    ![Nézet számítási célnak](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Hozzon létre egy számítási célnak

Az előző lépésekkel számítási célnak listájának megtekintéséhez. Ezek a lépések használatával hozzon létre egy számítási célnak:

1. Válassza a pluszjelet (+) számítási célt.

    ![Adjon hozzá egy számítási célnak](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Adjon meg egy nevet a számítási célnak.
1. Válassza ki **Machine Learning Compute** típusaként használandó számítási __képzési__.

    > [!IMPORTANT]
    > A felügyelt számítási erőforrásként képzéshez csak egy Azure Machine Learning számítási környezetet hozhat létre.

1. Az űrlap kitöltésekor. Adja meg a szükséges tulajdonságokat, különösen **Virtuálisgép-család**, és a **csomópontok maximális** használata a számítási üzembe helyezését. 
1. Kattintson a __Létrehozás__ gombra.
1. A létrehozási művelet állapotának megtekintéséhez válassza ki a számítási célnak a listában:

    ![Válassza ki a létrehozási művelet állapotának megtekintéséhez egy számítási célnak](./media/how-to-set-up-training-targets/View_list.png)

1. Ekkor megjelenik a számítási célnak részleteit:

    ![A számítógép cél részleteinek megtekintése](./media/how-to-set-up-training-targets/compute-target-details.png)

Most már küldhet a célszámítógép elleni futtató korábban leírtaknak megfelelően.


### <a name="reuse-existing-compute-targets"></a>Újból felhasználhatja a meglévő számítási célnak

A számítási célokhoz listájának megtekintéséhez a korábban ismertetett lépésekkel. Ezután tegye a következőket számítási célt használja:

1. Válassza a pluszjelet (+) számítási célt.
1. Adjon meg egy nevet a számítási célnak.
1. Válassza ki a számítási csatolni a __képzési__:

    > [!IMPORTANT]
    > Nem minden számítási típusok is csatlakoztatható az Azure Portalról.
    > Jelenleg a betanításhoz csatolható a számítási típusok a következők:
    >
    > * Egy távoli virtuális Gépen
    > * Azure Databricks
    > * Azure Data Lake Analytics
    > * Azure HDInsight

1. Töltse ki az űrlapot, és adjon meg értékeket a szükséges tulajdonságokat.

    > [!NOTE]
    > A Microsoft javasolja, hogy SSH-kulcsok biztonságosabbak, mint a jelszavak. Jelszavak védtelenek a találgatásos támadásokkal szemben. Az SSH-kulcsok titkosított aláírásokra támaszkodnak. SSH-kulcsokat használja az Azure Virtual Machines, a létrehozásával kapcsolatos információkat lásd az alábbi dokumentumokat:
    >
    > * [Hozzon létre és használhat SSH-kulcsokat a Linux vagy macOS rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Hozzon létre és SSH-kulcsok használata a Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Válassza ki __csatolása__.
1. A csatolás művelet állapotának megtekintéséhez a számítási célnak a listából.

Most már küldhet szembeni futtató számítási céljainak korábban leírtaknak megfelelően.

## <a name="notebook-examples"></a>A jegyzetfüzet-példák

Példák: a notebookok a következő helyeken:

* [útmutatóval-to-használat – azureml/képzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [oktatóanyagok és img-besorolás-1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Az Azure Machine Learning SDK-referencia](https://aka.ms/aml-sdk)
* [Oktatóanyag: A modell tanítása](tutorial-train-models-with-aml.md)
* [Az üzembe helyezés modellek](how-to-deploy-and-where.md)
* [Hozhat létre a machine learning-folyamatokat az Azure Machine Learning szolgáltatással](concept-ml-pipelines.md)
