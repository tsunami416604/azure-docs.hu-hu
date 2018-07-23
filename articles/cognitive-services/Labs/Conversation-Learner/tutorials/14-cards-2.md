---
title: Hogyan használja a kártyák Beszélgetéstanuló modell, rész 2 – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használja a kártyák egy Beszélgetéstanuló modellel.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1c7c88742c69041594006add76f7e3c642c64dec
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170572"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Kártya (2 1. rész) használata
Ez az oktatóanyag bemutatja, hogyan kitölthető űrlap kártya hozzáadása a robot. Hogyan helyezhetik át az űrlapmezők entitások megjelenik.

Beszélgetéstanuló vár a kártya-definíciós fájlokat, és a egy úgynevezett "kártyák", amely megtalálható a könyvtárban, ahol a robot elindult könyvtárban található.

## <a name="video"></a>Videó

[![Az oktatóanyag 14 előzetes verzió](http://aka.ms/cl-tutorial-14-preview)](http://aka.ms/blis-tutorial-14)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek

Kártyák a felhasználói felületi elemeket, amelyek lehetővé teszik a felhasználó kiválaszthatja a beállítást a beszélgetésben. 

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen modell listájában kattintson az oktatóanyag-14-kártyák – 2. 

### <a name="the-card"></a>A kártya

A kártya definíciója van a következő helyen: C:\<installedpath\>\src\cards\shippingAddress.json.

Ez a kártya három mezőt a szállítási címhez gyűjti: city, utca és állapota.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>Műveletek

Három műveletet hoztunk létre. Amint alább látható, az első művelet eredménye egy kártyát.

![](../media/tutorial14_actions.PNG)

Lássuk, hogyan jött létre a kártya művelet típusa:

- Figyelje meg, hogy a cím utca, ahol a típus Input.text és az azonosítót.
- Hasonlóképpen nincs cím, város és a egy legördülő-azonosító, cím-állapot.

Az azonosítók fontosak, ha a mezők feltöltése és elküldött, most már az entitás nevét, amely ezeket az értékeket kapja a bot.

## <a name="entities"></a>Entitások
Hogy meghatároztuk a kártya egyező, mint ahogyan fent három entitásokat.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>Műveletek

Hogy definiáltuk két műveletet.

![](../media/tutorial14_actions.PNG)

- Az első az a szállítási cím kártya, ahol a művelet típusa kártya és a sablon van kiválasztva, shippingAddress a legördülő listából.
- A második pedig vissza a szállítási cím beolvasni egy egyszerű művelet.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>Train párbeszédpanel

Nézzük végig, egy tanítási párbeszédpanel.

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
1. Adja meg "hi".
2. Kattintson a pontszám művelet.
3. Kattintással jelölje ki a "Szállítási címeket".
4. Adja meg a kártyát, és küldje el.
    - Figyelje meg, hogy ezek az értékek most helyezte az entitás a memóriába. Nincs elemzés, az űrlap már a bemeneti partíció van szükség.
5. Kattintson a pontszám műveletek.
3. Ide kattintva válassza "a szállítási $Address …".
4. Kattintson a kész tesztelése.

![](../media/tutorial14_train_dialog.PNG)

Most láthatta lekérjük az értékeket a kártya, amelynek kitölthető mezőt és a legördülő menük, és a rögzítése és gyűjtenek bot entitásokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Elágaztatási és visszavonás](./15-branching-and-undo.md)
