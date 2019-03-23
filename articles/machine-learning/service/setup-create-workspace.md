---
title: Munkaterület létrehozása
titleSuffix: Azure Machine Learning service
description: Az Azure Portalon, az SDK-t, egy sablon vagy a parancssori felület használatával az Azure Machine Learning szolgáltatás munkaterület létrehozása. Ez a munkaterület egy központi helyen hoz létre az Azure Machine Learning szolgáltatás használatakor minden összetevő dolgozhat biztosít.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 03/21/2019
ms.openlocfilehash: 2c3b63e671240a239dc2037ce56e56af4a8f95e7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369156"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Az Azure Machine Learning szolgáltatás munkaterület létrehozása

Az Azure Machine Learning szolgáltatás használatához a kell egy [ **Azure Machine Learning szolgáltatás munkaterület**](concept-azure-machine-learning-architecture.md#workspace).  Ez a munkaterület a szolgáltatás legfelső szintű erőforrás- és biztosít egy központi helyen hoz létre minden összetevő dolgozhat. 

Ebből a cikkből megismerheti, hogyan hozhat létre egy munkaterületet, ezek a módszerek bármelyikével: 
* A [az Azure portal](#portal) felület
* A [az Azure Machine Learning SDK a Pythonhoz](#sdk)
* Egy [Azure Resource Manager-sablon](#template)
* A [az Azure Machine Learning parancssori felület](#cli)

A munkaterületet hoz létre, az itt található lépéseket követve más oktatóanyagok és útmutatók előfeltételként is használható. 

A munkaterület létrehozásakor a következő Azure-erőforrások lettek hozzáadva, automatikusan (Ha még regionálisan érhető el):
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Csakúgy, mint más Azure-szolgáltatások, bizonyos korlátok és kvóták társítva a Machine Learning. [Ismerje meg a kvótákat és azt, hogyan kérhet belőlük többet.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Előfeltételek
Munkaterület létrehozásához Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

## <a name="portal"></a> Az Azure Portalon

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Függetlenül attól, hogy hogyan hozták létre, megtekintheti a munkaterületéhez a [az Azure portal](https://portal.azure.com/).  Lásd: [munkaterület megjelenítése](how-to-manage-workspace.md#view) részleteiről.

## <a name="sdk"></a> Python SDK

A munkaterületet, a Python SDK-t hozhat létre. Először telepítenie kell az SDK-t.

> [!IMPORTANT]
> Ha egy Azure adatelemzési virtuális gép vagy az Azure Databricks, hagyja ki az SDK telepítése.
> * Az Azure Data Science Virtual Machines 2018. szeptember 27. után létrehozott származnak az előre telepített Python SDK-val. Kihagyja a telepítését, és kezdje [hozzon létre egy munkaterületet, az SDK-val](#sdk-create).
> * Az Azure Databricks a környezetben, használja a [Databricks telepítési lépéseket](how-to-configure-environment.md#azure-databricks) helyette.

>[!NOTE]
> Ezek az utasítások segítségével telepítheti és használhatja az SDK-t a helyi számítógépről. Jupyter használ egy távoli virtuális gépen, állítsa be egy távoli asztali vagy X terminál-munkamenetben.

Azt javasoljuk, hogy az SDK telepítése előtt először hozzon létre egy elkülönített Python-környezetet. Bár ez a cikk [Miniconda](https://docs.conda.io/en/latest/miniconda.html), is használhatja teljes [Anaconda](https://www.anaconda.com/) telepítve vagy [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

Ez a cikk utasításait a rövid útmutató és oktatóanyag jegyzetfüzetek futtatásához szükséges összes csomagokat telepíti.  Más mintafüzetek további összetevők lehet szükség.  Ezek az összetevők kapcsolatos további információkért lásd: [telepítse az Azure Machine Learning SDK Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>A Miniconda telepítése

[Töltse le és telepítse a Miniconda](https://docs.conda.io/en/latest/miniconda.html). Válassza ki a Python 3.7 verzió telepítéséhez. Ne válassza ki a Python 2.x verzióját.  

### <a name="create-an-isolated-python-environment"></a>Elkülönített Python-környezet létrehozása

1. Nyisson meg egy parancssori ablakot, majd hozzon létre egy új conda-környezetet nevű *myenv* és telepítik a Pythont 3.6.5. Az Azure Machine Learning-SDK-t fog dolgozhat a Python 3.5.2-es verzióját vagy újabb, de az automatizált machine learning-összetevők nem, teljes körűen működőképes a Python 3.7-es verzióját.  Összetevők és a csomagok letöltése közben a környezet létrehozása több percig tart.

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. Aktiválja a környezetet.

    ```shell
    conda activate myenv
    ```

1. Engedélyezze a környezetspecifikus ipython kernelekkel:

    ```shell
    conda install notebook ipykernel
    ```

    Ezután hozzon létre a kernel:

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>Az SDK telepítése

1. Aktivált conda a környezetben telepítse a Machine Learning SDK alapvető összetevői a Jupyter notebook képességeket. A telepítés befejezéséhez a számítógép konfigurációja alapján néhány percet vesz igénybe.

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Az Azure Machine Learning-oktatóanyagok használandó ebben a környezetben, az ilyen csomagok telepítéséhez.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Ebben a környezetben használja az Azure Machine Learning-oktatóanyagok, telepítse az automatizált machine learning-összetevőket.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> Az egyes parancssori eszközök előfordulhat, hogy hozzá kell idézőjelek közé a következő:
> *  'azureml-sdk[notebooks]'
> * "azureml-sdk [automl]"
>

### <a name='sdk-create'></a> Hozzon létre egy munkaterületet, az SDK-val

Hozza létre a munkaterületet egy Jupyter Notebookot, a Python SDK használatával.

1. Hozzon létre, és/vagy a rövid útmutató és oktatóanyagok használni kívánt CD-t a címtárhoz.

1. Jupyter Notebook indításához írja be ezt a parancsot:

    ```shell
    jupyter notebook
    ```

1. A böngészőablakban hozzon létre egy új notebookot az alapértelmezett `Python 3` kernellel. 

1. Szeretné megjeleníteni az SDK-verzió, adja meg, majd ezután futtassa a következő Python-kódot a notebook cella:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Keresse meg az értéket a `<azure-subscription-id>` paramétert a [előfizetési listát az Azure Portalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Bármelyik előfizetést használhatja, amelyben a szerepköre tulajdonos vagy közreműködő.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   Futtassa a kódot, amikor a program kérheti jelentkezzen be az Azure-fiókjával. A bejelentkezés után a rendszer helyben gyorsítótárazza a hitelesítési jogkivonatot.

1. A munkaterület megtekintéséhez részleteit, például a hozzájuk kapcsolódó tárterületért, tároló-beállításjegyzék és a key vault adja meg a következő kódot:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


### <a name="write-a-configuration-file"></a>Konfigurációs fájl írása

Mentse a munkaterület adatait egy konfigurációs fájlt az aktuális könyvtár. A fájl neve *aml_config/config.json*.  

A munkaterület konfigurációs fájl egyszerűen később betöltése ugyanazon a munkaterületen. Más notebookok és ugyanabban a címtárban, vagy a kóddal alkönyvtárban található parancsfájlok is betölthet `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

Ez `write_config()` API-hívás létrehoz a konfigurációs fájlt az aktuális könyvtárban található. A *config.json* fájl tartalmazza a következőket:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> A munkaterület a Python-szkriptek vagy más címtárakban lévő Jupyter notebookok használata, a fájl másolása a könyvtárhoz. A fájl ugyanabban a könyvtárban, nevű alkönyvtárban lehet *aml_config*, vagy egy szülő könyvtárban.

## <a name="resource-manager-template"></a>Resource manager-sablon

Munkaterület létrehozása egy sablonból: [egy Azure Machine Learning szolgáltatás munkaterület létrehozása sablon használatával](how-to-create-workspace-template.md)

## <a name="cli"></a>parancssori felület

Egy munkaterület létrehozása a CLI-vel: [a CLI-bővítmény használata az Azure Machine Learning szolgáltatás](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

* Függetlenül attól, hogy hogyan hozták létre, megtekintheti a munkaterületéhez a [az Azure portal](https://portal.azure.com/).  Lásd: [munkaterület megjelenítése](how-to-manage-workspace.md#view) részleteiről.

* Próbálja ki a munkaterület az alábbi útmutatókkal és oktatóanyagokkal.

    * Gyors útmutató: [Jupyter notebook futtatása a felhőben](quickstart-run-cloud-notebook.md).
    * Gyors útmutató: [A saját kiszolgálón futtassa a Jupyter notebook](quickstart-run-local-notebook.md).
    * Kétrészes oktatóanyag: [Train](tutorial-train-models-with-aml.md) és [üzembe helyezése](tutorial-deploy-models-with-aml.md) egy kép besorolási mód.
    * Kétrészes oktatóanyag: [Adatok előkészítése](tutorial-data-prep.md) és [automatizált machine learning segítségével](tutorial-auto-train-models.md) regressziós modell létrehozásához.

* Ismerje meg, hogyan [a fejlesztési környezet konfigurálása](how-to-configure-environment.md).

* Tudjon meg többet a [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk).
