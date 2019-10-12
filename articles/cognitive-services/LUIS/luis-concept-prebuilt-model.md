---
title: Előre elkészített modellek – LUIS
titleSuffix: Azure Cognitive Services
description: Az előre elkészített modellek tartományok, leképezések, hosszúságú kimondott szöveg és entitások biztosítására szolgálnak. Elindíthatja az alkalmazást egy előre elkészített tartománnyal, vagy hozzáadhat egy kapcsolódó tartományt az alkalmazáshoz később.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b88801ded3dea7c7514ff117361feba3e95444ed
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264386"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Előre elkészített tartomány, cél és entitás modellek

Az előre elkészített modellek tartományok, leképezések, hosszúságú kimondott szöveg és entitások biztosítására szolgálnak. Elindíthatja az alkalmazást egy előre elkészített tartománnyal, vagy hozzáadhat egy kapcsolódó tartományt az alkalmazáshoz később. 

## <a name="types-of-prebuilt-models"></a>Az előre elkészített modellek típusai

A LUIS által készített előre összeépített modellek 3 típussal rendelkeznek. Minden modell bármikor hozzáadható az alkalmazáshoz. 

|Modell típusa|Tartalmazza|
|--|--|
|Domain|Szándékok, hosszúságú kimondott szöveg, entitások|
|Leképezések|Szándékok, hosszúságú kimondott szöveg|
|Entitások|Csak entitások| 

## <a name="prebuilt-domains"></a>Előre összeállított tartományok

A Language Understanding (LUIS) előre *elkészített tartományokat*biztosít, amelyek előre összeállított készletekből és [entitásokból](luis-concept-entity-types.md) állnak, amelyek együtt működnek a tartományok vagy az ügyfélalkalmazások általános kategóriái [között.](luis-how-to-add-intents.md) 

Az előre elkészített tartományokat betanítják, és készen állnak a LUIS-alkalmazásba való felvételre. Az előre elkészített tartományokban található szándékok és entitások teljes mértékben testreszabhatók az alkalmazáshoz való hozzáadásuk után. 

Ha egy teljes előre elkészített tartomány testreszabását indítja el, törölje azokat a leképezéseket és entitásokat, amelyeket az alkalmazásnak nem kell használnia. Hozzáadhat olyan leképezéseket vagy entitásokat is a készlethez, amelyeket az előre elkészített tartomány már biztosít. Ha **például a sportesemények** előre elkészített tartományát használja egy sportesemény-alkalmazáshoz, entitásokat adhat hozzá a sport csapatokhoz. Amikor elindítja a [hosszúságú kimondott szöveg](luis-how-to-add-example-utterances.md) -t a Luis számára, belefoglalja az alkalmazásra vonatkozó feltételeket is. LUIS megtanulja, hogy felismerje őket, és kihasználja az előkészített tartomány szándékait és entitásait az alkalmazás igényeinek megfelelően. 

> [!TIP]
> Az előre elkészített tartományban található szándékok és entitások együtt működnek a legjobban. Jobb megoldás, ha lehetséges, az azonos tartományhoz tartozó leképezéseket és entitásokat érdemes kombinálni.
> A segédprogramok előre elkészített tartománya olyan leképezéseket tartalmaz, amelyek bármely tartományban testreszabhatók. Például hozzáadhat `Utilities.Repeat` értéket az alkalmazáshoz, és betaníthatja, hogy a felhasználók milyen műveleteket végezhetnek el az alkalmazásban. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Előre elkészített tartomány leképezése viselkedésének módosítása

Előfordulhat, hogy egy előre elkészített tartomány olyan szándékot tartalmaz, amely hasonló a LUIS-alkalmazásban használni kívánt szándékhoz, de azt szeretné, hogy másképp viselkedjen. A **helyek** előre összeépített tartománya például egy `MakeReservation` szándékot biztosít éttermi foglalás készítéséhez, de azt szeretné, hogy az alkalmazás ezt a szándékot használja a szállodai foglalások elvégzésére. Ebben az esetben módosíthatja a szándék viselkedését azáltal, hogy hosszúságú kimondott szöveg biztosít a LUIS számára a szállodai foglalások létrehozásához és a `MakeReservation` szándék használatával történő címkézéséhez, így a LUIS újra betanítható úgy, hogy felismerje a (z) `MakeReservation` szándékot a Hotel lefoglalására irányuló kérelemben.

Az előre elkészített tartományokról az [előre elkészített tartományok című témakörben](./luis-reference-prebuilt-domains.md)talál teljes listát.

## <a name="prebuilt-intents"></a>Előre elkészített leképezések

A LUIS előre elkészített leképezéseket és azok hosszúságú kimondott szöveg biztosítja. A leképezések a teljes tartomány hozzáadása nélkül is felvehetők. A szándék hozzáadásával a szándék és a hosszúságú kimondott szöveg hozzáadásának folyamata. A leképezés neve és a teljes lista is módosítható.  

## <a name="prebuilt-entities"></a>Előre összeállított entitások

A LUIS előre összeállított entitásokat tartalmaz az általános típusú információk, például a dátumok, az időpontok, a számok, a mérések és a pénznemek felismeréséhez. Az előre elkészített entitások támogatása a LUIS-alkalmazás kulturális környezetében változik. A LUIS által támogatott előre elkészített entitások teljes listáját, beleértve a kulturális környezet támogatását, az [előre elkészített entitások referenciája](./luis-reference-prebuilt-entities.md)című témakörben talál.

Ha egy előre összeépített entitás szerepel az alkalmazásban, az előrejelzések a közzétett alkalmazásban is szerepelnek. Az előre elkészített entitások viselkedése előre betanítva, és **nem** módosítható. 

> [!NOTE]
> a **beépített. datetime** elavult. A rendszer helyébe a [**beépített. datetimeV2**](luis-reference-prebuilt-datetimev2.md), amely a dátum-és időtartományok felismerését, valamint a kétértelmű dátumok és időpontok jobb felismerését is lehetővé teszi.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [adhat hozzá előre elkészített entitásokat](luis-prebuilt-entities.md) az alkalmazáshoz.
