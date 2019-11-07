---
title: 'Oktatóanyag: az első ML-kísérlet létrehozása: telepítés'
titleSuffix: Azure Machine Learning
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Jupyter-jegyzetfüzetekben futó Azure Machine Learning Python SDK-t.  Az 1. részben létrehoz egy munkaterületet, amelyben a kísérleteket és a ML-modelleket fogja kezelni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: 59844c5989abb03bbe5134c83c9653290c17d0a9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582494"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Oktatóanyag: Ismerkedés az első ML-kísérlettel a Python SDK-val
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az oktatóanyagban a teljes körű lépéseket követve megkezdheti a Jupyter-jegyzetfüzetekben futó Azure Machine Learning Python SDK megkezdését. Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat első része**, amely a Python környezet beállítását és konfigurálását, valamint a kísérletek és a gépi tanulási modellek kezelésére szolgáló munkaterület létrehozását ismerteti. A második [**rész**](tutorial-1st-experiment-sdk-train.md) erre épít több gépi tanulási modell betanításához és a modell-felügyeleti folyamat bevezetéséhez a Azure Machine learning Studio és az SDK használatával.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy [Azure Machine learning-munkaterület](concept-workspace.md) , amelyet a következő oktatóanyagban szeretne használni.
> * Az oktatóanyagok notebookjának klónozása a munkaterületen lévő mappába.
> * Hozzon létre egy felhőalapú notebook-alapú virtuális gépet Azure Machine Learning Python SDK-val, és telepítse azt előre konfigurálva.


Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning munkaterület a felhőben található alapvető erőforrás, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használható. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi a szolgáltatásban. 

A munkaterületet az Azure-erőforrások kezeléséhez használható webalapú konzolon Azure Portal segítségével hozhatja létre. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Jegyezze fel a **munkaterületet** és az **előfizetést**. Ezekre azért van szükség, hogy a megfelelő helyen hozza létre a kísérletet. 


## <a name="azure"></a>Jegyzetfüzet-mappa klónozása

Ez a példa a Felhőbeli notebook-kiszolgálót használja a munkaterületen a telepítés ingyenes és előre konfigurált felületén. [Saját környezetét](how-to-configure-environment.md#local) használhatja, ha a környezetét, a csomagokat és a függőségeket szeretné vezérelni.

A következő kísérletet a Azure Machine Learning Studióban, egy összevont felületen végezheti el, amely magában foglalja a gépi tanulási eszközöket, amelyekkel adatelemzési forgatókönyvek végezhetők el az összes képzettségi szinthez tartozó adatelemző szakemberek számára.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/).

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

1. Válassza a bal oldali **jegyzetfüzetek** lehetőséget.

1. Nyissa meg a **Samples** mappát.

1. Nyissa meg a **Python** mappát.

1. Nyissa meg a mappát egy verziószámmal.  Ez a szám a Python SDK jelenlegi kiadását jelöli.

1. Válassza a **"..."** lehetőséget az **oktatóanyagok** mappa jobb oldalán, majd válassza a **klónozás**elemet.

    ![Klónozási mappa](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. A mappák listája megjeleníti a munkaterülethez hozzáférő összes felhasználót.  Válassza ki azt a mappát, amelybe az **oktatóanyagok** mappát el szeretné klónozott.

## <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">a klónozott jegyzetfüzet megnyitása

1. A **felhasználói fájlok** területen nyissa meg a mappát, majd nyissa meg a klónozott **oktatóanyagok** mappát.

    ![Oktatóanyagok mappa megnyitása](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > A jegyzetfüzeteket megtekintheti a **Samples** mappában, de onnan nem futtathat jegyzetfüzetet.  Jegyzetfüzet futtatásához győződjön meg arról, hogy a **felhasználói fájlok** szakaszban megnyitta a jegyzetfüzet klónozott verzióját.
    
1. Válassza ki a **tutorial-1st-Experiment-SDK-Train. ipynb** fájlt az **oktatóanyagok** mappában.

1. A felső sávon válassza ki a notebook futtatásához használni kívánt notebook virtuális gépet. Ezek a virtuális gépek előre konfigurálva vannak a Azure Machine Learning futtatásához szükséges összes értékkel. Kiválaszthat egy virtuális gépet, amelyet a munkaterület bármely felhasználója létrehoz. 

1. Ha nem találhatók virtuális gépek, válassza a **+ számítás hozzáadása** elemet a virtuális gép létrehozásához.

    1. Virtuális gép létrehozásakor adjon meg egy nevet.  A névnek 2 és 16 karakter közöttinek kell lennie. Az érvényes karakterek betűk, számjegyek és karakterek, és az Azure-előfizetésben is egyedinek kell lenniük.

    1. Ezután kattintson a **Létrehozás** elemre. A virtuális gép beállítása körülbelül 5 percet vesz igénybe.

1. Miután a virtuális gép elérhetővé válik, megjelenik a felső eszköztáron.  Most már futtathatja a jegyzetfüzetet az eszköztáron az **összes futtatása** lehetőséggel, vagy a jegyzetfüzet kódjának **SHIFT + ENTER** billentyűkombinációjának használatával.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következő feladatokat végezte el:

* Létrehozott egy Azure Machine Learning munkaterületet.
* Létrehozott és konfigurált egy felhőalapú notebook-kiszolgálót a munkaterületen.

Az oktatóanyag **második részében** a `tutorial-1st-experiment-sdk-train.ipynb` kódot futtatva elvégezheti a Machine learning-modellek betanítását. 

> [!div class="nextstepaction"]
> [Oktatóanyag: az első modell betanítása](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Ha nem tervezi az oktatóanyag vagy más oktatóanyagok 2. részét, akkor [állítsa le a Felhőbeli notebook Server-alapú virtuális gépet](tutorial-1st-experiment-sdk-train.md#clean-up-resources) , ha nem használja azt a Cost csökkentése érdekében.


