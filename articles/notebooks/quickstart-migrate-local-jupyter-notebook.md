---
title: Helyi Jupyter-jegyzetfüzet áttelepítése az Azure Notebookelőzetes verzióba
description: Gyorsan átvihet idáig egy Jupyter-jegyzetfüzetet az Azure Notebookelőzetes verzióba a helyi számítógépről vagy egy webes URL-címről, majd megoszthatja együttműködésre.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: baf05d7adb1340d712ff0fc87436d5bbac51bc8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064325"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Rövid útmutató: Helyi Jupyter-jegyzetfüzet áttelepítése az Azure Notebookelőzetes verzióban

Ebben a rövid útmutatóban áttelepítegy Jupyter-jegyzetfüzetet a helyi számítógépről vagy egy másik elérhető fájl URL-címére az Azure Notebooks ba. 

A jupyter notebookok a saját számítógépén csak az Ön számára érhetők el. Megoszthatja a fájlokat, de a címzettek saját helyi másolatokkal rendelkeznek a jegyzetfüzetről, és nehéz beépíteni a módosításokat. Még akkor is, ha a jegyzetfüzeteket egy megosztott online tárházban, például a GitHubon tárolja, minden közreműködőnek rendelkeznie kell egy helyi Jupyter-telepítéssel, mint az Öné.

A helyi vagy tárház-alapú jegyzetfüzetek Azure-jegyzetfüzetek áttelepítésével azonnal megoszthatja őket a közreműködőkkel, akiknek csak egy böngészőre van szükségük a jegyzetfüzetek megtekintéséhez és futtatásához. Ha bejelentkeznek az Azure Notebooks, akkor is módosíthatja.

## <a name="prerequisites"></a>Előfeltételek

- [Jupyter-jegyzetfüzet](https://jupyter-notebook.readthedocs.io) a helyi számítógépen vagy egy másik elérhető fájl URL-címén. 

## <a name="create-a-project-on-azure-notebooks"></a>Projekt létrehozása az Azure-jegyzetfüzetekben

Ez a rövid útmutató bemutatja egy jegyzetfüzet áttelepítését a helyi számítógépről vagy más elérhető fájl URL-címéről. A jegyzetfüzetek GitHub-tárházból történő áttelepítéséhez olvassa el [a rövid útmutató: Jegyzetfüzet klónozása](quickstart-clone-jupyter-notebook.md)című témakört.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Nyissa meg az [Azure-jegyzetfüzeteket,](https://notebooks.azure.com) és jelentkezzen be. (További részletek: [Rövid útmutató – Bejelentkezés az Azure Notebooks ba).](quickstart-sign-in-azure-notebooks.md)

1. A nyilvános profil lapon válassza a **Saját projektek** lehetőséget a lap tetején:

    ![A Saját projektek hivatkozás a böngészőablak tetején](media/quickstarts/my-projects-link.png)

1. A **Saját projektek** lapon válassza az **Új projekt** (billentyűparancs: n) lehetőséget. A gomb csak **+** akkor jelenhet meg, ha a böngészőablak keskeny:

    ![Új projekt parancs a Saját projektek lapon](media/quickstarts/new-project-command.png)

1. A megjelenő **Új projekt** létrehozása előugró ablakban adja meg az áttelepítendő jegyzetfüzet megfelelő értékeit a **Projekt név** és a **Projektazonosító** mezőkben, törölje a jelet a **Nyilvános projekt** beállításaiból, majd **hozzon létre egy README.md**, majd válassza a **Létrehozás lehetőséget.**

## <a name="upload-the-local-notebook"></a>A helyi jegyzetfüzet feltöltése

1. A projektlapon válassza a **Feltöltés** lehetőséget (amely csak akkor jelenhet meg felfelé mutató nyílként, ha a böngészőablak kicsi), majd válassza az 1 lehetőséget. A megjelenő előugró ablakban válassza a **Számítógéptől lehetőséget,** ha a jegyzetfüzet a helyi fájlrendszerben található, vagy az **URL-cím lehetőséget,** ha a jegyzetfüzet online található:

    ![Jegyzetfüzet feltöltése URL-címről vagy helyi számítógépről](media/quickstarts/upload-from-computer-url-command.png)

   Ismét, ha a jegyzetfüzet egy GitHub-tárházban, kövesse a [rövid útmutató: Egy jegyzetfüzet klónozása](quickstart-clone-jupyter-notebook.md) helyett.

   - Ha a **Számítógépről**lehetőséget használja, húzza az *.ipynb* fájlokat az előugró ablakba, vagy válassza a **Fájlok kiválasztása**lehetőséget, majd keresse meg és jelölje ki az importálni kívánt fájlokat. Ezután válassza a **Feltöltés** lehetőséget. A feltöltött fájlok neve megegyezik a helyi fájlokkal. Nem kell feltöltenie az *.ipynb_checkpoints* mappák tartalmát.

     ![Feltöltés a számítógép előugró ablakából](media/quickstarts/upload-from-computer-popup.png)

   - Ha **a Forrás URL-cím**használatával adja meg a forráscímet a **Fájl URL-címe** mezőben, és a fájlnevet, amelyet a projektben lévő jegyzetfüzethez szeretne rendelni a **Fájlnév** mezőben. Ezután válassza a **Feltöltés** lehetőséget. Ha több fájlja van külön URL-címmel, a **Fájl hozzáadása** paranccsal ellenőrizze az első megadott URL-címet, amely után a felugró ablak új mezőket biztosít egy másik fájlhoz.

     ![Feltöltés URL-felugró ablakból](media/quickstarts/upload-from-url-popup.png)

1. Nyissa meg és futtassa az újonnan feltöltött jegyzetfüzetet a tartalmának és működésének ellenőrzéséhez. Ha elkészült, válassza a **Fájlleállítása** > **lehetőséget, és zárja be** a jegyzetfüzetet.

1. A feltöltött jegyzetfüzetre mutató hivatkozás megosztásához kattintson a jobb gombbal a projektben lévő fájlra, és válassza a **Hivatkozás másolása** parancsot (billentyűparancs: y), majd illessze be a hivatkozást a megfelelő üzenetbe. A projekt egészét megoszthatja a Projekt lap **Megosztás** vezérlőjével.

1. A jegyzetfüzetektől eltérő fájlok szerkesztéséhez kattintson a jobb gombbal a projektben lévő fájlra, és válassza a **Fájl szerkesztése parancsot** (billentyűparancs: i). Az alapértelmezett művelet, a **Futtatás** (billentyűparancs: r) csak a fájl tartalmát jeleníti meg, és nem engedélyezi a szerkesztést.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet létrehozása és futtatása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
