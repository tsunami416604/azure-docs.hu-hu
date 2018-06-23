---
title: A Node.js - Microsoft kognitív szolgáltatások segítségével beszélgetés tanuló alkalmazás létrehozása |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan hozhat létre Node.js segítségével beszélgetés tanuló alkalmazást.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a3a51aa86a30b060c8dc4113da69462904d7df54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349342"
---
# <a name="create-a-conversation-learner-application-using-nodejs"></a>Node.js segítségével beszélgetés tanuló-alkalmazás létrehozása

Beszélgetés tanuló botok kialakításának összetettsége csökkenti. Ez lehetővé teszi egy hibrid fejlesztési-munkafolyamat kézzel írt kódot és a gépi tanulási botok írása szükséges kód csökkentése érdekében. Egyes rögzített részeit, például az ellenőrzése, ha a felhasználó be van-e bejelentkezve, vagy egy API-kérés tárolási készletben, ellenőrizze az alkalmazás továbbra is kell megírni. Azonban az egyéb módosítások állapot-és művelet is ismernie kell a tartomány szakértő vagy fejlesztői példa párbeszédpanelek.

## <a name="invitation-required"></a>A meghívó szükséges

*Meghívót projekt beszélgetés tanuló eléréséhez szükséges.*

Az SDK ad hozzá a botot, és egy felhőalapú szolgáltatás, amely az SDK fér hozzá a machine Learning szolgáltatáshoz projekt beszélgetés tanuló áll.  Jelenleg a projekt beszélgetés Leaner felhőalapú szolgáltatás használatához meghívót.  Ha még nem lett már, meghívott [meghívót kérelem](https://aka.ms/conversation-learner-request-invite).  Ha még nem kapott meghívót, nem fog tudni hozzáférni a felhő API.

## <a name="prerequisites"></a>Előfeltételek

- Csomópont 8.5.0 vagy magasabb és npm 5.3.0 vagy újabb verzióját. Telepítse a [ https://nodejs.org ](https://nodejs.org).
  
- LUIS szerzői kulcs:

  1. Jelentkezzen be [ http://www.luis.ai ](http://www.luis.ai).

  2. Kattintson a jobb felső, majd a "beállítások" a nevét a

  3. Szerzői kulcs jelenik meg az eredményül kapott lap

  (A kulcs szerzői LUIS 2 szerepkörök szolgálja ki.  Először is fog szolgálni a beszélgetés tanuló szerzői kulcs.  Második beszélgetés tanuló használ a LUIS entitás kibontási; a kulcs szerzői LUIS létrehozásához használt LUIS modellek az Ön nevében)

- Google Chrome böngésző. Telepítse a [ https://www.google.com/chrome/index.html ](https://www.google.com/chrome/index.html).

- git. Telepítse a [ https://git-scm.com/downloads ](https://git-scm.com/downloads).

- VSCode. Telepítse a [ https://code.visualstudio.com/ ](https://code.visualstudio.com/). Vegye figyelembe, hogy ez ajánlott, nem szükséges.

## <a name="quick-start"></a>Első lépések 

1. Telepítés és -buildek:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Során `npm install`, figyelmen kívül hagyhatja ezt a hibát akkor, ha: `gyp ERR! stack Error: Can't find Python executable`

2. Adja meg:

   Hozzon létre egy nevű fájlt `.env` a címtárban `cl-bot-01`.  A fájl tartalmát kell lennie:

   ```
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Indítsa el a botot:

    ```
    npm start
    ```

    Ez az általános üres botot fut `cl-bot-01/src/app.ts`.

3. Futtassa a beszélgetés tanuló felhasználói felületén:

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. Nyissa meg böngészőben http://localhost:5050 

Most már használ beszélgetés tanuló és hozhat létre és a beszélgetés tanuló modell tanítása.  

> [!NOTE]
> Indításakor a projekt beszélgetés tanuló áll rendelkezésre útján.  Ha http://localhost:5050 jeleníti meg a HTTP `403` hiba miatt, ez azt jelenti, hogy a fiók nem meghívott.  Adjon [meghívót kérelem](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Oktatóanyagok, bemutatók és botok közötti váltás

A fenti utasítások az általános üres botot elindult.  Oktatóanyag futtatásához, vagy inkább bemutató botot:

1. Ha a beszélgetés tanuló webes felhasználói felületének megnyitásához, térjen vissza a következő alkalmazások listájának http://localhost:5050/home.
    
2. Ha egy másik botot fut (például `npm start` vagy `npm run demo-pizza`), állítsa le.  Nem kell a felhasználói felület folyamat leállítása, vagy zárja be a webböngészőt.

3. Bemutató bot futtassa a parancssorból (2. lépés fent).  Különböző demók a következők:

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. Ha még nem tette, váltson a beszélgetés tanuló webes felhasználói felület a Chrome-ban betöltésével http://localhost:5050/home. 

5. Kattintson az "Import oktatóanyagok" (csak azért van szükség, egyszer).  Ez körülbelül egy perce érvénybe, és másolja a beszélgetés tanuló modellek az oktatóanyagok a beszélgetés tanuló figyelembe.

6. Kattintson a a bemutató-modellt a beszélgetés tanuló felhasználói felület, amely megfelel a bemutató-t elindította.

A bemutatók forrásfájljainak szerepelnek. `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Háttér-kódot tartalmazó bot létrehozása

1. Ha a beszélgetés tanuló webes felhasználói felületének megnyitásához, térjen vissza a következő alkalmazások listájának http://localhost:5050/home.
    
2. Ha bot fut (például `npm run demo-pizza`), állítsa le.  Nem kell a felhasználói felület folyamat leállítása, vagy zárja be a webböngészőt.

3. Ha szükséges, a kód szerkesztése `cl-bot-01/src/app.ts`.

4. Építse újra, és indítsa újra a botot:

    ```bash    
    npm run build
    npm start
    ```

5. Ha még nem tette, váltson a beszélgetés tanuló webes felhasználói felület a Chrome-ban betöltésével http://localhost:5050/home. 

6. Hozzon létre egy új téma tanuló alkalmazás a felhasználói felületen, és indítsa el a tanítási.

7. A kód módosításokat `cl-bot-01/src/app.ts`, ismételje meg a fenti lépéseket, 2. lépés-től kezdődő.

## <a name="vscode"></a>VSCode

A VSCode, nincs futtatása minden bemutató, és a "üres bot" konfigurációk `cl-bot-01/src/app.ts`.  Nyissa meg a `cl-bot-01` VSCode mappájában.

## <a name="advanced-configuration"></a>Speciális konfiguráció

A sablon `.env.example` fájl bemutatja, milyen környezeti változók adhatja meg a minták konfigurálásához.

Beállíthatja, hogy ezeket a portokat, adja hozzá a gépen futó egyéb szolgáltatások közötti ütközések elkerülésével egy `.env` fájlt a projekt gyökerében:

```bash
cp .env.example .env
```

A szabványos konfigurációtól, amely lehetővé teszi a botot helyileg történő futtatása, és indíthatja beszélgetés tanuló használja.  (Meg, a botot Botot keretében történő központi telepítéséhez ennek a fájlnak bizonyos szerkesztések lesz szükség.)

## <a name="support"></a>Támogatás

- Kérdéseit [Stack Overflow](https://stackoverflow.com) a "microsoft kognitív"
- A szolgáltatás kérése a [felhasználói véleményekkel foglalkozó weblapunkra](https://aka.ms/conversation-learner-uservoice)
- Nyissa meg a problémát az a [github-tárház](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Legnagyobb mértékben járulnak hozzá

A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. További információ: a [kód a viselkedési szabályzat GYIK RÉSZÉT](https://opensource.microsoft.com/codeofconduct/faq/) , vagy forduljon [ opencode@microsoft.com ](mailto:opencode@microsoft.com) további kérdéseivel megjegyzések.

## <a name="source-repositories"></a>Forrás adattárak

- [conversationlearner-minták](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-modellek](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner alkalmas a felhasználói felület](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [helló világ](./tutorials/1-hello-world.md)
