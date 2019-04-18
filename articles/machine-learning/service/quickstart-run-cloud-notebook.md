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
ms.openlocfilehash: 0672d90a25bc4c879d28512ab212f98f29efbf3b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358214"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: Ismerkedés az Azure Machine Learning felhőalapú notebook server használatával

Ez a cikk az Azure-jegyzetfüzetek futtatásához használt kódot, amely be van jelentkezve az Azure Machine Learning szolgáltatás [munkaterület](concept-azure-machine-learning-architecture.md). A munkaterület az eligazodást blokk, amellyel kísérletezhet, betanítását és a Machine Learning gépi tanulási modellek üzembe helyezése a felhőben. 

Ez a rövid útmutató felhőerőforrásokat használ, és nem igényel telepítést. A saját környezetben használja, lásd: [a rövid útmutató: A saját notebook server használata az Azure Machine Learning használatának első lépései](quickstart-run-local-notebook.md).  
 
Ebben a rövid útmutatóban tegye a következőket:

* Csatlakozás a munkaterülethez, a Python egy Jupyter Notebookban. A notebook pi és naplók hibák megbecsülni, minden egyes ismétléskor kódját tartalmazza. 
* A naplózott hiba értékeinek megtekintése a munkaterületén.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisite"></a>Előfeltétel

1. [Az Azure Machine Learning-munkaterület létrehozása](setup-create-workspace.md#portal) Ha nem rendelkezik ilyennel.

1. Nyissa meg a munkaterület a [az Azure portal](https://portal.azure.com/).  Lásd: hogyan [keresse meg a munkaterület](how-to-manage-workspace.md#view).

## <a name="use-your-workspace"></a>Saját munkaterület használatára

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



Ismerje meg, hogyan munkaterület segítségével kezelheti a gépi tanulási parancsfájlok. Ebben a szakaszban hajtsa végre a következő lépéseket:

* Megnyit egy notebookot az Azure Notebooksban.
* Naplózott értékeket létrehozó kódot futtat.
* A naplózott értékek megtekintése a munkaterületen.

Ez a példa bemutatja, hogyan segíthet a munkaterület a szkriptben létrehozott információk nyomon követésében. 

### <a name="open-a-notebook"></a>Notebook megnyitása 

[Az Azure notebookok](https://notebooks.azure.com) ingyenes felhőalapú platformot nyújt a Machine Learning végrehajtásához, amit az előre konfigurált Jupyter-notebookok. A munkaterületen indítsa el a platform az Azure Machine Learning szolgáltatás munkaterület használatának megkezdéséhez.

1. A munkaterület áttekintése lapon válassza ki a **első lépései az Azure notebookok** szeretné kipróbálni az első kísérlet az Azure-jegyzetfüzeteket.  Az Azure notebookok egy külön szolgáltatás, amely lehetővé teszi, hogy a futtatása a Jupyter notebooks ingyenes a felhőben.  Erre a hivatkozásra a szolgáltatás használata esetén a munkaterülethez csatlakoztassa információ felveszi a notebookok Azure-ban létrehozott könyvtárba.

   ![Ismerje meg a munkaterület](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. Jelentkezzen be az Azure-jegyzetfüzeteket.  Ellenőrizze, hogy ugyanazt a fiókot, amellyel jelentkezzen be az Azure Portalra jelentkezik. A cég vagy az intézmény a bejelentkezéshez [rendszergazdai hozzájárulást](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) írhat elő.

1. A bejelentkezés után megnyílik egy új lap, és megjelenik egy `Clone Library` üzenet. Ebben a könyvtárban a Klónozás betölti a notebookok és egyéb fájlokat az Azure-jegyzetfüzet-fiókba.  Ezeket a fájlokat, Fedezze fel az Azure Machine Learning segítségével.

1. Törölje a jelet **nyilvános** úgy, hogy a munkaterület-információk nem megosztja másokkal.

1. Válassza ki **Klónozás**.

   ![Klónozza a tár](./media/quickstart-run-cloud-notebook/clone.png)

1. Ha látja, hogy a projekt állapot Leállítva, kattintson a **ingyenes számítógépen futtassa** az ingyenes notebook server használatához.

    ![Futtassa a projektet az ingyenes számítási](./media/quickstart-run-cloud-notebook/run-project.png)

### <a name="run-the-notebook"></a>A notebook futtatása

A projekthez tartozó fájlok listáját, megjelenik egy `config.json` fájlt. A konfigurációs fájlban, az Azure Portalon hozza létre a munkaterületet információkat tartalmaz.  Ez a fájl lehetővé teszi, hogy a kód való csatlakozáshoz, és adja meg adatait egyszerűen a munkaterületre.

1. Válassza ki **01.run-experiment.ipynb** a notebook megnyitásához.

1. Az állapot területen arra kéri, hogy Várjon, amíg a kernel megkezdődött.  Az üzenet eltűnik, ha a kernel készen áll.

    ![Várjon, amíg a kernel indítása](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. Miután elindult a kernel, futtassa a cellák egy egyszerre **Shift + Enter**. Válassza ki vagy **cellák** > **összes futtatása** a teljes jegyzetfüzet futtatásához. Amikor megjelenik egy csillag __*__, mellett egy cellába, továbbra is fut a cellát. A cella kódjának lezárulása után megjelenik egy szám. 

1. Kövesse a Notebookban lévő utasításokat hitelesítéséhez az Azure-előfizetésében.

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

Létrehozta a modellekkel való kísérletezéshez és azok üzembe helyezéséhez szükséges erőforrásokat. Kódot is futtatott egy notebookban, és áttekintette a kód futtatási előzményeit a felhőben lévő munkaterületen.

Részletes munkafolyamat élményt biztosít betanítása és a modell üzembe helyezése a Machine Learning-oktatóanyagokat követve:  

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy rendszerkép osztályozási modell betanítása](tutorial-train-models-with-aml.md)