---
title: Fejlesztési környezet beállítása | Python
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állíthat be Azure Machine Learning Python-fejlesztési környezetet. Használjon Conda-környezeteket, hozzon létre konfigurációs fájlokat, és konfigurálja saját felhőalapú notebook-kiszolgálóját, a Jupyter notebookokat, az Azure Databricks, az ide, a Code Editort és a Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 54c607ebac02a9d7e534d24656a8687e9ff39725
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533179"
---
# <a name="set-up-a-development-environment-for-azure-machine-learning"></a>Fejlesztési környezet beállítása Azure Machine Learninghoz

Megtudhatja, hogyan konfigurálhat Azure Machine Learning Python-fejlesztési környezetet.

A következő táblázat a jelen cikkben tárgyalt összes fejlesztési környezetet tartalmazza, valamint az előnyeit és hátrányait.

| Környezet | Előnyök | Hátrányok |
| --- | --- | --- |
| [Helyi környezet](#local) | A fejlesztési környezet és a függőségek teljes körű vezérlése. Bármilyen felépíthető eszközzel, környezettel vagy tetszőleges IDE-val futtatható. | A kezdéshez tovább tart. Telepíteni kell a szükséges SDK-csomagokat, és a környezetnek is telepítve kell lennie, ha még nem rendelkezik ilyennel. |
| [Azure Machine Learning számítási példány](#compute-instance) | A legegyszerűbb módszer a kezdéshez. A teljes SDK már telepítve van a munkaterület virtuális gépén, és a jegyzetfüzet-oktatóanyagok előre klónozottak, és készen állnak a futtatásra. | A fejlesztési környezet és a függőségek szabályozásának hiánya. A Linux rendszerű virtuális gépekkel kapcsolatos további költségek (a virtuális gép leállítható, ha nem használatban van a költségek elkerülése érdekében). Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Azure Databricks](#aml-databricks) | Ideális megoldás a méretezhető Apache Spark platformon nagy léptékű, intenzív gépi tanulási munkafolyamatok futtatására. | A kísérleti gépi tanulás vagy a kisebb léptékű kísérletek és munkafolyamatok meggyilkolása. További költségek Azure Databricksért. Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/databricks/). |
| [A Data Science Virtual Machine (DSVM)](#dsvm) | A felhőalapú számítási példányhoz hasonlóan (a Python és az SDK előre telepítve van), de további népszerű adatelemzési és gépi tanulási eszközökkel előre telepítve van. Egyszerűen méretezhető és kombinálható más egyéni eszközökkel és munkafolyamatokkal. | Lassabban megkezdhető a felhőalapú számítási példányhoz képest. |

Ez a cikk további használati tippeket is tartalmaz a következő eszközökhöz:

* Jupyter jegyzetfüzetek: Ha már használ Jupyter jegyzetfüzeteket, akkor az SDK-nak néhány extrával kell telepítenie.

* Visual Studio code: Ha Visual Studio Code-t használ, a [Azure Machine learning bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) kiterjedt nyelvi támogatást nyújt a Pythonhoz, valamint olyan funkciókat is kínál, amelyek sokkal kényelmesebb és hatékonyabban használják a Azure Machine learning.

## <a name="prerequisites"></a>Előfeltételek

* Azure Machine Learning munkaterület. Ha még nem rendelkezik ilyennel, létrehozhat egy Azure Machine Learning munkaterületet az [Azure Portal](how-to-manage-workspace.md), az [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)és a [Azure Resource Manager sablonok](how-to-create-workspace-template.md)használatával.

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> (Csak helyi és DSVM) Munkaterület-konfigurációs fájl létrehozása

A munkaterület-konfigurációs fájl egy JSON-fájl, amely közli az SDK-val, hogyan kommunikálhat a Azure Machine Learning munkaterülettel. A fájl neve *config.json*, és a formátuma a következő:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

A JSON-fájlnak a Python-szkripteket vagy Jupyter-jegyzetfüzeteket tartalmazó címtár-struktúrában kell lennie. Ez lehet ugyanabban a címtárban, egy *. azureml*nevű alkönyvtár vagy egy szülő könyvtárban.

Ha ezt a fájlt a kódból szeretné használni, használja a [`Workspace.from_config`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true) metódust. Ez a kód betölti az adatokat a fájlból, és csatlakozik a munkaterülethez.

Hozzon létre egy munkaterület-konfigurációs fájlt az alábbi módszerek egyikével:

* Azure Portal

    **Töltse le a fájlt**: a [Azure Portal](https://ms.portal.azure.com)válassza a  **config.jsletöltése** elemet a munkaterület **Áttekintés** szakaszában.

    ![Azure Portal](./media/how-to-configure-environment/configure.png)

* Python SDK Azure Machine Learning

    Hozzon létre egy parancsfájlt a Azure Machine Learning munkaterülethez való kapcsolódáshoz, és a [`write_config`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true) metódus használatával hozza létre a fájlt, és mentse *. azureml/config.js*. Győződjön meg arról, hogy a, a `subscription_id` `resource_group` és a és a együtt van lecserélve `workspace_name` .

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

## <a name="local-computer"></a><a id="local"></a>Helyi számítógép

Helyi fejlesztési környezet (amely lehet távoli virtuális gép is, például egy Azure Machine Learning számítási példány vagy DSVM) konfigurálása:

1. Hozzon létre egy Python virtuális környezetet (virtualenv, Conda).

    > [!NOTE]
    > Bár ez nem kötelező, a [anaconda](https://www.anaconda.com/download/) vagy a [Miniconda](https://www.anaconda.com/download/) használata ajánlott a Python virtuális környezetek kezeléséhez és csomagok telepítéséhez.

    > [!IMPORTANT]
    > Ha Linux vagy macOS rendszerű, és a bashtől eltérő (például zsh) rendszerhéjat használ, előfordulhat, hogy bizonyos parancsok futtatásakor hibák jelentkeznek. A probléma megkerüléséhez a `bash` paranccsal indítson el egy új bash-rendszerhéjat, és futtassa a parancsokat.

1. Aktiválja az újonnan létrehozott Python virtuális környezetet.
1. Telepítse a [Azure Machine learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)-t.
1. A helyi környezet a Azure Machine Learning munkaterület használatára való konfigurálásához [hozzon létre egy munkaterület-konfigurációs fájlt](#workspace) , vagy használjon egy meglévőt.

Most, hogy beállította a helyi környezetét, készen áll a Azure Machine Learning használatának megkezdésére. A kezdéshez tekintse meg a [Azure Machine learning Python első lépések útmutatóját](tutorial-1st-experiment-sdk-setup-local.md) .

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter-notebookok

Helyi Jupyter Notebook-kiszolgáló futtatásakor javasoljuk, hogy hozzon létre egy IPython-rendszermagot a Python virtuális környezethez. Ez segít a várt kernel-és csomag-importálási viselkedés biztosításában.

1. Környezet-specifikus IPython-kernelek engedélyezése

    ```bash
    conda install notebook ipykernel
    ```

1. Hozzon létre egy kernelt a Python virtuális környezethez. Ügyeljen arra, hogy a helyére `<myenv>` a Python-beli virtuális környezet nevét írja.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. A Jupyter Notebook-kiszolgáló elindítása

A Azure Machine Learning és a Jupyter notebookok megismeréséhez tekintse meg a [Azure Machine learning notebook-tárházat](https://github.com/Azure/MachineLearningNotebooks) .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

A Visual Studio Code használata a fejlesztéshez:

1. Telepítse a [Visual Studio Code](https://code.visualstudio.com/Download)-ot.
1. Telepítse a [Azure Machine learning Visual Studio Code bővítményt](tutorial-setup-vscode-extension.md) (előzetes verzió).

Miután telepítette a Visual Studio Code bővítményt, felügyelheti [Azure Machine learning erőforrásait](how-to-manage-resources-vscode.md), [futtathatja és hibakeresési kísérleteit](how-to-debug-visual-studio-code.md), valamint [betanított modelleket telepíthet](tutorial-train-deploy-image-classification-model-vscode.md).

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure Machine Learning számítási példány

A Azure Machine Learning [számítási példány](concept-compute-instance.md) egy biztonságos, felhőalapú Azure-munkaállomás, amely egy Jupyter notebook-kiszolgálóval, JupyterLab és egy teljes körűen felügyelt gépi tanulási környezettel biztosítja az adatszakértőket.

A számítási példányok telepítése és konfigurálása nem szükséges.  

Hozzon létre egyet bármikor a Azure Machine Learning munkaterületen belülről. Adjon meg csak egy nevet, és adjon meg egy Azure-beli virtuális gép típusát. Próbálja ki most ezt az [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md).

További információ a számítási példányokról, beleértve a csomagok telepítésének módját: [számítási példányok](concept-compute-instance.md).

> [!TIP]
> Ha meg szeretné akadályozni, hogy a nem használt számítási példányok ne kelljen fizetni, [állítsa le a számítási példányt](tutorial-1st-experiment-bring-data.md#clean-up-resources).

A Jupyter Notebook-kiszolgáló és a JupyterLab mellett számítási példányokat is használhat az [integrált jegyzetfüzet szolgáltatásban Azure Machine learning Studióban](how-to-run-jupyter-notebooks.md).

A Azure Machine Learning Visual Studio Code bővítmény használatával [egy Azure Machine learning számítási példányt is konfigurálhat távoli Jupyter notebook kiszolgálóként](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Adatelemzési virtuális gép

A DSVM egy testreszabott virtuálisgép-(VM-) rendszerkép. Ez az adatelemzési munkák számára készült, amelyek előre konfigurált eszközök és szoftverek, például:

  - Csomagok, mint például a TensorFlow, a PyTorch, a Scikit-Learn, a XGBoost és a Azure Machine Learning SDK
  - Népszerű adatelemzési eszközök, mint például a Spark standalone és a drill
  - Azure-eszközök, például az Azure CLI, a AzCopy és a Storage Explorer
  - Integrált fejlesztői környezetek (ide), például a Visual Studio Code és a Notebookshoz
  - Jupyter Notebook kiszolgáló

Az eszközök átfogóbb listáját a [DSVM mellékelt eszközök útmutatójában](data-science-virtual-machine/tools-included.md)találja.

> [!IMPORTANT]
> Ha azt tervezi, hogy a DSVM [számítási célként](concept-compute-target.md) használja a képzéshez vagy a feladatokhoz, csak az Ubuntu támogatott.

A DSVM használata fejlesztési környezetként

1. Hozzon létre egy DSVM az alábbi módszerek egyikének használatával:

    * [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) -vagy [Windows](data-science-virtual-machine/provision-vm.md) -DSVM létrehozásához használja a Azure Portal.
    * [Hozzon létre egy DSVM ARM-sablonok használatával](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Az Azure parancssori felületének használata

        Ubuntu-DSVM létrehozásához használja a következő parancsot:

        ```azurecli-interactive
        # create a Ubuntu DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Windows DSVM létrehozásához használja a következő parancsot:

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Aktiválja a Azure Machine Learning SDK-t tartalmazó Conda-környezetet.

    * Ubuntu DSVM esetén:

        ```bash
        conda activate py36
        ```

    * Windows DSVM esetén:

        ```bash
        conda activate AzureML
        ```

1. Ha a DSVM a Azure Machine Learning munkaterület használatára szeretné konfigurálni, [hozzon létre egy munkaterület-konfigurációs fájlt](#workspace) , vagy használjon egy meglévőt.

A helyi környezetekhez hasonlóan a Visual Studio Code és a [Azure Machine learning Visual Studio Code bővítmény](#vscode) is használható a Azure Machine learning való interakcióhoz.

További információ: [Adatelemzési Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="azure-databricks"></a><a name="aml-databricks"></a> Azure Databricks

A Azure Databricks egy Apache Spark-alapú környezet az Azure-felhőben. Együttműködési jegyzetfüzet-alapú környezetet biztosít CPU vagy GPU-alapú számítási fürttel.

Hogyan működik a Azure Databricks Azure Machine Learning:

+ A modelleket Spark MLlib használatával is betaníthatja, és a modellt ACI/AK-ba helyezheti el Azure Databricks belülről.
+ Az [automatizált gépi tanulási](concept-automated-ml.md) képességeket egy speciális Azure ml SDK-val is használhatja Azure Databricks.
+ A Azure Databricks számítási célként [Azure Machine learning folyamatból](concept-ml-pipelines.md)is használhatja.

### <a name="set-up-your-databricks-cluster"></a>A Databricks-fürt beállítása

Hozzon létre egy [Databricks-fürtöt](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Egyes beállítások csak akkor érvényesek, ha az SDK-t a Databricks-on lévő automatizált gépi tanuláshoz telepíti.
**A fürt létrehozása néhány percig is eltarthat.**

Használja ezeket a beállításokat:

| Beállítás |A következőre érvényes:| Érték |
|----|---|---|
| Fürt neve |mindig| yourclustername |
| A Databricks futtatókörnyezete |mindig|Nem ML futtatókörnyezet 6,5 (Scala 2,11, Spark 2.4.3) |
| Python-verzió |mindig| 3 |
| Feldolgozók |mindig| 2 vagy magasabb |
| Munkavégző csomópont virtuálisgép-típusai <br>(meghatározza az egyidejű ismétlések maximális számát) |Automatizált ML<br>csak| A memóriára optimalizált virtuális gép előnyben részesített |
| Automatikus skálázás engedélyezése |Automatizált ML<br>csak| Törölje a jelet a  |

A folytatás előtt várjon, amíg a fürt fut.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>A megfelelő SDK telepítése Databricks-tárba

Ha a fürt fut, [hozzon létre egy függvénytárat](https://docs.databricks.com/user-guide/libraries.html#create-a-library) , hogy csatolja a megfelelő Azure Machine learning SDK-csomagot a fürthöz.

1. Kattintson a jobb gombbal arra a munkaterület-mappára, ahol a könyvtárat tárolni szeretné. Válassza **Create**a  >  **könyvtár**létrehozása lehetőséget.

1. **Csak egy** lehetőséget válasszon (más SDK-telepítés nem támogatott)

   |SDK- &nbsp; csomag &nbsp; extrái|Forrás|PyPi &nbsp; neve&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks| Python-tojás vagy PyPI feltöltése | azureml – SDK [databricks]|
   |Databricks esetében –<br> automatizált ML-képességek| Python-tojás vagy PyPI feltöltése | `azureml-sdk[automl]`|

   > [!Warning]
   > Más SDK-extrák nem telepíthetők. Válassza ki az előző beállítások közül az egyiket [ `databricks` ] vagy [ `automl` ].

   * Ne válassza **az Automatikus csatolás az összes fürthöz**lehetőséget.
   * Válassza a  **csatolás** elemet a fürt neve mellett.

1. A hibák figyelése, amíg az állapot a **csatolt**értékre módosul, ami több percet is igénybe vehet.  Ha ez a lépés meghiúsul:

   Próbálja meg újraindítani a fürtöt a alábbiak szerint:
   1. A bal oldali ablaktáblán válassza a **fürtök**lehetőséget.
   1. A táblázatban válassza ki a fürt nevét.
   1. A **tárak** lapon válassza az **Újraindítás**lehetőséget.

   Vegye figyelembe a következőket is:
   + A AutoML config Azure Databricks használatakor adja hozzá a következő paramétereket:
       1. ```max_concurrent_iterations``` a fürt munkavégző csomópontjainak száma alapján történik.
        2. ```spark_context=sc``` az alapértelmezett Spark-környezeten alapul.
   + Ha pedig egy régi SDK-verzióval rendelkezik, törölje a fürt telepített libs elemét, és váltson a kukába. Telepítse az új SDK-verziót, és indítsa újra a fürtöt. Ha az újraindítás után probléma merül fel, válassza le és csatlakoztassa újra a fürtöt.

Ha a telepítés sikeres volt, az importált függvénytárnak a következőhöz hasonlóan kell kinéznie:

SDK a Databricks-hez a Databricks-hez készült automatikus gépi tanulás **_nélkül_** ![ Azure Machine learning SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK az Databricks-hez **készült** automatizált Machine learning SDK-val a Databricks-re ![ telepített automatikus gépi tanulással](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Kezdje el az ismerkedést

Próbálja ki:
+ Habár több jegyzetfüzet is elérhető, **csak ezek a [minta-jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) működnek együtt Azure Databricksokkal.**

+ Importálja ezeket a mintákat közvetlenül a munkaterületről. Lásd alább: válassza az importálás importálás ![ ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ panelt](./media/how-to-configure-environment/azure-db-import.png)

+ Megtudhatja, hogyan [hozhat létre egy folyamatot a Databricks, mint a betanítási számításokat](how-to-create-your-first-pipeline.md).

## <a name="next-steps"></a>További lépések

- [Modell Betanítása](tutorial-train-models-with-aml.md) Azure Machine learningre a MNIST adatkészlettel
- A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) -dokumentáció megtekintése
