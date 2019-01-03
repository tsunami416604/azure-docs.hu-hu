---
title: Hogyan használja az Elágaztatás és a egy Beszélgetéstanuló modell – a Microsoft Cognitive Services-műveletek visszavonása |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja a Elágaztatás és a egy Beszélgetéstanuló modellel műveletek visszavonásához.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: d57270a4db2129370da7336d8a7bf012848890c2
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796778"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Elágaztatási és visszavonás műveletek használata
Ebben az oktatóanyagban azt lép, visszavonás és elágazási műveletek.


## <a name="details"></a>Részletek
### <a name="undo"></a>Visszavonás
Lehetővé teszi, hogy a fejlesztő "Visszavonás" utolsó felhasználói bemenet vagy művelet kiválasztását. A színfalak mögött "Visszavonás" ténylegesen hoz létre egy új párbeszédpanel és visszajátssza, akár az előző lépésben.  Ez azt jelenti, hogy az entitás észlelési visszahívási és API-hívások a párbeszédpanelen a rendszer újra meghívja.

### <a name="branch"></a>Ág
Létrehoz egy új train párbeszédpanel, amely ugyanúgy megkezdi egy meglévő betanításához párbeszédpanel – ez menti a munka, amelyet manuálisan újra belépés párbeszédpanel bekapcsolja. A színfalak mögött "ág" hoz létre egy új párbeszédpanel, és visszajátssza a meglévő train párbeszédpanelen a kijelölt pontig.  Ez azt jelenti, hogy az entitás észlelési visszahívási és API-hívások a párbeszédpanelen a rendszer újra meghívja.


## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e a robot, amely a kétpizzás rendelések:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Nyissa meg a, vagy importálja a Demót

Ha korábban már használta korábban sorbarendezésre kétpizzás az oktatóanyagot, majd nyissa meg a modellek a webes felhasználói felületen a listából. Szüksége lesz ellenkező esetben kattintson a "Oktatóanyagokban importálása", és válassza ki a "Demo-PizzaOrder" nevű modellt.

## <a name="undo"></a>Visszavonás

Íme egy példa bemutatja, hogyan tekintse meg a `Undo` szolgáltatást működés közben:

### <a name="training-dialogs"></a>Képzési párbeszédpanelek
1. A bal oldali panelen, kattintson a "Train-párbeszédpanelekhez", majd kattintson a `New Train Dialog` gombra.
2. Írja be a "Kétpizzás egy rendelés".
3. Kattintson a `Score Actions` gombra.
4. Válassza ki a kattintson a "Mit szeretne a a kétpizzás?"
5. Írja be a "bármit".
6. Kattintson a `Undo` gombra.
    - Az utolsó bejegyzés törlődik, és az utolsó Bot válasz a "Mit szeretne a a kétpizzás?"

## <a name="branch"></a>Ág

Ebben a bemutatóban azt fog egy meglévő Train párbeszédpanel megnyitásához, és hozzon létre egy új Train párbeszédpanel belőle elágaztatás szerint.

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez".
2. Figyelje meg, hogy a rács megtekintheti az oktatóprogram, amely csak egy "új rendelés" karakterlánccal kezdődik.
3. A rácsban kattintson az "új rendelés" a meglévő Train párbeszédpanel megnyitásához.
4. Kattintson a "nem" a párbeszédpanel az utolsó.
5. Kattintson a "Ág" ikonra, azt az ezen az ábrán pirossal bekarikázva:
    - ![](../media/tutorial15_branch.PNG)
    - A teljes Train párbeszédpanel előtt a "no" átmásolja az egy új Train párbeszédpanel.
    - Ezzel időt takarít meg újra meg az előző kerül, Fedezze fel az új beszélgetés "ág" ettől kezdve.
6. Írja be az "Igen", a találati adja meg.
7. Kattintson a `Score Actions` gombra.
    - Ezen a ponton a Bot automatikusan kiválasztja a válasz, de úgy fogjuk módosítani, hogy nem tetszik a választ.
8. Kattintson az utolsó Bot válasz.
    - Ez lesz ki egy másik választ tudassa velünk.
9. Válassza ki a "UseLastToppings".
10. Kattintson a `Score Actions` gombra.
    - A robot újra automatikusan választ választja ki. Ez legyen, "Vannak kolbászt, sajtok iránti szenvedélyének és gomba a kétpizzás.". 
    - Most azt, mint a válasz megőrizzük, így.
11. Kattintson a `Score Actions` gombra.
    - Újra a Bot automatikusan választja ki egy választ üzenetnek kell megjelennie, "Szeretné bármi más?"
12. Írja be a "nem".
13. Kattintson a `Save Branch` gombra.
14. Figyelje meg, hogy a rács most már két betanítások "új rendelés" karakterlánccal kezdődik.
    - Azok egyike, amelybe elágazhatna, hogy az azonos.
    - És a másik egy az imént mentett elágazó verziója.
    - Kattintson az egyes ellenőrizze ezeket az elvárásokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Verziókezelés és címkézése](./18-version-tag.md)
