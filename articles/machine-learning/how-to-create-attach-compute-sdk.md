---
title: Számítási erőforrások létrehozása a Python SDK-val
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning Python SDK segítségével képzési és üzembe helyezési számítási erőforrásokat (számítási célokat) hozhat létre a gépi tanuláshoz
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 96aa6839fe51bb8a8c26f411c1a1f9df6b8c5a7f
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147502"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Számítási célok létrehozása a modell betanításához és üzembe helyezéséhez a Python SDK-val

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben a számítási célok létrehozásához és kezeléséhez használja a Azure Machine Learning Python SDK-t. Számítási célokat a használatával is létrehozhat és kezelhet:
* [Azure Machine learning Studio](how-to-create-attach-compute-studio.md), 
* A Azure Machine Learning [CLI-bővítménye](reference-azure-machine-learning-cli.md#resource-management)
* A [vs Code bővítmény](how-to-manage-resources-vscode.md#compute-clusters) a Azure Machine Learninghoz.


## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes fiókot a virtuális gép létrehozásának megkezdése előtt. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma
* A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine learning munkaterület](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Mi a számítási cél?

A Azure Machine Learning segítségével különböző erőforrásokra és környezetekre is betaníthatja a modellt, együttesen [__számítási célok__](concept-azure-machine-learning-architecture.md#compute-targets)néven. A számítási cél lehet egy helyi gép vagy egy felhőalapú erőforrás, például egy Azure Machine Learning számítási, Azure-HDInsight vagy távoli virtuális gép.  Emellett számítási célokat is létrehozhat a modell telepítéséhez a ["hol és hogyan kell üzembe helyezni a modelleket"](how-to-deploy-and-where.md)című cikkben leírtak szerint.

## <a name="compute-targets-for-training"></a>Számítási célok képzéshez

A Azure Machine Learning különböző számítási célok esetében eltérő támogatással rendelkezik. Egy tipikus modell fejlesztési életciklusa kis mennyiségű adaton kezdi meg a fejlesztést és kísérletezést. Ebben a szakaszban a helyi környezet használatát javasoljuk. Például a helyi számítógép vagy egy felhőalapú virtuális gép. A nagyobb adatkészletek betanításának felskálázása vagy az elosztott képzések elvégzése során javasoljuk, hogy Azure Machine Learning számítás használatával hozzon létre egy olyan önálló vagy többcsomópontos fürtöt, amely minden egyes futtatásakor elküldi az autoskálázást. Saját számítási erőforrást is csatolhat, bár a különböző forgatókönyvek támogatása az alábbiakban részletezett módon változhat:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Azure Machine Learning számítási fürtök állandó erőforrásként hozhatók létre, vagy dinamikusan hozhatók létre futtatáskor. A futtatáson alapuló létrehozás eltávolítja a számítási célt a betanítási Futtatás befejezése után, így nem használhatja fel az így létrehozott számítási célokat.

Az alábbi lépésekkel konfigurálhatja ezeket a számítási célokat:

* [Helyi számítógép](#local)
* [Számítási fürt Azure Machine Learning](#amlcompute)
* [Azure Machine Learning számítási példány](#instance)
* [Távoli virtuális gépek](#vm)
* [Azure HDInsight](#hdinsight)


## <a name="local-computer"></a><a id="local"></a>Helyi számítógép

Ha a helyi számítógépet használja a betanításhoz, nem kell számítási célt létrehoznia.  Csak [küldje el a képzést](how-to-set-up-training-targets.md) a helyi gépről.

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Számítási fürt Azure Machine Learning

Azure Machine Learning számítási fürt felügyelt számítási infrastruktúra, amely lehetővé teszi, hogy egyszerűen hozzon létre egy vagy több csomópontos számítási módszert. A számítás a munkaterület-régión belül jön létre olyan erőforrásként, amely a munkaterület más felhasználóival is megoszthatók. A számítási feladatok automatikusan méretezhetők, ha egy feladatot elküldenek, és egy Azure-Virtual Network helyezhetők el. A számítás egy tároló környezetbe kerül, és a modell függőségeit egy [Docker-tárolóban](https://www.docker.com/why-docker)csomagolja.

A betanítási folyamat a felhőben lévő CPU-vagy GPU-alapú számítási csomópontok fürtön keresztüli elosztásához Azure Machine Learning számítást is használhat. A GPU-ket tartalmazó virtuálisgép-méretekkel kapcsolatos további információkért lásd: [GPU-optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

Azure Machine Learning a számítások alapértelmezett korlátai, például a lefoglalt magok száma. További információ: [Az Azure-erőforrások kezelése és kvóták igénylése](how-to-manage-quotas.md).

> [!TIP]
> A fürtök általában legfeljebb 100 csomópontot tudnak méretezni, ha elegendő kvóta van a szükséges magok számához. Alapértelmezés szerint a fürtök a csomópontok közötti kommunikációt engedélyezik a fürt csomópontjai között, így például támogatják az MPI-feladatokat. A fürtöket azonban a csomópontok 1000S is méretezheti úgy, hogy egyszerűen [egy támogatási jegyet emelnek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fel, és lehetővé teszi az előfizetés, a munkaterület vagy egy adott fürt számára a csomópontok közötti kommunikáció letiltását. 

Azure Machine Learning a számítások újra felhasználhatók a futtatások között. A számítás a munkaterület más felhasználóival is megoszthatók, és a futtatások között megmaradnak, automatikusan felfelé vagy lefelé skálázást, a csomópontok száma és a fürtön beállított max_nodes alapján. A min_nodes beállítás szabályozza a rendelkezésre álló minimális csomópontokat.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Létrehozás és csatolás**: állandó Azure Machine learning számítási erőforrás létrehozása Pythonban, a **vm_size** és a **max_nodes** tulajdonságainak megadása. A Azure Machine Learning ezután az intelligens alapértelmezett értékeket használja a többi tulajdonsághoz. A számítási műveletek nem a használat során nulla csomópontra vannak lebontva.   A dedikált virtuális gépek a feladatok igény szerinti futtatásához jönnek létre.
    
    * **vm_size**: a Azure Machine learning számítás által létrehozott csomópontok virtuálisgép-családja.
    * **max_nodes**: azon csomópontok maximális száma, amelyeknek az autoskálázása Azure Machine learning számítási feladatok futtatásakor.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Azure Machine Learning számítás létrehozásakor több speciális tulajdonság is konfigurálható. A tulajdonságok lehetővé teszik a rögzített méretű állandó fürt vagy az előfizetéshez tartozó meglévő Azure-Virtual Network létrehozását.  A részletekért tekintse meg a [AmlCompute osztályt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) .

    Emellett állandó Azure Machine Learning számítási erőforrást is létrehozhat és csatolhat [Azure Machine learning Studióban](how-to-create-attach-compute-studio.md#portal-create).

Most, hogy csatlakoztatta a számítást, a következő lépés a [betanítási Futtatás elküldése](how-to-set-up-training-targets.md).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Csökkentse a számítási fürt költségeit

Dönthet úgy is, hogy [alacsony prioritású virtuális gépeket](concept-plan-manage-cost.md#low-pri-vm) használ a számítási feladatok egy részének vagy egészének futtatásához. Ezek a virtuális gépek nem rendelkeznek garantált rendelkezésre állással, és a használat közben előzik is lehetnek. A rendszer újraindította a előzik-feladatot, és nem folytatja a műveletet. 

Egy alacsony prioritású virtuális gép megadásához használja a következő módszerek egyikét:
    
* A Studióban válassza az **alacsony prioritású** virtuális gép létrehozása lehetőséget.
    
* A Python SDK-val állítsa be az `vm_priority` attribútumot a létesítési konfigurációban.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* A CLI használatával állítsa be a `vm-priority` következőket:
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Felügyelt identitás beállítása

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Felügyelt identitás konfigurálása a létesítési konfigurációban:  
    
* Rendszerhez rendelt felügyelt identitás:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* Felhasználó által hozzárendelt felügyelt identitás: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

Felügyelt identitás hozzáadása meglévő számítási fürthöz:  
    
* Rendszer által hozzárendelt felügyelt identitás:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* Felhasználó által hozzárendelt felügyelt identitás:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>Felügyelt identitás használata

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Azure Machine Learning számítási példány

A [Azure Machine learning számítási példány](concept-compute-instance.md) felügyelt számítási infrastruktúra, amely lehetővé teszi egyetlen virtuális gép egyszerű létrehozását. A számítás a munkaterület-régión belül jön létre, de a számítási fürttől eltérően a példányok nem oszthatók meg a munkaterület más felhasználóival. A példány nem méretezhető le automatikusan.  A folyamatos költségek elkerülése érdekében le kell állítania az erőforrást.

Egy számítási példány több feladatot is futtathat párhuzamosan, és feladat-várólistával rendelkezik. 

A számítási példányok biztonságosan futtathatnak feladatokat egy [virtuális hálózati környezetben](how-to-enable-virtual-network.md#compute-instance)anélkül, hogy a vállalatoknak SSH-portokat kellene megnyitnia. A feladatot egy tároló környezetben hajtja végre a rendszer, és a modell függőségeit egy Docker-tárolóban csomagolja. 

1. **Létrehozás és csatolás**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás beküldése](how-to-set-up-training-targets.md)


## <a name="remote-virtual-machines"></a><a id="vm"></a>Távoli virtuális gépek

A Azure Machine Learning a saját számítási erőforrásait is támogatja, és csatolja azt a munkaterülethez. Az egyik ilyen erőforrástípus egy tetszőleges távoli virtuális gép, feltéve, hogy az Azure Machine Learningről érhető el. Az erőforrás lehet egy Azure-beli virtuális gép, egy távoli kiszolgáló a szervezetben vagy a helyszínen. Pontosabban, mivel az IP-cím és a hitelesítő adatok (Felhasználónév és jelszó, vagy SSH-kulcs) esetében bármilyen elérhető virtuális gép használható távoli futtatáshoz.

Használhat egy rendszer által készített Conda-környezetet, egy már meglévő Python-környezetet vagy egy Docker-tárolót. A Docker-tárolón való végrehajtáshoz a virtuális gépen futó Docker-motorral kell rendelkeznie. Ez a funkció különösen akkor hasznos, ha rugalmasabb, felhőalapú fejlesztési/kísérletezési környezetet szeretne használni, mint a helyi gépen.

Ebben a forgatókönyvben az Azure Data Science Virtual Machine (DSVM) használata választható Azure-beli virtuális gép. Ez a virtuális gép egy előre konfigurált adatelemzési és AI-fejlesztési környezet az Azure-ban. A virtuális gép számos eszközt és keretrendszert kínál a teljes életciklusú gépi tanulás fejlesztéséhez. A DSVM és a Azure Machine Learning használatával kapcsolatos további információkért lásd: [fejlesztési környezet konfigurálása](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Létrehozás**: hozzon létre egy DSVM, mielőtt a modellt betanítani. Az erőforrás létrehozásával kapcsolatban tekintse meg [a Linux (Ubuntu) Data Science Virtual Machine kiépítése](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)című témakört.

    > [!WARNING]
    > Azure Machine Learning csak az **Ubuntut**futtató virtuális gépeket támogatja. Amikor létrehoz egy virtuális gépet, vagy egy meglévő virtuális gépet választ, ki kell választania egy Ubuntut használó virtuális gépet.
    > 
    > A Azure Machine Learning azt is megköveteli, hogy a virtuális gép __nyilvános IP-címmel__rendelkezzen.

1. **Csatolás**: Ha egy meglévő virtuális gépet számítási célként szeretne csatolni, meg kell adnia a virtuális gép erőforrás-azonosítóját, felhasználónevét és jelszavát. A virtuális gép erőforrás-azonosítója az előfizetés-azonosító, az erőforráscsoport neve és a virtuális gép neve alapján hozható létre a következő karakterlánc-formátum használatával: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   A DSVM a munkaterülethez [Azure Machine learning Studio használatával](how-to-create-attach-compute-studio.md#attached-compute)is csatlakoztathatja.

1. **Konfigurálás**: hozzon létre egy futtatási konfigurációt a DSVM számítási célhoz. A Docker és a Conda a DSVM lévő képzési környezet létrehozásához és konfigurálásához használható.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](how-to-set-up-training-targets.md).

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Az Azure HDInsight egy népszerű platform a Big-adatelemzéshez. A platform Apache Spark biztosít, amely a modell betanítására használható.

1. **Létrehozás**: hozza létre a HDInsight-fürtöt, mielőtt felhasználja a modell betanításához. A Spark on HDInsight-fürt létrehozásával kapcsolatban lásd: [Spark-fürt létrehozása a HDInsight-ben](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    > [!WARNING]
    > Azure Machine Learning megköveteli, hogy a HDInsight-fürt __nyilvános IP-címmel__rendelkezzen.

    A fürt létrehozásakor meg kell adnia egy SSH-felhasználónevet és-jelszót. Jegyezze fel ezeket az értékeket, mert szüksége lesz rájuk a HDInsight számítási célként való használatához.
    
    A fürt létrehozása után kapcsolódjon hozzá az állomásnév \<clustername> -SSH.azurehdinsight.net, ahol a a \<clustername> fürthöz megadott név. 

1. **Csatolás**: Ha egy HDInsight-fürtöt számítási célként kíván csatolni, meg kell adnia a HDInsight-fürt erőforrás-azonosítóját, felhasználónevét és jelszavát. A HDInsight-fürt erőforrás-azonosítója az előfizetés-azonosító, az erőforráscsoport neve és a HDInsight-fürt neve alapján hozható létre a következő karakterlánc-formátum használatával: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

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

   A HDInsight-fürtöt [Azure Machine learning Studio használatával](how-to-create-attach-compute-studio.md#attached-compute)is csatlakoztathatja a munkaterülethez.

1. **Konfigurálás**: hozzon létre egy futtatási konfigurációt a HDI számítási célhoz. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch a nagy léptékű párhuzamos és nagy teljesítményű számítástechnikai (HPC) alkalmazások felhőben történő futtatására szolgál. A AzureBatchStep Azure Machine Learning folyamatokban felhasználható feladatok Azure Batch készletbe való elküldéséhez.

Azure Batch számítási célként való csatolásához a Azure Machine Learning SDK-t kell használnia, és meg kell adnia a következő információkat:

-    **Azure batch számítási név**: a munkaterületen belüli számításhoz használandó felhasználóbarát név
-    **Azure batch fiók neve**: a Azure batch fiók neve
-    **Erőforráscsoport**: az Azure batch fiókot tartalmazó erőforráscsoport.

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

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

A Azure Databricks egy Apache Spark-alapú környezet az Azure-felhőben. A számítási célként Azure Machine Learning folyamattal is használható.

Használat előtt hozzon létre egy Azure Databricks munkaterületet. Munkaterület-erőforrás létrehozásához tekintse [meg a Spark-feladatok futtatása Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentumon című témakört.

Azure Databricks számítási célként való csatolásához adja meg a következő információkat:

* __Databricks számítási név__: a számítási erőforráshoz hozzárendelni kívánt név.
* __Databricks-munkaterület neve__: az Azure Databricks munkaterület neve.
* __Databricks hozzáférési jogkivonat__: a Azure Databricks hitelesítéséhez használt hozzáférési jogkivonat. Hozzáférési jogkivonat létrehozásához tekintse meg a [hitelesítési](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) dokumentumot.

Az alábbi kód azt mutatja be, hogyan csatolható Azure Databricks számítási célként a Azure Machine Learning SDK-val (__az Databricks-munkaterületnek ugyanabban az előfizetésben kell lennie, mint a pénzmosás-munkaterület__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Részletesebb példaként tekintse meg a GitHubon egy [példát a notebookra](https://aka.ms/pl-databricks) .

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

A Azure Data Lake Analytics egy big data elemzési platform az Azure-felhőben. A számítási célként Azure Machine Learning folyamattal is használható.

Használat előtt hozzon létre egy Azure Data Lake Analytics fiókot. Az erőforrás létrehozásához tekintse meg az [Ismerkedés a Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentummal című témakört.

Data Lake Analytics számítási célként való csatolásához a Azure Machine Learning SDK-t kell használnia, és meg kell adnia a következő információkat:

* __Számítási név__: a számítási erőforráshoz hozzárendelni kívánt név.
* __Erőforráscsoport__: az Data Lake Analytics fiókot tartalmazó erőforráscsoport.
* __Fiók neve__: a Data Lake Analytics fiók neve.

A következő kód bemutatja, hogyan csatolhatja Data Lake Analytics számítási célként:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Részletesebb példaként tekintse meg a GitHubon egy [példát a notebookra](https://aka.ms/pl-adla) .

> [!TIP]
> Azure Machine Learning folyamatok csak az Data Lake Analytics-fiók alapértelmezett adattárában tárolt adatmennyiségek esetében használhatók. Ha a működéséhez szükséges adatmennyiség nem alapértelmezett tárolóban található, akkor az a használatával [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) másolhatja az Adatmásolást a betanítás előtt.

## <a name="notebook-examples"></a>Jegyzetfüzet-példák

Tekintse meg ezeket a jegyzetfüzeteket a különböző számítási célokból származó képzésekre:
* [használati útmutató – azureml/képzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [oktatóanyagok/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések

* A számítási erőforrás segítségével [elküldheti a betanítási futtatást](how-to-set-up-training-targets.md).
* [Oktatóanyag: a betanítási modell](tutorial-train-models-with-aml.md) felügyelt számítási célt használ a modellek betanításához.
* Ismerje meg, hogy miként lehet [hatékonyan hangolni a hiperparaméterek beállítása](how-to-tune-hyperparameters.md) a jobb modellek létrehozásához.
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe a modelleket](how-to-deploy-and-where.md).
* [Azure Machine Learning használata az Azure Virtual Networks használatával](how-to-enable-virtual-network.md)
