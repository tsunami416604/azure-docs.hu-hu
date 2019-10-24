---
title: Előre betanított entitások hozzáadása egy Conversation Learner modellhez – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan adhat hozzá előre betanított entitásokat egy Conversation Learner modellhez.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: fceca459b80725e1a9c8b7dbc6a4387ea98dbb7b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704125"
---
# <a name="how-to-add-pre-trained-entities"></a>Előre betanított entitások hozzáadása
Ez az oktatóanyag bemutatja, hogyan adhat hozzá előre betanított entitásokat a Conversation Learner modellhez.

## <a name="video"></a>Videó

[![Előre betanított entitások oktatóanyagának előzetes verziója](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="details"></a>Részletek

Az előre betanított entitások az entitások gyakori típusait ismerik fel, például számokat, dátumokat, pénzügyi mennyiségeket és egyebeket.  A "beépített", nem igényel semmilyen képzést, és a viselkedésük nem módosítható az egyéni entitásokkal szemben.  Alapértelmezés szerint az előre képzett entitások többértékű, és az entitás minden azonosított példányát összesítik.

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapot a webes KEZELŐFELÜLETen.

### <a name="create-the-model"></a>A modell létrehozása

1. Válassza az **új modell**lehetőséget.
2. Adja meg a **PretrainedEntities** **nevet**.
3. Kattintson a **Létrehozás** gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. Válassza az entitások lehetőséget a bal oldali panelen, majd az **új entitás**elemet.
2. Válassza az **előképzett/datetimeV2** lehetőséget az **entitás típusához**.
3. A **többértékű érték** megadásával engedélyezheti, hogy az entitás egy vagy több értéket gyűjtsön. Megjegyzés: az előre betanított entitások nem vonhatók le.
4. Kattintson a **Létrehozás** gombra.

![](../media/T08_entity_create.png)

1. Válassza a **műveletek** lehetőséget a bal oldali panelen, majd az **új művelet**elemet.
2. Adja meg **a következő dátumot: $Builtin-datetimev2** a **bot válaszához...**
3. Kattintson a **Létrehozás** gombra.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. Válassza a **műveletek** lehetőséget a bal oldali panelen, majd az **új művelet**elemet.
2. Adja meg **a dátumot?** a **bot válasza...** . Az előre betanított entitások nem lehetnek **kötelező entitások** , mert alapértelmezés szerint az összes hosszúságú kimondott szöveg el vannak ismerve.
3. Adja meg a **datetimev2** az **entitások kizárásához**.
4. Kattintson a **Létrehozás** gombra.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>A modell betanítása

1. Válassza a betanítási párbeszédablakok lehetőséget a bal oldali panelen, majd az **új vonat párbeszédablakban**.
2. A bal oldali csevegés paneljén adja meg a felhasználó teljes kiírásának a **Hello** értéket.
3. Válassza a **pontszám műveletek**lehetőséget.
4. Válassza ki **a dátumot?** a műveletek listából
5. Adja **meg** a felhasználót a bal oldali csevegési panelen.
    - A **mai** Kimondás automatikusan felismeri a Luis-ben előre betanított modelleket.
    - Az előre betanított entitások értékei fölé helyezve a LUIS által biztosított további adatok láthatók.

![](../media/T08_training.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitás-feloldók](./09-entity-resolvers.md)
