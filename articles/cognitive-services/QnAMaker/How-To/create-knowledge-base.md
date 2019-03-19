---
title: Tudásbázis létrehozása
titleSuffix: QnA Maker API - Azure Cognitive Services
description: A QnA Maker API szolgáltatás hozzáadása portál használatához Tudásbázis chit csevegési hozhat létre. Ez lehetővé teszi, hogy az alkalmazás vonzó. A robot chit csevegési kiindulási pontjaként adja hozzá a felső chit csevegési előre megadott készlete a KB, és időt takaríthat meg a teljesen új rögzíti őket költsége.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: b4553a392795bb8578f24848ccacc870b654bce9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116232"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Gyors útmutató: A QnA Maker API-portál használatával Tudásbázis létrehozása

A QnA Maker API portál segítségével egyszerűen adhat hozzá meglévő adatforrásokat, például egy Tudásbázis létrehozásakor. A QnA Maker új Tudásbázis hozhat létre a következő dokumentum-típusok közül:

<!-- added for scanability -->
* Gyakori kérdéseket tartalmazó oldalak
* Termékek kézikönyvek
* Strukturált dokumentumok

Például egy chit csevegési felhőszerepkör felel, hogy a felhasználó tudta további vonzó a felhasználóival.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="create-a-new-knowledge-base"></a>Új tudásbázis létrehozása

1. Jelentkezzen be a [QnA Maker portal](https://qnamaker.ai) az Azure hitelesítő adatait, és válassza a **Tudásbázis létrehozása**.

1. Ha még nem hozott létre a QnA Maker szolgáltatást, válassza ki a **hozzon létre egy kérdés-válasz szolgáltatás**. 

1. Válassza ki az Azure-bérlőhöz, Azure-előfizetés nevét és a QnA Maker szolgáltatást a listákból társított Azure-erőforrás neve **2. lépés** a QnA Maker Portal. Válassza ki az Azure QnA Maker szolgáltatást, amely üzemelteti a Tudásbázis.

    ![Kérdések és válaszok szolgáltatás beállítása](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Adja meg az új Tudásbázis a tudásbázist és az adatforrás nevét.

    ![Set-adatforrások](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Adja meg a szolgáltatás egy **nevét.** Ismétlődő nevek és speciális karakterek támogatottak.
    - Adja hozzá az URL-címek kinyert adatok számára. További információ a támogatott adatforrásokról típusú [Itt](../Concepts/data-sources-supported.md).
    - A kívánt kinyert adatokat tölt fel. Tekintse meg a [díjszabási információk](https://aka.ms/qnamaker-pricing) megtekintéséhez, hogy hány dokumentumok adhat hozzá.
    - Ha szeretné manuálisan adja hozzá a QnA-tudásbázisok, akkor kihagyhatja **4. lépés** az előző képen látható.

1. Adjon hozzá **Chit csevegési** a KB. Válassza ki a robot chit csevegési támogatásának hozzáadása a 3 személyiséghez egyik kiválasztásával. 

    ![Adja hozzá a csevegési chit KB](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Válassza ki **létrehozása a KB-os**.

    ![Tudásbázis létrehozása](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Az adatok kinyerhetők néhány percet vesz igénybe.

    ![Kinyerés](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. A Tudásbázis sikeres létrehozása után a rendszer átirányítja a **Tudásbázis** lapot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor elkészült, a Tudásbázis segítségével, távolítsa el a QnA Maker portálra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Csevegési chit személyes hozzáadása](./chit-chat-knowledge-base.md)
