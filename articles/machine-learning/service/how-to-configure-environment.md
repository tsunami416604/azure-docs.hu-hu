---
title: A fejlesztési környezet konfigurálása az Azure Machine Learning |} A Microsoft Docs
description: Ismerje meg, a fejlesztési környezet konfigurálása az Azure Machine Learning szolgáltatás használatakor. Ebből a dokumentumból megtudhatja, hogyan Conda-környezetek használata, konfigurációs fájljainak létrehozása és konfigurálása a Jupyter Notebooks, Azure notebookok, ide-ket, kód szerkesztők és az adatelemző virtuális gép.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: f68d5d7faf3555918b9f9a6add7754c8ae23d0a8
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239234"
---
# <a name="configure-a-development-environment-for-the-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatás a fejlesztési környezet konfigurálása

Ismerje meg, hogyan konfigurálhatja a fejlesztési környezetet az Azure Machine Learning szolgáltatással. Megtudhatja, hogyan hozhat létre egy konfigurációs fájl, amely összekapcsolja a környezet egy Azure Machine Learning szolgáltatás munkaterületén. Is megtudhatja, hogyan konfigurálhatja a következő fejlesztési környezetekben:

* A saját számítógépén Jupyter notebookok
* Visual Studio Code
* A választott Kódszerkesztő

