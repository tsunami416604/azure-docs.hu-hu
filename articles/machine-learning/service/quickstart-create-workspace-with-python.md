---
title: 'Rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatás a Pythonban'
description: Ismerkedés az Azure Machine Learning szolgáltatás a Pythonban. A Python SDK használatával hozzon létre egy munkaterületet, amely az alapvető blokk, amellyel kísérletezhet, betanítását és gépi tanulási modellek üzembe helyezése a felhőben.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec12
ms.openlocfilehash: 1a6bb648de251d8764eec52491a918e7cb1de653
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015381"
---
# <a name="quickstart-use-python-sdk-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: Használata Python SDK-t az Azure Machine Learning használatának első lépései

Ebben a rövid útmutatóban Machine Learning-[munkaterületet](concept-azure-machine-learning-architecture.md) fog létrehozni és használni a Pythonhoz készült Azure Machine Learning SDK segítségével. A Machine Learning használata során ez a munkaterület szolgál a gépi tanulási modellekkel való kísérletezés, valamint a betanításuk és üzembe helyezésük alapjául a felhőben. Ebben a rövid útmutatóban először saját Python-környezetet és Jupyter-notebookkiszolgálót konfigurál. Ha telepítés nélkül szeretné futtatni, tekintse meg a következőt: [Rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatással az Azure Portalon](quickstart-get-started.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Ebben az oktatóanyagban a Python SDK telepítése mellett az alábbiakat fogja végrehajtani:

* Munkaterület létrehozása az Azure-előfizetésben.
* Konfigurációs fájlt hoz létre az adott munkaterülethez a más notebookokban és szkriptekben történő későbbi használathoz.
* Kódot ír a munkaterületen belüli értékek naplózásához.
* A naplózott értékek megtekintése a munkaterületen.

Ebben a rövid útmutatóban létre fog hozni egy munkaterületet és egy konfigurációs fájlt. Ezek előfeltételként is használhatók a Machine Learning más szolgáltatási oktatóanyagaiban vagy útmutatóiban. Csakúgy, mint más Azure-szolgáltatásokra, a Machine Learningre is vonatkoznak bizonyos korlátok és kvóták. [Ismerje meg a kvótákat és azt, hogyan kérhet belőlük többet.](how-to-manage-quotas.md)

A következő Azure-erőforrásokat a rendszer automatikusan hozzáadja a munkaterületéhez, ha az Ön régiójában rendelkezésre állnak:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!NOTE]
> Ebben a cikkben kód szükséges Azure Machine Learning SDK verziója 1.0.2-es vagy újabb. 


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLfree) a virtuális gép létrehozásának megkezdése előtt.


## <a name="install-the-sdk"></a>Az SDK telepítése

*Ha 2018. szeptember 27. után létrehozott Data Science Virtual Machine-t használ, hagyja ki ezt a szakaszt.* Ezeken a Data Science Virtual Machine-példányokon már előre telepítve van a Python SDK.

