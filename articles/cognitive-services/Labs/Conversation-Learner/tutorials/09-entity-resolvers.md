---
title: Entitás feloldók Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
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
ms.openlocfilehash: ebe555bfd7b34efd87d400d786049964665c76e6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451059"
---
# <a name="entity-resolvers"></a>Entitás feloldók

Ez az oktatóanyag bemutatja, hogyan entitás feloldók Beszélgetéstanuló

## <a name="video"></a>Videó

[![Entitás feloldók oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek

- Feloldási típus az egyéni entitások tulajdonságot nem kötelező.
- Entitás feloldók kihasználhassák az előre betanított entitás felismerő a LUIS a további részletek és egyértelműség biztosít az egyéni entitás power.

## <a name="steps"></a>Lépések

### <a name="create-a-new-model"></a>Új modell létrehozása

1. A webes felhasználói felületen a "Új modell" gombra.
2. A "Name" mezőbe írja be a "Entitás feloldók", találati aránya adja meg, vagy a "Create" gombra.

### <a name="create-a-pair-of-entities"></a>Hozzon létre egy pár entitások

1. A bal oldali panelen kattintson a "Entitás", majd kattintson az "Új entitás" gombra.
2. A "Entitás neve" mezőjében írja be a "indító".
3. A "Feloldó típusa" legördülő menüben válassza ki a "datetimeV2".
4. A "Létrehozás" gombra.
5. Az információk előugró elolvasása után, kattintson az "OK" gombra.
6. Ugyanazokat a lépéseket követve hozzon létre "return" nevű, "datetimeV2" feloldó típussal is rendelkező másik entitás.

### <a name="create-a-pair-of-actions"></a>Hozzon létre egy pár műveletek

1. A bal oldali panelen kattintson a "Műveletek", majd kattintson az "Új Action" gombra.
2. A "Bot a válaszban" mező típusa, "a $departure elhagyása, és a $return visszaadó".
    - FONTOS – adja meg, vagy kattintson az entitásra a legördülő, írja be a "$[entityName]" találati kell Beszélgetéstanuló, különben ide, ha egy entitás helyett.
    - Figyelje meg, hogy a "Szükséges entitások" mezőt is megkapja ezeket entitásokat, és nem lehet eltávolítani. Ez megakadályozza, hogy ez a művelet váljon érhető el, amíg mindkettő szükséges entitások találhatók.
3. A "Létrehozás" gombra.
4. A "Új Action" gombra kattintva ismét létre.
5. Az a "Bot a response" mezőbe írja be, a "Ha azt tervezi, hogy utazás?".
6. A "Kizárásának entitások" mező típusa, "indító" és a is típus "return".
    - Ezek állapítható meg, hogy a robot, ez a művelet nem lépnek, ha bármelyik ezeket az entitásokat tartalmaz értéket.
7. A "Létrehozás" gombra.


### <a name="training"></a>Képzés

1. Tekintse meg a "képzés: [Status]" az a, és várja meg, hogy a rendszer "kész" részét bal felső részen található meg.
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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alternatív bemenetek](./10-alternative-inputs.md)
