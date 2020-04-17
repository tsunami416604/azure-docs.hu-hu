---
title: Webszolgáltatás bemenete/kimenete
description: A webszolgáltatás-modulok megismerése az Azure Machine Learning designerben (előzetes verzió)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462086"
---
# <a name="web-service-inputoutput"></a>Webszolgáltatás bemenete/kimenete

Ez a cikk ismerteti **a webszolgáltatás bemeneti** modul és **a webszolgáltatás kimeneti** modul az Azure Machine Learning designer (előzetes verzió).

**A WebService Bemeneti** modul csak **DataFrameDirectory**típusú bemeneti porthoz tud csatlakozni. A **Web Service Output** modul pedig csak **a DataFrameDirectory**típusú kimeneti portról csatlakoztatható. A két modul a modulfában, a **Webszolgáltatás** kategóriában található. 

**A webszolgáltatás-beviteli** modul jelzi, hogy a felhasználói adatok hol kerülnek be a folyamatba, és a **webszolgáltatás kimeneti** modulja jelzi, hogy a felhasználói adatok hol adják vissza a valós idejű következtetési folyamatot.

## <a name="how-to-use-web-service-inputoutput"></a>A webszolgáltatás bemeneti/kimeneti szolgáltatásának használata

- Amikor valós idejű következtetési folyamatot hoz létre a betanítási folyamatból, a **webszolgáltatás bemeneti** és **webszolgáltatás-kimeneti** modul automatikusan hozzáadódik, hogy megmutassa, hol kerülnek be a felhasználói adatok a folyamatba, és hol adják vissza az adatokat. 

    További információ [a valós idejű következtetési folyamat létrehozásáról.](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)

    > [!NOTE]
    > A valós idejű következtetési folyamat automatikus létrehozása egy szabályalapú legjobb erőfeszítési folyamat, nincs garancia a helyességre. A követelményeknek megfelelően manuálisan is hozzáadhat vagy eltávolíthat **webszolgáltatás-bemeneti/kimeneti** modulokat. Győződjön meg arról, hogy legalább egy **webszolgáltatás-bemeneti** modul és egy **webszolgáltatás kimeneti** modul a valós idejű következtetési folyamat. Ha több **webszolgáltatás-bemeneti** vagy **webszolgáltatás-kimeneti** modullal rendelkezik, győződjön meg arról, hogy azok egyedi nevekkel rendelkeznek, amelyeka nevet a modul jobb paneljén adhatják meg.

- Manuálisan is létrehozhat egy valós idejű következtetési folyamatot **webszolgáltatás bemeneti** és **webszolgáltatás kimeneti** moduljainak hozzáadásával a be nem küldött folyamathoz.

    > [!NOTE]
    >  A folyamat típusa az első beküldéskor lesz meghatározva. Ezért győződjön meg róla, hogy adja hozzá **a webszolgáltatás bemeneti** és **webszolgáltatás kimeneti** modul, mielőtt elküldi az első alkalommal, ha azt szeretné, hogy hozzon létre egy valós idejű következtetési folyamat.

   Az alábbi példa bemutatja, hogyan hozhat létre manuálisan valós idejű következtetési **folyamatot a Python-parancsfájl-modul végrehajtásából.** 

   ![Példa](media/module/web-service-input-output-example.png)
   
   Miután elküldte a folyamatot, és a futtatás sikeresen befejeződött, a valós idejű végpont üzembe helyezéséhez.
   
   > [!NOTE]
   >  A fenti példában adja meg az **adatok manuálisan** biztosítja az adatsémát a webszolgáltatás bemenet, és szükséges a valós idejű végpont üzembe helyezéséhez. Általában mindig csatlakoztasson egy modult vagy adatkészletet ahhoz a porthoz, amelyhez a **WebService Input** csatlakozik az adatséma biztosításához.
   
## <a name="next-steps"></a>További lépések
További információ [a valós idejű végpont üzembe helyezéséről.](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)
Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md)