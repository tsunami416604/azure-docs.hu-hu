---
title: Az Azure Bot Service – QnA Maker QnA robotot
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 74c7bc5c601cd36a8dd2454506745406bc00dac0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031288"
---
# <a name="create-a-qna-bot-with-azure-bot-service-v3"></a>A QnA robotot létrehozása az Azure Bot Service v3
Ez az oktatóanyag végigvezeti a QnA robotot az Azure Bot service v3 létrehozása az Azure Portalon.

## <a name="prerequisite"></a>Előfeltétel
Mielőtt hoz létre, kövesse a [Tudásbázis létrehozása](../How-To/create-knowledge-base.md) QnA Maker szolgáltatás létrehozása a kérdések és válaszok.

A robot a Tudásbázis hozta létre, a QnAMakerDialog keresztül érkező kérdéseket válaszol.

## <a name="create-a-qna-bot"></a>Hozzon létre egy QnA robotot
1. Az a [az Azure portal](https://portal.azure.com), jelölje be **létrehozás** menü panelen, és válassza ki az új erőforrás **összes**.

    ![a robot szolgáltatás létrehozása](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. A keresőmezőbe keresése **Web App Bot**.

    ![bot service kiválasztása](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. Az a **Bot Service panel**, adja meg a szükséges információkat:

    - Állítsa be **alkalmazásnév** a robot neve. A robot a felhőben (például mynotesbot.azurewebsites.net) történő telepítése során az altartomány neve lesz.
    - Válassza ki az előfizetést, erőforráscsoportot, App service-csomag és helyét.

4. Az SDK v4 a QnA robotot létrehozására vonatkozó utasításokat talál – lásd: [QnA v4 bot sablon](https://aka.ms/qna-bot-v4). A v3-sablonok használatával válassza ki az SDK-verziót **SDK v3** és az SDK nyelvi **C#** vagy **Node.js**.

    ![a robot sdk-beállítások](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Válassza ki a **kérdést és választ** sablon a robot sablon mező, majd mentse a sablon beállításokat kiválasztásával **kiválasztása**.

    ![bot service kiválasztása](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Tekintse át a beállításokat, majd válassza a **létrehozás**. Ez létrehozza és telepíti az QnAMakerDialog a bot service az Azure-bA.

    ![bot service kiválasztása](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Győződjön meg arról, hogy a bot service van telepítve.

    - Válassza ki **értesítések** (Harang ikon, amely mentén felső szegélyéhez, hogy az Azure Portalon található). Az értesítés módosul **központi telepítés elindítva** való **üzembe helyezés sikeres**.
    - Miután az értesítés megváltozik, és **üzembe helyezés sikeres**, jelölje be **erőforrás megnyitása** adott értesítésre.

## <a name="chat-with-the-bot"></a>A robot cseveghet
Kiválasztásával **erőforrás megnyitása** a robot erőforrás panel megnyílik.

A robot regisztrálása után kattintson a **vizsgálat a webes csevegési** a webes csevegési ablak megnyitásához. Írja be a "hello", a webes csevegési.

![A QnA robotot webes csevegési](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

A robot válaszol "állítsa be QnAKnowledgebaseId és QnASubscriptionKey az alkalmazásbeállítások. Ismerje meg, azok a https://aka.ms/qnaabssetup". Ez a válasz megerősíti, hogy a QnA robotot az üzenetet kapott, de nem tartozik még a QnA Maker Tudásbázis van. Ehhez a következő lépésben.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>A QnA Maker Tudásbázis csatlakozni a robotot

1. Nyissa meg **Alkalmazásbeállítások** és szerkesztheti a **QnAKnowledgebaseId**, **QnAAuthKey**, és a **QnAEndpointHostName** mezőket tartalmaz a QnA Maker Tudásbázis értékeit.

    ![Alkalmazásbeállítások](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. A Tudásbázis Azonosítóját, gazdagép URL-cím és a végpont kulcs lekérése a Tudásbázis a beállítások lapján https://qnamaker.ai.
    - Jelentkezzen be a [a QnA Maker](https://qnamaker.ai)
    - Nyissa meg a Tudásbázis
    - Kattintson a **beállítások** lap
    - **Közzététel** a Tudásbázis, ha még nem tette

    ![A QnA Maker értékek](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Ha azt szeretné, a QnA robotot előzetes verzióját, a Tudásbázis kapcsolódni, az értékét állítsa be **Ocp-Apim-Subscription-Key** való **QnAAuthKey**. Hagyja a **QnAEndpointHostName** üres.

## <a name="test-the-bot"></a>A robot tesztelése
Az Azure Portalon kattintson a **tesztelni, webes csevegési** a robot teszteléséhez. 

![A QnA Maker robotot](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

A QnA robotot most már a Tudásbázis a választ.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A QnA Maker és a LUIS integrálása](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Lásd még

- [A Tudásbázis kezelése](https://qnamaker.ai)
- [Az emberekéhez különböző csatornákon keresztül](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
