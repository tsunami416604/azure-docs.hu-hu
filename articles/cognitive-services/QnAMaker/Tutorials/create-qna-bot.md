---
title: QnA robotot – Azure Bot Service – QnA Maker
titleSuffix: Azure Cognitive Services
description: Hozzon létre egy QnA csevegési robotot a közzétételi lapról egy meglévő tudásbázishoz. Ez a robot a bot Framework SDK v4-et használja. A robot létrehozásához nem kell kódot írnia, az összes kódot Önnek kell megadnia.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: 4bb987a5a091871bec2c0cc8cec6d9ab804bb244
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697994"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Oktatóanyag: QnA-robot létrehozása Azure Bot Service v4-mel

Hozzon létre egy QnA csevegési robotot a **közzétételi** lapról egy meglévő tudásbázishoz. Ez a robot a bot Framework SDK v4-et használja. A robot létrehozásához nem kell kódot írnia, az összes kódot Önnek kell megadnia.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Azure Bot Service létrehozása meglévő Tudásbázisból
> * Ellenőrizze, működik-e a kód a robot cseveghet 

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz egy közzétett Tudásbázis ehhez az oktatóanyaghoz. Ha még nem rendelkezik ilyennel, a [Létrehozás és válasz](create-publish-query-in-portal.md) a Tudásbázisból című oktatóanyag lépéseit követve hozzon létre egy QnA Maker tudásbázist kérdésekkel és válaszokkal.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Hozzon létre egy QnA robotot

Hozzon létre egy robotot ügyfélalkalmazásként a Tudásbázisban. 

1. A QnA Maker portálon lépjen a **Közzététel** lapra, és tegye közzé a tudásbázist. Válassza a **robot létrehozása**lehetőséget. 

    ![A QnA Maker portálon lépjen a közzététel lapra, és tegye közzé a tudásbázist. Válassza a robot létrehozása lehetőséget.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Megnyílik a Azure Portal a robot létrehozási konfigurációjával.

1.  Adja meg a robot létrehozásának beállításait:

    |Beállítás|Value|Cél|
    |--|--|--|
    |Robot neve|`my-tutorial-kb-bot`|Ez a robothoz tartozó Azure-Erőforrás neve.|
    |Subscription|Lásd: cél.|Válassza ki ugyanazt az előfizetést, mint amit a QnA Maker erőforrások létrehozásához használt.|
    |Resource group|`my-tutorial-rg`|Az összes robottal kapcsolatos Azure-erőforráshoz használt erőforráscsoport.|
    |Location|`west us`|A robot Azure-erőforrásának helye.|
    |Tarifacsomag|`F0`|Az Azure bot Service ingyenes szintje.|
    |App neve|`my-tutorial-kb-bot-app`|Ez egy webalkalmazás, amely csak a robotot támogatja. Ez nem lehet azonos az alkalmazás nevével, mert a QnA Maker szolgáltatás már használatban van. A QnA Maker webalkalmazásának más erőforrásokkal való megosztása nem támogatott.|
    |SDK nyelve|C#|Ez a robot Framework SDK által használt mögöttes programozási nyelv. Az Ön választása [C#](https://github.com/Microsoft/botbuilder-dotnet) vagy [Node. js](https://github.com/Microsoft/botbuilder-js).|
    |QnA-hitelesítési kulcs|**Ne változzon**|Ezt az értéket Önnek kell kitöltenie.|
    |App Service-csomag helye|**Ne változzon**|Ebben az oktatóanyagban a hely nem fontos.|
    |Azure Storage|**Ne változzon**|A beszélgetési adatként az Azure Storage-táblákban tárolódnak.|
    |Application Insights|**Ne változzon**|A rendszer elküldi a naplózást Application Insightsba.|
    |Microsoft-alkalmazás azonosítója|**Ne változzon**|Az Active Directory-felhasználó és a jelszó megadása kötelező.|

    ![Hozza létre a Tudásbázis robotját ezekkel a beállításokkal.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Várjon pár percet, amíg a robot-létrehozási folyamat értesítési jelentései sikeresek voltak.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>A robot cseveghet

1. A Azure Portalban nyissa meg az új robot-erőforrást az értesítésből. 

    ![A Azure Portalban nyissa meg az új robot-erőforrást az értesítésből.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. A **bot Management**szolgáltatásban válassza a **tesztelés webes csevegésben** lehetőséget `How large can my KB be?`, és írja be a következőt:. A bot a következőket fogja válaszolni: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Tesztelje az új Tudásbázis robotját.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Az Azure bots szolgáltatással kapcsolatos további információkért lásd: [QnA Maker használata a kérdések megválaszolásához](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor elkészült, ebben az oktatóanyagban robottal, távolítsa el a robotot az Azure Portalon. 

Ha létrehozott egy új erőforráscsoportot a robot erőforrásaihoz, törölje az erőforráscsoportot. 

Ha nem hozott létre új erőforráscsoportot, meg kell keresnie a robothoz társított erőforrásokat. A legegyszerűbb módszer a robot és a bot alkalmazás nevének megkeresése. A robot erőforrásai a következők:

* Az App Service-csomag
* A keresési szolgáltatás
* A Cognitive Services-szolgáltatás
* Az App Service-ben
* Igény szerint is tartalmazhat az application insights szolgáltatást és az application insights-adatok a storage


## <a name="related-to-qna-maker-bots"></a>QnA Maker botokkal kapcsolatos

* A QnA Maker portálon használt QnA Maker Súgó robot [mintaként](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support)érhető el.
    ![QnA Maker Súgó bot ikon piros robot](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* Az [egészségügyi robotok](https://docs.microsoft.com/HealthBot/qna_model_howto) a QnA Maker használják az egyik [nyelvi modellként](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fogalom: Tudásbázis](../concepts/knowledge-base.md)

## <a name="see-also"></a>Lásd még

- [A Tudásbázis kezelése](https://qnamaker.ai)
- [Az emberekéhez különböző csatornákon keresztül](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
