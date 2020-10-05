---
title: Helyi Jupyter-jegyzetfüzet migrálása Azure Notebooks előzetes verzióra
description: Gyorsan átvihet egy Jupyter-jegyzetfüzetet Azure Notebooks előzetes verzióra a helyi számítógépről vagy egy webes URL-címről, majd megoszthatja azt együttműködésre.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 9c961ad67260cee2809e0bba1e79f2c709183dea
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85832117"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Gyors útmutató: helyi Jupyter-jegyzetfüzet migrálása Azure Notebooks előzetes verzióban

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ebben a rövid útmutatóban egy Jupyter-jegyzetfüzetet telepít át a helyi számítógépről vagy egy másik elérhető fájl URL-címéről Azure Notebooksra. 

A saját számítógépén található Jupyter-jegyzetfüzetek csak Ön számára érhetők el. Megoszthatja a fájljait, de a címzettek saját helyi példányaikat is megoszthatják a jegyzetfüzetből, és nem nehéz beépíteni a módosításokat. Még ha egy megosztott online tárházban (például a GitHubban) is tárol jegyzetfüzeteket, minden közreműködőnek rendelkeznie kell egy, a tiédként konfigurált helyi Jupyter-telepítéssel.

A helyi vagy tárház-alapú jegyzetfüzetek Azure Notebooksba való áttelepítésével azonnal megoszthatja azokat a munkatársakkal, akiknek csak egy böngészőre van szükségük a jegyzetfüzetek megtekintéséhez és futtatásához. Ha bejelentkeznek a Azure Notebooksba, akkor módosításokat is végezhetnek.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Jupyter notebook](https://jupyter-notebook.readthedocs.io) a helyi számítógépen vagy egy másik elérhető fájl URL-címe. 

## <a name="create-a-project-on-azure-notebooks"></a>Projekt létrehozása Azure Notebooks

Ez a rövid útmutató bemutatja, hogyan lehet áttelepíteni egy jegyzetfüzetet a helyi számítógépről vagy egy másik elérhető fájl URL-címéről. A jegyzetfüzetek GitHub-tárházból való áttelepítéséhez lásd: gyors útmutató [: jegyzetfüzet klónozása](quickstart-clone-jupyter-notebook.md).

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. (Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksre](quickstart-sign-in-azure-notebooks.md)).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon válassza az **új projekt** elemet (billentyűparancs: n). A gomb csak akkor jelenhet meg, **+** Ha a böngészőablak keskeny:

    ![Új projekt parancs a saját projektek oldalon](media/quickstarts/new-project-command.png)

1. A megjelenő **új projekt létrehozása** előugró ablakban adja meg az áttelepíteni kívánt jegyzetfüzet megfelelő értékeit a **projekt neve** és a **projekt azonosítója** mezőben, törölje a **nyilvános projekt** beállításait, és **hozzon létre egy readme.MD**, majd válassza a **Létrehozás**lehetőséget.

## <a name="upload-the-local-notebook"></a>A helyi jegyzetfüzet feltöltése

1. A projekt lapon válassza a **feltöltés** lehetőséget (amely csak akkor jelenik meg felfelé mutató nyílként, ha a böngészőablak kicsi), majd válassza az 1 lehetőséget. A megjelenő előugró ablakban válassza a **számítógépről** lehetőséget, ha a notebook a helyi fájlrendszerben található, vagy **URL-** címről, ha a jegyzetfüzet online állapotú:

    ![Parancs egy jegyzetfüzet URL-címről vagy helyi számítógépről való feltöltéséhez](media/quickstarts/upload-from-computer-url-command.png)

   Ha a jegyzetfüzet egy GitHub-tárházban található, kövesse a következő témakör lépéseit [: a notebook klónozása](quickstart-clone-jupyter-notebook.md) helyette.

   - Ha **a alkalmazást a számítógépről**használja, húzza át a *. ipynb* fájlokat a felugró ablakba, vagy válassza a **fájlok kiválasztása**lehetőséget, majd keresse meg és válassza ki az importálni kívánt fájlokat. Ezután válassza a **Feltöltés** lehetőséget. A feltöltött fájlok neve megegyezik a helyi fájlokkal. Nem kell feltöltenie *.ipynb_checkpoints* mappák tartalmát.

     ![Feltöltés a számítógép előugró ablakból](media/quickstarts/upload-from-computer-popup.png)

   - Ha az **URL-címet**használja **, a Fájlnév mezőben adja** meg a forrás címét a **fájl URL-címe** mezőben, valamint azt a fájlnevet, amelyet a projektben lévő jegyzetfüzethez szeretne rendelni. Ezután válassza a **Feltöltés** lehetőséget. Ha több, különálló URL-címmel rendelkező fájllal rendelkezik, a **fájl hozzáadása** paranccsal ellenőrizze az első megadott URL-címet, amely után az előugró ablak új mezőket biztosít egy másik fájlhoz.

     ![Feltöltés URL-felugró ablakból](media/quickstarts/upload-from-url-popup.png)

1. Nyissa meg és futtassa az újonnan feltöltött jegyzetfüzetet annak tartalmának és működésének ellenőrzéséhez. Ha elkészült, válassza a **fájl**  >  **leállítása és bezárás** lehetőséget a jegyzetfüzet bezárásához.

1. A feltöltött jegyzetfüzetre mutató hivatkozás megosztásához kattintson a jobb gombbal a fájlra a projektben, és válassza a **hivatkozás másolása** (billentyűparancs: y) lehetőséget, majd illessze be a hivatkozást a megfelelő üzenetbe. Másik lehetőségként megoszthatja a projektet teljes egészében a projekt oldalon található **megosztás** vezérlőelem használatával.

1. A jegyzetfüzetek kivételével a fájlok szerkesztéséhez kattintson a jobb gombbal a fájlra a projektben, és válassza a **fájl szerkesztése** (billentyűparancs: i) elemet. Az alapértelmezett művelet, **Futtatás** (billentyűparancs: r), csak a fájl tartalmát jeleníti meg, és nem teszi lehetővé a szerkesztést.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet létrehozása és futtatása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
