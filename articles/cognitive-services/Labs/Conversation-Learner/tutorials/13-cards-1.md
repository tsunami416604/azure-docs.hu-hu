---
title: Hogyan kártyák használata a beszélgetés tanuló alkalmazások, 1 – Microsoft kognitív szolgáltatások része |} Microsoft Docs
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
ms.openlocfilehash: e90ccd42b21eea6139c402937be7e20513d73c84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348606"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Kártya (2 1. rész) használata

Ez az oktatóanyag bemutatja, hogyan lehet felvenni, és egy egyszerű kártyát használni a botot.

Vegye figyelembe, hogy a beszélgetés tanuló vár a kártya definíciós fájlokat, és egy "kártyák" nevű könyvtárat, amely a címtárban található található ahol a botot elindult.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="details"></a>Részletek

Kártyák használata a felhasználói felületi elemei, amelyek lehetővé teszik a felhasználónak választania egy beállítást a beszélgetésben. 

### <a name="open-the-demo"></a>Nyissa meg a bemutató

A webes felhasználói felület alkalmazások listájának megtekintéséhez kattintson a oktatóanyag-13-kártyák-1. 

### <a name="the-card"></a>A kártya

A kártya definíciója van a következő helyen: C:\<installedpath\>\src\cards\prompt.json.

A rendszer vár a kártya definíciók a kártyák könyvtárban található.

![](../media/tutorial13_prompt.PNG)

- Vegye figyelembe a TextBlock és a kérdés sablont.
- Nincsenek a két gombok és a szöveg, amely lekérdezi az egyes elküldött küldje el.

### <a name="actions"></a>Műveletek

Létrehoztunk három műveletet. Alább látható módon az első művelete egy kártyát.

![](../media/tutorial13_actions.PNG)

Nézzük meg, hogyan hozták létre a kártya művelet típusa:

![](../media/tutorial13_cardaction.PNG)

Jegyezze fel a kérdés bemeneti és az 1. és 2 gomb szerepel. Ezek is sablon hivatkozik a kártyán ahol meg kell adni a kérdés és a megfelelő választ. Is hivatkoznak, és használja az entitások vagy a szöveg és entitások.

A szem ikon azt ismerteti, a kártya néz.

### <a name="train-dialog"></a>Vonat párbeszédpanel

Bemutatjuk, egy tanítási párbeszédpanelen.

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
1. Adja meg "hi".
2. Kattintson a pontszám művelet.
3. Kattintással jelölje ki a "Kérdés Ugrás balra vagy jobbra".
    - Kattintson a "" vagy "bal" megegyezik felhasználói írja be a "left" vagy "jobbra" kulcsszó. 
4. Kattintson a pontszám műveletek.
4. Kattintással jelölje be "left". Ez a nem-wait-művelet.
6. Kattintással jelölje ki a "Kérdés Ugrás balra vagy jobbra".
4. Kattintson a "jobbra".
5. Kattintson a pontszám műveletek.
3. Jelölje be a 'Jobbról'
6. Kattintással jelölje ki a "Kérdés Ugrás balra vagy jobbra".
4. Kattintson a végzett tesztelése.

Most láthatta kártyák működése. A kártyák könyvtárban vannak definiálva json sablonok. A sablonok fog surface, a felhasználói felület, amely egy karakterlánc vagy egy entitás vagy vegyesen is kitöltheti.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [2. rész kártyák](./14-cards-2.md)
