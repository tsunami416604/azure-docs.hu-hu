---
title: A Python fejlesztési környezet beállítása
titleSuffix: Azure Machine Learning service
description: Ismerje meg, a fejlesztési környezet konfigurálása az Azure Machine Learning szolgáltatáshoz való munka során. Ebből a cikkből megismerheti, hogyan Conda-környezetek használata, konfigurációs fájljainak létrehozása és konfigurálása a Jupyter notebookok, Azure notebookok, az Azure Databricks, ide-ket, kód szerkesztők és az adatelemző virtuális gép.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 02/24/2019
ms.custom: seodec18
ms.openlocfilehash: 1d4144a2a6cf41d594ee096d8802ccc5b29009a5
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361795"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Az Azure Machine Learning a fejlesztési környezet konfigurálása

Ebből a cikkből megismerheti, hogyan konfigurálhatja a fejlesztési környezetet az Azure Machine Learning szolgáltatással. Machine Learning szolgáltatás a platform-agnosztikus.

A fejlesztési környezet csak követelményei a Python 3, az Anaconda (az elkülönített környezet) és a egy konfigurációs fájl, amely tartalmazza az Azure Machine Learning munkaterület-információk.

Ez a cikk elsősorban a következő környezetekben és eszközöket:

* Az Azure notebookok: A Jupyter notebookok az Azure-felhőben üzemeltetett szolgáltatás. Első lépésként a legegyszerűbb módja,, mert az Azure Machine Learning SDK már telepítve van.

