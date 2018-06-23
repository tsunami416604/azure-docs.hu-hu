---
title: Beszélgetés tanuló bot - Microsoft kognitív szolgáltatások központi telepítése |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan telepítheti a beszélgetés tanuló bot.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 77cc998227d996a6e52b1b5629204da5dc735ede
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348591"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>A beszélgetés tanuló botot központi telepítése

Ez a dokumentum azt ismerteti, hogyan központi telepítése a beszélgetés tanuló bot – helyi vagy az Azure.

## <a name="prerequisite-determine-the-application-id"></a>Előfeltétel: határozza meg az Alkalmazásazonosítót 

A beszélgetés tanuló felhasználói felület kívül bot futtatásához meg kell adni a beszélgetés tanuló Alkalmazásazonosítót a botot által használt--, azaz a gépi tanulási modell a beszélgetés tanuló felhőben azonosítója.  (Ezzel szemben, ha fut a botot a beszélgetés tanuló felhasználói felületen, a felhasználói felület úgy dönt, melyik alkalmazás azonosítóját.).  

Íme az Alkalmazásazonosító beszerzése:

1. Indítsa el a botot és a beszélgetés tanuló felhasználói felületén.  A gyors üzembe helyezési útmutatójában részletes útmutatás; összefoglalásképpen:

    Egy parancssori ablakban:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    Egy másik parancs ablakban

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Nyissa meg böngészőben http://localhost:5050 

3. Kattintson a beszélgetés tanuló alkalmazástól le szeretné kérdezni a Azonosítóját.

4. A bal oldali navigációs sávján kattintson a "Beállítások".

5. Az "App ID" GUID a lap tetején jelenik meg.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>1. lehetőség: A helyi futtatásához beszélgetés tanuló bot telepítése

Bot a helyi számítógépen telepíti, és bemutatja, hogy hozzá tud férni a Botot keretrendszer emulator használatával.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>A hozzáférés a beszélgetés tanuló felhasználói felület kívül botot konfigurálása

Bot helyben fut, amikor az alkalmazás azonosító hozzáadása a botot `.env` fájlt:

    ```
    CONVERSATION_LEARNER_APP_ID=<YOUR_APP_ID>
    ```

Ezt követően indítsa el a botot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

A botot most fut. helyileg.  A Botot keretrendszer emulátorral hozzá tud férni.

### <a name="download-and-install-the-emulator"></a>Töltse le és telepítse az emulátort

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Az emulátor csatlakozik a botot

1. A bal felső sarkában az emulátor, az "adja meg a végpont URL-címe", adja meg `http://127.0.0.1:3978/api/messages`.  A többi mezőt hagyja üresen, és kattintson a "Csatlakozás".

2. Most beszélgetést a botot együtt.

## <a name="option-2-deploy-to-azure"></a>2. lehetőség: Üzembe helyezése az Azure-bA

Tegye közzé a beszélgetés tanuló botot hasonló úgy, ahogy bármely más botot közzé kívánja tenni. Magas szinten töltse fel a kód egy üzemeltetett webhely, állítsa be a megfelelő konfigurációs értékeket, és regisztrálja a botot különböző csatornák. Ez a videó bemutatja, hogyan közzététele az Azure Botot szolgáltatással botot részletes utasításokra van szüksége van.

Ha a botot van telepítve és fut, akkor csatlakozhatnak különböző csatornákhoz, például egy Azure Botot Csatornaregisztráció használatával Facebook, csoportok, Skype stb. Dokumentációját lásd: folyamat: https://docs.microsoft.com/en-us/bot-framework/bot-service-quickstart-registration

Az alábbiakban részletesen beszélgetés tanuló Bot telepítéséhez az Azure-bA.  Ezek az utasítások azt feltételezik, hogy a botot forrás például VSTS, a GitHub, a Bitbucketből vagy a onedrive vállalati verzió felhőalapú forrásból származik, és úgy konfigurálja a botot a folyamatos üzembe helyezés.

1. Jelentkezzen be az Azure-portálon: https://portal.azure.com

2. Hozzon létre egy új "Web App Bot" erőforrást 

    1. Nevezze el a botot
    2. Kattintson a "Bot sablon", "Node.js" Válassza ki, válassza a "Basic", majd kattintson a "Select" gombra.
    3. Kattintson a "create" a webes alkalmazás Botot létrehozásához.
    4. Várjon, amíg a webes alkalmazás Botot erőforrás létrehozni.

3. Az Azure portálon szerkessze az imént létrehozott webes alkalmazás Botot erőforrás.

    1. Kattintson a bal oldali "Alkalmazásbeállítások" nav elemre
    1. Görgessen le a "App Settings" szakasz
    2. Adja hozzá ezeket a beállításokat:

        Környezeti változó | érték
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | „https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/”
        CONVERSATION_LEARNER_APP_ID      | Alkalmazás azonosító GUID, a beszélgetés tanuló felhasználói felületén a "beállítások" az alkalmazás nyert >
        LUIS_AUTHORING_KEY               | Az alkalmazás kulcs szerzői LUIS
    
    4. Kattintson a "Mentés" a lap tetején
    5. A bal oldali "Build" navigációs elem megnyitása
    6. Kattintson az "A folyamatos üzembe helyezés beállítása" 
    7. Kattintson a "Telepítés" ikonra a központi telepítések
    8. Kattintson a "Kötelező beállítások"
    9. Válassza ki a forrás, amennyiben rendelkezésre áll-e a botot kódot, és konfigurálja a forrás.
