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
ms.openlocfilehash: 05140693026e21a73b756ed0ea7bc9936bef067e
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173298"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Használja az Elágaztatás és olyan műveletek visszavonásához
Ebben az oktatóanyagban azt lép, visszavonás és elágazási műveletek.


## <a name="details"></a>Részletek
- A Visszavonás: lehetővé teszi, hogy a fejlesztő "Visszavonás" felhasználói bevitel vagy művelet közül választhat. A színfalak mögött "Visszavonás" ténylegesen hoz létre egy új párbeszédpanel, és újra játszik legfeljebb az előző lépésben.  Ez azt jelenti, hogy az entitás észlelési visszahívási és API-hívások a párbeszédpanelen a rendszer újra meghívja.

- Ág: létrehoz egy új train párbeszédpanel, amely ugyanúgy megkezdi a párbeszédpanelen egy meglévő betanításához – ez menti a munka, amelyet manuálisan újra belépés párbeszédpanel bekapcsolja. A színfalak mögött "ág" hoz létre egy új párbeszédpanel, és újra játszik a meglévő train párbeszédpanelen a kijelölt pontig.  Ez azt jelenti, hogy az entitás észlelési visszahívási és API-hívások a párbeszédpanelen a rendszer újra meghívja.


## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e a kétpizzás rendelés robot:

    npm run demo-pizza

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen modell-lista kattintson a TutorialDemo Kétpizzás sorrendben. 

A rendelés Kétpizzás bemutató részletes ismertetéséért lásd a következő Kétpizzás rendelés oktatóanyagot.

## <a name="undo"></a>Visszavonás

Azt fogja visszavonása szerepel a párbeszédpanelen, és hozza létre újra ezt a lépést.

### <a name="training-dialogs"></a>Képzési párbeszédpanelek
Kezdjük a betanítás. 

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
1. Adja meg az "order kétpizzás egy'.
2. Kattintson a pontszám művelet.
3. Válassza ki a kattintson a "mit szeretne a a kétpizzás?"
4. Adja meg a "gomba és sajtok iránti szenvedélyének".
5. Kattintson a pontszám műveletek.
3. Kattintással jelölje ki a "nincs $Toppings a kétpizzás".
6. Válassza a "Szeretné bármi más?"
7. Adja meg a "gomba eltávolítása és paprikát hozzáadása".
    - Válassza ki, és az ENSZ-ellenőrzés a Toppings entitás. Egy műveletet, amely visszavonható hozunk létre.
2. Kattintson a visszavonási lépést.
    - Az utolsó bejegyzés törlődik, és azt vissza a következő "Szeretné bármi más?"  (az alábbi képernyőképen)
2. Adja meg a "gomba eltávolítása és paprikát hozzáadása".
8. Jelölje be a "nincs $Toppings a kétpizzás"
    - Ellenőrizze, hogy mindkét entitások megfelelően van-e kijelölve.
2. Kattintson a pontszám művelet. Továbbra is a javított párbeszédpanel most.
4. Kattintson a tanítási kész gombra.

Most láthatta visszavonási egy felhasználói bevitel eltávolítása és a művelet használata.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Ág

Tegyük fel most egy meglévő train párbeszédpanel megnyitásához, és hozzon létre egy másik train párbeszédpanel elágaztatás.

1. Kattintson a vonat párbeszédpanelek, majd a "új rendelés" a meglévő párbeszédpanel megnyitásához. 
2. Kattintson a "nem" a párbeszédpanel az utolsó (lásd az alábbi képernyőképen).
3. Kattintson az ág.
    - "nem" is törlődik, és a teljes párbeszédpanel szövegre adott találatokat átmásolja az új. 
    - Ezzel időt takarít meg újra meg az előző kerül, Fedezze fel az új "ág" ettől kezdve.
1. Írja be az "Igen".
2. Kattintson a pontszám művelet.
3. Válassza ki a "Nincs $Toppings a kétpizzás".
6. Válassza a "Szeretné bármi más?"
7. "Nem" adja meg.
4. Kattintson a tanítási kész gombra.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Verziókezelés és címkézése](./16-versioning-and-tagging.md)
