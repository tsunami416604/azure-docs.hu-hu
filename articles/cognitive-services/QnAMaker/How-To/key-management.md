---
title: Kulcskezelés - Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: A kérdések és válaszok készítő kulcsok kezelése
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b402187f4949dac34fa476648c81b980ba3efc96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348670"
---
# <a name="key-management"></a>Kulcsok kezelése

A kérdések és válaszok készítő szolgáltatás foglalkozik kulcsok, kétféle **előfizetés kulcsok** és **végpont kulcsok**.

![kulcskezelés](../media/qnamaker-how-to-key-management/key-management.png)

1. **Előfizetés kulcsok**: ezek a kulcsok használhatók hozzáférés a [kérdések és válaszok készítő felügyeleti szolgáltatás API-k](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Ezen API-k lehetővé teszik, hogy különböző CRUD műveletek végzése a Tudásbázis.  

2. **Végpont kulcsok**: ezek a kulcsok a Tudásbázis végpont kapott választ egy felhasználói kérdéshez eléréséhez használt. Ezt a végpontot a kódban Csevegés botot/alkalmazás, amely a kérdések és válaszok készítő szolgáltatás akkor általában használna.
 
## <a name="subscription-keys"></a>Előfizetés kulcsok
Megtekintheti és az Azure-portálról, amelyben létrehozta a kérdések és válaszok készítő erőforrás előfizetés-kulcsok alaphelyzetbe állítása. 
1. Ugrás a kérdések és válaszok készítő erőforrást az Azure portálon.

    ![Kérdések és válaszok készítő erőforrások listája](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ugrás a **kulcsok**.

    ![Előfizetés kulcs](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Végpont kulcsok

Végpont kulcsok kezelheti a [kérdések és válaszok készítő portal](https://qnamaker.ai).

1. Jelentkezzen be a [kérdések és válaszok készítő portal](https://qnamaker.ai), majd a **kulcsok kezelése**.

    ![végpontkulcs](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Megtekintheti, és a kulcsok alaphelyzetbe állítása.

    ![Endpoint-kezelő](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Ha úgy érzi, hogy azok biztonsága sérült, frissítse a kulcsokat. Ez lehet szükség az alkalmazás/Botot megfelelő módosítását.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy Tudásbázis más nyelven](./language-knowledge-base.md)
