---
title: Batch-előrejelzések futtatása Azure Machine Learning Designer használatával (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan taníthat ki egy modellt, és hogyan állíthat be egy batch-előrejelzési folyamatot a tervező használatával. Telepítse a folyamatot paraméteres webszolgáltatásként, amely bármely HTTP-könyvtárból indítható el.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 01/13/2020
ms.custom: Ignite2019
ms.openlocfilehash: 7a4801e46477165232e7f03184152b6c277c05b6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167270"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Batch-előrejelzések futtatása Azure Machine Learning Designer használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan használhatja a tervezőt batch-előrejelzési folyamat létrehozásához. A Batch-előrejelzés lehetővé teszi, hogy folyamatosan nagy mennyiségű adatkészletet állítson be igény szerint egy olyan webszolgáltatással, amely bármely HTTP-tárból indítható.

Ebben az útmutatóban a következő feladatokat tanulhatja meg:

> [!div class="checklist"]
> * Batch-következtetési folyamat létrehozása és közzététele
> * Folyamat-végpont felhasználása
> * Végpontok verzióinak kezelése

A Batch-pontozási szolgáltatások SDK-val való beállításával kapcsolatos további információkért lásd a kapcsolódó [útmutató](how-to-run-batch-predictions.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató feltételezi, hogy már rendelkezik egy betanítási folyamattal. A tervező bevezetéséhez fejezze be [a tervezői oktatóanyag egyik részét](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-batch-inference-pipeline"></a>Batch-következtetési folyamat létrehozása

A betanítási folyamatnak legalább egyszer futnia kell, hogy létre lehessen hozni egy következtetési folyamatot.

1. Nyissa meg a **tervező** lapot a munkaterületen.

1. Válassza ki azt a betanítási folyamatot, amely a modellt az előrejelzéshez használni kívánja.

1. **Futtassa** a folyamatot.

    ![A folyamat futtatása](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Most, hogy a betanítási folyamat futott, létrehozhat egy batch-következtetési folyamatot.

1. A **Futtatás**elem mellett válassza az új legördülő lista **létrehozása következtetési folyamat**elemet.

1. Válassza a **Batch-következtetési folyamat**lehetőséget.

    ![Batch-következtetési folyamat létrehozása](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
Az eredmény egy alapértelmezett batch-következtetési folyamat. 

### <a name="add-a-pipeline-parameter"></a>Folyamat paraméterének hozzáadása

Az új adatokat tartalmazó előrejelzések létrehozásához manuálisan is összekapcsolhat egy másik adatkészletet a folyamat Piszkozat nézetében, vagy létrehozhat egy paramétert az adatkészlethez. A paraméterek lehetővé teszik a Batch-következtetési folyamat működésének módosítását futásidőben.

Ebben a szakaszban egy adatkészlet-paramétert hoz létre egy másik adatkészlet megadásához, amely alapján előrejelzéseket készít.

1. Válassza ki az adatkészlet modulját.

1. Ekkor megjelenik egy ablaktábla a vászon jobb oldalán. A panel alján válassza a **beállítás folyamatként paramétert**.
   
    Adja meg a paraméter nevét, vagy fogadja el az alapértelmezett értéket.

## <a name="publish-your-batch-inferencing-pipeline"></a>A Batch-következtetési folyamat közzététele

Most már készen áll a következtetési folyamat üzembe helyezésére. Ezzel a folyamattal üzembe helyezi a folyamatot, és elérhetővé teszi mások számára a használatra.

1. Válassza ki a **Közzététel** gombot.

1. A megjelenő párbeszédpanelen bontsa ki a **PipelineEndpoint**legördülő listát, és válassza az **új PipelineEndpoint**lehetőséget.

1. Adja meg a végpont nevét és leírását (nem kötelező).

    A párbeszédpanel alján látható, hogy a paramétert a betanítás során használt adatkészlet-azonosító alapértelmezett értékével konfigurálta.

1. Kattintson a **Publish** (Közzététel) elemre.

![Folyamat közzététele](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Végpont felhasználása

Most már van egy adatkészlet-paraméterrel rendelkező közzétett folyamata. A folyamat a betanítási folyamat során létrehozott betanított modellt fogja használni a paraméterként megadott adatkészlet kiértékeléséhez.

### <a name="submit-a-pipeline-run"></a>Folyamat futtatásának elküldése 

Ebben a szakaszban egy manuális folyamat futtatását kell beállítania, és módosítania kell a folyamat paramétert az új adatértékek kiértékeléséhez. 

1. Az üzembe helyezés befejezése után lépjen a **végpontok** szakaszra.

1. Válassza a **folyamat-végpontok**lehetőséget.

1. Válassza ki a létrehozott végpont nevét.

![Végponti hivatkozás](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Válassza a **közzétett folyamatok**elemet.

    Ezen a képernyőn a végpont alatt közzétett összes közzétett folyamat látható.

1. Válassza ki a közzétett folyamatot.

    A folyamat részletei lap részletes futtatási előzményeket és kapcsolati karakterlánc-információkat jelenít meg a folyamathoz. 
    
1. Válassza a **Futtatás** lehetőséget a folyamat manuális futtatásának létrehozásához.

    ![Folyamat részletei](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Módosítsa a paramétert egy másik adatkészlet használatára.
    
1. Válassza a **Futtatás** lehetőséget a folyamat futtatásához.

### <a name="use-the-rest-endpoint"></a>A REST-végpont használata

A folyamatok végpontjának és a közzétett folyamatnak a **végpontok** szakaszban való felhasználásával kapcsolatos információkat talál.

A folyamat végpontjának REST-végpontját a Futtatás Áttekintés panelen találja. A végpont meghívásával meghívja az alapértelmezett közzétett folyamatát.

A közzétett **folyamatok** oldalon is felhasználhatja a közzétett folyamatokat. Válasszon ki egy közzétett folyamatot, és keresse meg a hozzá tartozó REST-végpontot. 

![Rest-végpont részletei](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

A REST-hívások elvégzéséhez szüksége lesz egy OAuth 2,0 tulajdonos típusú hitelesítési fejlécre. A munkaterület hitelesítésének beállításával és a paraméteres REST-hívás létrehozásával kapcsolatos további részletekért tekintse meg a következő [oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) .

## <a name="versioning-endpoints"></a>Verziószámozási végpontok

A tervező egy verziót rendel hozzá minden további, a végponton közzétett folyamathoz. Megadhatja a folyamat azon verzióját, amelyet paraméterként kíván végrehajtani a REST-hívásban. Ha nem ad meg verziószámot, a tervező az alapértelmezett folyamatot fogja használni.

Amikor közzétesz egy folyamatot, megadhatja, hogy az új alapértelmezett folyamat legyen a végpont számára.

![Alapértelmezett folyamat beállítása](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Az új alapértelmezett folyamat a végpont **közzétett folyamatok** lapján is beállítható.

![Alapértelmezett folyamat beállítása](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Következő lépések

A regressziós modell betanításához és üzembe helyezéséhez kövesse a tervezői [oktatóanyagot](tutorial-designer-automobile-price-train-score.md) .
