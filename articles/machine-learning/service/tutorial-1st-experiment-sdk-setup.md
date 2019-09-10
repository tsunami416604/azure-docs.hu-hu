---
title: 'Oktatóanyag: Hozza létre az első ML-kísérletét: Beállítás'
titleSuffix: Azure Machine Learning service
description: Ebben az oktatóanyag-sorozatban elvégezheti a teljes körű lépéseket a Jupyter-jegyzetfüzetekben futó Azure Machine Learning Python SDK megkezdéséhez.  Az első rész egy felhőalapú notebook-kiszolgáló környezet létrehozását, valamint a kísérletek és a gépi tanulási modellek kezelésére szolgáló munkaterület létrehozását ismerteti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 08/28/2019
ms.openlocfilehash: d968d6e799b75940d1fb73aa31c22eb84068df7d
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860421"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Oktatóanyag: Ismerkedés az első ML-kísérlet létrehozásával a Python SDK-val

Ebben az oktatóanyagban a teljes körű lépéseket követve megkezdheti a Jupyter-jegyzetfüzetekben futó Azure Machine Learning Python SDK megkezdését. Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat első része**, amely a Python környezet beállítását és konfigurálását, valamint a kísérletek és a gépi tanulási modellek kezelésére szolgáló munkaterület létrehozását ismerteti. A második [**rész**](tutorial-1st-experiment-sdk-train.md) erre épít több gépi tanulási modell betanításához, és a Azure Portal és az SDK használatával is bevezeti a modell felügyeleti folyamatát.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy [Azure Machine learning-munkaterület](concept-workspace.md) , amelyet a következő oktatóanyagban szeretne használni.
> * Hozzon létre egy felhőalapú Jupyter notebook virtuális gépet Azure Machine Learning Python SDK-val telepített és előre konfigurált.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning munkaterület a felhőben található alapvető erőforrás, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használható. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi az SDK-ban. Ha már rendelkezik Azure Machine Learning szolgáltatás munkaterülettel, ugorjon a [következő szakaszra](#azure). Ellenkező esetben hozzon létre egyet most.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Felhőalapú notebook-kiszolgáló létrehozása

Ez a példa a Felhőbeli notebook-kiszolgálót használja a munkaterületen a telepítés ingyenes és előre konfigurált felületén. [Saját környezetét](how-to-configure-environment.md#local) használhatja, ha a környezetét, a csomagokat és a függőségeket szeretné vezérelni.

A munkaterületről hozzon létre egy felhőalapú erőforrást a Jupyter-jegyzetfüzetek használatának megkezdéséhez. Ez az erőforrás egy felhőalapú Linux rendszerű virtuális gép, amely minden Azure Machine Learning szolgáltatás futtatásához szükséges.

1. Nyissa meg a munkaterületet a [Azure Portalban](https://portal.azure.com/).  Ha nem tudja, hogyan keresse meg a munkaterületét a portálon, tekintse meg a [munkaterület megkeresése](how-to-manage-workspace.md#view)című témakört.

1. A Azure Portal munkaterület lapján válassza a bal oldalon található **notebook virtuális gépek** elemet.

1. A notebook virtuális gép létrehozásához válassza az **+ új** lehetőséget.

     ![Új virtuális gép kiválasztása](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Adja meg a virtuális gép nevét. 
   + A notebook virtuális gép nevének 2 – 16 karakter közöttinek kell lennie. Az érvényes karakterek a betűk, a számok és a karakter.  
   + A névnek egyedinek kell lennie az Azure-előfizetésen belül is.

1. Ezután kattintson a **Létrehozás** elemre. A virtuális gép beállítása eltarthat egy kis ideig.

1. Várjon, amíg az állapot **futni**nem változik.
   A virtuális gép futása után a **notebook virtuális gépek** szakasz használatával indítsa el a Jupyter webes felületét.

1. A virtuális gép **URI** oszlopában válassza a **Jupyter** lehetőséget.

    ![A Jupyter notebook-kiszolgáló elindítása](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   A hivatkozás elindítja a notebook-kiszolgálót, és megnyitja a Jupyter notebook weboldalát egy új böngésző lapon.  Ez a hivatkozás csak a virtuális gépet létrehozó személy számára fog működni. A munkaterület minden felhasználójának létre kell hoznia a saját virtuális gépet.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következő feladatokat végezte el:

* Létrehozott egy Azure Machine Learning szolgáltatás munkaterületet.
* Létrehozott és konfigurált egy felhőalapú notebook-kiszolgálót a munkaterületen.

Az oktatóanyag **második részében** futtatja a kódot a Machine `tutorial-1st-experiment-sdk-train.ipynb` learning-modellek betanításához. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Az első modell betanítása](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Ha nem tervezi az oktatóanyag vagy más oktatóanyagok 2. részét, akkor [állítsa le a Felhőbeli notebook Server-alapú virtuális gépet](tutorial-1st-experiment-sdk-train.md#clean-up-resources) , ha nem használja azt a Cost csökkentése érdekében.


