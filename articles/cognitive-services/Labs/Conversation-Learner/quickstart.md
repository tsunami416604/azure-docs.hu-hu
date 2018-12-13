---
title: Hogyan hozhat létre egy Node.js - a Microsoft Cognitive Services használatával Beszélgetéstanuló modell |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan hozhat létre a Node.js használatával Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: b98b528d2d78d2285977dec49da18081df47e9d6
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166132"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Node.js használatával Beszélgetéstanuló modell létrehozása

Beszélgetéstanuló robotok létrehozása bonyolultságát is csökkenti. Lehetővé teszi a hibrid fejlesztési munka-folyamat lehetővé teszi kézzel írt kód és a gépi tanulás segítségével csökkentheti a robotok írása szükséges kódot. Bizonyos rögzített részei a modellt, például az ellenőrzést, ha a felhasználó be van jelentkezve, vagy egy API-kérelem ellenőrzéséhez árukészlet, így továbbra is kódolható legyen. Egyéb változások állapot-és műveleti megtanult is a tartományi szakértővel vagy fejlesztő által adott példa párbeszédpanelek.

## <a name="invitation-required"></a>Meghívó szükséges

*Meghívó projekt Beszélgetéstanuló eléréséhez szükséges.*

Projekt Beszélgetéstanuló egy SDK-t ad hozzá a robot és a egy felhőalapú szolgáltatás, amely az SDK fér hozzá a machine Learning áll.  Jelenleg a projekt beszélgetés Leaner felhőszolgáltatás használatához meghívó.  Ha még nem lett már, meghívót [meghívó igénylése](https://aka.ms/conversation-learner-request-invite).  Ha még nem kapott egy meghívást arra, nem érhető el a felhőalapú API-t fogjuk.

## <a name="prerequisites"></a>Előfeltételek

- Csomópont 8.5.0-s vagy magasabb szintű és az npm 5.3.0 vagy újabb verziója. Telepítse a [ https://nodejs.org ](https://nodejs.org).
  
- A LUIS szerzői kulcs:

  1. Jelentkezzen be [ http://www.luis.ai ](http://www.luis.ai).

  2. Kattintson a jobb felső sarkában, majd a "beállítások" nevére

  3. Kulcs létrehozási megjelenik az eredményül kapott oldalon

  (A kulcs létrehozási LUIS 2 szerepkörök szolgálja ki.  Először is fog szolgálni a kulcs létrehozási Beszélgetéstanuló.  A második Beszélgetéstanuló használ a LUIS entitások kinyeréséhez; a LUIS szerzői kulcs szerepel az Ön nevében modellek létrehozásának eljárásait a LUIS)

- Google Chrome böngésző. Telepítse a [ https://www.google.com/chrome/index.html ](https://www.google.com/chrome/index.html).

- a git. Telepítse a [ https://git-scm.com/downloads ](https://git-scm.com/downloads).

- VSCode. Telepítse a [ https://code.visualstudio.com/ ](https://code.visualstudio.com/). Vegye figyelembe, hogy ez javasolt, nem szükséges.

## <a name="quick-start"></a>Első lépések 

1. Telepítse, és hozhat létre:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Során `npm install`, figyelmen kívül hagyhatja ezt a hibát, akkor fordul elő, ha: `gyp ERR! stack Error: Can't find Python executable`

2. Konfigurálás:

   Hozzon létre egy fájlt nevű `.env` a címtárban `cl-bot-01`.  A fájl tartalmának kell lennie:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Indítsa el a robotot:

    ```
    npm start
    ```

    Ez az általános üres bot fut `cl-bot-01/src/app.ts`.

3. Beszélgetéstanuló felhasználói felület futtatása:

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. Nyissa meg böngészőben http://localhost:5050 

Beszélgetéstanuló most már használja és hozhat létre és Beszélgetéstanuló modell tanítása.  

> [!NOTE]
> Indításkor projekt Beszélgetéstanuló kapott meghívóval érhető el.  Ha http://localhost:5050 bemutatja egy olyan HTTP `403` hiba, ez azt jelenti, hogy a fiók nem kapott meghívót.  Adjon [meghívó igénylése](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Az oktatóanyagok, bemutatók és robotokat közötti váltás

A fenti útmutatást az általános üres bot elindult.  Oktatóanyag futtatásához, vagy inkább bemutató bot:

1. Ha a felhasználói felület megnyitása Beszélgetéstanuló webes, térjen vissza a modellek listáját http://localhost:5050/home.
    
2. Ha egy másik bot fut (például `npm start` vagy `npm run demo-pizza`), állítsa le.  Nem kell a felhasználói felület folyamat leállítása, vagy zárja be a webböngészőt.

3. A bemutató bot futtassa a parancssorból (2. lépés fent).  Bemutatók a következők:

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run tutorial-hybrid
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. Ha még nem tette, váltson a Beszélgetéstanuló webes felhasználói Felületére, a Chrome-ban betöltésével http://localhost:5050/home. 

5. Kattintson a "Oktatóanyagokban importálása" (csak egyszer kell megtennie kell).  Ez körülbelül egy percet vesz igénybe, és másolja a Beszélgetéstanuló modellek minden kapcsolatos Beszélgetéstanuló fiókjába.

6. Kattintson a bemutató modell a beszélgetés Learner felhasználói felületén, amely megfelel a bemutató-t elindította.

Forrásfájljainak a bemutatók szerepelnek `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Hozzon létre egy robot, amely háttér-kódot tartalmaz

1. Ha a felhasználói felület megnyitása Beszélgetéstanuló webes, térjen vissza a modellek listáját http://localhost:5050/home.
    
2. Ha egy robot fut (például `npm run demo-pizza`), állítsa le.  Nem kell a felhasználói felület folyamat leállítása, vagy zárja be a webböngészőt.

3. Ha szükséges, a kód szerkesztése `cl-bot-01/src/app.ts`.

4. Építse újra, és indítsa újra a robotot:

    ```bash    
    npm run build
    npm start
    ```

5. Ha még nem tette, váltson a Beszélgetéstanuló webes felhasználói Felületére, a Chrome-ban betöltésével http://localhost:5050/home. 

6. Beszélgetéstanuló új modell létrehozása a felhasználói felületen, és indítsa el a tanítási.

7. A kód módosításokat `cl-bot-01/src/app.ts`, ismételje meg a fenti lépéseket, kezdve a 2. lépés.

## <a name="vscode"></a>VSCode

A vscode-ban, nincs futtatása minden bemutató, valamint az "üres bot" konfigurációk `cl-bot-01/src/app.ts`.  Nyissa meg a `cl-bot-01` VSCode mappára.

## <a name="advanced-configuration"></a>Speciális konfiguráció

Egy sablon `.env.example` fájl bemutatja, milyen környezeti változók adhatja meg a minta konfigurálásához.

Ezeket a portokat, adja hozzá a gépen futó egyéb szolgáltatásokra közötti ütközések elkerülésével módosíthatja egy `.env` fájlt a projekt gyökerében:

```bash
cp .env.example .env
```

Ez az a szabványos konfigurációt, amely lehetővé teszi a robot helyileg történő futtatása, és vegye használatba Beszélgetéstanuló használ.  (Később az üzembe helyezéséhez a robot a Bot Framework, bizonyos szerkesztések ebbe a fájlba lesz szükség.)

## <a name="support"></a>Támogatás

- Kérdéseit [Stack Overflow](https://stackoverflow.com) az "a microsoft cognitive"
- A szolgáltatás igénylése a [User Voice lap](https://aka.ms/conversation-learner-uservoice)
- Nyisson egy problémát a a [GitHub-adattár](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Közreműködő

A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. További információért tekintse át a [viselkedési szabályzat GYIK dokumentumát](https://opensource.microsoft.com/codeofconduct/faq/), illetve további kérdések vagy megjegyzések esetén írjon az [opencode@microsoft.com](mailto:opencode@microsoft.com) e-mail-címre.

## <a name="source-repositories"></a>Verziókezelők

- [conversationlearner-minták](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-modellek](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner – webes csevegőt](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [helló világ](./tutorials/1-hello-world.md)
