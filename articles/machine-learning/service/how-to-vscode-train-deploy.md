---
title: Be & VS Code-ból a modellek üzembe helyezése
titleSuffix: Azure Machine Learning service
description: További információ az Azure Machine Learning Visual Studio Code és a képzés és üzembe helyezése gépi tanulási és deep learning-modellek a Visual Studio Code az Azure Machine Learning szolgáltatás elindítása.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e7df9086fa5ffc6273a6cb063bdee3cfdfa73e34
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013315"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>Betanítása és gépi tanulási modellek üzembe helyezése a Visual Studio Code használatával

Ebben a cikkben, megtudhatja, hogyan használható a **az Azure Machine Learning a Visual Studio Code** bővítmény betanítását és üzembe helyezése a gépi tanulási és deep learning-modellek a Visual Studio Code (a VS Code) az Azure Machine Learning szolgáltatással.

Az Azure Machine Learning kísérleteket fut a helyi és távoli számítási célnak támogatást nyújt. Minden egyes kísérlethez azt is nyomon követheti, több Futtatás gyakran különböző technikák, hiperparaméterek és egyéb iteratív próbálja szükség szerint. Az Azure Machine Learning segítségével nyomon követheti az egyéni metrikákat, és kísérletezzen a futtatásakor a data science megismételhetősége és auditability engedélyezése.

Az intranetes és ezek a modellek tesztelési és éles igényeinek.

## <a name="prerequisites"></a>Előfeltételek

+ Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

+ Rendelkezik a [Azure Machine Learning a VS Code](how-to-vscode-tools.md) bővítmény beállítása.

+ Rendelkezik a [Azure Machine Learning SDK telepítve van a Pythonhoz készült](how-to-vscode-tools.md) VS Code használatával.

## <a name="create-and-manage-compute-targets"></a>Létrehozni és kezelni a számítási célnak

Az Azure Machine Learning a VS Code készítse elő adatait, modelleket taníthat be és telepíthet, helyi vagy távoli számítási célnak a.

Ez a bővítmény az Azure Machine Learning számos különböző távoli számítási célnak támogatja. Tekintse meg a [teljes listája megtalálható a támogatott számítási célnak](how-to-set-up-training-targets.md) az Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Számítási célnak létrehozása az Azure Machine Learning a VS Code-ban

**Egy számítási célnak létrehozása:**

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure Machine Learning oldalsáv jelenik meg.

2. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. Az animált képen az előfizetés neve "Ingyenes próbaverzió" pedig a munkaterület "TeamWorkspace". 

3. A munkaterület csomópont alatt, kattintson a jobb gombbal a **számítási** csomópont, majd **létrehozása számítási**.

4. Válassza ki a számítási célt a listából. 

5. A parancskatalógus válassza ki a virtuális gép méretét.

6. A parancskatalógus adja meg a számítási célnak a mező nevét. 

7. Adja meg a speciális tulajdonságokat a JSON konfigurációs fájl, amely egy új lapon nyílik meg. Megadhatja a tulajdonságokat, például a csomópontok maximális számát...

8. Ha elkészült a számítási célnak konfigurálásával, kattintson a **küldés** a képernyő jobb alsó sarkában.

