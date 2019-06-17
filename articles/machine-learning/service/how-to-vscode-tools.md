---
title: A Visual Studio Code használata a machine Learning
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan telepítse az Azure Machine Learning a Visual Studio Code és a egy egyszerű kísérlet létrehozása az Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 70f9c34957b977aff9fc6211bf79415ed9abe255
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66016511"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>A Visual Studio Code az Azure Machine Learning használatának első lépései

Ebből a cikkből elsajátíthatja lesz a bővítmény használata az Azure Machine Learning a Visual Studio Code betanítására és gépi tanulási és deep learning-modellek üzembe helyezése.

A [Azure Machine Learning szolgáltatás](overview-what-is-azure-ml.md) támogatást nyújt, amely a helyi és távoli futtatása kísérletek számítási céljainak. Minden egyes kísérlethez azt is nyomon követheti, több Futtatás gyakran különböző technikák, hiperparaméterek és egyéb iteratív próbálja szükség szerint. Az Azure Machine Learning segítségével nyomon követheti az egyéni metrikákat, és kísérletezzen a futtatásakor a data science megismételhetősége és auditability engedélyezése.

Ezek a modellek tesztelési és éles igényeknek is telepítheti.

## <a name="prerequisites"></a>Előfeltételek

+ Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [az Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

+ A Visual Studio Code telepítve kell lennie. A Visual Studio Code egy könnyen használható, de hatékony nyílt forráskódú Kódszerkesztő, amely asztali számítógépeken fut. Ez tartalmaz beépített támogatást nyújt a Python és a más programozási nyelveket. Ha még nem telepítette a Visual Studio Code-ban [megtudhatja, hogyan](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Telepítse a Python 3.5-ös vagy újabb](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Az Azure Machine Learning a Visual Studio Code-bővítményének telepítése

Az Azure Machine Learning-bővítmény telepítésekor két további bővítmények automatikusan telepítve (ha az internet-hozzáféréssel rendelkezik). Ezek a [Azure-fiókkiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) és a [Microsoft Python bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Működik az Azure Machine Learning, a Visual Studio Code alakítsa át a Python-val integrált fejlesztőkörnyezet (IDE) kell. A Microsoft Python-bővítmény használatához szükséges [Python, a Visual Studio Code](https://code.visualstudio.com/docs/languages/python). Ezzel a bővítménnyel automatikusan települ az Azure Machine Learning-bővítménnyel. A bővítmény lehetővé teszi a Visual Studio Code egy kiváló IDE, és a Python interprety különböző operációs rendszereken működik. A Microsoft Python bővítmény automatikus kiegészítés, az IntelliSense, linting, hibakeresést és egységtesztelés biztosításához használt összes Visual Studio Code hatékonyságát. A bővítmény is lehetővé teszi a Python-környezetek, például a virtuális és a conda-környezetek közötti egyszerű váltást. További információ a Szerkesztés, fut, és hibakeresés Python-kód, tekintse meg a [hello-world Python-oktatóprogram](https://code.visualstudio.com/docs/python/python-tutorial).

Az Azure Machine Learning-bővítmény telepítése:

1. Nyissa meg a Visual Studio Code-ot.

1. Egy webböngészőben nyissa meg [Azure Machine Learning (előzetes verzió) a Visual Studio Code-bővítmény](https://aka.ms/vscodetoolsforai).

1. Válassza ki a webhelyen **telepítése**. 

1. Válassza ki a bővítmény lapon **telepítése**.

1. A bővítmény egy üdvözlő lapon megnyílik a Visual Studio Code, és az Azure szimbólum (az alábbi képernyőfelvételen vörös leírt) hozzáadódik a tevékenységsávon.

   ![Az Azure ikonra a tevékenységsávon Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. A párbeszédpanelen válassza ki a **bejelentkezés** , és kövesse az utasításokat az Azure-hitelesítésre. 
   
   Az Azure-fiók bővítményt, az Azure Machine Learning Visual Studio Code-bővítmény együtt lett telepítve, amely segítséget nyújt az Azure-fiókkal hitelesítést. Parancsok listája, az oldalon talál a [Azure-fiókkiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Tekintse meg a [IntelliCode bővítmény a Visual Studio Code (előzetes verzió)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode egy AI támogatású funkciókat kínál az IntelliSense használatához a Python, például az aktuális kód környezet alapján a leginkább releváns autocompletions adatcsatornához.

## <a name="install-the-azure-machine-learning-sdk"></a>Az Azure Machine Learning SDK telepítése

1. Ellenőrizze, hogy a Python 3.5-ös vagy újabb verziója telepítve van, és felismeri a Visual Studio Code-ot. Ha telepíti, most már, indítsa újra a Visual Studio Code és [jelölje be a Python-fordítóra](https://code.visualstudio.com/docs/python/python-tutorial).

1. A beépített terminál ablakában adja meg a használandó Python-fordítóra. Vagy válassza le az Enter billentyűt az alapértelmezett Python-fordítóra használja.

   ![Válassza ki a értelmezője számára készült](./media/vscode-tools-for-ai/python.png)

1. Az ablak jobb alsó sarkában lévő megjelenik egy értesítés, amely jelzi, hogy a [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) automatikusan települ. Az újonnan létrehozott Python-környezetet a helyi és a privát, és a Visual Studio Code használata az Azure Machine Learning szolgáltatás előfeltételei rendelkezik.

   ![Telepítse az Azure Machine Learning SDK a Pythonhoz](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Ismerkedés az Azure Machine Learning szolgáltatással

Mielőtt képzési megkezdése és üzembe helyezése a machine learning-modellek a Visual Studio Code, létre kell egy [az Azure Machine Learning szolgáltatás munkaterületén](concept-workspace.md) a felhőben. Ez a munkaterület tartalmazza a modellek és -erőforrásokat. 

Hozzon létre egy munkaterületet, és adja hozzá az első kísérlet:

1. A Visual Studio Code tevékenység sávon válassza az Azure ikont. Az Azure Machine Learning oldalsáv jelenik meg.

   [![Hozzon létre egy munkaterületet](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Kattintson a jobb gombbal az Azure-előfizetéshez, és válassza ki **munkaterület létrehozása**. Egy lista jelenik meg. Az animált képen például az előfizetés neve a következő **ingyenes próbaverzió**, és a munkaterület **TeamWorkspace**. 

1. Válasszon ki egy erőforráscsoportot a listából, vagy hozzon létre egy újat a parancskatalógus varázslóval.

1. A mezőbe írja be az új munkaterület egyedi, és törölje nevét. A képen például a munkaterület neve **TeamWorkspace**.

1. Válassza le az Enter billentyűt az új munkaterület létrehozása. A fa, az előfizetés neve alább megjelenik.

1. Kattintson a jobb gombbal a **kísérlet** csomópontot, majd **kísérlet létrehozása** a helyi menüből.  Kísérletek a futtatások Azure Machine Learning segítségével nyomon követheti.

1. A mezőben adjon meg egy nevet a. A képernyőképek, a kísérlet neve **MNIST**.
 
1. Válassza ki az új kísérlet létrehozásához adjon meg. A kísérlet a fában, a munkaterület neve alatt jelenik meg.

1. A munkaterületen, jobb gombbal kísérletre, állítsa be a **aktív** kísérletezhet. A **aktív** kísérlet az aktuális kísérlet. A mappa megnyitása a Visual Studio Code csatolva lesz a kísérlet a felhőben. A mappának tartalmaznia kell a helyi Python-szkriptek.

Most már a legfontosabb metrikákat tárolja a kísérlet előzményeit. Ehhez hasonlóan a modellek betanítása, lehet automatikusan feltölti az Azure Machine Learning és a kísérlet metrikákat és naplókat tárolja. 

[![Egy mappát a Visual Studio Code csatolása](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Létrehozni és kezelni a számítási célnak

Az Azure Machine Learning, a Visual Studio Code készítse elő adatait, modelleket taníthat be és telepíthet, helyi vagy távoli számítási célnak a.

A bővítmény több távoli számítási célnak támogatja az Azure Machine Learning. További információkért tekintse meg a támogatott [számítási céljainak az Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Számítási célnak létrehozása az Azure Machine Learning Visual Studio Code-ban

Egy számítási célnak létrehozása:

1. A Visual Studio Code tevékenység sávon válassza az Azure ikont. Az Azure Machine Learning oldalsáv jelenik meg.

2. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. A következő képen például az előfizetés neve a következő **ingyenes próbaverzió**, és a munkaterület **TeamWorkspace**. 

3. A munkaterület csomópont alatt, kattintson a jobb gombbal a **számítási** csomópont, majd **létrehozása számítási**.

4. Válassza ki a számítási célt a listából. 

5. A parancskatalógus jelölje be a virtuális gép méretét.

6. A parancskatalógus a mezőben adja meg a számítási célnak nevét. 

7. A JSON konfigurációs fájlban, amely egy új lapon nyílik meg adja meg minden speciális tulajdonságot. Megadhatja a tulajdonságokat, például a csomópontok maximális száma.

8. Ha befejezte a számítási célnak konfigurálása az ablak jobb alsó sarokban, válassza ki a **küldés**.

A következő példa bemutatja, hogyan hozhat létre egy Azure Machine Learning compute (AMLCompute):

[![A Visual Studio Code AML számítási létrehozása](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>A futtatási konfigurációs fájl

A Visual Studio Code-bővítmény automatikusan létrehoz egy helyi számítási célnak és futtatási konfigurációt a helyi és a docker-környezetben a helyi számítógépen. A futtatási konfigurációs fájlokat, a kapcsolódó számítási cél csomópont alatt találja. 

## <a name="train-and-tune-models"></a>Modelleket taníthat és hangolás

A Visual Studio Code (előzetes verzió) az Azure Machine Learning segítségével rövid idő alatt a kód ismételt futtatásával, végezzen el és hibakeresése és verziókövetési a megoldást használni. 

A kísérlet futtatása helyileg az Azure Machine Learning segítségével:

1. A Visual Studio Code tevékenység sávon válassza az Azure ikont. Az Azure Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. 

1. A munkaterület csomópontja alatt bontsa ki a **számítási** csomópontot, és kattintson a jobb gombbal a **konfiguráció futtatása** is használni szeretné a számítási. 

1. Válassza ki **kísérlet futtatásához**.

1. A Fájlkezelőben jelölje ki a futtatni kívánt szkriptet. 

1. Válassza ki **nézet kísérlet futtatása** betanított modelljeit a futtatások monitorozása és az integrált Azure Machine Learning portal megtekintéséhez.

A következő példa bemutatja, hogyan helyi futtatásához egy kísérlet:

[![Kísérlet futtatása helyileg](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Távoli számítási erőforrások kísérletek Visual Studio Code-ban

Egy távoli számítási célnak képzéshez használatához szüksége futtatási konfigurációs fájl létrehozása. A fájl arra utasítja az Azure Machine Learning nem csupán hol futtathatja a kísérletet, de is hogyan készítse elő a környezetét.

#### <a name="the-conda-dependencies-file"></a>A conda-függőségeket fájl

Alapértelmezés szerint egy új conda-környezet létrehozása az Ön számára, és a telepítési függőségek. Azonban meg kell adnia a függőségeket és azok verzióinak a a *aml_config/conda_dependencies.yml* fájlt. 

Az alábbi kódrészletet az alapértelmezett *aml_config/conda_dependencies.yml* megadja `tensorflow=1.12.0`. Ha nem adja meg a függőségi verzióját, a legújabb verzió használható. Hozzáadhat további függőségeket, a konfigurációs fájlban.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

A kísérlet Azure Machine Learning egy távoli futtatásához számítási célt:

1. A Visual Studio Code tevékenység sávon válassza az Azure ikont. Az Azure Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. 

1. A szerkesztő ablakában kattintson a jobb gombbal a Python-szkriptet, és válassza **AML: Kísérlet az Azure-beli futtató**. 

1. A parancskatalógus válassza ki a számítási célnak. 

1. A parancskatalógus a mezőben adja meg a futtatási konfiguráció nevét. 

1. Szerkessze a *conda_dependencies.yml* fájlban adja meg a kísérlet futtatókörnyezeti függőségek. Az ablak jobb alsó sarokban, válassza ki **küldés**. 

1. Válassza ki **nézet kísérlet futtatása** betanított modelljeit a futtatások monitorozása és az integrált Azure Machine Learning portal megtekintéséhez.

A következő példa bemutatja, hogyan egy távoli számítási célnak a kísérlet futtatásához:

[![Kísérlet futtatása egy távoli cél](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Üzembe helyezés és kezelés a modellek
Az Azure Machine Learning telepítheti és kezelheti a gépi tanulási modellek a felhőben és a peremhálózaton. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Regisztrálja a modellt az Azure Machine Learning Visual Studio Code-ból

Most, hogy a modell már betanított, regisztrálhatja a munkaterületén. Nyomon követheti, és a regisztrált modellek üzembe helyezése.

Regisztrálja a modellt:

1. A Visual Studio Code tevékenység sávon válassza az Azure ikont. Az Azure Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén.

1. A munkaterület csomópont alatt, kattintson a jobb gombbal **modellek** válassza **regisztrálása modell**.

1. A parancskatalógus a mezőben adja meg a modell neve. 

1. A listából, döntse el, hogy töltse fel a **modellfájl** (az egyetlen modellek) vagy egy **modell mappa** (a modelljeit több fájlt, például a tensorflow-hoz). 

1. Válassza ki a mappát vagy fájlt.

1. Ha befejezte a modell tulajdonságai konfigurálása az ablak jobb alsó sarokban, válassza ki a **küldés**. 

A következő példa bemutatja, hogyan regisztrálja a modellt az Azure Machine Learning:

[![Regisztrálás az AML-modell](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>A Visual Studio Code-ból a szolgáltatás üzembe helyezése

A Visual Studio Code a web service-t telepítheti:
+ Az Azure Container Instances (ACI) teszteléséhez.
+ Az Azure Kubernetes Service (AKS) éles üzemi környezetek részei.

Nem szeretne létrehozni egy ACI-tároló teszteléséhez előre, mivel az ACI-tárolók jönnek létre menet közben. Azonban szüksége az AKS-fürtök előre konfigurálhatja. További információkért lásd: [helyezhet üzembe modelleket az Azure Machine Learning szolgáltatással](how-to-deploy-and-where.md).

A webszolgáltatás üzembe helyezéséhez:

1. A Visual Studio Code tevékenység sávon válassza az Azure ikont. Az Azure Machine Learning oldalsáv jelenik meg.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén.

1. A munkaterület csomópontja alatt bontsa ki a **modellek** csomópont.

1. Kattintson a jobb gombbal a modell üzembe helyezése, és válassza a kívánt **szolgáltatás telepítése regisztrált modellből** a helyi menüből.

1. Válassza ki a telepíteni kívánt számítási célnak a parancskatalógus. 

1. A parancskatalógus a mezőben adja meg a szolgáltatás nevét.  

1. Válassza a parancskatalógus az Enter billentyűt a billentyűzeten keresse meg és válassza ki a parancsfájlt.

1. Válassza a parancskatalógus az Enter billentyűt a billentyűzeten keresse meg és válassza ki a conda-függőség fájlt.

1. Az ablak jobb alsó sarokban a szolgáltatás tulajdonságainak konfigurálása után, válassza ki a **küldés** üzembe helyezéséhez. A Tulajdonságok fájlban megadhat egy helyi docker-fájlt vagy egy schema.json fájlt.

A web service most már telepítve van.

A következő példa bemutatja, hogyan egy webszolgáltatás üzembe helyezéséhez:

[![Webszolgáltatás üzembe helyezése](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Billentyűparancsok használata

A billentyűzet használatával a Visual Studio Code az Azure Machine Learning-szolgáltatások eléréséhez. A legfontosabb billentyűparancsot tudni, hogy a Ctrl + Shift + P, mely megjeleníti a parancskatalógus. A parancs palettáról minden Visual Studio Code, többek között a leggyakoribb műveletek billentyűparancsai funkciója elérhető lesz.

[![A Visual Studio Code az Azure Machine Learning billentyűparancsai](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>További lépések

* Útmutató az Azure Machine Learning Visual Studio Code-en kívül betanításához leírását lásd: [oktatóanyag: Az Azure Machine Learning-modellek betanításához](tutorial-train-models-with-aml.md).
* Szerkesztése, futtatása és helyi kódok hibakeresése egy leírását lásd: a [hello-world Python-oktatóprogram](https://code.visualstudio.com/docs/python/python-tutorial).
