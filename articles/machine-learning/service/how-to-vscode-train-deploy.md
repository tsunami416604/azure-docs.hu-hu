---
title: Az Azure Machine Learning AI-bővítmény a Visual Studio Code Tools használatával
description: További információ a mesterséges Intelligencia és indítsa el a képzés és gépi tanulási és deep learning-modellek Azure Machine Learning szolgáltatás a VS Code telepítése a Visual Studio Code Tools.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 3a5ee8795dabcbf9e35e16a8ba0f9c0eb5490ead
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242976"
---
# <a name="vs-code-tools-for-ai-train-and-deploy-ml-models-from-vs-code"></a>A VS Code Tools for AI: Train és gépi Tanulási modellek a VS Code telepítése
Ebben a cikkben, megtudhatja, hogyan használható a **VS Code Tools for AI** betanítása és üzembe helyezése a gépi tanulási és deep learning-modellek Azure Machine Learning szolgáltatás a VS Code-bővítmény.

Az Azure Machine Learning kísérleteket fut a helyi és távoli számítási célnak támogatást nyújt. Minden egyes kísérlethez azt is nyomon követheti, több Futtatás gyakran különböző technikák, hiperparaméterek és egyéb iteratív próbálja szükség szerint. Az Azure Machine Learning segítségével nyomon követheti az egyéni metrikákat, és kísérletezzen a futtatásakor a data science megismételhetősége és auditability engedélyezése.

Az intranetes és ezek a modellek tesztelési és éles igényeinek.

## <a name="prerequisites"></a>Előfeltételek

+ [Rendelkezik a VS Code Tools for AI](how-to-vscode-tools.md) megfelelő az Azure Machine Learning.

+ Rendelkezik a [Azure Machine Learning SDK telepítve van a Pythonhoz készült](how-to-vscode-tools.md) VS Code használatával.

+ Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-and-manage-compute-targets"></a>Létrehozni és kezelni a számítási célnak

Visual Studio Code Tools for AI esetén, az adatok előkészítése, modelleket taníthat be és telepíthet, helyi vagy távoli számítási célnak a.

Ez a bővítmény az Azure Machine Learning számos különböző távoli számítási célnak támogatja. Tekintse meg a [teljes listája megtalálható a támogatott számítási célnak](how-to-set-up-training-targets.md) az Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Számítási célnak létrehozása az Azure Machine Learning a VS Code-ban

**Egy számítási célnak létrehozása:**

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure: A Machine Learning oldalsáv jelenik meg.

2. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. Az animált képen az előfizetés neve "OpenMind Studio" pedig a munkaterület "MyWorkspace". 

3. A munkaterület csomópont alatt, kattintson a jobb gombbal a **számítási** csomópont, majd **létrehozása számítási**.

4. Válassza ki a számítási célt a listából. 

5. A mezőben adjon meg egy egyedi nevet a számítási célnak, és adja meg a virtuális gép méretét.

6. Adja meg a speciális tulajdonságokat a JSON konfigurációs fájl, amely egy új lapon nyílik meg. 

7. Ha elkészült a számítási célnak konfigurálásával, kattintson a **Befejezés** jobb alsó sarokban.

