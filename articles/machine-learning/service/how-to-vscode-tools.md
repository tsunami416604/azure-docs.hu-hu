---
title: A Visual Studio Code használata
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
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995251"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>A Visual Studio Code az Azure Machine Learning használatának első lépései

Ebben a cikkben, megtudhatja, hogyan használható a **az Azure Machine Learning a Visual Studio Code** bővítmény betanítását és üzembe helyezése a gépi tanulási és deep learning-modellek a Visual Studio Code (a VS Code) az Azure Machine Learning szolgáltatással.

Az Azure Machine Learning szolgáltatás támogatást biztosít a futó kísérletek, helyi és távoli számítási célokhoz. Minden egyes kísérlethez azt is nyomon követheti, több Futtatás gyakran különböző technikák, hiperparaméterek és egyéb iteratív próbálja szükség szerint. Az Azure Machine Learning segítségével nyomon követheti az egyéni metrikákat, és kísérletezzen a futtatásakor a data science megismételhetősége és auditability engedélyezése.

Az intranetes és ezek a modellek tesztelési és éles igényeinek.

## <a name="prerequisites"></a>Előfeltételek

+ Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

+ A Visual Studio Code telepítve kell lennie. A VS Code egy könnyen használható, de hatékony nyílt forráskódú Kódszerkesztő, amely asztali számítógépeken fut. Ez tartalmaz beépített támogatást nyújt a Python és más.  [Ismerje meg, hogyan telepítheti a VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Telepítse a Python 3.5-ös vagy újabb](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Az Azure Machine Learning a VS Code-bővítmény telepítése

Amikor telepíti a **Azure Machine Learning** bővítményt, két további bővítmények automatikusan telepítve (ha az internet-hozzáféréssel rendelkezik). Azok a [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítmény és a [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) bővítmény

Dolgozunk az Azure Machine Learning, a VS Code alakítsa át a Python ide-vel kell. Használata [Python, a Visual Studio Code](https://code.visualstudio.com/docs/languages/python), igényel a Microsoft Python bővítmény, amely az Azure Machine Learning-bővítménnyel automatikusan telepíti. A bővítmény lehetővé teszi a VS Code kiváló IDE, és a Python interprety különböző bármely operációs rendszeren működik. A modul összes VS Code teljesítményt biztosít az automatikus kiegészítés és az IntelliSense, linting, hibakeresést és egységtesztelés együtt könnyedén váltani a Python-környezetek, például a virtuális és a conda-környezetek között. Tekintse meg a szerkesztési, ebben az útmutatóban fut, és hibakeresés Python-kód, tekintse meg a [Python Hello World oktatóanyag](https://code.visualstudio.com/docs/python/python-tutorial)

**Az Azure Machine Learning-bővítmény telepítése:**

1. Indítsa el a VS Code.

1. Egy böngészőben keresse fel: [A Visual Studio Code (előzetes verzió) az Azure Machine Learning](https://aka.ms/vscodetoolsforai) bővítmény

1. A weblapon kattintson **telepítése**. 

1. A bővítmény lapon kattintson a **telepítése**.

1. A bővítmény a VS Code-ban megnyílik egy üdvözlő lapon, és (a piros négyzetet az alábbi képen leírt) Azure szimbólum tevékenységsávon van adva.

   ![A Visual Studio Code tevékenységsávon Azure ikon](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Kattintson a párbeszédpanelen **bejelentkezés** , és kövesse a képernyőn használatával Azure-hitelesítésre. 
   
   Az Azure-fiók bővítményt, az Azure Machine Learning a VS Code-bővítmény együtt lett telepítve, amely segítséget nyújt az Azure-fiókkal hitelesítést. A parancsok listájának megtekintéséhez a [Azure-fiókkiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) lapot.

> [!Tip] 
> Tekintse meg a [IntelliCode bővítmény a VS Code (előzetes verzió)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode Python, az IntelliSense behívásakor az aktuális kód környezettől függően a leginkább releváns automatikus kiegészítése például AI támogatású képességeket biztosít.

## <a name="azure-ml-sdk-installation"></a>Azure ML SDK Installation

1. Győződjön meg arról, hogy a Python 3.5-ös vagy újabb telepítve és a VS Code rozpoznána. Ha telepíti azt most, majd indítsa újra a VS Code, és válassza ki a egy Python-fordítóra található utasításokat követve https://code.visualstudio.com/docs/python/python-tutorial.

1. A beépített terminál ablakában adja meg a használandó Python-fordítóra vagy tudja érni **Enter** az alapértelmezett Python-fordítóra használatára.

   ![Válassza ki a értelmezője számára készült](./media/vscode-tools-for-ai/python.png)

1. Az ablak jobb alsó sarkában található egy értesítés jelenik meg amely azt jelzi, hogy az Azure Machine Learning SDK automatikusan telepíti.    Egy helyi, privát Python-környezetet jön létre, amely rendelkezik az Azure Machine Learning szolgáltatás használata a Visual Studio Code előfeltételeit.

   ![Pythonhoz készült Azure Machine Learning SDK telepítése](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Ismerkedés az Azure Machine Learning szolgáltatással

Mielőtt elkezdené a képzés és a VS Code használata a machine learning-modellek üzembe helyezéséhez, létre kell egy [az Azure Machine Learning szolgáltatás munkaterületén](concept-azure-machine-learning-architecture.md#workspace) a felhőben is a modellek és -erőforrásokat tartalmaznak. Ismerje meg, hogyan hozzon létre egyet, és az első kísérlet létrehozása az adott munkaterületen.

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure Machine Learning oldalsáv jelenik meg.

   [![Telepítése](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Kattintson a jobb gombbal az Azure-előfizetéshez, és válassza ki **munkaterület létrehozása**. Egy lista jelenik meg. Az animált képen az előfizetés neve "Ingyenes próbaverzió" pedig a munkaterület "TeamWorkspace". 

1. A listából válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a parancskatalógus a varázsló használatával.

1. A mezőbe írja be az új munkaterület egyedi, és törölje nevét. A képernyőképek a munkaterület neve "TeamWorkspace".

1. Az enter, és az új munkaterület létrehozása. Az előfizetés neve alatti fájában jelenik meg.

1. A kísérlet csomóponton kattintson a jobb gombbal, és válassza a **kísérlet létrehozása** a helyi menüből.  Kísérletek a futtatások Azure Machine Learning segítségével nyomon követheti.

1. A mezőben adjon nevet a kísérlet során. A képernyőképek a kísérlet neve "MNIST".
 
1. Az enter, és az új kísérlet jön létre. A munkaterület neve alatti fájában jelenik meg.

1. Kattintson a jobb gombbal a munkaterület egy kísérletet, és válassza a "Állítja be aktív kísérlet". A **"Aktív"** kísérletet a kísérlet jelenleg használ, és a mappa megnyitása a VS Code lesz összekapcsolva Ez a kísérlet, a felhőben. A mappának tartalmaznia kell a helyi Python-szkriptek.

   Most már minden, a kísérlet fut, is futtathatja a kísérletet, így a fő mérőszámok tárolja a kísérlet előzmények, és a modellek betanítása, automatikusan lekérése az Azure Machine Learning feltöltött és a kísérlet metrikákat és naplókat tárolja.

   [![Csatolhat egy mappát a VS Code-ban](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


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

7. Adja meg a speciális tulajdonságokat a JSON konfigurációs fájl, amely egy új lapon nyílik meg. Megadhatja a tulajdonságokat, például a csomópontok maximális száma.

8. Ha elkészült a számítási célnak konfigurálásával, kattintson a **küldés** a képernyő jobb alsó sarkában.

Íme egy példa egy Azure Machine Learning Compute (AMLCompute) létrehozásához: [![A VS Code AML számítási létrehozása](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>A "futtatási konfiguráció" fájl

A VS Code-bővítmény automatikusan hozzon létre egy helyi számítási célnak, és futtassa a konfiguráció a **helyi** és **docker** környezetekben a helyi számítógépen. A futtatási konfigurációs fájlok a cél kapcsolódó számítási csomópont alatt található. 

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

Alapértelmezés szerint egy új conda-környezet létrehozása az Ön számára, és a telepítési függőségek. Azonban meg kell adnia a függőségeket és azok verzióinak a a `aml_config/conda_dependencies.yml` fájlt. 

Ez az egy kódrészletet az alapértelmezett aml_config/conda_dependencies.yml a. Meghatározhatja például, hogy "tensorflow = 1.12.0' az alább látható módon. Ha nem adja meg a függőségi verzióját, a legújabb verziót fog használni.  
Hozzáadhat további függőségeket, a konfigurációs fájlban.

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

### <a name="use-keyboard-shortcuts"></a>Billentyűparancsok használata

A VS Code többségét, mint az Azure Machine Learning szolgáltatások a VS Code-ban érhetők el, a billentyűzetről. Tudni, hogy a legfontosabb billentyűkombinációt a Ctrl + Shift + P, hogy megjelenítse a Parancskatalógus. Itt az összes VS Code, többek között a leggyakoribb műveletek billentyűparancsai funkciója elérhető lesz.

[![Az Azure Machine Learning a VS Code billentyűparancsai](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>További lépések

A VS Code-en kívül a Machine Learning betanítási útmutatót szeretne, olvassa el a [oktatóanyag: Az Azure Machine Learning-modellek betanításához](tutorial-train-models-with-aml.md).

A szerkesztési lépésenkénti útmutatóért fut, és helyileg, a kód hibakeresése lásd: a [Python Hello World oktatóanyag](https://code.visualstudio.com/docs/python/python-tutorial)
