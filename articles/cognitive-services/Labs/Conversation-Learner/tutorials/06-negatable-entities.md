---
title: A beszélgetés Learner modell – a Microsoft Cognitive Services negálható entitások használata |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja negálható entitásokat a beszélgetés Learner modellel.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 78dc759632c4fc3116a59ea1e5bc0b93200bca45
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168187"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>A beszélgetés Learner modell negálható entitások használata

Ez az oktatóanyag bemutatja az entitások "Negatable" tulajdonságát.

## <a name="video"></a>Videó

[![Negálható entitások oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy az általános oktatóanyag Bot fut.

    npm run tutorial-general

## <a name="details"></a>Részletek
A "Negatable" tulajdonság egy entitás lehetővé teszi mindkét normál (pozitív) címke és az entitás példánya negatív, alapján a pozitív és negatív modellek tanítása és törölni az értéket egy meglévő entitás. A "Negatable" tulajdonság beállítása rendelkező entitások a beszélgetés Leaner negálható entitásokat nevezzük.

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapon a webes felhasználói felületen.

### <a name="create-the-model"></a>A modell létrehozása

1. Válassza ki **új modell**.
2. Adja meg **NegatableEntity** a **neve**.
3. Kattintson a **Létrehozás** gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. Válassza ki **entitások** a bal oldali panelen, majd **új entitás**.
2. Válassza ki **betanított egyéni** a **entitástípus**.
3. Adja meg **neve** a **entitásnév**.
4. Ellenőrizze **Negatable** engedélyezése a felhasználóknak, hogy adjon meg egy entitás értéket, vagy tegyük fel, hogy valami probléma van *nem* entitás értékét, ezáltal törlése az egyező entitás értéket.
5. Kattintson a **Létrehozás** gombra.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. Válassza ki **műveletek** a bal oldali panelen, majd **új művelet**.
2. Adja meg **nem tudom, a neve.** a **robot a válasz...** .
3. Adja meg **neve** a **kizárásának feljogosítja a**.
4. Kattintson a **Létrehozás** gombra.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. Válassza ki **műveletek** a bal oldali panelen, majd **új művelet**.
2. Adja meg **tudom, hogy a neve. $Name.** a **robot a válasz...** .
3. Kattintson a **Létrehozás** gombra.

> [!NOTE]
> A **neve** entitás rendszer automatikusan felvette a mint a **szükséges entitások** hivatkozással a válasz utterance (kifejezés).

Most már két műveletet.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>A modell betanítását

1. Válassza ki **Train-párbeszédpanelekhez** a bal oldali panelen, majd **új Train párbeszédpanel**.
2. Adja meg **hello** az a felhasználó utterance (kifejezés) a bal oldali Csevegés panelen.
3. Válassza ki **műveletek pontozása**.
4. Válassza ki **nem tudom, a neve.** a műveletek listából. A PERCENTILIS 100 %-os, mint az egyetlen érvényes művelet a korlátok alapján.
5. Adja meg **a nevem Frank** az a felhasználó utterance (kifejezés) a bal oldali Csevegés panelen.
6. Jelöljön ki **Frank** majd **+ neve**. Negálható entitások két példánnyal rendelkezik: a (+) és ad hozzá, vagy felülírja a értéket; (-) mínusz eltávolítja az értéket.
7. Válassza ki **műveletek pontozása**. A **neve** entitás már definiálva van **Frank** a memória a modell, ezért a **tudom, hogy a neve. $Name** művelet érhető el.
8. Válassza ki **tudom, hogy a neve. $Name.** a műveletek listából.
9. Adja meg **a nevem nem Frank.** az a felhasználó utterance (kifejezés) a bal oldali Csevegés panelen.
10. Jelöljön ki **Frank** majd **-neve** törölni az értéket a a **neve** entitás.
11. Válassza ki **műveletek pontozása**.
12. Válassza ki **nem tudom, a neve.** a műveletek listából.
13. Adja meg **a nevem Susan.** az a felhasználó harmadik utterance (kifejezés) a bal oldali Csevegés panelen.
14. Jelöljön ki **Susan** majd **+ neve** 

![](../media/T06_training.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Többértékű entitások](./07-multi-value-entities.md)
