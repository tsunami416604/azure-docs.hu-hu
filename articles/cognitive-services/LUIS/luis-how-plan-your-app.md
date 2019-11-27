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
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 6a155f4c43da03ccdc40d289742918973aa6da7b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326771"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>A LUIS-alkalmazás sémájának megtervezése a tulajdonos tartomány és az adatkiemelés alapján

A LUIS-alkalmazás sémája a tárgy [tartományához](luis-glossary.md#domain)kapcsolódó [leképezéseket](luis-glossary.md#intent) és [entitásokat](luis-glossary.md#entity) tartalmaz. A cél a felhasználói [hosszúságú kimondott szöveg](luis-glossary.md#utterance)besorolása, az entitások pedig kinyerik az adatait a felhasználói hosszúságú kimondott szöveg.

## <a name="identify-your-domain"></a>A tartomány azonosítása

A LUIS-alkalmazások a tárgy tartomány körül vannak központosítva. Előfordulhat például, hogy rendelkezik egy utazási alkalmazással, amely a jegyek, a járatok, a szállodák és a bérelt autók foglalását kezeli. Egy másik alkalmazás rendelkezhetnek gyakorló, mentességre erőfeszítések nyomon követése és célok beállítás kapcsolódó tartalmat. A tartomány azonosításával könnyebben megtalálhatja a tartományhoz kapcsolódó szavakat vagy kifejezéseket.

> [!TIP]
> A LUIS [előre elkészített tartományokat](luis-how-to-use-prebuilt-domains.md) kínál számos gyakori forgatókönyvhöz. Ellenőrizze, hogy ha használhatja egy előre elkészített tartomány kiindulási pontként az alkalmazáshoz.

## <a name="identify-your-intents"></a>A leképezések azonosítása

Gondoljon az alkalmazás feladatához fontos [szándékokra](luis-concept-intent.md) .

Vessünk egy utazási alkalmazás a functions repülőjegyet könyvet, és ellenőrizze az időjárás, a felhasználó célhelyen példát. Megadhatja a műveletekhez tartozó `BookFlight` és `GetWeather` leképezéseket.

A további funkciókat tartalmazó összetettebb alkalmazásokban több szándék áll rendelkezésére, és körültekintően kell meghatároznia azokat, hogy a szándékok ne legyenek túl konkrétak. Előfordulhat például, hogy a `BookFlight` és az `BookHotel`nak külön szándékra van szüksége, de a `BookInternationalFlight` és a `BookDomesticFlight` túl hasonló lehet.

> [!NOTE]
> Ajánlott eljárás csak annyi szándék szerint kell végeznie az alkalmazás funkcióit. Ha túl sok leképezések definiálja, válik utterances megfelelően besorolni, LUIS nehezebb. Ha túl kevést ad meg, előfordulhat, hogy az általános átfedésben van.

Ha nincs szüksége az általános felhasználói szándék azonosítására, vegye fel az összes példa felhasználói hosszúságú kimondott szöveg az `None` szándékba. Ha az alkalmazás egyre nagyobb szándékot igényel, később is létrehozhatja őket.

## <a name="create-example-utterances-for-each-intent"></a>Példa utterances az egyes leképezés létrehozása

A kezdéshez ne hozzon létre túl sok hosszúságú kimondott szöveg az egyes szándékokhoz. A szándékok meghatározása után hozzon létre 15 – 30 példa hosszúságú kimondott szöveg-t. Minden Kimondás nem lehet azonos a korábban megadott hosszúságú kimondott szöveg. A hosszúságú kimondott szöveg jó választéka magában foglalja a szavak teljes darabszámát, a szavak megválasztását, a művelet időpontját és a központozást.

További információ: [a Luis-alkalmazások jó hosszúságú kimondott szöveg megismerése](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Az entitások azonosítása

A példa megcímkézzen az kinyert kívánt entitások azonosítása. Egy járat lefoglalásához olyan információra van szüksége, mint a cél, a dátum, a légitársaság, a jegy kategóriája és az utazási osztály. Hozzon létre entitásokat ezekhez az adattípusokhoz, majd jelölje meg az [entitásokat](luis-concept-entity-types.md) a példában hosszúságú kimondott szöveg. Az entitások fontosak a szándék teljesítéséhez.

Az alkalmazásban használni kívánt entitások meghatározásakor vegye figyelembe, hogy az Objektumtípusok közötti kapcsolatok rögzítéséhez különböző típusú entitások tartoznak. A [Luis-entitások](luis-concept-entity-types.md) részletesebben ismertetik a különböző típusokat.

> [!TIP]
> A LUIS [előre összeépített entitásokat](luis-prebuilt-entities.md) kínál a gyakori, társalgási felhasználói forgatókönyvekhez. Érdemes lehet előre elkészített entitásokat használni az alkalmazásfejlesztés kiindulási pontként.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A LUIS fejlesztői lifecylce megismerése](luis-concept-app-iteration.md)

