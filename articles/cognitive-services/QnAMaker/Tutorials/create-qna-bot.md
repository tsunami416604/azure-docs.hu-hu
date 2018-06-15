---
title: Kérdések és válaszok bot létrehozása az Azure szolgáltatással Botot - Azure kognitív szolgáltatások |} Microsoft Docs
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: f2721dfa9a0922ee4a5af1eb66fd4506feb94d28
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349898"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Hozzon létre egy kérdés-válasz Botot Azure Botot szolgáltatással
Ez az oktatóanyag végigvezeti Azure Botot szolgáltatással kérdések és válaszok bot felépítése az Azure portálon.

## <a name="prerequisite"></a>Előfeltétel
Mielőtt, kövesse a [hozzon létre egy Tudásbázis]() Maker kérdések és válaszok szolgáltatás létrehozása a kérdések és válaszok.

A botot válaszol a Tudásbázis létrehozott, a QnAMakerDialog keresztül érkező kérdéseket.

## <a name="create-a-qna-bot"></a>Kérdések és válaszok Bot létrehozása
1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **létrehozása** a menü panelt, és válassza ki az új erőforrás **láthatja az összes**.

    ![botot szolgáltatás létrehozása](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. A keresőmezőbe, keresse meg a **Web App Botot**.

    ![botot szolgáltatás kiválasztása](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. Az a **Botot szolgáltatás panelre**, adja meg a szükséges információkat, és válassza ki **létrehozása**. Ez létrehozza, és QnAMakerDialog botot szolgáltatást telepíti az Azure-bA.

    - Állítsa be **alkalmazásnév** a botot névre. A név lesz a altartomány a botot a felhőbe (például mynotesbot.azurewebsites.net) telepítésekor.
    - Válassza ki az előfizetés, az erőforráscsoportot, a App service-csomag és a helyet.
    - Válassza ki a **kérdés és válasz** Botot sablon mező (Node.js vagy C#) sablont.
    - Jelölje be a megerősítő a jogi nyilatkozatot. A jogi nyilatkozat feltételeit. a rendszer alatt a jelölőnégyzet jelölését.

        ![botot szolgáltatás kiválasztása](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. Győződjön meg arról, hogy a botot szolgáltatás már alkalmazva van.

    - Válassza ki **értesítések** (a harang ikonra, amely mentén a felső szegélyéhez, hogy az Azure portálon található). Az értesítés állapotúról **telepítése megkezdődött** való **KözpontiTelepítés sikerült**.
    - Után vált, az értesítés **KözpontiTelepítés sikerült**, jelölje be **forrást** adott értesítésre kattinthat.

## <a name="chat-with-the-bot"></a>A Botot hívni
Kiválasztása **forrást** a botot erőforráspanelre viszi.

Amikor a botot regisztrálva van, kattintson a **vizsgálati a webes Csevegés** a webes Csevegés ablaktábla megnyitása. Írja be a "Hello" szövegrészt webes Csevegés.

![Kérdések és válaszok botot webes Csevegés](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

A botot válaszol, "állítsa QnAKnowledgebaseId és QnASubscriptionKey alkalmazás beállításaiban. Megtudhatja, hogyan azokat a https://aka.ms/qnaabssetup". Ez a válasz megerősíti, hogy a kérdések és válaszok Botot a üzenetet kapott, de nincs társítva még nincs kérdések és válaszok készítő Tudásbázis. Ehhez a következő lépésben.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>A kérdések és válaszok készítő Tudásbázis csatlakozni a botot

1. Nyissa meg **Alkalmazásbeállítások** és szerkesztése a **QnAKnowledgebaseId**, **QnAAuthKey**, és a **QnAEndpointHostName** mezőket kell tartalmaznia a kérdések és válaszok készítő Tudásbázis értékeit.

    ![Alkalmazásbeállítások](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. A Tudásbázis Azonosítóját, gazdagépre URL-cím és a végpont kulcs beszerzése a Tudásbázis a beállítások lapján https://qnamaker.ai.
    - Jelentkezzen be [kérdések és válaszok készítő](https://qnamaker.ai)
    - Nyissa meg a Tudásbázis
    - Kattintson a **beállítások** lap
    - **Közzététel** a Tudásbázis, ha még nem tette meg

    ![Kérdések és válaszok készítő értékek](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Ha azt szeretné, a kérdések és válaszok botot előzetes verzióját, a Tudásbázis kapcsolódni, állítsa be a **Ocp-Apim-előfizetés-kulcs** való **QnAAuthKey**. Hagyja a **QnAEndpointHostName** üres.

## <a name="test-the-bot"></a>A botot tesztelése
Az Azure-portálon kattintson a **webes Csevegés tesztelése** a botot teszteléséhez. 

![Kérdések és válaszok készítő botot](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

A kérdések és válaszok Botot most érkező válaszolja meg a Tudásbázis.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kérdések és válaszok Maker és LUIS integrálása](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Lásd még

- [A Tudásbázis kezelése](https://qnamaker.ai)
- [A különböző csatornákhoz a botot engedélyezése](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
