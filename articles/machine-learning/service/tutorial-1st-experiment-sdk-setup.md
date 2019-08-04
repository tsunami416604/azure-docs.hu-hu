---
title: 'Oktatóanyag: Környezet és munkaterület beállítása.'
titleSuffix: Azure Machine Learning service
description: Ebben az oktatóanyag-sorozatban elvégezheti a teljes körű lépéseket a Jupyter-jegyzetfüzetekben futó Azure Machine Learning Python SDK megkezdéséhez.  Az első rész egy felhőalapú notebook-kiszolgáló környezet létrehozását, valamint a kísérletek és a gépi tanulási modellek kezelésére szolgáló munkaterület létrehozását ismerteti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: baeb175c57573e557d298ff3197394ab619434ff
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772651"
---
# <a name="tutorial-setup-environment-and-workspace"></a>Oktatóanyag: Telepítési környezet és munkaterület

Ebben az oktatóanyagban a teljes körű lépéseket követve megkezdheti a Jupyter-jegyzetfüzetekben futó Azure Machine Learning Python SDK megkezdését. Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat első része**, amely a Python környezet beállítását és konfigurálását, valamint a kísérletek és a gépi tanulási modellek kezelésére szolgáló munkaterület létrehozását ismerteti. A második [**rész**](tutorial-1st-experiment-sdk-train.md) erre épít több gépi tanulási modell betanításához, és a Azure Portal és az SDK használatával is bevezeti a modell felügyeleti folyamatát.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy Machine learning-munkaterületet, amelyet a következő oktatóanyagban használhat.
> * Hozzon létre egy jegyzetfüzet virtuális gépet, amely egy felhőalapú Jupyter notebook-kiszolgáló Azure Machine Learning Python SDK-val előre telepítve és előre konfigurálva.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag egyetlen előfeltétele egy Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

A munkaterület olyan alapszintű erőforrás a felhőben, amelyet a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használ. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi az SDK-ban. Ha már rendelkezik Azure Machine Learning szolgáltatás munkaterülettel, ugorjon a [következő szakaszra](#azure). Ellenkező esetben hozzon létre egyet most.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Felhőalapú notebook-kiszolgáló létrehozása

Ez a példa a Felhőbeli notebook-kiszolgálót használja a munkaterületen a telepítés ingyenes és előre konfigurált felületén. [Saját környezetét](how-to-configure-environment.md#local) használhatja, ha a környezetét, a csomagokat és a függőségeket szeretné vezérelni.

A munkaterületről hozzon létre egy felhőalapú erőforrást a Jupyter-jegyzetfüzetek használatának megkezdéséhez. Ez az erőforrás egy felhőalapú Linux rendszerű virtuális gép, amely minden Azure Machine Learning szolgáltatás futtatásához szükséges.

1. Nyissa meg a munkaterületet a [Azure Portalban](https://portal.azure.com/).  Ha nem tudja, hogyan keresse meg a munkaterületét a portálon, tekintse meg a [munkaterület](how-to-manage-workspace.md#view)megkeresése című témakört.

1. A Azure Portal munkaterület lapján válassza a bal oldalon található **notebook virtuális gépek** elemet.

1. A notebook virtuális gép létrehozásához válassza az **+ új** lehetőséget.

     ![Új virtuális gép kiválasztása](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Adja meg a virtuális gép nevét. Ezután kattintson a **Létrehozás** elemre.

    > [!NOTE]
    > A notebook virtuális gép nevének 2 – 16 karakter közöttinek kell lennie. Az érvényes karakterek a betűk, a számok és a karakter.  A névnek egyedinek kell lennie az Azure-előfizetésen belül is.

    ![Új virtuális gép létrehozása](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Várjon, amíg az állapot **futni**nem változik.

### <a name="launch-jupyter-web-interface"></a>A Jupyter webes felületének elindítása

A virtuális gép futása után a **notebook virtuális gépek** szakasz használatával indítsa el a Jupyter webes felületét.

1. A virtuális gép **URI** oszlopában válassza a **Jupyter** lehetőséget.

    ![A Jupyter notebook-kiszolgáló elindítása](./media/quickstart-run-cloud-notebook/start-server.png)

    A hivatkozás elindítja a notebook-kiszolgálót, és megnyitja a Jupyter notebook weboldalát egy új böngésző lapon.  Ez a hivatkozás csak a virtuális gépet létrehozó személy számára fog működni. A munkaterület minden felhasználójának létre kell hoznia a saját virtuális gépet.

1. A Jupyter notebook weboldalán a legfelső mappanév a felhasználónevét adja meg.  Válassza ki ezt a mappát.

    > [!TIP]
    > Ez a mappa a saját munkaterületén található [Storage](concept-workspace.md#resources) -tárolóban, és nem a notebook virtuális gépen van.  Törölheti a notebook virtuális gépet, és továbbra is megőrizheti a munkáját.  Amikor később hoz létre egy új jegyzetfüzet virtuális gépet, a rendszer ezt a mappát fogja betölteni. Ha a munkaterületet másokkal is megosztja, látni fogja a mappát, és látni fogja a sajátját.

1. Nyissa `samples-*` meg az alkönyvtárat `tutorials/tutorial-1st-experiment-sdk-train.ipynb` , majd nyissa meg az oktatóanyag **második részének** futtatásához.

## <a name="end"></a>Erőforrások törlése

Ne hajtsa végre ezt a szakaszt, ha továbbra is az oktatóanyag **2. részét** tervezi.

### <a name="stop-the-notebook-vm"></a>A notebook virtuális gép leállítása

Ha Felhőbeli jegyzetfüzet-kiszolgálót használt, állítsa le a virtuális gépet, ha nem használja a költségeket.

1. A munkaterületen válassza a **notebook virtuális gépek**lehetőséget.

   ![A virtuális gép kiszolgálójának leállítása](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Válassza ki a virtuális gépet a listából.

1. Válassza a **Leállítás**lehetőséget.

1. Ha készen áll a kiszolgáló ismételt használatára, válassza az **Indítás**lehetőséget.

### <a name="delete-everything"></a>Mindent törölni

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következő feladatokat végezte el:

* Létrehozott egy Azure Machine Learning szolgáltatás munkaterületet.
* Létrehozott és konfigurált egy felhőalapú notebook-kiszolgálót a munkaterületen.

Folytassa az oktatóanyag **2** . részével egy egyszerű gépi tanulási modell betanításához.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az első modell betanítása](tutorial-1st-experiment-sdk-train.md)
