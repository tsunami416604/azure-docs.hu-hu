---
title: Jupyter-jegyzetfüzet létrehozása és megosztása az Azure Notebookelőzetes verzióban
description: Gyorsan létrehozhat és futtathat egy Jupyter-jegyzetfüzetet az Azure Notebookelőzetes verzióban, majd megoszthatja azt másokkal.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064444"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Rövid útmutató: Jegyzetfüzet létrehozása és megosztása az Azure Notebookelőzetes verzióban

Ebben a rövid útmutatóban hozzon létre és futtasson egy Jupyter-jegyzetfüzetet az Azure Notebooks-on, majd ossza meg a jegyzetfüzetet másokkal. A Jupyter segítségével könnyedén kombinálhatja a Markdown-szöveget, a végrehajtható kódot, az állandó adatokat, a grafikákat és a vizualizációkat egy megosztható vásznon, a jegyzetfüzeten. Az Azure Notebooks egy ingyenes üzemeltetett szolgáltatás a Jupyter notebookok felhőben történő, telepítés nélküli fejlesztéséhez és futtatásához.

## <a name="prerequisites"></a>Előfeltételek
Nincs.

## <a name="create-a-new-project-and-notebook"></a>Új projekt és jegyzetfüzet létrehozása

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Nyissa meg az [Azurehttps://notebooks.azure.com) Notebooks webhelyet (](https://notebooks.azure.com) és jelentkezzen be. További részletek: [Gyorsútmutató – Bejelentkezés az Azure Notebooks ba.](quickstart-sign-in-azure-notebooks.md)

1. A nyilvános profil lapon válassza a **Saját projektek** lehetőséget a lap tetején:

    ![A Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **Saját projektek** lapon válassza a **+ Új projekt** (billentyűparancs: n) lehetőséget. A gomb csak **+** akkor jelenhet meg, ha a böngészőablak keskeny:

    ![Új projekt parancs a Saját projektek lapon](media/quickstarts/new-project-command.png)

1. A megjelenő **Új projekt** létrehozása előugró ablakban adja meg vagy állítsa be a következő részleteket, majd válassza a **Létrehozás**lehetőséget:

   - **A projekt neve**: Hello World in Python
   - **Projekt azonosítója**: hello-world-python
   - **Nyilvános projekt**: (törölve)
   - **README.md létrehozása**: (törölve)

     ![Új projekt előugró ablak a lakott részletekkel](media/quickstarts/new-project-popup.png)

1. Néhány pillanat múlva az Azure Notebooks navigál az új projekthez. Vegyen fel egy jegyzetfüzetet a projektbe a **+ Új** **+** legördülő menü kiválasztásával (amely csak ként jelenhet meg), majd válassza a **Jegyzetfüzet**lehetőséget:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. A **megjelenő Új jegyzetfüzet létrehozása** előugró ablakban adjon meg egy fájlnevet a jegyzetfüzethez, például *HelloWorldInPython.ipynb* (*.ipynb* jelenti ironpython (Jupyter) jegyzetfüzetet), és válassza a **Python 3.6** lehetőséget a nyelvhez (más néven a *kernel):*

    ![Új jegyzetfüzet létrehozása előugró ablak](media/quickstarts/new-notebook-popup.png)

1. Válassza az **Új** lehetőséget a jegyzetfüzet létrehozásának befejezéséhez, amely ezután megjelenik a projekt fájllistájában:

    ![A projekt fájllistájában megjelenő új jegyzetfüzet](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>A notebook futtatása

1. Jelölje ki az új jegyzetfüzetet a szerkesztőben való futtatásához; a kiválasztott kernel (Ebben a példában a Python 3.6) automatikusan aktiválódik ehhez a notebookhoz:

    ![Új jegyzetfüzet megtekintése az Azure-jegyzetfüzetekben](media/quickstarts/create-notebook-first-open.png)

1. Alapértelmezés szerint a jegyzetfüzetnek van egy üres kódcellája. Ha a cellatípust **Markdown**úrra szeretné módosítani, a Cellatípus legördülő menüvel válassza a **Markdown**lehetőséget:

    ![Új jegyzetfüzet cellatípusának módosítása](media/quickstarts/create-notebook-cell-type.png)

1. Írja be vagy illessze be a következő címsorszöveget a cellába:

    ```markdown
    # Hello World in Python
    ```

1. Mivel a Markdown szerkesztésével van, a szöveg fejlécként jelenik meg a "#" szöveggel. A Markdown HTML formátumba való megjelenítéséhez kattintson a **Futtatás** gombra. Az Azure Notebooks ezután automatikusan létrehoz egy új kódcellát:

    ![A cella futtatásgombja és a Markdown](media/quickstarts/run-cell-markdown-render.png)

1. A kódcellába írja be a következő Python-kódot:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. A kód futtatásához válassza a **Futtatás** (billentyűparancs: Shift+Enter) lehetőséget. A cella alatt a következő szöveghez hasonló sikeres kimenetnek kell látnia:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. A munka mentéséhez válassza a Mentés ikont:

    ![Mentés ikon a Jupyter notebook eszköztárán](media/quickstarts/hello-results-save-icon.png)

1. A **kiszolgáló** > leállításához és a böngészőablak bezárásához válassza a**Fájlbezárás és a Leállítás** menüparancsot.

## <a name="share-the-notebook"></a>Jegyzetfüzet megosztása

A jegyzetfüzet megosztásához szükség esetén váltson vissza a projektlapra, kattintson a jobb gombbal a jegyzetfüzetfájlra, válassza a **Hivatkozás másolása** parancsot (billentyűparancs: y), és illessze be a hivatkozást egy megfelelő üzenetbe (e-mail, e-mail stb.).

A projektlapon a **Megosztás** menüben is beszerezhet egy hivatkozást, létrehozhat egy e-mailt a hivatkozással, vagy beszerezheti a HTML- és Markdown-beágyazási kódot:

![Projektmegosztás parancs](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet létrehozása és futtatása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
