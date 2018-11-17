---
title: Az Azure Machine Learning AI-bővítmény a Visual Studio Code Tools használatával
description: További információ a mesterséges Intelligencia és indítsa el a képzés és gépi tanulási és deep learning-modellek Azure Machine Learning szolgáltatás a VS Code telepítése a Visual Studio Code Tools.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 57fe511e5de0d73f2a372da0ecab3e9a3039b194
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854203"
---
# <a name="vs-code-tools-for-ai-get-started-with-azure-machine-learning-from-visual-studio-code"></a>A VS Code Tools for AI: Ismerkedés az Azure Machine Learning Visual Studio Code-ból

Ebben a cikkben megismerkedhet a Visual Studio Code (a VS Code) bővítmény, **Tools for AI**, és indítsa el a betanítási, és üzembe helyezése a gépi tanulási és deep learning-modellek a VS Code-ban az Azure Machine Learning szolgáltatással.

Használja a Tools for AI-bővítmény a Visual Studio code-ban az Azure Machine Learning szolgáltatás használatával előkészítési adataihoz, tanítási és tesztelési gépi Tanulási modellek, helyi és távoli számítási céljainak, ezek a modellek üzembe helyezése, és nyomon követheti az egyéni metrikákat és kísérletek.

## <a name="prerequisite"></a>Előfeltétel

+ A Visual Studio Code telepítve kell lennie. A VS Code egy könnyen használható, de hatékony nyílt forráskódú Kódszerkesztő, amely asztali számítógépeken fut. Ez tartalmaz beépített támogatást nyújt a Python és más.  [Ismerje meg, hogyan telepítheti a VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Telepítse a Python 3.5-ös vagy újabb](https://www.anaconda.com/download/).

+ Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLfree) a virtuális gép létrehozásának megkezdése előtt.

## <a name="install-vs-code-tools-for-ai-extension"></a>Mesterséges Intelligencia kiterjesztése a VS Code Tools telepítése

