---
title: Helyi Jupyter notebook át Azure notebookok
description: Gyorsan Jupyter notebook átvitele az Azure-jegyzetfüzeteket a helyi számítógépen vagy egy webes URL-címet, majd ossza meg az együttműködéshez.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 3b7d5aab05923fb4356e0d45aaf21a77076a870a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277466"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Gyors útmutató: A helyi Jupyter notebook áttelepítése

A saját számítógépén helyileg létrehozott Jupyter notebookok csak az Ön számára érhetők el. Megoszthatja azt jelenti, hogy, különböző fájljait, de majd címzettek rendelkeznek-e a notebookot a saját helyi példányát, és előfordulhat, hogy minden olyan módosításokat építhet számára nehézséget jelent. Notebookok egy megosztott, például a GitHub online tárházban is tárolhat, de ehhez továbbra is szükséges, hogy rendelkezik-e a saját helyi Jupyter-telepítés az Öné azonos konfigurációjú minden közreműködő.

A helyi vagy a tárház notebookok Azure notebookok áttelepítésével tárolja ezeket a felhőben, amelyen azonnal megoszthatja a közreműködőkkel együtt. Ezeknek a közreműködőknek csak egy böngészőre van szükségük a jegyzetfüzet megtekintéséhez és futtatásához, és ha [bejelentkeznek](quickstart-sign-in-azure-notebooks.md) a Azure Notebooks, akkor módosításokat is végezhetnek.

Ez a rövid útmutató azt ismerteti, folyamata az áttelepítéshez a notebookot a helyi számítógépről vagy egy másik elérhető fájl URL-CÍMÉT. A jegyzetfüzetek GitHub-tárházból való áttelepítéséhez lásd: gyors útmutató [: jegyzetfüzet klónozása](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Hozzon létre egy projektet az Azure-jegyzetfüzetek

1. Lépjen [Azure Notebooks](https://notebooks.azure.com) , és jelentkezzen be. (Részletekért lásd: rövid útmutató [– bejelentkezés Azure Notebooksre](quickstart-sign-in-azure-notebooks.md)).

1. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején:

    ![A böngésző ablakának felső részén saját projektek hivatkozás](media/quickstarts/my-projects-link.png)

1. A **saját projektek** lapon válassza az **+ új projekt** elemet (billentyűparancs: n); a gomb csak akkor szerepelhet **+** , ha a böngészőablak keskeny:

    ![Új projekt parancsot a saját projektek lapon](media/quickstarts/new-project-command.png)

1. A megjelenő **új projekt létrehozása** előugró ablakban adja meg az áttelepíteni kívánt jegyzetfüzet megfelelő értékeit a **projekt neve** és a **projekt azonosítója** mezőben, törölje a **nyilvános projekt** beállításait, és **hozzon létre egy readme.MD**, majd válassza a **Létrehozás**lehetőséget.

## <a name="upload-the-local-notebook"></a>A helyi notebook feltöltéséhez

1. A projekt lapon válassza a **feltöltés** lehetőséget (amely csak akkor jelenik meg felfelé mutató nyílként, ha a böngészőablak kicsi), majd válassza az 1 lehetőséget. A megjelenő előugró ablakban válassza a **számítógépről** lehetőséget, ha a notebook a helyi fájlrendszerben található, vagy **URL-** címről, ha a jegyzetfüzet online állapotú:

    ![A parancs egy URL-cím vagy a helyi számítógép notebook feltöltéséhez](media/quickstarts/upload-from-computer-url-command.png)

   (Ha a jegyzetfüzet egy GitHub-tárházban található, kövesse a következő lépéseket [: a jegyzetfüzet klónozása](quickstart-clone-jupyter-notebook.md) .)

   - Ha **a alkalmazást a számítógépről**használja, húzza át a *. ipynb* fájlokat a felugró ablakba, vagy válassza a **fájlok kiválasztása**lehetőséget, majd keresse meg és válassza ki az importálni kívánt fájlokat. Ezután válassza a **feltöltés**lehetőséget. A feltöltött fájlok vannak megadva a neve megegyezik a helyi fájlokat. (A *. ipynb_checkpoints* mappák tartalmát nem kell feltöltenie.)

     ![Töltse fel a számítógép helyi menü](media/quickstarts/upload-from-computer-popup.png)

   - Ha az **URL-címet**használja **, a Fájlnév mezőben adja** meg a forrás címét a **fájl URL-címe** mezőben, valamint azt a fájlnevet, amelyet a projektben lévő jegyzetfüzethez szeretne rendelni. Ezután válassza a **feltöltés**lehetőséget. Ha több, különálló URL-címmel rendelkező fájllal rendelkezik, a **+ fájl hozzáadása** paranccsal ellenőrizze az első megadott URL-címet, amely után az előugró ablak új mezőket biztosít egy másik fájlhoz.

     ![Töltse fel az URL-cím előugró ablak](media/quickstarts/upload-from-url-popup.png)

1. Nyissa meg, és futtassa az újonnan feltöltött notebook annak tartalmát, és a művelet ellenőrzéséhez. Ha elkészült, válassza a **fájl** > **leállítása és lezárása** lehetőséget a jegyzetfüzet bezárásához.

1. A feltöltött jegyzetfüzetre mutató hivatkozás megosztásához kattintson a jobb gombbal a fájlra a projektben, és válassza a **hivatkozás másolása** (billentyűparancs: y) lehetőséget, majd illessze be a hivatkozást a megfelelő üzenetbe. Másik lehetőségként megoszthatja a projektet teljes egészében a projekt oldalon található **megosztás** vezérlőelem használatával.

1. A jegyzetfüzetek kivételével a fájlok szerkesztéséhez kattintson a jobb gombbal a fájlra a projektben, és válassza a **fájl szerkesztése** (billentyűparancs: i) elemet. Az alapértelmezett művelet, **Futtatás** (billentyűparancs: r), csak a fájl tartalmát jeleníti meg, és nem teszi lehetővé a szerkesztést.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet futtatásának létrehozása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
