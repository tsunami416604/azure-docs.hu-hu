---
title: Hogyan hozhat létre egy "Hello World" Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan hozhat létre egy "Hello World" Beszélgetéstanuló modellt.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 70b8f25bd699cbdb069892d65bf766ef3953f59d
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170871"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Hogyan hozhat létre egy "Hello World" modellt Beszélgetéstanuló

Ez az oktatóanyag bemutatja, hogyan Beszélgetéstanuló, a létrehozás műveletek, beleértve a oktatási interaktív módon, és a végfelhasználók naplózott párbeszédpanelek korrigálás használatának első lépései.

## <a name="video"></a>Videó

[![1. oktatóanyag előzetes verzió](http://aka.ms/cl-tutorial-01-preview)](http://aka.ms/blis-tutorial-01)


## <a name="requirements"></a>Követelmények
Ha még nem tette, először győződjön meg arról az összes telepítő lépés befejeződött, beleértve a létrehozása egy `.env` a LUIS szerzői kulcsot tartalmazó fájl.  Lásd: [rövid](https://github.com/Microsoft/ConversationLearner-Samples) részleteiről.

Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapon a webes felhasználói felületen.

### <a name="create-the-model"></a>A modell létrehozása
1. Kattintson az új modell
2. A név mezőben adja meg a "Hello World"
3. Kattintson a Létrehozás gombra

### <a name="create-an-action"></a>Hozzon létre egy műveletet

1. Kattintson a "Hello World" modellt, indítsa el a
2. Kattintson a műveletek, majd az új művelet
    - Művelet lehet, amely Beszélgetéstanuló visszaadja a felhasználó, egy API-hívás vagy kártya szöveges üzenetet.
3. A válasz írja be a "Hello World!"
    - Ez az a választ, amely visszaadja a robotot
4. Kattintson a Létrehozás gombra

A robot teheti meg, azaz az első lépésben létrehozott egy szöveges választ adja vissza.

### <a name="train-the-bot"></a>A robot betanítása

#### <a name="create-the-first-dialog"></a>Az első párbeszédpanel létrehozása

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel
2. Adjon meg egy példát, hogy mi a felhasználó meg fog jelenni a "hello" a beszélgetést, például kolduláshoz.
3. Kattintson a pontszám műveletek
4. Válassza ki a "Hello World!"
    - Ez létrehoz egy egy-kapcsolja példa párbeszédpanel. 
2. Adja meg a "goodbye"
3. Kattintson a pontszám műveletek
4. Művelet hozzáadása gombra, majd adja meg a "Viszlát!" válaszként majd kattintson a "Létrehozás"
5. Kattintson a tanítási kész gombra. Ezzel befejezi a betanítási párbeszédpanel.

Most már rendelkezik egy tanítási párbeszédpanel a rendszerben.

#### <a name="continue-teaching-the-bot"></a>A robot oktatási folytatása
Most egy további képzési lehetőségek, és tekintse meg a robot reakciója.

1. Kattintson az új Train párbeszédpanel
2. Adja meg "üdv mindenkinek nincs"
    - Ez hasonlít az első párbeszédpanel, és a egy jó pontszám lekérése a robot várhatóan.
2. Kattintson a pontszám művelet
    - A pozíció és pontszám nem ennek ellenére még lehet elég pontosak, és további oktatási igényelnek.
3. Kattintson a kijelölés mellett a "Hello World!"
4. Majd adja meg a "bye"
5. Kattintson a pontszám műveletek
6. Válassza ki a "goodbye" értékre!
7. Kattintson a kész tanítás

![](../media/tutorial1_actions.PNG)

A robot működésének megtekintéséhez egy másik oktatási munkamenet fogunk jutni.

Ismételje meg a fenti lépéseket, a "hi" és "byebye", és vegye figyelembe a pozíció és a robotok válasz pontszám a módosításokat, kattintson a pontszám művelet.

Most ismételje meg az "howdy" és "Viszlát" lépést, és vegye figyelembe, hogy rendelkezik a robot jelző pontszámok pontozási látható fejlesztések megtudhatta, ennek az interakciónak.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>A robot tesztelje, a felhasználó

1. Kattintson a napló párbeszédpanelek, majd az új naplófájl párbeszédpanel
2. Írja be a "helló"
3. Majd a "bye"

Próbálja meg elindítani a "bye" beszélgetést, és jegyezze fel a robot válasz is.

### <a name="view-conversations-in-the-log-dialogs"></a>A párbeszédpanelek a napló megtekintése beszélgetések

A napló párbeszédpanelek, megtekintheti a listában, beszélgetések, frissítése és interakciókat Mentés másként képzési párbeszédpanelek. Ehhez:

1. Kattintson a napló a témakör a
2. Ha a beszélgetés megfelelőnek tűnik, kattintson a az utolsó művelet például: "Goodbye"értékre.
3. Kattintással jelölje ki a javasolt választ. 
    - Válassza ki vagy adjon hozzá egy másik műveletet is.
4. Ezután kattintson a Kész gombra egy képzési párbeszédpanel, az elem mentéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Várjon, és nem várakozási művelet](./2-wait-vs-nonwait-actions.md)