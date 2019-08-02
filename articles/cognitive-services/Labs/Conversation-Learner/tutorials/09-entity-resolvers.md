---
title: Entitás-feloldók egy Conversation Learner modellben – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja az entitás-feloldókat Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 60c4abf1590cb91fd460cc6a2a5ba75a225ebd80
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704042"
---
# <a name="entity-resolvers"></a>Entitás-feloldók

Ez az oktatóanyag bemutatja, hogyan használhatja az entitás-feloldókat Conversation Learner

## <a name="video"></a>Videó

[![Entitás-újrasolverek oktatóanyagának előzetes verziója](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="details"></a>Részletek

- A feloldó típusa az egyéni entitások opcionális tulajdonsága.
- Az entitások feloldói kihasználják a LUIS előre betanított entitásának erejét, így további részleteket és átláthatóságot biztosítanak az egyéni entitások számára.

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapot a webes KEZELŐFELÜLETen.

### <a name="create-the-model"></a>A modell létrehozása

1. Válassza az **új modell**lehetőséget.
2. Adja meg az **entitás** -feloldókat a **név**mezőben.
3. Kattintson a **Létrehozás** gombra.

### <a name="create-a-pair-of-entities"></a>Entitások létrehozása

1. Válassza az entitások lehetőséget a bal oldali panelen, majd az **új entitás**elemet.
2. Adja meg az **entitás nevének**távozását.
3. A feloldó **típusához**válassza a **datetimeV2** lehetőséget.
4. Kattintson a **Létrehozás** gombra. Az **OK gombra**kattintva zárja be az információs felugró ablakát.
5. Ismételje meg az 1-4 **-es lépést** egy második, **datetimeV2** -feloldási típussal visszanevezett entitás létrehozásához.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Hozzon létre egy pár műveletet

1. Válassza a **műveletek** lehetőséget a bal oldali panelen, majd az **új művelet**elemet.
2. Adja meg, **hogy $Departure, és visszatér a $Return** a **bot válaszára..** .
    - FONTOS – ha a $ [entityName] karakterláncot írja be, az ENTER billentyűt lenyomva, a leConversation Learner gördülő listából pedig az entitásra kell kattintania, hogy az entitás helyett szöveg legyen.
    - Figyelje meg, hogy a **szükséges entitások** mező is megkapja ezeket az entitásokat, és nem távolíthatók el. Ez megakadályozza, hogy ez a művelet csak akkor váljon elérhetővé, ha mindkét szükséges entitás jelen van.
3. Kattintson a **Létrehozás** gombra.
4. Második művelet létrehozásához válassza az **új művelet** lehetőséget.
5. Adja meg, **hogy Mikor szeretne utazni?** a **bot válasza...**
6. Adja meg az indulási és a visszatérési értéket a nem **feljogosító entitások**számára. Ezek közlik a robottal, hogy ne végezze el ezt a műveletet, ha az entitások bármelyike tartalmaz értéket.
7. Kattintson a **Létrehozás** gombra.

![](../media/T09_actions.png)

### <a name="training"></a>Képzés

1. Tekintse meg a **betanítás: [status]** oldalt a balfelső sarokban a befejezéshez.
    - Ha ez a lehetőség túl sokáig tart, kattintson a frissítés hivatkozásra.
    - A "befejezett" betanítási állapotra azért van szükség, hogy az entitás feloldói működjenek a modell betanításakor.

2. A bal oldali panelen kattintson a "betanítási párbeszédpanelek" elemre, majd kattintson az "új vonat" gombra.
3. Gépelje be az első felhasználó teljes szövegét, "foglaljon le egy repülést". 
4. Kattintson a "pontszám műveletek" gombra.
5. Válassza ki a választ: "Mikor tervez utazni?".
6. Ha a felhasználó, válaszol a következőre: "kilépés holnap, és a jövő héten visszatérő vasárnap".
    - Figyelje meg, hogy Conversation Learner két "előre betanított dátum" entitást észlelt az adott felhasználónál.
7. Az "entitások észlelése" panelen jelölje ki a "holnap" szöveget, és válassza ki a "távozás" címkét.
8. A "vasárnap jövő héten" szöveget is címkézze a "Return" kifejezéssel
9. Kattintson a "pontszám műveletek" gombra.
    - Figyelje meg, hogy a "memória" panel tartalmazza az indulási és a visszaküldési dátumot.
    - Vigye az egérmutatót az egyes elemek fölé, és figyelje meg, hogy az entitások mikor vannak olyan dátumok, amelyek világosan rögzítik a tényleges naptári dátumot a "Sunday" vagy a "Tomorrow" értékkel szemben.
10. Válassza ki a "kilépés folyamatban..." lehetőséget. Bot-válasz.
11. Kattintson a Save (Mentés) gombra.

![](../media/T09_training.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitások enumerálása](./tutorial-enum-set-entity.md)
