---
title: Erőforrás és kulcskezelési – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker szolgáltatást, előfizetési kulcsok és végpont kulcsokat kétféle foglalkozik.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 9134819fc4610daadb617e123d861673e8cbfd32
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543438"
---
# <a name="key-management"></a>Kulcsok kezelése

A QnA Maker szolgáltatás kezelje a kulcs, kétféle **előfizetési kulcsok** és **végpont kulcsok**.

![Kulcskezelés](../media/qnamaker-how-to-key-management/key-management.png)

1. **Előfizetői azonosítók**: ezekkel a kulcsokkal való hozzáférést a [QnA Maker management szolgáltatás API-k](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Ezen API-k lehetővé teszik a Tudásbázis különböző CRUD-műveletek elvégzését.  

2. **Végpont kulcsok**: ezekkel a kulcsokkal kapott választ a felhasználó kérdés Tudásbázis-végpontot. Ezt a végpontot, amely a QnA Maker szolgáltatást használ fel Csevegés robot vagy alkalmazás kódját általában akkor érdemes használnia.
 
## <a name="subscription-keys"></a>Előfizetői azonosítók
Megtekintheti, és az előfizetési kulcsok az Azure Portalon, ahol létrehozta a QnA Maker erőforrás alaphelyzetbe állítása. 
1. Nyissa meg a QnA Maker erőforrás az Azure Portalon.

    ![Rendszererőforrás-lista a QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Lépjen a **kulcsok**.

    ![Előfizetői azonosító](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Végpont kulcsok

Végpont kulcsok kezelhető a [QnA Maker portal](https://qnamaker.ai).

1. Jelentkezzen be a [QnA Maker portal](https://qnamaker.ai), és nyissa meg **kulcsok kezelése**.

    ![Végponti kulcs](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Megtekintheti, vagy visszaállíthatja a kulcsokat.

    ![Endpoint-kezelő](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Frissítse a kulcsokat, ha úgy érzi, hogy sérült a biztonsága. Ez lehet szükség az alkalmazás vagy robot kód megfelelő módosításokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása egy másik nyelven](./language-knowledge-base.md)
