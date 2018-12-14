---
title: Számítási céljainak modell betanítása
titleSuffix: Azure Machine Learning service
description: Konfigurálja a machine learning-modell betanítása (számítási céljainak) képzési környezet. Könnyedén válthat képzési környezet. Indítsa el a helyi képzés, és ha horizontális felskálázásra van szüksége, váltson át a felhőalapú számítási célt. Databricks
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
ms.openlocfilehash: 664d56daf3e70e2e5699d0c07331c466c60e06c5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338862"
---
# <a name="set-up-compute-targets-for-model-training"></a>Állítsa be a modell betanítása és számítási célnak

Az Azure Machine Learning szolgáltatással betaníthatja a modell a különböző számítási erőforrásokon. Ezek a számítási erőforrásokat, nevű __számítási céljainak__, helyi vagy a felhőben. Ebből a dokumentumból megtanulhatja a támogatott számítási célokhoz és azok használatát.

Egy számítási célnak egy erőforrást, ahol a tanítási szkriptet fut, vagy a modellt webszolgáltatásként üzembe helyezésekor üzemeltetett. Hozzon létre, és a egy számítási célnak, az Azure Machine Learning SDK, az Azure Portalon vagy az Azure CLI használata kezelheti. Ha egy másik szolgáltatás (például egy HDInsight-fürtöt) használatával létrehozott számítási célnak, használhatja őket az Azure Machine Learning szolgáltatás munkaterületén csatolásával.

Számítási célokhoz, amely támogatja az Azure Machine Learning három tág kategóriába sorolhatók:

* __Helyi__: A helyi gépen, vagy egy felhőalapú virtuális Gépen a fejlesztői és kísérleti környezetben használni. 

* __Felügyelt számítási__: Az Azure Machine Learning Compute egy számítási kínál, amelynek az Azure Machine Learning szolgáltatás kezeli. Lehetővé teszi, hogy egyszerűen hozzon létre egy vagy több node számítási képzés, a tesztelés és a batch következtetési.

* __Csatolt számítási__: Is használata a saját Azure-felhő számítási és csatlakoztassa azt az Azure Machine Learning. További információ alább támogatott számításicsomópont-típusok és azok használatát.


## <a name="supported-compute-targets"></a>Támogatott számítási célnak

Az Azure Machine Learning szolgáltatás különböző támogatással rendelkezik a különböző számítási céloknak között. Egy tipikus modell fejlesztési életciklus kisebb mennyiségű adatot a dev/Kísérletezési kezdődik. Ezen a ponton használatát javasoljuk a helyi környezetben. Például a helyi számítógépen vagy egy felhőalapú virtuális Gépen. Vertikális felskálázás a tanítási a nagyobb adatkészletek, vagy hajtsa végre az elosztott betanítás, egy vagy több node fürtöt létrehozni, hogy az automatikus skálázást alkalmat futtató minden elküldésekor a az Azure Machine Learning Compute használatát javasoljuk. Bár a különböző forgatókönyvekben eltérőek lehetnek az alábbiakban ismertetett támogatási is hozzáadhat a saját számítási erőforrás:

