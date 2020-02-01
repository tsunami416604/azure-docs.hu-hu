---
title: 'Oktatóanyag: QnA robot – Azure Bot Service-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy QnA csevegési robotot a közzétételi lapról egy meglévő tudásbázishoz. Ez a robot a bot Framework SDK v4-et használja. A robot létrehozásához nem kell kódot írnia, az összes kódot Önnek kell megadnia.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: diberry
ms.openlocfilehash: a9afef07af1332910cc9e25f9d66db5930df3bd4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901661"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Oktatóanyag: QnA-robot létrehozása Azure Bot Service v4-sel

Hozzon létre egy QnA csevegési robotot a **közzétételi** lapról egy meglévő tudásbázishoz. Ez a robot a bot Framework SDK v4-et használja. A robot létrehozásához nem kell kódot írnia, az összes kódot Önnek kell megadnia.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Azure Bot Service létrehozása meglévő Tudásbázisból
> * Csevegés a robottal a kód működésének ellenőrzéséhez

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz közzé kell tennie egy tudásbázist. Ha még nem rendelkezik ilyennel, a [Létrehozás és válasz a Tudásbázisból](create-publish-query-in-portal.md) című oktatóanyag lépéseit követve hozzon létre egy QnA Maker tudásbázist kérdésekkel és válaszokkal.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>QnA-robot létrehozása

Hozzon létre egy robotot ügyfélalkalmazásként a Tudásbázisban.

1. A QnA Maker portálon lépjen a **Közzététel** lapra, és tegye közzé a tudásbázist. Válassza a **robot létrehozása**lehetőséget.

    [![a QnA Maker portálon lépjen a közzététel lapra, és tegye közzé a tudásbázist. Válassza a robot létrehozása lehetőséget.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png#lightbox)

    Megnyílik a Azure Portal a robot létrehozási konfigurációjával.

1.  Adja meg a robot létrehozásának beállításait:

    |Beállítás|Value (Díj)|Rendeltetés|
    |--|--|--|
    |Bot-fogantyú|`qna-maker-central-us-bot`|Ez a robothoz tartozó Azure-Erőforrás neve.|
    |Előfizetést|Lásd: cél.|Válassza ki ugyanazt az előfizetést, mint amit a QnA Maker erőforrások létrehozásához használt.|
    |Erőforráscsoport|`docs`|Az összes robottal kapcsolatos Azure-erőforráshoz használt erőforráscsoport.|
    |Földrajzi egység|`west us`|A robot Azure-erőforrásának helye.|
    |Díjcsomag|`F0`|Az Azure bot Service ingyenes szintje.|
    |App neve|`qna-maker-central-us-bot-app`|Ez egy webalkalmazás, amely csak a robotot támogatja. Ez nem lehet azonos az alkalmazás nevével, mert a QnA Maker szolgáltatás már használatban van. A QnA Maker webalkalmazásának más erőforrásokkal való megosztása nem támogatott.|
    |SDK nyelve|C#|Ez a robot Framework SDK által használt mögöttes programozási nyelv. Az Ön választása [C#](https://github.com/Microsoft/botbuilder-dotnet) vagy [Node. js](https://github.com/Microsoft/botbuilder-js).|
    |QnA-hitelesítési kulcs|**Ne változzon**|Ezt az értéket Önnek kell kitöltenie.|
    |App Service-csomag/hely|**Ne változzon**|Ebben az oktatóanyagban a hely nem fontos.|
    |Application Insights|**Ne változzon**|A rendszer elküldi a naplózást Application Insightsba.|
    |Microsoft-alkalmazás azonosítója|**Ne változzon**|Az Active Directory-felhasználó és a jelszó megadása kötelező.|

    > [!div class="mx-imgBorder"]
    > ![hozza létre a Tudásbázis robotját ezekkel a beállításokkal.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Várjon pár percet, amíg a robot-létrehozási folyamat értesítési jelentései sikeresek voltak.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Csevegés a robottal

1. A Azure Portalban nyissa meg az új robot-erőforrást az értesítésből.

    ![A Azure Portalban nyissa meg az új robot-erőforrást az értesítésből.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. A **bot Management** **szolgáltatásban válassza a tesztelés webes csevegésben** lehetőséget, és írja be a következőt: `How large can my KB be?`. A bot a következőket fogja válaszolni:


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Tesztelje az új Tudásbázis robotját.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Az Azure bots szolgáltatással kapcsolatos további információkért lásd: [QnA Maker használata a kérdések megválaszolásához](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az oktatóanyag robotjának, távolítsa el a robotot a Azure Portal.

Ha létrehozott egy új erőforráscsoportot a robot erőforrásaihoz, törölje az erőforráscsoportot.

Ha nem hozott létre új erőforráscsoportot, meg kell keresnie a robothoz társított erőforrásokat. A legegyszerűbb módszer a robot és a bot alkalmazás nevének megkeresése. A robot erőforrásai a következők:

* A App Service terv
* A keresési szolgáltatás
* A kognitív szolgáltatás
* Az App Service
* Igény szerint az Application betekintő szolgáltatás és az Application-elemzések adattárolási szolgáltatása is felvehető


## <a name="related-to-qna-maker-bots"></a>QnA Maker botokkal kapcsolatos

* Az [egészségügyi robotok](https://docs.microsoft.com/HealthBot/qna_model_howto) a QnA Maker használják az egyik [nyelvi modellként](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Koncepció: Tudásbázis](../concepts/knowledge-base.md)

## <a name="see-also"></a>Lásd még:

- [A Tudásbázis kezelése](https://qnamaker.ai)
- [A robot engedélyezése különböző csatornákon](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
