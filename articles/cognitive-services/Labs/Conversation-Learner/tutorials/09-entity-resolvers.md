---
title: Entitás feloldók beszélgetés Learner modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan entitás feloldók Beszélgetéstanuló.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cca78e2536a922165f40bbcbabcae005021aa70b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167847"
---
# <a name="entity-resolvers"></a>Entitás feloldók

Ez az oktatóanyag bemutatja, hogyan entitás feloldók Beszélgetéstanuló

## <a name="video"></a>Videó

[![Entitás feloldók oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy az általános oktatóanyag Bot fut.

    npm run tutorial-general

## <a name="details"></a>Részletek

- Feloldási típus az egyéni entitások tulajdonságot nem kötelező.
- Entitás feloldók kihasználhassák az előre betanított entitás felismerő a LUIS a további részletek és egyértelműség biztosít az egyéni entitás power.

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapon a webes felhasználói felületen.

### <a name="create-the-model"></a>A modell létrehozása

1. Válassza ki **új modell**.
2. Adja meg **entitás feloldók** a **neve**.
3. Kattintson a **Létrehozás** gombra.

### <a name="create-a-pair-of-entities"></a>Hozzon létre egy pár entitások

1. Válassza ki **entitások** a bal oldali panelen, majd **új entitás**.
2. Adja meg **indító** a **entitásnév**.
3. Válassza ki **datetimeV2** a **feloldó típus**.
4. Kattintson a **Létrehozás** gombra. A tájékoztató előugró ablak bezárása kiválasztásával **OK**.
5. Ismételje meg az 1 – 4 nevű második entitás létrehozásakor lépéseket **vissza** a **datetimeV2** feloldó típusa.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Hozzon létre egy pár műveletek

1. Válassza ki **műveletek** a bal oldali panelen, majd **új művelet**.
2. Adja meg **a $departure elhagyó, és a $return visszaadó** a **robot a válasz...** .
    - FONTOS – [entityName] $ találati kell beírni adja meg, vagy kattintson a legördülő egyéb Beszélgetéstanuló az entitás a mérlegeli, ez egy entitás helyett.
    - Figyelje meg, hogy a **szükséges entitások** mező is megkapja ezeket entitásokat, és nem lehet eltávolítani. Ez megakadályozza, hogy ez a művelet váljon érhető el, amíg mindkettő szükséges entitások találhatók.
3. Kattintson a **Létrehozás** gombra.
4. Válassza ki **új művelet** hozhat létre egy második műveletet.
5. Adja meg **Ha azt tervezi, hogy utazás?** a **robot a válasz...** .
6. Adja meg **indító** és **vissza** a **kizárásának entitások**. Ezek állapítható meg, hogy a robot, ez a művelet nem lépnek, ha bármelyik ezeket az entitásokat tartalmaz értéket.
7. Kattintson a **Létrehozás** gombra.

![](../media/T09_actions.png)

### <a name="training"></a>Képzés

1. Tekintse meg a **képzés: [Status]** a bal felső sarokban lévő **befejezve**.
    - A "Frissítés" hivatkozásra is kattinthat, ha túl sokáig tart.
    - Képzési állapota "Befejezve" azért szükséges, hogy az entitás feloldók működnek, ha a modell betanítását azt.

2. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd kattintson a "Train párbeszédpanel" gombra.
3. Az első felhasználó utterance (kifejezés), a "book me repülőjegyet" típusú. 
4. A "Score műveletek" gombra.
5. Válassza ki a választ, a "Ha azt tervezi, hogy utazás?".
6. A felhasználó válaszol, "elhagyó holnap és adatszolgáltató Sunday jövő héten".
    - Figyelje meg, hogyan Beszélgetéstanuló azt észlelte, hogy a felhasználó két "Előre betanított dátum" entitás kapcsolja be.
7. A "Entitás kimutatási" panelen jelölje ki a szöveget "holnap", és mint "indító" címkével
8. Is címkézését a szöveg "Sunday jövő héten", "return"
9. A "Score műveletek" gombra.
    - Figyelje meg, hogyan a "Memória" panelen az indulási és visszaadandó dátumokat tartalmaz.
    - Mindegyik mutasson, és figyelje meg, hogyan történik az entitásokat a dátum objektumokat, amely egyértelműen rögzítése a tényleges naptári dátum ellentétben a "Sunday" vagy "holnap".
10. Válassza ki a "elhagyását választotta a …" A robot választ.
11. A "Mentés" gombra.

![](../media/T09_training.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alternatív bemenetek](./10-alternative-inputs.md)
