---
title: QnA robotot – Azure Bot Service – QnA Maker
titleSuffix: Azure Cognitive Services
description: Ez az oktatóanyag végigvezeti a QnA robotot az Azure Bot service v3 létrehozása az Azure Portalon.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: b948835b1a15eee391c6902d18229ec4926b66ca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095211"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>Oktatóanyag: A QnA robotot létrehozása az Azure Bot Service v3

Ez az oktatóanyag végigvezeti a QnA robotot az Azure Bot service v3-létrehozásához a [az Azure portal](https://portal.azure.com) kód írása nélkül. Közzétett Tudásbázis (KB) csatlakozik egy robot rendkívül egyszerű, bot alkalmazás beállításainak módosítása. 

> [!Note] 
> Ez a témakör olyan a robot SDK 3-as verziója. 4. verziójú annak [Itt](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs). 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Az Azure Bot Service a QnA Maker sablon létrehozása
> * Ellenőrizze, működik-e a kód a robot cseveghet 
> * A robot a közzétett KB-os csatlakozás
> * A robot a kérdés tesztelése

Ebben a cikkben is használhatja az ingyenes a QnA Maker [szolgáltatás](../how-to/set-up-qnamaker-service-azure.md).

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz egy közzétett Tudásbázis ehhez az oktatóanyaghoz. Ha nem rendelkezik egy, kövesse a [Tudásbázis létrehozása](../How-To/create-knowledge-base.md) QnA Maker szolgáltatás létrehozása a kérdések és válaszok.

## <a name="create-a-qna-bot"></a>Hozzon létre egy QnA robotot

1. Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget.

    ![a robot szolgáltatás létrehozása](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. A keresőmezőbe keresése **Web App Bot**.

    ![bot service kiválasztása](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. A **Bot Service** szolgáltatásban adja meg a szükséges adatokat:

    - Állítsa be **alkalmazásnév** a robot neve. A robot a felhőben (például mynotesbot.azurewebsites.net) történő telepítése során az altartomány neve lesz.
    - Válassza ki az előfizetést, erőforráscsoportot, App service-csomag és helyét.

4. A v3-sablonok használatával válassza ki az SDK-verziót **SDK v3** és az SDK nyelvi **C#** vagy **Node.js**.

    ![a robot sdk-beállítások](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Válassza ki a **kérdést és választ** sablon a robot sablon mező, majd mentse a sablon beállításokat kiválasztásával **kiválasztása**.

    ![Mentse a bot service sablon kiválasztása](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Tekintse át a beállításokat, majd válassza a **létrehozás**. Ez létrehozza és telepíti a bot service az Azure-bA.

    ![a robot létrehozása](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Győződjön meg arról, hogy a bot service van telepítve.

    - Válassza ki **értesítések** (Harang ikon, amely mentén felső szegélyéhez, hogy az Azure Portalon található). Az értesítés módosul **központi telepítés elindítva** való **üzembe helyezés sikeres**.
    - Miután az értesítés megváltozik, és **üzembe helyezés sikeres**, jelölje be **erőforrás megnyitása** adott értesítésre.

## <a name="chat-with-the-bot"></a>A robot cseveghet

Kiválasztásával **erőforrás megnyitása** végigvezeti a robot-erőforráshoz.

Válassza ki **vizsgálat a webes csevegési** a webes csevegési ablak megnyitásához. Írja be a "hi" a webes csevegési.

![A QnA robotot webes csevegési](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

A robot válaszol "állítsa be QnAKnowledgebaseId és QnASubscriptionKey az alkalmazásbeállítások. Ez a válasz megerősíti, hogy a QnA robotot az üzenetet kapott, de nem tartozik még a QnA Maker Tudásbázis van. 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>A QnA Maker Tudásbázis csatlakozni a robotot

1. Nyissa meg **Alkalmazásbeállítások** és szerkesztheti a **QnAKnowledgebaseId**, **QnAAuthKey**, és a **QnAEndpointHostName** mezőket tartalmaz a QnA Maker Tudásbázis értékeit.

    ![Alkalmazásbeállítások](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. A Tudásbázis Azonosítóját, gazdagép URL-cím és a végpont kulcs beszerzése a Tudásbázis a QnA Maker portál beállítások lapján.

   - Jelentkezzen be a [a QnA Maker](https://qnamaker.ai)
   - Nyissa meg a Tudásbázis
   - Válassza ki a **beállítások** lap
   - **Közzététel** a Tudásbázis, ha még nem tette

     ![A QnA Maker értékek](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

## <a name="test-the-bot"></a>A robot tesztelése

Az Azure Portalon válassza ki a **tesztelni, webes csevegési** a robot teszteléséhez. 

![A QnA Maker robotot](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

A QnA robotot a Tudásbázis a választ.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor elkészült, ebben az oktatóanyagban robottal, távolítsa el a robotot az Azure Portalon. A bot Service-szolgáltatások a következők:

* Az App Service-csomag
* A keresési szolgáltatás
* A Cognitive Services-szolgáltatás
* Az App Service-ben
* Igény szerint is tartalmazhat az application insights szolgáltatást és az application insights-adatok a storage

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fogalom: Tudásbázis](../concepts/knowledge-base.md)

## <a name="see-also"></a>Lásd még

- [A Tudásbázis kezelése](https://qnamaker.ai)
- [Az emberekéhez különböző csatornákon keresztül](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
