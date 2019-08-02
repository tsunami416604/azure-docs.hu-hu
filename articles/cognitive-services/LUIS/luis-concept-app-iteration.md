---
title: Iterációs alkalmazás tervezése – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2a540606a6f9cfa790a2244628e7f0b7bef35986
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639261"
---
# <a name="authoring-cycle-for-your-luis-app"></a>A LUIS-alkalmazás szerzői ciklusa
LUIS legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések. 

![Tartalomkészítési ciklus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>A LUIS-modell létrehozása
A modell célja döntse el, mi a felhasználó kéri (a blokkolni vagy leképezés) és a kérdés mely részeit adja meg az adatokat (entitások), amely segít meghatározni, hogy a válasz. 

A modell kell lennie az app-tartomány adott szavak, hogy megállapítsuk, és, hogy van megfelelő, valamint a tipikus word rendezés kifejezések. 

A modellhez leképezések szükségesek, és _rendelkeznie kell_ entitásokkal. 

## <a name="add-training-examples"></a>Példák hozzáadása
A LUIS példa utterances a szándék fog vonatkozni a kell. A példák kell elegendő változata, a word választási lehetőség és a szórendjét meg tudja határozni, melyik a cél az utterance (kifejezés) tervezték. Valamennyi példa utterance (kifejezés) rendelkeznie kell címkével ellátott entitásokként szükséges adatokat. 

A LUIS figyelmen kívül hagyja, hogy nem vonatkoznak az alkalmazás tartományhoz az utterance (kifejezés) való hozzárendelésével megcímkézzen utasította a **nincs** szándékot. Minden olyan szavak vagy kifejezések nem kell az utterance (kifejezés) kihúzott nem kell lesz. Nincs címke szót vagy kifejezést figyelmen kívül van. 

## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele
Ha az egyes szándékokban 15 – 30 különböző hosszúságú kimondott szöveg van, és a szükséges entitások vannak megjelölve, akkor be [](luis-how-to-train.md) kell tanítania, majd [közzé](luis-how-to-publish-app.md)kell tennie. A közzététel sikeres értesítésből a hivatkozás segítségével a végpontok beolvasása. Győződjön meg arról, hogy létrehozta és közzéteszi az alkalmazást, hogy az elérhető legyen a szükséges [végpont](luis-reference-regions.md) -régiókban. 

## <a name="https-endpoint-testing"></a>HTTPS-végpont tesztelése
A LUIS-alkalmazás a HTTPS-végpont tesztelheti. A végponton végzett tesztelés lehetővé teszi, hogy a LUIS kiválassza az alacsony [](luis-how-to-review-endpoint-utterances.md)megbízhatóságú hosszúságú kimondott szöveg az ellenőrzéshez.  

## <a name="recycle"></a>Lomtár

Amikor elkészült, a szerzői műveletek a ciklust, elkezdheti újra. Első lépésként [tekintse meg a hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) Luis-t, amely alacsony megbízhatósággal van megjelölve. Ellenőrizze ezeket mind a szándék entitás kimondott szöveg. Miután utterances tekintse át, a felülvizsgálat lista üresnek kell lennie.  

Érdemes [lehet](luis-concept-version.md#clone-a-version) az aktuális verziót egy új verzióba beépíteni, majd megkezdeni a szerzői műveletek módosítását az új verzióban. 

## <a name="batch-testing"></a>Kötegelt tesztelés

A [Batch](luis-concept-batch-test.md) -teszteléssel megtekintheti, hogy a Luis hány példát hosszúságú kimondott szöveg. A példák a LUIS új kell lennie, és megfelelően feliratú szándékot, és azt szeretné, hogy a LUIS található entitások. A teszteredmények azt jelzik, LUIS végre arról, hogy az adott készletét kimondott szöveg. 

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [együttműködési](luis-concept-collaborator.md).
