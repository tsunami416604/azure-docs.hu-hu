---
title: A Visual Studio Code használata
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan telepítse az Azure Machine Learning a Visual Studio Code és a egy egyszerű kísérlet létrehozása az Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 898fd7d0416d1e1b27a25cabf524e72a7de44fde
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269590"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>A Visual Studio Code az Azure Machine Learning használatának első lépései

Ebből a cikkből elsajátíthatja, hogyan telepítheti a **a Visual Studio Code az Azure Machine Learning** bővítményt, és az első kísérlet létrehozása az Azure Machine Learning szolgáltatás a Visual Studio Code (a VS Code).

A Visual Studio code-ban az Azure Machine Learning-bővítmény használatával az Azure Machine Learning szolgáltatás használatával előkészítési adataihoz, tanítási és tesztelési gépi tanulási modelleket a helyi és távoli számítási célnak, ezek a modellek üzembe helyezése és nyomon követheti az egyéni metrikákat és kísérletek.

## <a name="prerequisite"></a>Előfeltétel


+ Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

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

1. A bővítmény a VS Code-ban megnyílik egy üdvözlő lapon, és az Azure szimbólum tevékenységsávon van adva.

   ![A Visual Studio Code tevékenységsávon Azure ikon](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Kattintson a párbeszédpanelen **bejelentkezés** , és kövesse a képernyőn használatával Azure-hitelesítésre. 
   
   Az Azure-fiók bővítményt, az Azure Machine Learning a VS Code-bővítmény együtt lett telepítve, amely segítséget nyújt az Azure-fiókkal hitelesítést. A parancsok listájának megtekintéséhez a [Azure-fiókkiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) lapot.

> [!Tip] 
> Tekintse meg a [IntelliCode bővítmény a VS Code (előzetes verzió)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode Python, az IntelliSense behívásakor az aktuális kód környezettől függően a leginkább releváns automatikus kiegészítése például AI támogatású képességeket biztosít.

## <a name="install-the-sdk"></a>Az SDK telepítése

1. Győződjön meg arról, hogy a Python 3.5-ös vagy újabb telepítve és a VS Code rozpoznána. Ha telepíti azt most, majd indítsa újra a VS Code, és válassza ki a egy Python-fordítóra található utasításokat követve https://code.visualstudio.com/docs/python/python-tutorial.

1. A VS Code-ban nyissa meg a Parancskatalógust **Ctrl + Shift + P**.

1. Írja be "az Azure ML SDK telepítése" a pip található az SDK telepítési parancs. Egy helyi, privát Python-környezetet jön létre, amely az Azure Machine Learning használatának előfeltételei a Visual Studio Code-ot.

   ![Pythonhoz készült Azure Machine Learning SDK telepítése](./media/vscode-tools-for-ai/install-sdk.png)

1. A beépített terminál ablakában adja meg a használandó Python-fordítóra vagy tudja érni **Enter** az alapértelmezett Python-fordítóra használatára.

   ![Válassza ki a értelmezője számára készült](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Ismerkedés az Azure Machine Learning szolgáltatással

Mielőtt elkezdené a képzés és a VS Code használata a machine learning-modellek üzembe helyezéséhez, létre kell egy [az Azure Machine Learning szolgáltatás munkaterületén](concept-azure-machine-learning-architecture.md#workspace) a felhőben is a modellek és -erőforrásokat tartalmaznak. Ismerje meg, hogyan hozzon létre egyet, és az első kísérlet létrehozása az adott munkaterületen.

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure Machine Learning oldalsáv jelenik meg.

   [![Az oldalsáv](./media/vscode-tools-for-ai/CreateNewWorkspace.gif)](./media/vscode-tools-for-ai/CreateNewWorkspace.gif#lightbox)

1. Kattintson a jobb gombbal az Azure-előfizetéshez, és válassza ki **munkaterület létrehozása**. Egy lista jelenik meg. Az animált képen az előfizetés neve "Ingyenes próbaverzió" pedig a munkaterület "TeamWorkspace". 

1. A listából válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a parancskatalógus a varázsló használatával.

1. A mezőbe írja be az új munkaterület egyedi, és törölje nevét. A képernyőképek a munkaterület neve "TeamWorkspace".

1. Az enter, és az új munkaterület létrehozása. Az előfizetés neve alatti fájában jelenik meg.

1. A kísérlet csomóponton kattintson a jobb gombbal, és válassza a **kísérlet létrehozása** a helyi menüből.  Kísérletek a futtatások Azure Machine Learning segítségével nyomon követheti.

1. A mezőben adjon nevet a kísérlet során. A képernyőképek a kísérlet neve "MNIST".
 
1. Az enter, és az új kísérlet jön létre. A munkaterület neve alatti fájában jelenik meg.

1. Kattintson a jobb gombbal a kísérlet neve, és válassza a **kísérlet mappát csatolni**. A mappának tartalmaznia kell a helyi Python-szkriptek. A mappa majd csatolva lesz a kísérlet a felhőben. 

   Most már minden, a kísérlet fut, is futtathatja a kísérletet, így a fő mérőszámok tárolja a kísérlet előzmények, és a modellek betanítása, automatikusan lekérése az Azure Machine Learning feltöltött és a kísérlet metrikákat és naplókat tárolja.

   [![Csatolhat egy mappát a VS Code-ban](./media/vscode-tools-for-ai/CreateNewExperimentandFolder.gif)](./media/vscode-tools-for-ai/CreateNewExperimentandFolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Billentyűparancsok használata

A VS Code többségét, mint az Azure Machine Learning szolgáltatások a VS Code-ban érhetők el, a billentyűzetről. Tudni, hogy a legfontosabb billentyűkombinációt a Ctrl + Shift + P, hogy megjelenítse a Parancskatalógus. Itt az összes VS Code, többek között a leggyakoribb műveletek billentyűparancsai funkciója elérhető lesz.

[![Az Azure Machine Learning a VS Code billentyűparancsai](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>További lépések

Mostantól használhatja a Visual Studio Code az Azure Machine Learning működjön.

Ismerje meg, hogyan [hozzon létre a számítási célokhoz, betanítását és üzembe helyezése a Visual Studio Code modellek](how-to-vscode-train-deploy.md).
