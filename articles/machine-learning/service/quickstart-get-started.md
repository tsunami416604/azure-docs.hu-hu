---
title: Az Azure Portalon a rövid útmutató
titleSuffix: Azure Machine Learning service
description: Ismerkedés az Azure Machine Learning szolgáltatásban. Az Azure portal használatával hozzon létre egy munkaterületet, amely az alapvető blokk, amellyel kísérletezhet, betanítását és gépi tanulási modellek üzembe helyezése a felhőben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: f04cd2c6d3d4eff0cbc03ee57b9a57c6883db1d6
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239009"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: Az Azure Machine Learning használatának első lépései az Azure portal használatával

Az Azure Portalon létrehozhat egy Azure Machine Learning-munkaterületet. A Machine Learning használata során ez a munkaterület szolgál a gépi tanulási modellekkel való kísérletezés, valamint a betanításuk és üzembe helyezésük alapjául a felhőben. Ez a rövid útmutató felhőerőforrásokat használ, és nem igényel telepítést. Ehelyett saját Jupyter notebookok kiszolgáló beállítása: [a rövid útmutató: Az Azure Machine Learning használatának első lépései a Python használatával](quickstart-create-workspace-with-python.md).  
 
Ebben a rövid útmutatóban tegye a következőket:

* Munkaterület létrehozása az Azure-előfizetésben.
* Próbálja ki a Python egy Jupyter notebook és a naplófájlok értékeket több ismétléseinek között.
* A naplózott értékek megtekintése a munkaterületen.

