---
title: Helyi Jupyter notebook át Azure notebookok |} A Microsoft Docs
description: Gyorsan Jupyter notebook átvitele az Azure-jegyzetfüzeteket a helyi számítógépen vagy egy webes URL-címet, majd ossza meg az együttműködéshez.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: bac6834a8ffb107c344086d1d3c28990f32a4760
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856395"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Gyors útmutató: A helyi Jupyter notebook áttelepítése

A saját számítógépén helyileg létrehozott Jupyter notebookok csak az Ön számára érhetők el. Megoszthatja azt jelenti, hogy, különböző fájljait, de majd címzettek rendelkeznek-e a notebookot a saját helyi példányát, és előfordulhat, hogy minden olyan módosításokat építhet számára nehézséget jelent. Notebookok egy megosztott, például a GitHub online tárházban is tárolhat, de ehhez továbbra is szükséges, hogy rendelkezik-e a saját helyi Jupyter-telepítés az Öné azonos konfigurációjú minden közreműködő.

A helyi vagy a tárház notebookok Azure notebookok áttelepítésével tárolja ezeket a felhőben, amelyen azonnal megoszthatja a közreműködőkkel együtt. Ezeket a közreműködők kell csupán egy böngésző megtekintését és futtatását a jegyzetfüzet, ha azok [jelentkezzen be a](quickstart-sign-in-azure-notebooks.md) az Azure-jegyzetfüzeteket is módosíthatják.

Ez a rövid útmutató azt ismerteti, folyamata az áttelepítéshez a notebookot a helyi számítógépről vagy egy másik elérhető fájl URL-CÍMÉT. A GitHub-adattárból notebookok áttelepítéséhez lásd: [a rövid útmutató: egy jegyzetfüzetet klónozása](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Hozzon létre egy projektet az Azure-jegyzetfüzetek

1. Lépjen a [Azure notebookok](https://notebooks.azure.com) , és jelentkezzen be. (További információkért lásd: [rövid útmutató – jelentkezzen be Azure-jegyzetfüzetek](quickstart-sign-in-azure-notebooks.md)).

1. Válassza ki a nyilvános profiloldalán **saját projektek** az oldal tetején:

    ![A böngésző ablakának felső részén saját projektek hivatkozás](media/quickstarts/my-projects-link.png)

1. A a **saját projektek** lapon jelölje be **+ új projekt** (billentyűparancs: n); a gomb csak megjelenik **+** Ha a böngészőablak keskeny:

    ![Új projekt parancsot a saját projektek lapon](media/quickstarts/new-project-command.png)

1. Az a **új projekt létrehozása** előugró ablak, amely akkor jelenik meg, adja meg a megfelelő értékeket a notebook a migráláshoz az **projektnév** és **Projektazonosító** mezők, törölje a beállítások **nyilvános projekt** és **hozzon létre egy README.md**, majd **létrehozás**.

## <a name="upload-the-local-notebook"></a>A helyi notebook feltöltéséhez

1. Válassza ki a lapot, **feltöltése** (amely jelenhet meg egy felfelé mutató nyíl csak akkor, ha a böngészőablak a kis), majd válassza az 1. A megjelenő előugró ablakban válassza ki **számítógépről** Ha a helyi fájlrendszerben található a jegyzetfüzet vagy **URL-CÍMRŐL** Ha a jegyzetfüzet online található:

    ![A parancs egy URL-cím vagy a helyi számítógép notebook feltöltéséhez](media/quickstarts/upload-from-computer-url-command.png)

   (Újra, ha a jegyzetfüzet egy GitHub-adattárból, kövesse [a rövid útmutató: egy jegyzetfüzetet klónozása](quickstart-clone-jupyter-notebook.md) helyette.)

    - Ha használ **a számítógép**, áthúzása a *.ipynb* fájlok a helyi menü, vagy válasszon **fájlok kiválasztása**, majd keresse meg és jelölje ki az importálni kívánt fájlokat. Válassza ki **feltöltése**. A feltöltött fájlok vannak megadva a neve megegyezik a helyi fájlokat. (Nem szükséges töltse fel a tartalmát bármely *.ipynb_checkpoints* mappák.)

    ![Töltse fel a számítógép helyi menü](media/quickstarts/upload-from-computer-popup.png)

    - Használatakor **URL-CÍMRŐL**, adja meg a forrás-címet a **fájl URL-cím** mező, és a fájlnevet a notebookot a projekt a hozzárendelése a **Fájlnév** mező. Válassza ki **feltöltése**. Ha több fájlt külön URL-ekkel rendelkezik, használja a **+ fájl hozzáadása** paranccsal ellenőrizheti az első URL-címet ad meg, amely után előugró új mezők kínál egy másik fájlba.

    ![Töltse fel az URL-cím előugró ablak](media/quickstarts/upload-from-url-popup.png)

1. Nyissa meg, és futtassa az újonnan feltöltött notebook annak tartalmát, és a művelet ellenőrzéséhez. Ha elkészült, válassza ki a **fájl** > **Halt, zárja be** bezárja a notebookot.

1. A feltöltött jegyzetfüzet mutató hivatkozás megosztása, kattintson a jobb gombbal a projektre, és válassza ki a fájlt **hivatkozás másolása** (billentyűparancs: y), majd illessze be a hivatkozás a megfelelő üzenetet. Azt is megteheti, mint egy teljes használata a projekt megoszthatja a **megosztása** vezérlőelem a lapot.

1. Eltérő notebookok fájlok szerkesztéséhez kattintson a jobb gombbal a projektre, és válassza ki a fájlt **Edit file** (billentyűparancs: e). Az alapértelmezett művelet **futtatása** (billentyűparancs: r), csak a fájl tartalmát jeleníti meg, és nem szerkeszthető.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: egy Futtatás ehhez lineáris regressziós modell futtatása Jupyter notebook létrehozása](tutorial-create-run-jupyter-notebook.md)
