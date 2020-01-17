---
title: Python fejlesztői környezet beállítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan konfigurálhatja Azure Machine Learning fejlesztési környezetét. Használjon Conda-környezeteket, hozzon létre konfigurációs fájlokat, és konfigurálja saját felhőalapú notebook-kiszolgálóját, a Jupyter notebookokat, az Azure Databricks, az ide, a Code Editort és a Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: b70fcb1d63636984e1d014723b50170651a553d9
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156879"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Fejlesztési környezet konfigurálása Azure Machine Learninghoz
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy fejlesztési környezetet a Azure Machine Learning való együttműködéshez. Azure Machine Learning a platform agnosztikus. A fejlesztői környezetének egyetlen nehéz követelménye a Python 3. Egy elkülönített környezet (például anaconda vagy Virtualenv) is ajánlott.

A következő táblázat a jelen cikkben tárgyalt összes fejlesztési környezetet tartalmazza, valamint az előnyeit és hátrányait.

| Környezet | Előnyök | Hátrányok |
| --- | --- | --- |
| [Felhőalapú Azure Machine Learning számítási példány (előzetes verzió)](#compute-instance) | A legegyszerűbb módszer a kezdéshez. A teljes SDK már telepítve van a munkaterület virtuális gépén, és a jegyzetfüzet-oktatóanyagok előre klónozottak, és készen állnak a futtatásra. | A fejlesztési környezet és a függőségek szabályozásának hiánya. A Linux rendszerű virtuális gépekkel kapcsolatos további költségek (a virtuális gép leállítható, ha nem használatban van a költségek elkerülése érdekében). Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Helyi környezet](#local) | A fejlesztési környezet és a függőségek teljes körű vezérlése. Bármilyen felépíthető eszközzel, környezettel vagy tetszőleges IDE-val futtatható. | A kezdéshez tovább tart. Telepíteni kell a szükséges SDK-csomagokat, és a környezetnek is telepítve kell lennie, ha még nem rendelkezik ilyennel. |
| [Azure Databricks](#aml-databricks) | Ideális megoldás a méretezhető Apache Spark platformon nagy léptékű, intenzív gépi tanulási munkafolyamatok futtatására. | A kísérleti gépi tanulás vagy a kisebb léptékű kísérletek és munkafolyamatok meggyilkolása. További költségek Azure Databricksért. Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/databricks/). |
| [A Data Science Virtual Machine (DSVM)](#dsvm) | A felhőalapú számítási példányhoz hasonlóan (a Python és az SDK előre telepítve van), de további népszerű adatelemzési és gépi tanulási eszközökkel előre telepítve van. Egyszerűen méretezhető és kombinálható más egyéni eszközökkel és munkafolyamatokkal. | Lassabban megkezdhető a felhőalapú számítási példányhoz képest. |


Ez a cikk további használati tippeket is tartalmaz a következő eszközökhöz:

* [Jupyter-jegyzetfüzetek](#jupyter): Ha már használja a Jupyter notebook, az SDK-nak van néhány extrája, amelyet telepítenie kell.

* [Visual Studio Code](#vscode): Ha Visual Studio Code-t használ, a [Azure Machine learning bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) kiterjedt nyelvi támogatást nyújt a Pythonhoz, valamint olyan funkciókat is kínál, amelyek sokkal kényelmesebb és hatékonyabban használják a Azure Machine learning.

## <a name="prerequisites"></a>Előfeltételek

Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásával kapcsolatban tekintse meg [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md)című témakört. A munkaterület csak a saját [felhőalapú notebook-kiszolgálóval](#compute-instance), a [DSVM](#dsvm)vagy a [Azure Databrickstel](#aml-databricks)kezdheti meg.

A [helyi számítógép](#local)SDK-környezetének telepítéséhez a [Jupyter notebook Server](#jupyter) vagy a [Visual Studio Code](#vscode) is szükséges:

- Vagy a [anaconda](https://www.anaconda.com/download/) vagy a [Miniconda](https://conda.io/miniconda.html) csomagkezelő.

- Linux vagy macOS rendszeren a bash rendszerhéjra van szükség.

    > [!TIP]
    > Ha Linux vagy macOS rendszerű, és a bashtől eltérő (például zsh) rendszerhéjat használ, előfordulhat, hogy bizonyos parancsok futtatásakor hibák jelentkeznek. A probléma megkerüléséhez használja a `bash` parancsot egy új bash-rendszerhéj elindításához és a parancsok futtatásához.

- Windows rendszeren szükség van a parancssorra vagy az anaconda promptra (amelyet a anaconda és a Miniconda telepít).

## <a id="compute-instance"></a>Saját felhőalapú számítási példány

A Azure Machine Learning [számítási példány (előzetes verzió)](concept-compute-instance.md) egy biztonságos felhőalapú Azure-munkaállomás, amely Jupyter notebook-kiszolgálóval, JupyterLab és teljes mértékben előkészített ml-környezettel biztosítja az adatszakértőket.

> [!NOTE]
> A számítási példányok csak az **USA északi középső** régióját vagy **Egyesült Királyság déli régiójat**tartalmazó munkaterületekhez érhetők el.
>Ha a munkaterület bármely más régióban található, akkor továbbra is létrehozhat és használhat [notebookos virtuális gépet](concept-compute-instance.md#notebookvm) .

A számítási példányok telepítése és konfigurálása nem szükséges.  Hozzon létre egyet bármikor a Azure Machine Learning munkaterületen belülről. Adjon meg csak egy nevet, és adjon meg egy Azure-beli virtuális gép típusát. Próbálja ki most ezt az [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md).


További információ a [számítási példányokról](concept-compute-instance.md).

A számítási költségek kiszámításához [állítsa le a számítási példányt](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a id="dsvm"></a>Data Science Virtual Machine

A DSVM egy testreszabott virtuálisgép-(VM-) rendszerkép. Ez az adatelemzési munkához készült, amely előre konfigurálva van a következővel:

  - Csomagok, mint például a TensorFlow, a PyTorch, a Scikit-Learn, a XGBoost és a Azure Machine Learning SDK
  - Népszerű adatelemzési eszközök, mint például a Spark standalone és a drill
  - Azure-eszközök, például az Azure CLI, a AzCopy és a Storage Explorer
  - Integrált fejlesztői környezetek (ide), például a Visual Studio Code és a Notebookshoz
  - Jupyter Notebook kiszolgáló

A Azure Machine Learning SDK a DSVM Ubuntu-vagy Windows-verziójával működik. Ha azonban azt tervezi, hogy a DSVM számítási célként is használja, akkor csak az Ubuntu támogatott.

A DSVM használata fejlesztési környezetként:

1. Hozzon létre egy DSVM az alábbi környezetek egyikében:

    * A Azure Portal:

        * [Ubuntu-Data Science Virtual Machine létrehozása](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Windows Data Science Virtual Machine létrehozása](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Az Azure CLI:

        > [!IMPORTANT]
        > * Ha az Azure CLI-t használja, először be kell jelentkeznie az Azure-előfizetésbe az `az login` parancs használatával.
        >
        > * Ha az ebben a lépésben szereplő parancsokat használja, meg kell adnia az erőforráscsoport nevét, a virtuális gép nevét, a felhasználónevet és a jelszót.

        * Ubuntu Data Science Virtual Machine létrehozásához használja a következő parancsot:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Windows Data Science Virtual Machine létrehozásához használja a következő parancsot:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. A Azure Machine Learning SDK már telepítve van a DSVM. Az SDK-t tartalmazó Conda-környezet használatához használja a következő parancsok egyikét:

    * Ubuntu DSVM esetén:

        ```shell
        conda activate py36
        ```

    * Windows DSVM esetén:

        ```shell
        conda activate AzureML
        ```

1. Annak ellenőrzéséhez, hogy elérhető-e az SDK, és ellenőrizze a verziót, használja a következő Python-kódot:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Ha a DSVM a Azure Machine Learning munkaterület használatára szeretné konfigurálni, tekintse meg a [munkaterület-konfigurációs fájl létrehozása](#workspace) című szakaszt.

További információ: [Adatelemzési Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Helyi számítógép

Ha helyi számítógépet használ (amely távoli virtuális gép is lehet), hozzon létre egy Anaconda-környezetet, és telepítse az SDK-t. Például:

1. Ha még nincs telepítve, töltse le és telepítse az [anacondat](https://www.anaconda.com/distribution/#download-section) (Python 3,7 verzió).

1. Nyisson meg egy Anaconda-parancssort, és hozzon létre egy környezetet a következő parancsokkal:

    Futtassa a következő parancsot a környezet létrehozásához.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Ezután aktiválja a környezetet.

    ```shell
    conda activate myenv
    ```

    Ez a példa egy, a Python 3.6.5 használatával létrehozott környezetet hoz létre, de az egyes alverziókat is kiválaszthatja. Előfordulhat, hogy az SDK kompatibilitása bizonyos főverziók esetében nem garantált (3,5 + ajánlott), és ha hibát tapasztal, érdemes egy másik verziót/alverziót kipróbálni az anaconda-környezetben. A környezet létrehozása több percet is igénybe vehet, miközben az összetevők és csomagok letöltődnek.

1. Futtassa az alábbi parancsokat az új környezetben a környezet-specifikus IPython-kernelek engedélyezéséhez. Ez biztosítja a várt kernel-és csomag-importálási viselkedést a Jupyter-jegyzetfüzetek anaconda-környezeteken belüli használata esetén:

    ```shell
    conda install notebook ipykernel
    ```

    Ezután futtassa a következő parancsot a kernel létrehozásához:

    ```shell
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. A csomagok telepítéséhez használja az alábbi parancsokat:

    Ezzel a paranccsal a notebook-és `automl`-extrákkal telepítheti a Base Azure Machine Learning SDK-t. A `automl` extra egy nagyméretű telepítés, és a szögletes zárójelből is eltávolítható, ha nem szeretne automatizált gépi tanulási kísérleteket futtatni. A `automl` extra a Azure Machine Learning adat-előkészítési SDK-t is tartalmazza alapértelmezett függőségként.

    ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Ha olyan üzenetet kap, hogy a PyYAML nem távolítható el, használja inkább a következő parancsot:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * A macOS Catalinatől kezdve a zsh (Z Shell) az alapértelmezett bejelentkezési rendszerhéj és az interaktív rendszerhéj. A zsh-ben használja a következő parancsot, amely a "\\" (fordított perjel) zárójelekkel rendelkezik:
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`


   Az SDK telepítése több percet is igénybe vehet. További információ a telepítési lehetőségekről: telepítési [útmutató](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Telepítsen további csomagokat a Machine learning-kísérletezéshez.

    Használja a következő parancsok egyikét, és cserélje le *\<új csomag >* a telepíteni kívánt csomagra. A csomagok `conda install` használatával történő telepítése megköveteli, hogy a csomag az aktuális csatornák része legyen (az új csatornák hozzáadhatók a anaconda Cloud-ban).

    ```shell
    conda install <new package>
    ```

    Azt is megteheti, hogy `pip`használatával telepít csomagokat.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter notebookok

A Jupyter-jegyzetfüzetek a [Jupyter projekt](https://jupyter.org/)részét képezik. Interaktív kódolási élményt biztosítanak, ahol olyan dokumentumokat hozhat létre, amelyek élő kódot és szövegeket és grafikákat vegyítenek. A Jupyter notebookok szintén nagyszerű megoldást nyújtanak az eredmények másokkal való megosztására, mivel a dokumentumban lévő kódrészletek kimenetét mentheti el. A Jupyter notebookok számos platformon telepíthetők.

A [helyi számítógép](#local) szakasz eljárása telepíti a szükséges összetevőket a Jupyter-jegyzetfüzetek anaconda-környezetben való futtatásához.

Az összetevők engedélyezése a Jupyter Notebook környezetben:

1. Nyisson meg egy Anaconda-parancssort, és aktiválja a környezetet.

    ```shell
    conda activate myenv
    ```

1. [A GitHub-tárház](https://aka.ms/aml-notebooks) klónozása minta típusú jegyzetfüzetek készletében.

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Indítsa el a Jupyter Notebook kiszolgálót a következő paranccsal:

    ```shell
    jupyter notebook
    ```

1. Annak ellenőrzéséhez, hogy Jupyter Notebook használhatja-e az SDK-t, hozzon létre egy **új** jegyzetfüzetet, válassza a **Python 3** lehetőséget a rendszermagként, majd futtassa a következő parancsot egy jegyzetfüzet-cellában:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Ha problémákat tapasztal a modulok importálásakor és a `ModuleNotFoundError`fogadásakor, győződjön meg arról, hogy a Jupyter-kernel a környezete megfelelő elérési útjához csatlakozik, ehhez futtassa a következő kódot egy jegyzetfüzet-cellában.

    ```python
    import sys
    sys.path
    ```

1. A Jupyter Notebook a Azure Machine Learning munkaterület használatára való konfigurálásához lépjen a munkaterület- [konfigurációs fájl létrehozása](#workspace) szakaszra.


### <a id="vscode"></a>Visual Studio Code

A Visual Studio Code egy rendkívül népszerű platformfüggetlen Kódszerkesztő, amely a [Visual Studio Marketplace](https://marketplace.visualstudio.com/vscode)-en keresztül elérhető bővítmények széles körét támogatja a programozási nyelveken és eszközökön. A [Azure Machine learning bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) telepíti a [Python-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Python-környezetek (virtuális, anaconda stb.) összes típusának kódolásához. Emellett kényelmi funkciókat biztosít a Azure Machine Learning erőforrásokkal való munkához és a Azure Machine Learning kísérletek futtatásához anélkül, hogy a Visual Studio Code-ot el kellene hagyni.

A Visual Studio Code használata a fejlesztéshez:

1. Telepítse a Azure Machine Learning-bővítményt a Visual Studio Code-hoz, lásd: [Azure Machine learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    További információ: [Azure Machine learning használata a Visual Studio Code](tutorial-setup-vscode-extension.md)-hoz.

1. Ismerje meg, hogyan használható a Visual Studio Code bármilyen típusú Python-fejlesztéshez: [Bevezetés a Python használatába a VSCode-ben](https://code.visualstudio.com/docs/python/python-tutorial).

    - Az SDK-t tartalmazó SDK Python-környezet kiválasztásához nyissa meg a VS Code parancsot, majd válassza a CTRL + SHIFT + P (Linux és Windows) vagy a Command + Shift + P (Mac) lehetőséget.
        - Megnyílik a __parancs paletta__ .

    - Írja be a __Python: Select tolmács elemet__, majd válassza ki a megfelelő környezetet

1. Annak ellenőrzéséhez, hogy használhatja-e az SDK-t, hozzon létre egy új Python-fájlt (...), amely a következő kódot tartalmazza:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Futtassa ezt a kódot a "cella futtatása" Codelensben kattintva, vagy egyszerűen nyomja le a SHIFT-ENTER billentyűkombinációt.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
A Azure Databricks egy Apache Spark-alapú környezet az Azure-felhőben. Együttműködik a notebookon alapuló, CPU-vagy GPU-alapú számítási fürttel.

Hogyan működik a Azure Databricks Azure Machine Learning:
+ A modelleket Spark MLlib használatával is betaníthatja, és a modellt ACI/AK-ba helyezheti el Azure Databricks belülről.
+ Az [automatizált gépi tanulási](concept-automated-ml.md) képességeket egy speciális Azure ml SDK-val is használhatja Azure Databricks.
+ A Azure Databricks számítási célként [Azure Machine learning folyamatból](concept-ml-pipelines.md)is használhatja.

### <a name="set-up-your-databricks-cluster"></a>A Databricks-fürt beállítása

Hozzon létre egy [Databricks-fürtöt](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Egyes beállítások csak akkor érvényesek, ha az SDK-t a Databricks-on lévő automatizált gépi tanuláshoz telepíti.
**A fürt létrehozása néhány percig is eltarthat.**

Használja ezeket a beállításokat:

| Beállítás |A következőkre vonatkozik| Value (Díj) |
|----|---|---|
| Fürt neve |mindig| yourclustername |
| A Databricks futtatókörnyezete |mindig|Nem ML futtatókörnyezet 6,0 (Scala 2,11, Spark 2.4.3) |
| Python-verzió |mindig| 3 |
| Feldolgozók |mindig| 2 vagy magasabb |
| Munkavégző csomópont virtuálisgép-típusai <br>(meghatározza az egyidejű ismétlések maximális számát) |Automatizált ML<br>csak| A memóriára optimalizált virtuális gép előnyben részesített |
| Automatikus skálázás engedélyezése |Automatizált ML<br>csak| Törölje a jelet a |

A folytatás előtt várjon, amíg a fürt fut.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>A megfelelő SDK telepítése Databricks-tárba
Ha a fürt fut, [hozzon létre egy függvénytárat](https://docs.databricks.com/user-guide/libraries.html#create-a-library) , hogy csatolja a megfelelő Azure Machine learning SDK-csomagot a fürthöz.

1. Kattintson a jobb gombbal arra a munkaterület-mappára, ahol a könyvtárat tárolni szeretné. Válassza a **létrehozás** > **könyvtár**lehetőséget.

1. **Csak egy** lehetőséget válasszon (más SDK-telepítés nem támogatott)

   |SDK&nbsp;csomag&nbsp;extrák|Forrás|PyPi&nbsp;neve&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks| Python-tojás vagy PyPI feltöltése | azureml – SDK [databricks]|
   |Databricks esetében –<br> automatizált ML-képességek| Python-tojás vagy PyPI feltöltése | azureml – SDK [automl]|

   > [!Warning]
   > Más SDK-extrák nem telepíthetők. Válassza ki a fenti beállítások közül a [databricks] vagy a [automl] lehetőséget.

   * Ne válassza **az Automatikus csatolás az összes fürthöz**lehetőséget.
   * Válassza a **csatolás** elemet a fürt neve mellett.

1. A hibák figyelése, amíg az állapot a **csatolt**értékre módosul, ami több percet is igénybe vehet.  Ha ez a lépés meghiúsul:

   Próbálja meg újraindítani a fürtöt a alábbiak szerint:
   1. A bal oldali ablaktáblán válassza a **fürtök**lehetőséget.
   1. A táblázatban válassza ki a fürt nevét.
   1. A **tárak** lapon válassza az **Újraindítás**lehetőséget.

   Vegye figyelembe a következőket is:
   + A AutoML config Azure Databricks használatakor adja hozzá a következő paramétereket:
       1. ```max_concurrent_iterations``` a fürt munkavégző csomópontjainak száma alapján történik.
        2. a ```spark_context=sc``` az alapértelmezett Spark-környezeten alapul.
   + Ha pedig egy régi SDK-verzióval rendelkezik, törölje a fürt telepített libs elemét, és váltson a kukába. Telepítse az új SDK-verziót, és indítsa újra a fürtöt. Ha az újraindítás után probléma merül fel, válassza le és csatlakoztassa újra a fürtöt.

Ha a telepítés sikeres volt, az importált függvénytárnak a következőhöz hasonlóan kell kinéznie:

Databricks-hez készült SDK a Databricks Azure Machine Learning SDK-val **_nem rendelkező_** automatizált Machine learning-![](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Databricks-hez **készült** SDK az automatikus gépi tanulás ![SDK-val a Databricks-on telepített automatikus gépi tanulással](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Kezdje el az ismerkedést

Próbálja ki:
+ Habár több jegyzetfüzet is elérhető, **csak ezek a [minta-jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) működnek együtt Azure Databricksokkal.**

+ Importálja ezeket a mintákat közvetlenül a munkaterületről. Lásd alább: ![válassza az importálás](./media/how-to-configure-environment/azure-db-screenshot.png)
![importálás panelt](./media/how-to-configure-environment/azure-db-import.png)

+ Megtudhatja, hogyan [hozhat létre egy folyamatot a Databricks, mint a betanítási számításokat](how-to-create-your-first-pipeline.md).

## <a id="workspace"></a>Munkaterület-konfigurációs fájl létrehozása

A munkaterület-konfigurációs fájl egy JSON-fájl, amely közli az SDK-val, hogyan kommunikálhat a Azure Machine Learning munkaterülettel. A fájl neve *config. JSON*, és a formátuma a következő:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

A JSON-fájlnak a Python-szkripteket vagy Jupyter-jegyzetfüzeteket tartalmazó címtár-struktúrában kell lennie. Ez lehet ugyanabban a címtárban, egy *. azureml*nevű alkönyvtár vagy egy szülő könyvtárban.

Ha ezt a fájlt a kódból szeretné használni, használja a `ws=Workspace.from_config()`. Ez a kód betölti az adatokat a fájlból, és csatlakozik a munkaterülethez.

A konfigurációs fájlt háromféleképpen is létrehozhatja:

* **A [ws. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** : paranccsal írjon be egy *config. JSON* fájlt. A fájl a munkaterület konfigurációs adatait tartalmazza. A *config. JSON* fájlt letöltheti vagy másolhatja más fejlesztői környezetbe.

* **Töltse le a fájlt**: a [Azure Portal](https://ms.portal.azure.com)a munkaterület **Áttekintés** szakaszában válassza a **config. JSON letöltése** lehetőséget.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Hozza létre a fájlt programozott**módon: az alábbi kódrészletben az előfizetés-azonosító, az erőforráscsoport és a munkaterület nevének megadásával csatlakozhat egy munkaterülethez. Ezután menti a munkaterület konfigurációját a következő fájlba:

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

    Ez a kód a konfigurációs fájlt a *. azureml/config. JSON* fájlba írja.


## <a name="next-steps"></a>Következő lépések

- [Modell Betanítása](tutorial-train-models-with-aml.md) Azure Machine learningre a MNIST adatkészlettel
- A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -dokumentáció megtekintése
