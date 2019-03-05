---
title: Iteratív Alkalmazástervezés
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 67bcb33727bc808f5e5bea701daffc77dde736ff
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337481"
---
# <a name="authoring-cycle-for-your-luis-app"></a>A LUIS-alkalmazás az Authoring Tool ciklus
LUIS legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések. 

![Tartalomkészítési ciklus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>A LUIS-modell létrehozása
A modell célja döntse el, mi a felhasználó kéri (a blokkolni vagy leképezés) és a kérdés mely részeit adja meg az adatokat (entitások), amely segít meghatározni, hogy a válasz. 

A modell kell lennie az app-tartomány adott szavak, hogy megállapítsuk, és, hogy van megfelelő, valamint a tipikus word rendezés kifejezések. 

A modell szándékkal, entitásokat is tartalmaz. 

## <a name="add-training-examples"></a>Példák hozzáadása
A LUIS példa utterances a szándék fog vonatkozni a kell. A példák kell elegendő változata, a word választási lehetőség és a szórendjét meg tudja határozni, melyik a cél az utterance (kifejezés) tervezték. Valamennyi példa utterance (kifejezés) rendelkeznie kell címkével ellátott entitásokként szükséges adatokat. 

A LUIS figyelmen kívül hagyja, hogy nem vonatkoznak az alkalmazás tartományhoz az utterance (kifejezés) való hozzárendelésével megcímkézzen utasította a **nincs** szándékot. Minden olyan szavak vagy kifejezések nem kell az utterance (kifejezés) kihúzott nem kell lesz. Nincs címke szót vagy kifejezést figyelmen kívül van. 

## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele
Miután 10 – 15 különböző utterances az egyes szándékot, a címkével ellátott szükséges entitásokkal, betanítását és tegye közzé. A közzététel sikeres értesítésből a hivatkozás segítségével a végpontok beolvasása. Ügyeljen arra, hogy az alkalmazás létrehozása, és tegye közzé az alkalmazást, hogy elérhető legyen a [végpont régiók](luis-reference-regions.md) van szüksége. 

## <a name="https-endpoint-testing"></a>HTTPS-végpont tesztelése
A LUIS-alkalmazás a HTTPS-végpont tesztelheti. A végpont a tesztelés lehetővé teszi, hogy a LUIS felülvizsgálatra alacsony – magabiztosan megszólalásokat kiválasztása.  

## <a name="recycle"></a>Lomtár
Amikor elkészült, a szerzői műveletek a ciklust, elkezdheti újra. Indítsa el a LUIS megjelölve alacsony magabiztosan végpont utterances áttekintése. Ellenőrizze ezeket mind a szándék entitás kimondott szöveg. Miután utterances tekintse át, a felülvizsgálat lista üresnek kell lennie.  

## <a name="batch-testing"></a>Kötegelt tesztelés
A Batch vizsgálat nem láthatják az hány példa utterances LUIS szerint kell kiértékelni. A példák a LUIS új kell lennie, és megfelelően feliratú szándékot, és azt szeretné, hogy a LUIS található entitások. A teszteredmények azt jelzik, LUIS végre arról, hogy az adott készletét kimondott szöveg. 

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [együttműködési](luis-concept-collaborator.md).
