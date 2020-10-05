---
title: Jupyter-jegyzetfüzet klónozása a GitHubról Azure Notebooks előzetes verzióval
description: Gyorsan klónozott Jupyter notebookot egy GitHub-adattárból, és futtathatja a Azure Notebooks-fiókjában.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 267e79e7d4bf108ac3b2c72d64cee5a07ba638be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87424477"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Gyors útmutató: jegyzetfüzet klónozása Azure Notebooks előzetes verzióban

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ebben a rövid útmutatóban a GitHubban tárolt Jupyter-jegyzetfüzetet egy Azure Notebooks fiókba másolja. 

A GitHub-adattárak tároló-és verziókövetés biztosítanak a Jupyter-jegyzetfüzetekhez. A közreműködők megőrzik a Tárházak helyi példányait, és ezekből a másolatokból futtatják a jegyzetfüzeteket. A Jupyter-jegyzetfüzetnek a GitHubról a Azure Notebooks fiókba való klónozásával létrejön a jegyzetfüzet egy független másolata. A módosításokat csak a Azure Notebooks fiókjában tárolja a rendszer, és nincs hatással az eredeti GitHub-tárházra. 

Mivel a Azure Notebooks klón a felhőben van, megoszthatja azokat közreműködők használatával, akiknek nem kell helyi másolatot készíteniük vagy Jupyter telepíteniük a számítógépükre. Azt is megteheti, hogy egy jegyzetfüzetet egyszerűen egy saját projekt kiindulási pontjaként vagy adatfájlok beszerzéséhez használ. 

## <a name="prerequisites"></a>Előfeltételek
Nincsenek.

## <a name="clone-azure-cognitive-services-notebooks"></a>Azure Cognitive Services-jegyzetfüzetek klónozása

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksra](quickstart-sign-in-azure-notebooks.md).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon kattintson a felfelé mutató nyíl gombra (billentyűparancs: U; a gomb a GitHub-tárház **feltöltésekor** jelenik meg, ha a böngészőablak elég széles):

    ![GitHub-tárház-parancs feltöltése a saját projektek oldalon](media/quickstarts/upload-github-repo-command.png)

1. A megjelenő **GitHub-tárház feltöltése** lapon adja meg vagy állítsa be a következő adatokat, majd válassza az **Importálás**lehetőséget:

   - **GitHub-adattár**: Microsoft/kognitív-szolgáltatások – jegyzetfüzetek (ez a név a Jupyter notebookokat az Azure Cognitive Services-on [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) ).
   - **Rekurzív klónozás**: (törölve)
   - **Projekt neve**: Cognitive Services klón
   - **Projekt azonosítója**: kognitív – szolgáltatások – klón
   - **Nyilvános**: (törölve)

     ![A GitHub-tárház felugró ablakának feltöltése a tárház adatainak összegyűjtéséhez](media/quickstarts/upload-github-repo-popup.png)

1. Legyen türelmes, amíg a folyamat befejeződik; a tárház klónozása néhány percet is igénybe vehet.

1. A klónozás befejezése után Azure Notebooks viszi az új projektbe, ahol megtekintheti az összes fájl másolatát.

    :::image type="content" source="media/quickstarts/completed-clone.png" alt-text="Egy befejezett klón nézete." lightbox="media/quickstarts/completed-clone.png":::

## <a name="share-a-notebook"></a>Jegyzetfüzet megosztása

1. A klónozott projekt másolatának megosztásához használja a **megosztás** vezérlőelemet, vagy szerezzen be egy hivatkozást, szerezze be a HIVATKOZÁST tartalmazó HTML-vagy Markdown-kódot, vagy hozzon létre egy e-mail-üzenetet a következő hivatkozással:

    ![Projekt megosztási parancsa](media/quickstarts/share-project-command.png)

1. Mivel a projekt klónozásakor a **nyilvános** lehetőséget törölte, a klón privát. A másolás nyilvános létrehozásához válassza a **projekt beállításai**lehetőséget, állítsa be a **nyilvános projekt** beállítást a felugró ablakban, majd válassza a **Mentés**lehetőséget.

1. A futtatásához válasszon ki egy jegyzetfüzetet a projektben. Az Azure Cognitive Services adattárában található minden jegyzetfüzet, például a saját önálló gyors útmutatója. Az alábbi képen a BingImageSearchAPI notebook használatának eredménye látható, miután hozzáadtak egy Cognitive Services API-előfizetési kulcsot, és megváltoztatták a "kölykök" keresési kifejezést a "nyuszik" kifejezésre:

    ![A GitHubról klónozott Jupyter notebook futtatása](media/quickstarts/clone-notebook-result.png)

1. Ha végzett a jegyzetfüzet futtatásával **, válassza**  >  a**Bezárás és a leállítása** lehetőséget a jegyzetfüzet és a böngészőablak bezárásához.

1. Ha egy egyéni jegyzetfüzetet szeretne megosztani a projektben, kattintson a jobb gombbal a jegyzetfüzetre, és válassza a **hivatkozás másolása** lehetőséget (billentyűparancs: y):

    ![Helyi menü parancs egy különálló jegyzetfüzetre mutató hivatkozás másolásához](media/quickstarts/copy-link-to-individual-notebook.png)

1. A jegyzetfüzetek kivételével a fájlok szerkesztéséhez kattintson a jobb gombbal a fájlra a projektben, és válassza a **fájl szerkesztése** (billentyűparancs: i) elemet. Az alapértelmezett művelet, **Futtatás** (billentyűparancs: r), csak a fájl tartalmát jeleníti meg, és nem teszi lehetővé a szerkesztést.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet létrehozása és futtatása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
