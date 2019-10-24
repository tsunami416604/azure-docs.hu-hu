---
title: Elágazó és visszavonási műveletek használata Conversation Learner modellel – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használhatja az elágazó és a visszavonási műveleteket egy Conversation Learner modellel.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f4f3024451696dbd0244d9da39cba67b49447af1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703628"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Elágazási és visszavonási műveletek használata
Ebben az oktatóanyagban visszavonási és elágazási műveleteket végezünk.


## <a name="details"></a>Részletek
### <a name="undo"></a>Visszavonás
Lehetővé teszi, hogy a fejlesztő "visszavonja" a legutóbbi felhasználói bevitelt vagy műveletet. A háttérben a "Visszavonás" nevű művelet ténylegesen új párbeszédpanelt hoz létre, és az előző lépéssel játssza újra.  Ez azt jelenti, hogy az entitás-észlelési visszahívási és API-hívások a párbeszédablakban újra meghívva lesznek.

### <a name="branch"></a>Ág
Létrehoz egy új vonat párbeszédpanelt, amely ugyanúgy kezdődik, mint egy meglévő betanítási párbeszédpanel – ezzel a művelettel megtakaríthatja a manuális újbóli bevitelt a párbeszédablakban. A színfalak mögött az "ág" új párbeszédpanelt hoz létre, és a meglévő vonat párbeszédablakot a kijelölt lépésig játssza le.  Ez azt jelenti, hogy az entitás-észlelési visszahívási és API-hívások a párbeszédablakban újra meghívva lesznek.


## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy a pizza-rendeléseket használó robot fusson:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>A bemutató megnyitása vagy importálása

Ha már használta a pizza-rendezési oktatóanyagot, egyszerűen nyissa meg a modellt a webes felhasználói felületen lévő listából. Ellenkező esetben az "oktatóanyagok importálása" elemre kell kattintania, és ki kell választania a "demo-PizzaOrder" nevű modellt.

## <a name="undo"></a>Visszavonás

Az alábbi példa bemutatja, hogyan tekintheti meg `Undo` a funkciót a következő műveletekben:

### <a name="training-dialogs"></a>Betanítási párbeszédpanelek
1. A bal oldali panelen kattintson a "betanítási párbeszédpanelek" elemre `New Train Dialog` , majd a gombra.
2. Írja be a "pizza rendelése" kifejezést.
3. Kattintson a `Score Actions` gombra.
4. Ide kattintva kiválaszthatja a "mit szeretne a pizzán?"
5. Írja be a "Anything" kifejezést.
6. Kattintson a `Undo` gombra.
    - A rendszer eltávolítja az utolsó bejegyzést, és elhagyja a "mit szeretne a pizzában?" című rész utolsó válaszát.

## <a name="branch"></a>Ág

Ebben a bemutatóban egy meglévő betanítási párbeszédpanelt nyitunk meg, és létrehozunk egy új betanítási párbeszédpanelt az elágazás alapján.

1. A bal oldali panelen kattintson a "betanítási párbeszédpanelek" elemre.
2. Figyelje meg, hogy a rácson csak egy olyan képzés jelenik meg, amely az "új sorrend" kifejezéssel kezdődik.
3. A rácsban kattintson az "új sorrend" elemre a meglévő vonat párbeszédpanel megnyitásához.
4. Kattintson az utolsó "nem" gombra a párbeszédpanelen.
5. Kattintson a "ág" ikonra, a képen piros színnel jelenik meg:
    - ![](../media/tutorial15_branch.PNG)
    - A "nem" megelőzően a teljes betanítási párbeszédablak egy új betanítási párbeszédablakba másolódik.
    - Ezzel a lépéssel újra beírhatja az előző fordulatot egy új "ág" beszélgetési pont megismeréséhez.
6. Írja be az "igen" értéket, és nyomja meg az ENTER billentyűt
7. Kattintson a `Score Actions` gombra.
    - Ekkor a robot automatikusan választ választ, de nem tetszik a válasz, ezért módosítani fogjuk.
8. Kattintson az utolsó bot-válaszra.
    - Ez lehetővé teszi, hogy válasszon egy másik választ.
9. Válassza a "UseLastToppings" lehetőséget.
10. Kattintson a `Score Actions` gombra.
    - A bot ismét automatikusan választ választ. Tegyük fel, hogy "a kolbász, a sajt és a gombák szerepelnek a pizzán." 
    - Ezúttal a válaszhoz hasonlóan fogjuk tartani.
11. Kattintson a `Score Actions` gombra.
    - A robot ismét választ kér a válaszra, azaz "szeretne bármi más?"
12. Írja be a "nem" értéket.
13. Kattintson a `Save Branch` gombra.
14. Figyelje meg, hogy a rácsnak most két tanítása van, amelyek az "új sorrend" kifejezéssel kezdődnek.
    - Az egyik közülük az, amelyet az ág kikapcsolására használt.
    - A másik pedig az imént mentett elágazó verzió.
    - Kattintson mindegyikre, hogy ellenőrizze ezeket az elvárásokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Verziószámozás és címkézés](./18-version-tag.md)
