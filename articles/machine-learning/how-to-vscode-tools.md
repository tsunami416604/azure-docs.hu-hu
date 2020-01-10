---
title: Azure Machine Learning a Visual Studio Code-ban
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan telepíthet Azure Machine Learning a Visual Studio Code-hoz, és hogyan hozhat létre kísérletet a Azure Machine Learningban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: 59002d0f17fc7213d8db0fad01b1f984542adbd5
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834810"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Ismerkedés a Visual Studio Code Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan használhatja a **Visual Studio Code** -bővítményhez készült Azure Machine Learningt a gépi tanulási modellek betanításához és üzembe helyezéséhez.

A [Azure Machine learning](overview-what-is-azure-ml.md) egyszerűsíti a gépi tanulási modellek létrehozását, betanítását és üzembe helyezését.
+ A betanításhoz támogatást nyújt a kísérletek helyi vagy távoli futtatásához. Minden kísérlet esetében több Futtatás egyéni metrikáit is naplózhatja a hiperparaméterek beállítása finomhangolásához
+ A Azure Machine Learning használatával könnyedén üzembe helyezhet gépi tanulási modelleket a tesztelési és a termelési igényekhez.

## <a name="prerequisites"></a>Előfeltételek

+ Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

+ Telepítse a [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)-ot, amely egy egyszerű Kódszerkesztő, amely Windows, Mac és Linux rendszeren fut.

