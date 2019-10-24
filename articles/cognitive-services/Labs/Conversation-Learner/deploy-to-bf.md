---
title: Conversation Learner robot-Microsoft Cognitive Services üzembe helyezése | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan helyezhet üzembe egy Conversation Learner robotot.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 05fd83506aac26df33f18bec83dcadac8dee2d90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705281"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Conversation Learner robot üzembe helyezése

Ez a dokumentum ismerteti, hogyan helyezhet üzembe egy Conversation Learner robotot – akár helyileg, akár az Azure-ban.

## <a name="prerequisite-determine-the-model-id"></a>Előfeltétel: a modell AZONOSÍTÓjának meghatározása 

Ha a Conversation Learner felhasználói felületen kívülről szeretne robotot futtatni, be kell állítania a Conversation Learner modell AZONOSÍTÓját, amelyet a robot használni fog – azaz a Machine learning modell AZONOSÍTÓját a Conversation Learner felhőben.  (Ezzel szemben, amikor a Conversation Learner felhasználói felületen futtatja a robotot, a felhasználói felület kiválasztja a modell AZONOSÍTÓját.).  

A modell AZONOSÍTÓjának beszerzése:

1. Indítsa el a robotot és a Conversation Learner felhasználói felületet.  A teljes körű utasításokért lásd a gyors üzembe helyezési útmutatót. Összefoglalás:

    Egyetlen parancssori ablakban:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    A portok parancsablakban

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Böngésző megnyitása a következőhöz`http://localhost:5050` 

3. Kattintson arra a Conversation Learner modellre, amelynek AZONOSÍTÓját le szeretné kérni

4. Kattintson a bal oldali navigációs sávban a "beállítások" elemre.

5. A "modell azonosítója" GUID az oldal tetején jelenik meg.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>1\. lehetőség: Conversation Learner robot üzembe helyezése helyileg való futtatáshoz

Ez üzembe helyez egy robotot a helyi gépre, és bemutatja, hogyan érheti el azt a bot Framework Emulator használatával.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>A robot konfigurálása a Conversation Learner felhasználói felületén kívüli hozzáféréshez

A robot helyi futtatásakor adja hozzá az alkalmazás azonosítóját a robot `.env` fájlhoz:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Ezután indítsa el a robotot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

A robot mostantól helyileg fut.  A bot Framework Emulator használatával férhet hozzá.

### <a name="download-and-install-the-emulator"></a>Az emulátor letöltése és telepítése

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Az emulátor összekötése a robottal

1. Az emulátor bal felső részén, a "adja meg a végpont URL-címe" mezőbe írja `http://127.0.0.1:3978/api/messages`be a következőt:.  Hagyja üresen a többi mezőt, és kattintson a "kapcsolat" gombra.

2. Most már beszélget a robottal.

## <a name="option-2-deploy-to-azure"></a>2\. lehetőség: Üzembe helyezés az Azure-ban

Tegye közzé a Conversation Learner robotját ugyanúgy, mint bármely más robotot. Magas szinten töltse fel a kódot egy üzemeltetett webhelyre, állítsa be a megfelelő konfigurációs értékeket, majd regisztrálja a robotot különböző csatornákkal. A részletes utasítások ebben a videóban azt mutatják be, hogyan teheti közzé a robotot a Azure Bot Service használatával.

A bot üzembe helyezése és futtatása után különböző csatornákat (például Facebook, csapatok, Skype stb.) kapcsolhat össze. Azure bot Channel-regisztráció használata. A folyamat dokumentációját lásd: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Az alábbiakban részletes útmutatást talál a Conversation Learner-bot Azure-ba történő üzembe helyezéséhez.  Ezek az utasítások feltételezik, hogy a robot forrása elérhető egy felhőalapú forrásból, például az Azure DevOps Services, a GitHub, a BitBucket vagy a OneDrive, és a robotot a folyamatos üzembe helyezéshez konfigurálja.

1. Jelentkezzen be a Azure Portal https://portal.azure.com

2. Új "webalkalmazás-bot" erőforrás létrehozása 

    1. Adja meg a robot nevét
    2. Kattintson a "bot Template" elemre, válassza a "Node. js" elemet, válassza az "alapszintű" lehetőséget, majd kattintson a "kiválasztás" gombra.
    3. Kattintson a "létrehozás" gombra a webalkalmazás-robot létrehozásához.
    4. Várjon, amíg létrejön a webalkalmazás-robot erőforrása.

3. A Azure Portal szerkessze az imént létrehozott webalkalmazás-robot-erőforrást.

   1. Kattintson a bal oldalon található "Alkalmazásbeállítások" navigációs elemre.
   1. Görgessen le az "app Settings" (Alkalmazásbeállítások) szakaszhoz
   2. Adja hozzá ezeket a beállításokat:

       Környezeti változó | value
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
       CONVERSATION_LEARNER_MODEL_ID      | Az alkalmazás azonosítójának GUID azonosítója, amely a modell "Settings" (beállítások) részének Conversation Learner felhasználói felületéről származik >
       LUIS_AUTHORING_KEY               | A modell LUIS authoring kulcsa
       LUIS_SUBSCRIPTION_KEY            | Nem kötelező, de ajánlott a közzétett robotok számára, hogy ne használja a szerzői kvótát.
    
   4. Kattintson a Save (Mentés) gombra az oldal tetején
   5. A "Build" NAV-elem megnyitása a bal oldalon
   6. Kattintson a "folyamatos üzembe helyezés konfigurálása" elemre. 
   7. Kattintson a "telepítés" ikonra a központi telepítések területen.
   8. Kattintson a kötelező beállítások elemre.
   9. Válassza ki azt a forrást, ahol a robot kódja elérhető, és konfigurálja a forrást.
