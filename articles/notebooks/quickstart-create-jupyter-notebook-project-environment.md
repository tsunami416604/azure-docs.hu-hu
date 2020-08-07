---
title: Azure Notebooks előnézeti projekt létrehozása egyéni környezettel
description: Hozzon létre egy új projektet Azure Notebooks előzetes verzióban, amely a telepített csomagok és indítási parancsfájlok meghatározott készletével van konfigurálva.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 655c016b55abdcf4b6f546a1fe16348ec4c83724
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853364"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Rövid útmutató: projekt létrehozása egyéni környezettel Azure Notebooks előzetes verzióban

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

A Azure Notebooksban egy projekt a fájlok, például a jegyzetfüzetek, az adatfájlok, a dokumentáció, a képek stb. gyűjteménye, valamint egy olyan környezet, amely meghatározott telepítési parancsokkal konfigurálható. Ha a projektet a projekttel együtt határozza meg, akkor bárki, aki a projektet a saját Azure Notebooks-fiókjába klónozott, minden olyan információval rendelkezik, amelyre szükségük van a szükséges környezet újbóli létrehozásához.

## <a name="create-a-project"></a>Projekt létrehozása

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. (Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksre](quickstart-sign-in-azure-notebooks.md)).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon válassza az **+ új projekt** elemet (billentyűparancs: n); a gomb csak akkor jelenhet meg, **+** Ha a böngészőablak keskeny:

    ![Új projekt parancs a saját projektek oldalon](media/quickstarts/new-project-command.png)

1. A megjelenő **új projekt létrehozása** előugró ablakban írja be vagy adja meg a következő adatokat, majd válassza a **Létrehozás**lehetőséget:

    - **Projekt neve**: projekt egyéni környezettel
    - **Projekt azonosítója**: projekt – egyéni – környezet
    - **Nyilvános projekt**: (törölve)
    - **Hozzon létre egy readme.MD**: (törölve)

1. Néhány pillanat elteltével Azure Notebooks navigál az új projekthez. Vegyen fel egy jegyzetfüzetet a projektbe úgy, hogy kijelöli az **+ új** legördülő listát (amely csak a **+** következőként jelenik meg), majd a **Jegyzetfüzet**lehetőséget választja.

1. Adjon nevet a jegyzetfüzetnek, például *Egyéni környezet. ipynb*, válassza a **Python 3,6** lehetőséget a nyelvhez, majd válassza az **új**lehetőséget.

## <a name="add-a-custom-setup-step"></a>Egyéni telepítési lépés hozzáadása

1. A projekt lapon válassza a **projekt beállításai**lehetőséget.

    ![Project settings parancs](media/quickstarts/project-settings-command.png)

1. A **projekt beállításai** előugró ablakban válassza a **környezet** lapot, majd a **környezet beállítása lépésnél**válassza a **+ Hozzáadás**lehetőséget:

    ![Új környezet telepítési lépésének hozzáadására szolgáló parancs](media/quickstarts/environment-add-command.png)

1. A **+ Add** parancs egy művelet és egy, a projektben lévő fájlokból kiválasztott célfájl által meghatározott lépést hoz létre. A következő műveletek támogatottak:

   | Művelet | Leírás |
   | --- | --- |
   | Requirements.txt | A Python-projektek requirements.txt fájlban határozzák meg a függőségeiket. Ha ezt a lehetőséget választja, válassza ki a megfelelő fájlt a projekt fájljának listájából, és válassza ki a Python-verziót is a megjelenő további legördülő listában. Ha szükséges, a **Mégse** gombra kattintva térjen vissza a projekthez, töltse fel vagy hozza létre a fájlt, majd térjen vissza a **projekt beállításai**  >  **környezet** lapra, és hozzon létre egy új lépést. Ezzel a lépéssel automatikusan futtat egy jegyzetfüzetet a projektben.`pip install -r <file>` |
   | Rendszerhéj-parancsfájl | A használatával egy bash rendszerhéj-parancsfájlt (jellemzően egy *. sh* kiterjesztésű fájlt) jelezhet, amely a környezet inicializálásához futtatni kívánt parancsokat tartalmazza. |
   | Environment. YML | A környezetek kezelésére szolgáló Conda használó Python-projekt egy Environment *. YML* fájlt használ a függőségek leírására. Ha ezt a lehetőséget választja, válassza ki a megfelelő fájlt a projekt fájl listájából. |

   > [!WARNING]
   > Mivel ez egy előzetes verziójú szolgáltatás a fejlesztés alatt, jelenleg egy ismert probléma van, ahol a `Environment.yml` beállítás a várt módon nem lesz alkalmazva a projektre. A projekt és a Jupyter-jegyzetfüzetek nem töltik be a megadott környezeti fájlt.

1. A telepítési lépések eltávolításához válassza a lépéstől jobbra lévő **X-et** .

1. Ha az összes telepítési lépés bekerül, válassza a **Mentés**lehetőséget. (A módosítások elvetéséhez kattintson a **Mégse** gombra).

1. A környezet teszteléséhez hozzon létre és futtasson egy új jegyzetfüzetet, majd hozzon létre egy kódot tartalmazó cellát olyan utasításokkal, amelyek a környezetben lévő csomagtól függenek, például egy Python- `import` utasítás használatával. Ha az utasítás sikeres, a szükséges csomag telepítése sikeresen megtörtént a környezetben.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Projektek kezelése és konfigurálása Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet létrehozása és futtatása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
