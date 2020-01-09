---
title: Helyi Jupyter-jegyzetfüzet migrálása Azure Notebooks előzetes verzióra
description: Gyorsan átvihet egy Jupyter-jegyzetfüzetet Azure Notebooks előzetes verzióra a helyi számítógépről vagy egy webes URL-címről, majd megoszthatja azt együttműködésre.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 9e5270c59a64f9510f9108bbe4d00b922178888c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647050"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Gyors útmutató: helyi Jupyter-jegyzetfüzet migrálása Azure Notebooks előzetes verzióban

A saját számítógépén létrehozott Jupyter jegyzetfüzetek csak Ön számára érhetők el. A fájlokat többféle módon is megoszthatja, de a címzettek saját helyi másolattal rendelkeznek a jegyzetfüzetből, és az esetlegesen elvégzett módosítások beépítését is megnehezíti. A jegyzetfüzeteket egy megosztott online tárházban (például a GitHubban) is tárolhatja, de ehhez továbbra is szükség van arra, hogy minden közreműködő saját helyi Jupyter-példánya legyen ugyanazzal a konfigurációval, mint a tiéd.

A helyi vagy tárház-alapú jegyzetfüzetek Azure Notebooksba való áttelepítésével azokat a felhőben tárolhatja, amelyekről azonnal megoszthatja őket a közreműködők használatával. Ezeknek a közreműködőknek csak egy böngészőre van szükségük a jegyzetfüzet megtekintéséhez és futtatásához, és ha [bejelentkeznek](quickstart-sign-in-azure-notebooks.md) a Azure Notebooks, akkor módosításokat is végezhetnek.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ez a rövid útmutató azt mutatja be, hogyan lehet áttelepíteni egy jegyzetfüzetet a helyi számítógépről vagy más elérhető fájl URL-címéről. A jegyzetfüzetek GitHub-tárházból való áttelepítéséhez lásd: gyors útmutató [: jegyzetfüzet klónozása](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Projekt létrehozása Azure Notebooks

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. (Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksre](quickstart-sign-in-azure-notebooks.md)).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon válassza az **+ új projekt** elemet (billentyűparancs: n); a gomb csak akkor szerepelhet **+** , ha a böngészőablak keskeny:

    ![Új projekt parancs a saját projektek oldalon](media/quickstarts/new-project-command.png)

1. A megjelenő **új projekt létrehozása** előugró ablakban adja meg az áttelepíteni kívánt jegyzetfüzet megfelelő értékeit a **projekt neve** és a **projekt azonosítója** mezőben, törölje a **nyilvános projekt** beállításait, és **hozzon létre egy readme.MD**, majd válassza a **Létrehozás**lehetőséget.

## <a name="upload-the-local-notebook"></a>A helyi jegyzetfüzet feltöltése

1. A projekt lapon válassza a **feltöltés** lehetőséget (amely csak akkor jelenik meg felfelé mutató nyílként, ha a böngészőablak kicsi), majd válassza az 1 lehetőséget. A megjelenő előugró ablakban válassza a **számítógépről** lehetőséget, ha a notebook a helyi fájlrendszerben található, vagy **URL-** címről, ha a jegyzetfüzet online állapotú:

    ![Parancs egy jegyzetfüzet URL-címről vagy helyi számítógépről való feltöltéséhez](media/quickstarts/upload-from-computer-url-command.png)

   (Ha a jegyzetfüzet egy GitHub-tárházban található, kövesse a következő lépéseket [: a jegyzetfüzet klónozása](quickstart-clone-jupyter-notebook.md) .)

   - Ha **a alkalmazást a számítógépről**használja, húzza át a *. ipynb* fájlokat a felugró ablakba, vagy válassza a **fájlok kiválasztása**lehetőséget, majd keresse meg és válassza ki az importálni kívánt fájlokat. Ezután válassza a **Feltöltés** lehetőséget. A feltöltött fájlok neve megegyezik a helyi fájlokkal. (A *. ipynb_checkpoints* mappák tartalmát nem kell feltöltenie.)

     ![Feltöltés a számítógép előugró ablakból](media/quickstarts/upload-from-computer-popup.png)

   - Ha az **URL-címet**használja **, a Fájlnév mezőben adja** meg a forrás címét a **fájl URL-címe** mezőben, valamint azt a fájlnevet, amelyet a projektben lévő jegyzetfüzethez szeretne rendelni. Ezután válassza a **Feltöltés** lehetőséget. Ha több, különálló URL-címmel rendelkező fájllal rendelkezik, a **+ fájl hozzáadása** paranccsal ellenőrizze az első megadott URL-címet, amely után az előugró ablak új mezőket biztosít egy másik fájlhoz.

     ![Feltöltés URL-felugró ablakból](media/quickstarts/upload-from-url-popup.png)

1. Nyissa meg és futtassa az újonnan feltöltött jegyzetfüzetet annak tartalmának és működésének ellenőrzéséhez. Ha elkészült, válassza a **fájl** > **leállítása és lezárása** lehetőséget a jegyzetfüzet bezárásához.

1. A feltöltött jegyzetfüzetre mutató hivatkozás megosztásához kattintson a jobb gombbal a fájlra a projektben, és válassza a **hivatkozás másolása** (billentyűparancs: y) lehetőséget, majd illessze be a hivatkozást a megfelelő üzenetbe. Másik lehetőségként megoszthatja a projektet teljes egészében a projekt oldalon található **megosztás** vezérlőelem használatával.

1. A jegyzetfüzetek kivételével a fájlok szerkesztéséhez kattintson a jobb gombbal a fájlra a projektben, és válassza a **fájl szerkesztése** (billentyűparancs: i) elemet. Az alapértelmezett művelet, **Futtatás** (billentyűparancs: r), csak a fájl tartalmát jeleníti meg, és nem teszi lehetővé a szerkesztést.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet futtatásának létrehozása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
