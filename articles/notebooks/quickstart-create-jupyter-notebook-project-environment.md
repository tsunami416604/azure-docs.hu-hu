---
title: Hozzon létre egy Azure Notebooks Preview projektet egyéni környezettel
description: Hozzon létre egy új projektet az Azure Notebookok előzetes verziójában, amely a telepített csomagok és indítási parancsfájlok meghatározott készletével van konfigurálva.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78196841"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Rövid útmutató: Hozzon létre egy projektet egyéni környezettel az Azure Notebookelőzetes verzióban

Az Azure Notebooks projekt fájlok gyűjteménye, például jegyzetfüzetek, adatfájlok, dokumentáció, képek és így tovább, valamint egy adott beállítási parancsokkal konfigurálható környezet. A környezet meghatározásával a projekt, aki klónozza a projektet a saját Azure Notebooks-fiók rendelkezik az összes szükséges információt, hogy újra a szükséges környezetet.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Projekt létrehozása

1. Nyissa meg az [Azure-jegyzetfüzeteket,](https://notebooks.azure.com) és jelentkezzen be. (További részletek: [Rövid útmutató – Bejelentkezés az Azure Notebooks ba).](quickstart-sign-in-azure-notebooks.md)

1. A nyilvános profil lapon válassza a **Saját projektek** lehetőséget a lap tetején:

    ![A Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **Saját projektek** lapon válassza a **+ Új projekt** (billentyűparancs: n); a gomb csak **+** akkor jelenhet meg, ha a böngészőablak keskeny:

    ![Új projekt parancs a Saját projektek lapon](media/quickstarts/new-project-command.png)

1. A megjelenő **Új projekt** létrehozása előugró ablakban adja meg vagy állítsa be a következő részleteket, majd válassza a **Létrehozás**lehetőséget:

    - **Projekt neve**: Projekt egyéni környezettel
    - **Projektazonosító**: projekt-egyéni környezet
    - **Nyilvános projekt**: (törölve)
    - **README.md létrehozása**: (törölve)

1. Néhány pillanat múlva az Azure Notebooks navigál az új projekthez. Vegyen fel egy jegyzetfüzetet a projektbe a **+ Új** **+** legördülő menü kiválasztásával (amely csak jelenik meg), majd a **Jegyzetfüzet**lehetőséget.

1. Adjon a jegyzetfüzetnek egy olyan nevet, mint az *Egyéni környezet.ipynb*, válassza a **Python 3.6** lehetőséget a nyelvhez, és válassza az **Új**lehetőséget.

## <a name="add-a-custom-setup-step"></a>Egyéni beállítási lépés hozzáadása

1. A projekt lapon válassza a **Projektbeállítások lehetőséget.**

    ![Projektbeállítások parancs](media/quickstarts/project-settings-command.png)

1. A **Projektbeállítások** előugró ablakban válassza a **Környezet** lapot, majd a **Környezet beállítása lépések csoportban**válassza a **+ Hozzáadás lehetőséget:**

    ![Parancs új környezeti beállítási lépés hozzáadásához](media/quickstarts/environment-add-command.png)

1. A **+ Hozzáadás** parancs létrehoz egy művelet által meghatározott lépést és egy célfájlt, amely a projekt fájljaiból van kiválasztva. A következő műveletek támogatottak:

   | Művelet | Leírás |
   | --- | --- |
   | Követelmények.txt | A Python-projektek egy requirements.txt fájlban határozzák meg a függőségeiket. Ezzel a beállítással válassza ki a megfelelő fájlt a projekt fájllistájából, és válassza ki a Python-verziót a megjelenő további legördülő listában. Ha szükséges, válassza **a Mégse** lehetőséget a projekthez való visszatéréshez, a fájl feltöltéséhez vagy létrehozásához, majd térjen vissza a **Projektbeállítások** > **környezete** lapra, és hozzon létre egy új lépést. Ezzel a lépéssel a projektben automatikusan fut egy jegyzetfüzet`pip install -r <file>` |
   | Shell parancsfájl | A bash shell parancsfájl (általában az *.sh* kiterjesztéssel rendelkező fájl) jelzésére használható, amely a környezet inicializálásához futtatni kívánt parancsokat tartalmazza. |
   | Környezet.yml | Egy Python-projekt, amely conda-t használ egy környezet kezeléséhez, egy *environments.yml* fájlt használ a függőségek leírására. Ezzel a beállítással válassza ki a megfelelő fájlt a projekt fájllistájából. |

   > [!WARNING]
   > Mivel ez egy fejlesztés alatt álló előzetes verziójú `Environment.yml` szolgáltatás, jelenleg van egy ismert probléma, ahol a beállítás nem a várt módon lesz alkalmazva a projektre. A projekt és a Jupyter-jegyzetfüzetek jelenleg nem töltik be a megadott környezeti fájlt.

1. A beállítási lépés eltávolításához válassza a lépés jobb oldalán lévő **X-et.**

1. Ha az összes beállítási lépés a helyén van, válassza a **Mentés gombot.** (A módosítások elvetéséhez válassza a **Mégse** gombot).

1. A környezet teszteléséhez hozzon létre és futtasson egy új jegyzetfüzetet, majd hozzon létre egy `import` kódcellát a környezetben lévő csomagtól függő utasításokkal, például egy Python-utasítás használatával. Ha az utasítás sikeres, akkor a szükséges csomag telepítése sikeresen megtörtént a környezetben.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Projektek kezelése és konfigurálása az Azure-jegyzetfüzetekben](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: hozzon létre egy Jupyter notebookot a lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