Az ajánlott módszer az, hogy használjon egészében Anaconda [conda virtuális környezetek](https://conda.io/docs/user-guide/tasks/manage-environments.html) elkülöníteni a munkakörnyezet csomagok közötti függőségi ütközések elkerülése érdekében. Ez a cikk bemutatja a conda-környezet beállításával és használatával, az Azure Machine Learning lépéseit.


## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning szolgáltatás munkaterületén. Szereplő lépések segítségével hozzon létre egyet, a [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md) dokumentumot.

* [Egészében Anaconda](https://www.anaconda.com/download/) vagy [Miniconda](https://conda.io/miniconda.html) Csomagkezelő.

 * A Visual Studio Code-környezethez a [Python bővítményét](https://code.visualstudio.com/docs/python/python-tutorial).

> [!NOTE]
> Ebben a dokumentumban használt rendszerhéjparancsokat tesztelik, Linux és MacOS rendszeren futó bash-környezet. A parancsok a Windows cmd.exe is kell vizsgálni.

## <a name="create-workspace-configuration-file"></a>Munkaterület konfigurációs fájl létrehozása

A munkaterület konfigurációs fájlt az Azure Machine Learning szolgáltatás munkaterület kommunikálni az SDK-t használják.  Ez a fájl első kétféleképpen történhet:

* Végezze el a [rövid](quickstart-get-started.md) , hozzon létre egy munkaterületet, és a konfigurációs fájlt. A fájl `config.json` Azure notebookok jön létre az Ön számára.  Ez a fájl munkaterületét a konfigurációs információkat tartalmaz.  Töltse le, vagy másolja be a parancsfájlok vagy a jegyzetfüzet, amely erre hivatkozik ugyanabban a könyvtárban.


* A konfigurációs fájl létrehozása saját maga a következő lépésekben:

    1. Nyissa meg a munkaterület a [az Azure portal](https://portal.azure.com). Másolás a __munkaterületnév__, __erőforráscsoport__, és __előfizetés-azonosító__. Ezeket az értékeket a konfigurációs fájl létrehozásához használt.

        ![Azure Portal](./media/how-to-configure-environment/configure.png) 
    
    1. Hozzon létre a fájlt a Python-kód. A parancsfájlok vagy a munkaterület hivatkozó notebookok ugyanabban a könyvtárban futtassa a kódot:

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
        Ez a következő ír `aml_config/config.json` fájlt: 
    
        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        Másolhatja a `aml_config` könyvtár vagy csak `config.json` fájlt bármilyen más címtár, amely hivatkozik a munkaterület-bA.

>[!NOTE] 
>Egyéb parancsfájlokat vagy ugyanabban a könyvtárban vagy alatt notebookok tölti be a munkaterület `ws=Workspace.from_config()`

## <a name="azure-notebooks-and-data-science-virtual-machine"></a>Az Azure jegyzetfüzet és az adatelemző virtuális gép

Az Azure notebookok és az Azure adatelemzési virtuális gépek (DSVM) előre konfigurálva az Azure Machine Learning szolgáltatással. Szükséges összetevők, például az Azure Machine Learning SDK előtelepített ezekben a környezetekben.

Az Azure notebookok Jupyter Notebook szolgáltatás az Azure-felhőben. Az adatelemző virtuális gép a data science munkahelyi előre konfigurált Virtuálisgép-rendszerképet. A virtuális gép tartalmazza a népszerű eszközök, ide-ket és csomagokat, például a Jupyter Notebooks, a PyCharm és a tensorflow-hoz.

A munkaterület konfigurációs fájl használata ezekben a környezetekben továbbra is szüksége lesz.

További információ a Data Science virtuális gépeken: [adatelemző virtuális gépek](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) dokumentációját.

Példa Azure notebookok használatával az Azure Machine Learning szolgáltatással, tekintse meg a [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md) dokumentumot.

## <a name="configure-jupyter-notebooks-on-your-own-computer"></a>A saját számítógépén Jupyter notebookok konfigurálása

1. Nyisson meg egy parancssor vagy a rendszerhéjat.

2. Hozzon létre egy conda-környezetet, használja a következő parancsokat:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

    A környezet létrehozása több percet is igénybe vehet, le kell tölteni szükségük lehet a Python 3.6 és más összetevőket.

3. Notebook kiegészítő funkciók az Azure Machine Learning SDK telepítése esetén használja a következő parancsot:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl]
    ```

    Az SDK telepítése több percet is igénybe vehet.

4. A machine learning-kísérletezés a csomagokat telepíteni, használja a következő parancsot, és cserélje le `<new package>` a csomag telepítéséhez:

    ```shell
    conda install <new package>
    ```

5. Jupyter Notebook conda-kompatibilis kiszolgáló telepítése és engedélyezése kísérlet widgetek (a Futtatás adatainak megtekintése), a következő parancsokat használja:

    ```shell
    # install Jupyter 
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

6. Jupyter Notebook indításához használja a következő parancsot:

    ```shell
    jupyter notebook
    ```

7. Nyisson meg egy új jegyzetfüzetet, és válassza a kernel "myenv". Majd ellenőrizze, hogy rendelkezik-e Azure Machine Learning SDK-t a notebook cella futó alábbi paranccsal:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>A Visual Studio Code konfigurálása

1. Nyisson meg egy parancssor vagy a rendszerhéjat.

2. Hozzon létre egy conda-környezetet, használja a következő parancsokat:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

2. Az Azure Machine Learning SDK telepítéséhez használja a következő parancsot:
 
    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

4. A Visual Studio code Tools for AI telepítéséhez tekintse meg a Visual Studio Marketplace-en [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai). 

5. A machine learning-kísérletezés a csomagokat telepíteni, használja a következő parancsot, és cserélje le `<new package>` a csomag telepítéséhez:

    ```shell
    conda install <new package>
    ```

6. Indítsa el a Visual Studio Code-ot, és hogyan __CTRL-SHIFT-P__ beolvasni a __Parancskatalógus__. Adja meg *Python: kiválasztása Interpret*, és válassza ki a létrehozott conda-környezetet.

    > [!NOTE]
    > A Visual Studio Code automatikusan tudomása conda-környezeteket a számítógépen. További információkért lásd: [Visual Studio code dokumentáció](https://code.visualstudio.com/docs/python/environments#_conda-environments).

7. A konfiguráció érvényesítéséhez használja, hozzon létre egy új Python-parancsfájlt az alábbi kódot, és futtassa a Visual Studio Code:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-code-editor-of-your-choice"></a>A választott Kódszerkesztő konfigurálása

Az Azure Machine Learning SDK-val egy egyéni Kódszerkesztő használatához először hozzon létre conda-környezet fent leírtak szerint. Ezután kövesse az utasításokat az egyes szerkesztő a conda-környezet használata. Ha például a PyCharm vonatkozó utasításokat a következő helyen találhatók [ https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html ](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).
 
## <a name="next-steps"></a>További lépések

* [A MNIST-adatkészlet az Azure Machine Learning modell betanítása](tutorial-train-models-with-aml.md)
