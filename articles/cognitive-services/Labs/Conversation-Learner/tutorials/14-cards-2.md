---
title: Hogyan kártyák használata a beszélgetés tanuló alkalmazások, a 2 - Microsoft kognitív szolgáltatások része |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan kártyák használata a beszélgetés tanuló alkalmazás.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 254f0953fd3e281a35857e69d9795e3decebf45d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348594"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Kártya (2 1. rész) használata
Ez az oktatóanyag bemutatja, hogyan kitölthető űrlap kártya hozzáadása a botot. Ebben bemutatjuk, hogyan áthelyezi az űrlapmezők entitásokat.

Beszélgetés tanuló vár a kártya definíciós fájlok nevű "kártyák", amely megtalálható a könyvtárban, ahol a botot elindult kell lenniük.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="details"></a>Részletek

Kártyák használata a felhasználói felületi elemei, amelyek lehetővé teszik a felhasználónak választania egy beállítást a beszélgetésben. 

### <a name="open-the-demo"></a>Nyissa meg a bemutató

A webes felhasználói felület alkalmazások listájának megtekintéséhez kattintson a oktatóanyag-14-kártyák – 2. 

### <a name="the-card"></a>A kártya

A kártya definíciója van a következő helyen: C:\<installedpath\>\src\cards\shippingAddress.json.

Ez a kártya gyűjti a szállítási cím három mezők: város, utca és állapotát.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>Műveletek

Létrehoztunk három műveletet. Alább látható módon az első művelete egy kártyát.

![](../media/tutorial14_actions.PNG)

Nézzük meg, hogyan hozták létre a kártya művelet típusa:

- Figyelje meg a cím utca, ahol a típus Input.text és az azonosítót.
- Hasonlóképpen nincs város és a legördülő lista az azonosítója a cím-állapotát.

Az azonosítók fontosak, ha a mezők feltöltve és elküldött, most már az entitás nevét, amely megkapja ezeket az értékeket a botot.

## <a name="entities"></a>Entitások
A kártya megfelelő, a fenti látott három entitások definiáltuk.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>Műveletek

Definiáltuk két műveletet.

![](../media/tutorial14_actions.PNG)

- Az egyik a szállítási cím kártya, ahol a művelettípus kártya és sablon kiválasztása shippingAddress, a legördülő listából.
- A második pedig a szállítási cím vissza elolvasására egyszerű művelet.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>Vonat párbeszédpanel

Bemutatjuk, egy tanítási párbeszédpanelen.

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
1. Adja meg "hi".
2. Kattintson a pontszám művelet.
3. Kattintással jelölje ki a "Szállítási címeket".
4. Adja meg a kártya, és küldje el.
    - Figyelje meg, hogy ezeket az értékeket most áthelyezte az entitást a memóriába. Nincs elemzése szükséges, a képernyő már partíció a bemeneti adatok.
5. Kattintson a pontszám műveletek.
3. Ide kattintva válassza ki "szállítási való $Address...".
4. Kattintson a végzett tesztelése.

![](../media/tutorial14_train_dialog.PNG)

Most láthatta értékek beolvasása a kártya, amelyen kitölthető mezőket és a legördülő lista, és a rögzítése és gyűjtenek botot entitásokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ugorhat és visszavonás](./15-branching-and-undo.md)