A következő Azure-erőforrásokat a rendszer automatikusan hozzáadja a munkaterületéhez, ha az Ön régiójában rendelkezésre állnak:

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Azure Storage](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Az Ön által létrehozott erőforrások előfeltételként is használhatók a Machine Learning más szolgáltatási oktatóanyagaiban vagy útmutatóiban. A többi Azure-szolgáltatáshoz hasonlóan a Machine Learninghez társított egyes erőforrások korlátozva vannak. Ilyen például, a számítási fürt méretét. Tudjon meg többet a [alapértelmezett korlátok és a kvóta növelése](how-to-manage-quotas.md).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.


## <a name="create-a-workspace"></a>Munkaterület létrehozása 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]


## <a name="use-the-workspace"></a>A munkaterület használata

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



Most megtudhatja, hogyan munkaterület segít, a gépi tanulási parancsfájlok kezelését. Ebben a szakaszban hajtsa végre a következő lépéseket:

* Megnyit egy notebookot az Azure Notebooksban.
* Naplózott értékeket létrehozó kódot futtat.
* A naplózott értékek megtekintése a munkaterületen.

Ez a példa bemutatja, hogyan segíthet a munkaterület a szkriptben létrehozott információk nyomon követésében. 

### <a name="open-a-notebook"></a>Notebook megnyitása 

[Az Azure notebookok](https://notebooks.azure.com) ingyenes felhőalapú platformot nyújt a Machine Learning végrehajtásához, amit az előre konfigurált Jupyter-notebookok. A munkaterület már el is indíthatja a platform az Azure Machine Learning szolgáltatás munkaterület használatának megkezdéséhez.

1. A munkaterület oldalán válassza **Fedezze fel az Azure Machine Learning szolgáltatás munkaterület**.

 ![Ismerje meg a munkaterület](./media/quickstart-get-started/explore_aml.png)

1. Válassza ki **nyissa meg az Azure-jegyzetfüzetek** szeretné kipróbálni az első kísérlet az Azure-jegyzetfüzeteket.  Az Azure notebookok egy külön szolgáltatás, amely lehetővé teszi, hogy a futtatása a Jupyter notebooks ingyenes a felhőben.  Erre a hivatkozásra a szolgáltatás használata esetén a munkaterülethez csatlakoztassa információ felveszi a notebookok Azure-ban létrehozott könyvtárba.

 ![Az Azure Notebooks megnyitása](./media/quickstart-get-started/explore_ws.png)

1. Jelentkezzen be az Azure-jegyzetfüzeteket.  Ellenőrizze, hogy ugyanazt a fiókot, amellyel jelentkezzen be az Azure Portalra jelentkezik. A cég vagy az intézmény a bejelentkezéshez [rendszergazdai hozzájárulást](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) írhat elő.

1. A bejelentkezés után megnyílik egy új lap, és megjelenik egy `Clone Library` üzenet. Ebben a könyvtárban a Klónozás betölti a notebookok és egyéb fájlokat az Azure-jegyzetfüzet-fiókba.  Ezeket a fájlokat, Fedezze fel az Azure Machine Learning segítségével.

1. Törölje a jelet **nyilvános** úgy, hogy a munkaterület-információk nem megosztja másokkal.

1. Válassza ki **Klónozás**.

 ![Klónozza a tár](./media/quickstart-get-started/clone.png)

1. Ha látja, hogy a projekt állapot Leállítva, kattintson a **ingyenes számítási futtathatók** az ingyenes notebook server használatához.

    ![Futtassa a projektet az ingyenes számítási](./media/quickstart-get-started/run-project.png)

### <a name="run-the-notebook"></a>A notebook futtatása

A projekthez tartozó fájlok listáját, megjelenik egy `config.json` fájlt. A konfigurációs fájlban, az Azure Portalon hozza létre a munkaterületet információkat tartalmaz.  Ez a fájl lehetővé teszi, hogy a kód való csatlakozáshoz, és adja meg adatait egyszerűen a munkaterületre.

1. Válassza ki **01.run-experiment.ipynb** a notebook megnyitásához.

1. Az állapot területen arra kéri, hogy Várjon, amíg a kernel megkezdődött.  Az üzenet eltűnik, ha a kernel készen áll.

    ![Várjon, amíg a kernel indítása](./media/quickstart-get-started/wait-for-kernel.png)

1. Miután elindult a kernel, futtassa a cellák egy egyszerre **Shift + Enter**. Válassza ki vagy **cellák** > **összes futtatása** a teljes jegyzetfüzet futtatásához. Amikor megjelenik egy csillag __*__, mellett egy cellába, továbbra is fut a cellát. A cella kódjának lezárulása után megjelenik egy szám. 

1. Kövesse a Notebookban lévő utasításokat hitelesítéséhez az Azure-előfizetésében.

Ha végzett a jegyzetfüzet-ban futó összes cellát, megtekintheti a bejelentkezett értékek a munkaterületén.

## <a name="view-logged-values"></a>Naplózott értékek megtekintése

1. A kimenet a `run` cella vissza az Azure Portalon a kísérlet eredmények megtekintése a munkaterület mutató hivatkozást tartalmaz. 

    ![Kísérletek megtekintése](./media/quickstart-get-started/view_exp.png)

1. Kattintson a **Azure-portálra mutató hivatkozást** futtatás adatainak megtekintéséhez a munkaterületén.  Ez a hivatkozás megnyitja a munkaterületet az Azure Portalon.

1. A grafikon láthatja a naplózott értékek is automatikusan létrejön a munkaterületen. Mindig automatikusan létrejön egy diagram, amikor több értéket naplóz ugyanazzal a névparaméterrel.

   ![Előzmények megtekintése](./media/quickstart-get-started/web-results.png)

Mivel a kódot a hozzávetőleges pi véletlenszerű értéket használ, a grafikon különböző értékek jelennek meg.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Tartsa meg az erőforráscsoportot is, de egyetlen munkaterület törlése. A munkaterület tulajdonságainak megjelenítéséhez, és válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

Létrehozta a modellekkel való kísérletezéshez és azok üzembe helyezéséhez szükséges erőforrásokat. Kódot is futtatott egy notebookban, és áttekintette a kód futtatási előzményeit a felhőben lévő munkaterületen.

Részletes munkafolyamat élményt biztosít betanítása és a modell üzembe helyezése a Machine Learning-oktatóanyagokat követve:  

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy rendszerkép osztályozási modell betanítása](tutorial-train-models-with-aml.md)
