---
title: Conversation Learner modell létrehozása Node. js-Microsoft Cognitive Services használatával | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan hozhat létre Conversation Learner modellt a Node. js használatával.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7ab32fb421a2c0db72652d1bbf12d312bffd5d1e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706537"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Conversation Learner modell létrehozása a Node. js használatával

Conversation Learner csökkenti a robotok létrehozásának összetettségét. Lehetővé teszi a hibrid fejlesztési munkafolyamatot, amely lehetővé teszi a kézzel írt kód és a gépi tanulás számára a robotok írásához szükséges kód mennyiségének csökkentését. A modell bizonyos rögzített részei, például annak ellenőrzése, hogy a felhasználó be van-e jelentkezve, vagy API-kérést készít az áruházbeli leltár ellenőrzéséhez, továbbra is kódolható. Az állapot és a művelet kijelölésének egyéb változásai azonban a tartományi szakértő vagy a fejlesztő által megadott párbeszédablakokból is megtekinthetők.

## <a name="invitation-required"></a>Meghívás szükséges

*A Project Conversation Learner eléréséhez meghívás szükséges.*

A Project Conversation Learner a robothoz hozzáadott SDK-val, valamint egy felhőalapú szolgáltatással, amelyet az SDK a gépi tanuláshoz fér hozzá.  Jelenleg a Project beszélgetés leaner Cloud Service-hez való hozzáféréshez meghívásra van szükség.  Ha még nem hívta meg, [kérjen meg egy](https://aka.ms/conversation-learner-request-invite)meghívót.  Ha nem kapott meghívót, nem fog tudni hozzáférni a Cloud API-hoz.

## <a name="prerequisites"></a>Előfeltételek

- Node 8.5.0 vagy magasabb, NPM 5.3.0 vagy újabb. Telepítés innen [https://nodejs.org](https://nodejs.org):.
  
- LUIS szerzői kulcs:

  1. Jelentkezzen [https://www.luis.ai](https://www.luis.ai)be.

  2. A jobb felső sarokban kattintson a nevére, majd a "beállítások" elemre.

  3. A szerzői kulcs megjelenik az eredményül kapott oldalon.

  (A LUIS authoring Key 2 szerepkört szolgál ki.  Először is a Conversation Learner szerzői kulcsként szolgál majd.  Másodszor, Conversation Learner LUIS-t használ az entitások kinyeréséhez; a LUIS authoring Key használatával LUIS-modelleket hozhat létre az Ön nevében.

- Google Chrome böngésző. Telepítés innen [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html):.

- git. Telepítés innen [https://git-scm.com/downloads](https://git-scm.com/downloads):.

- VSCode. Telepítés innen [https://code.visualstudio.com/](https://code.visualstudio.com/):. Vegye figyelembe, hogy ez az ajánlott, nem kötelező.

## <a name="quick-start"></a>Gyors üzembe helyezés 

1. Telepítés és létrehozás:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Ebben az esetben figyelmen kívül hagyhatja ezt a hibát ,haakövetkezőtörténik:`npm install``gyp ERR! stack Error: Can't find Python executable`

2. Konfigurálás:

   Hozzon létre egy `.env` nevű fájlt a `cl-bot-01`címtárban.  A fájl tartalmának a következőket kell tennie:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Kezdő robot:

    ```
    npm start
    ```

    Ezzel futtatja az általános üres robotot a alkalmazásban `cl-bot-01/src/app.ts`.

3. Böngésző megnyitása a következőhöz`http://localhost:3978`

Most már használja a Conversation Learnert, és létrehozhat és megtaníthat egy Conversation Learner modellt.  

> [!NOTE]
> Indításkor a Project Conversation Learner a meghívóval érhető el.  Ha `http://localhost:3978/ui` http`403` -hibát jelez, ez azt jelenti, hogy a fiókja nem lett meghívva.  [Kérje meg a](https://aka.ms/conversation-learner-request-invite)meghívót.

## <a name="tutorials-demos-and-switching-between-bots"></a>Oktatóanyagok, bemutatók és váltás a botok között

A fenti utasítások elindították az általános üres robotot.  Oktatóanyag vagy demó robot futtatása a következő helyett:

1. Ha a Conversation Learner webes KEZELŐFELÜLETe meg van nyitva, térjen vissza a modellek `http://localhost:3978/ui/home`listájához.
    
2. Ha egy másik robot fut (például `npm start` vagy `npm run demo-pizza`), állítsa le.  Nem kell leállítania a felhasználói felület folyamatát, vagy be kell állítania a böngészőt.

3. Futtasson egy demó robotot a parancssorból (2. lépés).  A bemutatók a következők:

   ```bash
   npm run tutorial-general
   npm run tutorial-entity-detection
   npm run tutorial-session-callbacks
   npm run tutorial-api-calls
   npm run tutorial-hybrid
   npm run demo-password
   npm run demo-pizza
   npm run demo-storage
   ```

4. Ha még nem tette meg, váltson a Chrome Conversation Learner webes felhasználói felületére a betöltéssel `http://localhost:3978/ui/home`. 

5. Kattintson az "oktatóanyagok importálása" elemre, és válassza ki a bemutató modellt a Conversation Learner felhasználói felületen, amely megfelel az elindított bemutatónak.

A bemutatók forrásfájljait a következő címen található:`cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Háttér-kódot tartalmazó robot létrehozása

1. Ha a Conversation Learner webes KEZELŐFELÜLETe meg van nyitva, térjen vissza a modellek `http://localhost:3978/ui/home`listájához.
    
2. Ha egy robot fut (például `npm run demo-pizza`), állítsa le.  Nem kell leállítania a felhasználói felület folyamatát, vagy be kell állítania a böngészőt.

3. Ha szükséges, szerkessze a `cl-bot-01/src/app.ts`kódot a alkalmazásban.

4. A robot újraépítése és újraindítása:

    ```bash    
    npm run build
    npm start
    ```

5. Ha még nem tette meg, váltson a Chrome Conversation Learner webes felhasználói felületére a betöltéssel `http://localhost:3978/ui/home`. 

6. Hozzon létre egy új Conversation Learner modellt a felhasználói felületen, és indítsa el az oktatást.

7. A kód módosításának `cl-bot-01/src/app.ts`végrehajtásához ismételje meg a fenti lépéseket a 2. lépéstől kezdve.

## <a name="vscode"></a>VSCode

A VSCode-ben az egyes demók futtatási konfigurációi, valamint az "üres bot" `cl-bot-01/src/app.ts`szerepel a következőben:.  Nyissa `cl-bot-01` meg a mappát a VSCode-ben.

## <a name="advanced-configuration"></a>Speciális konfiguráció

Egy sablonfájl `.env.example` azt mutatja be, hogy milyen környezeti változókat lehet beállítani a minták konfigurálásához.

Ezeket a portokat úgy módosíthatja, hogy elkerülje a számítógépen futó egyéb szolgáltatások közötti ütközéseket, `.env` ha egy fájlt ad hozzá a projekt gyökeréhez:

```bash
cp .env.example .env
```

Ez a szabványos konfigurációt használja, amely lehetővé teszi a robot helyi futtatását, és a Conversation Learner használatának megkezdését.  (Később, a robot a bot-keretrendszerbe való üzembe helyezéséhez a fájl szerkesztésére is szükség lesz.)

## <a name="support"></a>Támogatás

- Kérdések címkézése [stack overflow](https://stackoverflow.com) a "Microsoft kognitív"
- Szolgáltatás kérése a [felhasználói hangalapú oldalon](https://aka.ms/conversation-learner-uservoice)
- Probléma megnyitása a [GitHub](https://github.com/Microsoft/ConversationLearner-Samples) -tárházban

## <a name="contributing"></a>Hozzájárul

A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. További információért tekintse át a [viselkedési szabályzat GYIK dokumentumát](https://opensource.microsoft.com/codeofconduct/faq/), illetve további kérdések vagy megjegyzések esetén írjon az [opencode@microsoft.com](mailto:opencode@microsoft.com) e-mail-címre.

## <a name="source-repositories"></a>Forrás adattárak

- [conversationlearner – minták](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [helló világ](./tutorials/01-hello-world.md)
