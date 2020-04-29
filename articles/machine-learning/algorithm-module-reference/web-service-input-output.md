---
title: Webszolgáltatás bemenete/kimenete
description: Tudnivalók a Azure Machine Learning Designer webszolgáltatási moduljairól (előzetes verzió)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462086"
---
# <a name="web-service-inputoutput"></a>Webszolgáltatás bemenete/kimenete

Ez a cikk a **webszolgáltatások bemeneti** modulját és a **webszolgáltatás kimeneti** modulját ismerteti Azure Machine learning Designerben (előzetes verzió).

A **webszolgáltatás bemeneti** modulja csak **DataFrameDirectory**típusú bemeneti porthoz tud csatlakozni. A **webszolgáltatás kimeneti** modulja csak **DataFrameDirectory**típusú kimeneti portról csatlakoztatható. A két modul a modul fájában, a **webszolgáltatás** kategóriában található. 

A **webszolgáltatás bemeneti** modulja azt jelzi, hogy a felhasználói adatok hol jutnak be a folyamatba, és a **webszolgáltatás kimeneti** modulja azt jelzi, hogy a rendszer hol adja vissza a felhasználói adatokat egy valós idejű következtetési folyamat során.

## <a name="how-to-use-web-service-inputoutput"></a>A webszolgáltatás bemenetének/kimenetének használata

- Amikor valós idejű következtetési folyamatot hoz létre a betanítási folyamatból, a **webszolgáltatás bemeneti** és **webszolgáltatás-kimeneti** modulját a rendszer automatikusan hozzáadja annak megjelenítéséhez, hogy a felhasználói adatok hol lépnek be a folyamatba, és hol adja vissza az adatokat. 

    További információ a [valós idejű következtetések létrehozásának folyamatáról](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > A valós idejű következtetések automatikus generálása egy szabályon alapuló, legalkalmasabb folyamat, amely nem garantálja a megfelelő pontosságot. A követelmények teljesítéséhez manuálisan is hozzáadhat vagy eltávolíthat **webszolgáltatás-bemeneti/kimeneti** modulokat. Győződjön meg arról, hogy van legalább egy **webszolgáltatás-bemeneti** modul és egy **webszolgáltatás kimeneti** modulja a valós idejű következtetési folyamatban. Ha több webszolgáltatás- **bemeneti** vagy webszolgáltatás- **kimeneti** modulja van, ügyeljen arra, hogy egyedi névvel rendelkezzenek, amelyekkel a modul jobb oldali paneljén adhatja meg a nevet.

- A valós idejű következtetéseket manuálisan is létrehozhatja a **webszolgáltatás bemeneti** és **webszolgáltatás-kimeneti** moduljainak a nem elküldött folyamathoz való hozzáadásával.

    > [!NOTE]
    >  A folyamat típusát az első elküldésekor határozza meg a rendszer. Ügyeljen arra, hogy a **webszolgáltatás bemeneti** és **webszolgáltatás-kimeneti** modulját először a beküldés előtt adja hozzá, ha valós idejű következtetési folyamatot szeretne létrehozni.

   Az alábbi példa bemutatja, hogyan hozhat létre manuálisan valós idejű következtetési folyamatot a **Python parancsfájl-modul végrehajtásával** . 

   ![Példa](media/module/web-service-input-output-example.png)
   
   Miután elküldte a folyamatot, és a Futtatás sikeresen befejeződött, a valós idejű végpontot is telepítheti.
   
   > [!NOTE]
   >  A fenti példában az **adatok manuális megadása** a webszolgáltatás bemeneti adatsémáját adja meg, és a valós idejű végpont üzembe helyezéséhez szükséges. Általában mindig csatlakoztatnia kell egy modult vagy adatkészletet ahhoz a porthoz, amelyhez a **webszolgáltatás bemenete** csatlakozik az Adatséma biztosításához.
   
## <a name="next-steps"></a>További lépések
További információ [a valós idejű végpont üzembe helyezéséről](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .