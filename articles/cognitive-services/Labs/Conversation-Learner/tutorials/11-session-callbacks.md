---
title: Munkamenet-visszahívások használata Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használja a munkamenet-visszahívások Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 0f51b232470e4e4da3f25d40d025dd3b09dd1204
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171915"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Munkamenet-visszahívások Beszélgetéstanuló modell használatával

Ez az oktatóanyag azt mutatja be, a onSessionStart és onSessionEnd visszahívásokat.

## <a name="video"></a>Videó

[![Az oktatóanyag 11 előzetes verzió](http://aka.ms/cl-tutorial-11-preview)](http://aka.ms/blis-tutorial-11)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, amely a `tutorialSessionCallbacks` bot fut-e.

    npm run tutorial-session-callbacks

## <a name="details"></a>Részletek
Ez az oktatóanyag bemutatja a munkamenet, milyen munkamenetek alapértelmezett kezel, és hogyan adott viselkedést felülírhatja fogalmát.

Egy témakör-a robot neve. Több hellyé rendelkezhet, de nincsenek hosszú szünetek a beszélgetésben (például 30 percig).  Tekintse meg a "Korlátok" az alapértelmezett munkamenet időtúllépési időtartam a következő súgóoldalt.

Ha hosszú oldaltörések, majd a bot halad át a következő munkamenet.  Új munkamenet indítása a ütemezhetők Neurális hálózat beteszi a kezdeti állapotában.  Alapértelmezés szerint, is törli az összes entitás értéket, bár ez a viselkedés megváltoztatható (illusztrált alább).

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A modell-lista kattintson az oktatóanyag-11-SessionCallbacks. 

### <a name="entities"></a>Entitások

Négy entitások határozzák meg a modellben.

![](../media/tutorial11_entities.PNG)

Megjegyzés: az egyik dolog, hogy BotName programozott entitás.  Ehhez az entitáshoz munkamenet kezdési időpontban a robot szerint lesz beállítva.

### <a name="actions"></a>Műveletek

Négy művelet a modellben vannak meghatározva.

![](../media/tutorial11_actions.PNG)

Először ez az oktatóanyag bemutatja a munkamenet--elején entitásértékek vezérlése például BotName entitás beállítása előtt a felhasználó szerint bármit.

Ez az oktatóanyag bemutatja, a második történő megőrzésére egy munkamenet a következő értékeket.  Ebben az oktatóanyagban feltételezzük a felhasználó nevét és telefonszámát egyik munkamenetből a következő változatlanok maradnak, de, hogy azok helyétől változhat.  A Microsoft ezért megőrzése nevét és telefonszámát munkamenetek, de világos felhasználói hely között.

### <a name="train-dialog"></a>Train párbeszédpanel

Íme egy példa párbeszédpanel. Ez az egy munkamenet -, nincsenek hosszú szünetek ezen a párbeszédpanelen.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>A visszahívások programkódja

A kód a visszahívás módszer van a fájlban: c:\<installedpath > \src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Mindkét módszer sem kötelező.

- OnSessionStartCallback: Ez a módszer a BotName entitás állítja be.
- OnSessionEndCallback: megadhatja a szeretné megőrizni. Ezzel törli a felhasználónév és a felhasználó telefont kivételével minden entitás.

### <a name="try-the-bot"></a>A robot kipróbálása

A webes felhasználói felületen váltson, és kattintson a napló-párbeszédpanelekhez.

1. Adja meg a "hello".
2. Rendszer: "Üdvözöljük, vagyok Botty. Mi a neve? " amely rendelkezik a OnSessionStartCallback Botty érkező nevét.
3. Adja meg a "jason".
4. Rendszer: "Üdvözöljük jason. Mi a telefonszáma? "
5. Adja meg "555-555-5555".
6. Rendszer: "állapítható meg Botty a helynek, jason?"
7. Írja be a "Redmond".

Ez az egyetlen munkamenetben. Új munkamenet indításához kell ezt a munkamenetet. 

1. Kattintson a munkamenet időkorlátja. Ez helyezi át, a következő munkamenet.
    - A "Időtúllépés" gomb hibakeresési célra van megadva.  Tényleges-munkamenetben hosszú szünetet kellene történnek, körülbelül 30 perc.  Tekintse meg a "Korlátok" az alapértelmezett munkamenet időtúllépési időtartam a következő súgóoldalt.
1. Adja meg "hi".
2. Rendszer: "állapítható meg Botty a helynek, jason?"
    - A rendszer rendelkezik megjegyzett nevét és a telefon száma.
2. Adjon meg egy új helyet: "Seattle".
3. Rendszer: "tehát jason Ön Seattle".
4. Kattintson a kész tesztelése.

Most váltson vissza Log párbeszédpanelekhez. Figyelje meg, hogy az utolsó beszélgetés rendelkezik ketté bontása mert minden egyes log párbeszédpanel felel meg egyetlen munkamenetben.  

![](../media/tutorial11_splitdialogs.PNG)

- Az első kapcsolati Botty van beállítva, de nevét és telefonszámát nem állnak.
- A második interakció a nevét és telefonszámát számát jeleníti meg.

Most láthatta, milyen munkamenetek alapértelmezett kezel, és hogyan az alapértelmezett viselkedést felülírhatja. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [API-hívások](./12-api-calls.md)
