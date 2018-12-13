---
title: Az alkalmazás megtervezése
titleSuffix: Language Understanding - Azure Cognitive Services
description: Megfelelő alkalmazást szándékok és entitások, és ezután terveket hozhat létre az alkalmazás a Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: diberry
ms.openlocfilehash: e14b9f2930ed9c170f31bd654829efe3b5a99446
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091294"
---
# <a name="plan-your-luis-app"></a>A LUIS-alkalmazás megtervezése

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

### <a name="simple-entity"></a>Egyszerű entitás
Egy egyszerű entitás egyetlen fogalom írja le.

![egyszerű entitás](./media/luis-plan-your-app/simple-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#simple-entity-data) tudhat meg többet az egyszerű entitás kinyerését a végpont JSON lekérdezési válasz. Próbálja ki a [rövid](luis-quickstart-primary-and-secondary-data.md) tudhat meg többet az egyszerű entitás használatával.

### <a name="hierarchical-entity"></a>Hierarchikus entitás
Egy hierarchikus entitás egy olyan speciális típusú egy **egyszerű** entitás; egy kategóriát és annak tagjait meghatározása a szülő-gyermek típusú kapcsolat formájában. A kapcsolat az utterance (kifejezés) belül a környezet határozza meg. Gyermekek egy hierarchikus entitás is egyszerű entitások lesznek.

![a hierarchikus](./media/luis-plan-your-app/hierarchical-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#hierarchical-entity-data) tudhat meg többet a végpont JSON lekérdezési válasz a hierarchikus entitás kinyerését. Próbálja ki a [rövid](luis-quickstart-intent-and-hier-entity.md) hierarchikus entitás használatával kapcsolatos további.

### <a name="composite-entity"></a>Összetett entitást
Egy összetett entitást az egész részét alkotó más entitások épül fel. Egy összetett entitás többféle típusú entitást tartalmaz.

![Összetett entitást](./media/luis-plan-your-app/composite-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#composite-entity-data) tudhat meg többet az összetett entitás kinyerését a végpont JSON lekérdezési válasz. Próbálja ki a [oktatóanyag](luis-tutorial-composite-entity.md) további egy összetett entitást használatáról.

### <a name="prebuilt-entity"></a>Előre összeállított entitások
A LUIS biztosít [előre összeállított entitások](luis-prebuilt-entities.md) közös adattípusok, például a száma, adatok, e-mail címét és URL-címe. A szám előre összeállított entitások egy a jegy sorrendben jegyek száma is használhat.

![Előre összeállított entitások száma](./media/luis-plan-your-app/number-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#prebuilt-entity-data) tudhat meg többet az előre összeállított entitások kinyeréséhez a végpontról JSON lekérdezési válasz. 

### <a name="list-entity"></a>Listaentitás 
Egy lista entitás egy explicit módon megadott értékek listája. Minden egyes érték egy vagy több szinonimák áll. Egy utazási alkalmazás választhatja hozhat létre, amelyek repülőtér nevek listája entitásokat.

![lista entitás](./media/luis-plan-your-app/list-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#list-entity-data) tudhat meg többet a végpontról JSON lekérdezési válasz lista entitások kinyeréséhez. Próbálja ki a [rövid](luis-quickstart-intent-and-list-entity.md) egy lista entitás használatával kapcsolatos további.

### <a name="regular-expression-entity"></a>Reguláriskifejezés-entitás
A reguláris kifejezésnek entitás lehetővé teszi, hogy a helyes formátumú adatokat nyerhet ki az utterance (kifejezés) egy reguláris kifejezés alapján a LUIS.

![Reguláriskifejezés-entitás](./media/luis-plan-your-app/regex-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#regular-expression-entity-data) tudhat meg többet a végpontról JSON lekérdezési válasz reguláris kifejezés entitások kinyeréséhez. Próbálja ki a [rövid](luis-quickstart-intents-regex-entity.md) egy reguláris kifejezésnek entitás használatával kapcsolatos további.

## <a name="next-steps"></a>További lépések
Után az alkalmazás be van tanítva, közzétett, és lekérdezi a végpont utterances, kívánnak megvalósítani az előrejelzési fejlesztései [aktív tanulás](luis-how-to-review-endoint-utt.md), [listák kifejezés](luis-concept-feature.md), és [minták](luis-concept-patterns.md). 


* Lásd: [az első Language Understanding Intelligent Services (LUIS) alkalmazás létrehozása](luis-get-started-create-app.md) LUIS-alkalmazások létrehozása rövid leírását.
