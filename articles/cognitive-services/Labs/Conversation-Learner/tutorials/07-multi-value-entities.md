---
title: Többértékű entitások használata a beszélgetés Learner modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja a többértékű entitásokat a beszélgetés Learner modellel.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 07550ed9c78aa584e55403d225eef4bcff33b057
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167422"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Beszélgetéstanuló modell többértékű entitások használata
Ez az oktatóanyag bemutatja az entitások a többértékű tulajdonság.

## <a name="video"></a>Videó

[![Többértékű entitások oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy az általános oktatóanyag Bot fut.

    npm run tutorial-general

## <a name="details"></a>Részletek
Többértékű entitások gyűlnek értékek listáját ahelyett, hogy egyetlen érték tárolására.  Ezek az entitások akkor hasznos, ha a felhasználók egynél több értéket adhat meg. A példában egy kétpizzás toppings.

Entitások megjelölt többértékű egyes ismert példányok az entitás egy listát a Bot memóriában hozzáfűzi fog rendelkezni. Az entitás értéket, hanem felülírják a későbbi felismerés fűzi hozzá.

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapon a webes felhasználói felületen.

### <a name="create-the-model"></a>A modell létrehozása

1. Válassza ki **új modell**.
2. Adja meg **MultiValueEntities** a **neve**.
3. Kattintson a **Létrehozás** gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. Válassza ki **entitások** a bal oldali panelen, majd **új entitás**.
2. Válassza ki **betanított egyéni** a **entitástípus**.
3. Adja meg **toppings** a **entitásnév**.
4. Ellenőrizze **többértékű** ahhoz, hogy az entitás egy vagy több értéket összeadódhatnak.
5. Ellenőrizze **negálható**.
6. Kattintson a **Létrehozás** gombra.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. Válassza ki **műveletek** a bal oldali panelen, majd **új művelet**.
2. Adja meg **az alábbiakban a toppings: $toppings** a **robot a válasz...** . A vezető dollárjel entitáshivatkozás jelzi.
3. Kattintson a **Létrehozás** gombra.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. Válassza ki **műveletek** a bal oldali panelen, majd **új művelet**.
2. Adja meg **milyen toppings szeretné használni?** a **robot a válasz...** .
3. Adja meg **toppings** a **kizárásának feljogosítja a**.
4. Kattintson a **Létrehozás** gombra.

Most már két műveletet.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>A modell betanítását

1. Válassza ki **Train-párbeszédpanelekhez** a bal oldali panelen, majd **új Train párbeszédpanel**.
2. Adja meg **üdv mindenkinek** az a felhasználó utterance (kifejezés) a bal oldali Csevegés panelen.
3. Válassza ki **műveletek pontozása**.
4. Válassza ki **milyen toppings szeretné használni?** a a műveletek listájáról. A PERCENTILIS 100 %-os, mint az egyetlen érvényes művelet a korlátok alapján.
5. Adja meg **sajtok iránti szenvedélyének és gomba** az a felhasználó utterance (kifejezés) a bal oldali Csevegés panelen.
6. Jelöljön ki **sajtok iránti szenvedélyének** majd **+ toppings**.
7. Jelöljön ki **gomba** majd **+ toppings**.
8. Válassza ki **műveletek pontozása**.
9. Válassza ki **az alábbiakban a toppings: $toppings** a a műveletek listájáról.
10. Adja meg **Peppert** a következő utterance a felhasználó a csevegési bal oldali panelen.
11. Jelöljön ki **bors** majd **+ toppings**.
12. Válassza ki **műveletek pontozása**.
13. Válassza ki **az alábbiakban a toppings: $toppings** a a műveletek listájáról.
14. Adja meg **sajtok iránti szenvedélyének eltávolítása** az a felhasználó harmadik utterance (kifejezés) a bal oldali Csevegés panelen.
15. Jelöljön ki **sajtok iránti szenvedélyének** majd **-toppings**.
16. Válassza ki **műveletek pontozása**.
17. Válassza ki **az alábbiakban a toppings: $toppings** a a műveletek listájáról.

![](../media/T07_training.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre betanított entitások](./08-pre-trained-entities.md)
