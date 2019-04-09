---
title: Rövid útmutató-jegyzetfüzet futtatása a felhőben
titleSuffix: Azure Machine Learning service
description: Ismerkedés az Azure Machine Learning szolgáltatásban. A felhőben felügyelt notebook server használatával próbálja ki a munkaterülethez.  A munkaterület az eligazodást blokk, amellyel kísérletezhet, betanítását és gépi tanulási modellek üzembe helyezése a felhőben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 9cd643185fb4647b19082980edfd333c507aab8a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266256"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: Ismerkedés az Azure Machine Learning felhőalapú notebook server használatával

Hozzon létre egy felhőalapú notebook kiszolgálót, majd futtassa a kódot, amely értékek bejelentkezik az Azure Machine Learning szolgáltatás használatával [munkaterület](concept-azure-machine-learning-architecture.md). A munkaterület az eligazodást blokk, amellyel kísérletezhet, betanítását és a Machine Learning gépi tanulási modellek üzembe helyezése a felhőben. 

Ez a rövid útmutató bemutatja, hogyan felhőalapú erőforrás létrehozása az Azure Machine Learning-munkaterületet, a konfigurált az Azure Machine Learning futtatásához szükséges Python-környezetet. A saját környezetben használja, lásd: [a rövid útmutató: A saját notebook server használata az Azure Machine Learning használatának első lépései](quickstart-run-local-notebook.md).  
 
Ebben a rövid útmutatóban tegye a következőket:

* Hozzon létre egy munkaállomás
* Indítsa el a Jupyter Notebook kiszolgálót a munkaállomáson
* Nyisson meg egy jegyzetfüzetet, pi, és a naplók a hibákat, minden egyes ismétléskor becslésbe kódot tartalmaz.
* A jegyzetfüzet futtatásához.
* A naplózott hiba értékeinek megtekintése a munkaterületén.  Ez a példa bemutatja, hogyan segíthet a munkaterület a szkriptben létrehozott információk nyomon követésében. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

1. [Az Azure Machine Learning-munkaterület létrehozása](setup-create-workspace.md#portal) Ha nem rendelkezik ilyennel.

1. Nyissa meg a munkaterület a [az Azure portal](https://portal.azure.com/).  Ha nem biztos abban, hogy hogyan keresse meg a munkaterület a portálon, hogyan [keresse meg a munkaterület](how-to-manage-workspace.md#view).

## <a name="create-a-workstation"></a>Hozzon létre egy munkaállomás 

A notebook munkaállomás lehetővé teszi egy felhőalapú platform, amely az Azure Machine Learning szolgáltatást futtatja, amit az előre konfigurált Jupyter notebookokhoz. A munkaterület hozhat létre erre a platformra Jupyter notebookok használatának megkezdéséhez.

1. A munkaterület oldalán az Azure Portalon, válassza **Notebook munkaállomás** a bal oldalon.

1. Válassza ki **Notebookjait hozzon létre egy Azure Machine Learning munkaállomás (előzetes verzió)**

   ![Ismerje meg a munkaterület](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. A **Notebook munkaállomások** szakasz bemutatja az összes rendelkezésre álló jegyzetfüzet felhőalapú kiszolgálók listáját a munkaterületén.  Itt is kezelheti ezeket az erőforrásokat és törölje őket, ha már nincs szüksége. 

1. Válassza ki **Munkaállomás hozzáadása** hozhat létre egy notebookot munkaállomás.

     ![Válassza a Munkaállomás hozzáadása](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. A Notebook Munkaállomás hozzáadása szakaszban adjon a munkaállomáson a **nevű számítási** , és válassza ki a **számítási típus**. Ezután kattintson a **Létrehozás** elemre.

    ![Hozzon létre új munkaállomás](media/quickstart-run-cloud-notebook/create-new-workstation.png)

    > [!NOTE]
    > A munkaállomás létrehozása körülbelül két percet vesz igénybe. Amikor végzett, a állapota "Fut" frissítések és a Jupyter és JupyterLab mutató hivatkozásokat tartalmaz jelennek meg.

## <a name="launch-jupyter-web-interface"></a>Indítsa el a Jupyter webes felület

A munkaállomás létrehozása után a Notebook munkaállomások szakasz segítségével indítsa el a Jupyter webes felületén.

* Válassza ki **Jupyter** vagy **Jupyter labor** a a **indítsa el a** oszlopában a munkaállomáson.

    ![Jupyter notebook server indítása](./media/quickstart-run-cloud-notebook/start-notebook-server.png)

    Ez elindítja a notebook kiszolgálót, és a egy új böngészőlapon nyílik meg a kiszolgáló kezdőlapja.  A kiszolgáló látható mintafüzetek a használhatja az Azure Machine Learning szolgáltatás használatába.

### <a name="run-the-notebook"></a>A notebook futtatása

-Pi becslése és naplózza a hibát a munkaterülethez jegyzetfüzet futtatása.

1. Válassza ki **01.run-experiment.ipynb** a notebook megnyitásához.

1. Az állapot területen arra kéri, hogy Várjon, amíg a kernel megkezdődött.  Az üzenet eltűnik, ha a kernel készen áll.

    ![Várjon, amíg a kernel indítása](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. Miután elindult a kernel, futtassa a cellák egy egyszerre **Shift + Enter**. Válassza ki vagy **cellák** > **összes futtatása** a teljes jegyzetfüzet futtatásához. Amikor megjelenik egy csillag (__*__) mellett egy cellába, továbbra is fut a cellát. A cella kódjának lezárulása után megjelenik egy szám.  

Ha végzett a jegyzetfüzet-ban futó összes cellát, megtekintheti a bejelentkezett értékek a munkaterületén.

## <a name="view-logged-values"></a>Naplózott értékek megtekintése

1. A kimenet a `run` cella vissza az Azure Portalon a kísérlet eredmények megtekintése a munkaterület mutató hivatkozást tartalmaz. 

    ![Kísérletek megtekintése](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Kattintson a **az Azure-portálra mutató hivatkozást** futtatás adatainak megtekintéséhez a munkaterületén.  Ez a hivatkozás megnyitja a munkaterületet az Azure Portalon.

1. A grafikon láthatja a naplózott értékek is automatikusan létrejön a munkaterületen. Mindig automatikusan létrejön egy diagram, amikor több értéket naplóz ugyanazzal a névparaméterrel.

   ![Előzmények megtekintése](./media/quickstart-run-cloud-notebook/web-results.png)

Mivel a kódot a hozzávetőleges pi véletlenszerű értéket használ, a grafikon különböző értékek jelennek meg.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Tartsa meg az erőforráscsoportot is, de egyetlen munkaterület törlése. A munkaterület tulajdonságainak megjelenítéséhez, és válassza ki **törlése**.


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban elvégezte a következőket:

* Hozzon létre egy munkaállomás
* Indítsa el a Jupyter Notebook kiszolgálót a munkaállomáson
* Nyisson meg egy jegyzetfüzetet, pi, és a naplók a hibákat, minden egyes ismétléskor becslésbe kódot tartalmaz.
* A jegyzetfüzet futtatásához.
* A naplózott hiba értékeinek megtekintése a munkaterületén.  Ez a példa bemutatja, hogyan segíthet a munkaterület a szkriptben létrehozott információk nyomon követésében. 

Részletes munkafolyamat élményt biztosít betanítása és a modell üzembe helyezése a Machine Learning-oktatóanyagokat követve:  

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy rendszerkép osztályozási modell betanítása](tutorial-train-models-with-aml.md)
