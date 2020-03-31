---
title: Python-fejlesztési környezet beállítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan konfigurálhatja a fejlesztői környezetet az Azure Machine Learninghez. Conda-környezetek használatával konfigurációs fájlokat hozhat létre, és konfigurálhatja saját felhőalapú jegyzetfüzet-kiszolgálóját, jupyter-jegyzetfüzeteit, Azure Databricks-ét, Azonosítókat, kódszerkesztőket és az adatelemzési virtuális gépet.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: a5f46f5af723e1245afbc6bca90d25ae9036d646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472426"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Fejlesztői környezet konfigurálása az Azure Machine Learninghez
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan konfigurálhat egy fejlesztői környezetet az Azure Machine Learning használatával. Az Azure Machine Learning platformfüggetlen. A fejlesztői környezet egyetlen nehéz követelménye a Python 3. Egy elszigetelt környezetben, mint anaconda vagy Virtualenv is ajánlott.

Az alábbi táblázat a cikkben szereplő egyes fejlesztői környezeteket, valamint az előnyöket és hátrányokat mutatja be.

| Környezet | Előnyök | Hátrányok |
| --- | --- | --- |
| [Felhőalapú Azure Machine Learning-számítási példány (előzetes verzió)](#compute-instance) | A legegyszerűbb módja az indulásnak. A teljes SDK már telepítve van a munkaterületi virtuális gép, és notebook oktatóanyagok előre klónozott, és készen áll a futtatásra. | A fejlesztési környezet és a függőségek feletti ellenőrzés hiánya. A Linux virtuális gép (virtuális gép leállítható, ha nem használja a díjak elkerülése érdekében. Lásd [az árképzés részleteit](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Helyi környezet](#local) | A fejlesztési környezet és a függőségek teljes körű vezérlése. Bármilyen tetszőleges buildeszközzel, környezettel vagy IDE-vel futtathatja. | Tovább tart a kezdéshez. A szükséges SDK-csomagokat telepíteni kell, és egy környezetet is telepíteni kell, ha még nem rendelkezik ilyentel. |
| [Azure Databricks](#aml-databricks) | Ideális nagyméretű, intenzív gépi tanulási munkafolyamatok futtatásához a méretezhető Apache Spark platformon. | Túlzás kísérleti gépi tanuláshoz, vagy kisebb léptékű kísérletekhez és munkafolyamatokhoz. Az Azure Databricks további költségek. Lásd [az árképzés részleteit](https://azure.microsoft.com/pricing/details/databricks/). |
| [Az adatelemzési virtuális gép (DSVM)](#dsvm) | Hasonló a felhőalapú számítási példányhoz (a Python és az SDK előre telepítve van), de további népszerű adatelemzési és gépi tanulási eszközökkel előre telepítve. Könnyen méretezhető és kombinálható más egyéni eszközökkel és munkafolyamatokkal. | A felhőalapú számítási példányhoz képest lassabb, első lépések. |

Ez a cikk további használati tippeket is tartalmaz a következő eszközökhöz:

* [Jupyter notebookok:](#jupyter)Ha már használja a Jupyter Notebook, az SDK néhány extrával, hogy telepítenie kell.

* [Visual Studio-kód:](#vscode)Ha a Visual Studio-kódot használja, az [Azure Machine Learning bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) széles körű nyelvi támogatást tartalmaz a Pythonhoz, valamint olyan funkciókat, amelyek sokkal kényelmesebbé és hatékonyabbá teszik az Azure Machine Learning használatával való munkát.

## <a name="prerequisites"></a>Előfeltételek

Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásáról az [Azure Machine Learning-munkaterület létrehozása című témakörben található.](how-to-manage-workspace.md) A munkaterület mindössze annyit kell, hogy a saját [felhőalapú notebook-kiszolgáló,](#compute-instance)a [DSVM](#dsvm)vagy az [Azure Databricks.](#aml-databricks)

A [helyi számítógép](#local)SDK-környezetének telepítéséhez a [Jupyter notebook-kiszolgálóra](#jupyter) vagy a [Visual Studio-kódra](#vscode) is szüksége van:

- Vagy az [Anaconda,](https://www.anaconda.com/download/) vagy [a Miniconda](https://conda.io/miniconda.html) csomagkezelő.

- Linux vagy macOS rendszeren szüksége van a bash shell.

    > [!TIP]
    > Ha Linux vagy macOS rendszert használ, és a bash-től eltérő héjat (például zsh- t) használ, előfordulhat, hogy hibákat kap, amikor bizonyos parancsokat futtat. A probléma kerülő megoldásához a `bash` paranccsal indítson el egy új bash rendszerhéjat, és ott futtassa a parancsokat.

- Windows rendszerben a parancssorra vagy az Anaconda-sorra van szükség (az Anaconda és a Miniconda telepítette).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Saját felhőalapú számítási példány

Az Azure Machine Learning [számítási példány (előzetes verzió)](concept-compute-instance.md) egy biztonságos, felhőalapú Azure-munkaállomás, amely az adatszakértők egy Jupyter notebook-kiszolgáló, JupyterLab és egy teljesen előkészített gépi tanulási környezet.

Nincs mit telepíteni vagy konfigurálni egy számítási példányhoz.  Hozzon létre egyet az Azure Machine Learning-munkaterületen belülről. Adjon meg csak egy nevet, és adjon meg egy Azure virtuális gép típusát. Próbálja ki most ezzel [az oktatóanyaggal: Telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md).

További információ a [számítási példányokról.](concept-compute-instance.md)

A számítási díjak felmerülésének leállításához [állítsa le a számítási példányt.](tutorial-1st-experiment-sdk-train.md#clean-up-resources)

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Adatelemzési virtuális gép

A DSVM egy testreszabott virtuális gép (VM) lemezkép. Olyan adatelemzési munkára tervezték, amely előre konfigurálva van:

  - Csomagok, mint a TensorFlow, PyTorch, Scikit-learn, XGBoost és az Azure Machine Learning SDK
  - Népszerű adatelemzési eszközök, például a Spark Standalone és a Drill
  - Az Azure-eszközök, például az Azure CLI, az AzCopy és a Storage Explorer
  - Integrált fejlesztői környezetek , például visual studio kód és PyCharm
  - Jupyter notebook-kiszolgáló

Az Azure Machine Learning SDK a DSVM Ubuntu vagy Windows verzióján is működik. De ha azt tervezi, hogy a DSVM-et számítási célként is használja, csak az Ubuntu támogatott.

A DSVM fejlesztési környezetként való használata:

1. Hozzon létre egy DSVM-et az alábbi környezetek egyikében:

    * Az Azure-portál:

        * [Ubuntu rendszerű adatelemzési virtuális gép létrehozása](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Windows rendszerű adatelemzési virtuális gép létrehozása](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Az Azure CLI:

        > [!IMPORTANT]
        > * Az Azure CLI használatakor először be kell jelentkeznie `az login` az Azure-előfizetésbe a parancs használatával.
        >
        > * Ebben a lépésben a parancsok használatakor meg kell adnia egy erőforráscsoport nevét, a virtuális gép nevét, egy felhasználónevet és egy jelszót.

        * Ubuntu Data Science virtuális gép létrehozásához használja a következő parancsot:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Windows data science virtuális gép létrehozásához használja a következő parancsot:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. The Azure Machine Learning SDK is already installed on the DSVM. Az SDK-t tartalmazó Conda-környezet használatához használja az alábbi parancsok egyikét:

    * Ubuntu DSVM esetén:

        ```bash
        conda activate py36
        ```

    * Windows DSVM esetén:

        ```bash
        conda activate AzureML
        ```

1. Annak ellenőrzéséhez, hogy hozzáfér-e az SDK-hoz, és ellenőrizze a verziót, használja a következő Python-kódot:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Konfigurálása a DSVM az Azure Machine Learning-munkaterület használatára, tekintse meg a [munkaterület létrehozása konfigurációs fájl](#workspace) szakaszban.

További információ: [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="local-computer"></a><a id="local"></a>Helyi számítógép

Ha helyi számítógépet használ (amely egyben távoli virtuális gép is lehet), hozzon létre egy Anaconda-környezetet, és telepítse az SDK-t. Például:

1. Töltse le és telepítse [az Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3.7-es verzió) alkalmazást, ha még nem rendelkezik vele.

1. Nyisson meg egy Anaconda-kérdést, és hozzon létre egy környezetet a következő parancsokkal:

    A környezet létrehozásához futtassa a következő parancsot.

    ```bash
    conda create -n myenv python=3.6.5
    ```

    Akkor aktiválja a környezetet.

    ```bash
    conda activate myenv
    ```

    Ebben a példában létrehoz egy környezetet a python 3.6.5 használatával, de bármilyen adott subversions lehet választani. Előfordulhat, hogy bizonyos főverziók esetén nem garantált az SDK-kompatibilitás (3.5+ használata ajánlott), és ha hibákba ütközik, ajánlott egy másik verziót/felforgatást kipróbálni az Anaconda környezetben. Az összetevők és csomagok letöltése több percet vesz igénybe a környezet létrehozása.

1. Futtassa a következő parancsokat az új környezetben a környezetspecifikus IPython kernelek engedélyezéséhez. Ez biztosítja a várt kernel- és csomagimportálási viselkedést, amikor az Anaconda környezetben dolgozik a Jupyter notebookokkal:

    ```bash
    conda install notebook ipykernel
    ```

    Ezután futtassa a következő parancsot a kernel létrehozásához:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. A csomagok telepítéséhez használja az alábbi parancsokat:

    Ez a parancs telepíti az alap Azure `automl` Machine Learning SDK notebook és extrák. Az `automl` extra egy nagy telepítés, és eltávolítható a zárójelek, ha nem kívánja futtatni az automatizált gépi tanulási kísérletek. Az `automl` extra alapértelmezés szerint függőségként tartalmazza az Azure Machine Learning Data Prep SDK-t is.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Ha olyan üzenetet kap, hogy a PyYAML nem távolítható el, használja a következő parancsot:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * A macOS Catalina-val kezdődően a zsh (Z shell) az alapértelmezett bejelentkezési rendszerhéj és interaktív rendszerhéj. A zsh-ban használja a következő parancsot, amely a zárójelek "\\( fordított perjel) használatával távozik:
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Az SDK telepítése több percet vesz igénybe. A telepítési lehetőségekről további információt a [telepítési útmutatóban](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)talál.

1. Telepítsen más csomagokat a gépi tanulási kísérletezéshez.

    Használja az alábbi parancsok * \<* egyikét, és cserélje le az új csomagot>a telepíteni kívánt csomagra. A csomagok telepítése `conda install` az on-n keresztül megköveteli, hogy a csomag az aktuális csatornák részét képezi (új csatornákadhatók az Anaconda Cloud-ban).

    ```bash
    conda install <new package>
    ```

    Azt is megteheti, hogy `pip`csomagokat telepít a segítségével.

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter notebookok

Jupyter Notebooks része a [Jupyter Project](https://jupyter.org/). Interaktív kódolási élményt nyújtanak, ahol olyan dokumentumokat hozhat létre, amelyek az élő kódot narratív szöveggel és grafikával keverik. A Jupyter Notebooks nagyszerű módja annak, hogy megossza az eredményeket másokkal, mivel a kódszakaszok kimenetét mentheti a dokumentumban. A Jupyter notebookokat számos platformra telepítheti.

A Helyi [számítógép](#local) szakaszban található eljárás telepíti a Jupyter notebookok Anaconda környezetben való futtatásához szükséges összetevőket.

Ezeknek az összetevőknek a jupyteri notebookkörnyezetben való engedélyezése:

1. Nyisson meg egy Anaconda-kérdést, és aktiválja a környezetet.

    ```bash
    conda activate myenv
    ```

1. Klónozza [a GitHub-tárházat](https://aka.ms/aml-notebooks) mintajegyzetfüzetek készletéhez.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Indítsa el a Jupyter notebook kiszolgálót a következő paranccsal:

    ```bash
    jupyter notebook
    ```

1. Annak ellenőrzéséhez, hogy a Jupyter Notebook képes-e az SDK használatára, hozzon létre egy **új** jegyzetfüzetet, válassza a **Python 3-at** kernelként, majd futtassa a következő parancsot egy jegyzetfüzetcellában:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Ha problémákba ütközik a modulok `ModuleNotFoundError`importálása, és kap egy , győződjön meg arról, hogy a Jupyter kernel csatlakozik a megfelelő elérési utat a környezet által futtatva a következő kódot egy notebook cellában.

    ```python
    import sys
    sys.path
    ```

1. Ha a Jupyter-jegyzetfüzetet az Azure Machine Learning-munkaterület használatára szeretné konfigurálni, nyissa meg a [Munkaterület konfigurációs fájllétrehozása](#workspace) szakaszt.

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio kód

Visual Studio Code egy nagyon népszerű platformfüggetlen kód szerkesztő, amely támogatja a széles körű programozási nyelvek és eszközök révén elérhető kiterjesztések a [Visual Studio piacon.](https://marketplace.visualstudio.com/vscode) Az [Azure Machine Learning-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) telepíti a [Python-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a kódoláshoz minden típusú Python-környezetben (virtuális, Anaconda stb.). Emellett kényelmi funkciókat biztosít az Azure Machine Learning-erőforrásokkal való munkához és az Azure Machine Learning-kísérletek futtatásához anélkül, hogy elhagyná a Visual Studio-kódot.

A Visual Studio code használata fejlesztéshez:

1. Telepítse az Azure Machine Learning bővítményt a Visual Studio-kódhoz, lásd: [Azure Machine Learning.](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)

    További információ: [Use Azure Machine Learning for Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Ismerje meg, hogyan használhatja a Visual Studio-kódot bármilyen típusú Python-fejlesztéshez, olvassa [el a Python használatának első lépéseit a VSCode-ban.](https://code.visualstudio.com/docs/python/python-tutorial)

    - Az SDK-t tartalmazó SDK Python-környezet kiválasztásához nyissa meg a VS Code programot, majd válassza a Ctrl+Shift+P (Linux és Windows) vagy a Command+Shift+P (Mac) lehetőséget.
        - Megnyílik __a parancspaletta.__

    - Írja be a __Python: Select Interpreter__, majd válassza ki a megfelelő környezetet

1. Annak ellenőrzéséhez, hogy használhatja-e az SDK-t, hozzon létre egy új Python-fájlt (.py), amely a következő kódot tartalmazza:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Futtassa ezt a kódot a "Cella futtatása" CodeLens gombra kattintva, vagy egyszerűen nyomja meg a shift-enter billentyűt.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Az Azure Databricks egy Apache Spark-alapú környezet az Azure-felhőben. Ez biztosítja a kollaboratív Notebook-alapú környezet ben CPU vagy GPU-alapú számítási klaszter.

Hogyan működik az Azure Databricks az Azure Machine Learning szolgáltatással:
+ Betaníthat egy modellt a Spark MLlib használatával, és üzembe helyezheti a modellt az ACI/AKS-be az Azure Databricks-en belül.
+ Az Azure Databricks [automatikus gépi tanulási](concept-automated-ml.md) képességeit egy speciális Azure ML SDK-ban is használhatja.
+ Az Azure Databricks-t számítási célként használhatja egy [Azure Machine Learning-folyamatból.](concept-ml-pipelines.md)

### <a name="set-up-your-databricks-cluster"></a>A Databricks-fürt beállítása

[Databricks-fürt](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)létrehozása. Egyes beállítások csak akkor érvényesek, ha telepíti az SDK automatikus gépi tanulás databricks.
**A fürt létrehozása néhány percet vesz igénybe.**

Használja ezeket a beállításokat:

| Beállítás |A következőkre vonatkozik:| Érték |
|----|---|---|
| Fürt neve |Mindig| aclustername |
| A Databricks futtatókörnyezete |Mindig|Nem ML Runtime 6.0 (scala 2.11, szikra 2.4.3) |
| Python-verzió |Mindig| 3 |
| Munkavállalók |Mindig| 2 vagy magasabb |
| Munkavégző csomópont virtuálisgép-típusai <br>(meghatározza az egyidejű ismétlések maximális számának meghatározását) |Automatizált ML<br>Csak| Memória optimalizált virtuális gép előnyben részesített |
| Automatikus skálázás engedélyezése |Automatizált ML<br>Csak| Törölje a jelet a  |

A folytatás előtt várja meg, amíg a fürt fut.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>A megfelelő SDK telepítése Databricks-könyvtárba
A fürt futása után [hozzon létre egy könyvtárat](https://docs.databricks.com/user-guide/libraries.html#create-a-library) a megfelelő Azure Machine Learning SDK-csomag fürthöz való csatolásához.

1. Kattintson a jobb gombbal arra az aktuális Munkaterület mappára, amelyben a tárat tárolni szeretné. Válassza **a Tár létrehozása** > **Library**lehetőséget.

1. Csak egy lehetőség **közül választhat** (más SDK-telepítés nem támogatott)

   |SDK&nbsp;&nbsp;csomag extrák|Forrás|PyPi-név&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks esetében| Python egg vagy PyPI feltöltése | azureml-sdk[databricks]|
   |Databricks -with-<br> automatizált ml-képességek| Python egg vagy PyPI feltöltése | azureml-sdk[automl]|

   > [!Warning]
   > Más SDK extrák nem telepíthetők. Csak az egyiket válassza az előző lehetőségek közül [databricks] vagy [automl].

   * Ne válassza **az Automatikus csatolás az összes fürthöz**lehetőséget.
   * Válassza a **Csatolás** gombot a fürt neve mellett.

1. Figyelje a hibákat, amíg az állapot nem változik **a Csatolt**, ami több percet is igénybe vehet.  Ha ez a lépés sikertelen:

   Próbálja meg újraindítani a fürtöt:
   1. A bal oldali ablaktáblában válassza a **Fürtök**lehetőséget.
   1. A táblában válassza ki a fürt nevét.
   1. A **Könyvtárak** lapon válassza az **Újraindítás**gombot.

   Is figyelembe kell venni:
   + Az AutoML-konfigurációban az Azure Databricks használatakor adja hozzá a következő paramétereket:
       1. ```max_concurrent_iterations```a fürtben lévő munkavégző csomópontok számán alapul.
        2. ```spark_context=sc```az alapértelmezett szikrakörnyezeten alapul.
   + Vagy ha egy régi SDK-verzióval rendelkezik, törölje a fürt telepített libs-ből való kijelölését, és helyezze át a kukába. Telepítse az új SDK-verziót, és indítsa újra a fürtöt. Ha az újraindítás után probléma merül fel, válassza le és csatlakoztassa újra a fürtöt.

Ha a telepítés sikeres volt, az importált könyvtárnak az alábbira kell hasonlítaniaa:

SDK Databricks **_automatizált_** gépi tanulás ![nélkül az Azure Machine Learning SDK Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK Databricks **automatizált** gépi tanulási ![SDK-val a Databricks-re telepített automatizált gépi tanulással](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Kezdje el az ismerkedést

Próbálja ki:
+ Bár számos mintajegyzetfüzet érhető el, **csak [ezek a mintajegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) működnek az Azure Databricks.**

+ Importálja ezeket a mintákat közvetlenül a munkaterületről. Lásd alább: ![](./media/how-to-configure-environment/azure-db-screenshot.png)
![Válassza az Importálás panel lehetőséget](./media/how-to-configure-environment/azure-db-import.png)

+ Ismerje meg, hogyan [hozhat létre egy folyamatot Databricks a betanítási számítási.](how-to-create-your-first-pipeline.md)

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Munkaterületkonfigurációs fájl létrehozása

A munkaterület konfigurációs fájl egy JSON-fájl, amely megmondja az SDK-nak, hogyan kommunikáljon az Azure Machine Learning-munkaterülettel. A fájl neve *config.json*, és a következő formátumú:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Ennek a JSON-fájlnak a Python-parancsfájlokat vagy jupyter-jegyzetfüzeteket tartalmazó könyvtárstruktúrában kell lennie. Lehet ugyanabban a könyvtárban, egy *.azureml*nevű alkönyvtárban vagy szülőkönyvtárban.

Ha ezt a fájlt a `ws=Workspace.from_config()`kódból szeretné használni, használja a használatát. Ez a kód betölti a fájlból származó adatokat, és csatlakozik a munkaterülethez.

A konfigurációs fájlt háromféleképpen hozhatja létre:

* **Használja [a ws.write_config:](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** *config.json* fájl írását. A fájl a munkaterület konfigurációs adatait tartalmazza. A *config.json* fájl más fejlesztői környezetbe tölthető vagy másolható.

* **Töltse le a fájlt:** Az [Azure Portalon](https://ms.portal.azure.com)válassza **a Config.json letöltése lehetőséget** a munkaterület Áttekintés **szakaszában.**

     ![Azure portál](./media/how-to-configure-environment/configure.png)

* **A fájl létrehozása programozott módon:** A következő kódrészletben az előfizetés-azonosító, az erőforráscsoport és a munkaterület nevének megadásával csatlakozhat egy munkaterülethez. Ezután menti a munkaterület konfigurációját a fájlba:

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

    Ez a kód a konfigurációs fájlt az *.azureml/config.json* fájlba írja.

## <a name="next-steps"></a>További lépések

- [Modell betanítása](tutorial-train-models-with-aml.md) az Azure Machine Learningen az MNIST adatkészlettel
- Az [Azure Machine Learning SDK python-hivatkozásának](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) megtekintése
