---
title: Egyéni környezettel rendelkező Azure notebookok projekt létrehozása
description: A telepített csomagokat, és az indítási parancsfájlok egy meghatározott készletével együtt konfigurált Azure-jegyzetfüzetekben hozzon létre egy új projektet.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 35b5f1e29ae125dcac79c278578e900a73ffc7be
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771385"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Gyors útmutató: Projekt létrehozása egyéni környezettel

A projekt Azure notebookok gyűjteménye, fájlok, például a notebookok, adatfájlokat, dokumentáció, lemezképek és így tovább, és a egy környezetben, amelyek adott telepítő parancsokkal konfigurálhatók. A környezet a projekttel definiálásával bárki, aki a projekt klónozza a saját Azure-jegyzetfüzetek fiókba, létre kell hoznia a szükséges környezetet minden adattal rendelkezik.

## <a name="create-a-project"></a>Projekt létrehozása

1. Lépjen a [Azure notebookok](https://notebooks.azure.com) , és jelentkezzen be. (További információkért lásd: [rövid útmutató – jelentkezzen be Azure-jegyzetfüzetek](quickstart-sign-in-azure-notebooks.md)).

1. Válassza ki a nyilvános profiloldalán **saját projektek** az oldal tetején:

    ![A böngésző ablakának felső részén saját projektek hivatkozás](media/quickstarts/my-projects-link.png)

1. A a **saját projektek** lapon jelölje be **+ új projekt** (billentyűparancs: n); a gomb csak megjelenik **+** Ha a böngészőablak keskeny:

    ![Új projekt parancsot a saját projektek lapon](media/quickstarts/new-project-command.png)

1. Az a **új projekt létrehozása** előugró ablak, amely akkor jelenik meg, adja meg, vagy állítsa be a következő adatokat, majd válassza ki **létrehozás**:

    - **Projektnév**: Egyéni környezettel rendelkező projekt
    - **A projekt Azonosítóját**: project-egyéni-környezet
    - **Nyilvános projekt**: (törölve)
    - **Hozzon létre egy README.md**: (törölve)

1. Néhány pillanat múlva Azure notebookok nyit meg az új projekt. Kiválasztásával adjon hozzá egy jegyzetfüzetet a projekthez a **+ új** legördülő (amely jelennek meg, csak **+**), majd válassza **Notebook**.

1. Nevezze el a notebook például *egyéni environment.ipynb*válassza **Python 3.6-os** a nyelvet, és válassza a **új**.

## <a name="add-a-custom-setup-step"></a>Egyéni telepítés lépés hozzáadása

1. Válassza ki a lapot, **Projektbeállítások**.

    ![Projekt beállítások parancs](media/quickstarts/project-settings-command.png)

1. Az a **Projektbeállítások** előugró ablakban válassza a **környezet** fülre, majd **környezet beállítási lépéseket**, jelölje be **+ Hozzáadás**:

    ![Parancs használatával adja hozzá az új környezet beállítása lépés](media/quickstarts/environment-add-command.png)

1. A **+ Hozzáadás** parancs létrehoz egy művelet és a egy kiválasztott elemet a projekt fájljainak célfájl által meghatározott lépés. A következő műveletek támogatottak:

    | Művelet | Leírás |
    | --- | --- |
    | A Requirements.txt | Python-projektek függősége a requirements.txt fájl adja meg. Ezzel a beállítással válassza ki a megfelelő fájlt a projekt fájl listából, és is kiválaszthatja a további legördülő, amely akkor jelenik meg a Python-verzió. Ha szükséges, válassza ki a **megszakítása** térjen vissza a projekthez, töltse fel vagy hozza létre a fájlt, majd térjen vissza a **Projektbeállítások** > **környezet** lapra, és hozzon létre egy Új lépés. Az ebben a lépésben helyen jegyzetfüzet futtatása a projektben automatikusan fut `pip install -r <file>` |
    | Héjparancsfájl | Bash héjparancsfájl azt, hogy (általában egy fájl a *.sh* kiterjesztéssel), amely tartalmazza a környezet inicializálása futtatni kívánt parancs. |
    | Environment.yml | Egy Python-projekt által használt conda-környezet kezelése célokra egy *environments.yml* függőségeket leíró fájl. Ezzel a beállítással a projekt fájl listából válassza ki a megfelelő fájlt. |

1. Minden olyan telepítési lépés eltávolításához jelölje ki a **X** jobb oldalán a lépést.

1. Ha az összes beállítási lépéseket érvényesítve van, válassza ki **mentése**. (Válasszon **Mégse** elveti a módosításokat).

1. A környezet teszteléséhez hozzon létre egy új jegyzetfüzet futtatása, majd hozzon létre egy kódcellát utasításokat, amelyek egy csomagot a környezetben, a Python használatával például függenek `import` utasítást. Ha az utasítás végrehajtása sikeres, majd a szükséges csomag sikeresen telepítve a környezetben.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kezelheti és konfigurálhatja a projektek Azure-jegyzetfüzetekben](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: egy Futtatás ehhez lineáris regressziós modell futtatása Jupyter notebook létrehozása](tutorial-create-run-jupyter-notebook.md)
