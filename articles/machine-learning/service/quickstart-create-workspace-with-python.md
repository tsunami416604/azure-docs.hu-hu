---
title: 'Gyors útmutató: Ismerkedés a Pythonban'
titleSuffix: Azure Machine Learning service
description: Ismerkedés az Azure Machine Learning szolgáltatás a Pythonban. A Python SDK használatával hozzon létre egy munkaterületet, amely az alapvető blokk, amellyel kísérletezhet, betanítását és gépi tanulási modellek üzembe helyezése a felhőben.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 2c8edd73a287d5bca2f3deb68448ba951b7a3367
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106562"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: Az Azure Machine Learning használatának első lépései a Python SDK használatával

Ez a cikk létrehozása, majd az Azure Machine Learning szolgáltatás használhatja az Azure Machine Learning SDK Pythonhoz készült [munkaterület](concept-azure-machine-learning-architecture.md). A munkaterület az eligazodást blokk, amellyel kísérletezhet, betanítását és a Machine Learning gépi tanulási modellek üzembe helyezése a felhőben. 

Saját Python-környezetet és a Jupyter Notebook Server konfigurálásával megkezdése. Futtassa a telepítés nélkül, tekintse meg [a rövid útmutató: Az Azure Machine Learning használatának első lépései az Azure portal használatával](quickstart-get-started.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Ebben a cikkben:
* Telepítse a Python SDK-t.
* Munkaterület létrehozása az Azure-előfizetésben.
* Konfigurációs fájlt hoz létre az adott munkaterülethez a más notebookokban és szkriptekben történő későbbi használathoz.
* Kódot ír a munkaterületen belüli értékek naplózásához.
* A naplózott értékek megtekintése a munkaterületen.

Létrehozhat egy munkaterületet, és a egy konfigurációs fájlt használja, mint más Machine Learning szolgáltatássaé kapcsolatos oktatóanyagok és útmutatók előfeltételek. Csakúgy, mint más Azure-szolgáltatások, bizonyos korlátok és kvóták társítva a Machine Learning. [Ismerje meg a kvótákat és azt, hogyan kérhet belőlük többet.](how-to-manage-quotas.md)

A következő Azure-erőforrásokat a rendszer automatikusan hozzáadja a munkaterületéhez, ha az Ön régiójában rendelkezésre állnak:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

## <a name="install-the-sdk"></a>Az SDK telepítése

> [!IMPORTANT]
> Ha 2018. szeptember 27. után létrehozott data science virtuális gép használja, kihagyhatja ezt a szakaszt.
> Adatelemző virtuális gépek után ez a dátum biztosítja az előre telepített Python SDK-val létrehozott.

Ebben a cikkben a kódot igényel az Azure Machine Learning SDK verziója 1.0.2-es vagy újabb.

Azt javasoljuk, hogy az SDK telepítése előtt először hozzon létre egy elkülönített Python-környezetet. Bár ez a cikk [Miniconda](https://conda.io/docs/user-guide/install/index.html), is használhatja teljes [Anaconda](https://www.anaconda.com/) telepítve vagy [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>A Miniconda telepítése

[Töltse le és telepítse a Miniconda](https://conda.io/miniconda.html). Válassza ki a Python 3.7-es vagy újabb. Ne válassza ki a Python 2.x.

### <a name="create-an-isolated-python-environment"></a>Elkülönített Python-környezet létrehozása 

1. Nyisson meg egy parancssori ablakot, és hozzon létre egy új nevű conda-környezet *myenv* a Python 3.6-ot.

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. Aktiválja a környezetet.

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>Az SDK telepítése

Az aktivált conda-környezetben telepítse az SDK-t. Ez a kód telepíti a Machine Learning SDK alapvető összetevőit. A Jupyter Notebook server a conda-környezetben is telepíti. A telepítés befejezéséhez a számítógép konfigurációja alapján néhány percet vesz igénybe.

```shell
# Install Jupyter
conda install nb_conda

# Install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]
```

További kulcsszavak segítségével más összetevők az SDK telepítése:

```shell
# Install the base SDK and auto ml components
pip install azureml-sdk[automl]

# Install the base SDK and the model explainability component
pip install azureml-sdk[explain]

# Install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

Az Azure Databricks a környezetben, használja a [Databricks telepítési lépéseket](how-to-configure-environment.md#azure-databricks
) helyette.


## <a name="create-a-workspace"></a>Munkaterület létrehozása

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
                         location='eastus2' # Or other supported Azure region   
                        )
   ```

   Futtassa a kódot, amikor a program kérheti jelentkezzen be az Azure-fiókjával. A bejelentkezés után a rendszer helyben gyorsítótárazza a hitelesítési jogkivonatot.

1. A munkaterület megtekintéséhez részleteit, például a hozzájuk kapcsolódó tárterületért, tároló-beállításjegyzék és a key vault adja meg a következő kódot:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Konfigurációs fájl írása

Mentse a munkaterület adatait egy konfigurációs fájlt az aktuális könyvtár. A fájl neve *aml_config\config.json*.  

A `write_config()` API-hívás létrehozza a konfigurációs fájlt az aktuális könyvtárban. A *config.json* fájl tartalmazza a következőket:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

A munkaterület konfigurációs fájl egyszerűen később betöltése ugyanazon a munkaterületen. A munkaterületet az ugyanabban a könyvtárban vagy alkönyvtárban lévő egyéb notebookokkal és szkriptekkel együtt is betöltheti. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]



## <a name="use-the-workspace"></a>A munkaterület használata

Írjon olyan kódot, amely az alapszintű SDK API-kat használja a kísérleti futtatások nyomon követéséhez.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>A naplózott eredmények megtekintése
A futtatás végeztével áttekintheti a próbafuttatást az Azure Portalon. Egy URL-címet, amely az eredményeket a legutóbbi futtatás navigál, használja a következő kódot:

```python
print(run.get_portal_url())
```

A hivatkozás segítségével a böngészőjében megtekintheti a naplózott értékeket a Microsoft Azure Portalon.

![Az Azure Portalon naplózott értékek](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
>[!IMPORTANT]
>Használhatja a többi Machine Learning-oktatóanyag előfeltételei itt és útmutató létrehozott erőforrások cikkeket.

Ha nem szeretné használni az ebben a cikkben létrehozott erőforrásokat, törölje azokat az díjak elkerüléséhez.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott kísérletezhet, és a modellek üzembe helyezése a szükséges erőforrásokat. Jegyzetfüzet egy kódot futtatta, és a kód futtatási előzményeinek megismerte a munkaterületen, a felhőben.

A kód használata a Machine Learning szolgáltatássaé kapcsolatos oktatóanyagok, néhány további csomagokat a környezetben kell.

1. Zárja be a jegyzetfüzetet a böngészőben.
1. A parancssori ablakban válassza ki a Ctrl + C billentyűkombinációval, a Jupyter Notebook server leállítása.
1. A további csomagokat telepíteni.  Ha nem telepít `azureml-sdk[automl]` fent, győződjön meg arról, hogy tegye meg most.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

Miután telepítette ezeket a csomagokat, folytassa az oktatóanyagok, betanítását és a modell üzembe helyezése. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy rendszerkép osztályozási modell betanítása](tutorial-train-models-with-aml.md)

Emellett megismerheti a [GitHubon lévő speciális példákat](https://aka.ms/aml-notebooks) is.
