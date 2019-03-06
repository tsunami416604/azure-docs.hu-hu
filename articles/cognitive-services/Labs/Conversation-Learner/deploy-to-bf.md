---
title: Beszélgetéstanuló robot – a Microsoft Cognitive Services üzembe helyezése |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan helyezhet üzembe egy Beszélgetéstanuló robot.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bc04afead8742c9f384287ecb8d33c54770456b6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447387"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Beszélgetéstanuló robotprogramok üzembe helyezése

Ez a dokumentum azt ismerteti, hogyan helyezhet üzembe egy Beszélgetéstanuló robot – helyi vagy az Azure-bA.

## <a name="prerequisite-determine-the-model-id"></a>Előfeltétel: a modell Azonosítóját határozza meg 

A beszélgetés Learner felhasználói felületén kívül robotprogramok futtatja, be kell a Beszélgetéstanuló Modellazonosító, amelyet használni fog a robot – azaz a gépi tanulási modell Beszélgetéstanuló felhőbeli azonosítója.  (Ellentétben a robot a beszélgetés Learner felhasználói felületen futtatásakor a felhasználói felület úgy dönt, mely forrásmodell azonosítóját.).  

Íme a Modellazonosító beszerzése:

1. Indítsa el a robot és a beszélgetés Learner felhasználói felületén.  A rövid útmutató a teljes körű útmutatást; lásd: összefoglalásképpen:

    Egy parancsablakban:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    Más parancssori ablakban

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Nyissa meg böngészőben http://localhost:5050 

3. Kattintson a Beszélgetéstanuló modellel ismerkedhet meg azonosítója

4. Kattintson a "Beállítások" a a bal oldali navigációs sávon.

5. Az oldal tetején a "Model ID" GUID jelenik meg.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Option 1: Helyi futtatásához egy Beszélgetéstanuló bot üzembe helyezése

Üzembe helyez egy robot a helyi gépre, és bemutatja, hogyan férhet hozzá, a Bot Framework emulator használatával.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>A robot a beszélgetés Learner felhasználói felületén kívül hozzáférés konfigurálása

Amikor helyileg futtatja a robotprogramok, adja meg az Alkalmazásazonosítót a robot `.env` fájlt:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Ezután indítsa el a robot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

A robot mostantól helyileg fut.  Hozzá tud férni a Bot Framework emulátort.

### <a name="download-and-install-the-emulator"></a>Töltse le és telepítse az emulátort

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Az emulátor csatlakozik a robot

1. A bal felső sarkában az emulátorban, a "adja meg a végpont URL-címe", adja meg `http://127.0.0.1:3978/api/messages`.  A többi mezőt hagyja üresen, és kattintson a "Kapcsolódás" elemre.

2. Most már szolgáltatásprogram a robottal.

## <a name="option-2-deploy-to-azure"></a>Option 2: Üzembe helyezés az Azure-ban

Tegye közzé a ugyanúgy, mint bármilyen más robot közzé kívánja tenni a hasonló Beszélgetéstanuló robotjait. Magas szinten töltse fel a kódot egy üzemeltetett webhelyre, állítsa be a megfelelő konfigurációs értékeket, és regisztrálja a robot a különböző csatornákon. A videó közzététele a robot, az Azure Bot Service használatával történő bemutató részletes utasítások találhatók.

A robot üzemel, és a futtató csatlakozhat más csatornákat, például a Facebook, a Teams, a Skype stb. az Azure Bot Csatornaregisztráció használatával. A folyamat lásd: dokumentáció: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Az alábbiakban lépésről lépésre ismerteti, hogyan telepíthető egy beszélgetés Learner Bot az Azure-bA.  Ezek az utasítások feltételezik, hogy a robot forrás, például az Azure DevOps-szolgáltatásokkal, GitHub, BitBucket vagy onedrive vállalati verzió egy felhőalapú adatforrás érhető el, és konfigurálja a robot a folyamatos üzembe helyezéshez.

1. Jelentkezzen be az Azure Portalra https://portal.azure.com

2. Hozzon létre egy új "Az Web App Bot" erőforrást 

    1. Nevezze el a robotot
    2. Kattintson a "Bot sablon", "Node.js" válassza, válassza az "Alapszintű", majd kattintson a "Select" gombra
    3. Kattintson a "create" az Web App Bot létrehozásához.
    4. Várjon, amíg a Web App Bot erőforrás létrehozását.

3. Az Azure Portalon szerkesztheti az újonnan létrehozott Web App Bot erőforrás.

    1. Kattintson az "Alkalmazás beállítások" nav elemet a bal oldalon
    1. Görgessen le az "Alkalmazás beállítások" szakaszban
    2. Adja hozzá ezeket a beállításokat:

        Környezeti változó | érték
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | „https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/”
        CONVERSATION_LEARNER_MODEL_ID      | Alkalmazás-azonosító GUID, a beszélgetés Learner felhasználói felületen a "beállítások" modell szerzett >
        LUIS_AUTHORING_KEY               | Ehhez a modellhez kulcs létrehozási LUIS
        LUIS_SUBSCRIPTION_KEY            | Nem kötelező, de a közzétett robotok kerülje a szerzői műveletek kvótájának ajánlott.
    
    4. Az oldal tetején a "Mentés" gombra
    5. Nyissa meg a "Build" nav elemet a bal oldalon
    6. Kattintson az "A folyamatos üzembe helyezés konfigurálása" 
    7. Kattintson a "Telepítés" ikonra a központi telepítések
    8. Kattintson a "Kötelező beállítások"
    9. Válassza ki a forrás, amennyiben rendelkezésre áll-e a robot kód, és konfigurálja a forrás.
