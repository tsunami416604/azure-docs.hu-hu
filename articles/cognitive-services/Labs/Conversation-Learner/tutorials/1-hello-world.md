---
title: A "Hello, World" beszélgetésazonosító tanuló alkalmazás – Microsoft kognitív szolgáltatások létrehozása |} Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan hozhat létre a "Hello, World" beszélgetésazonosító tanuló alkalmazást.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 30026285ac6dda45d2f5e3718aae741b928cf242
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348683"
---
# <a name="how-to-create-a-hello-world-application-with-conversation-learner"></a>Hogyan hozhat létre a "Hello, World" alkalmazást a beszélgetés tanuló

Ez az oktatóanyag bemutatja, hogyan beszélgetés tanuló, többek között a műveleteket, létre oktatási interaktív módon, és a végfelhasználók számára naplózott párbeszédpanelek korrigálás használatába.

## <a name="requirements"></a>Követelmények
Ha még nem tette meg, győződjön meg arról az összes beállítási lépéseket készültek el, beleértve a létrehozásának egy `.env` a kulcs szerzői LUIS fájlt.  Lásd: [gyors üzembe helyezés](https://github.com/Microsoft/ConversationLearner-Samples) részleteiről.

Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="steps"></a>Lépések

Indítsa el a webes felhasználói felület kezdőlapján.

### <a name="create-the-app"></a>Az alkalmazás létrehozása
1. Kattintson az új alkalmazás
2. A név mezőbe írja be a Hello World
3. Kattintson a Létrehozás gombra

### <a name="create-an-action"></a>Hozzon létre egy műveletet

1. Kattintson a Hello World alkalmazásnak, hogy indítsa el a
2. Kattintson a műveletek, majd az új művelet
    - Egy művelet lehet egy szöveges üzenetet, amely a beszélgetés tanuló visszaadja a felhasználó, az API-hívás vagy a kártya.
3. A válasz írja be a "Hello World!"
    - Ez az a választ, amely a botot ad vissza
4. Kattintson a Létrehozás gombra

A botot teheti azaz elsőként létrehozott szöveg választ.

### <a name="train-the-bot"></a>A botot képzése

#### <a name="create-the-first-dialog"></a>Hozzon létre első párbeszédpanel

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel
2. Adja meg, mi a felhasználó megtudhatja, hogy a "hello" például a beszélgetés kolduláshoz példát.
3. Kattintson a pontszám műveletek
4. Válassza ki a "Hello World!"
    - Ezzel létrehoz egy példa egy szolgálna párbeszédpanelt. 
2. Adja meg a "goodbye"
3. Kattintson a pontszám műveletek
4. Kattintson a művelet hozzáadása, majd adja meg a "Goodbye!" válaszként majd kattintson a "Létrehozás"
5. Kattintson a tanítási végezhető el. Ezzel befejezi a képzés párbeszédpanel.

Most, hogy egy tanítási párbeszédpanel a rendszerben.

#### <a name="continue-teaching-the-bot"></a>Továbbra is a botot oktatási
Most egy további képzési tegye, és tekintse meg, hogyan válaszol az a botot.

1. Kattintson az új vonat párbeszédpanel
2. Adja meg "hi nincs"
    - Ez hasonlít az első párbeszédpanel, és egy jó pontszám lekérése a botot várhatóan.
2. Kattintson a pontszám művelet
    - A pozíció és pontszám még nem elég pontosak, és további oktatási igényel.
3. Kattintson a Kiválasztás mellett a "Hello World!"
4. Adja meg az "bye"
5. Kattintson a pontszám műveletek
6. Válassza ki a "Goodbye"!
7. Kattintson a tanítási kész

![](../media/tutorial1_actions.PNG)

Hogyan működik-e a botot egy másik oktatási munkamenetet műveleteket végezzük el.

Ismételje meg a fenti lépéseket, a "hi" és "byebye", és jegyezze fel a pozíció és a botok válasz pontszám történt módosításokat, kattintson a pontszám művelet.

Ezután ismételje meg a a "howdy" és "jó bye", és vegye figyelembe, hogy rendelkezik a botot jelző pontszámok pontozási látható fejlesztések megtanulta, kommunikáció.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>A botot végfelhasználói tesztelése

1. Kattintson a napló párbeszédpanelek, majd új naplófájl párbeszédpanel
2. Írja be a "hello nincs"
3. Majd "bye"

Indítsa el a "bye" beszélgetést, és jegyezze fel a botot válasz is.

### <a name="view-conversations-in-the-log-dialogs"></a>A napló párbeszédpanel a nézet témakörök

A napló párbeszédpanelek, megtekintheti a lista beszélgetéseket, frissítése, valamint a kapcsolatok elmentse képzési párbeszédpanelek. Ehhez:

1. Kattintson a napló a témakör a
2. Ha a beszélgetés megfelelőnek tűnik, kattintson a az utolsó művelet pl. "Goodbye".
3. Ide kattintva válassza ki a javasolt választ. 
    - Válassza ki vagy adja hozzá egy újabb műveletet is.
4. Majd kattintson a Kész gombra menti ezt az képzési párbeszédpanel.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Várjon, és nem várakozási műveletek](./2-wait-vs-nonwait-actions.md)