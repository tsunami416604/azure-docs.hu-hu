---
title: Batch-előrejelzések futtatása Azure Machine Learning Designer használatával (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan taníthat ki egy modellt, és hogyan állíthat be egy batch-előrejelzési folyamatot a tervező használatával. Telepítse a folyamatot paraméteres webszolgáltatásként, amely bármely HTTP-könyvtárból indítható el.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/19/2019
ms.custom: Ignite2019
ms.openlocfilehash: 8d80282044adfa723940aa6f68efc1e719e713c0
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533166"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Batch-előrejelzések futtatása Azure Machine Learning Designer használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a útmutatóban megismerheti, hogyan végezheti el a modell betanítását és a Batch-előrejelzési folyamat és webszolgáltatás beállítását. A Batch-előrejelzés lehetővé teszi a nagy adatkészleteken futó betanított modellek folyamatos és igény szerinti pontozását, amelyet opcionálisan konfigurálhat bármely HTTP-könyvtárból indítható webszolgáltatásként. 

A Batch-pontozási szolgáltatások SDK használatával történő beállításához tekintse meg a kapcsolódó [útmutató](how-to-run-batch-predictions.md)című témakört.

Ebben az útmutatóban a következő feladatokat ismerheti meg:

> [!div class="checklist"]
> * Alapszintű ML-kísérlet létrehozása egy folyamatban
> * Paraméteres batch-következtetési folyamat létrehozása
> * Folyamatok manuális vagy REST-végpontból történő kezelése és futtatása

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

1. Hozzon létre egy [munkaterületet](tutorial-1st-experiment-sdk-setup.md).

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/).

Ez az útmutató azt feltételezi, hogy alapszintű ismerettel rendelkezik egy egyszerű folyamat létrehozásához a tervezőben. A Designer bevezetéséhez fejezze be az [oktatóanyagot](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Batch-következtetési folyamat létrehozásához először gépi tanulási kísérletre van szükség. Hozzon létre egyet a munkaterület **tervező** lapján, és hozzon létre egy új folyamatot a **könnyen használható előre elkészített modulok** lehetőség kiválasztásával.

![Tervező kezdőlapja](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-1.png)

A következő egy egyszerű gépi tanulási modell demonstrációs célokra. Az adatok egy olyan regisztrált adatkészlet, amely az Azure Open adatkészletek cukorbetegség-adatainak alapján lett létrehozva. Az adatkészletek Azure Open-adatkészletekben való regisztrálásával kapcsolatban lásd: [útmutató szakasz](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) . Az adat felosztása képzésre és érvényesítési csoportokra történik, és a megnövelt döntési fa betanítása és kiértékelése megtörténik. A folyamatnak legalább egyszer futtatnia kell a következtetési folyamat létrehozásához. A folyamat futtatásához kattintson a **Futtatás** gombra.

![Egyszerű kísérlet létrehozása](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>Batch-következtetési folyamat létrehozása

Most, hogy a folyamat le lett futtatva, a **Futtatás** és a **Közzététel** a **create következtetési folyamat létrehozása**nevű új lehetőség áll rendelkezésre. Kattintson a legördülő listára, és válassza a **Batch következtetési folyamat**lehetőséget.

![Batch-következtetési folyamat létrehozása](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-5.png)

Az eredmény egy alapértelmezett batch-következtetési folyamat. Ez magában foglalja az eredeti folyamat-kísérlet beállítását, egy csomópontot a pontozáshoz, és egy csomópontot, amely az eredeti folyamat alapján szerzi be a nyers adatait.

![Alapértelmezett batch-következtetési folyamat](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-6.png)

További csomópontokat is hozzáadhat a Batch-következtetési folyamat működésének módosításához. Ebben a példában egy csomópontot ad hozzá a bemeneti adatok véletlenszerű mintavételezéséhez a pontozás előtt. Hozzon létre egy **partíciót és egy minta** csomópontot, és helyezze el a nyers adat-és pontozási csomópontok között. Ezután kattintson a **partícióra és a minta** csomópontra, hogy hozzáférjen a beállításokhoz és a paraméterekhez.

![Új csomópont](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-7.png)

A *mintavételi paraméterek gyakorisága* határozza meg, hogy az eredeti adathalmaz hány százalékát használja a rendszer a véletlenszerűen kiválasztott értékre. Ez egy olyan paraméter, amely hasznos lehet a gyakori módosításhoz, így azt egy folyamat paraméterként is engedélyezheti. A folyamat paraméterei futásidőben módosíthatók, és egy adattartalom-objektumban adhatók meg, ha a folyamat újbóli futtatása REST-végpontról. 

Ha ezt a mezőt folyamat paraméterként szeretné engedélyezni, kattintson a mező felett lévő három pontra, majd kattintson a **Hozzáadás a folyamathoz paraméter**elemre. 

![Minta beállításai](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-8.png)

Ezután adja meg a paraméter nevét és alapértelmezett értékét. A név azonosítja a paramétert, és megadhatja azt egy REST-hívásban.

![Folyamat paramétere](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>Batch-következtetési folyamat üzembe helyezése

Most már készen áll a folyamat üzembe helyezésére. Kattintson a **telepítés** gombra, amely megnyitja a felületet egy végpont beállításához. Kattintson a legördülő listára, és válassza az **új PipelineEndpoint**elemet.

![Folyamat üzembe helyezése](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-10.png)

Adja meg a végpont nevét és leírását (nem kötelező). Az alsó sarokban a 0,8 alapértelmezett értékével konfigurált `sample-rate` paraméter jelenik meg. Ha elkészült, kattintson a **telepítés**elemre.

![Telepítési végpont](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-11.png)

## <a name="manage-endpoints"></a>Végpontok kezelése 

Az üzembe helyezés befejezése után lépjen a **végpontok** lapra, és kattintson az imént létrehozott végpont nevére.

![Végponti hivatkozás](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-12.png)

Ezen a képernyőn látható az adott végpont alatti összes közzétett folyamat. Kattintson a következtetési folyamatra.

![Következtetési folyamat](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-13.png)

A folyamat részletei lap részletes futtatási előzményeket és kapcsolati karakterlánc-információkat jelenít meg a folyamathoz. A folyamat manuális futtatásának létrehozásához kattintson a **Futtatás** gombra.

![Folyamat részletei](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-14.png)

A Futtatás beállításban megadhatja a Futtatás leírását, és módosíthatja a folyamat paramétereinek értékét. Ezúttal újra kell futtatni a következtetési folyamatot, amelynek a mintavételi sebessége 0,9. Kattintson a **Futtatás** gombra a folyamat futtatásához.

![Folyamat futtatása](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-15.png)

A **felhasználás lapon a** folyamat újrafuttatásához használt Rest-végpont szerepel. A REST-hívások elvégzéséhez szüksége lesz egy OAuth 2,0 tulajdonos típusú hitelesítési fejlécre. A munkaterület hitelesítésének beállításával és a paraméteres REST-hívás létrehozásával kapcsolatos további részletekért tekintse meg a következő [oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) .

## <a name="next-steps"></a>Következő lépések

A regressziós modell betanításához és üzembe helyezéséhez kövesse a tervezői [oktatóanyagot](tutorial-designer-automobile-price-train-score.md) .
