---
title: 'Oktatóanyag: Az első pénzmosási kísérlet létrehozása'
titleSuffix: Azure Machine Learning
description: Ebben az oktatóanyagban a Jupyter-jegyzetfüzetekben futó Azure Machine Learning Python SDK-t fogja elkezdeni.  Az első részben hozzon létre egy munkaterületet, ahol kezelheti a kísérleteket és a hibaidő-modelleket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: a6f977c0cdca670b40ccdc01db64a493962e3dda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239888"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Oktatóanyag: Első lépések a Python SDK-val végzett első ML-kísérlet létrehozásához
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az oktatóanyagban a jupyter-jegyzetfüzetekben futó Azure Machine Learning Python SDK-val való ismerkedéshez végezze el a teljes körű lépéseket. Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat első része,** és a Python-környezet beállítását és konfigurációját ismerteti, valamint a kísérletek és a gépi tanulási modellek kezeléséhez munkaterületet hoz létre. [**A második rész**](tutorial-1st-experiment-sdk-train.md) erre épül, hogy több gépi tanulási modellt tanítson be, és bemutassa a modellkezelési folyamatot az Azure Machine Learning stúdió és az SDK használatával.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy [Azure Machine Learning-munkaterületet](concept-workspace.md) a következő oktatóanyagban való használatra.
> * Klónozza az oktatóanyagok jegyzetfüzetét a munkaterületi mappába.
> * Hozzon létre egy felhőalapú számítási példányt az Azure Machine Learning Python SDK telepítve és előre konfigurálva.


Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt elkezdené. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning-munkaterület egy alapvető erőforrás a felhőben, amely et a kísérletezés, a betanítás és a gépi tanulási modellek üzembe helyezése. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz köti a szolgáltatásban. 

Hozzon létre egy munkaterületet az Azure Portalon keresztül, egy webalapú konzol az Azure-erőforrások kezelésére. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Vegye figyelembe **munkaterületét** és **előfizetését.** Ezekre szüksége lesz, hogy a kísérletet a megfelelő helyen hozza létre. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Jegyzetfüzet futtatása a munkaterületen

Ez az oktatóanyag a munkaterület felhőalapú jegyzetfüzet-kiszolgálóját használja a telepítés nélküli és előre konfigurált élményhez. Használja [a saját környezetét,](how-to-configure-environment.md#local) ha szeretné, hogy a környezet, a csomagok és a függőségek felett.

Kövesse ezt a videót, vagy használja az alábbi részletes lépéseket az oktatóanyag klónozásához és futtatásához a munkaterületről. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]



### <a name="clone-a-notebook-folder"></a>Jegyzetfüzetmappa klónozása

A következő kísérletbeállítási és -futtatási lépéseket az Azure Machine Learning-stúdióban, egy összevont felületen futtathatja, amely gépi tanulási eszközöket tartalmaz az adatelemzési forgatókönyvek végrehajtásához az adatelemzési szakemberek számára, minden képzettségi szinten.

1. Jelentkezzen be az [Azure Machine Learning stúdióba.](https://ml.azure.com/)

1. Válassza ki az előfizetést és a létrehozott munkaterületet.

1. Válassza a bal oldalon a **Jegyzetfüzetek** lehetőséget.

1. Nyissa **meg** a Minták mappát.

1. Nyissa meg a **Python** mappát.

1. Nyissa meg azt a mappát, amelyen verziószám van.  Ez a szám a Python SDK aktuális kiadását jelöli.

1. Válassza ki a **"..."** a jobb oldalon az **oktatóanyagok mappát,** majd válassza **a Klónozás**.

    ![Klónozott mappa](./media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. A munkaterületet elérő felhasználók listáját jeleníti meg a mappák listája.  Válassza ki a mappát klónozni az **oktatóanyagok mappát.**

### <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">A klónozott jegyzetfüzet megnyitása

1. A **Felhasználói fájlok csoportban** nyissa meg a mappát, majd nyissa meg a klónozott **oktatóanyagok mappát.**

    ![Oktatóanyagok mappa megnyitása](./media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > A **minták** mappában megtekintheti a jegyzetfüzeteket, de onnan nem futtathat jegyzetfüzeteket.  A jegyzetfüzet futtatásához győződjön meg arról, hogy a jegyzetfüzet klónozott verzióját a Felhasználói fájlok szakaszban **nyitja** meg.
    
1. Válassza ki a **tutorial-1st-experiment-sdk-train.ipynb** fájlt az **oktató/create-first-ml-kísérlet** mappában.

1. A felső sávon válassza ki a jegyzetfüzet futtatásához használni kívánt számítási példányt. Ezek a virtuális gépek előre konfigurálva vannak [mindennel, amire szüksége van az Azure Machine Learning futtatásához.](concept-compute-instance.md#contents) Kiválaszthatja a munkaterület bármely felhasználója által létrehozott virtuális gép. 

1. Ha nem található virtuális gép, válassza a **+ Add lehetőséget** a számítási példány virtuális gépének létrehozásához. 

    1. Amikor létrehoz egy virtuális gép, adjon meg egy nevet.  A névnek 2 és 16 karakter között kell lennie. Az érvényes karakterek a betűk, a számjegyek és a - karakter, és az Azure-előfizetésben is egyedinek kell lenniük.

    1.  Válassza ki a virtuális gép méretét a rendelkezésre álló lehetőségek közül.

    1. Ezután válassza **a Létrehozás lehetőséget.** A virtuális gép beállítása körülbelül 5 percet is igénybe vehet.

1. Miután a virtuális gép elérhetővé válik, megjelenik a felső eszköztáron.  A jegyzetfüzetet most már futtathatja az eszköztár **Összes futtatása,** illetve a jegyzetfüzet kódcelláiban lévő **Shift+Enter** billentyűkombinációval.

Ha van egyéni kütyü, vagy inkább a Jupyter / JupyterLab válassza ki a **Jupyter** legördülő a jobb szélen, majd válassza **a Jupyter** vagy **JupyterLab**. Megnyílik az új böngészőablak.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az alábbi feladatokat hajtotta végre:

* Létrehozott egy Azure Machine Learning-munkaterületet.
* Létrehozott és konfigurált egy felhőalapú jegyzetfüzet-kiszolgálót a munkaterületen.

Az oktatóanyag **második részében** `tutorial-1st-experiment-sdk-train.ipynb` futtatja a kódot egy gépi tanulási modell betanításához. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Az első modell betanítása](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Ha nem tervezi a következő 2. [stop the cloud notebook server VM](tutorial-1st-experiment-sdk-train.md#clean-up-resources)


