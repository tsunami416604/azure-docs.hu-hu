---
title: A Visual Studio Code használata a Machine learninghez
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan telepítheti Azure Machine Learning a Visual Studio Code-ba, és hogyan hozhat létre egy egyszerű kísérletet Azure Machine Learningban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ronglu
author: ronglums
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: b1d18006e6ec3ff1659f8fb80f8cf6766797d76b
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114429"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Ismerkedés a Visual Studio Code Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio Code Azure Machine Learning bővítményét a Machine learning és a Deep learning modellek betanításához és üzembe helyezéséhez.

A [Azure Machine learning szolgáltatás](overview-what-is-azure-ml.md) támogatja a helyileg és távoli számítási célokon futtatott kísérletek támogatását. Minden egyes kísérlethez azt is nyomon követheti, több Futtatás gyakran különböző technikák, hiperparaméterek és egyéb iteratív próbálja szükség szerint. Az Azure Machine Learning segítségével nyomon követheti az egyéni metrikákat, és kísérletezzen a futtatásakor a data science megismételhetősége és auditability engedélyezése.

Ezeket a modelleket a tesztelési és a termelési igényekhez is üzembe helyezheti.

## <a name="prerequisites"></a>Előfeltételek

+ Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

+ Telepíteni kell a Visual Studio Code-ot. A Visual Studio Code egy egyszerű, de hatékony forráskód-szerkesztő, amely az asztalon fut. Beépített támogatást nyújt a Pythonhoz és más programozási nyelvekhez. Ha még nem telepítette a Visual Studio Code-ot, [tekintse meg](https://code.visualstudio.com/docs/setup/setup-overview)a című témakört.

+ [Telepítse a Python 3,5-es vagy újabb verzióját](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>A Azure Machine Learning-bővítmény telepítése a Visual Studio Code-hoz

A Azure Machine Learning bővítmény telepítésekor a rendszer két további bővítményt is automatikusan telepít (ha van internet-hozzáférése). Ezek az [Azure-fiók kiterjesztése](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) és a [Microsoft Python bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

A Azure Machine Learning való együttműködéshez be kell kapcsolni a Visual Studio Code-ot egy Python-integrált fejlesztői környezetbe (IDE). A Python használatához a [Visual Studio Code-ban](https://code.visualstudio.com/docs/languages/python)a Microsoft Python bővítményre van szükség. Ezt a bővítményt a rendszer automatikusan telepíti a Azure Machine Learning bővítménnyel. A bővítmény lehetővé teszi, hogy a Visual Studio Code kiváló IDE-ként működjön, és minden operációs rendszeren, számos Python-tolmácsmal működik. A Microsoft Python bővítmény a Visual Studio Code összes funkcióját használja az automatikus kiegészítés, az IntelliSense, a betöltés, a hibakeresés és az egység tesztelése érdekében. A bővítmény használatával könnyedén válthat a Python-környezetek között, beleértve a virtuális és a Conda környezeteket is. A Python-kódok szerkesztésével, futtatásával és hibakeresésével kapcsolatos további információkért tekintse meg a [Python Hello-World oktatóanyagot](https://code.visualstudio.com/docs/python/python-tutorial).

A Azure Machine Learning bővítmény telepítése:

1. Nyissa meg a Visual Studio Code-ot.

1. A böngészőben nyissa meg a [Azure Machine learning for Visual Studio Code Extension (előzetes verzió)](https://aka.ms/vscodetoolsforai)lehetőséget.

1. Ezen a lapon válassza a **telepítés**lehetőséget. 

1. A bővítmény lapon válassza a **telepítés**lehetőséget.

1. A bővítmény egy üdvözlő lapja megjelenik a Visual Studio Code-ban, és az Azure-szimbólum (az alábbi képernyőképen piros színnel jelenik meg) a tevékenység sávjába kerül.

   ![Azure ikon a Visual Studio Code tevékenység sávján](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. A párbeszédpanelen válassza a **Bejelentkezés** lehetőséget, és kövesse az utasításokat az Azure-beli hitelesítéshez. 
   
   Az Azure-fiók bővítmény, amely a Visual Studio Code-bővítménnyel Azure Machine Learning együtt települt, segít az Azure-fiókkal való hitelesítésben. A parancsok listáját az [Azure-fiók bővítményének](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)oldalán tekintheti meg.

> [!Tip] 
> Tekintse meg a [Visual Studio Code (előzetes verzió) IntelliCode bővítményét](https://go.microsoft.com/fwlink/?linkid=2006060). A IntelliCode mesterséges intelligenciával rendelkező funkciókat biztosít a Pythonban, például az aktuális kód kontextusán alapuló, a leglényegesebb autobefejezési műveletek elvégzését.

## <a name="install-the-azure-machine-learning-sdk"></a>Az Azure Machine Learning SDK telepítése

1. Győződjön meg arról, hogy a Python 3,5-es vagy újabb verziója telepítve van, és a Visual Studio Code felismeri. Ha most telepíti, indítsa újra a Visual Studio Code-ot, és [válassza ki a Python](https://code.visualstudio.com/docs/python/python-tutorial)-tolmácsot.

1. Az integrált terminál ablakban válassza ki a használni kívánt Python-tolmácsot. Vagy válassza az ENTER billentyűt az alapértelmezett Python-értelmező használatához.

   ![A tolmács kiválasztása](./media/vscode-tools-for-ai/python.png)

1. Az ablak jobb alsó sarkában megjelenik egy értesítés, amely azt jelzi, hogy a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) telepítése automatikusan megtörténik. Az újonnan létrehozott Python-környezet helyi és privát, és rendelkezik a Visual Studio Code előfeltételeivel a Azure Machine Learning szolgáltatással való használathoz.

   ![A Pythonhoz készült Azure Machine Learning SDK telepítése](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Ismerkedés az Azure Machine Learning szolgáltatással

Mielőtt elkezdi a gépi tanulási modellek a Visual Studio Code-ban való üzembe helyezését, létre kell hoznia egy [Azure Machine learning szolgáltatás](concept-workspace.md) -munkaterületet a felhőben. Ez a munkaterület fogja tartalmazni a modelleket és az erőforrásokat. 

Munkaterület létrehozása és az első kísérlet hozzáadása:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont. Megjelenik a Azure Machine Learning oldalsáv.

   [![Munkaterület létrehozása](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Kattintson a jobb gombbal az Azure-előfizetésre, és válassza a **Munkaterület létrehozása**lehetőséget. Megjelenik egy lista. A példában szereplő animált ábrán az előfizetés neve **ingyenes próbaverzió**, a munkaterület pedig **TeamWorkspace**. 

1. Válasszon ki egy erőforráscsoportot a listából, vagy hozzon létre egy újat a parancs palettáján a varázsló segítségével.

1. A mezőbe írja be az új munkaterület egyedi és egyértelmű nevét. A példában szereplő képen a munkaterület neve **TeamWorkspace**.

1. Az új munkaterület létrehozásához kattintson az ENTER gombra. Megjelenik a fában az előfizetés neve alatt.

1. Kattintson a jobb gombbal a **kísérlet** csomópontjára, majd válassza a **kísérlet létrehozása** lehetőséget a helyi menüből.  A kísérletek Azure Machine Learning használatával követik nyomon a futtatásokat.

1. A mezőben adja meg a kísérlet nevét. A példa képernyőképeken a kísérlet neve **MNIST**.
 
1. Az új kísérlet létrehozásához kattintson az ENTER gombra. A kísérlet megjelenik a fában a munkaterület neve alatt.

1. Egy munkaterületen kattintson a jobb gombbal egy kísérletre, és állítsa be **aktív** kísérletként. Az **aktív** kísérlet az aktuális kísérlet. A Visual Studio Code-ban megnyitott mappája ehhez a kísérlethez lesz társítva a felhőben. Ennek a mappának tartalmaznia kell a helyi Python-parancsfájlokat.

A rendszer most a fő mérőszámokat a kísérlet előzményei között tárolja. Hasonlóképpen, a betanított modellek automatikusan fel lesznek töltve Azure Machine Learningre, és a kísérleti metrikák és naplók mellett tárolódnak. 

[![Mappa csatolása a Visual Studio Code-ban](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Létrehozni és kezelni a számítási célnak

A Visual Studio Code Azure Machine Learning segítségével előkészítheti az adatait, betaníthatja a modelleket, és helyileg és távoli számítási célokon is üzembe helyezheti azokat.

A bővítmény több távoli számítási célt is támogat Azure Machine Learning számára. További információkért tekintse meg a Azure Machine Learning által támogatott [számítási célok](how-to-set-up-training-targets.md)teljes listáját.

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Számítási célok létrehozása Azure Machine Learninghoz a Visual Studio Code-ban

Számítási cél létrehozása:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont. Megjelenik a Azure Machine Learning oldalsáv.

2. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. Az alábbi ábrán az előfizetés neve **ingyenes próbaverzió**, a munkaterület pedig **TeamWorkspace**. 

3. A munkaterület csomópont alatt, kattintson a jobb gombbal a **számítási** csomópont, majd **létrehozása számítási**.

4. Válassza ki a számítási célt a listából. 

5. A parancs palettáján válassza ki a virtuális gép méretét.

6. A parancs paletta mezőjében adja meg a számítási cél nevét. 

7. Az új lapon megnyíló JSON-konfigurációs fájlban a speciális tulajdonságokat kell megadnia. Megadhat olyan tulajdonságokat, mint például a csomópontok maximális száma.

8. Amikor befejezte a számítási cél konfigurálását, az ablak jobb alsó sarkában válassza a **Küldés**lehetőséget.

Íme egy példa arra, hogyan hozhat létre Azure Machine Learning számítást (AMLCompute):

[![PÉNZMOSÁS-számítás létrehozása a Visual Studio Code-ban](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>A konfigurációs fájl futtatása

A Visual Studio Code bővítmény automatikusan létrehoz egy helyi számítási célt, és futtatja a helyi és a Docker-környezetek konfigurációit a helyi számítógépen. A konfigurációs fájlok futtatása a társított számítási cél csomópont alatt található. 

## <a name="train-and-tune-models"></a>Modelleket taníthat és hangolás

A Visual Studio Code (előzetes verzió) Azure Machine Learning használatával gyorsan megismételhető a kód, a lépés és a hibakeresés, valamint a forráskód-vezérlési megoldás használata. 

A kísérlet helyi futtatása a Azure Machine Learning használatával:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont. Megjelenik a Azure Machine Learning oldalsáv.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. 

1. A munkaterület csomópont alatt bontsa ki a **számítási** csomópontot, majd kattintson a jobb gombbal a használni kívánt számítási **konfiguráció futtatására** . 

1. Válassza ki **kísérlet futtatásához**.

1. A Fájlkezelőben válassza ki a futtatni kívánt parancsfájlt. 

1. Válassza a **kísérlet futtatásának megtekintése** lehetőséget, hogy az integrált Azure Machine learning portálon figyelje a futtatásokat, és tekintse meg a betanított modelleket.

Íme egy példa arra, hogyan futtathat egy kísérletet helyileg:

[![Kísérlet helyi futtatása](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Távoli számítások használata a Visual Studio Code-kísérleteknél

Ha távoli számítási célt kíván használni a betanításhoz, létre kell hoznia egy futtatási konfigurációs fájlt. A fájl arra utasítja az Azure Machine Learning nem csupán hol futtathatja a kísérletet, de is hogyan készítse elő a környezetét.

#### <a name="the-conda-dependencies-file"></a>A conda-függőségeket fájl

Alapértelmezés szerint a rendszer létrehoz egy új Conda-környezetet, és felügyeli a telepítési függőségeit. A függőségeket és azok verzióit azonban a *aml_config/conda_dependencies. YML* fájlban kell megadnia. 

A következő kódrészlet az alapértelmezett *aml_config/conda_dependencies. YML* határozza meg `tensorflow=1.12.0`. Ha nem határozza meg a függőség verzióját, a rendszer a legújabb verziót fogja használni. Hozzáadhat további függőségeket, a konfigurációs fájlban.

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

A kísérlet futtatása a Azure Machine Learning használatával távoli számítási céllal:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont. Megjelenik a Azure Machine Learning oldalsáv.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén. 

1. A szerkesztő ablakban kattintson a jobb gombbal a Python-szkriptre, és **válassza a pénzmosás elemet: Futtasson kísérletet az**Azure-ban. 

1. A parancs palettáján válassza ki a számítási célt. 

1. A parancs paletta mezőjében adja meg a futtatási konfiguráció nevét. 

1. Szerkessze a *conda_dependencies. YML* fájlt a kísérlet futásidejű függőségeinek megadásához. Ezután az ablak jobb alsó sarkában válassza a **Küldés**lehetőséget. 

1. Válassza a **kísérlet futtatásának megtekintése** lehetőséget, hogy az integrált Azure Machine learning portálon figyelje a futtatásokat, és tekintse meg a betanított modelleket.

Íme egy példa arra, hogyan futtathat kísérletet egy távoli számítási célra:

[![Kísérlet futtatása távoli célra](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Üzembe helyezés és kezelés a modellek
Azure Machine Learning a gépi tanulási modellek a felhőben és a peremen is üzembe helyezhetők és kezelhetők. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>A modell regisztrálása Azure Machine Learning a Visual Studio Code-ból

Most, hogy betanítta a modellt, regisztrálhatja azt a munkaterületen. A regisztrált modelleket nyomon követheti és üzembe helyezheti.

A modell regisztrálása:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont. Megjelenik a Azure Machine Learning oldalsáv.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén.

1. A munkaterület csomópont alatt, kattintson a jobb gombbal **modellek** válassza **regisztrálása modell**.

1. A parancs paletta mezőjében adja meg a modell nevét. 

1. A listából válassza ki, hogy fel kívánja-e tölteni egy **modellt** (egyetlen modell esetében) vagy egy **modell mappát** (több fájlból álló modelleknél, például TensorFlow). 

1. Válassza ki a mappát vagy fájlt.

1. Amikor befejezte a modell tulajdonságainak konfigurálását, az ablak jobb alsó sarkában válassza a **Küldés**lehetőséget. 

Az alábbi példa bemutatja, hogyan regisztrálhat modelleket Azure Machine Learningba:

[![Modell regisztrálása a pénzmosás-ban](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>A szolgáltatás üzembe helyezése a Visual Studio Code-ból

A Visual Studio Code-ban a webszolgáltatását a következőre helyezheti üzembe:
+ Azure Container Instances (ACI) teszteléshez.
+ Azure Kubernetes szolgáltatás (ak) éles környezetben.

A teszteléshez nem kell ACI-tárolót létrehoznia, mert az ACI-tárolók menet közben jönnek létre. Azonban az AK-fürtöket előre kell konfigurálnia. További információ: [modellek üzembe helyezése a Azure Machine learning szolgáltatással](how-to-deploy-and-where.md).

Webszolgáltatás üzembe helyezése:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont. Megjelenik a Azure Machine Learning oldalsáv.

1. A fanézetben bontsa ki az Azure-előfizetés és az Azure Machine Learning szolgáltatás munkaterületén.

1. A munkaterület csomópontja alatt bontsa ki a **modellek** csomópont.

1. Kattintson a jobb gombbal a telepíteni kívánt modellre, majd válassza a **szolgáltatás telepítése a regisztrált modellből** lehetőséget a helyi menüből.

1. A parancs palettáján válassza ki azt a számítási célt, amelyet telepíteni kíván. 

1. A parancs paletta mezőjében adja meg a szolgáltatás nevét.  

1. A parancs palettáján válassza ki az ENTER billentyűt a billentyűzeten a tallózáshoz, és válassza ki a parancsfájlt.

1. A parancs palettáján válassza ki az ENTER billentyűt a billentyűzeten a tallózáshoz, és válassza ki a Conda függőségi fájlt.

1. Amikor befejezte a szolgáltatás tulajdonságainak konfigurálását, az ablak jobb alsó sarkában válassza a Submit ( **Küldés** ) lehetőséget a telepítéshez. A szolgáltatás tulajdonságai fájlban megadhat egy helyi Docker-fájlt vagy egy Schema. JSON fájlt.

A web service most már telepítve van.

Íme egy példa arra, hogyan helyezhet üzembe egy webszolgáltatást:

[![Webszolgáltatás üzembe helyezése](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Billentyűparancsok használata

A billentyűzettel elérheti a Visual Studio Code Azure Machine Learning funkcióit. A legfontosabb billentyűparancs a CTRL + SHIFT + P billentyűkombinációval, amely a parancssort jeleníti meg. A parancs palettáján hozzáférhet a Visual Studio Code összes funkciójához, beleértve a leggyakoribb műveletekhez használható billentyűparancsokat is.

[![A Visual Studio Code Azure Machine Learning billentyűparancsai](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>További lépések

* A Visual Studio code-on kívüli Azure Machine learning betanításával kapcsolatos útmutatóért lásd [: oktatóanyag: Modellek betanítása](tutorial-train-models-with-aml.md)Azure Machine Learningokkal.
* A kódok helyi szerkesztéséről, futtatásáról és hibakereséséről a [Python Hello-World oktatóanyagban](https://code.visualstudio.com/docs/python/python-tutorial)talál további információt.
