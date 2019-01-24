---
title: A Python fejlesztési környezet beállítása
titleSuffix: Azure Machine Learning service
description: Ismerje meg, a fejlesztési környezet konfigurálása az Azure Machine Learning szolgáltatáshoz való munka során. Ebből a cikkből megismerheti, hogyan Conda-környezetek használata, konfigurációs fájljainak létrehozása és konfigurálása a Jupyter notebookok, Azure notebookok, az Azure Databricks, ide-ket, kód szerkesztők és az adatelemző virtuális gép.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 0f53b3cec843ca8016c61a360025b5e731b96f55
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815871"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Az Azure Machine Learning a fejlesztési környezet konfigurálása

Ebből a cikkből megismerheti, hogyan konfigurálhatja a fejlesztési környezetet az Azure Machine Learning szolgáltatással. Machine Learning szolgáltatás a platform-agnosztikus. 

A fejlesztési környezet csak követelményei a Python 3, a Conda (az elkülönített környezet) és a egy konfigurációs fájl, amely tartalmazza az Azure Machine Learning munkaterület-információk.

Ez a cikk elsősorban a következő környezetekben és eszközöket:

* [Az Azure notebookok](#aznotebooks): A Jupyter notebookok az Azure-felhőben üzemeltetett szolgáltatás. Első lépésként a legegyszerűbb módja,, mert az Azure Machine Learning SDK már telepítve van.

* [Az adatelemző virtuális gép (DSVM)](#dsvm): Egy előre konfigurált fejlesztői és kísérleti környezet az Azure-felhőben, amely a data science munkahelyi készült, és csak Virtuálisgép-példányok CPU vagy a GPU-alapú példányok is telepíthető. Python 3, Conda, a Jupyter notebookok és az Azure Machine Learning SDK már telepítve vannak. A virtuális gép együttműködik a népszerű machine learning és a deep learning-keretrendszerek, eszközök és -szerkesztők, gépi tanulási megoldások fejlesztéséhez. Fontos valószínűleg a legteljesebb körű fejlesztői környezet, a machine learning az Azure platformon.

* [A Jupyter Notebook](#jupyter): Ha már használja a Jupyter Notebookot, az SDK-val rendelkezik néhány kiegészítő funkciók, amelyek kell telepítenie.

* [A Visual Studio Code](#vscode): Ha Visual Studio Code-ot használ, tartalmaz néhány hasznos bővítményt, amely telepíthető.

* [Az Azure Databricks](#aml-databricks): Egy népszerű elemzési platform, amely az Apache Spark alapul. Ismerje meg, hogyan tehet szert az Azure Machine Learning SDK alakzatot a fürthöz úgy, hogy a modellek üzembe helyezése.

Ha már a Python 3-környezettel rendelkezik, vagy csak szeretné, az SDK telepítésének alapvető lépéseit, tekintse meg a [helyi számítógép](#local) szakaszban.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Machine Learning szolgáltatás munkaterületén. A munkaterület létrehozásához lásd: [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md).

- Vagy a [egészében Anaconda](https://www.anaconda.com/download/) vagy [Miniconda](https://conda.io/miniconda.html) Csomagkezelő.

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

Bevezetés az Azure-jegyzetfüzetek történő fejlesztésbe, lásd: [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md).

Alapértelmezés szerint az Azure-jegyzetfüzetek használja, amely a 4GB memóriát és 1GB adat ingyenes szolgáltatási réteg. Azonban eltávolíthatja ezeket a korlátokat az Azure-jegyzetfüzetek projekt egy adatelemző virtuális gép példány csatolásával. További információkért lásd: [kezelése és konfigurálása az Azure-jegyzetfüzetek projektek - számítási kapacitás](/azure/notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

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

Ha a helyi számítógépen (amely előfordulhat, hogy a távoli virtuális gépek is), hozzon létre egy Conda-környezetet, és az SDK telepítése az alábbiak szerint:

1. Nyisson meg egy parancssort vagy a rendszerhéjat.

1. Hozzon létre egy Conda-környezetben a következő parancsokat:

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    A környezet létrehozása, ha a Python 3.6 és más összetevők le kell néhány percet igénybe vehet.

1. Telepítse a notebook kiegészítő funkciók az Azure Machine Learning SDK és az adatok előkészítési SDK a következő paranccsal:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Ha egy üzenet, hogy PyYAML nem távolítható el, használja az a következő parancsot:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Eltarthat néhány percig, az SDK telepítése.

1. A machine learning-kísérletezés a csomagok telepítéséhez. A következő parancsot, és cserélje le  *\<új csomag >* a csomag telepítéséhez:

    ```shell
    conda install <new package>
    ```

1. Győződjön meg arról, hogy telepítve van-e az SDK-val, használja a következő Python-kódban:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter-notebookok

A Jupyter Notebooks részét képezik a [Jupyter projekt](https://jupyter.org/). Ahol létrehozhatja az élő kód kombinálhatók a leíró szöveg és képek, dokumentumok interaktív kódolási élményt nyújtanak. Jupyter notebookok módon is egy nagyszerű az eredményeket megoszthatja másokkal, mert a kód szakaszok kimenete mentheti a dokumentumot. A Jupyter Notebooks kiszállítására különböző platformokon is telepítheti.

Az eljárás a [helyi számítógép](#local) szakaszban Jupyter notebookokhoz telepíti a választható összetevőket. Ahhoz, hogy ezeket az összetevőket a Jupyter Notebook környezetében, tegye a következőket:

1. Nyisson meg egy parancssort vagy a rendszerhéjat.

1. A Conda-kompatibilis Jupyter Notebook Server telepítéséhez használja a következő parancsot:

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. Nyissa meg a Jupyter Notebookot az alábbi parancsot:

    ```shell
    jupyter notebook
    ```

1. Győződjön meg arról, hogy az Jupyter Notebookot az SDK-val, az új notebook megnyitásához, jelölje be **myenv** , a kernel, és futtassa a következő parancsot a notebook cellába:

    ```python
    import azureml.core
    azureml.core.VERSION
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

Használhatja egy egyéni az Azure Machine Learning-SDK verziója: Azure Databricks teljes körű egyéni machine Learning. Vagy Databricks belül a modell betanítását, és központilag telepítenie [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code).

A Databricks-fürt előkészítéséhez és mintafüzetek lekérése:

1. Hozzon létre egy [Databricks-fürt](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) a következő beállításokkal:

    | Beállítás | Érték |
    |----|---|
    | Fürt neve | yourclustername |
    | A Databricks futtatókörnyezete | Any non ML runtime (non ML 4.x, 5.x) |
    | Python-verzió | 3 |
    | Feldolgozók | 2-es vagy újabb |

    Csak akkor, ha a Databricks automatizált gépi tanulási használni, használja ezeket a beállításokat:
    
    |   Beállítás | Érték |
    |----|---|
    | Munkavégző csomópont virtuális gépek típusai | Az optimalizált memóriájú virtuális gép előnyben részesített |
    | Automatikus skálázás engedélyezése | Törölje a jelet |
    
    A Databricks-fürtben található munkavégző csomópontok számát a gépi tanulás automatikus beállítások egyidejű ismétlések maximális száma határozza meg.  

    A fürt létrehozása néhány percet vesz igénybe. Várjon, amíg a fürt fut, mielőtt továbblépne.

1. Telepítse, és csatolja a fürt az Azure Machine Learning-SDK csomagot.  

    * [Hozzon létre egy könyvtárat](https://docs.databricks.com/user-guide/libraries.html#create-a-library) ezek a beállítások egyikével (_csak egyet az alábbi lehetőségek közül választhat_):
    
        * Az Azure Machine Learning-SDK telepítése _nélkül_ automatikus machine learning-funkció:
            | Beállítás | Érték |
            |----|---|
            |Forrás | Töltse fel a Python tojás vagy PyPI
            |PyPi neve | azureml-sdk[databricks]
    
        * Az Azure Machine Learning-SDK telepítése _a_ automatikus gépi tanulás:
            | Beállítás | Érték |
            |----|---|
            |Forrás | Töltse fel a Python tojás vagy PyPI
            |PyPi neve | azureml-sdk[automl_databricks]
    
    * Ne válassza **automatikusan csatolja az összes fürt**

    * Válassza ki **Attach** a fürt neve melletti

    * Győződjön meg arról, hogy nincsenek hibák állapotra vált, amíg **csatolt**. Eltarthat néhány percig.

    Ha régi SDK-verziója, kapcsolja ki azt a fürt telepített függvénytárak és a Kukába helyezni. Az új SDK-verzió telepítése, és indítsa újra a fürtöt. Ha ezt követően egy problémát, válassza le, és csatlakoztassa újból a fürthöz.

    Ha elkészült, a szalagtár van csatolva, ahogyan az alábbi lemezképek. Vegye figyelembe ezeket [Databricks gyakori problémák](resource-known-issues.md#databricks).

    * Ha telepítette az Azure Machine Learning-SDK _nélkül_ gépi tanulási automatikus ![SDK nélkül automatikus telepítve a Databricks gépi tanulás ](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

    * Ha telepítette az Azure Machine Learning-SDK _a_ gépi tanulási automatikus ![SDK-val automatikus telepítve a Databricks gépi tanulás ](./media/how-to-configure-environment/automlonadb.jpg)

   Ha ez a lépés sikertelen, indítsa újra a fürtöt, az alábbiak szerint:

   a. A bal oldali panelen válassza ki a **fürtök**. 
   
   b. A tábla válassza ki a fürt nevét. 

   c. Az a **kódtárak** lapon jelölje be **indítsa újra a**.

1. Töltse le a [Azure Databricks-vagy az Azure Machine Learning SDK notebook archívumfájl](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc).

   >[!Warning]
   > Számos mintafüzetek Azure Machine Learning szolgáltatással való használatra érhetők el. Csak [ezek mintafüzetek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) Azure Databricks működnek.

1.  [Az archív fájl importálása](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) be a Databricks-fürt, és kísérletez leírt a [Machine Learning notebookok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) lapot.


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

* **Kövesse a [Azure Machine Learning a rövid útmutató](quickstart-get-started.md)**: A *config.json* fájl jön létre az Azure-jegyzetfüzetek könyvtárban. A fájl munkaterületét a konfigurációs információkat tartalmaz. Töltse le, vagy másolja a *config.json* más fejlesztési környezethez.

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

- [A modell betanítását](tutorial-train-models-with-aml.md) az Azure Machine Learning a MNIST adatkészlettel]
- Nézet a [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) referencia
- További információ a [az Azure Machine Learning adat-előkészítési SDK](https://aka.ms/data-prep-sdk)
