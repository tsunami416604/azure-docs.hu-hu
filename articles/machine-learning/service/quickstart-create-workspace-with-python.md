---
title: 'Gyors útmutató: Ismerkedés a Pythonban'
titleSuffix: Azure Machine Learning service
description: Ismerkedés az Azure Machine Learning szolgáltatás a Pythonban. A Python SDK használatával hozzon létre egy munkaterületet, amely az alapvető blokk, amellyel kísérletezhet, betanítását és gépi tanulási modellek üzembe helyezése a felhőben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: sdgilley
ms.author: sgilley
ms.date: 01/22/2019
ms.custom: seodec18
ms.openlocfilehash: fcf919a4120348ba8101280d960470ce46e5bdb0
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294246"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: Az Azure Machine Learning használatának első lépései a Python SDK használatával

Ez a cikk létrehozása, majd az Azure Machine Learning szolgáltatás használhatja az Azure Machine Learning SDK for Python 3 [munkaterület](concept-azure-machine-learning-architecture.md). A munkaterület az eligazodást blokk, amellyel kísérletezhet, betanítását és a Machine Learning gépi tanulási modellek üzembe helyezése a felhőben.

Saját Python-környezetet és a Jupyter Notebook Server konfigurálásával megkezdése. Futtassa a telepítés nélkül, tekintse meg [a rövid útmutató: Az Azure Machine Learning használatának első lépései az Azure portal használatával](quickstart-get-started.md).

Ebben a rövid videó verziójának megtekintése:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Ebben a rövid útmutatóban a következőket hajtja végre:

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

>[!NOTE]
> Ebben a cikkben kód szükséges Azure Machine Learning SDK verziója 1.0.2-es vagy újabb verzió 1.0.8 teszteltük és.


Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="install-the-sdk"></a>Az SDK telepítése

> [!IMPORTANT]
> Ha egy Azure adatelemzési virtuális gép vagy az Azure Databricks, kihagyhatja ezt a szakaszt.
> * Az Azure Data Science Virtual Machines 2018. szeptember 27. után létrehozott származnak az előre telepített Python SDK-val.
> * Az Azure Databricks a környezetben, használja a [Databricks telepítési lépéseket](how-to-configure-environment.md#azure-databricks) helyette.

>[!NOTE]
> Ezek az utasítások segítségével telepítheti és SDK-t a Jupyter Notebook használata a helyi számítógépen. Jupyter használata a távoli virtuális gépen, állítsa be egy távoli asztali vagy X terminál-munkamenetben.

Azt javasoljuk, hogy az SDK telepítése előtt először hozzon létre egy elkülönített Python-környezetet. Bár ez a cikk [Miniconda](https://docs.conda.io/en/latest/miniconda.html), is használhatja teljes [Anaconda](https://www.anaconda.com/) telepítve vagy [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

Ebben a rövid útmutatóban található utasításokat a rövid útmutató és oktatóanyag jegyzetfüzetek futtatásához szükséges összes csomagokat telepíti.  Más mintafüzetek további összetevők lehet szükség.  Ezek az összetevők kapcsolatos további információkért lásd: [telepítse az Azure Machine Learning SDK Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/install).

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


## <a name="create-a-workspace"></a>Munkaterület létrehozása

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


## <a name="write-a-configuration-file"></a>Konfigurációs fájl írása

Mentse a munkaterület adatait egy konfigurációs fájlt az aktuális könyvtár. A fájl neve *aml_config\config.json*.

A munkaterület konfigurációs fájl egyszerűen később betöltése ugyanazon a munkaterületen. A munkaterületet az ugyanabban a könyvtárban vagy alkönyvtárban lévő egyéb notebookokkal és szkriptekkel együtt is betöltheti.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

Ez `write_config()` API-hívás létrehoz a konfigurációs fájlt az aktuális könyvtárban található. A *config.json* fájl tartalmazza a következőket:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>A munkaterület használata

Futtassa egy kódrészletet, amely az alapszintű SDK API-k segítségével nyomon követheti a Kísérletezési futtatások:

1. Egy kísérlet létrehozása a munkaterületen.
1. Egyetlen érték jelentkezzen be a kísérletet.
1. Értékek listáját jelentkezzen be a kísérletet.

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

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy rendszerkép osztályozási modell betanítása](tutorial-train-models-with-aml.md)

Is feltárhatja [speciális példák a Githubon](https://aka.ms/aml-notebooks) vagy megtekintheti a [SDK felhasználói útmutató](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