Amikor telepíti a **Tools for AI** bővítményt, két további bővítmények automatikusan telepítve (ha az internet-hozzáféréssel rendelkezik). Azok a [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítmény és a [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) bővítmény

Dolgozunk az Azure Machine Learning, a VS Code alakítsa át a Python ide-vel kell. Használata [Python, a Visual Studio Code](https://code.visualstudio.com/docs/languages/python), a Microsoft Python bővítményét, lekérdezi az telepített Tools for AI automatikusan igényel. A bővítmény lehetővé teszi a VS Code kiváló IDE, és a Python interprety különböző bármely operációs rendszeren működik. A modul összes VS Code teljesítményt biztosít az automatikus kiegészítés és az IntelliSense, linting, hibakeresést és egységtesztelés együtt könnyedén váltani a Python-környezetek, például a virtuális és a conda-környezetek között. Tekintse meg a szerkesztési, ebben az útmutatóban fut, és hibakeresés Python-kód, tekintse meg a [Python Hello World oktatóanyag](https://code.visualstudio.com/docs/python/python-tutorial)

**A Tools for AI-bővítmény telepítése:**

1. Indítsa el a VS Code.

1. Egy böngészőben keresse fel: https://aka.ms/vscodetoolsforai. 

1. A weblapon kattintson **telepítése**. 

1. A bővítmény lapon kattintson a **telepítése**.

1. A bővítmény a VS Code-ban megnyílik egy üdvözlő lapon, és az Azure szimbólum tevékenységsávon van adva.

   ![A Visual Studio Code tevékenységsávon Azure ikon](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Kattintson a párbeszédpanelen **bejelentkezés** , és kövesse a képernyőn használatával Azure-hitelesítésre. 
   
   Az Azure-fiók bővítményt, telepítését és a VS Code Tools for AI, amely segít az Azure-fiókkal hitelesítést. A parancsok listájának megtekintéséhez a [Azure-fiókkiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) lapot.

> [!Tip] 
> Tekintse meg a [IntelliCode bővítmény a VS Code (előzetes verzió)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode Python, az IntelliSense behívásakor az aktuális kód környezettől függően a leginkább releváns automatikus kiegészítése például AI támogatású képességeket biztosít.

## <a name="install-the-sdk"></a>Az SDK telepítése

1. Győződjön meg arról, hogy a Python 3.5-ös vagy újabb telepítve és a VS Code rozpoznána. Ha telepíti azt most, majd indítsa újra a VS Code, és válassza ki a egy Python-fordítóra található utasításokat követve https://code.visualstudio.com/docs/python/python-tutorial.

1. A VS Code-ban nyissa meg a parancskatalógust **Ctrl + Shift + P**.

1. Írja be "az Azure ML SDK telepítése" a pip található az SDK telepítési parancs. Egy helyi, privát Python-környezetet jön létre, amely az Azure Machine Learning használatának előfeltételei a Visual Studio Code-ot.
   ![Telepítése](./media/vscode-tools-for-ai/install-sdk.png)

1. A beépített terminál ablakában adja meg a használandó Python-fordítóra vagy tudja érni **Enter** az alapértelmezett Python-fordítóra használatára.

   ![Telepítése](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Ismerkedés az Azure Machine Learning szolgáltatással

Mielőtt elkezdené a képzés és a VS Code használatával gépi Tanulási modellek üzembe helyezéséhez, létre kell egy [Azure Machine Learning szolgáltatás munkaterület](concept-azure-machine-learning-architecture.md#workspace) a felhőben is a modellek és -erőforrásokat tartalmaznak. Ismerje meg, hogyan hozzon létre egyet, és az első kísérlet létrehozása az adott munkaterületen.

1. Kattintson az Azure ikon a Visual Studio Code tevékenységsávon. Az Azure: A Machine Learning oldalsáv jelenik meg.

   ![Telepítése](./media/vscode-tools-for-ai/createworkspace.gif)

1. Kattintson a jobb gombbal az Azure-előfizetéshez, és válassza ki **munkaterület létrehozása**. Egy lista jelenik meg. Az animált képen az előfizetés neve "OpenMind Studio" pedig a munkaterület "MyWorkspace". 

1. A listából válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a parancskatalógus a varázsló használatával.

1. A mezőbe írja be az új munkaterület egyedi, és törölje nevét. A képernyőképek a munkaterület neve "MyWorkspace".

1. Az enter, és az új munkaterület létrehozása. Az előfizetés neve alatti fájában jelenik meg.

1. Kattintson a jobb gombbal a munkaterület nevét, és válassza a **kísérlet létrehozása** a helyi menüből.  Kísérletek a futtatások Azure Machine Learning segítségével nyomon követheti.

1. A mezőben adjon nevet a kísérlet során. A képernyőképek a kísérlet neve "MNIST".
 
1. Az enter, és az új kísérlet jön létre. A munkaterület neve alatti fájában jelenik meg.

1. Kattintson a jobb gombbal a kísérlet neve, és válassza a **csatolása egy helyi mappába**. A mappának tartalmaznia kell a helyi Python-szkriptek. A mappa majd csatolva lesz a kísérlet a felhőben. 

   Most már minden, a kísérlet fut, is futtathatja a kísérletet, így a fő mérőszámok tárolja a kísérlet előzmények, és a modellek betanítása, automatikusan lekérése az Azure Machine Learning feltöltött és a kísérlet metrikákat és naplókat tárolja.

   [![Csatolhat egy mappát a VS Code-ban](./media/vscode-tools-for-ai/attachfolder.gif)](./media/vscode-tools-for-ai/attachfolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Billentyűparancsok használata

A VS Code többségét, mint az Azure Machine Learning szolgáltatások a VS Code-ban érhetők el, a billentyűzetről. Tudni, hogy a legfontosabb billentyűkombinációt a Ctrl + Shift + P, hogy megjelenítse a Parancskatalógus. Itt az összes VS Code, többek között a leggyakoribb műveletek billentyűparancsai funkciója elérhető lesz.

[![A VS Code Tools for AI billentyűparancsai](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>További lépések

Mostantól használhatja a Visual Studio Code az Azure Machine Learning működjön.

Ismerje meg, hogyan [hozzon létre a számítási célokhoz, betanítását és üzembe helyezése a Visual Studio Code modellek](how-to-vscode-train-deploy.md).