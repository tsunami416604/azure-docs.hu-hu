---
title: Jupyter-jegyzetfüzet-projekt exportálása a Azure Notebooks előzetes verzióból
description: Jupyter-jegyzetfüzet-projekt gyors exportálása.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: 31e32a6ebb39429078c7c6747bc6b10f177699ac
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802700"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>Gyors útmutató: Jupyter notebook-projekt exportálása Azure Notebooks előzetes verzióban

Ebben a rövid útmutatóban egy Azure Notebooks projektet fog letölteni más Jupyter notebook-megoldásokban való használatra. 

## <a name="prerequisites"></a>Előfeltételek
Egy meglévő Azure Notebooks-projekt.

## <a name="export-an-azure-notebooks-project"></a>Azure Notebooks projekt exportálása

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksra](quickstart-sign-in-azure-notebooks.md).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. Válasszon ki egy projektet.
1. Kattintson a letöltés gombra az összes projektfájlt tartalmazó Zip-fájl letöltésének elindításához.
1. Azt is megteheti, hogy egy adott projekt lapon a "projekt letöltése" gombra kattintva letölti az adott projekt összes fájlját.

A projektfájlok letöltése után más Jupyter Notebook-megoldásokkal is használhatja azokat. Az alábbi szakaszokban ismertetett néhány lehetőség az alábbiakat tartalmazza: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="download-the-requirements-file-used-by-azure-notebooks"></a>Az Azure Notebooks által használt követelmények fájl letöltése

Ha olyan környezetet szeretne létrehozni, amely megfelel a Azure Notebooks előzetes verziójának, használhatja a GitHubon elérhető **AzureNotebooksRequirements.txt** fájlt.

