---
title: Erőforrás és kulcskezelési – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker szolgáltatást, előfizetési kulcsok és végpont kulcsokat kétféle foglalkozik.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c1d8104f94d3f215108000b904848f3b968492ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372834"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>A QnA Maker kulcsok kezelése

A QnA Maker szolgáltatás kezelje a kulcs, kétféle **előfizetési kulcsok** és **végpont kulcsok**.

![Kulcskezelés](../media/qnamaker-how-to-key-management/key-management.png)

1. **Előfizetői azonosítók**: Ezekkel a kulcsokkal való hozzáférést a [QnA Maker management szolgáltatás API-k](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Ezen API-k lehetővé teszik a Tudásbázis szerkesztéséhez.  

2. **Végpont kulcsok**: Ezekkel a kulcsokkal kapott választ a felhasználó kérdés Tudásbázis-végpontot. Általában akkor érdemes használnia ezt a végpontot a csevegőrobot, vagy az ügyfél alkalmazáskóddal, amelyhez a QnA Maker szolgáltatást használ fel.
 
## <a name="subscription-keys"></a>Előfizetői azonosítók
Megtekintheti, és az előfizetési kulcsok az Azure Portalon, ahol létrehozta a QnA Maker erőforrás alaphelyzetbe állítása. 
1. Nyissa meg a QnA Maker erőforrás az Azure Portalon.

    ![Rendszererőforrás-lista a QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Lépjen a **kulcsok**.

    ![Előfizetői azonosító](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Végpont kulcsok

Végpont kulcsok kezelhető a [QnA Maker portal](https://qnamaker.ai).

1. Jelentkezzen be a [QnA Maker portal](https://qnamaker.ai), nyissa meg a profil, és kattintson **Szolgáltatásbeállítások**.

    ![Végponti kulcs](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Megtekintheti, vagy visszaállíthatja a kulcsokat.

    ![Endpoint-kezelő](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Frissítse a kulcsokat, ha úgy érzi, hogy sérült a biztonsága. Ez lehet szükség az ügyfélalkalmazás vagy a robot kód a megfelelő módosításokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása egy másik nyelven](./language-knowledge-base.md)
