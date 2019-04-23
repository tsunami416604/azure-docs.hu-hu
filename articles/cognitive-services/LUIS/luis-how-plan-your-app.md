---
title: Az alkalmazás megtervezése
titleSuffix: Language Understanding - Azure Cognitive Services
description: Megfelelő alkalmazást szándékok és entitások, és ezután terveket hozhat létre az alkalmazás a Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 9d54cff81f39f41b60800e9b33f3b4da1a735d85
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794989"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>A LUIS alkalmazás tulajdonos tartomány, szándékok és entitások megtervezése

Fontos, hogy az alkalmazás megtervezése. A tartomány, többek között lehetséges szándékok és entitások, amely az alkalmazás a azonosítására.  

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

Miután eldöntötte a szándék fog vonatkozni, hozzon létre az egyes szándékot 10 vagy 15 példa utterances. Először nem kevesebb, mint ez a szám vagy hozzon létre az egyes szándékot számos kimondott szöveg. Minden kimondásakor eltér az előző utterance (kifejezés) kell lennie. A megcímkézzen jó különböző általános word-count, a word választás, művelet igeidőt és írásjelek tartalmazza. 

## <a name="identify-your-entities"></a>Az entitások azonosítása

A példa megcímkézzen az kinyert kívánt entitások azonosítása. Könyvet a repülőút, szükség lesz néhány adatra, mint például a cél, dátum, légitársaság, jegy kategória, és továbbítani osztály. Entitások létrehozása a ezeken az adattípusokon, és megjelölheti a [entitások](luis-concept-entity-types.md) a példa megcímkézzen mert azok fontos megjelölésű teljesítéséért. 

Ha azt állapítja meg, mely entitások használata az alkalmazásban, vegye figyelembe, hogy nincsenek-e eltérő típusú entitásokat típusú objektumok közötti kapcsolatok rögzítéséhez. [A LUIS entitások](luis-concept-entity-types.md) a különböző típusaival kapcsolatos további részleteket biztosít.

## <a name="next-steps"></a>További lépések

Után az alkalmazás be van tanítva, közzétett, és lekérdezi a végpont utterances, kívánnak megvalósítani az előrejelzési fejlesztései [aktív tanulás](luis-how-to-review-endpoint-utterances.md), [listák kifejezés](luis-concept-feature.md), és [minták](luis-concept-patterns.md). 


* Lásd: [az első Language Understanding Intelligent Services (LUIS) alkalmazás létrehozása](luis-get-started-create-app.md) LUIS-alkalmazások létrehozása rövid leírását.
