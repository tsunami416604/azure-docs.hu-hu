---
title: Hozzon létre egy Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: A robot chit csevegési hozzáadása megkönnyíti több természetes nyelvi és vonzóbbá tehetik. A QnA Maker egyszerűen adja hozzá a felső chit csevegési, előre megadott készlete a KB-os teszi lehetővé. Ez a robot chit csevegési a kiindulási pont lehet, és időt takaríthat meg a teljesen új rögzíti őket költsége.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 274c2289c75f44c5a1c8dd3799612a23f46a6d67
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037694"
---
# <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A QnA Maker egyszerűen lehet például egy Tudásbázis létrehozásakor adja hozzá a meglévő adatforrásokat. A QnA Maker új Tudásbázis hozhat létre a következő dokumentum-típusok közül:

<!-- added for scanability -->
* Gyakori kérdéseket tartalmazó oldalak
* Termékek kézikönyvek
* Strukturált dokumentumok

## <a name="steps"></a>Lépések

1. Jelentkezzen be a [QnA Maker portal](https://qnamaker.ai) az Azure hitelesítő adatait, és válassza a **hozzon létre új szolgáltatást**.

    ![Tudásbázis létrehozása ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Ha még nem hozott létre a QnA Maker szolgáltatást, válassza ki a **hozzon létre egy kérdés-válasz szolgáltatás**. 

3. Válassza ki az Azure-bérlőhöz, Azure-előfizetés nevét és a QnA Maker szolgáltatást a listákból társított Azure-erőforrás neve **2. lépés** a QnA Maker Portal. Válassza ki az Azure QnA Maker szolgáltatást, amely üzemelteti a Tudásbázis.

    ![Kérdések és válaszok szolgáltatás beállítása](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. Adja meg az új Tudásbázis a tudásbázist és az adatforrás nevét.

    ![Set-adatforrások](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Adja meg a szolgáltatás egy **nevét.** Ismétlődő nevek és speciális karakterek támogatottak.
    - Adja hozzá az URL-címek kinyert adatok számára. További információ a támogatott adatforrásokról típusú [Itt](../Concepts/data-sources-supported.md).
    - A kívánt kinyert adatokat tölt fel. Tekintse meg a [díjszabási információk](https://aka.ms/qnamaker-pricing) megtekintéséhez, hogy hány dokumentumok adhat hozzá.
    - Ha szeretné manuálisan adja hozzá a QnA-tudásbázisok, akkor kihagyhatja **4. lépés** az előző képen látható.

5. Adjon hozzá **Chit csevegési** a KB. Válassza ki a robot chit csevegési támogatásának hozzáadása a 3 előre definiált személyiséghez egyik kiválasztásával. 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. Válassza ki **létrehozása a KB-os**.

    ![Tudásbázis létrehozása](../media/qnamaker-how-to-create-kb/create-kb.png)

7. Az adatok kinyerhetők néhány percet vesz igénybe.

    ![Kinyerés](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. A Tudásbázis sikeres létrehozása után a rendszer átirányítja a **Tudásbázis** lapot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Csevegési chit személyes hozzáadása](./chit-chat-knowledge-base.md)