Íme egy példa az Azure Batch AI szolgáltatással: [ ![létrehozása Azure Batch AI-számítási a VS Code-ban](./media/vscode-tools-for-ai/createcompute.gif)](./media/vscode-tools-for-ai/createcompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Távoli számítási erőforrások kísérletek a VS Code-ban

Egy távoli számítási célnak betanításakor használatához szüksége futtatási konfigurációs fájl létrehozása. A fájl arra utasítja az Azure Machine Learning nem csupán hol futtathatja a kísérletet, de is hogyan készítse elő a környezetét.

#### <a name="the-run-configuration-file"></a>A "futtatási konfiguráció" fájl

A VS Code-bővítmény automatikusan létrehoz egy futtatási konfigurációt a **helyi** és **docker** környezetekben a helyi számítógépen.

Ez az az alapértelmezett konfigurációs fájl futtatása egy kódrészletet.

Ha meg szeretné telepíteni a szalagtárak és függőségek összes saját maga, `userManagedDependencies: True` , és hogy helyi Kísérletezési futtatások majd használja az alapértelmezett Python-környezetet a VS Code Python bővítmény által megadott.

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
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

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>Modelleket taníthat és hangolás

A VS Code-ból az Azure Machine Learning segítségével rövid idő alatt a kód ismételt futtatásával, végighaladhat és hibakeresés és a forrás kód control megoldást választott használni. 

**A kísérlet futtatásához az Azure Machine Learning:**

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure: A Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. Az animált képen az előfizetés neve "OpenMind Studio" pedig a munkaterület "MyWorkspace". 

1. Munkaterület csomópontja alatt bontsa ki a **számítási** csomópontot, és kattintson a jobb gombbal a **konfiguráció futtatása** számítási is használni szeretné. 

1. Válassza ki **kísérlet futtatásához**.

1. Kattintson a **nézet kísérlet futtatása** betanított modelljeit a futtatások monitorozása és az integrált Azure Machine Learning portal megtekintéséhez.

   [![Machine learning-kísérlet futtatása a VS Code-ból](./media/vscode-tools-for-ai/runexperiment.gif)](./media/vscode-tools-for-ai/runexperiment.gif#lightbox)

## <a name="deploy-and-manage-models"></a>Üzembe helyezés és kezelés a modellek
Az Azure Machine Learning lehetővé teszi, hogy üzembe helyezése és kezelése a gépi tanulási modellek a felhőben és a peremhálózaton. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Regisztrálja a modellt az Azure Machine Learning, VS Code-ból

Most, hogy a modell van tanítva, regisztrálhatja a munkaterületén.
A regisztrált modellek nyomon követi és telepíteni kell.

**Regisztrálja a modellt:**

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure: A Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén.

1. A munkaterület csomópont alatt, kattintson a jobb gombbal **modellek** válassza **regisztrálása modell**.

1. A listából válassza ki, hogy a feltöltendő egy **modellfájl** (az egyetlen modellek) egy **modell mappa** (a modelljeit több fájlt, például a tensorflow-hoz). 

1. A fájl vagy mappa kiválasztásához használja a fájl kiválasztásakor párbeszédpanel.

   [![COMPUTE](./media/vscode-tools-for-ai/registermodel.gif)](./media/vscode-tools-for-ai/registermodel.gif#lightbox)

> [!Warning]
> Most távolítsa el a címkék a létrehozott json-fájlt.

### <a name="deploy-your-service-from-vs-code"></a>A VS Code-ból a szolgáltatás üzembe helyezése

VS Code használatát, a web service-t is telepítheti:
+ Az Azure Container Instance (aci Szolgáltatásban): teszteléshez
+ Az Azure Kubernetes Service (AKS): az éles környezetben 

Nem kell előre teszteléséhez, mivel azok jönnek létre menet közben egy ACI-tároló létrehozása. Azonban az AKS-fürtök előre konfigurálni kell. 

Tudjon meg többet [üzembe helyezés az Azure Machine Learning](how-to-deploy-and-where.md) általában.

**A webszolgáltatás üzembe helyezéséhez:**

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure: A Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén.

1. A munkaterület csomópontja alatt bontsa ki a **modellek** csomópont.

1. Kattintson a jobb gombbal a modell üzembe helyezése, és válassza a kívánt **szolgáltatás telepítése regisztrált modellből** parancsot a helyi menüből.

1. A VS Code parancskatalógus válassza ki a számítási célnak, melyben szeretné üzembe helyezni a listából. 

1. A mezőben adja meg a szolgáltatás nevét. 

1. Kattintson a jobb alsó sarokban párbeszédpanelen **Tallózás** , és válassza ki a pontozó szkript. A párbeszédpanel bezárása.

1. Ha egy helyi Docker-fájlt, kattintson a **Tallózás** a második, a megjelenő párbeszédpanelen. 

   Ha megszakíthatja a párbeszédpanelen, és nem ad meg egy helyi Docker-fájlt, az "Azure Machine Learning" egy alapértelmezett használják.

1. A harmadik párbeszédpanelen megjelenő, kattintson a **Tallózás** , és válassza ki a helyi conda-fájl elérési útját, vagy megadhatja a json-szerkesztő később a fájl elérési útját.

1. Ha egy schema.json fájlt szeretné-e használni, kattintson a **Tallózás** a negyedik párbeszédpanel, amely akkor jelenik meg, és válassza ki a fájlt.

A web service most már telepítve van.

Íme egy példa az Azure-Tárolópéldányon: [ ![VS Code-ból Azure-Tárolópéldányon](./media/vscode-tools-for-ai/deploy.gif)](./media/vscode-tools-for-ai/deploy.gif#lightbox)

## <a name="next-steps"></a>További lépések

A VS Code-en kívül a Machine Learning betanítási útmutatót szeretne, olvassa el a [oktatóanyag: az Azure Machine Learning-modellek betanításához](tutorial-train-models-with-aml.md).

A szerkesztési lépésenkénti útmutatóért fut, és helyileg, a kód hibakeresése lásd: a [Python Hello World oktatóanyag](https://code.visualstudio.com/docs/languages/python/docs/python/python-tutorial)
