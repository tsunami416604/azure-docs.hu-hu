---
title: Az alkalmazás megtervezése - LUIS
description: Vázolja a releváns alkalmazásleképezéseket és entitásokat, majd hozza létre az alkalmazásterveket az intelligens szolgáltatások (LANGUAGE Understanding Intelligent Services, LUIS) szolgáltatásban.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382309"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>A LUIS-alkalmazásséma megtervezése a tulajdonostartományés az adatok kinyerése segítségével

A LUIS alkalmazásséma a [tulajdonostartományhoz](luis-glossary.md#domain)tartozó [leképezéseket](luis-glossary.md#intent) és [entitásokat](luis-glossary.md#entity) tartalmaz. A szándékok osztályozzák a felhasználói [utterances,](luis-glossary.md#utterance)és az entitások kinyerése adatokat a felhasználói utterances.

## <a name="identify-your-domain"></a>A tartomány azonosítása

A LUIS-alkalmazás egy tulajdonostartomány köré épül. Előfordulhat például, hogy rendelkezik egy utazási alkalmazással, amely kezeli a jegyek, járatok, szállodák és bérelt autók foglalását. Egy másik alkalmazás az edzéssel, az erőnléti erőfeszítések nyomon követésével és a célok kitűzésével kapcsolatos tartalmakat biztosíthat. A tartomány azonosítása segít megtalálni a tartományszempontjából releváns szavakat vagy kifejezéseket.

> [!TIP]
> A LUIS [előre összeállított tartományokat](luis-how-to-use-prebuilt-domains.md) kínál számos gyakori forgatókönyvhöz. Ellenőrizze, hogy használhat-e előre összeállított tartományt az alkalmazás kiindulópontjaként.

## <a name="identify-your-intents"></a>A szándékok azonosítása

Gondolja át az alkalmazás feladatához fontos [szándékokat.](luis-concept-intent.md)

Vegyük például egy utazási alkalmazás, a funkciók at foglaljon egy járatot, és ellenőrizze az időjárás a felhasználó rendeltetési helyére. Megadhatja a `BookFlight` `GetWeather` műveletek és leképezések.

Egy összetettebb alkalmazás több függvényt, több szándékot, és meg kell határozniőket gondosan, hogy a szándékok nem túl specifikus. Például, `BookFlight` `BookHotel` és lehet, hogy külön `BookInternationalFlight` szándékok, de `BookDomesticFlight` lehet, hogy túl hasonló.

> [!NOTE]
> Ajánlott eljárás, hogy csak annyi leképezést használjon, amennyi az alkalmazás funkcióinak végrehajtásához szükséges. Ha túl sok leképezést határoz meg, a LUIS nehezebbé teszi az utterances megfelelő besorolását. Ha túl keveset határoz meg, akkor lehet, hogy olyan általánosak, hogy átfedik egymást.

Ha nem kell azonosítania az általános felhasználói szándékot, adja hozzá `None` az összes példa felhasználói utterances a szándékhoz. Ha az alkalmazás egyre több leképezésre van szüksége, később létrehozhatja őket.

## <a name="create-example-utterances-for-each-intent"></a>Példakimondottszövegek létrehozása az egyes leképezésekhez

Először is, ne hozzon létre túl sok kimondott szöveget az egyes szándékok. Miután megállapította a szándékok, hozzon létre 15–30 példa utterances egy szándékot. Minden utterance (kifejezés) különböznie kell a korábban megadott utterances. A kimondott szövegek jó változatossága az általános szószám, a szóválasztás, az igei idő és [az írásjelek](luis-reference-application-settings.md#punctuation-normalization)közé tartozik.

További információkért tekintse meg [a LUIS-alkalmazások helyes kimondott szövegének megértését.](luis-concept-utterance.md)

## <a name="identify-your-entities"></a>Az entitások azonosítása

A példa utterances, azonosítsa a kivonni kívánt entitásokat. A járat foglalásához olyan információkra van szüksége, mint a célállomás, a dátum, a légitársaság, a jegykategória és az utazási osztály. Hozzon létre entitásokat ezekhez az adattípusokhoz, majd jelölje meg az [entitásokat](luis-concept-entity-types.md) a példa kimondott szövegekben. Az entitások fontosak a szándék megvalósításához.

Az alkalmazásban használandó entitások meghatározásakor ne feledje, hogy az objektumtípusok közötti kapcsolatok rögzítésére különböző típusú entitások tartoznak. [A LUIS-ban lévő entitások](luis-concept-entity-types.md) részletesebben ismertetik a különböző típusokat.

> [!TIP]
> A LUIS [előre összeállított entitásokat](luis-prebuilt-entities.md) kínál a gyakori, társalgási felhasználói forgatókönyvekhez. Fontolja meg az előre összeállított entitások használatát az alkalmazásfejlesztés kiindulópontjaként.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A LUIS fejlesztési életképességének megismerése](luis-concept-app-iteration.md)

