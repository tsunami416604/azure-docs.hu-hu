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
ms.date: 10/24/2018
ms.openlocfilehash: 6c2d5a776f603161ef730028168b91844c120aec
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158992"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Az Azure Machine Learning a fejlesztési környezet konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja a fejlesztési környezetet az Azure Machine Learning szolgáltatással, többek között:

- Hogyan hozzon létre egy konfigurációs fájlt, amely összekapcsolja a környezet egy Azure Machine Learning szolgáltatás munkaterületén.
- Hogyan konfigurálható a következő fejlesztési környezetekben:
  - A számítógépen a Jupyter notebookok
  - Visual Studio Code
  - Egyéni kód szerkesztése
- Hogyan állítható be egy [conda virtuális környezet](https://conda.io/docs/user-guide/tasks/manage-environments.html) , és használja az Azure Machine Learning. A munkakörnyezet csomagok közötti függőségi ütközések elkerülése érdekében el egészében Anaconda használatát javasoljuk.

## <a name="prerequisites"></a>Előfeltételek

- Állítsa be az Azure Machine Learning szolgáltatás munkaterületén. Kövesse a [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md).
- Telepítse vagy a [egészében Anaconda](https://www.anaconda.com/download/) vagy [Miniconda](https://conda.io/miniconda.html) Csomagkezelő.
- Ha a Visual Studio Code-ot használ, a [Python bővítményét](https://code.visualstudio.com/docs/python/python-tutorial).

> [!NOTE]
> A jelen cikkben ismertetett bash (a Linux és Mac OS) használatával rendszerhéjparancsokat tesztelheti vagy az parancssor (a Windows).

## <a name="create-a-workspace-configuration-file"></a>Munkaterület-konfigurációs fájl létrehozása

Az Azure Machine Learning SDK a munkaterület konfigurációs fájl használatával kommunikálni az Azure Machine Learning szolgáltatás munkaterületén.

- A konfigurációs fájl létrehozásához hajtsa végre a [Azure Machine Learning a rövid útmutató](quickstart-get-started.md).
  - A rövid útmutató folyamat létrehoz egy `config.json` Azure jegyzetfüzetekben található fájl. Ez a fájl munkaterületét a konfigurációs információkat tartalmaz.
  - Töltse le vagy másolja a `config.json` be ugyanabban a könyvtárban, a parancsfájlok vagy a jegyzetfüzet, amely erre hivatkozik.

- Másik lehetőségként hozhat létre a fájlt manuálisan az alábbi lépéseket:

    1. Nyissa meg a munkaterület a [az Azure portal](https://portal.azure.com). Másolás a __munkaterületnév__, __erőforráscsoport__, és __előfizetés-azonosító__. Ezeket az értékeket a konfigurációs fájl létrehozásához használt.
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

    1. A fájl létrehozásához a következő kóddal Python, és ellenőrizze, hogy a kód futtatásához a parancsfájlok vagy a munkaterület hivatkozó notebookok ugyanabban a címtárban:

        ```python
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'

        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        A kód írása a következő `aml_config/config.json` fájlt:

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        Másolhatja a `aml_config` könyvtár vagy csak `config.json` fájlt bármilyen más címtár, amely hivatkozik a munkaterület-bA.

       > [!NOTE]
       > Egyéb parancsfájlokat vagy ugyanabban a könyvtárban vagy az alatti notebookok betölteni a munkaterület `ws=Workspace.from_config()`.

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Az Azure notebookok és az adatelemző virtuális gépek

Az Azure notebookok és az Azure Data Science virtuális gépek (Dsvm) származnak konfigurálva az Azure Machine Learning szolgáltatással. Ezekben a környezetekben szükséges összetevők, például az Azure Machine Learning SDK tartalmazza.

- Az Azure notebookok Jupyter Notebook szolgáltatás az Azure-felhőben.
- Az adatelemző virtuális gép egy egyéni virtuális gépet (VM) lemezképet data science munkahelyi feladatokra tervezve. Ezek a következők:
  - Népszerű eszközök
  - Integrált fejlesztői környezeteket (ide)
  - Csomagokat, például a Jupyter Notebooks, a PyCharm és a tensorflow-hoz
- A munkaterület konfigurációs fájl használata ezekben a környezetekben továbbra is szüksége lesz.

Az Azure Machine Learning szolgáltatással Azure notebookok használatának példájáért lásd: [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md).

További információ a Data Science virtuális gépeken: [adatelemző virtuális gépek](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="configure-jupyter-notebooks-on-your-computer"></a>A Jupyter notebookok a számítógép konfigurálása

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

   A Python referenciadokumentumai osztályok és módszerek a következő SDK-k tekintheti meg:
   + [Az Azure Machine Learning SDK a Pythonhoz](https://aka.ms/aml-sdk)
   + [Azure Machine Learning adat-előkészítési SDK](https://aka.ms/data-prep-sdk)

   > [!NOTE]
   > Ha egy üzenetet kap, amely `PyYAML` nem lehet eltávolítani, helyette használja a következő parancsot:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Eltarthat néhány percig, az SDK telepítése.

1. A machine learning-kísérletezés a csomagok telepítéséhez. A következő parancsot, és cserélje le `<new package>` a csomag telepítéséhez:

    ```shell
    conda install <new package>
    ```

1. Jupyter Notebook conda-kompatibilis kiszolgáló telepítése, és engedélyezze a kísérlet widgetek (a Futtatás adatainak megtekintése). A következő parancsokat használja:

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. Nyissa meg a Jupyter Notebookot az alábbi parancsot:

    ```shell
    jupyter notebook
    ```

1. Nyisson meg egy új jegyzetfüzetet, és a "myenv" megjelöli a kernel majd ellenőrizze, hogy rendelkezik-e az Azure Machine Learning SDK-t. A notebook cella futtassa a következő parancsot:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>A Visual Studio Code konfigurálása

1. Nyisson meg egy parancssort vagy a rendszerhéjat.

1. Hozzon létre egy conda-környezetben a következő parancsokat:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. Az Azure Machine Learning SDK és az adatok előkészítési SDK telepítése a következő paranccsal:

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. A Visual Studio code Tools for AI-bővítmény telepítése. Lásd: [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

1. A machine learning-kísérletezés a csomagok telepítéséhez. Használja a következő parancsot, és cserélje le `<new package>` a csomag telepítéséhez:

    ```shell
    conda install <new package>
    ```

1. Nyissa meg a Visual Studio Code-ot, és ezután **CTRL-SHIFT-P** (a Windows) vagy **parancs-SHIFT-P** (a Mac OS) beolvasni a **Parancskatalógus**. Adja meg _Python: kiválasztása Interpret_ , és válassza ki a létrehozott conda-környezetet.

   > [!NOTE]
   > A Visual Studio Code automatikusan tudomása conda-környezeteket a számítógépen. További információkért lásd: [Visual Studio code dokumentáció](https://code.visualstudio.com/docs/python/environments#_conda-environments).

1. A konfiguráció érvényesítéséhez, hozzon létre egy új Python-parancsfájlt a következő kódra, és futtassa a Visual Studio Code használatával:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>Egy egyéni Kódszerkesztő konfigurálása

Azure Machine Learning SDK-val egy tetszőleges Kódszerkesztő is használhatja.

1. Hozza létre a conda-környezetet, a 2. lépésben leírtak szerint [konfigurálása a Visual Studio Code](#configure-visual-studio-code) felett.
1. Kövesse az utasításokat az egyes szerkesztő a conda-környezet használata. Ha például követheti a [PyCharm utasításokat](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).

## <a name="next-steps"></a>További lépések

- [A MNIST-adatkészlet az Azure Machine Learning modell betanítása](tutorial-train-models-with-aml.md)
