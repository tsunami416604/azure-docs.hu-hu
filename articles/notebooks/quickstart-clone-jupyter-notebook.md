---
title: Jupyter-jegyzetfüzet klónozása a GitHubról az Azure Notebook előzetes verzióval
description: Gyorsan klónozhat egy Jupyter-jegyzetfüzetet egy GitHub-tárházból, és futtathatja az Azure Notebook-fiókjában.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d0f3a12ff04e115074c3821c5e29652484710bca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064597"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Rövid útmutató: Jegyzetfüzet klónozása az Azure Notebookelőzetes verzióban

Ebben a rövid útmutatóban a GitHubon tárolt Jupyter-jegyzetfüzetet egy Azure Notebooks-fiókba másolja. 

A GitHub-adattárak tárolási és verzióvezérlést biztosítanak a Jupyter-jegyzetfüzetekhez. A közreműködők karbantartják az adattárak helyi példányait, és ezekből a másolatokból futtatják a jegyzetfüzeteket. Jupyter-jegyzetfüzetek klónozása a GitHubról az Azure Notebooks-fiókba a jegyzetfüzet független másolatát hozza létre. A módosítások csak az Azure Notebook-fiókban tárolódnak, és nincsenek hatással az eredeti GitHub-tárházra. 

Mivel az Azure Notebooks klóna a felhőben van, megoszthatja azt a közreműködőkkel, akiknek nem kell helyi másolatokat készíteniük, vagy a Jupyter telepítve van a számítógépükre. Előfordulhat, hogy a jegyzetfüzetet egyszerűen egy saját projekt kiindulópontjaként klónozhatja, vagy adatfájlokat szerezhet be. 

## <a name="prerequisites"></a>Előfeltételek
Nincs.

## <a name="clone-azure-cognitive-services-notebooks"></a>Azure Cognitive Services-jegyzetfüzetek klónozása

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Nyissa meg az [Azure-jegyzetfüzeteket,](https://notebooks.azure.com) és jelentkezzen be. További részletek: [Gyorsútmutató – Bejelentkezés az Azure Notebooks ba.](quickstart-sign-in-azure-notebooks.md)

1. A nyilvános profil lapon válassza a **Saját projektek** lehetőséget a lap tetején:

    ![A Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **Saját projektek** lapon jelölje ki a felfelé mutató nyílgombot (billentyűparancs: U; a gomb **GitHub-tárház feltöltéseként** jelenik meg, ha a böngészőablak elég széles):

    ![A GitHub-tárcsatorna feltöltése parancs a Saját projektek lapon](media/quickstarts/upload-github-repo-command.png)

1. A megjelenő **GitHub-tárházban** adja meg vagy állítsa be a következő részleteket, majd válassza az **Importálás**lehetőséget:

   - **GitHub-tárház**: Microsoft/cognitive-services-notebookok (ez a név klónozza a Jupyter notebookok az Azure Cognitive Services-on). [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)
   - **Klónozás rekurzívan:**(törölve)
   - **Projekt neve**: Cognitive Services Clone
   - **Projektazonosító**: kognitív-szolgáltatások-klón
   - **Nyilvános**: (törölve)

     ![A GitHub Repo felugró ablakának feltöltése a repo adatok gyűjtéséhez](media/quickstarts/upload-github-repo-popup.png)

1. Legyen türelmes, amíg a folyamat befejeződik; a tárház klónozása eltarthat néhány percig.

1. Miután a klónozás befejeződött, az Azure Notebooks az új projekthez vezeti, ahol megtekintheti az összes fájl másolatát.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Jegyzetfüzet megosztása

1. A klónozott projekt példányának megosztásához használja a **Megosztás** vezérlőt, szerezzen be egy hivatkozást, szerezze be a hivatkozást tartalmazó HTML- vagy Markdown-kódot, vagy hozzon létre egy e-mailt a hivatkozással:

    ![Projektmegosztás parancs](media/quickstarts/share-project-command.png)

1. Mivel a projekt klónozása kor törölte a **Nyilvános** lehetőséget, a klón magánjellegű. A másolat nyilvánossá választásához válassza a **Projektbeállítások**lehetőséget, adja meg a **Nyilvános projekt** beállítást az előugró ablakban, majd kattintson a **Mentés gombra.**

1. Jelöljön ki egy jegyzetfüzetet a projektben a futtatásához. Az Azure Cognitive Services-tárházban található jegyzetfüzetek például saját önálló rövid útmutató. Az alábbi képen a BingImageSearchAPI-jegyzetfüzet használatának eredményét mutatja be, miután hozzáadták a Cognitive Services API-előfizetési kulcsát, és a "kölykök" keresőkifejezést "nyuszikra" változtatták:

    ![A GitHubról klónozott Jupyter-jegyzetfüzet futtatása](media/quickstarts/clone-notebook-result.png)

1. Ha végzett a jegyzetfüzet futtatásával, válassza a > **Fájlbezárás és leállítás** lehetőséget a jegyzetfüzet és a böngészőablak bezárásához. **File**

1. Ha egy adott jegyzetfüzetet szeretne megosztani a projektben, kattintson a jobb gombbal a jegyzetfüzetre, és válassza a **Hivatkozás másolása parancsot** (billentyűparancs: y):

    ![Helyi menü parancs az egyes jegyzetfüzetre mutató hivatkozás másolásához](media/quickstarts/copy-link-to-individual-notebook.png)

1. A jegyzetfüzetektől eltérő fájlok szerkesztéséhez kattintson a jobb gombbal a projektben lévő fájlra, és válassza a **Fájl szerkesztése parancsot** (billentyűparancs: i). Az alapértelmezett művelet, a **Futtatás** (billentyűparancs: r) csak a fájl tartalmát jeleníti meg, és nem engedélyezi a szerkesztést.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet létrehozása és futtatása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
