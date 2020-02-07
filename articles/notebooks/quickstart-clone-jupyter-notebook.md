---
title: Jupyter-jegyzetfüzet klónozása a GitHubról Azure Notebooks előzetes verzióval
description: Gyorsan klónozza a GitHub-adattárból egy Jupyter notebookot, és futtathatja az Azure-jegyzetfüzet-fiókban.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d0f3a12ff04e115074c3821c5e29652484710bca
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064597"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Gyors útmutató: jegyzetfüzet klónozása Azure Notebooks előzetes verzióban

Ebben a rövid útmutatóban a GitHubban tárolt Jupyter-jegyzetfüzetet egy Azure Notebooks fiókba másolja. 

A GitHub-adattárak tároló-és verziókövetés biztosítanak a Jupyter-jegyzetfüzetekhez. A közreműködők megőrzik a Tárházak helyi példányait, és ezekből a másolatokból futtatják a jegyzetfüzeteket. A Jupyter-jegyzetfüzetnek a GitHubról a Azure Notebooks fiókba való klónozásával létrejön a jegyzetfüzet egy független másolata. A módosításokat csak a Azure Notebooks fiókjában tárolja a rendszer, és nincs hatással az eredeti GitHub-tárházra. 

Mivel a Azure Notebooks klón a felhőben van, megoszthatja azokat közreműködők használatával, akiknek nem kell helyi másolatot készíteniük vagy Jupyter telepíteniük a számítógépükre. Azt is megteheti, hogy egy jegyzetfüzetet egyszerűen egy saját projekt kiindulási pontjaként vagy adatfájlok beszerzéséhez használ. 

## <a name="prerequisites"></a>Előfeltételek
Nincs.

## <a name="clone-azure-cognitive-services-notebooks"></a>Az Azure Cognitive Services notebookok klónozása

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksra](quickstart-sign-in-azure-notebooks.md).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![A böngésző ablakának felső részén saját projektek hivatkozás](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon kattintson a felfelé mutató nyíl gombra (billentyűparancs: U; a gomb a GitHub-tárház **feltöltésekor** jelenik meg, ha a böngészőablak elég széles):

    ![GitHub-tárház-parancs feltöltése a saját projektek oldalon](media/quickstarts/upload-github-repo-command.png)

1. A megjelenő **GitHub-tárház feltöltése** lapon adja meg vagy állítsa be a következő adatokat, majd válassza az **Importálás**lehetőséget:

   - **GitHub-adattár**: Microsoft/kognitív-szolgáltatások – jegyzetfüzetek (ez a név a Jupyter notebookokat az Azure Cognitive Services [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Rekurzív klónozás**: (törölve)
   - **Projekt neve**: Cognitive Services klón
   - **Projekt azonosítója**: kognitív – szolgáltatások – klón
   - **Nyilvános**: (törölve)

     ![A GitHub-tárház felugró ablakának feltöltése a tárház adatainak összegyűjtéséhez](media/quickstarts/upload-github-repo-popup.png)

1. Türelemmel, amíg a folyamat befejeződése után; a tárház klónozása pár percet is igénybe vehet.

1. A Klónozás befejeződése után az Azure-jegyzetfüzetek megnyílik az új projekt, ahol megtekintheti az összes fájl másolatát.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Jegyzetfüzet megosztása

1. A klónozott projekt másolatának megosztásához használja a **megosztás** vezérlőelemet, vagy szerezzen be egy hivatkozást, szerezze be a HIVATKOZÁST tartalmazó HTML-vagy Markdown-kódot, vagy hozzon létre egy e-mail-üzenetet a következő hivatkozással:

    ![Projekt megosztás parancs](media/quickstarts/share-project-command.png)

1. Mivel a projekt klónozásakor a **nyilvános** lehetőséget törölte, a klón privát. A másolás nyilvános létrehozásához válassza a **projekt beállításai**lehetőséget, állítsa be a **nyilvános projekt** beállítást a felugró ablakban, majd válassza a **Mentés**lehetőséget.

1. Válassza ki egy jegyzetfüzetet a projektben való futtatáshoz. Minden egyes notebook az Azure Cognitive Services-tárházban, például a saját önálló rövid. Az alábbi képen láthatók a BingImageSearchAPI notebook használata után a Cognitive Services API-előfizetési kulcs hozzáadása és módosítása a keresési kifejezés "Kiskutyák", "bunnies" eredménye:

    ![A githubról klónozott futtatása a Jupyter notebook](media/quickstarts/clone-notebook-result.png)

1. Ha végzett a jegyzetfüzet futtatásával, válassza a **fájl** > **Bezárás és megállás** elemet a jegyzetfüzet és a böngésző ablakának bezárásához.

1. Ha egy egyéni jegyzetfüzetet szeretne megosztani a projektben, kattintson a jobb gombbal a jegyzetfüzetre, és válassza a **hivatkozás másolása** lehetőséget (billentyűparancs: y):

    ![Helyi menü parancsot az egyes notebook mutató hivatkozás másolása](media/quickstarts/copy-link-to-individual-notebook.png)

1. A jegyzetfüzetek kivételével a fájlok szerkesztéséhez kattintson a jobb gombbal a fájlra a projektben, és válassza a **fájl szerkesztése** (billentyűparancs: i) elemet. Az alapértelmezett művelet, **Futtatás** (billentyűparancs: r), csak a fájl tartalmát jeleníti meg, és nem teszi lehetővé a szerkesztést.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet létrehozása és futtatása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
