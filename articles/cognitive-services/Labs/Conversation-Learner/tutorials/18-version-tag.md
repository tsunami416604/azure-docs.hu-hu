---
title: Címkézés verzió használata a beszélgetés Learner modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használható a verziókezelés és a egy Beszélgetéstanuló modellel címkézés.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5073d3ab967c4c4e1b90636c247839875a6aa0d7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086309"
---
# <a name="how-to-use-version-tagging"></a>Címkézés verzió használata

Ez az oktatóanyag bemutatja, hogyan a beszélgetés Learner modell verziói címkét, és állítsa be melyik verzió "élő".  

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz a Bot Framework-emulátor használatával hozhat létre a Log párbeszédpanelek, nem a napló párbeszédpanel webes felhasználói felületen.  

Ehhez az oktatóanyaghoz, hogy az általános oktatóanyag Bot fut:

    npm run tutorial-general

## <a name="details"></a>Részletek

Címkézett a modell verziói statikus; nem szerkeszthetők vagy módosíthatók. A modell szerkesztésekor mindig szerkeszti a fő verzióját. Amikor hozzáad egy új címkét, Beszélgetéstanuló rögzíti a modell pillanatkép abban az időpontban. 

A robot a "Live" verzióval kiválasztott modell verziót fogja használni, de rendelkezik beszélgetéseit megtekinthető lesz, csak akkor, ha a "Szerkesztés címkét" a "Master" értékre van állítva. Ha a modell "Címke szerkesztése" tulajdonsága "Master" értékre van állítva, majd a pillanatkép a modell megtekintheti, de semmilyen módon nem módosítható.

## <a name="steps"></a>Lépések

### <a name="install-the-bot-framework-emulator"></a>Telepítse a Bot Framework-emulátor

1. Nyissa meg a következőt: [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
2. Töltse le és telepítse az emulátort.

### <a name="create-a-model"></a>Modell létrehozása

1. A modell lista kezdőlapon kattintson a `New Model` gombra.
2. Az a `Name` mezőbe írja be, "Az oktatóanyag-18-Versioning", az enter.
4. A bal oldali panelen kattintson a "Beállítások" lehetőséget.
5. Másolja a vágólapra a CONVERSATION_LEARNER_MODEL_ID mező tartalmát.

### <a name="configure-the-emulator"></a>Az Emulator konfigurálása

1. Beszélgetéstanuló gyökérmappájában nyissa meg a ".env" fájlt.
2. Adjon hozzá egy sort a ".env" fájlt ehhez hasonló:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. A Beszélgetéstanuló szolgáltatás újraindításához a parancssor használatával kilép, és ismételt:
    - `npm run tutorial-general`
4. Bot Framework emulátorban hozzon létre egy új bot konfigurációt, a végponti URL-cím beállítása `http://localhost:3978/api/messages`

### <a name="version-1"></a>1-es verzió

Az 1. verzió egyetlen művelettel hozunk létre.

1. A webes felhasználói felület, a bal oldali panelen kattintson a "Műveletek", majd kattintson a `New Action` gombra.
2. A "Bot a válaszban" mezőben adja meg "Üdvözöljük ott (1-es)".
3. Kattintson a `Save` gombra.

Most már azt fogja megjelölheti ezt "1" a modell verziója.

1. A bal oldali panelen kattintson a "beállítások", majd kattintson a a ![](../media/tutorial18_version_tags.PNG)felfedéséhez "Verzió címkék" ikont a `New Tag` gomb, amely kattintson.
    - Nevezze el "1. verziójának"
1. A "Live címke" legördülő lista kiválasztása "1" verzió.  
    - A robot használatával csatornák most "1" a modell verzióját fogja használni.
    - Az entitások, műveleteket és a verzió 1 modell párbeszédpanelek Train már nem módosítható.
    - Ha "1. verziójának" címkeként a"Szerkesztés" csak lesz megtekinthetik a modellt, és nem szerkeszthetik.
    - Hagyja "szerkesztési címkét" a "Master" állítsa be, mert a modell, amely szerkeszthető csak verzióját.

Most "1. verziójának" megjelenik a "Verzió címkék" rácsban.

### <a name="version-2"></a>2-es verzió

Most már fogjuk módosítani a modellt, hogy megkülönböztesse a 1-es verzió.

1. A bal oldali panelen kattintson a "Műveletek".
2. A műveletek rácsban kattintson a "hi ott (1-es)".
3. Módosítsa a "Bot a response" mező a "hi ott (2. verzió)".
4. Kattintson a `Save` gombra.
5. Kattintson a `New Action` gombra.
6. Az a "Bot a response" mező típusa, a "bye bye (2-es)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Erősítse meg Bot Framework Emulator használatával 1. verzió

1. A Bot Framework emulátorban írja be az üzenetet, a "Hey hiba".
2. Figyelje meg, hogy a robot válaszol a "hi ott (1-es)".
    - Ez ellenőrzi, hogy 1. verzió "élő".

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>A beszélgetés naplók megtekintéséhez a beszélgetés Learner webes felhasználói felületen

1. A bal oldali panelen kattintson a "Log-párbeszédpanelekhez"
    - Ha nem lát minden párbeszédpanelek, kattintson a frissítés gombra.
2. Figyelje meg az "1. verzió" címke a rácsban.
3. A rácsban kattintson a "hi ott (1-es)"

> [!NOTE]
> Azt is javíthatja a megadottakat válassza ki az összes jelenleg elérhető Beszélgetéstanuló funkciót, azonban ezeket a szerkesztéseket jönnek létre a Master és nem 1. verzió.

Most láthatta versioning működését, és hogyan használhatja a robot a Bot Framework Emulator használatával.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bemutató – jelszó-visszaállítás](./demo-password-reset.md)
