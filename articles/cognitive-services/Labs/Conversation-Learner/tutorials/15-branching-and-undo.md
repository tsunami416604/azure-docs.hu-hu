---
title: A beszélgetés tanuló alkalmazás – Microsoft kognitív szolgáltatások műveletek visszavonása és használatáról a ugorhat |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használja a ugorhat, és visszavonja a beszélgetés tanuló alkalmazásokkal rendelkező műveleteket.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 724a9e47267e0bd7417130efe54c609ac7a465fb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348639"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Ugorhat használata és a visszavonási művelet
Az oktatóanyag azt ismerteti a visszavonás, illetve elágaztatási műveletek.

## <a name="details"></a>Részletek
- Visszavonási: lehetővé teszi, hogy a fejlesztő "Visszavonás" felhasználói bevitel vagy művelet közül választhat. A háttérben "Visszavonás" ténylegesen hoz létre egy új párbeszédpanel, és újra játszik akár az előző lépésben.  Ez azt jelenti, hogy az entitás észlelési visszahívási és API meghívja a párbeszédpanelen hívja meg a rendszer újra.

- Fiókirodai: hoz létre egy új vonat párbeszédpanel, amely egy meglévő betanítása párbeszédpanel – ez menti a részéről az erőfeszítés ugyanúgy kezdődik manuálisan újbóli megadása párbeszédpanel bekapcsolása. A háttérben "ág" hoz létre egy új párbeszédpanel, és újra szerepe a kijelölt lépés akár meglévő vonat párbeszédpanel.  Ez azt jelenti, hogy az entitás észlelési visszahívási és API meghívja a párbeszédpanelen hívja meg a rendszer újra.


## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e a pizzaszósz rendelés botot:

    npm run demo-pizza

### <a name="open-the-demo"></a>Nyissa meg a bemutató

A webes felhasználói felület alkalmazások listájának megtekintéséhez kattintson a TutorialDemo pizzaszósz sorrendje. 

A pizzaszósz rendelés bemutató a részletekért tekintse meg az pizzaszósz sorrendje.

## <a name="undo"></a>Visszavonás

Rendszer szerepel a párbeszédpanelen visszavonása, és hozza létre újra a lépésben.

### <a name="training-dialogs"></a>Képzési párbeszédpanelek
Kezdjük a betanítás. 

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
1. Adja meg az "order egy pizzaszósz".
2. Kattintson a pontszám művelet.
3. Kattintással jelölje be "mit szeretne az a pizzaszósz?"
4. Adja meg a "gomba és sajt".
5. Kattintson a pontszám műveletek.
3. Kattintással jelölje ki a "rendelkezik $Toppings a pizzaszósz".
6. Válassza a "Kívánja, hogy bármi más?"
7. Adja meg a "gomba eltávolítása és paprika hozzáadása".
    - Válassza ki a gomba és un-ellenőrzés a Toppings entitás. Létrehozzuk visszavonható művelet.
2. Kattintson a visszavonás lépésre.
    - Vegye figyelembe, hogy az utolsó tétel eltávolítása, és azt vissza a következő "Kívánja, hogy bármi más?"  (az alábbi képernyőfelvételen)
2. Adja meg a "gomba eltávolítása és paprika hozzáadása".
8. Jelölje be a "rendelkezik $Toppings a pizzaszósz"
    - Győződjön meg arról, hogy mindkét entitások megfelelően van-e kiválasztva.
2. Kattintson a pontszám művelet. Folytathatja a javított párbeszédpanel most.
4. Kattintson a tanítási végezhető el.

Adjon meg egy felhasználót visszavonása és a művelet most láthatta.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Ág

Tegyük fel most egy meglévő vonat párbeszédpanel megnyitásához, és hozzon létre egy másik vonat párbeszédpanel ugorhat.

1. Kattintson a vonat párbeszédpanelek, majd a "új order" a meglévő párbeszédpanel megnyitásához. 
2. Kattintson a "nem" a párbeszédpanel az utolsó (lásd az alábbi képernyőképen látható).
3. Kattintson a fiókirodákban.
    - Vegye figyelembe, hogy a "nem" is törlődik, és a teljes párbeszédpanel adott pontig egy új másolódik. 
    - Ez akkor ismételt megadása az előző bekapcsolása felfedezése, mely egy új "ág" ettől menti.
1. Írja be az "Igen".
2. Kattintson a pontszám művelet.
3. Válassza ki a "Rendelkezik $Toppings a pizzaszósz".
6. Válassza a "Kívánja, hogy bármi más?"
7. "Nem" adja meg.
4. Kattintson a tanítási végezhető el.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Versioning és címkézés](./16-versioning-and-tagging.md)
