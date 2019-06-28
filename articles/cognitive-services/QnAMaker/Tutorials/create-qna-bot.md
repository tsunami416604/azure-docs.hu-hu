---
title: QnA robotot – Azure Bot Service – QnA Maker
titleSuffix: Azure Cognitive Services
description: A közzétételi oldalon egy meglévő Tudásbázis – kérdések és válaszok csevegőrobotot hozzon létre. Ez a robot a Bot keretrendszer SDK v4 használ. Nem kell a robotot hozhat létre bármilyen kód megírását, minden a kódot biztosítunk Önnek.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 1d475d988847bd4cc9e37b91a0c5d28678cb05f2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446652"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Oktatóanyag: A QnA robotot létrehozása az Azure Bot Service v4

A QnA csevegőrobotot létrehozása a **közzététel** egy meglévő Tudásbázis lapját. Ez a robot a Bot keretrendszer SDK v4 használ. Nem kell a robotot hozhat létre bármilyen kód megírását, minden a kódot biztosítunk Önnek.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Az Azure Bot Service egy meglévő Tudásbázis létrehozása
> * Ellenőrizze, működik-e a kód a robot cseveghet 

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz egy közzétett Tudásbázis ehhez az oktatóanyaghoz. Ha nem rendelkezik egy, kövesse a [létrehozása és a válasz a KB-os](create-publish-query-in-portal.md) az oktatóanyag a QnA Maker Tudásbázis létrehozása a kérdések és válaszok.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Hozzon létre egy QnA robotot

Hozzon létre egy robot a Tudásbázis ügyfélalkalmazást. 

1. A QnA Maker portálon nyissa meg a **közzététel** lapon, és közzéteheti a tudásbázist. Válassza ki **létrehozni a Bot**. 

    ![A QnA Maker portálon nyissa meg a közzétételi oldalon, és közzéteheti a tudásbázist. Válassza a robot létrehozása.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Az Azure portal megnyitása a robot létrehozásának konfigurációval.

1.  Adja meg a beállításokat a robot létrehozásához:

    |Beállítás|Érték|Cél|
    |--|--|--|
    |Robot neve|`my-tutorial-kb-bot`|Ez az az Azure-erőforrás neve a robot.|
    |Előfizetés|Tekintse meg a cél.|Válassza ki a ugyanahhoz az előfizetéshez, a QnA Maker erőforrások létrehozásához használt.|
    |Erőforráscsoport|`my-tutorial-rg`|Az összes bot kapcsolódó Azure-erőforrások használt erőforráscsoport.|
    |Location|`west us`|A robot Azure-erőforrás helye.|
    |Tarifacsomag|`F0`|Az Azure bot service az ingyenes szintet.|
    |App neve|`my-tutorial-kb-bot-app`|Ez a webes alkalmazás csak a robot támogatásához. Ezzel nem kell az ugyanazon alkalmazás neve, ahogy a QnA Maker szolgáltatás már használja. A QnA Maker webalkalmazás osztanak meg minden egyéb erőforrás nem támogatott.|
    |SDK-nyelv|C#|Ez az az alapul szolgáló programozási nyelv, a bot framework SDK-t használják. A választható lehetőségek: [ C# ](https://github.com/Microsoft/botbuilder-dotnet) vagy [Node.js](https://github.com/Microsoft/botbuilder-js).|
    |A QnA hitelesítési kulcs|**Ne módosítsa**|Ez az érték kitölti a rendszer az Ön számára.|
    |App service-csomag/hely|**Ne módosítsa**|A jelen oktatóanyag esetében a hely nem számít.|
    |Azure Storage|**Ne módosítsa**|Az Azure Storage-táblák beszélgetési adatok tárolódik.|
    |Application Insights|**Ne módosítsa**|Naplózás küld az Application Insights.|
    |A Microsoft-alkalmazás azonosítója|**Ne módosítsa**|Az Active directory – felhasználók és a jelszó megadása kötelező.|

    ![Hozzon létre a Tudásbázis robot ezekkel a beállításokkal.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Várjon néhány percig, amíg a robot létrehozásának folyamata értesítési sikeres befejezést jelent.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>A robot cseveghet

1. Az Azure Portalon nyissa meg az új bot erőforrás a értesítésből. 

    ![Az Azure Portalon nyissa meg az új bot erőforrás a értesítésből.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. A **Bot felügyeleti**válassza **vizsgálat a webes csevegési** , és írja be: `How large can my KB be?`. A robot válaszol, és: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Tesztelje az új Tudásbázis robot.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Azure Robotokat kapcsolatos további információkért lásd: [használata a QnA Maker megválaszolandó kérdések](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor elkészült, ebben az oktatóanyagban robottal, távolítsa el a robotot az Azure Portalon. 

Ha létrehozott egy új erőforráscsoportot a robot-erőforrások, törölje az erőforráscsoportot. 

Létrehozott egy új erőforráscsoportot, meg kell keresnie a robot kapcsolódó erőforrások. A legegyszerűbb módja a robot és a robot neve szerint kereshet. A robot erőforrások a következők:

* Az App Service-csomag
* A keresési szolgáltatás
* A Cognitive Services-szolgáltatás
* Az App Service-ben
* Igény szerint is tartalmazhat az application insights szolgáltatást és az application insights-adatok a storage


## <a name="related-to-qna-maker-bots"></a>A QnA Maker robotokat kapcsolatos

* A QnA Maker súgó robot, a QnA Maker Portal használt érhető el egy [bot minta](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![A QnA Maker súgó bot ikonra a piros robot](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Egészségügyi robotokat](https://docs.microsoft.com/HealthBot/qna_model_howto) használata a QnA Maker egy saját [nyelvi modellek](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fogalom: Tudásbázis](../concepts/knowledge-base.md)

## <a name="see-also"></a>Lásd még

- [A Tudásbázis kezelése](https://qnamaker.ai)
- [Az emberekéhez különböző csatornákon keresztül](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