Azt javasoljuk, hogy az SDK telepítése előtt először hozzon létre egy elkülönített Python-környezetet. Bár ez a rövid útmutató a [Minicondát](https://conda.io/docs/user-guide/install/index.html) használja, a teljes telepített [Anacondát](https://www.anaconda.com/) vagy a [Python virtualenv](https://virtualenv.pypa.io/en/stable/)-t is használhatja.

### <a name="install-miniconda"></a>A Miniconda telepítése


[Töltse le](https://conda.io/miniconda.html) és telepítse a Minicondát. Válassza ki a Python 3.7-es vagy újabb verzióját. Ne válassza ki a Python 2.x verzióját.

### <a name="create-an-isolated-python-environment"></a>Elkülönített Python-környezet létrehozása 

Nyisson meg egy parancssori ablakot. Ezután hozzon létre egy új Conda-környezetet `myenv` néven, a Python 3.6-os verziójával.

```shell
conda create -n myenv -y Python=3.6
```

Aktiválja a környezetet.

  ```shell
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Az SDK telepítése

Az aktivált conda-környezetben telepítse az SDK-t. Ez a kód telepíti a Machine Learning SDK alapvető összetevőit. A Jupyter Notebook server a conda-környezetben is telepíti. A telepítés befejezéséhez, a gép konfigurációjától függően néhány percet vesz igénybe.

```sh
# install Jupyter
conda install nb_conda

# install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]

```

Különböző "plusz" kulcsszó használatával további összetevők az SDK telepítése.

```sh
# install the base SDK and auto ml components
pip install azureml-sdk[automl]

# install the base SDK and model explainability component
pip install azureml-sdk[explain]

# install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

A telepítés inkább egy Databricks-környezetben.

```
# install the base SDK and automl components in Azure Databricks environment
# read more at: https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks
pip install azureml-sdk[databricks]
```


## <a name="create-a-workspace"></a>Munkaterület létrehozása

A Jupyter-notebook indításához írja be ezt a parancsot.
```shell
jupyter notebook
```

A böngészőablakban hozzon létre egy új notebookot az alapértelmezett `Python 3` kernellel. 

Az SDK-verzió megjelenítéséhez írja be a következő Python-kódot egy notebookcellába, és hajtsa végre a kódot.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

Hozzon létre egy új Azure-erőforráscsoportot és egy új munkaterületet.

Keresse meg az `<azure-subscription-id>` értékét [a Microsoft Azure Portalon lévő előfizetési listában](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Bármelyik előfizetést használhatja, amelyben a szerepköre tulajdonos vagy közreműködő.

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',    
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region  
                     )
```

Előfordulhat, hogy a fenti kód végrehajtása megnyit egy új böngészőablakot az Azure-fiókjával való bejelentkezéshez. A bejelentkezés után a rendszer helyben gyorsítótárazza a hitelesítési jogkivonatot.

A munkaterület részleteinek (például társított tárolás, tárolóregisztrációs adatbázis és Key Vault-tároló) megtekintéséhez adja meg a következő kódot.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Konfigurációs fájl írása

A munkaterület adatait mentse el egy konfigurációs fájlban az aktuális könyvtárba. A fájl megnevezése: 'aml_config\config.json'.  

Ezzel a munkaterület-konfigurációs fájl megkönnyíti a munkaterület újbóli betöltését, ha később is szüksége lesz rá. A munkaterületet az ugyanabban a könyvtárban vagy alkönyvtárban lévő egyéb notebookokkal és szkriptekkel együtt is betöltheti. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]


A `write_config()` API-hívás létrehozza a konfigurációs fájlt az aktuális könyvtárban. A `config.json` fájl a következő szkriptet tartalmazza.

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
A futtatás végeztével áttekintheti a próbafuttatást az Azure Portalon. A következő kód használatával nyomtassa ki a legutóbbi futtatás eredményeinek URL-címét.

```python
print(run.get_portal_url())
```

A hivatkozás segítségével a böngészőjében megtekintheti a naplózott értékeket a Microsoft Azure Portalon.

![Naplózott értékek a portálon](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
>[!IMPORTANT]
>A létrehozott erőforrások előfeltételként is használhatók más Machine Learning-oktatóanyagokban vagy -útmutatókban.

Ha nem tervezi használni az itt létrehozott erőforrásokat, törölje őket, nehogy többletköltségekkel kelljen számolnia.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]


## <a name="next-steps"></a>További lépések

Létrehozta a modellekkel való kísérletezéshez és azok üzembe helyezéséhez szükséges erőforrásokat. Kódot is futtatott egy notebookban, és áttekintette a kód futtatási előzményeit a felhőben lévő munkaterületen.

A Machine Learning-oktatóanyagokkal való használathoz szüksége lesz még néhány további csomagra a környezetben.

1. Zárja be a jegyzetfüzetet a böngészőben.
1. A parancssori ablakban használja a `Ctrl`+`C` billentyűkombinációt a jegyzetfüzet-kiszolgáló leállításához.
1. Telepítse a további csomagokat.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```


Miután telepítette ezeket a csomagokat, kövesse a modellek betanításával és üzembe helyezésével kapcsolatos oktatóanyagokat. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Képbesorolási modell betanítása](tutorial-train-models-with-aml.md)

A [GitHubon lévő összetettebb példákkal](https://aka.ms/aml-notebooks) is megismerkedhet.
