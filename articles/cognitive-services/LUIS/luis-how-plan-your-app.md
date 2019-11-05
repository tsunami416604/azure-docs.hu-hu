---
title: Az alkalmazás megtervezése – LUIS
titleSuffix: Azure Cognitive Services
description: Körvonalazza a releváns alkalmazás-leképezéseket és entitásokat, majd hozza létre az alkalmazási terveket Language Understanding intelligens szolgáltatásokban (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467696"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>A LUIS-alkalmazás sémájának megtervezése a tulajdonos tartomány és az adatkiemelés alapján

A LUIS-alkalmazás sémája a tárgy tartományához kapcsolódó leképezéseket és entitásokat tartalmaz. A cél a felhasználói hosszúságú kimondott szöveg besorolása, az entitások pedig kinyerik az adatait a felhasználói hosszúságú kimondott szöveg. 

## <a name="identify-your-domain"></a>A tartomány azonosítása

A LUIS-alkalmazások egy adott tartományszintű témakör köré épülnek.  Előfordulhat például, hogy olyan utazási alkalmazást futtat, amely jegyek, repülőjáratok, szállodák és bérelt autók foglalását végzi. Egy másik alkalmazás a gyakorlásával, a fitness-erőfeszítések nyomon követésével és a célok beállításával kapcsolatos tartalmakat is biztosít. A tartomány azonosításával olyan szavakat vagy kifejezéseket találhat, amelyek fontosak a tartomány számára.

> [!TIP]
> A LUIS [előre elkészített tartományokat](luis-how-to-use-prebuilt-domains.md) kínál számos gyakori forgatókönyvhöz.
> Ellenőrizze, hogy használható-e előre összeépített tartomány az alkalmazás kiindulási pontjaként.

## <a name="identify-your-intents"></a>A szándékok azonosítása

Gondoljon az alkalmazás feladatához fontos [szándékokra](luis-concept-intent.md) . 

Tegyük fel például, hogy egy utazási alkalmazás példája egy repülés lefoglalására és az időjárási adatok ellenőrzésére a felhasználó célhelyén. Megadhatja a műveletekhez tartozó `BookFlight` és `GetWeather` leképezéseket. 

A további funkciókat tartalmazó összetettebb alkalmazásokban több szándék áll rendelkezésére, és körültekintően kell meghatároznia azokat, hogy a szándékok ne legyenek túl konkrétak. Előfordulhat például, hogy a `BookFlight` és az `BookHotel`nak külön szándékra van szüksége, de a `BookInternationalFlight` és a `BookDomesticFlight` túl hasonló lehet.

> [!NOTE]
> Az ajánlott eljárás az, hogy csak annyi szándékot használjon, amennyire az alkalmazás funkcióinak elvégzéséhez szükség van. Ha túl sok leképezést határoz meg, akkor a LUIS a hosszúságú kimondott szöveg helyes besorolása érdekében nehezebbé válik. Ha túl kevést ad meg, előfordulhat, hogy az általános átfedésben van.

Ha nem kell azonosítania az általános felhasználói szándékot, adja hozzá az összes példa felhasználói hosszúságú kimondott szöveg a nincs szándékhoz. Ha az alkalmazás egyre nagyobb szándékot igényel, később is létrehozhatja őket. 

## <a name="create-example-utterances-for-each-intent"></a>Példa hosszúságú kimondott szöveg létrehozása az egyes szándékokhoz

A leképezések meghatározása után hozzon létre 15 – 30 példa hosszúságú kimondott szöveg az egyes szándékokhoz. A kezdéshez ne legyen kevesebb ennél a számnál, vagy hozzon létre túl sok hosszúságú kimondott szöveg az egyes leképezésekhez. Minden Kimondás nem lehet azonos az előzőtől. A hosszúságú kimondott szöveg jó választéka magában foglalja a szavak teljes darabszámát, a szavak megválasztását, a művelet időpontját és a központozást. 

További információkért tekintse át a [hosszúságú kimondott szöveg](luis-concept-utterance.md) .

## <a name="identify-your-entities"></a>Entitások azonosítása

A példában hosszúságú kimondott szöveg azonosítsa a kibontani kívánt entitásokat. Egy járat lefoglalásához olyan információra van szüksége, mint a cél, a dátum, a légitársaság, a jegy kategóriája és az utazási osztály. Hozzon létre entitásokat ezekhez az adattípusokhoz, majd jelölje meg az [entitásokat](luis-concept-entity-types.md) a példában szereplő hosszúságú kimondott szöveg, mert fontosak a szándék megvalósításához. 

Ha meghatározza, hogy mely entitásokat szeretné használni az alkalmazásban, vegye figyelembe, hogy különböző típusú entitások vannak az objektumok típusai közötti kapcsolatok rögzítéséhez. A [Luis-entitások](luis-concept-entity-types.md) részletesebben ismertetik a különböző típusokat.

## <a name="next-steps"></a>További lépések

Ismerje meg a tipikus [fejlesztési ciklust](luis-concept-app-iteration.md).  