Íme egy példa egy Azure Machine Learning Compute (AMLCompute) létrehozásához: [![A VS Code AML számítási létrehozása](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>A "futtatási konfiguráció" fájl

A VS Code-bővítmény automatikusan hozzon létre egy helyi számítási célnak, és futtassa a konfiguráció a **helyi** és **docker** környezetekben a helyi számítógépen. A futtatási konfigurációs fájlokat a kapcsolódó számítási célnak területen találhatók. 

Ez az egy kódrészletet az alapértelmezett helyi futtatási konfigurációs fájlból. Alapértelmezés szerint `userManagedDependencies: True` , telepítenie kell a szalagtárak és függőségek összes saját maga és majd helyi Kísérletezési futtatások fogja használni az alapértelmezett Python-környezetet a VS Code Python bővítmény által megadott.

```yaml
# user_managed_dependencies = True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: True
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

## <a name="train-and-tune-models"></a>Modelleket taníthat és hangolás

A VS Code (előzetes verzió) az Azure Machine Learning segítségével rövid idő alatt a kód ismételt futtatásával, végighaladhat és hibakeresés és a forrás kód control megoldást választott használni. 

**A kísérlet futtatása helyileg az Azure Machine Learning:**

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. 

1. Munkaterület csomópontja alatt bontsa ki a **számítási** csomópontot, és kattintson a jobb gombbal a a **konfiguráció futtatása** számítási is használni szeretné. 

1. Válassza ki **kísérlet futtatásához**.

1. Válassza ki a parancsfájl futtatásához a Fájlkezelőben. 

1. Kattintson a **nézet kísérlet futtatása** betanított modelljeit a futtatások monitorozása és az integrált Azure Machine Learning portal megtekintéséhez.

Íme egy példa a helyileg futó kísérlet: [![Kísérlet futtatása helyileg](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Távoli számítási erőforrások kísérletek a VS Code-ban

Egy távoli számítási célnak betanításakor használatához szüksége futtatási konfigurációs fájl létrehozása. A fájl arra utasítja az Azure Machine Learning nem csupán hol futtathatja a kísérletet, de is hogyan készítse elő a környezetét.

#### <a name="the-conda-dependencies-file"></a>A conda-függőségeket fájl

Alapértelmezés szerint egy új conda-környezet létrehozása az Ön számára, és a telepítési függőségek. Azonban meg kell adnia a függőségeket a `aml_config/conda_dependencies.yml` fájlt.

Ez az egy kódrészletet az alapértelmezett aml_config/conda_dependencies.yml a.
Hozzáadhat további függőségeket, a konfigurációs fájlban.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**A kísérlet Azure Machine Learning egy távoli futtatásához számítási célt:**

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. 

1. A python-szkript-szerkesztő ablakban kattintson a jobb gombbal, és válassza ki **AML: Kísérlet az Azure-beli futtató**. 

1. A parancskatalógus válassza ki a számítási célnak. 

1. A parancskatalógus adja meg a futtatási konfiguráció nevét a mezőbe. 

1. Adja meg a futtatókörnyezeti függőségek a kísérletet, majd kattintson a conda_dependencies.yml fájl szerkesztésével **küldés** a képernyő jobb alsó sarkában. 

1. Kattintson a **nézet kísérlet futtatása** betanított modelljeit a futtatások monitorozása és az integrált Azure Machine Learning portal megtekintéséhez.

Íme egy példa, egy kísérlet futtatása távoli számítási célt: [![Kísérlet futtatása egy távoli cél](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Üzembe helyezés és kezelés a modellek
Az Azure Machine Learning lehetővé teszi, hogy üzembe helyezése és kezelése a gépi tanulási modellek a felhőben és a peremhálózaton. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Regisztrálja a modellt az Azure Machine Learning, VS Code-ból

Most, hogy a modell van tanítva, regisztrálhatja a munkaterületén.
A regisztrált modellek nyomon követi és telepíteni kell.

**Regisztrálja a modellt:**

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén.

1. A munkaterület csomópont alatt, kattintson a jobb gombbal **modellek** válassza **regisztrálása modell**.

1. A parancskatalógus adja meg a mezőben egy modell neve. 

1. A listából válassza ki, hogy a feltöltendő egy **modellfájl** (az egyetlen modellek) egy **modell mappa** (a modelljeit több fájlt, például a tensorflow-hoz). 

1. Válassza ki a mappát vagy fájlt.

1. Ha elkészült a modell tulajdonságok konfigurálásával, kattintson a **küldés** a képernyő jobb alsó sarkában. 

Íme egy példa a modell AML regisztrálásához: [![Regisztrálás az AML-modell](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>A VS Code-ból a szolgáltatás üzembe helyezése

VS Code használatát, a web service-t is telepítheti:
+ Az Azure Container Instance (aci Szolgáltatásban): teszteléshez
+ Az Azure Kubernetes Service (AKS): az éles környezetben 

Nem kell előre teszteléséhez, mivel azok jönnek létre menet közben egy ACI-tároló létrehozása. Azonban az AKS-fürtök előre konfigurálni kell. 

Tudjon meg többet [üzembe helyezés az Azure Machine Learning](how-to-deploy-and-where.md) általában.

**A webszolgáltatás üzembe helyezéséhez:**

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén.

1. A munkaterület csomópontja alatt bontsa ki a **modellek** csomópont.

1. Kattintson a jobb gombbal a modell üzembe helyezése, és válassza a kívánt **szolgáltatás telepítése regisztrált modellből** parancsot a helyi menüből.

1. A parancskatalógus válassza ki a számítási célnak, melyben szeretné üzembe helyezni a listából. 

1. A parancskatalógus adja meg ezt a szolgáltatást, a mező nevét.  

1. A parancskatalógus nyomja le az Enter billentyűt a billentyűzeten keresse meg és válassza ki a parancsfájlt.

1. A parancskatalógus nyomja le az Enter billentyűt a billentyűzeten keresse meg és válassza ki a conda-függőség fájlt.

1. Ha elkészült a szolgáltatástulajdonságok konfigurálását, kattintson a **küldés** üzembe helyezéséhez a képernyő jobb alsó sarkában található. A szolgáltatás tulajdonságok fájlban megadhat egy helyi Docker-fájlt vagy egy schema.json fájlt, előfordulhat, hogy szeretne használni.

A web service most már telepítve van.

Íme egy példa egy webszolgáltatás üzembe helyezéséhez: [![A web Service szolgáltatásának telepítése](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

## <a name="next-steps"></a>További lépések

A VS Code-en kívül a Machine Learning betanítási útmutatót szeretne, olvassa el a [oktatóanyag: Az Azure Machine Learning-modellek betanításához](tutorial-train-models-with-aml.md).

A szerkesztési lépésenkénti útmutatóért fut, és helyileg, a kód hibakeresése lásd: a [Python Hello World oktatóanyag](https://code.visualstudio.com/docs/python/python-tutorial)
