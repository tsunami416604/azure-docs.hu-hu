---
title: Létrehozhat és használhat a számítási célokhoz modell betanítása
titleSuffix: Azure Machine Learning service
description: Konfigurálja a machine learning-modell betanítása (számítási céljainak) képzési környezet. Könnyedén válthat képzési környezetek között. Indítsa el a helyi képzés. Ha horizontális felskálázásra van szüksége, váltson a felhőalapú számítási célt.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/07/2019
ms.custom: seodec18
ms.openlocfilehash: a549a46912b0d60f878a18cae1e70a763afc0243
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874698"
---
# <a name="set-up-compute-targets-for-model-training"></a>Állítsa be a modell betanítása és számítási célnak

Az Azure Machine Learning szolgáltatás a modell a különböző erőforrások vagy környezetek, együttesen a betaníthatja [ __számítási céljainak__](concept-azure-machine-learning-architecture.md#compute-target). Egy számítási célnak lehet egy helyi számítógépre vagy egy felhőalapú erőforrás, például az Azure Machine Learning COMPUTE számítási, az Azure HDInsight vagy egy távoli virtuális gépen.  Is létrehozhat a modell üzembe helyezése számítási célnak leírtak szerint [", és a modellek üzembe helyezése"](how-to-deploy-and-where.md).

Hozzon létre, és a egy számítási célnak, az Azure Machine Learning SDK, az Azure Portalon vagy az Azure CLI használata kezelheti. Ha egy másik szolgáltatás (például egy HDInsight-fürtöt) használatával létrehozott számítási célnak, használhatja őket az Azure Machine Learning szolgáltatás munkaterületén csatolásával.
 
Ebben a cikkben megismerheti, hogyan használható a különböző számítási célnak modell betanítása.  Minden számítási célnak vonatkozó lépéseket kövesse az ugyanabban a munkafolyamatban:
1. __Hozzon létre__ egy számítási célnak, ha még nem rendelkezik.
2. __Csatolása__ a számítási célnak a munkaterülethez.
3. __Konfigurálása__ számítási cél, hogy a parancsfájl által igényelt Python környezet és a csomag függőségeit tartalmazza.


>[!NOTE]
> Ebben a cikkben kód az Azure Machine Learning SDK-val 1.0.6-os lett tesztelve.

## <a name="compute-targets-for-training"></a>Számítási céljainak képzéshez

Az Azure Machine Learning szolgáltatás különböző támogatással rendelkezik a különböző számítási célnak között. Egy tipikus modell fejlesztési életciklus kisebb mennyiségű adatot a dev/Kísérletezési kezdődik. Ezen a ponton használatát javasoljuk a helyi környezetben. Például a helyi számítógépen vagy egy felhőalapú virtuális Gépen. Vertikális felskálázás a tanítási a nagyobb adatkészletek, vagy hajtsa végre az elosztott betanítás, egy vagy több node fürtöt létrehozni, hogy az automatikus skálázást alkalmat futtató minden elküldésekor a az Azure Machine Learning Compute használatát javasoljuk. Bár a különböző forgatókönyvekben eltérőek lehetnek az alábbiakban ismertetett támogatási is hozzáadhat a saját számítási erőforrás:


|Számítási célt képzéshez| GPU-gyorsítás | Automatikus<br/> hiperparaméter finomhangolása | Automatikus</br> gépi tanulás | Az Azure Machine Learning-folyamatokat |
|----|:----:|:----:|:----:|:----:|
|[Helyi számítógép](#local)| Talán | &nbsp; | ✓ | &nbsp; |
|[Az Azure Machine Learning Compute](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Távoli virtuális Gépen](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](how-to-create-your-first-pipeline.md#databricks)| &nbsp; | &nbsp; | ✓ | ✓ |
|[Az Azure Data Lake Analytics](how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Az Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Azure Batch](#azbatch)| &nbsp; | &nbsp; | &nbsp; | ✓ |

**Célok több betanítási feladatokhoz felhasználható számítási**. Például ha csatlakoztat egy távoli virtuális Gépen a munkaterülethez, felhasználhatja azt több feladat esetében.

> [!NOTE]
> Az Azure Machine Learning Compute létrehozhatják állandó erőforrásként vagy dinamikusan létrehozott, egy Futtatás kérés. Futtatás-alapú létrehozása eltávolítja a számítási célnak, a betanítási Futtatás befejezése után, ezért az ilyen módon létrehozott számítási célnak nem használhat újra.

## <a name="whats-a-run-configuration"></a>Mi az, hogy egy futtatási konfigurációt?

Amikor képzésekről, indítsa el a helyi számítógépen, és később futhatnak a tanítási szkriptet a különböző számítási céloknak közös. Az Azure Machine Learning szolgáltatás a szkriptet futtathatja a különböző számítási célnak a parancsfájl módosítása nélkül. 

Ehhez szüksége a környezet létrehozása az egyes számítási célnak a egy **futtatási konfigurációt**.  Ezt követően amikor szeretné futtatni a betanítási kísérlet a különböző számítási célt, adja meg az adott számítási futtatási beállításait. 

Tudjon meg többet [kísérletek elküldése](#submit) Ez a cikk végén található.

### <a name="manage-environment-and-dependencies"></a>Környezet és a függőségek kezelése

Amikor létrehoz egy futtatási konfigurációt, kell annak eldöntése, hogyan kezelheti a környezet és a számítási célnak a függőségeket. 

#### <a name="system-managed-environment"></a>A rendszer által felügyelt környezetben

A rendszer által felügyelt környezetben használja, ha azt szeretné, [Conda](https://conda.io/docs/) segítségével kezelheti a Python-környezetet és a parancsfájl-függőségeket. A rendszer által felügyelt környezet alapértelmezés szerint és a leggyakoribb megoldás feltételezi. Ez hasznos a távoli számítási célokhoz, különösen akkor, ha a cél nem konfigurálható. 

Adja meg az egyes függőségi használatával mást nem kell tennie a [CondaDependency osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) Conda-majd létrehoz egy fájlt **conda_dependencies.yml** a a **aml_config** a könyvtár a munkaterületen, a függőségek csomagolása és állítja be a Python-környezetében, a betanítási kísérlet elküldésekor a listája. 

A kezdeti beállítás új környezet a szükséges függőségek méretétől függően több percig is eltarthat. Mindaddig, amíg változatlan marad a csomagok listájában, a telepítési idő csak egyszer történik.
  
Az alábbi kód példa scikit igénylő rendszer által felügyelt környezetben – ismerje meg:
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Felhasználó által felügyelt környezetben

Felhasználó által felügyelt környezetben Ön felelős egy környezet felépítésének lépésein, és a számítási célnak a tanítási szkriptet van szüksége minden csomag telepítését. Ha a képzési környezet már konfigurálva van (például a helyi gépen), akkor kihagyhatja a telepítő beállításával `user_managed_dependencies` igaz értékre. Conda nem ellenőrzése a környezetben, vagy az Ön számára telepít semmit.

A következő kód bemutatja egy példa az egy felhasználó által felügyelt környezetben a betanítási Futtatás:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-compute-targets-with-python"></a>Állítsa be a Python használatával számítási célnak

Használja a számítási céljainak konfigurálhatja ezeket az alábbi szakaszok:

* [Helyi számítógép](#local)
* [Az Azure Machine Learning Compute](#amlcompute)
* [Távoli virtuális gépek](#vm)
* [Az Azure HDInsight](#hdinsight)


### <a id="local"></a>Helyi számítógép

1. **Létrehozásához és csatolásához**: Hiba esetén nem kell létrehozni, vagy csatlakoztassa egy számítási célnak, használhatja a helyi számítógépet képzési környezet.  

1. **Konfigurálása**:  Amikor számítási célt használja a helyi számítógépen, a képzési kódot fut-e a a [fejlesztési környezet](how-to-configure-environment.md).  Ha abban a környezetben már van szüksége, használja a felhasználó által felügyelt környezetben a Python-csomagokat.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Most, hogy a számítási csatolva, és konfigurálni a Futtatás, a következő lépés, hogy [küldje el a betanítási Futtatás](#submit).

### <a id="amlcompute"></a>Az Azure Machine Learning Compute

Az Azure Machine Learning Compute egy felügyelt számítási infrastruktúra, amely lehetővé teszi a felhasználó hozhat létre egyszerűen egy egyetlen vagy több csomópontos számítási. A számítási erőforrásai, amelyek megoszthatók más felhasználókkal a munkaterületen a munkaterület régión belül jön létre. A számítási felskálázással automatikusan egy feladat elküldésekor, és elhelyezheti egy Azure virtuális hálózaton. A számítási végrehajtja a tárolóalapú környezetben, és a modell függőségek, a csomagok egy [Docker-tároló](https://www.docker.com/why-docker).

Használhatja az Azure Machine Learning Compute a betanítási folyamat szét a felhőben Processzor és GPU számítási csomópontból álló fürtben. További információ a GPU-kat tartalmazó Virtuálisgép-méretek: [GPU-optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Az Azure Machine Learning Compute esetében alapértelmezett korlátozások, például, amely kiosztható magok számát. További információkért lásd: [az Azure-erőforrások kezelése és a kérés kvóták](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Az Azure Machine Learning számítási környezetet hozhat létre, ha ütemez egy Futtatás igény szerint, vagy állandó erőforrásként.

#### <a name="run-based-creation"></a>Futtatás-alapú létrehozása

Hozhat létre az Azure Machine Learning Compute számítási célként futási időben. A számítási automatikusan létrejön a futtatáskor. A fürt skálázását követve rugalmasan méretezhető száma legfeljebb **max_nodes** , amelyeket a futtatási konfiguráció. A Futtatás befejeződése után a rendszer automatikusan törli a számítást.

> [!IMPORTANT]
> Futtatás-alapú létrehozása az Azure Machine Learning compute jelenleg előzetes verzióban érhető el. Ne használja a run-alapú létrehozása, ha automatizált hiperparaméter finomhangolása használja, vagy az automatizált a machine learning. Hiperparaméter finomhangolása vagy automatizált a machine learning használatához hozzon létre egy [állandó számítási](#persistent) cél helyette.

1.  **Hozzon létre, csatlakoztathat, valamint**: A run-alapú létrehozása hajt végre a szükséges lépéseket létrehozása, csatlakoztatása és konfigurálja a számítási célnak a futtatási konfigurációt.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Most, hogy a számítási csatolva, és konfigurálni a Futtatás, a következő lépés, hogy [küldje el a betanítási Futtatás](#submit).

#### <a id="persistent"></a>Állandó számítási

Egy állandó Azure Machine Learning COMPUTE számítási feladatok felhasználhatók. A számítási megoszthatók más felhasználókkal a munkaterületen, és a feladatok között megőrzi.

1. **Létrehozásához és csatolásához**: Állandó Azure Machine Learning COMPUTE számítási erőforrás létrehozása pythonban, adja meg a **vm_size** és **max_nodes** tulajdonságait. Az Azure Machine Learning intelligens alapértelmezett beállítások ezután használja a többi tulajdonság esetén. A számítási automatikus skálázást alkalmat lefelé nulla csomópontokat, amikor nincs használatban.   A feladatok futtatását, igény szerint dedikált virtuális gépek jönnek létre.
    
    * **vm_size**: A hozott létre az Azure Machine Learning COMPUTE számítási csomópontok Virtuálisgép-család.
    * **max_nodes**: Az automatikus méretezés akár amikor futtat egy feladatot az Azure Machine Learning COMPUTE számítási csomópontok maximális száma.
    
 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

  Néhány speciális tulajdonságok konfigurálhatja az Azure Machine Learning Compute létrehozásakor is. A tulajdonságok lehetővé teszik a rögzített méretű, vagy egy meglévő Azure virtuális hálózaton belül állandó fürt létrehozása az előfizetésében.  Tekintse meg a [AmlCompute osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) részleteiről.
    
 Vagy hozzon létre és a egy állandó Azure Machine Learning COMPUTE számítási erőforrás csatolása [az Azure Portalon](#portal-create).

1. **Konfigurálása**: Hozzon létre egy futtatási konfigurációt az állandó számítási célnak számára.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Most, hogy a számítási csatolva, és konfigurálni a Futtatás, a következő lépés, hogy [küldje el a betanítási Futtatás](#submit).


### <a id="vm"></a>Távoli virtuális gépek

Az Azure Machine Learning a történő visszaállítását a saját számítási erőforrásokat, és a munkaterülethez csatolja azt is támogatja. Egy ilyen erőforrástípus egy tetszőleges távoli virtuális gép, mindaddig, amíg az Azure Machine Learning szolgáltatásból származó érhető el. Az erőforrás lehet egy Azure virtuális Gépen, a szervezet, vagy a helyszínen egy távoli kiszolgálóra. Pontosabban adja meg az IP-cím és a hitelesítő adatok (felhasználónév és jelszó vagy SSH-kulcs), használhatja bármely elérhető virtuális gép távoli futtatások.

Egy rendszer által fejlesztett conda-környezetben, egy már meglévő Python-környezetet, vagy egy Docker-tárolót is használhatja. Hajtsa végre a Docker-tárolóban, a virtuális gépen a Docker-motor kell rendelkeznie. Ez a funkció akkor különösen hasznos, ha egy olyan rugalmasabb, felhőalapú fejlesztési/kísérleti környezetben, mint a helyi gépen.

Az Azure adatelemzési virtuális gép (DSVM) az Azure virtuális gépként választott használata ebben a forgatókönyvben. Ez a virtuális gép egy előre konfigurált adatelemzés és a mesterséges Intelligencia fejlesztési környezetet az Azure-ban. A virtuális gép egy válogatott választott eszközök és a teljes-életciklus machine learning-fejlesztési keretrendszerek kínál. A dsvm-hez az Azure Machine Learning használatával további információkért lásd: [a fejlesztési környezet konfigurálása](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Hozzon létre**: Hozzon létre egy adatelemző virtuális GÉPET és a modell betanításához. Ez az erőforrás létrehozásához lásd: [az adatelemző virtuális gép kiépítése Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Az Azure Machine Learning csak az Ubuntu rendszerű virtuális gépeket támogatja. Hozzon létre egy virtuális Gépet, vagy válasszon egy meglévő virtuális Gépet, ki kell választania egy Ubuntu használó virtuális gép.

1. **Csatolása**: Egy számítási célnak egy meglévő virtuális géphez csatolni kell adnia a teljesen minősített tartománynevét (FQDN), a felhasználónév és a jelszót a virtuális gép. Cserélje le a példában \<teljesen minősített tartományneve > az a virtuális gép vagy a nyilvános IP-cím nyilvános teljes Tartománynevét. Cserélje le \<username > és \<jelszó > SSH-felhasználónevet és jelszót a virtuális gép.

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

 A DSVM csatlakoztathat a munkaterület vagy [az Azure portal használatával](#portal-reuse).

1. **Konfigurálása**: Hozzon létre egy futtatási konfigurációt a DSVM számítási célnak számára. A docker és a conda létrehozása és a képzési környezet konfigurálása a DSVM szolgálnak.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Most, hogy a számítási csatolva, és konfigurálni a Futtatás, a következő lépés, hogy [küldje el a betanítási Futtatás](#submit).

### <a id="hdinsight"></a>Az Azure HDInsight 

Az Azure HDInsight egy népszerű big-data elemzési platform. A platform az Apache Spark, amely a modell betanításához használható biztosítja.

1. **Hozzon létre**:  A HDInsight-fürt létrehozása, előtt a modell betanításához. A Spark HDInsight-fürt létrehozásához lásd: [a HDInsight Spark-fürt létrehozása](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    A fürt létrehozásakor egy SSH-felhasználónevet és jelszót kell megadnia. Jegyezze fel ezeket az értékeket, és igény szerint HDInsight számítási célként használandó.
    
    Miután a fürt létrejött, csatlakozzon hozzá az állomásnévvel \<clustername >-ssh.azurehdinsight.net, ahol \<clustername > a neve, a fürt számára megadott. 

1. **Csatolása**: Egy HDInsight-fürt egy számítási célnak csatolásához kell megadnia az állomásnevet, felhasználónév és jelszó a HDInsight-fürt. Az alábbi példa egy fürt csatlakoztatása a munkaterület az SDK-t használja. Cserélje le a példában \<clustername > az a fürt nevére. Cserélje le \<username > és \<jelszó > az SSH-felhasználónév és a fürthöz tartozó jelszót.

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

  Vagy a HDInsight-fürt csatlakoztathat a munkaterület [az Azure portal használatával](#portal-reuse).

1. **Konfigurálása**: Hozzon létre egy futtatási konfigurációt a HDI számítási célnak számára. 

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Most, hogy a számítási csatolva, és konfigurálni a Futtatás, a következő lépés, hogy [küldje el a betanítási Futtatás](#submit).


### <a id="azbatch"></a>Azure Batch 

Az Azure Batch segítségével hatékonyan futtathat nagy méretű párhuzamos és nagy teljesítményű feldolgozási (HPC) alkalmazásokat a felhőben. Az Azure Machine Learning folyamat küldhetők be feladatok a gépek Azure Batch-készlet AzureBatchStep is használható.

Csatlakoztassa az Azure Batch számítási célként, az Azure Machine Learning SDK, és adja meg a következő információkat:

-   **Az Azure Batch számítási neve**: Egy rövid nevet a számítási erőforrások a munkaterületen belül használható
-   **Az Azure Batch-fiók nevének**: Az Azure Batch-fiók neve
-   **Erőforráscsoport**: Az Azure Batch-fiókot tartalmazó erőforráscsoportot.

A következő kód bemutatja, hogyan csatlakoztathat Azure Batch számítási célként mutat be:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

batch_compute_name = 'mybatchcompute' # Name to associate with new compute in workspace

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>" # Name of the Batch account
batch_resource_group = "<batch_resource_group>" # Name of the resource group which contains this account

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-compute-in-the-azure-portal"></a>Az Azure Portalon számítási beállítása

Elérheti a számítási célokhoz, amely az Azure Portalon a munkaterülethez lesznek társítva.  A portálon is használhatja:

* [Nézet számítási céljainak](#portal-view) csatlakoztatva a munkaterülethez
* [Hozzon létre egy számítási célnak](#portal-create) a munkaterületen
* [Egy számítási célnak csatolása](#portal-reuse) , amely a munkaterületen kívül lett létrehozva

Miután a tároló létrehozása és csatlakoztatása a munkaterülethez, használja azt a futtatási konfigurációt a `ComputeTarget` objektum: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Nézet számítási célnak


A számítási célokhoz, a munkaterület megtekintéséhez használja az alábbi lépéseket:

1. Keresse meg a [az Azure portal](https://portal.azure.com) , és nyissa meg a munkaterületet. 
1. A __alkalmazások__válassza __számítási__.

    ![Nézet számítási lap](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>Hozzon létre egy számítási célnak

Az előző lépésekkel számítási célnak listájának megtekintéséhez. Ezek a lépések használatával hozzon létre egy számítási célnak: 

1. Válassza a pluszjelet (+) számítási célt.

    ![Adjon hozzá egy számítási célnak](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Adjon meg egy nevet a számítási célnak. 

1. Válassza ki **Machine Learning Compute** típusaként használandó számítási __képzési__. 

    >[!NOTE]
    >Az Azure Portalon hozhat létre csak felügyelt számítási erőforrásokat az Azure Machine Learning Compute.  Minden egyéb számítási erőforrások csatolható a létrehozásuk után.

1. Az űrlap kitöltésekor. Adja meg a szükséges tulajdonságokat, különösen **Virtuálisgép-család**, és a **csomópontok maximális** használata a számítási üzembe helyezését.  

    ![Töltse ki a kérdőívet](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. Kattintson a __Létrehozás__ gombra.


1. A létrehozási művelet állapotának megtekintéséhez válassza ki a számítási célnak a listában:

    ![Válassza ki a létrehozási művelet állapotának megtekintéséhez egy számítási célnak](./media/how-to-set-up-training-targets/View_list.png)

1. Ekkor megjelenik a számítási célnak részleteit: 

    ![A számítógép cél részleteinek megtekintése](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>Számítási célnak csatolása

Az Azure Machine Learning szolgáltatás munkaterület alkalmazáson kívül létrehozott számítási célnak használatához csatolja őket. Egy számítási célnak csatolása tesz elérhetővé a munkaterülethez.

A számítási célokhoz listájának megtekintéséhez a korábban ismertetett lépésekkel. A következő lépések segítségével csatolása számítási célt: 

1. Válassza a pluszjelet (+) számítási célt. 
1. Adjon meg egy nevet a számítási célnak. 
1. Válassza ki a számítási csatolni a __képzési__:

    > [!IMPORTANT]
    > Nem minden számítási típusok is csatlakoztatható az Azure Portalról. Jelenleg a betanításhoz csatolható a számítási típusok a következők:
    >
    > * Egy távoli virtuális Gépen
    > * Az Azure Databricks (használatra a machine learning-folyamatokat)
    > * Az Azure Data Lake Analytics (használatra a machine learning-folyamatokat)
    > * Azure HDInsight

1. Töltse ki az űrlapot, és adjon meg értékeket a szükséges tulajdonságokat.

    > [!NOTE]
    > A Microsoft javasolja, hogy SSH-kulcsok biztonságosabbak, mint a jelszavak. Jelszavak védtelenek a találgatásos támadásokkal szemben. Az SSH-kulcsok titkosított aláírásokra támaszkodnak. SSH-kulcsokat használja az Azure Virtual Machines, a létrehozásával kapcsolatos információkat lásd az alábbi dokumentumokat:
    >
    > * [Hozzon létre és használhat SSH-kulcsokat a Linux vagy macOS rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Hozzon létre és SSH-kulcsok használata a Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Válassza ki __csatolása__. 
1. A csatolás művelet állapotának megtekintéséhez a számítási célnak a listából.

## <a name="set-up-compute-with-the-cli"></a>A CLI-vel számítási beállítása

A számítási célokhoz, a munkaterület használatával társított érheti el a [CLI-bővítmény](reference-azure-machine-learning-cli.md) Azure Machine Learning szolgáltatás.  A parancssori felület is használhatja:

* Hozzon létre egy felügyelt számítási célnak
* Frissítés a felügyelt számítási célnak
* Egy nem felügyelt számítási célnak csatolása

További információkért lásd: [erőforrás-kezelés](reference-azure-machine-learning-cli.md#resource-management).


## <a id="submit"></a>Küldje el a betanítási Futtatás

Miután létrehozott egy futtatási konfigurációt, használhatja azt a kísérlet futtatásához.  A kód a minta elküldeni a betanítási Futtatás megegyezik a számítási tárolók összes típusára vonatkozóan:

1. Hozzon létre egy kísérlet futtatása
1. Küldje el a futtatást.
1. Várjon, amíg a Futtatás befejeződik.

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Először hozzon létre egy kísérletet a munkaterületén.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>A kísérlet elküldése

A kísérlet elküldése egy `ScriptRunConfig` objektum.  Ez az objektum tartalmazza a:

* **source_directory**: A forráskönyvtár a tanítási szkriptet tartalmazó
* **parancsfájl**: A tanítási szkriptet azonosító
* **run_config**: A futtatási konfigurációtól, amely viszont meghatározza, hol történik a képzés.

A betanítási Futtatás elküldésekor egy pillanatképet a betanítási szkriptekhez tartalmazó könyvtárba, és a számítási elküld. További információkért lásd: [pillanatképek](concept-azure-machine-learning-architecture.md#snapshot).

Használja például [a helyi tároló](#local) konfiguráció:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Váltson egy másik futtatási konfigurációt, például a különböző számítási célt futtatását ugyanazon kísérletben a [amlcompute cél](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

Vagy használhatja:

* A kísérlet elküldése egy `Estimator` , ahogyan az objektum [estimators Train gépi Tanulási modelleket](how-to-train-ml-models.md). 
* Egy kísérlet elküldésére [a CLI-bővítmény használata](reference-azure-machine-learning-cli.md#experiments).

## <a name="notebook-examples"></a>A jegyzetfüzet-példák

Tekintse meg ezeket a notebookokat-betanítás a különböző számítási célnak példákat:
* [útmutatóval-to-használat – azureml/képzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [oktatóanyagok és img-besorolás-1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: A modell betanítását](tutorial-train-models-with-aml.md) felügyelt számítási célt használja egy modell betanításához.
* Miután a betanított modell, további [hogyan és hol helyezhet üzembe modelleket](how-to-deploy-and-where.md).
* Nézet a [RunConfiguration osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK-leírás.
* [Az Azure Machine Learning szolgáltatás használata az Azure Virtual Network szolgáltatással](how-to-enable-virtual-network.md)
