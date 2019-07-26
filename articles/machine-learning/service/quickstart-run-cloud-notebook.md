---
title: 'Gyors útmutató: Jegyzetfüzet futtatása a felhőben'
titleSuffix: Azure Machine Learning service
description: Ez az oktatóanyag bemutatja, hogyan kezdheti meg a Azure Machine Learning szolgáltatást, és hogyan használhat felügyelt notebook-kiszolgálót a felhőben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1124bb17abb9340b442d8d6075551ffe0dc681f7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68371052"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: Felhőalapú notebook-kiszolgáló használata az Azure Machine Learning használatának megkezdéséhez

Ez a rövid útmutató bemutatja, hogyan kezdheti el a Azure Machine Learning szolgáltatás használatát a felhőben felügyelt notebook-kiszolgáló használatával. Nincs szükség telepítésre. Ha inkább saját Python-környezetbe szeretné telepíteni az SDK-t, tekintse meg a gyors útmutató [: A Azure Machine Learning](quickstart-run-local-notebook.md)használatának megkezdéséhez használja a saját notebook-kiszolgálóját.

Ez a rövid útmutató bemutatja, hogyan használhatja a [Azure Machine learning szolgáltatás](concept-azure-machine-learning-architecture.md) munkaterületet a gépi tanulási (ml-) kísérletek nyomon követésére. Ehhez hozzon létre egy [Jegyzetfüzet virtuális gépet (előzetes verzió)](how-to-configure-environment.md#notebookvm): egy biztonságos, felhőalapú Azure-munkaállomást, amely Jupyter notebook-kiszolgálót, JupyterLab és teljes mértékben előkészített ml-környezetet biztosít. Ezután futtasson egy Python-jegyzetfüzetet ezen a virtuális gépen (VM), amely az értékeket naplózza a munkaterületen.

Ehhez hajtsa végre a következő műveleteket:

* Hozzon létre egy munkaterületet.
* Hozzon létre egy jegyzetfüzet virtuális gépet a munkaterületen.
* Nyissa meg a Jupyter webes felületét.
* Nyisson meg egy, a PI-t és a naplókat tartalmazó jegyzetfüzetet, amely minden egyes iteráció során hibákat tartalmaz.
* Futtassa a jegyzetfüzetet.
* Tekintse meg a naplózott hibák értékét a munkaterületen. Az alábbi példa bemutatja, hogyan segíti a munkaterület a parancsfájlokban generált információk nyomon követését.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Ha Azure Machine Learning szolgáltatás-munkaterülettel rendelkezik, ugorjon a [következő szakaszra](#create-notebook). Ellenkező esetben hozzon létre egyet most.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Jegyzetfüzet-alapú virtuális gép létrehozása

 A munkaterületen hozzon létre egy felhőalapú erőforrást a Jupyter-jegyzetfüzetek használatának megkezdéséhez. Ez az erőforrás egy felhőalapú platform, amely minden, ami a Azure Machine Learning szolgáltatás futtatásához szükséges.

1. Nyissa meg a munkaterületet a [Azure Portalban](https://portal.azure.com/). Ha nem tudja, hogyan keresse meg a munkaterületét a portálon, tekintse meg a [munkaterület megtekintése](how-to-manage-workspace.md#view)című témakört.

1. A munkaterület lapon válassza a bal oldalon található **notebook virtuális gépek** lehetőséget.

1. A notebook virtuális gép létrehozásához válassza az **+ új** lehetőséget.  

     ![Új virtuális gép kiválasztása](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Adja meg a virtuális gép nevét. Ezután kattintson a **Létrehozás** elemre.

    > [!NOTE]
    > A notebook virtuális gép nevének 2 és 16 karakter közöttinek kell lennie. A betűk, számjegyek és kötőjelek érvényes karakterek. A névnek egyedinek kell lennie az Azure-előfizetésen belül.

    ![Új virtuális gép létrehozása](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Várjon körülbelül 4 – 5 percet, amíg az állapot **futni**nem változik.


## <a name="open-the-jupyter-web-interface"></a>A Jupyter webes felületének megnyitása

A virtuális gép futása után nyissa meg a Jupyter webes felületét a **notebook virtuális gépek** szakasz használatával.

1. A virtuális gép **URI** oszlopában válassza a **Jupyter** lehetőséget.  

    ![A Jupyter notebook-kiszolgáló elindítása](./media/quickstart-run-cloud-notebook/start-server.png)

    A hivatkozás elindítja a notebook-kiszolgálót, és megnyitja a Jupyter notebook weboldalát egy új böngésző lapon.  Ez a hivatkozás csak a virtuális gépet létrehozó személy számára fog működni.  A munkaterület minden felhasználójának létre kell hoznia a saját virtuális gépet.

1. A Jupyter notebook weboldalán a felső mappa neve a felhasználónevét adja meg. Válassza ki ezt a mappát.

    > [!TIP]
    > Ez a mappa a saját munkaterületén található [Storage](concept-workspace.md#resources) -tárolóban, és nem a notebook virtuális gépen van.  Törölheti a notebook virtuális gépet, és továbbra is megőrizheti a munkáját.  Amikor később hoz létre egy új jegyzetfüzet virtuális gépet, a rendszer ezt a mappát fogja betölteni.  Ha a munkaterületet másokkal is megosztja, látni fogja a mappát, és látni fogja a sajátját. 

1. A Samples mappa neve tartalmazza a verziószámot (például**1.0.33.1**). Válassza ki a minták mappát.

1. Válassza ki a **Gyorsindítás** mappát.

## <a name="run-the-notebook"></a>A notebook futtatása

Futtasson egy, a PI-t megbecsülő jegyzetfüzetet, és naplózza a hibát a munkaterületen.

1. Válassza a **01. Run-Experiment. ipynb** elemet a jegyzetfüzet megnyitásához.

1. Ha a "kernel nem található" riasztást látja, válassza ki a **Python 3,6-AzureML** kernelt (körülbelül a lista félúton), és állítsa be a kernelt.

1. Válassza ki az első kódlapot, majd válassza a **Futtatás**lehetőséget.

    > [!NOTE]
    > A kódok celláihoz zárójelek tartoznak. Ha a zárójelek üresek ( __[]__ ), a kód nem lett futtatva. A kód futása közben egy csillag jelenik meg ( __[*]__ ). A kód befejeződése után a **[1]** szám jelenik meg.  A szám azt jelzi, hogy a cellák milyen sorrendben futottak.
    >
    > Cella futtatásához használja a **SHIFT + ENTER** billentyűkombinációt.

    ![Az első kódlapot futtató cella futtatása](media/quickstart-run-cloud-notebook/cell1.png)

1. Futtassa a második kód celláját. Ha a hitelesítésre vonatkozó utasítások jelennek meg, másolja át a kódot, és kövesse a bejelentkezéshez szükséges hivatkozást. A bejelentkezés után a böngésző emlékezni fog erre a beállításra.  

    ![Hitelesítés](media/quickstart-run-cloud-notebook/authenticate.png)

1. Ha a kód cellájának futtatása sikeres, a ( __[2]__ ) cella megjelenik. Ha be kellett jelentkeznie, megjelenik egy sikeres hitelesítési állapotüzenetek üzenet.   Ha nem kell bejelentkeznie, a cella kimenete nem jelenik meg. Csak a szám jelenik meg, amely azt jelzi, hogy a cella sikeresen futott.

    ![Sikeres műveletről tájékoztató üzenet](media/quickstart-run-cloud-notebook/success.png)

1. Futtassa a kód többi celláját. Ahogy az egyes cellák futnak, a cella száma megjelenik. Csak az utolsó cella jeleníti meg a többi kimenetet.  

    A legnagyobb kód cellában `run.log` több helyen jelenik meg. Mindegyik `run.log` hozzáadja az értékét a munkaterülethez.

## <a name="view-logged-values"></a>Naplózott értékek megtekintése

1. A `run` cella kimenete tartalmaz egy hivatkozást a Azure Portal, ahol megtekintheti a kísérlet eredményeit a munkaterületen.

    ![Kísérletek megtekintése](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Az **Azure Portalra mutató hivatkozás** kiválasztásával megtekintheti a futtatásával kapcsolatos információkat a munkaterületen. Ez a hivatkozás megnyitja a munkaterületet a Azure Portal.

1. A megjelenő naplózott értékek sorai automatikusan létrejönnek a munkaterületen. Mindig automatikusan létrejön egy diagram, amikor több értéket naplóz ugyanazzal a névparaméterrel. Például:

   ![Előzmények megtekintése](./media/quickstart-run-cloud-notebook/web-results.png)

Mivel a PI-hoz közelítő kód véletlenszerű értékeket használ, a mintaterületek eltérőek lehetnek.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="stop-the-notebook-vm"></a>A notebook virtuális gép leállítása

Állítsa le a notebook virtuális gépet, ha nem használja a költségeket a költséghatékonyság csökkentése érdekében.  

1. A munkaterületen válassza a **notebook virtuális gépek**lehetőséget.

   ![A virtuális gép kiszolgálójának leállítása](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Válassza ki a virtuális gépet a listából.

1. Válassza a **Leállítás**lehetőséget.

1. Ha készen áll a kiszolgáló ismételt használatára, válassza az **Indítás**lehetőséget.

### <a name="delete-everything"></a>Mindent törölni

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

A feladatok elvégzése után lépjen a Jupyter Notebook weblapra. A **Gyorsindítás** mappában Nyissa meg és futtassa a **02. Deploy-Web-Service. ipynb** jegyzetfüzetet, és ismerkedjen meg a webszolgáltatás üzembe helyezésével.

Ha más Python-csomagokat szeretne telepíteni a Jupyter-környezetbe, használja ezt a kódot egy jegyzetfüzeten belül:

```
!source activate py36 && pip install <packagename>
```

A Jupyter Notebook weblapon más jegyzetfüzeteket is megkereshet a Samples mappában, hogy többet tudjon meg a Azure Machine Learning szolgáltatásról.

A részletes munkafolyamat-élményért kövesse Machine Learning oktatóanyagokat a modellek betanításához és üzembe helyezéséhez:  

> [!div class="nextstepaction"]
> [Oktatóanyag: Rendszerkép besorolási modell betanítása](tutorial-train-models-with-aml.md)
