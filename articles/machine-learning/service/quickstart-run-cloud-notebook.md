---
title: 'Gyors útmutató: -Jegyzetfüzet futtatása a felhőben'
titleSuffix: Azure Machine Learning service
description: Ismerkedés az Azure Machine Learning szolgáltatásban. A felhőben felügyelt notebook server használatával próbálja ki a munkaterülethez.  A munkaterület az eligazodást blokk, amellyel kísérletezhet, betanítását és gépi tanulási modellek üzembe helyezése a felhőben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: ecb97860e70141a744833aa9b9a4fcea3f3688f0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149822"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: Ismerkedés az Azure Machine Learning felhőalapú notebook server használatával

Hozzon létre egy felhőalapú notebook kiszolgálót, majd használja azt.  Ez a rövid útmutatóban értékek jelentkezik be a Python-kód futtatása a [Azure Machine Learning szolgáltatás munkaterület](concept-azure-machine-learning-architecture.md). A munkaterület az eligazodást blokk, amellyel kísérletezhet, betanítását és a Machine Learning gépi tanulási modellek üzembe helyezése a felhőben. 

Ez a rövid útmutató bemutatja, hogyan felhőalapú erőforrás létrehozása az Azure Machine Learning-munkaterületet, a konfigurált az Azure Machine Learning futtatásához szükséges Python-környezetet. A saját környezetben használja, lásd: [a rövid útmutató: A saját notebook server használata az Azure Machine Learning használatának első lépései](quickstart-run-local-notebook.md).  
 
Ebben a rövid útmutatóban tegye a következőket:

* Hozzon létre egy új felhőalapú notebook kiszolgálót a munkaterületén.
* A Jupyter webes felületének megnyitásához.
* Nyisson meg egy jegyzetfüzetet, pi, és a naplók a hibákat, minden egyes ismétléskor becslésbe kódot tartalmaz.
* A jegyzetfüzet futtatásához.
* A naplózott hiba értékeinek megtekintése a munkaterületén. Ez a példa bemutatja, hogyan segíthet a munkaterület a szkriptben létrehozott információk nyomon követésében. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület.  [A munkaterület létrehozása](setup-create-workspace.md#portal) mostantól, ha nem rendelkezik ilyennel.

## <a name="create-a-cloud-based-notebook-server"></a>Hozzon létre egy felhőalapú notebook kiszolgálót

 A munkaterület hozzon létre egy felhőalapú erőforrás Jupyter notebookok használatának megkezdéséhez. Ehhez az erőforráshoz biztosít egy felhőalapú platform, minden, amit az Azure Machine Learning szolgáltatás futtatására előre konfigurált.

1. Nyissa meg a munkaterület a [az Azure portal](https://portal.azure.com/).  Ha nem biztos abban, hogy hogyan keresse meg a munkaterület a portálon, hogyan [keresse meg a munkaterület](how-to-manage-workspace.md#view).

1. A munkaterület oldalán az Azure Portalon, válassza **Notebook virtuális gépek** a bal oldalon.

1. Válassza ki **+ új** notebook virtuális gép létrehozásához.

     ![Válassza ki az új virtuális gép](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Adja meg a virtuális gép nevét. Ezután kattintson a **Létrehozás** elemre.

    > [!NOTE]
    > A Notebook virtuális gép nevét a 2-16 karakter között kell lennie. Érvényes karakterek: betűk, számjegyek, és a - karakter.  A név is egyedinek kell lennie az Azure-előfizetése között.

    ![Új virtuális gép létrehozása](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Várjon körülbelül 4 – 5 percig, amíg a állapota **futó**.


## <a name="launch-jupyter-web-interface"></a>Indítsa el a Jupyter webes felület

Miután a virtuális gép fut, használja a **Notebook virtuális gépek** szakasz a Jupyter webes felületének megnyitásához.

1. Válassza ki **Jupyter** a a **URI** oszlop a virtuális géphez.  

    ![Indítsa el a Jupyter notebook kiszolgálót](./media/quickstart-run-cloud-notebook/start-server.png)

    A hivatkozás elindítja a notebook kiszolgálót, és a egy új böngészőlapon nyílik meg a Jupyter notebook weblapra.  Ez a hivatkozás csak a virtuális Gépet létrehozó személy fog működni.

1. A felső foldername a Jupyter notebook weblapon, az a felhasználónév.  Válassza ki a mappát.

1. A minták foldername tartalmaz egy verziószámot, például **minták – 1.0.33.1**.  Válassza ki a mintákat tartalmazó mappára.

1. Válassza ki a **rövid** notebookot.

## <a name="run-the-notebook"></a>A notebook futtatása

-Pi becslése és naplózza a hibát a munkaterülethez jegyzetfüzet futtatása.

1. Válassza ki **01.run-experiment.ipynb** a notebook megnyitásához.

1. Kattintson az első kódcella, és válassza ki **futtatása**.

    > [!NOTE]
    > Kód cellák előtt zárójelek közé van. Ha a zárójelek üres (__[]__), a kódot még nem futott. A kód futtatásakor megjelenik egy csillag (__[*]__). Miután befejeződött a kódot, számos **[1]** jelenik meg.  A számot jelzi, hogy a sorrendet, amelyben a cellák futott.
    >
    > Használat **a Shift + Enter** futtatásához egy cella parancsikonja.

    ![Az első kódcella futtatásához](media/quickstart-run-cloud-notebook/cell1.png)

1. A második kódcella futtatásához. Ha utasításokat hitelesítéséhez, másolja a vágólapra a kódot, és kövesse a hivatkozást, hogy jelentkezzen be. Miután bejelentkezik, a böngésző megjegyzi ezt a beállítást.  

    ![Hitelesítés](media/quickstart-run-cloud-notebook/authenticate.png)

1. Ha elkészült, a cella szám __: [2]__ jelenik meg.  Ha a bejelentkezéshez, látni fogja a sikeres hitelesítés állapotüzenetet.   Ha nem rendelkezik való bejelentkezéshez, kimenetet a cella nem jelenik meg, csak a szám jelenik meg, hogy a cella sikeresen futtatta-e a megjelenítendő.

    ![Sikeres műveletről tájékoztató üzenet](media/quickstart-run-cloud-notebook/success.png)

1. Futtassa a kódot cellák a többi.  Mivel minden cella a lejáratot követően újrainduljon, látni fogja a cellák száma jelenik meg. Csak az utolsó cella egyéb kimenetet jeleníti meg.  

    A legnagyobb kódcellába, lásd: `run.log` több helyen is használja. Minden egyes `run.log` annak értéket ad hozzá a munkaterülethez.


## <a name="view-logged-values"></a>Naplózott értékek megtekintése

1. A kimenet a `run` cella vissza az Azure Portalon a kísérlet eredmények megtekintése a munkaterület mutató hivatkozást tartalmaz. 

    ![Kísérletek megtekintése](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Kattintson a **az Azure-portálra mutató hivatkozást** futtatás adatainak megtekintéséhez a munkaterületén.  Ez a hivatkozás megnyitja a munkaterületet az Azure Portalon.

1. A grafikon láthatja a naplózott értékek is automatikusan létrejön a munkaterületen. Mindig automatikusan létrejön egy diagram, amikor több értéket naplóz ugyanazzal a névparaméterrel.

   ![Előzmények megtekintése](./media/quickstart-run-cloud-notebook/web-results.png)

Mivel a kódot a hozzávetőleges pi véletlenszerű értéket használ, a grafikon különböző értékek jelennek meg.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

### <a name="stop-the-notebook-vm"></a>A notebook virtuális gép leállítása

Állítsa le a notebookot a virtuális gép, amikor a költségek csökkentése érdekében nem használ.  

1. A munkaterületen válassza ki a **Notebook virtuális gépek**.

   ![A virtuális gép kiszolgáló leállítása](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Válassza ki a virtuális gépet a listából.

1. Válassza ki **leállítása**.

1. Ha készen áll a felhasználó a kiszolgáló ismét, válassza ki a **Start**.

### <a name="delete-everything"></a>Teljes tartalmának törlése

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Tartsa meg az erőforráscsoportot is, de egyetlen munkaterület törlése. A munkaterület tulajdonságainak megjelenítéséhez, és válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban elvégezte ezeket a feladatokat:

* Hozzon létre egy notebookot virtuális Gépet.
* A Jupyter webes felületének megnyitásához.
* Nyisson meg egy jegyzetfüzetet, pi, és a naplók a hibákat, minden egyes ismétléskor becslésbe kódot tartalmaz.
* A jegyzetfüzet futtatásához.
* A naplózott hiba értékeinek megtekintése a munkaterületén.  Ez a példa bemutatja, hogyan segíthet a munkaterület a szkriptben létrehozott információk nyomon követésében. 

A Jupyter Notebook weblapon böngésszen más notebookjait tudhat meg többet az Azure Machine Learning szolgáltatás a mintákat tartalmazó mappára.

Részletes munkafolyamat élményt biztosít betanítása és a modell üzembe helyezése a Machine Learning-oktatóanyagokat követve:  

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy rendszerkép osztályozási modell betanítása](tutorial-train-models-with-aml.md)