|Számítási célt| GPU-gyorsítás | Automatizált hiperparaméter finomhangolása | Automatizált gépi tanulás | Folyamatok elvégzésére barátságos|
|----|:----:|:----:|:----:|:----:|
|[Helyi számítógép](#local)| Talán | &nbsp; | ✓ | &nbsp; |
|[Az Azure Machine Learning Compute](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Távoli virtuális Gépen](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Az Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Az Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ Az Azure Databricks és az Azure Data Lake Analytics képes __csak__ egy folyamatban használni. A folyamatok további információkért lásd: a [folyamatokat az Azure Machine Learning](concept-ml-pipelines.md) dokumentumot.

> [!IMPORTANT]
> Az Azure Machine Learning Compute belül kell létrehoznia a munkaterület. Meglévő példányok egy munkaterülethez nem csatolható.
>
> Más számítási célnak kívül az Azure Machine Learning létrehozott legyen, és ezután a munkaterülethez csatolja.

> [!NOTE]
> Néhány számítási modellek betanításakor a Docker-tárolórendszerképekhez támaszkodnak célok. A GPU-alaprendszerképet csak a Microsoft Azure-szolgáltatásokra kell használni. A modell betanítása és a szolgáltatások a következők:
>
> * Az Azure Machine Learning Compute
> * Azure Kubernetes Service
> * A Data Science virtuális gép.

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

> [!TIP]
> Amikor egy számítási célnak társítani a munkaterülethez, vagy hozzon létre felügyelt számítási, vagy csatolni a meglévő számítási, meg kell adnia egy nevet, a számítási erőforrásokat. Ennek 2-16 karakter hosszúságúnak kell lennie.

Egy számítási célnak átváltás egy másik magában foglalja a létrehozása egy [futtatási konfigurációt](concept-azure-machine-learning-architecture.md#run-configuration). A futtatási konfigurációtól határozza meg, futtassa a parancsfájlt a számítási célnak módját.

## <a name="training-scripts"></a>Betanítási szkriptekhez

Amikor elindítja a betanítási futtatás, a könyvtárra, amelyben a betanítási szkriptekhez pillanatképet, és a számítási elküld. További információkért lásd: [pillanatképek](concept-azure-machine-learning-architecture.md#snapshot).

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

  
### <a name="system-managed-environment"></a>A rendszer által felügyelt környezetben

A rendszer által felügyelt környezetekben a függőségek kezelése conda támaszkodnak. Conda létrehoz egy fájlt `conda_dependencies.yml` függőségek listáját tartalmazza. A rendszer új conda-környezet létrehozása és a szkriptek futtatása, majd kérje meg. Lehet, hogy a rendszer által felügyelt környezetekben újrafelhasznált később, a lehető leghosszabbak a `conda_dependencies.yml` fájlok változatlan marad. 

A kezdeti beállítás be egy új környezet a szükséges függőségek méretétől függően több percig is eltarthat. A következő kódrészletet bemutatja a scikit hoz létre egy rendszer által felügyelt környezetben, amelyek függ – további tudnivalók:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Az Azure Machine Learning Compute

Az Azure Machine Learning Compute egy felügyelt számítási infrastruktúra, amely lehetővé teszi a felhasználó hozhat létre egyszerűen egy - a több - node számítási. A létrehozást __a munkaterület régión belül__ és a egy erőforrás, amelyek megoszthatók más felhasználókkal a munkaterületén. Méretezés automatikusan a feladat elküldésekor, és elhelyezheti egy Azure virtuális hálózaton. A végrehajtása egy __tárolóalapú környezet__, Docker-tárolóban a modell függőségek csomagolása.

Használhatja az Azure Machine Learning Compute a betanítási folyamat szét a felhőben Processzor és GPU számítási csomópontból álló fürtben. További információ a GPU-kat tartalmazó Virtuálisgép-méretek: a [GPU-optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) dokumentációját.

> [!NOTE]
> Az Azure Machine Learning Compute alapértelmezett korlátokkal rendelkeznek a többek között a kiosztható magok számát. További információkért lásd: a [az Azure-erőforrások kezelése és a kérés kvóták](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas) dokumentumot.

Az Azure Machine Learning COMPUTE számítási igény szerinti hozhat létre, ha ütemez egy futtatás, vagy állandó erőforrásként.

### <a name="run-based-creation"></a>Futtatás-alapú létrehozása

Az Azure Machine Learning Compute számítási célként futásidőben létrehozhat. Ebben az esetben a számítási futtatás, akár a futtatási konfigurációt megadott max_nodes méretezhető automatikusan létrejön, és ezután __automatikusan__ a Futtatás után.

> [!IMPORTANT]
> Futtatás-alapú létrehozása az Azure Machine Learning compute jelenleg előzetes verzióként. Ne használjon run-alapú létrehozása, ha a Hiperparaméter finomhangolása vagy a Machine Learning automatikus használ. Ha szeretné használni a Hiperparaméter finomhangolása vagy automatizált Machine Learning, hozzon létre az Azure Machine Learning compute futtató elküldése előtt.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Állandó számítási (alapszintű)

Egy állandó Azure Machine Learning COMPUTE számítási feladatok több felhasználhatók. A munkaterület más felhasználókkal megosztható, és a feladatok között marad.

Az Azure Machine Learning Compute állandó erőforrás létrehozása, adja meg a `vm_size` és `max_nodes` paramétereket. Az Azure Machine Learning intelligens alapértelmezett beállítások ezután használja a többi paramétert.  Például a számítási automatikus lefelé nulla csomópontokat, amikor nincs használatban, és dedikált virtuális gépek létrehozásához történő futtatásra van beállítva a feladatok igény szerint. 

* **vm_size**: A hozott létre az Azure Machine Learning COMPUTE számítási csomópontok Virtuálisgép-család.
* **max_nodes**: Az automatikus méretezés, miközben a feladat futtatása az Azure Machine Learning COMPUTE számítási csomópontok maximális száma.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Állandó számítási (speciális)

Néhány speciális tulajdonságok konfigurálhatja az Azure Machine Learning Compute létrehozásakor is.  Ezek a tulajdonságok lehetővé teszik a rögzített méretű, vagy egy meglévő Azure virtuális hálózaton belül állandó fürt létrehozása az előfizetésében.

Mellett `vm_size` és `max_nodes`, használhatja az alábbi tulajdonságokat:

* **min_nodes**: Csomópontok minimális száma (alapértelmezés szerint a 0 csomópontra), miközben a feladat futtatása az Azure Machine Learning Compute linearity való.
* **vm_priority**: "Dedikált" (alapértelmezett) és "lowpriority" virtuális gépek választhat az Azure Machine Learning Compute létrehozásakor. Alacsony prioritású virtuális gépek használata az Azure többletkapacitással, és így olcsóbb, de a Futtatás folyamatban van a felfüggesztett kockázati.
* **idle_seconds_before_scaledown**: Az automatikus skálázás min_nodes, mielőtt futtatási befejezése után várjon üresjárati idő (alapértelmezett érték 120 másodperc)
* **vnet_resourcegroup_name**: Az erőforráscsoport a __meglévő__ virtuális hálózatot. Az Azure Machine Learning Compute a virtuális hálózaton belül jön létre.
* **vnet_name**: Virtuális hálózat neve. A virtuális hálózat az Azure Machine Learning-munkaterület ugyanabban a régióban kell lennie.
* **subnet_name**: A virtuális hálózatban lévő alhálózat neve. Azure Machine Learning Compute-erőforrások hozzá lesz rendelve IP-cím az alhálózat címtartományából.

> [!TIP]
> Egy állandó Azure Machine Learning COMPUTE számítási erőforrás létrehozásakor a tulajdonságait, például a min_nodes vagy a max_nodes frissíthetik is rendelkezik. Egyszerűen hívja a `update()` függvény azt.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
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

Az Azure Machine Learning a történő visszaállítását a saját számítási erőforrásokat, és a munkaterülethez csatolja azt is támogatja. Egy ilyen erőforrás típus egy tetszőleges távoli virtuális gép mindaddig, amíg az Azure Machine Learning szolgáltatásból származó érhető el. Annak oka az lehet, vagy egy Azure virtuális Gépen, vagy egy távoli kiszolgálóra, a szervezet vagy a helyszínen. Pontosabban a megadott IP-címét és hitelesítő adatok (felhasználónév/jelszó vagy SSH-kulcs), távoli Futtatás bármely elérhető virtuális Gépet is használhat.
Egy rendszer által fejlesztett conda-környezetben, egy már meglévő Python-környezetet, vagy egy Docker-tárolót is használhatja. Végrehajtás Docker-tároló használata szükséges, hogy a virtuális gépen futó Docker-motor. Ez a funkció akkor különösen hasznos, ha egy olyan rugalmasabb, felhőalapú fejlesztési/kísérleti környezetben, mint a helyi gépen.

> [!TIP]
> Azt javasoljuk, hogy az adatelemző virtuális gép használata az Azure virtuális gépként választott ehhez a forgatókönyvhöz. Ez nem egy előre konfigurált adatelemzési és AI fejlesztési környezetet az Azure-ban a válogatott választott eszközökön és keretrendszereken a gépi Tanulási fejlesztési teljes életciklusát. További információ az adatelemző virtuális gép használatával az Azure Machine Learning: a [a fejlesztési környezet konfigurálása](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm) dokumentumot.

> [!WARNING]
> Az Azure Machine Learning csak az Ubuntu rendszert futtató virtuális gépeket támogatja. Ha egy virtuális gépet hoz létre, vagy egy meglévő kiválasztásával, ki kell választania egy Ubuntu használó.

Az alábbi lépéseket az SDK segítségével betanítási célként egy adatelemzési virtuális gép (DSVM) konfigurálása:

1. Egy számítási célnak egy meglévő virtuális géphez csatolni kell adnia a teljes tartománynév, a bejelentkezési nevet és a jelszót a virtuális gép.  Cserélje le a példában ```<fqdn>``` nyilvános teljes tartománynévvel a virtuális Gépet, vagy a nyilvános IP-cím. Cserélje le ```<username>``` és ```<password>``` az SSH-felhasználót és a virtuális gép jelszava:

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create compute config.
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")
    # If using SSH instead of a password, use this:
    #                                                  ssh_port=22,
    #                                                   username='<username>',
    #                                                   password=None,
    #                                                   private_key_file="path-to-file",
    #                                                   private_key_passphrase="passphrase")

    # Attach the compute
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. A DSVM számítási célnak a konfiguráció létrehozásához. A docker és a conda létrehozása és konfigurálása a oktatókörnyezet DSVM használhatók:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Az Azure Databricks

Az Azure Databricks egy Apache Spark-alapú környezetben az Azure-felhőben. Az Azure Machine Learning-folyamat modellek betanításakor számítási célt, használható.

> [!IMPORTANT]
> Az Azure Databricks számítási célnak csak akkor használható a Machine Learning folyamat.
>
> Létre kell hoznia egy Azure Databricks-munkaterület és a modell betanításához. Ezek az erőforrás létrehozásához tekintse meg a [Spark-feladatok futtatása Azure Databricksen](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentumot.

Csatlakoztassa az Azure Databricks, egy számítási célnak, az Azure Machine Learning SDK, és adja meg a következő információkat:

* __Számítási neve__: A számítási erőforrás hozzárendelni kívánt nevét.
* __Databricks-munkaterület neve__: Az Azure Databricks-munkaterület neve.
* __Hozzáférési jogkivonat__: A hozzáférési jogkivonatot az Azure databricks hitelesítéséhez. Hozzáférési jogkivonat létrehozásához, tekintse meg a [hitelesítési](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentumot.

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

    # Create attach config
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

Az Azure Data Lake Analytics egy big data-elemzési platform az Azure-felhőben. Az Azure Machine Learning-folyamat modellek betanításakor számítási célt, használható.

> [!IMPORTANT]
> Az Azure Data Lake Analytics számítási célnak csak akkor használható a Machine Learning folyamat.
>
> Létre kell hoznia egy Azure Data Lake Analytics-fiók és a modell betanításához. Ez az erőforrás létrehozásához tekintse meg a [Ismerkedés az Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentumot.

Csatlakoztassa a Data Lake Analytics számítási célként, az Azure Machine Learning SDK, és adja meg a következő információkat:

* __Számítási neve__: A számítási erőforrás hozzárendelni kívánt nevét.
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
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Az Azure Machine Learning-folyamatokat a Data Lake Analytics-fiók az alapértelmezett data store-ban tárolt adatokkal folytatott csak működik. Ha adatokat kell a munkahelyi egy nem alapértelmezett tároló, használhat egy [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) képzési előtt az adatok másolásához.

## <a id="hdinsight"></a>Az Azure HDInsight 

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
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attaches a HDInsight cluster as a compute target.
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

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>Küldje el a betanítási Futtatás

A betanítási Futtatás elküldése két módja van:

* Küldjön egy `ScriptRunConfig` objektum.
* Küldjön egy `Pipeline` objektum.

> [!IMPORTANT]
> Az Azure Databricks és az Azure-Datalake elemzési számítási céljainak csak akkor használható a folyamat.
> A helyi számítási célnak a folyamat nem használható.

### <a name="submit-using-scriptrunconfig"></a>Küldés használatával `ScriptRunConfig`

A képzési elküldésére szolgáló kód minta fusson, `ScriptRunConfig` azonos, függetlenül a számítási célt:

* Hozzon létre egy `ScriptRunConfig` objektumot használ a számítási célnak a futtatási konfigurációtól.
* Küldje el a futtatást.
* Várjon, amíg a Futtatás befejeződik.

Az alábbi példa a konfigurációt használja, a jelen dokumentum korábbi szakaszában létrehozott helyi számítási rendszer által felügyelt cél:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="submit-using-a-pipeline"></a>Küldje el a folyamat használata

A kód a képzési fusson, a folyamat elküldése azonos, függetlenül a számítási célnak a mintát:

* Lépés hozzáadása a folyamathoz a számítási erőforrás.
* Küldjön el egy futtatás, a folyamat használatával.
* Várjon, amíg a Futtatás befejeződik.

Az alábbi példában az Azure Databricks számítási célnak a jelen dokumentum korábbi létrehozva:

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
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

További információ a machine learning-folyamatokat, tekintse meg a [folyamatok és az Azure Machine Learning](concept-ml-pipelines.md) dokumentum.

Például a Jupyter Notebooks, amelyek bemutatják a képzést nyújt egy folyamatot, tekintse meg [ https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline ](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Megtekintheti, és állítsa be a compute az Azure portal használatával

Milyen számítási célok tartoznak a munkaterület az Azure Portalon tekintheti meg. A listához lekéréséhez használja az alábbi lépéseket:

1. Látogasson el a [az Azure portal](https://portal.azure.com) , és keresse meg a munkaterülethez.
2. Kattintson a __számítási__ mellett kapcsolni a __alkalmazások__ szakaszban.

    ![Nézet számítási lap](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Hozzon létre egy számítási célnak

A fenti lépésekkel számítási célnak listájának megtekintéséhez, és a következő lépések segítségével hozzon létre egy számítási célnak:

1. Kattintson a __+__ bejelentkezés hozzáadása egy számítási célnak.

    ![Számítás hozzáadása ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Adjon meg egy nevet a számítási célnak
1. Válassza ki **Machine Learning Compute** típusaként használandó számítási __képzés__

    > [!IMPORTANT]
    > Csak hozhat létre az Azure Machine Learning COMPUTE számítási, a felügyelt számítási képzéshez

1. Töltse ki az űrlap, különösen a Virtuálisgép-család és a csomópontok maximális száma a számítási mozgékonyságot használandó 
1. Kattintson a __Létrehozás__ elemre.
1. A létrehozási művelet állapotának megtekintéséhez a listából a számítási célnak

    ![Számítási listájának megtekintése](./media/how-to-set-up-training-targets/View_list.png)

1. Ekkor megjelenik a számítási célnak részleteit.

    ![Részletek](./media/how-to-set-up-training-targets/compute-target-details.png)

1. Most már küldhet egy Futtatás ezeken a célokon leírtaknak megfelelően a fenti ellen


### <a name="reuse-existing-compute-in-your-workspace"></a>Újból felhasználhatja a meglévő számítási a munkaterületen

A fenti lépésekkel számítási célnak listájának megtekintéséhez, majd kövesse az alábbi lépéseket a számítási célnak használja:

1. Kattintson a **+** bejelentkezés hozzáadása egy számítási célnak
2. Adjon meg egy nevet a számítási célnak
3. Válassza ki a számítási csatolni a __képzés__

    > [!IMPORTANT]
    > Nem az összes számítási típus lehet csatolni a portál használatával.
    > Jelenleg a képzéshez csatolható típusok a következők:
    > 
    > * Távoli virtuális Gépen
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. Töltse ki az űrlap

    > [!NOTE]
    > A Microsoft javasolja, hogy SSH-kulcsokat, mivel ezek biztonságosabb, mint a jelszavak. Jelszavak védtelenek a találgatásos támadások, miközben SSH-kulcsok titkosított aláírásokra támaszkodnak. Létrehozásával kapcsolatos információkat használja az SSH-kulcsok az Azure Virtual Machines a következő dokumentumokban talál:
    >
    > * [Hozzon létre és használhat SSH-kulcsokat a Linux vagy macOS rendszeren]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Hozzon létre és SSH-kulcsok használata a Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Válassza ki a csatolása
1. A csatolás művelet állapotának megtekintéséhez a listából a számítási célnak
1. Most már küldhet egy Futtatás ezeken a célokon leírtaknak megfelelően a fenti ellen

## <a name="examples"></a>Példák
Tekintse meg a következő helyeken notebookok:
* [útmutatóval-to-használat – azureml/képzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [oktatóanyagok és img-besorolás-1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Az Azure Machine Learning SDK-referencia](https://aka.ms/aml-sdk)
* [Oktatóanyag: A modell tanítása](tutorial-train-models-with-aml.md)
* [Az üzembe helyezés modellek](how-to-deploy-and-where.md)
* [Hozhat létre a machine learning-folyamatokat az Azure Machine Learning szolgáltatással](concept-ml-pipelines.md)
