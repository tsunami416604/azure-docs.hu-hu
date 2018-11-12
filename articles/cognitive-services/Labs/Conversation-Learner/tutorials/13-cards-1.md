---
title: Hogyan használja a kártyák Beszélgetéstanuló modell, 1. rész – a Microsoft Cognitive Services |} A Microsoft Docs
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
ms.openlocfilehash: da261beeec4f02dfa7c7cf9071e51dc17cf5c7cd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254383"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Kártya (2 1. rész) használata

Ez az oktatóanyag bemutatja, hogyan adhat hozzá, és a egy egyszerű kártya használata a robot.

> [!NOTE]
> Beszélgetéstanuló jelenleg ezt várja a kártya definíciós fájlokat, és egy "kártyák" nevű könyvtárat, amely megtalálható a címtárban található, a bot elindult. Használunk ez konfigurálható a jövőben.

## <a name="video"></a>Videó

[![Az oktatóanyag 13 előzetes verzió](https://aka.ms/cl-tutorial-13-preview)](https://aka.ms/blis-tutorial-13)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek

Kártyák a felhasználói felületi elemeket, amelyek lehetővé teszik a felhasználó kiválaszthatja a beállítást a beszélgetésben. 

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen modell-lista kattintson a – 13-kártyák – 1. oktatóanyag. 

### <a name="the-card"></a>A kártya

A kártya definíciója van a következő helyen: C:\<installedpath\>\src\cards\prompt.json.

A rendszer vár, a kártya-definíciók keresése a kártyák könyvtárban található.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Figyelje meg, hogy a törzs típusa `TextBlock` és a `{{question}}` helyőrző a szövegmezőben.
> Nincsenek a két elküldéséhez, gombok és a szöveg, amelyben az egyes elküldött beolvasása.

### <a name="actions"></a>Műveletek

Három műveletet hoztunk létre. Amint alább látható, az első művelet eredménye egy kártyát.

![](../media/tutorial13_actions.PNG)

Lássuk, hogyan jött létre a kártya művelet típusa:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> A kérdés bemeneti, és a gombok 1. és 2. Most már a kártyán hivatkozik, akkor adja meg a kérdést, és a hozzájuk tartozó válaszok. Is hivatkozhat és entitások vagy vegyesen szöveg és entitásokat.

Szem ikonra bemutatja, hogy a kártya néz ki.

### <a name="train-dialog"></a>Train párbeszédpanel

Nézzük végig, egy tanítási párbeszédpanel.

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
1. Adja meg "hi".
2. Kattintson a pontszám művelet.
3. Kattintással jelölje ki a "Rákérdezés go bal vagy jobb oldali".
    - Kattintson "a bal oldali" vagy "megfelelő" megegyezik a felhasználó beírja a "left" vagy "megfelelő" jelölik. 
4. Kattintson a pontszám műveletek.
4. Kattintással jelölje be "left". Ez az egy nem várja meg a műveletet.
6. Kattintással jelölje ki a "Rákérdezés go bal vagy jobb oldali".
4. Kattintson a "megfelelő".
5. Kattintson a pontszám műveletek.
3. Jelölje be a 'Jobbról'
6. Kattintással jelölje ki a "Rákérdezés go bal vagy jobb oldali".
4. Kattintson a kész tesztelése.

Most láthatta kártyák működését. Json-sablonok, a kártyák könyvtárban vannak definiálva. A sablonok a felületen a felhasználói felület, amely fel lehet tölteni egy karakterlánc vagy egy entitás gazdagéptípusból, vagy mindkettő használatával.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kártyák 2. rész](./14-cards-2.md)
