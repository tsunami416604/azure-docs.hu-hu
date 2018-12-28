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
ms.openlocfilehash: fd86e3a65b542bad0f3114a32362041c34fb74d8
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791234"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: Az Azure Machine Learning használatának első lépései a Python SDK használatával

Ezt a gyorsútmutatót követve hozzon létre, majd az Azure Machine Learning szolgáltatás használhatja az Azure Machine Learning SDK Pythonhoz készült [munkaterület](concept-azure-machine-learning-architecture.md). A Machine Learning használata során ez a munkaterület szolgál a gépi tanulási modellekkel való kísérletezés, valamint a betanításuk és üzembe helyezésük alapjául a felhőben. Indítsa el a saját Python-környezetet és a Jupyter Notebook server konfigurálásával. Telepítés nélkül futtatja, lásd: [a rövid útmutató: Az Azure Machine Learning használatának első lépései az Azure portal használatával](quickstart-get-started.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Ebben az oktatóanyagban a Python SDK telepítése, és ezeket a feladatokat:

* Munkaterület létrehozása az Azure-előfizetésben.
* Konfigurációs fájlt hoz létre az adott munkaterülethez a más notebookokban és szkriptekben történő későbbi használathoz.
* Kódot ír a munkaterületen belüli értékek naplózásához.
* A naplózott értékek megtekintése a munkaterületen.

Ebben a rövid útmutatóban létre fog hozni egy munkaterületet és egy konfigurációs fájlt. Egyéb Machine Learning szolgáltatássaé kapcsolatos oktatóanyagok és útmutatók előfeltételei is használhatja ezeket az erőforrásokat. Csakúgy, mint más Azure-szolgáltatások nincsenek korlátok és kvóták, Machine Learning társított. [Ismerje meg a kvóták és arról, hogyan kérhet további](how-to-manage-quotas.md).

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

Azt javasoljuk, hogy az SDK telepítése előtt először hozzon létre egy elkülönített Python-környezetet. Bár ez a rövid útmutató a [Minicondát](https://conda.io/docs/user-guide/install/index.html) használja, a teljes telepített [Anacondát](https://www.anaconda.com/) vagy a [Python virtualenv](https://virtualenv.pypa.io/en/stable/)-t is használhatja.

### <a name="install-miniconda"></a>A Miniconda telepítése


[Töltse le](https://conda.io/miniconda.html) és telepítse a Minicondát. Válassza ki a Python 3.7-es vagy újabb verzióját. Ne válassza ki a Python 2.x verzióját.

### <a name="create-an-isolated-python-environment"></a>Elkülönített Python-környezet létrehozása 

Nyisson meg egy parancssori ablakot. Ezután hozzon létre egy új conda-környezetet, nevű **myenv** a Python 3.6-ot.

```shell
conda create -n myenv -y Python=3.6
```

A környezet aktiválása:

```shell
conda activate myenv
```

### <a name="install-the-sdk"></a>Az SDK telepítése

Az aktivált conda-környezetben telepítse az SDK-t. Ez a kód telepíti a Machine Learning SDK alapvető összetevőit. A Jupyter Notebook server a conda-környezetben is telepíti. A telepítés befejezéséhez a számítógép konfigurációja alapján néhány percet vesz igénybe.

```sh
# Install Jupyter
conda install nb_conda

# Install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]
```

További kulcsszavak segítségével más összetevők az SDK telepítése.

```sh
# Install the base SDK and auto ml components
pip install azureml-sdk[automl]

# Install the base SDK and the model explainability component
pip install azureml-sdk[explain]

# Install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

Az Azure Databricks a környezetben használja az alábbi telepítési parancsot:

```
# Install the base SDK and automl components in the Azure Databricks environment.
# For more information, see https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks.
pip install azureml-sdk[databricks]
```


## <a name="create-a-workspace"></a>Munkaterület létrehozása

Jupyter Notebook indításához írja be ezt a parancsot:

```shell
jupyter notebook
```

A böngészőablakot, hozzon létre egy új jegyzetfüzetet az alapértelmezett **Python 3** kernel. 

Az SDK-verzió megjelenítéséhez írja be a következő Python-kódot egy notebookcellába, és hajtsa végre a kódot.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

Hozzon létre egy új Azure-erőforráscsoportot és egy új munkaterületet.

Keresse meg az értéket a `<azure-subscription-id>` paramétert a [előfizetési listát az Azure Portalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Bármelyik előfizetést használhatja, amelyben a szerepköre tulajdonos vagy közreműködő.

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

Részleteket, például a hozzájuk kapcsolódó tárterületért, tároló-beállításjegyzék és a key vault, tekintse meg a munkaterület adja meg a következő kódot:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Konfigurációs fájl írása

Mentse a munkaterület adatait a konfigurációs fájlt az aktuális könyvtárban található. A fájl neve **aml_config\config.json**.  

A munkaterület konfigurációs fájl megkönnyíti a későbbi betölteni az ugyanazon a munkaterületen. A munkaterület más jegyzetfüzetek és a parancsfájlokat az ugyanabban a könyvtárban vagy egy alkönyvtár betöltheti. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]


A `write_config()` API-hívás létrehozza a konfigurációs fájlt az aktuális könyvtárban. A config.json fájl tartalmazza a következő parancsfájlt:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>A munkaterület használata

Írjon olyan kódot, amely az alapszintű SDK API-kat használja a kísérleti futtatások nyomon követéséhez.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]


## <a name="view-logged-results"></a>A naplózott eredmények megtekintése
A futtatás végeztével áttekintheti a próbafuttatást az Azure Portalon. A következő kód használatával egy URL-címet, amely az eredményeket a legutóbbi futtatás navigál nyomtatni:

```python
print(run.get_portal_url())
```

A hivatkozás segítségével a böngészőjében megtekintheti a naplózott értékeket a Microsoft Azure Portalon.

![Az Azure Portalon naplózott értékek](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

> [!IMPORTANT]
> A létrehozott erőforrások más Machine Learning szolgáltatássaé kapcsolatos oktatóanyagok és útmutatók előfeltételei használható.

Ha nem szeretné, az ebben a rövid útmutatóban létrehozott erőforrásokat használja, törölheti őket, így nem kell díjat fizetniük.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott erőforrások kísérletezhet, és helyezhet üzembe modelleket. Kód futtatott egy jegyzetfüzetet, és megvizsgálta a kód futtatási előzményeinek a munkaterületen, a felhőben.

A kód használata a Machine Learning szolgáltatássaé kapcsolatos oktatóanyagok, néhány további csomagokat a környezetben kell.

1. Zárja be a jegyzetfüzetet a böngészőben.
1. A parancssori ablakban adja meg a Ctrl + C billentyűkombinációval, a Jupyter Notebook server leállítása.
1. A további csomagokat telepíteni:

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```


A csomagok telepítése után folytassa az oktatóanyagok, betanítását és a modell üzembe helyezése. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy rendszerkép osztályozási modell betanítása](tutorial-train-models-with-aml.md)

A [GitHubon lévő összetettebb példákkal](https://aka.ms/aml-notebooks) is megismerkedhet.
