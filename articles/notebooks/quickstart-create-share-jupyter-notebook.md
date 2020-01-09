---
title: Jupyter-jegyzetfüzet létrehozása és megosztása Azure Notebooks előzetes verzióban
description: Gyorsan létrehozhat és futtathat Jupyter notebookot Azure Notebooks előzetes verzióban, majd megoszthatja a jegyzetfüzetet másokkal.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 7b983d927bfd789cddb8d00675294cefc9f55e08
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647084"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Rövid útmutató: jegyzetfüzet létrehozása és megosztása Azure Notebooks előzetes verzióban

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és futtathat Jupyter-jegyzetfüzeteket Azure Notebooks, majd megoszthatja a jegyzetfüzetet másokkal.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-new-project-and-notebook"></a>Új projekt és jegyzetfüzet létrehozása

1. Lépjen a [Azure Notebooks webhelyre (https://notebooks.azure.com)](https://notebooks.azure.com) és jelentkezzen be. (Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksre](quickstart-sign-in-azure-notebooks.md)).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon válassza az **+ új projekt** elemet (billentyűparancs: n); a gomb csak akkor szerepelhet **+** , ha a böngészőablak keskeny:

    ![Új projekt parancs a saját projektek oldalon](media/quickstarts/new-project-command.png)

1. A megjelenő **új projekt létrehozása** előugró ablakban írja be vagy adja meg a következő adatokat, majd válassza a **Létrehozás**lehetőséget:

   - **Projekt neve**: "Helló világ!" alkalmazás a Pythonban
   - **Projekt azonosítója**: Hello-World-Python
   - **Nyilvános projekt**: (törölve)
   - **Hozzon létre egy readme.MD**: (törölve)

     ![Új projekt előugró ablaka a feltöltött adatokkal](media/quickstarts/new-project-popup.png)

1. Néhány pillanat elteltével Azure Notebooks navigál az új projekthez. Vegyen fel egy jegyzetfüzetet a projektbe úgy, hogy kiválasztja az **+ új** legördülő listát (amely csak **+ként** jelenhet meg), majd a **Jegyzetfüzet**kiválasztása:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. A megjelenő **új jegyzetfüzet létrehozása** előugró ablakban adja meg a jegyzetfüzet fájlnevét, például *HelloWorldInPython. ipynb* ( *. ipynb* , IronPython (Jupyter) notebook), és válassza a **Python 3,6** nyelvet (más néven *kernel*):

    ![Az új jegyzetfüzet létrehozása felugró ablak](media/quickstarts/new-notebook-popup.png)

1. Válassza az **új** lehetőséget a jegyzetfüzet létrehozásának befejezéséhez, amely ezután megjelenik a projekt fájllista:

    ![Új jegyzetfüzet jelenik meg a projekt fájllista](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>A notebook futtatása

1. Válassza ki az új jegyzetfüzetet a szerkesztőben való futtatáshoz. a kiválasztott kernel (ebben a példában a Python 3,6) automatikusan aktiválódik ehhez a jegyzetfüzethez:

    ![Új jegyzetfüzet megtekintése Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Alapértelmezés szerint a jegyzetfüzetben egyetlen üres kód cella van. Ha módosítani szeretné a cella típusát a **Markdown**értékre, a cella típusa legördülő listából válassza ki a **Markdown**:

    ![A cella típusának módosítása új jegyzetfüzetben](media/quickstarts/create-notebook-cell-type.png)

1. Adja meg vagy illessze be a következő fejléc szövegét a cellába:

    ```markdown
    # Hello World in Python
    ```

1. Mivel a Markdown szerkeszti, a szöveg fejlécként jelenik meg a "#" kifejezéssel. A Markdown HTML-fájlba való megjelenítéséhez kattintson a **Futtatás** gombra. Ezután a Azure Notebooks automatikusan létrehoz egy új kódlapot:

    ![A cella és a megjelenített Markdown Futtatás gombja](media/quickstarts/run-cell-markdown-render.png)

1. A Code (kód) cellában adja meg a következő Python-kódot:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. A kód futtatásához válassza a **Futtatás** elemet (billentyűparancs: SHIFT + ENTER). A cella alatt az alábbi szöveghez hasonló sikeres kimenetnek kell megjelennie:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Válassza a Mentés ikont a munka mentéséhez:

    ![Mentés ikon a Jupyter notebook eszköztárán](media/quickstarts/hello-results-save-icon.png)

1. Válassza ki a **fájlt** > **Bezárás és** leállítás menüpontot a kiszolgáló leállításához és a böngészőablak bezárásához.

## <a name="share-the-notebook"></a>A jegyzetfüzet megosztása

A jegyzetfüzet megosztásához váltson vissza a projekt lapra, ha szükséges, kattintson a jobb gombbal a notebook fájlra, válassza a **hivatkozás másolása** (billentyűparancs: y) lehetőséget, és illessze be a hivatkozást egy megfelelő üzenetbe (E-mail, im stb.).

A projekt lapon a **megosztás** menüt is használhatja egy hivatkozás beszerzéséhez, egy e-mail-üzenet létrehozásához vagy a HTML-és Markdown-beágyazási kód beszerzéséhez:

![Projekt megosztási parancsa](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet futtatásának létrehozása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