* [Az adatelemző virtuális gép (DSVM)](#dsvm): Egy előre konfigurált fejlesztői és kísérleti környezet az Azure-felhőben, amely a data science munkahelyi készült, és csak Virtuálisgép-példányok CPU vagy a GPU-alapú példányok is telepíthető. Python 3, Conda, a Jupyter notebookok és az Azure Machine Learning SDK már telepítve vannak. A virtuális gép együttműködik a népszerű machine learning és a deep learning-keretrendszerek, eszközök és -szerkesztők, gépi tanulási megoldások fejlesztéséhez. Fontos valószínűleg a legteljesebb körű fejlesztői környezet, a machine learning az Azure platformon.

* [A Jupyter Notebook](#jupyter): Ha már használja a Jupyter Notebookot, az SDK-val rendelkezik néhány kiegészítő funkciók, amelyek kell telepítenie.

* [A Visual Studio Code](#vscode): Ha Visual Studio Code-ot használ, tartalmaz néhány hasznos bővítményt, amely telepíthető.

* [Az Azure Databricks](#aml-databricks): Egy népszerű elemzési platform, amely az Apache Spark alapul. Ismerje meg, hogyan tehet szert az Azure Machine Learning SDK alakzatot a fürthöz úgy, hogy a modellek üzembe helyezése.

Ha már a Python 3-környezettel rendelkezik, vagy csak szeretné, az SDK telepítésének alapvető lépéseit, tekintse meg a [helyi számítógép](#local) szakaszban.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Machine Learning szolgáltatás munkaterületén. A munkaterület létrehozásához lásd: [hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](setup-create-workspace.md).

- Vagy a [Anaconda](https://www.anaconda.com/download/) vagy [Miniconda](https://conda.io/miniconda.html) Csomagkezelő.

    > [!IMPORTANT]
    > Anaconda és Miniconda nem szükséges, ha Azure notebookok használata.

- Linux vagy MacOS rendszeren szüksége lesz a bash felületet.

    > [!TIP]
    > Ha Ön Linux vagy macOS rendszeren, és eltérő (például a zsh) bash rendszerhéj-felületet használhat hibákat kaphat, ha az egyes parancsok futtatása. Ez a probléma megkerüléséhez használja a `bash` paranccsal indítsa el az új rendszerhéjakba, és ott futtassa a parancsokat.

- A Windows, a parancssort vagy szükséges Anaconda parancssort (telepített Anaconda és Miniconda).

## <a id="aznotebooks"></a>Az Azure notebookok

[Az Azure notebookok](https://notebooks.azure.com) (előzetes verzió) egy interaktív fejlesztési környezetben az Azure-felhőben. Ismerkedés az Azure Machine Learning fejlesztési legegyszerűbb módja.

* Az Azure Machine Learning SDK már telepítve van.
* Miután létrehozott egy Azure Machine Learning szolgáltatás munkaterület az Azure Portalon, kattintson a egy gombot, amellyel automatikusan konfigurálja a jegyzetfüzetet az Azure-környezet a munkaterülethez.

Bevezetés az Azure-jegyzetfüzetek történő fejlesztésbe, lásd: [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-run-cloud-notebook.md).

Alapértelmezés szerint az Azure-jegyzetfüzetek használja, amely a 4GB memóriát és 1GB adat ingyenes szolgáltatási réteg. Azonban eltávolíthatja ezeket a korlátokat az Azure-jegyzetfüzetek projekt egy adatelemző virtuális gép példány csatolásával. További információkért lásd: [kezelése és konfigurálása az Azure-jegyzetfüzetek projektek - számítási kapacitás](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a id="dsvm"></a>Adatelemző virtuális gép

A DSVM használata egy egyéni virtuális gépet (VM) lemezképet. Előre konfigurált data science munkahelyi készült:

  - Csomagokat, például a TensorFlow, PyTorch, Scikit-ismerje meg, XGBoost és az Azure Machine Learning SDK
  - Például a Spark önálló és a népszerű beépített adatelemzési eszközzel
  - Az Azure eszközök, például a Storage Explorer, az AzCopy és az Azure CLI-vel
  - Integrált fejlesztői környezeteket (ide-ket), például a Visual Studio Code és a PyCharm
  - Jupyter Notebook Server

Az Azure Machine Learning SDK-t a dsvm-hez, az Ubuntu vagy a Windows verziójával működik. Azonban ha azt tervezi, használja a dsvm-hez, valamint egy számítási célnak, csak Ubuntu támogatott.

A DSVM használata a fejlesztési környezetként, tegye a következőket:

1. Hozzon létre egy adatelemző virtuális GÉPET a következő környezetekben valamelyikét:

    * Az Azure Portalon:

        * [Egy Ubuntu adatelemző virtuális gép létrehozása](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [A Windows Data Science virtuális gép létrehozása](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Az Azure CLI:

        > [!IMPORTANT]
        > * Az Azure parancssori felület használata esetén, először jelentkezzen be az Azure-előfizetéshez használatával a `az login` parancsot.
        >
        > * Ebben a lépésben a parancsok használatakor meg kell adnia egy erőforráscsoport-nevet, egy nevet a virtuális gép, egy felhasználónevet és jelszót.

        * Egy Ubuntu adatelemző virtuális gép létrehozásához használja a következő parancsot:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * A Windows Data Science virtuális gép létrehozásához használja a következő parancsot:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Az Azure Machine Learning SDK-t a dsvm-hez már telepítve van. A Conda-környezet, amely tartalmazza az SDK-t használ, használja a következő parancsok egyikét:

    * For Ubuntu DSVM:

        ```shell
        conda activate py36
        ```

    * A DSVM Windows:

        ```shell
        conda activate AzureML
        ```

1. Győződjön meg arról, hogy az SDK eléréséhez, és ellenőrizze a verziót, használja a következő Python-kódban:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. A dsvm-hez az Azure Machine Learning szolgáltatás munkaterület használatára, lásd: a [munkaterület konfigurációs fájl létrehozása](#workspace) szakaszban.

További információkért lásd: [adatelemző virtuális gépek](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Helyi számítógép

Ha a helyi számítógépen (amely előfordulhat, hogy a távoli virtuális gépek is), Anaconda környezet létrehozása, és az SDK telepítése az alábbiak szerint:

1. Töltse le és telepítse [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3.7 verzió) Ha már nincs.

1. Nyisson meg egy Anaconda-parancssort, és a egy környezet létrehozásához az alábbi parancsokkal:

    Futtassa a következő parancsot a környezet létrehozásához.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Ezután aktiválja a környezetben.

    ```shell
    conda activate myenv
    ```

    Ez a példa létrehoz egy python 3.6.5-környezet, de bármely adott subversions választható ki. SDK-kompatibilitási nem lehet garantálni az egyes főbb verziók (3.5 + ajánlott), és azt javasoljuk, hogy ha hibákba ütközik, próbálja meg egy másik verziója/subversion Anaconda környezetében. Összetevők és a csomagok letöltése közben a környezet létrehozása több percig tart.

1. Az új környezetben környezetspecifikus ipython kernelekkel engedélyezéséhez futtassa a következő parancsokat. Ez biztosítja a várt kernel és a csomag importálása viselkedés Anaconda környezetekben a Jupyter notebookok használata során:

    ```shell
    conda install notebook ipykernel
    ```

    Ezután futtassa a következő parancsot a kernel létrehozásához:

    ```shell
    ipython kernel install --user
    ```

1. A következő parancsokat használja a csomagok telepítéséhez:

    Ez a parancs telepíti az alapszintű Azure Machine Learning SDK-jegyzetfüzet és automl kiegészítő funkciók. A `automl` extra nagy telepítése, és eltávolíthatja a szögletes zárójelek Ha nem szeretne automatikus machine learning-kísérletek futtassa. A `automl` extra is magában foglalja az Azure Machine Learning Data Prep SDK függőségként alapértelmezés szerint.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    Ez a parancs használatával telepítse az Azure Machine Learning Data Prep SDK önállóan:

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > Ha egy üzenet, hogy PyYAML nem távolítható el, használja az a következő parancsot:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Eltarthat néhány percig, az SDK telepítése. Tekintse meg a [telepítése útmutató](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) további információt a telepítési beállításokat.

1. A machine learning-kísérletezés az egyéb csomagok telepítéséhez.

    Használja a következő parancsok egyikét, és cserélje le  *\<új csomag >* telepíteni szeretné a csomaghoz. Keresztül csomagok telepítése `conda install` megköveteli, hogy a csomag része a jelenlegi csatornákon (új csatornák is hozzáadhatók Anaconda-felhőben).

    ```shell
    conda install <new package>
    ```

    Azt is megteheti, csomagok használatával telepíthet `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter-notebookok

A Jupyter Notebooks részét képezik a [Jupyter projekt](https://jupyter.org/). Ahol létrehozhatja az élő kód kombinálhatók a leíró szöveg és képek, dokumentumok interaktív kódolási élményt nyújtanak. Jupyter notebookok módon is egy nagyszerű az eredményeket megoszthatja másokkal, mert a kód szakaszok kimenete mentheti a dokumentumot. A Jupyter Notebooks kiszállítására különböző platformokon is telepítheti.

Az eljárás a [helyi számítógép](#local) szakasz futtatásához a Jupyter notebookok Anaconda környezetben szükséges összetevőket telepíti. Ahhoz, hogy ezeket az összetevőket a Jupyter Notebook környezetében, tegye a következőket:

1. Nyisson meg egy Anaconda-parancssort, és aktiválja a környezetben.

    ```shell
    conda activate myenv
    ```

1. Indítsa el a Jupyter Notebook server, a következő paranccsal:

    ```shell
    jupyter notebook
    ```

1. Győződjön meg arról, hogy a Jupyter Notebook használhat az SDK-t, hozzon létre egy **új** jegyzetfüzet, jelölje be **Python 3** , a kernel, és futtassa a következő parancsot a notebook cellába:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Ha problémák lépnek fel a modulok importálása és fogad egy `ModuleNotFoundError`, győződjön meg arról, a Jupyter kernel Notebook cellába a következő kód futtatásával csatlakozik a környezetnek a helyes elérési útra.

    ```python
    import sys
    sys.path
    ```

1. A Jupyter Notebook használata az Azure Machine Learning szolgáltatás munkaterület konfigurálásához nyissa meg a [munkaterület konfigurációs fájl létrehozása](#workspace) szakaszban.

### <a id="vscode"></a>A Visual Studio Code

A Visual Studio Code egy platformfüggetlen Kódszerkesztő. Python-támogatás a helyi Python 3 és Conda telepítési támaszkodik, de további eszközöket biztosít a mesterséges Intelligencia való munkához. Azt is támogatást nyújt a Kódszerkesztő belül a Conda-környezet kiválasztása.

A Visual Studio Code fejlesztési célra, tegye a következőket:

1. Python-fejlesztéshez a Visual Studio Code használatával kapcsolatban lásd: [Python a vscode-ban – első lépések](https://code.visualstudio.com/docs/python/python-tutorial).

1. Válassza ki a Conda-környezetet, hogy nyissa meg a VS Code, és válassza ki a Ctrl + Shift + P (Linux és Windows) vagy a parancs + Shift + P (Mac).
    A __parancs raklapos__ nyílik meg.

1. Adja meg __Python: Válassza ki a Interpret__, majd válassza ki a Conda-környezetben.

1. Ellenőrzése, hogy az SDK-t is használhatja, hozzon létre, és futtassa egy új Python-fájlt (.py), amely a következő kódot tartalmazza:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Az Azure Machine Learning-bővítmény a Visual Studio Code telepítése: [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    További információkért lásd: [használata az Azure Machine Learning, a Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Az Azure Databricks egy Apache Spark-alapú környezetben az Azure-felhőben. Biztosít egy együttműködésen alapuló, a Processzor és GPU-alapú számítási fürt alapú Notebook-környezet.

Az Azure Databricks hogyan működik együtt az Azure Machine Learning szolgáltatás:
+ Spark MLlib segítségével a modell betanítását, és a modell üzembe helyezése az ACI/aks-ben az Azure databricksben. 
+ Is [gépi tanulási automatikus](concept-automated-ml.md) egy különleges Azure ML SDK az Azure Databricks funkciói.
+ Használhatja az Azure Databricks egy számítási cél és a egy [Azure Machine Learning-folyamat](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>A Databricks-fürt beállítása

Hozzon létre egy [Databricks-fürt](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Egyes beállítások csak akkor, ha az automatikus machine learning-databricksen az SDK telepítése vonatkoznak.
**A fürt létrehozása néhány percet vesz igénybe.**

Használja ezeket a beállításokat:

| Beállítás |A következőkre vonatkozik| Érték |
|----|---|---|
| Fürt neve |mindig| yourclustername |
| A Databricks futtatókörnyezete |mindig| Any non ML runtime (non ML 4.x, 5.x) |
| Python-verzió |mindig| 3 |
| Feldolgozók |mindig| 2-es vagy újabb |
| Munkavégző csomópont virtuális gépek típusai <br>(az egyidejű az ismétlések maximális száma határozza meg) |Automatizált gépi tanulás<br>csak| Az optimalizált memóriájú virtuális gép előnyben részesített |
| Automatikus skálázás engedélyezése |Automatizált gépi tanulás<br>csak| Törölje a jelet |

Várjon, amíg a fürt fut, mielőtt továbblépne.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Telepítse a megfelelő SDK-t egy Databricks-tárba
Ha a fürt már fut, [hozzon létre egy könyvtárat](https://docs.databricks.com/user-guide/libraries.html#create-a-library) csatolása a megfelelő Azure Machine Learning-SDK csomagot és a fürt. 

1. Válasszon **csak egy** beállítás (más SDK telepítése nem támogatott)

   |SDK&nbsp;package&nbsp;extras|Forrás|PyPi&nbsp;neve&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |A Databricks| Töltse fel a Python tojás vagy PyPI | azureml-sdk[databricks]|
   |A Databricks - a-<br> automatizált gépi Tanulási képességek| Töltse fel a Python tojás vagy PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Nincs más SDK-t kiegészítő funkciók is telepíthető. Válassza ki az előző beállítások [databricks] vagy [automl_databricks] csak az egyik.

   * Ne válassza **automatikusan csatolja az összes fürt**.
   * Válassza ki **Attach** a fürt neve mellett.

1. A figyelő a hibákat, amíg az állapota **csatolt**, ami eltarthat néhány percig.  Ha ezt a lépést nem sikerül, ellenőrizze a következőket: 

   A fürt a indítsa újra:
   1. A bal oldali panelen válassza ki a **fürtök**.
   1. A tábla válassza ki a fürt nevét.
   1. Az a **kódtárak** lapon jelölje be **indítsa újra a**.
      
   Továbbá vegye figyelembe:
   + Egyes csomagokat, mint például `psutil`, Databricks ütközéseket okozhat a telepítés során. Az ilyen hibák elkerülése érdekében csomagok telepítéséhez fagyasztási lib verziójával, mint például `pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0`. 
   + Vagy ha egy régi SDK-verzióval rendelkezik, kapcsolja ki azt a fürt telepített függvénytárak és a Kukába helyezni. Az új SDK-verzió telepítése, és indítsa újra a fürtöt. Ha ezt követően egy problémát, válassza le, és csatlakoztassa újból a fürthöz.

Ha a telepítés sikeres volt, az importált függvénytár például a következők közül kell kinéznie:
   
A Databricks SDK **_nélkül_** gépi tanulási automatikus ![Machine Learning-hez készült Azure SDK Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Databricks készült SDK **WITH** gépi tanulási automatikus ![SDK-val automatikus telepítve a Databricks gépi tanulás](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Böngészés indítása

Próbálja ki:
+ Töltse le a [notebook archívumfájl](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) Azure Databricks vagy az Azure Machine Learning SDK és [az archív fájl importálása](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) be a Databricks-fürt.  
  Számos mintafüzetek érhetők el, amíg **csak [ezek mintafüzetek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) Azure Databricks működnek.**
  
+ Ismerje meg, hogyan [létrehoz egy folyamatot, a képzési számítási Databricks](how-to-create-your-first-pipeline.md).

## <a id="workspace"></a>Munkaterület-konfigurációs fájl létrehozása

A munkaterület konfigurációs fájl nem egy JSON-fájlt, amely arra utasítja az SDK-val, hogyan kommunikáljon az Azure Machine Learning szolgáltatás munkaterületén. A fájl neve *config.json*, és rendelkezik a következő formátumban:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

A JSON-fájlt, amely tartalmazza a Python-szkriptek vagy Jupyter notebookok a könyvtárstruktúra kell lennie. Azt is lehet ugyanabban a könyvtárban, nevű alkönyvtárban *aml_config*, vagy egy szülő könyvtárban.

Ezt a fájlt a kód használatához `ws=Workspace.from_config()`. Ez a kód az adatokat tölt be a fájlt, és a munkaterülethez csatlakozik.

Háromféleképpen hozhat létre a konfigurációs fájlban:

* **Kövesse a [hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](setup-create-workspace.md#sdk)**: A *config.json* fájl jön létre az Azure-jegyzetfüzetek könyvtárban. A fájl munkaterületét a konfigurációs információkat tartalmaz. Töltse le, vagy másolja a *config.json* más fejlesztési környezethez.

* **Hozza létre manuálisan a fájl**: Ezzel a módszerrel használhatja egy szövegszerkesztőben. Annak az értékeket, amelyek a konfigurációs fájlt a munkaterületek ellátogat a [az Azure portal](https://portal.azure.com). Másolja a munkaterület neve, az erőforráscsoport és az előfizetés-azonosító értékeket, és használhatja azokat a konfigurációs fájlban.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **A fájl létrehozása programozott módon**: Az alábbi kódrészlet csatlakoztatja egy munkaterülethez azáltal, hogy az előfizetés-azonosító, az erőforráscsoportot és a munkaterület nevét. Ezután menti a fájlt a munkaterület-konfiguráció:

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

    Ez a kód írása a konfigurációs fájlt a *aml_config/config.json* fájlt.

## <a name="next-steps"></a>További lépések

- [A modell betanítását](tutorial-train-models-with-aml.md) az Azure Machine Learning a MNIST adatkészlettel
- Nézet a [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) referencia
- További információ a [az Azure Machine Learning adat-előkészítési SDK](https://aka.ms/data-prep-sdk)
