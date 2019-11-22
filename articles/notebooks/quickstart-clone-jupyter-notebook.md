---
title: Klónozza a GitHub-Azure-jegyzetfüzetek használata Jupyter notebook
description: Gyorsan klónozza a GitHub-adattárból egy Jupyter notebookot, és futtathatja az Azure-jegyzetfüzet-fiókban.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 500bd3f85409bb2f5e7b73be0478694695cc9c00
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277523"
---
# <a name="quickstart-clone-a-notebook"></a>Gyors útmutató: Egy jegyzetfüzetet klónozása

Számos adatelemzők és fejlesztők tárolja a jegyzetfüzetekre a [GitHub-adattárak](https://github.com), egy ingyenes szolgáltatás, amely számos különböző projekttípusok tárolási és verziókövetési. GitHub gyakran használják, pedig a helyileg futó Jupyter notebookok dolgoztunk. Ezekben az esetekben minden közreműködő fenntartja az adattár helyi példányának, és futtatja a notebookok a másolatot.

A Klónozás másolatot készít egy GitHub-jegyzetfüzetet az Azure-jegyzetfüzetek fiókban helyette. Ezt klón független az eredeti adattárból; módosítások csak az Azure-jegyzetfüzetek fiókban vannak tárolva, és az eredeti nem befolyásolják. Mivel a Klónozási a felhőben, akik nem kell minden olyan helyi másolatkészítésre vagy is, hogy a saját számítógépükre Jupyter más közreműködőkkel együtt megoszthatja a projekt. A notebook klónozhatja is előfordulhat, hogy egyszerűen kiindulási pontként egy projektet a saját vagy adatfájlok beszerzése.

## <a name="clone-azure-cognitive-services-notebooks"></a>Az Azure Cognitive Services notebookok klónozása

1. Lépjen a [Azure notebookok](https://notebooks.azure.com) , és jelentkezzen be. (További információkért lásd: [rövid útmutató – jelentkezzen be Azure-jegyzetfüzetek](quickstart-sign-in-azure-notebooks.md)).

1. Válassza ki a nyilvános profiloldalán **saját projektek** az oldal tetején:

    ![A böngésző ablakának felső részén saját projektek hivatkozás](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon kattintson a felfelé mutató nyíl gombra (billentyűparancs: U; a gomb a GitHub-tárház **feltöltésekor** jelenik meg, ha a böngészőablak elég széles):

    ![GitHub-tárház-parancs feltöltése a saját projektek oldalon](media/quickstarts/upload-github-repo-command.png)

1. A megjelenő **GitHub-tárház feltöltése** lapon adja meg vagy állítsa be a következő adatokat, majd válassza az **Importálás**lehetőséget:

   - **GitHub-adattár**: Microsoft/kognitív-szolgáltatások – jegyzetfüzetek (ez a név a Jupyter notebookokat az Azure Cognitive Services [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Klónozza a rekurzív módon**: (törölve)
   - **Projektnév**: Cognitive Services-klón
   - **A projekt Azonosítóját**: cognitive services-klón
   - **Nyilvános**: (törölve)

     ![A GitHub-tárház felugró ablakának feltöltése a tárház adatainak összegyűjtéséhez](media/quickstarts/upload-github-repo-popup.png)

1. Türelemmel, amíg a folyamat befejeződése után; a tárház klónozása pár percet is igénybe vehet.

1. A Klónozás befejeződése után az Azure-jegyzetfüzetek megnyílik az új projekt, ahol megtekintheti az összes fájl másolatát.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Jegyzetfüzet megosztása

1. A klónozott projekt osztani, használja a **megosztása** vezérléséhez vagy hivatkozás beszerzése, szerezze be a hivatkozást tartalmazó HTML- vagy a Markdown kódot, vagy létrehozhat egy e-mailt a hivatkozást tartalmazó:

    ![Projekt megosztás parancs](media/quickstarts/share-project-command.png)

1. Mivel töröltük az **nyilvános** beállítást, ha a projektet, a klón Klónozás je privátní. Válassza ki ahhoz, hogy a Másolás nyilvános, **Projektbeállítások**állítsa be a **nyilvános projekt** az előugró lehetőséget, majd válassza ki **mentése**.

1. Válassza ki egy jegyzetfüzetet a projektben való futtatáshoz. Minden egyes notebook az Azure Cognitive Services-tárházban, például a saját önálló rövid. Az alábbi képen láthatók a BingImageSearchAPI notebook használata után a Cognitive Services API-előfizetési kulcs hozzáadása és módosítása a keresési kifejezés "Kiskutyák", "bunnies" eredménye:

    ![A githubról klónozott futtatása a Jupyter notebook](media/quickstarts/clone-notebook-result.png)

1. Ha befejezte a notebook fut, válassza ki **fájl** > **zárja be és halt** gombra kattintva zárja be a jegyzetfüzetet, és a böngészőablakot.

1. Egy a projektben egyes notebook megosztásához kattintson a jobb gombbal a jegyzetfüzetet, és válassza **hivatkozás másolása** (billentyűparancs: y):

    ![Helyi menü parancsot az egyes notebook mutató hivatkozás másolása](media/quickstarts/copy-link-to-individual-notebook.png)

1. Eltérő notebookok fájlok szerkesztéséhez kattintson a jobb gombbal a projektre, és válassza ki a fájlt **Edit file** (billentyűparancs: e). Az alapértelmezett művelet **futtatása** (billentyűparancs: r), csak a fájl tartalmát jeleníti meg, és nem szerkeszthető.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: egy Futtatás ehhez lineáris regressziós modell futtatása Jupyter notebook létrehozása](tutorial-create-run-jupyter-notebook.md)