+ [Telepítse a Python 3,5-es vagy újabb verzióját](https://www.anaconda.com/download/).


## <a name="install-the-extension"></a>A bővítmény telepítése

A Azure Machine Learning bővítmény telepítésekor a rendszer két további bővítményt is telepít automatikusan. Ezek az [Azure-fiók kiterjesztése](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) és a [Microsoft Python bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python). A Python-kód szerkesztésére, futtatására és hibakeresésére szolgáló Python-bővítmény használatáról további információt a [Python Hello-World oktatóanyagban](https://code.visualstudio.com/docs/Python/Python-tutorial)talál.

A Azure Machine Learning bővítmény telepítése:

1. Nyissa meg a Visual Studio Code-ot.

1. Váltson a kiterjesztések lapra, és keressen rá a "Azure Machine Learning" kifejezésre.

1. A bővítmény lapon válassza a **telepítés**lehetőséget.

1. A bővítmény egy üdvözlő lapja megnyílik a Visual Studio Code-ban, és az Azure-szimbólum (az alábbi képernyőképen piros színnel jelenik meg) megjelenik a tevékenység sávjában.

   ![Azure ikon a Visual Studio Code tevékenység sávján](./media/how-to-vscode-tools/azure-activity-bar.png)

1. A párbeszédpanelen válassza a **Bejelentkezés** lehetőséget, és kövesse az utasításokat az Azure-beli hitelesítéshez.

   Az Azure-fiók bővítmény, amely a Visual Studio Code-bővítménnyel Azure Machine Learning együtt települt, segít az Azure-fiókkal való hitelesítésben. A parancsok listáját az [Azure-fiók bővítményének](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)oldalán tekintheti meg.

> [!TIP]
> A bővítmény telepítőjét közvetlenül a [Visual Studio Code bővítmény (előzetes verzió) Azure Machine learning](https://aka.ms/vscodetoolsforai)is letöltheti.

## <a name="quickstart-with-azure-machine-learning"></a>Rövid útmutató Azure Machine Learning
A betanítási szkriptek több módon is futtathatók Azure Machine Learning használatával. Ha most ismerkedik az első lépésekkel, ismerkedjen meg az Azure-ban futtatandó képzési szkriptek gyors elküldésével.

Ha már ismeri a Azure Machine Learning fogalmakat, és további részleteket szeretne megtudni a bővítménnyel való felügyeletéről és használatáról, tekintse meg az alábbi, Azure Machine Learning részletesebben a [vs Code-](how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code) ot.

## <a name="run-an-existing-python-training-script-in-azure"></a>Meglévő Python-betanítási szkript futtatása az Azure-ban
Ha meglévő betanítási szkripttel rendelkezik, a VS Code-hoz készült Azure Machine Learning-bővítmény nem csupán kiváló szerkesztési, hibakeresési és forrásoldali felügyeleti élményt nyújt, de megkönnyíti az Azure-beli szkriptek metrikáinak futtatását és tárolását is.

Lássunk hozzá. Használhat saját betanítási parancsfájlt, ha készen áll, vagy klónozott minta [vscode-Tools-for-AI](https://github.com/microsoft/vscode-tools-for-ai)-tárházat. Ez a bővítménysel kapcsolatos problémák bejelentésére szolgáló nyilvános tárház. Emellett egy kis **mnist** -minta mappát is tartalmaz, amelyet ebben a példában fogunk használni.

1. Nyissa meg a **mnist** MAPPÁT a vs Code-ban.

1. Hozzon létre egy új Python-környezetet a kedvenc virtuális környezet csomagjának vagy Anacondajának használatával, és telepítse a TensorFlow és a NumPy csomagokat.

1. Válassza ki a VS Code állapotjelző sáv bal alsó sarkában a Python-tolmácsként létrehozott új környezetet.

1. Nyissa meg a **Train.py** , és futtassa a hibakeresőt, és nyomja le a Futtatás gombot (vagy csak nyomja le az F5 billentyűt).

   [![MNIST-képzés futtatása](./media/how-to-vscode-tools/run-mnist.gif)](./media/how-to-vscode-tools/run-mnist.gif#lightbox)

Ha minden megfelelően van telepítve, a szkript futni fog, és létrehoz egy TensorFlow modellt a kimenetek mappában.

[![TensorFlow-modell megjelenítése](./media/how-to-vscode-tools/show-tensorflow-model.gif)](./media/how-to-vscode-tools/show-tensorflow-model.gif#lightbox)

Most, hogy már tudja, hogy a szkript megfelelően fut, futtassa azt az Azure-ban!

Ez egyszerűen elvégezhető a **Train.py**további módosítása nélkül. Néhány egyszerű módosítással azonban a Azure Machine Learning használatával automatikusan nyomon követheti a kiválasztott fontos mérőszámokat az egyes képzések futtatásával kapcsolatban.

### <a name="make-azure-aware-of-your-run-metrics"></a>Az Azure ismerete a futtatási metrikák révén
Ha módosítani szeretné a projektjét, hogy az Azure képes legyen a fontos információk ismeretére a futtatásokban:

1. Hozzon létre egy **amlrun.py** nevű fájlt ugyanabban a mappában, mint a **Train.py**

    ```Python
    import azureml
    from azureml.core import Run

    # Access the Azure ML run
    # Init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. A amlrun-fájl importálása a **Train.py** -ben

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. A Run objektum inicializálása a **Train.py** -ben

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. Metrikák naplózása az Azure-ba a Run. log () függvénnyel:

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # Log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>A szkript futtatása az Azure-ban
Ennyi az egész! Most már csak a bővítmény használatával futtathatja a szkriptet a felhőben! Vegye figyelembe, hogy a következő bemutató videó az új Azure Machine Learning munkaterület és számítások létrehozásához szükséges időt, valamint a betanítási parancsfájl futtatásához szükséges időt veszi igénybe.

   [Azure ML-kísérlet indítása ![](./media/how-to-vscode-tools/start-golden-path.gif)](./media/how-to-vscode-tools/start-golden-path.gif#lightbox)

A kísérlet futtatása gombra kattintva válaszolja meg az utasításokat a következő módon:

1. Válassza ki az Azure-előfizetését.
1. Válassza az *új* Azure Machine learning munkaterület létrehozása lehetőséget.
1. Válassza ki az előre konfigurált sablonok készletét, hogy inicializálja a Python-környezetet a futtatásokhoz. A sablonok kiindulási pontot biztosítanak, és a következő beállításokat tartalmazzák:
    1. **PyTorch**, **TensorFlow**vagy **Scikit – Learn**
    1. **Egyszeri** vagy **elosztott** számítási képzés
    1. **Általános** egyéni környezetekhez
1. A sablonban nem szereplő csomagok hozzáadásával győződjön meg arról, hogy a pip és a Conda csomagok listája befejeződött a parancsfájlhoz.
1. Tekintse át a kísérlet futtatásához használt alapértelmezett neveket és specifikációkat, majd kattintson a **kísérlet elküldése** hivatkozásra a JSON-fájlban. A JSON-fájl nem lesz mentve, mert egyszerűen áttekintheti vagy módosíthatja a kísérlet beállításait a küldés előtt.
1. Dőljön hátra, és pihenjen, amíg a bővítmény beállítja az Ön számára, és futtatja a szkriptet!

    [![betanítás a felhőben](./media/how-to-vscode-tools/run-golden-path.gif)](./media/how-to-vscode-tools/run-golden-path.gif#lightbox)

Néhány másodpercen belül értesítést kap arról, hogy a kísérlet el lett küldve az Azure-ba, amelyen megtekintheti az előrehaladását a Azure Machine Learning Studióban. ehhez kattintson a **kísérlet futtatásának megtekintése** HIVATKOZÁSRA a vs Code-értesítésben, vagy a vs code-on belül a frissítés gombra az Azure lapon.

Jelenleg a futtatási mérőszámok megtekintése csak a Studióban támogatott. A fenti, a **kísérlet futtatására** szolgáló hivatkozás megtekintése hivatkozásra kattintva megtekintheti a naplókat, amelyeken a naplózott mérőszámok jelennek meg.
[![kísérlet futtatása a portálon](./media/how-to-vscode-tools/experiment-run-on-portal.PNG)](./media/how-to-vscode-tools/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>Részletes Azure Machine Learning a VS Code-ban

A fenti forgatókönyvben a legegyszerűbb útvonalat követve elküldte a kísérletet. Előfordulhat, hogy észrevette, hogy a bővítmény a kísérlet futtatásához szükséges lépéseket lekicsinyíti. Ebben a szakaszban azt mutatjuk be, hogyan kezelheti egyenként a Azure Machine Learning fogalmakat, így maximálisan vezérelheti a vezérlést.

Mielőtt elkezdi a gépi tanulási modellek a Visual Studio Code-ban való üzembe helyezését, létre kell hoznia egy [Azure Machine learning munkaterületet](concept-workspace.md) a felhőben. Ez a munkaterület fogja tartalmazni a modelleket és az erőforrásokat.

### <a name="create-a-workspace"></a>Munkaterületek létrehozása

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont. Megjelenik a Azure Machine Learning oldalsáv.

    [Munkaterület létrehozása ![](./media/how-to-vscode-tools/create-workspace.gif)](./media/how-to-vscode-tools/create-workspace.gif#lightbox)


1. Kattintson a jobb gombbal az Azure-előfizetésre, és válassza a **Munkaterület létrehozása**lehetőséget. Alapértelmezés szerint a létrehozás dátumát és időpontját tartalmazó név jön létre. Módosítsa a nevet **TeamWorkspace** , majd nyomja le az ENTER billentyűt.

1. Válasszon ki egy erőforráscsoportot a listából, ha tudja, hogy melyiket szeretné kiválasztani, vagy újat létrehozni. Ha újat hoz létre, válasszon egy helyet, amely a modell üzembe helyezéséhez legközelebb eső helyen található. Ebben az esetben az **USA 2. nyugati**régióját választottuk.

1. Az ENTER billentyű lenyomása után Azure Machine Learning megkapja a munkaterület létrehozásához szükséges kérést. Értesítést kap a Visual Studio Code értesítési területének folyamatáról.

1. Bontsa ki az előfizetés csomópontot az újonnan létrehozott munkaterület megkereséséhez.

### <a name="create-an-experiment"></a>Kísérlet létrehozása
Egy vagy több kísérlet hozható létre a munkaterületen, és nyomon követheti és elemezheti az egyes modellek betanítási futtatásait. A futtatások az Azure-felhőben vagy a helyi gépen is elvégezhető.

1. Bontsa ki a **TeamWorkspace** -munkaterületet. Kattintson a jobb gombbal a **kísérletek** csomópontra, majd válassza a **kísérlet létrehozása** elemet a helyi menüből.

1. A parancssorban adja meg a kísérlet nevét. A példa képernyőképeken a kísérlet neve **MNIST**.

1. Az új kísérlet létrehozásához kattintson az ENTER gombra. Az új kísérlet a fastruktúrában jelenik meg, mint a **kísérletek** csomópont gyermeke.

1. Egy munkaterületen kattintson a jobb gombbal egy kísérletre, és állítsa be **aktív** kísérletként. A felhőben kísérletező **aktív** kísérletek a Visual Studio Code-ban jelenleg megnyitott mappába. Ennek a mappának tartalmaznia kell a helyi Python-parancsfájlokat. Egy aktív kísérlet beállításával az összes betanítási művelethez tartozó fő mérőszámok a kísérletben tárolódnak, függetlenül attól, hogy hol vannak végrehajtva.

    [![kísérlet létrehozása](./media/how-to-vscode-tools/create-experiment.gif)](./media/how-to-vscode-tools/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>Számítási célok létrehozása és kezelése

A Visual Studio Code Azure Machine Learning segítségével előkészítheti az adatait, betaníthatja a modelleket, és helyileg és távoli számítási célokon is üzembe helyezheti azokat.

A bővítmény több távoli számítási célt is támogat Azure Machine Learning számára. További információkért tekintse meg a Azure Machine Learning által támogatott [számítási célok](how-to-set-up-training-targets.md)teljes listáját.

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Számítási célok létrehozása Azure Machine Learninghoz a Visual Studio Code-ban

Számítási cél létrehozása:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont. Megjelenik a Azure Machine Learning oldalsáv.

1. A fanézetben bontsa ki az Azure-előfizetését és Azure Machine Learning munkaterületét.

1. A munkaterület csomópontban kattintson a jobb gombbal a **számítási** csomópontra, majd válassza a **számítás létrehozása**lehetőséget.

1. Válassza ki a számítási cél típusát a listából.

1. A parancssorban válassza ki a virtuális gép méretét. A számításokat szöveggel szűrheti, például "GPU".

1. A parancssorba írja be a számítási cél nevét.

1. A név megadása után a rendszer az alapértelmezett paraméterek használatával hozza létre a számítást. A paraméterek módosításához kattintson a jobb gombbal az új számításra, majd válassza a **számítás szerkesztése**lehetőséget.

1. A megjelenő JSON-ban hajtsa végre a kívánt módosításokat, majd kattintson a "Mentés és folytatás" Codelensben (a billentyűzettel a **CTRL** billentyűt lenyomva megnyithatja a parancssort, és futtathatja az **Azure ml: mentés és folytatás** parancsot)

Íme egy példa arra, hogyan hozhat létre és szerkeszthet egy Azure Machine Learning számítást (AMLCompute):

[![a pénzmosás-számítás létrehozása a Visual Studio Code-ban](./media/how-to-vscode-tools/create-remote-compute.gif)](./media/how-to-vscode-tools/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>A konfigurációs fájl futtatása

Egy Azure Machine Learning kísérlet számítási feladatokhoz való futtatásához a számítást megfelelően kell konfigurálni. A futtatási konfigurációs fájl az a mechanizmus, amellyel ez a környezet meg van adva.

Íme egy példa arra, hogyan hozhat létre egy futtatási konfigurációt a fent létrehozott AmlCompute.

[![futtatási konfiguráció létrehozása a számítási feladatokhoz](./media/how-to-vscode-tools/create-runconfig.gif)](./media/how-to-vscode-tools/create-runconfig.gif#lightbox)

Az Azure ML-kísérletek a helyi gépen való futtatásához a futtatási konfigurációs fájlra továbbra is szükség van. Helyi futtatási konfiguráció létrehozásakor a használt Python-környezet alapértelmezés szerint a VS Code-ban beállított tolmács elérési útját fogja használni.

### <a name="train-and-tune-models"></a>Modellek betanítása és finomhangolása

A VS Code-hoz készült Azure ML bővítménnyel több módon is futtathatja a betanítási szkripteket egy kísérletben.

1. Kattintson a jobb gombbal a betanítási szkriptre, és válassza az **Azure ml: Futtatás mint kísérlet az Azure-ban lehetőséget.**
1. Kattintson a kísérlet futtatása eszköztár ikonra.
1. Kattintson a jobb gombbal a futtatási konfiguráció csomópontra.
1. A VS Code parancs paletta használatával hajtsa végre az **Azure ml: kísérlet futtatása**

Azure Machine Learning kísérlet futtatása:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont.

1. A fanézetben bontsa ki az Azure-előfizetését és Azure Machine Learning munkaterületét.

1. A munkaterület csomópont alatt bontsa ki a **kísérletek** csomópontot, majd kattintson a jobb gombbal a futtatni kívánt kísérletre.

1. Válassza a **kísérlet futtatása**lehetőséget.

1. Válassza ki annak a Python-fájlnak a nevét, amelyet futtatni szeretne a modell betanításához, majd nyomja le az ENTER billentyűt a Futtatás elküldéséhez. Megjegyzés: a kiválasztott fájlnak a VS Code-ban jelenleg megnyitott mappában kell lennie.

1. Miután elküldte a futtatást, egy **futtatási csomópont** fog megjelenni a választott kísérlet alatt. Ezzel a csomóponttal figyelheti a futtatások állapotát. Megjegyzés: Előfordulhat, hogy a legutóbbi állapot megjelenítéséhez időnként frissítenie kell az ablakot.

Íme egy példa arra, hogyan futtathat kísérletet a korábban létrehozott számítási feladatokhoz:

[![kísérlet helyi futtatása](./media/how-to-vscode-tools/run-experiment.gif)](./media/how-to-vscode-tools/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>Modellek üzembe helyezése és kezelése
Azure Machine Learning a gépi tanulási modellek a felhőben és a peremen is üzembe helyezhetők és kezelhetők.

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>A modell regisztrálása Azure Machine Learning a Visual Studio Code-ból

Most, hogy betanítta a modellt, regisztrálhatja azt a munkaterületen. A regisztrált modelleket nyomon követheti és üzembe helyezheti.

A modell regisztrálása:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont. Megjelenik a Azure Machine Learning oldalsáv.

1. A fanézetben bontsa ki az Azure-előfizetését és Azure Machine Learning munkaterületét.

1. A munkaterület csomópontban kattintson a jobb gombbal a **modellek** elemre, és válassza a **modell regisztrálása**lehetőséget.

1. A parancs paletta mezőjében adja meg a modell nevét.

1. A listából válassza ki, hogy fel kívánja-e tölteni egy **modellt** (egyetlen modell esetében) vagy egy **modell mappát** (több fájlból álló modelleknél, például TensorFlow).

1. Válassza ki a mappát vagy a fájlt.

1. Amikor befejezte a modell tulajdonságainak konfigurálását, az ablak jobb alsó sarkában válassza a **Küldés**lehetőséget.

Az alábbi példa bemutatja, hogyan regisztrálhat modelleket Azure Machine Learningba:

[Modell regisztrálása a pénzmosás ![](./media/how-to-vscode-tools/register-model.gif)](./media/how-to-vscode-tools/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>A szolgáltatás üzembe helyezése a Visual Studio Code-ból

A Visual Studio Code-ban a webszolgáltatását a következőre helyezheti üzembe:
+ Azure Container Instances (ACI) teszteléshez.
+ Azure Kubernetes szolgáltatás (ak) éles környezetben.

A teszteléshez nem kell ACI-tárolót létrehoznia, mert az ACI-tárolók szükség szerint jönnek létre. Azonban az AK-fürtöket előre kell konfigurálnia. További információ: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

Webszolgáltatás üzembe helyezése:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont. Megjelenik a Azure Machine Learning oldalsáv.

1. A fanézetben bontsa ki az Azure-előfizetését és a Azure Machine Learning munkaterületét.

1. A munkaterület csomópont alatt bontsa ki a **modellek** csomópontot.

1. Kattintson a jobb gombbal a telepíteni kívánt modellre, majd válassza a **szolgáltatás telepítése a regisztrált modellből** lehetőséget a helyi menüből.

1. A parancs palettáján válassza ki azt a számítási célt, amelyet telepíteni kíván.

1. A parancs paletta mezőjében adja meg a szolgáltatás nevét.

1. A parancs palettáján válassza ki az ENTER billentyűt a billentyűzeten a tallózáshoz, és válassza ki a parancsfájlt.

1. A parancs palettáján válassza ki az ENTER billentyűt a billentyűzeten a tallózáshoz, és válassza ki a Conda függőségi fájlt.

1. Amikor befejezte a szolgáltatás tulajdonságainak konfigurálását, az ablak jobb alsó sarkában válassza a Submit ( **Küldés** ) lehetőséget a telepítéshez. A szolgáltatás tulajdonságai fájlban megadhat egy helyi Docker-fájlt vagy egy Schema. JSON fájlt.

A webszolgáltatás már telepítve van.

Íme egy példa arra, hogyan helyezhet üzembe egy webszolgáltatást:

[webszolgáltatás üzembe helyezése ![](./media/how-to-vscode-tools/create-image.gif)](./media/how-to-vscode-tools/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>Kísérletezés további funkciókkal

A Command paletta a Visual Studio Code számos Azure Machine Learning funkciójának elérésére használható. A parancs paletta típusának meghívásához nyomja le a CTRL + SHIFT + P billentyűkombinációt. Itt megkeresheti a bővítmény további Azure ML-funkcióit.

[a Visual Studio Code-hoz készült Azure Machine Learning billentyűparancsai ![](./media/how-to-vscode-tools/commands.gif)](./media/how-to-vscode-tools/commands.gif#lightbox)

## <a name="next-steps"></a>Következő lépések

* A Visual Studio code-on kívüli Azure Machine Learning betanításával kapcsolatos útmutatóért lásd [: oktatóanyag: modellek betanítása a Azure Machine learning](tutorial-train-models-with-aml.md)
* A kód helyi szerkesztésével, futtatásával és hibakeresésével kapcsolatos útmutatóért lásd a [Python Hello-World oktatóanyagot](https://code.visualstudio.com/docs/Python/Python-tutorial)
* A Python-környezet testreszabásával kapcsolatos további információkért lásd: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md)

