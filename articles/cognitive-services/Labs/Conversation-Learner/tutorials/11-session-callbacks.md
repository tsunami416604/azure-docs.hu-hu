---
title: A beszélgetés tanuló alkalmazás – Microsoft kognitív szolgáltatások munkamenet-visszahívások használata |} Microsoft Docs
titleSuffix: Azure
description: Ismerje meg a munkamenet-visszahívások használata a beszélgetés tanuló alkalmazás.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f8970620c1f0f87ccae13d031092a048144ffb19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348630"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-application"></a>A beszélgetés tanuló alkalmazások munkamenet visszahívások használata

Ez az oktatóanyag azt mutatja be a onSessionStart és onSessionEnd visszahívások.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e a "tutorialSessionCallbacks.ts" bot.

    npm run tutorial-session-callbacks

## <a name="details"></a>Részletek
Ez az oktatóanyag egy munkamenet, alapértelmezés szerint munkamenetek kezelésének és hogyan lehet felülbírálni viselkedésmódot fogalma magában foglalja.

A munkamenet egy beszélgetést a botot. Több viszont rendelkezhet, de nincsenek hosszú szünetek a téma (például 30 perc).  Lásd a Súgó "Korlátozások" alapértelmezett munkamenet időtúllépési időtartama.

Ha hosszú oldaltörések, majd a botot halad át a következő munkamenet.  Ismétlődő Neurális hálózat egy új munkamenet indítása elhelyezi a kezdeti állapotot.  Alapértelmezés szerint azt is törli az összes entitás értéket, bár ez a viselkedés akkor módosítható (illusztrált alatt).

### <a name="open-the-demo"></a>Nyissa meg a bemutató

Az alkalmazás listájában kattintson a oktatóanyag-11-SessionCallbacks. 

### <a name="entities"></a>Entitások

Négy entitások definiáltuk az alkalmazásban.

![](../media/tutorial11_entities.PNG)

Egy dolog, ami arról értesít az, hogy BotName programozott entitás.  Ez lesz beállítva, a botot által munkamenet kezdési időpontban.

### <a name="actions"></a>Műveletek

Létrehoztunk négy művelet közül választhat. 

![](../media/tutorial11_actions.PNG)

Először, ez az oktatóanyag bemutatja vezérlése entitás értékek elején. a munkamenet – például a felhasználó szerint semmit a BotName entitás beállítást.

Ez az oktatóanyag bemutatja, hogyan megőrizni a egy munkamenet a következő értékek.  Ebben az oktatóanyagban feltételezzük, hogy a felhasználó nevét és a telefonszámot változatlanok, és a következő egy munkamenet azonban, hogy azok helye változhat.  A Microsoft ezért után is nevét és telefonszámát munkamenetek, de egyszerű felhasználó helye.

### <a name="train-dialog"></a>Vonat párbeszédpanel

Íme egy példa párbeszédpanel. Ez az egy munkamenet - Ez azt jelenti, hogy nincsenek hosszú szünetek ezen a párbeszédpanelen.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>A visszahívások kódja

A fájl a kódot a visszahívási módszer van: c:\<installedpath > \src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Mindkét módszer opcionálisak.

- OnSessionStartCallback: Ez a módszer a BotName entitás állítja be.
- OnSessionEndCallback: megadhatja a kívánt törölje. Ezzel törli a felhasználónév és a felhasználó telefont kivételével valamennyi entitást.

### <a name="try-the-bot"></a>Próbálja meg a botot

Váltás a webes felhasználói felületén, majd kattintson a napló párbeszédpanel.

1. Adja meg a "Hello szövegrészt".
2. Rendszer: "Hi, vagyok Botty. A keresztneve? " a név a OnSessionStartCallback Botty származó tartalmaz.
3. Adja meg a "jason".
4. Rendszer: "Hi jason. Mi az, hogy a megadott telefonszámot? "
5. Adja meg "555-555-5555".
6. Rendszer: "állapítható meg Botty a helyet, jason?"
7. Írja be a "Redmond".

Ez az egyetlen munkamenethez. Indítson el egy új munkamenetet, igazolnia kell a munkamenet befejezéséhez. 

1. Kattintson a munkamenet időkorlátja. Ez helyezi át, a következő munkamenet.
    - A "Munkamenet időkorlátja" gombra a hibakeresési célra valósul meg.  Az aktuális munkamenetben hosszú szünetet kellene fordulhat elő, körülbelül 30 perc.  Lásd a Súgó "Korlátozások" alapértelmezett munkamenet időtúllépési időtartama.
1. Adja meg "hi".
2. Rendszer: "állapítható meg Botty a helyet, jason?"
    - A rendszer megjegyezze rendelkezik neve és a telefon.
2. Adjon meg egy új helyet: "Seattle".
3. Rendszer: "tehát jason áll budapesti".
4. Kattintson a végzett tesztelése.

Most lépjen vissza napló párbeszédpanel. Értesítés az utolsó beszélgetés osztották két mert minden egyes napló párbeszédpanel egy munkamenet felel meg.  

![](../media/tutorial11_splitdialogs.PNG)

- Az első beavatkozás a Botty be van állítva, de nevét és a telefonszám nem.
- A második interakció nevét és a telefon számát mutatja.

Most láthatta, hogyan munkamenetek alapértelmezett kezeli, és hogyan felülbírálhatja az alapértelmezett viselkedés. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [API-hívások](./12-api-calls.md)
