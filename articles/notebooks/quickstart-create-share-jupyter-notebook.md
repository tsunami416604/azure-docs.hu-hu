---
title: Jupyter-jegyzetfüzet létrehozása és megosztása Azure Notebooks előzetes verzióban
description: Gyorsan létrehozhat és futtathat Jupyter notebookot Azure Notebooks előzetes verzióban, majd megoszthatja a jegyzetfüzetet másokkal.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064444"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Rövid útmutató: jegyzetfüzet létrehozása és megosztása Azure Notebooks előzetes verzióban

Ebben a rövid útmutatóban egy Jupyter-jegyzetfüzetet hoz létre és futtat Azure Notebookson, majd megoszthatja a jegyzetfüzetet másokkal. A Jupyter segítségével egyszerűen egyesítheti a Markdown szövegét, a végrehajtható kódokat, az állandó adattípusokat, a grafikát és a vizualizációkat egy megosztható vászonon, a notebookon. Az Azure Notebooks egy ingyenes üzemeltetett szolgáltatás a Jupyter notebookok felhőben történő, telepítés nélküli fejlesztéséhez és futtatásához.

## <a name="prerequisites"></a>Előfeltételek
Nincs.

## <a name="create-a-new-project-and-notebook"></a>Új projekt és jegyzetfüzet létrehozása

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Lépjen a [Azure Notebooks webhelyre (https://notebooks.azure.com)](https://notebooks.azure.com) és jelentkezzen be. Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksra](quickstart-sign-in-azure-notebooks.md).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![A böngésző ablakának felső részén saját projektek hivatkozás](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon válassza az **+ új projekt** elemet (billentyűparancs: n). A gomb csak akkor szerepelhet **+** , ha a böngészőablak keskeny:

    ![Új projekt parancsot a saját projektek lapon](media/quickstarts/new-project-command.png)

1. A megjelenő **új projekt létrehozása** előugró ablakban írja be vagy adja meg a következő adatokat, majd válassza a **Létrehozás**lehetőséget:

   - **Projekt neve**: "Helló világ!" alkalmazás a Pythonban
   - **Projekt azonosítója**: Hello-World-Python
   - **Nyilvános projekt**: (törölve)
   - **Hozzon létre egy readme.MD**: (törölve)

     ![Új projekt előugró feltöltött adatokkal](media/quickstarts/new-project-popup.png)

1. Néhány pillanat múlva Azure notebookok nyit meg az új projekt. Vegyen fel egy jegyzetfüzetet a projektbe úgy, hogy kiválasztja az **+ új** legördülő listát (amely csak **+ként** jelenhet meg), majd a **Jegyzetfüzet**kiválasztása:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. A megjelenő **új jegyzetfüzet létrehozása** előugró ablakban adja meg a jegyzetfüzet fájlnevét, például *HelloWorldInPython. ipynb* ( *. ipynb* , IronPython (Jupyter) notebook), és válassza a **Python 3,6** nyelvet (más néven *kernel*):

    ![Az Új jegyzetfüzet létrehozása előugró ablak](media/quickstarts/new-notebook-popup.png)

1. Válassza az **új** lehetőséget a jegyzetfüzet létrehozásának befejezéséhez, amely ezután megjelenik a projekt fájllista:

    ![A projekt fájlok listája jelenik meg az új notebook](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>A notebook futtatása

1. Válassza ki az Új jegyzetfüzet futtassa azt a szerkesztő; Ez a jegyzetfüzet automatikusan aktiválódik a kernel kiválasztott (ebben a példában a Python 3.6):

    ![Az új notebook az Azure-jegyzetfüzetek megtekintése](media/quickstarts/create-notebook-first-open.png)

1. Alapértelmezés szerint a a Notebookban egy üres kódcellába rendelkezik. Ha módosítani szeretné a cella típusát a **Markdown**értékre, a cella típusa legördülő listából válassza ki a **Markdown**:

    ![Az új notebook cella típusának módosítása](media/quickstarts/create-notebook-cell-type.png)

1. Írja be vagy illessze be a következő fejléc szövege a cellába:

    ```markdown
    # Hello World in Python
    ```

1. Markdown szerkeszt, mert a szöveg jelenik meg a "#" fejlécet. A Markdown HTML-fájlba való megjelenítéséhez kattintson a **Futtatás** gombra. Az Azure notebookok majd automatikusan létrehoz egy új kódcellát ezt követően:

    ![A cella és a megjelenített Markdown a Futtatás gomb](media/quickstarts/run-cell-markdown-render.png)

1. A kódcellába adja meg a következő Python-kódban:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. A kód futtatásához válassza a **Futtatás** elemet (billentyűparancs: SHIFT + ENTER). A cella alatti kimenetnek kell megjelennie a sikeres az alábbi szöveghez hasonló:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Kattintson a Mentés ikonra kattintva mentse a munkáját:

    ![Mentés ikonra a Jupyter notebook eszköztár](media/quickstarts/hello-results-save-icon.png)

1. Válassza ki a **fájlt** > **Bezárás és** leállítás menüpontot a kiszolgáló leállításához és a böngészőablak bezárásához.

## <a name="share-the-notebook"></a>A notebook megosztása

A jegyzetfüzet megosztásához váltson vissza a projekt lapra, ha szükséges, kattintson a jobb gombbal a notebook fájlra, válassza a **hivatkozás másolása** (billentyűparancs: y) lehetőséget, és illessze be a hivatkozást egy megfelelő üzenetbe (E-mail, im stb.).

A projekt lapon a **megosztás** menüt is használhatja egy hivatkozás beszerzéséhez, egy e-mail-üzenet létrehozásához vagy a HTML-és Markdown-beágyazási kód beszerzéséhez:

![Projekt megosztás parancs](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet létrehozása és futtatása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
