---
title: Az alkalmazás megtervezése – LUIS
description: Körvonalazza a releváns alkalmazás-leképezéseket és entitásokat, majd hozza létre az alkalmazási terveket Language Understanding intelligens szolgáltatásokban (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382309"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>A LUIS-alkalmazás sémájának megtervezése a tulajdonos tartomány és az adatkiemelés alapján

A LUIS-alkalmazás sémája a tárgy [tartományához](luis-glossary.md#domain)kapcsolódó [leképezéseket](luis-glossary.md#intent) és [entitásokat](luis-glossary.md#entity) tartalmaz. A cél a felhasználói [hosszúságú kimondott szöveg](luis-glossary.md#utterance)besorolása, az entitások pedig kinyerik az adatait a felhasználói hosszúságú kimondott szöveg.

## <a name="identify-your-domain"></a>A tartomány azonosítása

A LUIS-alkalmazások a tárgy tartomány körül vannak központosítva. Előfordulhat például, hogy rendelkezik egy utazási alkalmazással, amely a jegyek, a járatok, a szállodák és a bérelt autók foglalását kezeli. Egy másik alkalmazás a gyakorlásával, a fitness-erőfeszítések nyomon követésével és a célok beállításával kapcsolatos tartalmakat is biztosít. A tartomány azonosításával könnyebben megtalálhatja a tartományhoz kapcsolódó szavakat vagy kifejezéseket.

> [!TIP]
> A LUIS [előre elkészített tartományokat](luis-how-to-use-prebuilt-domains.md) kínál számos gyakori forgatókönyvhöz. Ellenőrizze, hogy használható-e előre összeépített tartomány az alkalmazás kiindulási pontjaként.

## <a name="identify-your-intents"></a>A szándékok azonosítása

Gondoljon az alkalmazás feladatához fontos [szándékokra](luis-concept-intent.md) .

Tegyük fel például, hogy egy utazási alkalmazás példája egy repülés lefoglalására és az időjárási adatok ellenőrzésére a felhasználó célhelyén. Megadhatja a `BookFlight` műveletek `GetWeather` és a leképezéseket.

A további funkciókat tartalmazó összetettebb alkalmazásokban több szándék áll rendelkezésére, és körültekintően kell meghatároznia azokat, hogy a szándékok ne legyenek túl konkrétak. Előfordulhat például, `BookFlight` hogy külön leképezéseket kell létrehoznia, de `BookInternationalFlight` `BookDomesticFlight` túl hasonló lehet. `BookHotel`

> [!NOTE]
> Az ajánlott eljárás az, hogy csak annyi szándékot használjon, amennyire az alkalmazás funkcióinak elvégzéséhez szükség van. Ha túl sok leképezést határoz meg, akkor a LUIS a hosszúságú kimondott szöveg helyes besorolása érdekében nehezebbé válik. Ha túl kevést ad meg, előfordulhat, hogy az általános átfedésben van.

Ha nincs szüksége az általános felhasználói szándék azonosítására, vegye fel az összes példa felhasználói hosszúságú kimondott szöveg a `None` szándékba. Ha az alkalmazás egyre nagyobb szándékot igényel, később is létrehozhatja őket.

## <a name="create-example-utterances-for-each-intent"></a>Példa hosszúságú kimondott szöveg létrehozása az egyes szándékokhoz

A kezdéshez ne hozzon létre túl sok hosszúságú kimondott szöveg az egyes szándékokhoz. A szándékok meghatározása után hozzon létre 15 – 30 példa hosszúságú kimondott szöveg-t. Minden Kimondás nem lehet azonos a korábban megadott hosszúságú kimondott szöveg. A hosszúságú kimondott szöveg jó választéka magában foglalja a szavak teljes darabszámát, a szavak megválasztását, a művelet időpontját és a [központozást](luis-reference-application-settings.md#punctuation-normalization).

További információ: [a Luis-alkalmazások jó hosszúságú kimondott szöveg megismerése](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Entitások azonosítása

A példában hosszúságú kimondott szöveg azonosítsa a kibontani kívánt entitásokat. Egy járat lefoglalásához olyan információra van szüksége, mint a cél, a dátum, a légitársaság, a jegy kategóriája és az utazási osztály. Hozzon létre entitásokat ezekhez az adattípusokhoz, majd jelölje meg az [entitásokat](luis-concept-entity-types.md) a példában hosszúságú kimondott szöveg. Az entitások fontosak a szándék teljesítéséhez.

Az alkalmazásban használni kívánt entitások meghatározásakor vegye figyelembe, hogy az Objektumtípusok közötti kapcsolatok rögzítéséhez különböző típusú entitások tartoznak. A [Luis-entitások](luis-concept-entity-types.md) részletesebben ismertetik a különböző típusokat.

> [!TIP]
> A LUIS [előre összeépített entitásokat](luis-prebuilt-entities.md) kínál a gyakori, társalgási felhasználói forgatókönyvekhez. Érdemes lehet előre elkészített entitásokat használni az alkalmazásfejlesztés kiindulási pontként.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A LUIS fejlesztői lifecylce megismerése](luis-concept-app-iteration.md)

