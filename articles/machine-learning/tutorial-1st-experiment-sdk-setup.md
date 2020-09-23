---
title: 'Oktatóanyag: Jupyter-jegyzetfüzetek (Python) – első lépések'
titleSuffix: Azure Machine Learning
description: Jupyter Notebook oktatóanyagok beállítása.  Hozzon létre egy Azure Machine Learning munkaterületet, klónozott Jupyter notebookokat a munkaterületen, és hozzon létre egy számítási példányt, amelyen futtatja a jegyzetfüzeteket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: de52013628f5d02bedcf72a99e0fad25cabe5d8f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896864"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Oktatóanyag: első lépések a Azure Machine Learning Jupyter-jegyzetfüzetekben

Ebben az oktatóanyagban elvégezheti a Azure Machine Learning használatának első lépéseit a [felügyelt felhőalapú munkaállomás (számítási példány)](concept-compute-instance.md)Jupyter notebookok használatával. Ez az oktatóanyag az összes többi Jupyter Notebook oktatóanyag előfutára.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy [Azure Machine learning-munkaterület](concept-workspace.md) , amelyet más Jupyter notebook oktatóanyagokban használhat.
> * Az oktatóanyagok notebookjának klónozása a munkaterületen lévő mappába.
> * Hozzon létre egy felhőalapú számítási példányt Azure Machine Learning Python SDK-val telepített és előre konfigurált.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning munkaterület a felhőben található alapvető erőforrás, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használható. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi a szolgáltatásban.

A munkaterületet az Azure-erőforrások kezeléséhez használható webalapú konzolon Azure Portal segítségével hozhatja létre.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Jegyezze fel a **munkaterületet** és az **előfizetést**. Ezekre azért van szükség, hogy a megfelelő helyen hozza létre a kísérletet. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Jegyzetfüzet futtatása a munkaterületen

Azure Machine Learning tartalmaz egy felhőalapú notebook-kiszolgálót a munkaterületen a telepítés ingyenes és előre konfigurált felületén. [Saját környezetét](tutorial-1st-experiment-sdk-setup-local.md) használhatja, ha a környezetét, a csomagokat és a függőségeket szeretné vezérelni.

 Kövesse ezt a videót, vagy használja az alábbi részletes lépéseket az oktatóanyag-jegyzetfüzetnek a munkaterületről történő klónozásához és futtatásához.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Jegyzetfüzet-mappa klónozása

A következő kísérletet a Azure Machine Learning Studióban, egy összevont felületen végezheti el, amely magában foglalja a gépi tanulási eszközöket, amelyekkel adatelemzési forgatókönyvek végezhetők el az összes képzettségi szinthez tartozó adatelemző szakemberek számára.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/).

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

1. Válassza a bal oldali **jegyzetfüzetek** lehetőséget.

1. Válassza a felül található **minták** fület.

1. Nyissa meg a **Python** mappát.

1. Nyissa meg a mappát egy verziószámmal.  Ez a szám a Python SDK jelenlegi kiadását jelöli.

1. Válassza a **"..."** lehetőséget az **oktatóanyagok** mappa jobb oldalán, majd válassza a **klónozás**elemet.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Oktatóanyagok mappa klónozása":::

1. A mappák listája megjeleníti a munkaterülethez hozzáférő összes felhasználót.  Válassza ki azt a mappát, amelybe az **oktatóanyagok**  mappát el szeretné klónozott.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>A klónozott jegyzetfüzet megnyitása

1. Nyissa meg az **oktatóanyagok** mappát, amely éppen be lett zárva a **felhasználói fájlok** szakaszba.

    > [!IMPORTANT]
    > A jegyzetfüzeteket megtekintheti a **Samples** mappában, de onnan nem futtathat jegyzetfüzetet.  Jegyzetfüzet futtatásához győződjön meg arról, hogy a **felhasználói fájlok** szakaszban megnyitta a jegyzetfüzet klónozott verzióját.
    
1. Válassza ki a **tutorial-1st-Experiment-SDK-Train. ipynb** fájlt az **oktatóanyagok/rendszerkép-besorolás-mnist-adat** mappában.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Oktatóanyagok mappa megnyitása":::

1. A felső sávon válassza ki a jegyzetfüzet futtatásához használni kívánt számítási példányt. Ezek a virtuális gépek előre konfigurálva vannak a [Azure Machine learning futtatásához szükséges összes értékkel](concept-compute-instance.md#contents).

1. Ha nem található virtuális gép, válassza a **+ Hozzáadás** elemet a számítási példány virtuális gép létrehozásához. 

    1. Amikor létrehoz egy virtuális gépet, kövesse az alábbi szabályokat:  
        + A név megadása kötelező, és nem lehet üres.
        + A névnek egyedinek kell lennie (a kis-és nagybetűk megkülönböztetése nélkül) a munkaterület/számítási példány Azure-régiójában lévő összes meglévő számítási példányon. Ha a választott név nem egyedi, akkor riasztás jelenik meg.
        + Az érvényes karakterek a kis-és nagybetűk, a számok (0 – 9) és a kötőjel (-) karakter.
        + A névnek 3 – 24 karakter hosszúnak kell lennie.
        + A névnek betűvel kell kezdődnie (nem szám vagy kötőjel karakter).
        + Ha a kötőjel karaktert használja, a kötőjel után legalább egy betűt kell követnie. Példa: test-, test-0, test-01 érvénytelen, míg a test-a0, test-0a érvényes példányok.

    1.  Válassza ki a virtuális gép méretét a rendelkezésre álló lehetőségek közül. Az oktatóanyagok esetében az alapértelmezett virtuális gép jó választás.

    1. Ezután válassza a **Létrehozás** elemet. A virtuális gép beállítása körülbelül 5 percet vesz igénybe.

1. Miután a virtuális gép elérhetővé válik, megjelenik a felső eszköztáron.  Most már futtathatja a jegyzetfüzetet az eszköztáron az **összes futtatása** lehetőséggel, vagy a jegyzetfüzet kódjának **SHIFT + ENTER** billentyűkombinációjának használatával.

Ha egyéni widgetekkel rendelkezik, vagy inkább a Jupyter/JupyterLab-t használja, válassza a jobb szélen a **Jupyter** legördülő listát, majd válassza a **Jupyter** vagy a **JupyterLab**lehetőséget. Megnyílik az új böngészőablak.

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a fejlesztési környezetet, folytassa a modell betanítását Jupyter Notebookban:

> [!div class="nextstepaction"]
> [Oktatóanyag: képosztályozási modellek betanítása MNIST-adatokkal és scikit-Learn](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Ha nem tervezi a további oktatóanyagok követését, állítsa le a Felhőbeli notebook-kiszolgáló virtuális gépet, ha nem használja azt a Cost csökkentése érdekében:

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
