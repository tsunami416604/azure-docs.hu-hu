---
title: Jupyter Notebook létrehozása és megosztása Azure Notebooks Preview-ban
description: Gyorsan létrehozhat és futtathat egy Jupyter Notebook Azure Notebooks előzetes verzióban, majd megoszthatja a jegyzetfüzetet másokkal.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 77575b5b9e006ac9881d03dc95509d55c53c26d7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88589333"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Rövid útmutató: jegyzetfüzet létrehozása és megosztása Azure Notebooks előzetes verzióban

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ebben a rövid útmutatóban létrehoz és futtat egy Jupyter Notebookt a Azure Notebookson, majd megosztja a jegyzetfüzetet másokkal. A Jupyter segítségével egyszerűen egyesítheti a Markdown szövegét, a végrehajtható kódokat, az állandó adattípusokat, a grafikát és a vizualizációkat egy megosztható vászonon, a notebookon. A Azure Notebooks egy ingyenes üzemeltetett szolgáltatás, amely Jupyter-jegyzetfüzeteket fejleszt és futtat a felhőben telepítés nélkül.

## <a name="prerequisites"></a>Előfeltételek
Nincsenek.

## <a name="create-a-new-project-and-notebook"></a>Új projekt és jegyzetfüzet létrehozása

1. Lépjen a [Azure Notebooks webhelyre https://notebooks.azure.com) (](https://notebooks.azure.com) és jelentkezzen be. Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksra](quickstart-sign-in-azure-notebooks.md).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon válassza az **+ új projekt** elemet (billentyűparancs: n). A gomb csak akkor jelenhet meg, **+** Ha a böngészőablak keskeny:

    ![Új projekt parancs a saját projektek oldalon](media/quickstarts/new-project-command.png)

1. A megjelenő **új projekt létrehozása** előugró ablakban írja be vagy adja meg a következő adatokat, majd válassza a **Létrehozás**lehetőséget:

   - **Projekt neve**: „Helló világ!” alkalmazás a Pythonban
   - **Projekt azonosítója**: Hello-World-Python
   - **Nyilvános projekt**: (törölve)
   - **Hozzon létre egy readme.MD**: (törölve)

     ![Új projekt előugró ablaka a feltöltött adatokkal](media/quickstarts/new-project-popup.png)

1. Néhány pillanat elteltével Azure Notebooks navigál az új projekthez. Vegyen fel egy jegyzetfüzetet a projektbe úgy, hogy kijelöli az **+ új** legördülő listát (amely csak a következőt jeleníti meg **+** ), majd a **Jegyzetfüzet**lehetőséget választja:

    :::image type="content" source="media/quickstarts/empty-project-new-notebook-button.png" alt-text="Egy új, üres projekt és a notebook parancs hozzáadása." lightbox="media/quickstarts/empty-project-new-notebook-button.png":::

1. A megjelenő **új jegyzetfüzet létrehozása** előugró ablakban adja meg a jegyzetfüzet fájlnevét, például *HelloWorldInPython. ipynb* (*. ipynb* , IronPython (Jupyter) notebook), és válassza a **Python 3,6** nyelvet (más néven *kernel*):

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

    ![Mentés ikon a Jupyter Notebook eszköztáron](media/quickstarts/hello-results-save-icon.png)

1. Válassza a **fájl**  >  **Bezárás és** leállítás menüpontot a kiszolgáló leállításához és a böngészőablak bezárásához.

## <a name="share-the-notebook"></a>A jegyzetfüzet megosztása

A jegyzetfüzet megosztásához váltson vissza a projekt lapra, ha szükséges, kattintson a jobb gombbal a notebook fájlra, válassza a **hivatkozás másolása** (billentyűparancs: y) lehetőséget, és illessze be a hivatkozást egy megfelelő üzenetbe (E-mail, im stb.).

A projekt lapon a **megosztás** menüt is használhatja egy hivatkozás beszerzéséhez, egy e-mail-üzenet létrehozásához vagy a HTML-és Markdown-beágyazási kód beszerzéséhez:

![Projekt megosztási parancsa](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter Notebook létrehozása és futtatása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