1. Navigáljon a Azure Notebooks [GitHub-adattárhoz](https://github.com/microsoft/AzureNotebooks) , vagy [közvetlenül is hozzáférhet a fájlhoz](https://aka.ms/aznbrequirementstxt).
1. Töltse le a Azure Notebooks-környezethez tartozó csomagok függőségeit tartalmazó **AzureNotebooksRequirements.txt** fájlt. 
1. A parancssorban navigáljon a projektekhez használni kívánt könyvtárra, és használja a követelmények fájlt a megfelelő csomagok telepítéséhez.

    > [!Note]
    > Habár nem szükséges, általában létre kell hoznia egy új virtuális környezetet a csomagok telepítéséhez az alább látható módon.

    ```bash
    cd your-project-dir
    python -m venv .venv
    python -m pip install -r AzureNotebooksRequirements.txt
    ```

További információ a követelmények fájljairól: [pip docs]( https://pip.pypa.io/en/stable/user_guide/#requirements-files).

## <a name="use-notebooks-in-visual-studio-code"></a>Jegyzetfüzetek használata a Visual Studio Code-ban

A [vs Code](https://code.visualstudio.com/) egy ingyenes Kódszerkesztő, amelyet helyileg használhat, vagy távoli számítási feladatokhoz kapcsolódhat. A Python bővítménnyel együtt teljes körű környezetet kínál a Python-fejlesztéshez, beleértve a Jupyter notebookok használatának gazdag natív élményét. 

![VS Code Jupyter Notebook támogatás](media/vs-code-jupyter-notebook.png)

A projektfájlok [letöltése](#export-an-azure-notebooks-project) után a vs Code használatával használhatja őket. A VS Code és a Jupyter notebookok használatával kapcsolatos útmutatásért lásd: [Jupyter notebookok használata a Visual Studio Code-ban](https://code.visualstudio.com/docs/python/jupyter-support) és [adatelemzésben a Visual Studio Code-](https://code.visualstudio.com/docs/python/data-science-tutorial) oktatóanyagokban.

A Visual Studio Code [Azure Notebooks követelményeivel](#download-the-requirements-file-used-by-azure-notebooks) is felhasználhatja a Azure Notebooks előzetes verziójával egyező környezetet.

## <a name="use-notebooks-in-visual-studio-codespaces"></a>Jegyzetfüzetek használata a Visual Studio Codespaces

A Visual Studio Codespaces olyan felhőben üzemeltetett környezeteket biztosít, ahol a jegyzetfüzeteket a Visual Studio Code vagy a webböngésző használatával szerkesztheti. Ugyanazzal a nagyszerű Jupyter-élménysel rendelkezik, mint a VS Code, de nem kell semmit telepítenie az eszközre. Ha nem szeretné beállítani a helyi környezetet, és inkább egy felhőalapú megoldást szeretne használni, akkor a codespace létrehozása nagyszerű lehetőség. Első lépések:

1. [Töltse le](#export-an-azure-notebooks-project) a Project-fájlokat.
1. [Hozzon létre egy GitHub-tárházat](https://help.github.com/github/getting-started-with-github/create-a-repo) a jegyzetfüzetek tárolásához. 
1. [Adja hozzá a fájljait](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) a tárházhoz.
1. A Visual Studio Codespaces beállítása a [böngészőben](https://docs.microsoft.com/visualstudio/online/how-to/browser), a [Visual Studióban](https://docs.microsoft.com/visualstudio/online/how-to/vside)vagy a [Visual Studio Code](https://docs.microsoft.com/visualstudio/online/how-to/vscode)-ban.

## <a name="use-notebooks-with-azure-machine-learning"></a>Jegyzetfüzetek használata Azure Machine Learning

A Azure Machine Learning teljes gépi tanulási platformot biztosít, amely lehetővé teszi a felhasználók számára, hogy gyorsabban építsenek és helyezzen üzembe modelleket az Azure-ban. Az Azure ML lehetővé teszi, hogy Jupyter jegyzetfüzeteket futtasson egy virtuális gépen vagy egy megosztott fürtözött számítástechnikai környezetben. Ha felhőalapú megoldásra van szüksége az ML-munkaterheléshez a kísérlet követésével, az adatkészletek kezelésével és egyebekkel kapcsolatban, javasoljuk, hogy Azure Machine Learning. Ismerkedés az Azure ML-vel:

1. [Töltse le](#export-an-azure-notebooks-project) a Project-fájlokat.
1. [Hozzon létre egy munkaterületet](../machine-learning/how-to-manage-workspace.md) a Azure Portalban.

   ![Munkaterület létrehozása](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Nyissa meg az [Azure studiót (előzetes verzió)](https://ml.azure.com/).
1. A bal oldali navigációs sáv használatával válassza a **jegyzetfüzetek**lehetőséget.
1. Kattintson a **fájlok feltöltése** gombra, és töltse fel a Azure Notebooksból letöltött projektfájlok fájljait.

Az Azure ML-vel és a Jupyter-jegyzetfüzetek futtatásával kapcsolatos további információkért tekintse át a [dokumentációt](../machine-learning/how-to-run-jupyter-notebooks.md) , vagy próbálja ki [Machine Learning](https://docs.microsoft.com/learn/modules/intro-to-azure-machine-learning-service/) modult Microsoft Learn.


## <a name="use-azure-lab-services"></a>Azure Lab Services használata

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/) lehetővé teszi a pedagógusok számára, hogy az előre konfigurált virtuális gépeket egy teljes osztályterem számára könnyedén beállítsák és igény szerint férhessenek hozzá. Ha a Jupyter-jegyzetfüzetekkel és a Felhőbeli számításokkal együtt szeretné használni a testreszabott tantermi környezetben, a labor Services nagyszerű megoldás.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 A projektfájlok [letöltése](#export-an-azure-notebooks-project) után Azure Lab Services használhatja őket. A laborok beállításával kapcsolatos útmutatásért lásd: [tesztkörnyezet beállítása az adatelemzés megtanításához a Python és a Jupyter notebook használatával](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>A GitHub használata

A GitHub lehetővé teszi a jegyzetfüzetek (és más fájlok) tárolását, a notebookok megosztását és másokkal való együttműködését. Ha a projektek megosztását és másokkal való együttműködését keresi, a GitHub nagyszerű megoldás, és a [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces) kombinálva kiváló fejlesztési élményt nyújt. Ismerkedés a GitHubtal

1. [Töltse le](#export-an-azure-notebooks-project) a Project-fájlokat.
1. [Hozzon létre egy GitHub-tárházat](https://help.github.com/github/getting-started-with-github/create-a-repo) a jegyzetfüzetek tárolásához. 
1. [Adja hozzá a fájljait](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) a tárházhoz.
