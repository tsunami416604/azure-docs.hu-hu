---
title: A beszélgetés Learner modell – a Microsoft Cognitive Services Pre-Trained entitások hozzáadása |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan Pre-trained entitásokat ad hozzá a beszélgetés Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f5b3234c45a9ee80bc5a2c2afe67046896270802
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58163786"
---
# <a name="how-to-add-pre-trained-entities"></a>Pre-trained entitások hozzáadása
Ez az oktatóanyag bemutatja, hogyan Pre-Trained entitásokat ad hozzá a beszélgetés Learner modell.

## <a name="video"></a>Videó

[![Előre betanított entitások oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy az általános oktatóanyag Bot fut.

    npm run tutorial-general

## <a name="details"></a>Részletek

Előre betanított entitások felismerése közös típusú entitások, például a számokat, dátumokat, pénzügyi összegek és mások.  Működnek-e "-a-beépített," nem igényelnek képzést, és eltérően az egyéni entitások nem lehet módosítani a felhasználók viselkedését.  Alapértelmezés szerint a Pre-Trained entitások többértékű adatelemeket, az entitás minden egyes azonosított példány gyűlik.

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapon a webes felhasználói felületen.

### <a name="create-the-model"></a>A modell létrehozása

1. Válassza ki **új modell**.
2. Adja meg **PretrainedEntities** a **neve**.
3. Kattintson a **Létrehozás** gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. Válassza ki **entitások** a bal oldali panelen, majd **új entitás**.
2. Válassza ki **előtti-Trained/datetimeV2** a **entitástípus**.
3. Ellenőrizze **többértékű** ahhoz, hogy az entitás egy vagy több értéket összeadódhatnak. Vegye figyelembe, hogy Pre-Trained entitások negálható nem lehet.
4. Kattintson a **Létrehozás** gombra.

![](../media/T08_entity_create.png)

1. Válassza ki **műveletek** a bal oldali panelen, majd **új művelet**.
2. Adja meg **a dátum az $builtin-datetimev2** a **robot a válasz...** .
3. Kattintson a **Létrehozás** gombra.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. Válassza ki **műveletek** a bal oldali panelen, majd **új művelet**.
2. Adja meg **Mi az a dátum?** a **robot a válasz...** . Előre betanított entitások nem lehet **szükséges entitások** , azok minden utterances alapértelmezett felismeri.
3. Adja meg **beépített-datetimev2** a **kizárásának feljogosítja a**.
4. Kattintson a **Létrehozás** gombra.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>A modell betanítását

1. Válassza ki **Train-párbeszédpanelekhez** a bal oldali panelen, majd **új Train párbeszédpanel**.
2. Adja meg **hello** az a felhasználó utterance (kifejezés) a bal oldali Csevegés panelen.
3. Válassza ki **műveletek pontozása**.
4. Válassza ki **Mi az a dátum?** a a műveletek listájáról
5. Adja meg **Ma** az a felhasználó utterance (kifejezés) a bal oldali Csevegés panelen.
    - A **Ma** utterance (kifejezés) automatikusan felismeri a LUIS előre betanított modellek.
    - LUIS által biztosított további adatok felett Pre-Trained entitások értékét jeleníti meg.

![](../media/T08_training.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitás feloldók](./09-entity-resolvers.md)
