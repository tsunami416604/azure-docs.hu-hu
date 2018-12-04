---
title: Létrehozhat és megoszthat a Jupyter notebook az Azure-ban |} A Microsoft Docs
description: Gyorsan létrehozása és futtatása a Jupyter notebook Azure notebookokban, majd a notebook megoszthatja másokkal.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 5add60ad-0b4b-4fd5-adf5-eb50ce072d00
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 9f7659dadb4c710169ec1e700d31e60cbc957ac1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855983"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Gyors útmutató: Létrehozása, és a egy jegyzetfüzetet megosztása

1. Lépjen a [Azure notebookok](https://notebooks.azure.com) , és jelentkezzen be. (További információkért lásd: [rövid útmutató – jelentkezzen be Azure-jegyzetfüzetek](quickstart-sign-in-azure-notebooks.md)).

1. Válassza ki a nyilvános profiloldalán **saját projektek** az oldal tetején:

    ![A böngésző ablakának felső részén saját projektek hivatkozás](media/quickstarts/my-projects-link.png)

1. A a **saját projektek** lapon jelölje be **+ új projekt** (billentyűparancs: n); a gomb csak megjelenik **+** Ha a böngészőablak keskeny:

    ![Új projekt parancsot a saját projektek lapon](media/quickstarts/new-project-command.png)

1. Az a **új projekt létrehozása** előugró ablak, amely akkor jelenik meg, adja meg, vagy állítsa be a következő adatokat, majd válassza ki **létrehozás**:

    - **Projektnév**: Hello World Python-ben
    - **A projekt Azonosítóját**: hello-world – python
    - **Nyilvános projekt**: (törölve)
    - **Hozzon létre egy README.md**: (törölve)

    ![Új projekt előugró feltöltött adatokkal](media/quickstarts/new-project-popup.png)

1. Néhány pillanat múlva Azure notebookok nyit meg az új projekt. Kiválasztásával adjon hozzá egy jegyzetfüzetet a projekthez a **+ új** legördülő (amely jelennek meg, csak **+**), majd válassza **Notebook**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "Egy új, üres projekt, és adja hozzá a notebook parancs")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. Az a **Új jegyzetfüzet létrehozása** előugró ablak, amely akkor jelenik meg, adja meg a FileName paramétert a notebook például *HelloWorldInPython.ipynb* (*.ipynb* azt jelenti, hogy Ironpythonu (Jupyter) Notebook ), és válassza ki **Python 3.6-os** nyelvének (más néven a *kernel*):

    ![Az Új jegyzetfüzet létrehozása előugró ablak](media/quickstarts/new-notebook-popup.png)

1. Válassza ki **új** hozza létre a jegyzetfüzetet, majd a projekt fájlok listája látható:

    ![A projekt fájlok listája jelenik meg az új notebook](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>A notebook futtatása

1. Válassza ki az Új jegyzetfüzet futtassa azt a szerkesztő; Ez a jegyzetfüzet automatikusan aktiválódik a kernel kiválasztott (ebben a példában a Python 3.6):

    ![Az új notebook az Azure-jegyzetfüzetek megtekintése](media/quickstarts/create-notebook-first-open.png)

1. Alapértelmezés szerint a a Notebookban egy üres kódcellába rendelkezik. A cella típusának a módosításához **Markdown**, a cella típusa legördülő segítségével válassza ki a **Markdown**:

    ![Az új notebook cella típusának módosítása](media/quickstarts/create-notebook-cell-type.png)

1. Írja be vagy illessze be a következő fejléc szövege a cellába:

    ```markdown
    # Hello World in Python
    ```

1. Markdown szerkeszt, mert a szöveg jelenik meg a "#" fejlécet. A Markdown HTML leképezni, válassza ki a **futtatása** gombra. Az Azure notebookok majd automatikusan létrehoz egy új kódcellát ezt követően:

    ![A cella és a megjelenített Markdown a Futtatás gomb](media/quickstarts/run-cell-markdown-render.png)

1. A kódcellába adja meg a következő Python-kódban:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Válassza ki **futtatása** (billentyűparancs: a Shift + Enter) a kód futtatásához. A cella alatti kimenetnek kell megjelennie a sikeres az alábbi szöveghez hasonló:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Kattintson a Mentés ikonra kattintva mentse a munkáját:

    ![Mentés ikonra a Jupyter notebook eszköztár](media/quickstarts/hello-results-save-icon.png)

1. Válassza ki a **fájl** > **Halt, zárja be** menü paranccsal állíthatja le a kiszolgálón, és zárja be a böngészőablakot.

## <a name="share-the-notebook"></a>A notebook megosztása

1. Szeretne megosztani a jegyzetfüzet, váltson vissza a projekt lapját a szükség esetén, kattintson a jobb gombbal a jegyzetfüzet-fájlt, jelölje ki **hivatkozás másolása** (billentyűparancs: y), és illessze be a hivatkozás egy megfelelő üzenet (e-mail, azonnali üzenetek stb.).

    A projekt oldalon is használhatja a **megosztás** menüre, és szerezzen be egy hivatkozást, létrehozhat egy e-mailt a hivatkozás, vagy a HTML- és Markdown-beágyazási kódot:

    ![Projekt megosztás parancs](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: egy Futtatás ehhez lineáris regressziós modell futtatása Jupyter notebook létrehozása](tutorial-create-run-jupyter-notebook.md)
