---
title: Az alkalmazás megtervezése – LUIS
titleSuffix: Azure Cognitive Services
description: Megfelelő alkalmazást szándékok és entitások, és ezután terveket hozhat létre az alkalmazás a Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dc648b30dc1236080be06044f510557ae0ce9476
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638310"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>A LUIS-alkalmazás megtervezése a tulajdonos tartománnyal, szándékokkal és entitásokkal

Az alkalmazás megtervezéséhez azonosítsa a tárgy tartományát. Ide tartoznak az alkalmazáshoz kapcsolódó lehetséges szándékok és entitások.  

## <a name="identify-your-domain"></a>A tartomány azonosítása

LUIS-alkalmazásokon eltérése a tartomány-specifikus témakör.  Például előfordulhat, hogy rendelkezik egy utazási alkalmazás, amely végrehajtja a jegyeket, és szállásfoglalások egész sorát, szállodákban, bérleti autók foglalási. Egy másik alkalmazás rendelkezhetnek gyakorló, mentességre erőfeszítések nyomon követése és célok beállítás kapcsolódó tartalmat. A tartomány azonosítása segítséget szavakat vagy kifejezéseket, a tartomány számára fontos.

> [!TIP]
> A LUIS kínál [előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md) számos gyakori szituációhoz kínál.
> Ellenőrizze, hogy ha használhatja egy előre elkészített tartomány kiindulási pontként az alkalmazáshoz.

## <a name="identify-your-intents"></a>A leképezések azonosítása

Gondoljunk a [leképezések](luis-concept-intent.md) az alkalmazás a feladat fontos. Vessünk egy utazási alkalmazás a functions repülőjegyet könyvet, és ellenőrizze az időjárás, a felhasználó célhelyen példát. Megadhatja a "BookFlight" és "GetWeather" szándék fog vonatkozni, ezek a műveletek számára. Egy összetettebb alkalmazásban a további funkciók további leképezések rendelkezik, és azokat figyelmesen úgy, hogy nincs túl adott meg kell határozni. Például "BookFlight" és "BookHotel" szükség lehet a külön szándék fog vonatkozni, de "BookInternationalFlight" és "BookDomesticFlight" túl hasonló lehet.

> [!NOTE]
> Ajánlott eljárás csak annyi szándék szerint kell végeznie az alkalmazás funkcióit. Ha túl sok leképezések definiálja, válik utterances megfelelően besorolni, LUIS nehezebb. Ha túl megad néhány, így általános helyrendszerszerepkörökre egymással átfedésben lévő lehet.

## <a name="create-example-utterances-for-each-intent"></a>Példa utterances az egyes leképezés létrehozása

A leképezések meghatározása után hozzon létre 15 – 30 példa hosszúságú kimondott szöveg az egyes szándékokhoz. A kezdéshez ne legyen kevesebb ennél a számnál, vagy hozzon létre túl sok hosszúságú kimondott szöveg az egyes leképezésekhez. Minden kimondásakor eltér az előző utterance (kifejezés) kell lennie. A megcímkézzen jó különböző általános word-count, a word választás, művelet igeidőt és írásjelek tartalmazza. 

További információkért tekintse át a [hosszúságú kimondott szöveg](luis-concept-utterance.md) .

## <a name="identify-your-entities"></a>Az entitások azonosítása

A példa megcímkézzen az kinyert kívánt entitások azonosítása. Egy járat lefoglalásához olyan információra van szüksége, mint a cél, a dátum, a légitársaság, a jegy kategóriája és az utazási osztály. Hozzon létre entitásokat ezekhez az adattípusokhoz, majd jelölje meg az [entitásokat](luis-concept-entity-types.md) a példában szereplő hosszúságú kimondott szöveg, mert fontosak a szándék megvalósításához. 

Ha azt állapítja meg, mely entitások használata az alkalmazásban, vegye figyelembe, hogy nincsenek-e eltérő típusú entitásokat típusú objektumok közötti kapcsolatok rögzítéséhez. [A LUIS entitások](luis-concept-entity-types.md) a különböző típusaival kapcsolatos további részleteket biztosít.

## <a name="next-steps"></a>További lépések

Után az alkalmazás be van tanítva, közzétett, és lekérdezi a végpont utterances, kívánnak megvalósítani az előrejelzési fejlesztései [aktív tanulás](luis-how-to-review-endpoint-utterances.md), [listák kifejezés](luis-concept-feature.md), és [minták](luis-concept-patterns.md). 


* Lásd: [az első Language Understanding Intelligent Services (LUIS) alkalmazás létrehozása](luis-get-started-create-app.md) LUIS-alkalmazások létrehozása rövid leírását.
