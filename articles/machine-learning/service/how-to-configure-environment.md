---
title: A fejlesztési környezet konfigurálása az Azure Machine Learning |} A Microsoft Docs
description: Ismerje meg, a fejlesztési környezet konfigurálása az Azure Machine Learning szolgáltatás használatakor. Ebből a dokumentumból megtudhatja, hogyan Conda-környezetek használata, konfigurációs fájljainak létrehozása és konfigurálása a Jupyter Notebooks, Azure notebookok, ide-ket, kód szerkesztők és az adatelemző virtuális gép.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: c963c83df6eae09651ae8ec95a8bbb21bc1edfad
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870317"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Az Azure Machine Learning a fejlesztési környezet konfigurálása

Ebből a dokumentumból megtudhatja, hogyan konfigurálhatja a fejlesztési környezetet az Azure Machine Learning szolgáltatással. Az Azure Machine Learning szolgáltatás a platform-agnosztikus nem. A fejlesztési környezet csak követelményei vannak __Python 3__, __Conda__ (az elkülönített környezet), és a egy Azure Machine Learning-munkaterület adatait tartalmazó konfigurációs fájlt.

Ez a dokumentum a következő konkrét környezetekben és eszközök összpontosít:

* [Az Azure notebookok](#aznotebooks): az Azure-felhőben üzemeltetett, A Jupyter notebookok szolgáltatás. Ez __a legegyszerűbb__ megoldást a kezdéshez, mivel az Azure Machine Learning SDK már telepítve van.
* [Az adatelemző virtuális gép](#dsvm): A __előre konfigurált fejlesztői és kísérleti környezetben__ az Azure-felhőben, amely __tervezett data science munkahelyi__ és egyaránt telepíthető Egyetlen Virtuálisgép-példányok Processzor és GPU-alapú példányok. Python 3, Conda, a Jupyter notebookok és az Azure Machine Learning SDK már telepítve vannak. A virtuális gép együttműködik a népszerű gépi tanulás / deep learning-keretrendszerek, eszközök és -szerkesztők, gépi Tanulási megoldások fejlesztéséhez. Valószínűleg __a legteljesebb körű__ fejlesztői környezet ML-hez az Azure platformon.
* [A Jupyter Notebooks](#jupyter): Ha már használja a Jupyter Notebooks, az SDK-val rendelkezik-e néhány kiegészítő funkciók, amelyek kell telepítenie.
* [A Visual Studio Code](#vscode): Visual Studio Code-ot használ, hogy vannak-e néhány hasznos bővítményt, amely telepíthető.
* [Az Azure Databricks](#aml-databricks): egy népszerű elemzési platform az Apache Spark alapján. Ismerje meg, hogyan tehet szert az Azure Machine Learning SDK alakzatot a fürt üzembe helyezéséhez modellek.

Ha már a Python 3-környezettel rendelkezik, vagy csak szeretné, az SDK telepítésének alapvető lépéseit, tekintse meg a [helyi számítógép](#local) szakaszban.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Machine Learning szolgáltatás munkaterületén. Kövesse a [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md) hozhat létre egyet.

- Vagy a [egészében Anaconda](https://www.anaconda.com/download/) vagy [Miniconda](https://conda.io/miniconda.html) Csomagkezelő.

    > [!IMPORTANT]
    > Anaconda és Miniconda nem szükséges, ha Azure notebookok használatával.

- Linux vagy Mac OS szüksége lesz a bash felületet.

    > [!TIP]
    > Ha Linux vagy Mac OS, és eltérő (például a zsh) bash rendszerhéj-felületet használhat hiba jelenhet meg néhány parancs futtatásakor. Ez a probléma megkerüléséhez használja a `bash` paranccsal indítsa el az új rendszerhéjakba, és ott futtassa a parancsokat.

- A Windows, a parancssort vagy szükséges Anaconda parancssort (telepített Anaconda és Miniconda).

## <a id="anotebooks"></a>Az Azure notebookok

[Az Azure notebookok](https://notebooks.azure.com) (előzetes verzió) egy interaktív fejlesztési környezetben az Azure-felhőben. Ez __a legegyszerűbb__ Ismerkedés az Azure Machine Learning fejlesztési módja.

* Az Azure Machine Learning SDK __már telepítve van__.
* Miután létrehozott egy Azure Machine Learning szolgáltatás munkaterület az Azure Portalon, automatikusan konfigurálja a jegyzetfüzetet az Azure-környezet a munkaterület egy gombra kattinthat.

Bevezetés az Azure-jegyzetfüzetek történő fejlesztésbe, kövesse a [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md) dokumentumot.

## <a id="dsvm"></a>Adatelemző virtuális gép

Az adatelemzési virtuális gép (DSVM) egy egyéni virtuális gépet (VM) lemezképet **tervezett data science munkahelyi** , amely az előre konfigurálva a rendszerben:

  - Csomagokat, például a Tensorflow, a Pytorch, scikit-Xgboost és az Azure Machine Learning SDK megismeréséhez
  - Népszerű adatelemzési eszközök, mint például a Spark önálló, részletezés
  - Azure-eszközök például a CLI, az Azcopy és a Storage Explorerben
  - Integrált fejlesztői környezeteket (ide-ket), például a Visual Studio Code-ot, a PyCharm és az RStudio
  - Jupyter Notebook Server 

Az Azure Machine Learning SDK-t a dsvm-hez, az Ubuntu vagy a Windows verzió használható. A fejlesztési környezetet az adatelemző virtuális gép használja, használja az alábbi lépéseket:

1. A Data Science virtuális gép létrehozásához használja a következő módszerek egyikét:

    * Az Azure Portal használatával:

        * [Hozzon létre egy __Ubuntu__ adatelemző virtuális gép](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Hozzon létre egy __Windows__ adatelemző virtuális gép](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Az Azure CLI használatával:

        > [!IMPORTANT]
        > Az Azure parancssori felület használata esetén, először jelentkezzen be az Azure-előfizetéshez használatával a `az login` parancsot.
        >
        > Ebben a lépésben a parancsok használatakor meg kell adnia egy erőforráscsoport-nevet, egy nevet a virtuális gép, egy felhasználónevet és jelszót.

        * Hozhat létre egy __Ubuntu__ adatelemzési virtuális gépet, használja a következő parancsot:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Hozhat létre egy __Windows__ adatelemzési virtuális gépet, használja a következő parancsot:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Az Azure Machine Learning SDK **már telepítve van** a a dsvm-hez. A Conda-környezet, amely tartalmazza az SDK-t használ, használja a következő parancsok egyikét:

    * A __Ubuntu__ DSVM, használja a következő parancsot:

        ```shell
        conda activate py36
        ```

    * A __Windows__ DSVM, használja a következő parancsot:

        ```shell
        conda activate AzureML
        ```

1. Győződjön meg arról, hogy az SDK eléréséhez, és ellenőrizze a verziót, használja a következő Python-kódban:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. A dsvm-hez az Azure Machine Learning szolgáltatás munkaterület használatára, lásd: a [munkaterület konfigurálása](#workspace) szakaszban.

További információ a Data Science virtuális gépeken: [adatelemző virtuális gépek](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Helyi számítógép

A helyi számítógépen (amely a távoli virtuális gép is lehet) használjon az alábbi lépéseket a conda-környezet létrehozásához és telepítéséhez az SDK-val:

1. Nyisson meg egy parancssort vagy a rendszerhéjat.

1. Hozzon létre egy conda-környezetben a következő parancsokat:

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    A környezet létrehozása, ha a Python 3.6 és más összetevők le kell néhány percet igénybe vehet.

1. Telepítse a notebook kiegészítő funkciók az Azure Machine Learning SDK és az adatok előkészítési SDK a következő paranccsal:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Ha egy üzenetet kap, amely `PyYAML` nem lehet eltávolítani, helyette használja a következő parancsot:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Eltarthat néhány percig, az SDK telepítése.

1. A machine learning-kísérletezés a csomagok telepítéséhez. A következő parancsot, és cserélje le `<new package>` a csomag telepítéséhez:

    ```shell
    conda install <new package>
    ```

1. Annak ellenőrzéséhez, hogy az SDK telepítve van, a következő Python-kódban:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter-notebookok

A Jupyter Notebooks részét képezik a [Jupyter projekt](https://jupyter.org/). Ahol létrehozhatja az élő kód kombinálhatók a leíró szöveg és képek, dokumentumok interaktív kódolási élményt nyújtanak. Jupyter-notebookok módon is egy nagyszerű az eredményeket megoszthatja másokkal, a kód szakaszok kimenete mentheti a dokumentumot. A Jupyter Notebooks kiszállítására különböző platformokon is telepítheti.

A lépések a [helyi számítógép](#local) szakaszban Jupyter notebookokhoz választható összetevők telepítéséhez. Ahhoz, hogy ezeket az összetevőket a Jupyter Notebook környezetben, használja az alábbi lépéseket:

1. Nyisson meg egy parancssort vagy a rendszerhéjat.

1. A következő paranccsal conda-kompatibilis Jupyter Notebook kiszolgálót telepíteni:

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. Nyissa meg a Jupyter Notebookot az alábbi parancsot:

    ```shell
    jupyter notebook
    ```

1. Győződjön meg arról, hogy a Jupyter Notebookot az SDK-t használhatja, nyisson meg egy új jegyzetfüzetet, és válassza ki a "myenv", a kernel. Ezután futtassa a következő parancsot egy jegyzetfüzetet cellába:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. A Jupyter Notebook az Azure Machine Learning szolgáltatás munkaterületet használja, lásd: a [munkaterület konfigurálása](#workspace) szakaszban.

### <a id="vscode"></a>A Visual Studio Code

A Visual Studio Code egy platformfüggetlen Kódszerkesztő. Python-támogatás a helyi Python 3 és Conda telepítési támaszkodik, de további eszközöket biztosít a mesterséges Intelligencia való munkához. Azt is támogatást nyújt a Kódszerkesztő belül a Conda-környezet kiválasztása.

A Visual Studio Code fejlesztési célra, használja az alábbi lépéseket:

1. Python-fejlesztéshez a Visual Studio Code használatával kapcsolatban lásd: a [Python a vscode-ban – első lépések](https://code.visualstudio.com/docs/python/python-tutorial) dokumentumot.

1. Válassza ki a Conda-környezetet, nyissa meg a VS Code, majd használja __Ctrl-Shift-P__ (Linux és Windows) vagy __parancs-Shift-P__ (Mac) lekérése a __parancs raklapos__. Adja meg __Python: kiválasztása Interpret__ , és válassza ki a conda-környezetet.

1. Ellenőrizze, hogy az SDK-t is használhatja, hozzon létre egy új Python-fájlt (.py), amely a következő kódot tartalmazza. Ezután futtassa a fájlt:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Az Azure Machine Learning-bővítményének telepítése a Visual Studio Code, tekintse meg a [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) lapot.

    További információkért lásd: [Azure Machine Learning használatával a Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

Használhatja egy egyéni az Azure Machine Learning-SDK verziója: Azure Databricks teljes körű egyéni machine Learning. Vagy a Databricks és a használati modellje betanításához [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) , a modell üzembe helyezése

A Databricks-fürt előkészítéséhez és mintafüzetek lekérése:

1. Hozzon létre egy [Databricks-fürt](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) 4.x-es (előnyben részesített magas egyidejűségi) Databricks futtatókörnyezet verziójával rendelkező **Python 3**. 

1. Hozzon létre egy könyvtárat [telepítse, és csatolja](https://docs.databricks.com/user-guide/libraries.html#create-a-library) az Azure Machine Learning SDK Pythonhoz készült `azureml-sdk[databricks]` PyPi csomagot a fürtön. Amikor elkészült, megjelenik a szalagtárral ezen a képen látható módon. Vegye figyelembe ezeket [Databricks gyakori problémák](resource-known-issues.md#databricks).

   ![SDK-t a Databricks ](./media/how-to-azure-machine-learning-on-databricks/sdk-installed-on-databricks.jpg)

   Ha ez a lépés sikertelen, indítsa újra a fürtöt:
   1. Válassza ki `Clusters`a bal oldali panelen. Válassza ki a fürt nevét a táblázatban. 
   1. Az a `Libraries` lapon jelölje be `Restart`.

1. Töltse le a [Azure Databricks az Azure Machine Learning SDK notebook archív fájl /](https://github.com/Azure/MachineLearningNotebooks/blob/master/databricks/Databricks_AMLSDK_github.dbc).

   >[!Warning]
   > Számos mintafüzetek Azure Machine Learning szolgáltatással való használatra érhetők el. Csak ezek mintafüzetek Azure Databricks használata: https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks

1.  [Az archív fájl importálása](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) be a Databricks-fürt, és kísérletez [az itt leírtak szerint](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks).


## <a id="workspace"></a>Munkaterület-konfigurációs fájl létrehozása

A munkaterület konfigurációs fájl nem JSON-dokumentumok, amely arra utasítja az SDK-val, hogyan kommunikáljon az Azure Machine Learning szolgáltatás munkaterületén. A fájl neve `config.json` és rendelkezik a következő formátumban:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Ez a fájl a könyvtárstruktúra, amely tartalmazza a Python-szkriptek vagy Jupyter notebookok kell lennie. Ez lehet ugyanabban a könyvtárban, nevű alkönyvtárban `aml_config`, vagy egy szülő könyvtárban.

Ezt a fájlt a kód használatához `ws=Workspace.from_config()`. Ez a kód az adatokat tölt be a fájlt, és a munkaterülethez csatlakozik.

Háromféleképpen hozhat létre a konfigurációs fájlban:

* Ha követi az [Azure Machine Learning a rövid útmutató](quickstart-get-started.md), amely egy `config.json` fájl jön létre az Azure-jegyzetfüzetek könyvtárban. Ez a fájl munkaterületét a konfigurációs információkat tartalmaz. Töltse le, vagy ezt `config.json` más fejlesztési környezethez.

* Is **manuálisan hozza létre a fájlt** egy szövegszerkesztő használatával. Értékeit, hogy a munkaterület összefoglalója lépnek a konfigurációs fájlban található a [az Azure portal](https://portal.azure.com). Másolás a __munkaterületnév__, __erőforráscsoport__, és __előfizetés-azonosító__ értéket, majd használhatja azokat a konfigurációs fájlban.
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

* Is **a fájl létrehozása programozott módon**. A következő kódrészletet bemutatja, hogyan csatlakozhat egy munkaterülethez azáltal, hogy az előfizetés-azonosító, az erőforráscsoportot és a munkaterület nevét. A munkaterület-konfiguráció majd fájlba mentése:

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

    Ez a kód írása a konfigurációs fájlt a `aml_config/config.json` fájlt.

## <a name="next-steps"></a>További lépések

- [A MNIST-adatkészlet az Azure Machine Learning modell betanítása](tutorial-train-models-with-aml.md)
- [Az Azure Machine Learning SDK a Pythonhoz](https://aka.ms/aml-sdk)
- [Azure Machine Learning adat-előkészítési SDK](https://aka.ms/data-prep-sdk)
