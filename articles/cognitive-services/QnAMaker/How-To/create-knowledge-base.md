---
title: Hozzon létre egy Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: A robot chit csevegési hozzáadása megkönnyíti több természetes nyelvi és vonzóbbá tehetik. A QnA Maker egyszerűen adja hozzá a felső chit csevegési, előre megadott készlete a KB-os teszi lehetővé. Ez a robot chit csevegési a kiindulási pont lehet, és időt takaríthat meg a teljesen új rögzíti őket költsége.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 66705a0afdcdb04fe49bea0bfc03286df3611071
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543358"
---
# <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

QnA Maker segítségével nagyon egyszerű üzembe helyezni a Tudásbázis létrehozása meglévő adatforrásokat. Hozzon létre egy új QnA Maker Tudásbázis a gyakori kérdéseket tartalmazó oldalak, a termékek kézikönyvek strukturált dokumentumok, vagy adja hozzá őket besorolást.

## <a name="steps"></a>Lépések

1. Első lépésként jelentkezzen be a [QnA Maker portal](https://qnamaker.ai) az azure hitelesítő adatait, és kattintson a **hozzon létre új szolgáltatást**.

    ![Tudásbázis létrehozása ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Ha még nem hozott létre a QnA Maker szolgáltatást, válassza ki a **hozzon létre egy kérdés-válasz szolgáltatás**. Ellenkező esetben a QnA Maker szolgáltatást választhat a legördülő listákból a 2. lépésben. Válassza ki a QnA Maker szolgáltatást, amely a Tudásbázis fogja futtatni.

    ![Kérdések és válaszok szolgáltatás beállítása](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Adja meg a következő információkat a Tudásbázis létrehozásához.

    ![Set-adatforrások](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Adja meg a szolgáltatás egy **nevét.** Duplikált nevek használata támogatott, és speciális karaktereket is támogatottak.
    - Illessze be az URL-címek, amelyek kinyerése. További információ a támogatott adatforrásokról típusú [Itt](../Concepts/data-sources-supported.md).
    - Azt is megteheti töltse fel a fájlokat, amelyek az adatok ki kell olvasni. Tekintse meg a [díjszabási információk](https://aka.ms/qnamaker-pricing
) megtekintéséhez, hogy hány dokumentumok adhat hozzá.
    - Ha szeretné manuálisan adja hozzá a QnA-tudásbázisok, kihagyhatja a fájlok csatolása.

4. Kattintson a **Létrehozás** gombra.

    ![Tudásbázis létrehozása](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Az adatok kinyerhetők néhány percet vesz igénybe.

    ![Kinyerés](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Ha a Tudásbázis sikeresen létrejött, a rendszer átirányítja a **Tudásbázis** lapot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis importálása](../Tutorials/migrate-knowledge-base.md)
