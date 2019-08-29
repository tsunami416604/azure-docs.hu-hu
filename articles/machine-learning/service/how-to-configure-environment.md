---
title: Python fejlesztői környezet beállítása
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan konfigurálhat fejlesztési környezetet, ha együttműködik a Azure Machine Learning szolgáltatással. Ebből a cikkből megtudhatja, hogyan használhatja a Conda-környezeteket, hogyan hozhat létre konfigurációs fájlokat, és hogyan konfigurálhat saját felhőalapú notebook-kiszolgálót, Jupyter notebookokat, Azure Databricks, Azure Notebookseket, ide-kódokat és a Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 220f68461d47293e9f43a650e4fa5d1d59bce02f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128337"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Az Azure Machine Learning a fejlesztési környezet konfigurálása

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy fejlesztési környezetet Azure Machine Learning szolgáltatással való együttműködéshez. Azure Machine Learning szolgáltatás a platform agnosztikus. A fejlesztői környezetének egyetlen nehéz követelménye a Python 3. Egy elkülönített környezet (például anaconda vagy Virtualenv) is ajánlott.

A következő táblázat a jelen cikkben tárgyalt összes fejlesztési környezetet tartalmazza, valamint az előnyeit és hátrányait.

| Környezet | Szakemberek számára | Hátrányok |
| --- | --- | --- |
| [Felhőalapú notebook-alapú virtuális gép](#notebookvm) | A legegyszerűbb módszer a kezdéshez. A teljes SDK már telepítve van a munkaterület virtuális gépén, és a jegyzetfüzet-oktatóanyagok előre klónozottak, és készen állnak a futtatásra. | A fejlesztési környezet és a függőségek szabályozásának hiánya. A Linux rendszerű virtuális gépekkel kapcsolatos további költségek (a virtuális gép leállítható, ha nem használatban van a költségek elkerülése érdekében). Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Helyi környezet](#local) | A fejlesztési környezet és a függőségek teljes körű vezérlése. Bármilyen felépíthető eszközzel, környezettel vagy tetszőleges IDE-val futtatható. | A kezdéshez tovább tart. Telepíteni kell a szükséges SDK-csomagokat, és a környezetnek is telepítve kell lennie, ha még nem rendelkezik ilyennel. |
| [Azure Databricks](#aml-databricks) | Ideális megoldás a méretezhető Apache Spark platformon nagy léptékű, intenzív gépi tanulási munkafolyamatok futtatására. | A kísérleti gépi tanulás vagy a kisebb léptékű kísérletek és munkafolyamatok meggyilkolása. További költségek Azure Databricksért. Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/databricks/). |
| [A Data Science Virtual Machine (DSVM)](#dsvm) | Hasonlóan a felhőalapú notebook virtuális géphez (a Python és az SDK előre telepítve van), de további népszerű adatelemzési és gépi tanulási eszközökkel előre telepítve van. Egyszerűen méretezhető és kombinálható más egyéni eszközökkel és munkafolyamatokkal. | A felhőalapú notebook virtuális géphez képest lassabban megkezdhető a kezdeti lépések. |
| [Azure Notebooks](#aznotebooks) | A Python és az SDK előzetes telepítése ingyenes és könnyű használatot nyújt. | A felhőalapú notebook virtuális géphez képest kevésbé nagy teljesítményű virtuális gépek érhetők el. Elkülönített a munkaterületről és egyéb erőforrásokról. |

Ez a cikk további használati tippeket is tartalmaz a következő eszközökhöz:

* [Jupyter jegyzetfüzetek](#jupyter): Ha már használja a Jupyter Notebook, az SDK-nak van néhány extrája, amelyet telepítenie kell.

* [Visual Studio Code](#vscode): Ha Visual Studio Code-ot használ, néhány hasznos bővítményt is telepíthet.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Machine Learning szolgáltatás munkaterületén. A munkaterület létrehozásával kapcsolatban tekintse meg a [Azure Machine learning szolgáltatás munkaterületének létrehozása](how-to-manage-workspace.md)című témakört. A munkaterület csak akkor érhető el, ha a saját [felhőalapú notebook-kiszolgálóval](#notebookvm), [DSVM](#dsvm), [Azure Databricks](#aml-databricks)vagy Azure Notebooksval szeretne kezdeni [](#aznotebooks).

A [helyi számítógép](#local)SDK-környezetének telepítéséhez a [Jupyter notebook Server](#jupyter) vagy a [Visual Studio Code](#vscode) is szükséges:

- Vagy a [anaconda](https://www.anaconda.com/download/) vagy a [Miniconda](https://conda.io/miniconda.html) csomagkezelő.

- Linux vagy macOS rendszeren a bash rendszerhéjra van szükség.

    > [!TIP]
    > Ha Linux vagy macOS rendszerű, és a bashtől eltérő (például zsh) rendszerhéjat használ, előfordulhat, hogy bizonyos parancsok futtatásakor hibák jelentkeznek. Ez a probléma megkerüléséhez használja a `bash` paranccsal indítsa el az új rendszerhéjakba, és ott futtassa a parancsokat.

- A Windows, a parancssort vagy szükséges Anaconda parancssort (telepített Anaconda és Miniconda).

## <a id="notebookvm"></a>Saját felhőalapú jegyzetfüzet-alapú virtuális gép

A notebook virtuális gép (előzetes verzió) egy biztonságos felhőalapú Azure-munkaállomás, amely Jupyter notebook-kiszolgálóval, JupyterLab és teljes mértékben előkészített ML-környezettel biztosítja az adatszakértőket.

A notebook virtuális gép:

+ **Biztonságos**. Mivel a virtuális gépek és a notebookok hozzáférése HTTPS-vel és Azure Active Directorytel van ellátva, alapértelmezés szerint az informatikai szakemberek egyszerűen kihasználhatják az egyszeri bejelentkezést és más biztonsági funkciókat, például a többtényezős hitelesítést.

+ **Előre konfigurált**. Ez a teljes mértékben előkészített Python ML-környezet a népszerű IaaS Data Science VM, és a következőket tartalmazza:
  + Azure ML Python SDK (legújabb)
  + Automatikus konfiguráció a munkaterülettel való munkavégzéshez
  + Egy Jupyter notebook-kiszolgáló
  + JupyterLab notebook IDE
  + Előre konfigurált GPU-illesztőprogramok
  + Mély tanulási keretrendszerek kiválasztása


  Ha kódot használ, a virtuális gép oktatóanyagokat és mintákat tartalmaz, amelyek segítségével megismerheti a Azure Machine Learning szolgáltatás használatát. A minta jegyzetfüzeteket a munkaterület Azure Blob Storage fiókjában tárolja, így azok megoszthatók a virtuális gépek között. A futtatáskor hozzáférhetnek a munkaterület adattárakhoz és számítási erőforrásaihoz is.

+ **Egyszerű beállítás**: Hozzon létre egyet bármikor a Azure Machine Learning munkaterületen belülről. Adjon meg csak egy nevet, és adjon meg egy Azure-beli virtuális gép típusát. Próbálja ki most ezt [az oktatóanyagot: Környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md)beállítása.

+ **Testreszabható**. A felügyelt és biztonságos virtuálisgép-ajánlat során teljes hozzáférést biztosít a hardver képességeihez, és testre szabhatja a szíved iránti vágyát. Például gyorsan létrehozhatja a legújabb NVidia V100-alapú virtuális gépet az új neurális hálózati architektúra lépésenkénti hibakeresésének végrehajtásához.

A notebook virtuális gépekkel kapcsolatos költségek leállításához [állítsa le a notebook virtuális gépet](tutorial-1st-experiment-sdk-setup.md#stop-the-notebook-vm). 

## <a id="dsvm"></a>Adatelemző virtuális gép

A DSVM egy testreszabott virtuálisgép-(VM-) rendszerkép. Ez az adatelemzési munkához készült, amely előre konfigurálva van a következővel:

  - Csomagok, mint például a TensorFlow, a PyTorch, a Scikit-Learn, a XGBoost és a Azure Machine Learning SDK
  - Népszerű adatelemzési eszközök, mint például a Spark standalone és a drill
  - Azure-eszközök, például az Azure CLI, a AzCopy és a Storage Explorer
  - Integrált fejlesztői környezetek (ide), például a Visual Studio Code és a Notebookshoz
  - Jupyter Notebook Server

A Azure Machine Learning SDK a DSVM Ubuntu-vagy Windows-verziójával működik. Ha azonban azt tervezi, hogy a DSVM számítási célként is használja, akkor csak az Ubuntu támogatott.

A DSVM fejlesztési környezetként való használatához tegye a következőket:

1. Hozzon létre egy DSVM az alábbi környezetek egyikében:

    * A Azure Portal:

        * [Ubuntu-Data Science Virtual Machine létrehozása](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Windows Data Science Virtual Machine létrehozása](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Az Azure CLI:

        > [!IMPORTANT]
        > * Ha az Azure CLI-t használja, először be kell jelentkeznie az Azure-előfizetésbe `az login` az parancs használatával.
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

2. A Azure Machine Learning SDK már telepítve van a DSVM. A Conda-környezet, amely tartalmazza az SDK-t használ, használja a következő parancsok egyikét:

    * Ubuntu DSVM esetén:

        ```shell
        conda activate py36
        ```

    * Windows DSVM esetén:

        ```shell
        conda activate AzureML
        ```

1. Győződjön meg arról, hogy az SDK eléréséhez, és ellenőrizze a verziót, használja a következő Python-kódban:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Ha a DSVM a Azure Machine Learning szolgáltatás munkaterületének használatára szeretné konfigurálni, tekintse meg a [munkaterület-konfigurációs fájl létrehozása](#workspace) című szakaszt.

További információ: adatelemzési [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Helyi számítógép

Ha helyi számítógépet használ (amely távoli virtuális gép is lehet), hozzon létre egy Anaconda-környezetet, és telepítse az SDK-t a következő módon:

1. Ha még nincs [](https://www.anaconda.com/distribution/#download-section) telepítve, töltse le és telepítse az anacondat (Python 3,7 verzió).

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

1. Futtassa az alábbi parancsokat az új környezetben a környezet-specifikus ipython-kernelek engedélyezéséhez. Ez biztosítja a várt kernel-és csomag-importálási viselkedést a Jupyter-jegyzetfüzetek anaconda-környezeteken belüli használata esetén:

    ```shell
    conda install notebook ipykernel
    ```

    Ezután futtassa a következő parancsot a kernel létrehozásához:

    ```shell
    ipython kernel install --user
    ```

1. A csomagok telepítéséhez használja az alábbi parancsokat:

    Ezzel a paranccsal a notebook-és automl-extrákkal telepítheti a Base Azure Machine Learning SDK-t. Az `automl` extra egy nagyméretű telepítés, és a zárójelből is eltávolítható, ha nem szeretne automatizált gépi tanulási kísérleteket futtatni. Az `automl` extra a Azure Machine learning adat-előkészítési SDK-t is tartalmazza alapértelmezett függőségként.

    ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Ha olyan üzenetet kap, hogy a PyYAML nem távolítható el, használja inkább a következő parancsot:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * A macOS Catalinatől kezdve a zsh (Z Shell) az alapértelmezett bejelentkezési rendszerhéj és az interaktív rendszerhéj. A zsh-ben használja az alábbi parancsot, amely a "\\" (fordított perjel) karakterrel rendelkező zárójeleket követi:
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`


   Az SDK telepítése több percet is igénybe vehet. További információ a telepítési lehetőségekről: telepítési [útmutató](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Telepítsen további csomagokat a Machine learning-kísérletezéshez.

    Használja a következő parancsok egyikét, és cserélje le  *\<az új csomag >* a telepíteni kívánt csomagra. A csomagok az `conda install` -on keresztül történő telepítése megköveteli, hogy a csomag az aktuális csatornák része legyen (az új csatornák hozzáadhatók az anaconda Cloud-ban).

    ```shell
    conda install <new package>
    ```

    Azt is megteheti, hogy `pip`csomagokat telepít a használatával.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter-notebookok

A Jupyter Notebooks részét képezik a [Jupyter projekt](https://jupyter.org/). Ahol létrehozhatja az élő kód kombinálhatók a leíró szöveg és képek, dokumentumok interaktív kódolási élményt nyújtanak. A Jupyter notebookok szintén nagyszerű megoldást nyújtanak az eredmények másokkal való megosztására, mivel a dokumentumban lévő kódrészletek kimenetét mentheti el. A Jupyter Notebooks kiszállítására különböző platformokon is telepítheti.

A [helyi számítógép](#local) szakasz eljárása telepíti a szükséges összetevőket a Jupyter-jegyzetfüzetek anaconda-környezetben való futtatásához. Ezeknek az összetevőknek a Jupyter Notebook-környezetben való engedélyezéséhez tegye a következőket:

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

1. Ha problémákba ütközik a modulok importálása és a `ModuleNotFoundError`fogadása során, győződjön meg arról, hogy a Jupyter-kernel a környezete megfelelő elérési útjához csatlakozik, ehhez futtassa a következő kódot egy jegyzetfüzet-cellában.

    ```python
    import sys
    sys.path
    ```
    
1. A Jupyter Notebook a Azure Machine Learning szolgáltatás munkaterületének használatára való konfigurálásához lépjen a [munkaterület-konfigurációs fájl létrehozása](#workspace) szakaszra.


### <a id="vscode"></a>A Visual Studio Code

A Visual Studio Code egy platformfüggetlen Kódszerkesztő. Python-támogatás a helyi Python 3 és Conda telepítési támaszkodik, de további eszközöket biztosít a mesterséges Intelligencia való munkához. Azt is támogatást nyújt a Kódszerkesztő belül a Conda-környezet kiválasztása.

Ha a Visual Studio Code-ot szeretné használni a fejlesztéshez, tegye a következőket:

1. Ha szeretné megtudni, hogyan használható a Visual Studio Code a Python fejlesztéséhez, tekintse meg a következő témakört: a [Python használatának első lépései a VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. A Conda-környezet kiválasztásához nyissa meg a VS Code parancsot, majd válassza a CTRL + SHIFT + P (Linux és Windows) vagy a Command + Shift + P (Mac) lehetőséget.
    Megnyílik a __parancs-raklap__ .

1. Adja __meg a Pythont: Válassza a tolmács__lehetőséget, majd válassza ki a Conda-környezetet.

1. Annak ellenőrzéséhez, hogy használhatja-e az SDK-t, hozzon létre, majd futtasson egy új Python-fájlt (...), amely a következő kódot tartalmazza:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. A Visual Studio Code-hoz készült Azure Machine Learning-bővítmény telepítéséhez lásd: [eszközök a mesterséges intelligenciához](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    További információ: [Azure Machine learning használata a Visual Studio Code](how-to-vscode-tools.md)-hoz.

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
A Azure Databricks egy Apache Spark-alapú környezet az Azure-felhőben. Együttműködik a notebookon alapuló, CPU-vagy GPU-alapú számítási fürttel.

Hogyan működik a Azure Databricks Azure Machine Learning szolgáltatással:
+ A modelleket Spark MLlib használatával is betaníthatja, és a modellt ACI/AK-ba helyezheti el Azure Databricks belülről.
+ Az [automatizált gépi tanulási](concept-automated-ml.md) képességeket egy speciális Azure ml SDK-val is használhatja Azure Databricks.
+ A Azure Databricks számítási célként [Azure Machine learning folyamatból](concept-ml-pipelines.md)is használhatja.

### <a name="set-up-your-databricks-cluster"></a>A Databricks-fürt beállítása

Hozzon létre egy [Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)-fürtöt. Egyes beállítások csak akkor érvényesek, ha az SDK-t a Databricks-on lévő automatizált gépi tanuláshoz telepíti.
**A fürt létrehozása néhány percig is eltarthat.**

Használja ezeket a beállításokat:

| Beállítás |A következőkre vonatkozik:| Value |
|----|---|---|
| Fürt neve |mindig| yourclustername |
| A Databricks futtatókörnyezete |mindig| A nem ML-es futtatókörnyezet (nem ML 4. x, 5. x) |
| Python-verzió |mindig| 3 |
| Feldolgozók |mindig| 2 vagy magasabb |
| Munkavégző csomópont virtuálisgép-típusai <br>(meghatározza az egyidejű ismétlések maximális számát) |Automatizált ML<br>csak| A memóriára optimalizált virtuális gép előnyben részesített |
| Automatikus skálázás engedélyezése |Automatizált ML<br>csak| Akadálytalan |

A folytatás előtt várjon, amíg a fürt fut.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>A megfelelő SDK telepítése Databricks-tárba
Ha a fürt fut, [hozzon létre egy függvénytárat](https://docs.databricks.com/user-guide/libraries.html#create-a-library) , hogy csatolja a megfelelő Azure Machine learning SDK-csomagot a fürthöz.

1. **Csak egy** lehetőséget válasszon (más SDK-telepítés nem támogatott)

   |SDK&nbsp;package&nbsp;extras|Source|PyPi&nbsp;neve&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks| Python-tojás vagy PyPI feltöltése | azureml-sdk[databricks]|
   |Databricks esetében –<br> automatizált ML-képességek| Python-tojás vagy PyPI feltöltése | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Más SDK-extrák nem telepíthetők. Válassza ki a fenti beállítások közül a [databricks] vagy a [automl_databricks] lehetőséget.

   * Ne válassza **az Automatikus csatolás az összes fürthöz**lehetőséget.
   * Válassza a **csatolás** elemet a fürt neve mellett.

1. A hibák figyelése, amíg az állapot a **csatolt**értékre módosul, ami több percet is igénybe vehet.  Ha ez a lépés sikertelen, ellenőrizze a következőket:

   Próbálja meg újraindítani a fürtöt a alábbiak szerint:
   1. A bal oldali ablaktáblán válassza a **fürtök**lehetőséget.
   1. A táblázatban válassza ki a fürt nevét.
   1. A **tárak** lapon válassza az **Újraindítás**lehetőséget.

   Vegye figyelembe a következőket is:
   + A AutoML config Azure Databricks használatakor adja hozzá a következő paramétereket:
       1. ```max_concurrent_iterations```a fürt munkavégző csomópontjainak száma alapján történik.
        2. ```spark_context=sc```az alapértelmezett Spark-környezeten alapul.
   + Ha pedig egy régi SDK-verzióval rendelkezik, törölje a fürt telepített libs elemét, és váltson a kukába. Telepítse az új SDK-verziót, és indítsa újra a fürtöt. Ha az újraindítás után probléma merül fel, válassza le és csatlakoztassa újra a fürtöt.

Ha a telepítés sikeres volt, az importált függvénytárnak a következőhöz hasonlóan kell kinéznie:

SDK a Databricks -hez a Databricks ![-hez készült automatikus gépi tanulás nélkül Azure Machine learning SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK az Databricks-hez **készült** automatizált ![Machine learning SDK-val a Databricks-re telepített automatikus gépi tanulással](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Ismerkedés a felfedezéssel

Próbálja ki:
+ Töltse le a Azure Databricks/Azure Machine Learning SDK [notebook Archive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) -fájlját, és [importálja az archív fájlt](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) a Databricks-fürtbe.
  Habár több jegyzetfüzet is elérhető, **csak ezek a [minta](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) -jegyzetfüzetek működnek együtt Azure Databricksokkal.**

+ Megtudhatja, hogyan [hozhat létre egy folyamatot a Databricks, mint a](how-to-create-your-first-pipeline.md)betanítási számításokat.

## <a id="aznotebooks"></a>Az Azure notebookok

[Az Azure notebookok](https://notebooks.azure.com) (előzetes verzió) egy interaktív fejlesztési környezetben az Azure-felhőben. Ez egy egyszerű módszer a Azure Machine Learning fejlesztésének megkezdésére.

* A Azure Machine Learning SDK már telepítve van.
* Miután létrehozta Azure Machine Learning szolgáltatás munkaterületét a Azure Portalban, a gombra kattintva automatikusan konfigurálhatja az Azure notebook-környezetet a munkaterülettel való együttműködésre.

A Azure Notebooks használatának megkezdéséhez használja a [Azure Portal](https://portal.azure.com) .  Nyissa meg a munkaterületet, és az **Áttekintés** szakaszban válassza az első **lépések lehetőséget Azure Notebooks**.

Alapértelmezés szerint a Azure Notebooks egy ingyenes szolgáltatási szintet használ, amely legfeljebb 4 GB memóriával és 1 GB adattal rendelkezik. A korlátokat azonban eltávolíthatja egy Data Science Virtual Machine-példánynak a Azure Notebooks projekthez való csatolásával. További információ: [Azure Notebooks projektek kezelése és konfigurálása – számítási réteg](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a id="workspace"></a>Munkaterület-konfigurációs fájl létrehozása

A munkaterület-konfigurációs fájl egy JSON-fájl, amely közli az SDK-val, hogyan kommunikálhat a Azure Machine Learning szolgáltatás munkaterületével. A fájl neve *config. JSON*, és a formátuma a következő:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

A JSON-fájlnak a Python-szkripteket vagy Jupyter-jegyzetfüzeteket tartalmazó címtár-struktúrában kell lennie. Ez lehet ugyanabban a címtárban, egy *. azureml*nevű alkönyvtár vagy egy szülő könyvtárban.

Ezt a fájlt a kód használatához `ws=Workspace.from_config()`. Ez a kód az adatokat tölt be a fájlt, és a munkaterülethez csatlakozik.

A konfigurációs fájlt háromféleképpen is létrehozhatja:

* **A [ws. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** : paranccsal írhat egy *config. JSON* fájlt. A fájl a munkaterület konfigurációs adatait tartalmazza. A *config. JSON* fájlt letöltheti vagy másolhatja más fejlesztői környezetbe.

* **Töltse le a fájlt**: A [Azure Portal](https://ms.portal.azure.com)a munkaterület **Áttekintés** szakaszában válassza a **config. JSON letöltése** lehetőséget.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Hozza létre a fájlt programozott**módon: A következő kódrészletben az előfizetés-azonosító, az erőforráscsoport és a munkaterület nevének megadásával csatlakozhat egy munkaterülethez. Ezután menti a munkaterület konfigurációját a következő fájlba:

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


## <a name="next-steps"></a>További lépések

- [Modell](tutorial-train-models-with-aml.md) betanítása Azure Machine learningre a MNIST adatkészlettel
- A Pythonhoz készült [Azure Machine learning SDK](https://aka.ms/aml-sdk) -dokumentáció megtekintése
- Tudnivalók a [Azure Machine learning](https://aka.ms/data-prep-sdk) adatelőkészítési csomagjáról
