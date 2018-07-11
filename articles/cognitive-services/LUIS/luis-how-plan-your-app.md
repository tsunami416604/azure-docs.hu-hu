---
title: A LUIS-alkalmazások megtervezése |} A Microsoft Docs
description: Megfelelő alkalmazást szándékok és entitások, és ezután terveket hozhat létre az alkalmazás a Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 2ce202bbb1479db18fb88cfef4d510ae4cb39a78
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952100"
---
# <a name="plan-your-luis-app"></a>A LUIS-alkalmazás megtervezése

Fontos az alkalmazás megtervezése, létrehozása, a LUIS megkezdése előtt. Készítsen elő egy Vázlat vagy a lehetséges szándékok és entitások, amely a tartomány-specifikus a témakör az alkalmazás a séma.  

## <a name="identify-your-domain"></a>A tartomány azonosítása
LUIS-alkalmazásokon eltérése a tartomány-specifikus témakör.  Például előfordulhat, hogy rendelkezik egy utazási alkalmazás, amely végrehajtja a jegyeket, és szállásfoglalások egész sorát, szállodákban, bérleti autók foglalási. Egy másik alkalmazás rendelkezhetnek gyakorló, mentességre erőfeszítések nyomon követése és célok beállítás kapcsolódó tartalmat. 

> [!TIP]
> A LUIS kínál [előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md) számos gyakori szituációhoz kínál.
> Ellenőrizze, hogy ha használhatja egy előre elkészített tartomány kiindulási pontként az alkalmazáshoz.

## <a name="identify-your-intents"></a>A leképezések azonosítása
Gondoljunk a [leképezések](luis-concept-intent.md) az alkalmazás a feladat fontos. Vessünk egy utazási alkalmazás a functions repülőjegyet könyvet, és ellenőrizze az időjárás, a felhasználó célhelyen példát. Megadhatja a "BookFlight" és "GetWeather" szándék fog vonatkozni, ezek a műveletek számára. Egy összetettebb alkalmazásban a további funkciók további leképezések rendelkezik, és azokat figyelmesen úgy, hogy nincs túl adott meg kell határozni. Például "BookFlight" és "BookHotel" szükség lehet a külön szándék fog vonatkozni, de "BookInternationalFlight" és "BookDomesticFlight" túl hasonló lehet.

> [!NOTE]
> Ajánlott eljárás csak annyi szándék szerint kell végeznie az alkalmazás funkcióit. Ha túl sok leképezések definiálja, válik utterances megfelelően besorolni, LUIS nehezebb. Ha túl megad néhány, így általános helyrendszerszerepkörökre egymással átfedésben lévő lehet.


## <a name="identify-your-entities"></a>Az entitások azonosítása
Könyvet a repülőút, szükség lesz néhány adatra, mint például a cél, dátum, légitársaság, jegy kategória, és továbbítani osztály. Ezeket az adatokat adhat hozzá [entitások](luis-concept-entity-types.md) mert azok fontos megjelölésű teljesítéséért. 

Ha azt állapítja meg, mely entitások használata az alkalmazásban, vegye figyelembe, hogy nincsenek-e eltérő típusú entitásokat típusú objektumok közötti kapcsolatok rögzítéséhez. [A LUIS entitások](luis-concept-entity-types.md) a különböző típusaival kapcsolatos további részleteket biztosít.

### <a name="simple-entity"></a>Egyszerű entitás
Egy egyszerű entitás egyetlen fogalom írja le.

![egyszerű entitás](./media/luis-plan-your-app/simple-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#simple-entity-data) tudhat meg többet az egyszerű entitás kinyerését a végpont JSON lekérdezési válasz. Próbálja ki az egyszerű entitás [rövid](luis-quickstart-primary-and-secondary-data.md) tudhat meg többet az egyszerű entitás használatával.

### <a name="hierarchical-entity"></a>Hierarchikus entitás
Egy hierarchikus entitás egy olyan speciális típusú egy **egyszerű** entitás; egy kategóriát és annak tagjait meghatározása a szülő-gyermek típusú kapcsolat formájában.

![a hierarchikus](./media/luis-plan-your-app/hierarchical-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#hierarchical-entity-data) tudhat meg többet a végpont JSON lekérdezési válasz a hierarchikus entitás kinyerését. Próbálja ki a hierarchikus entitás [rövid](luis-quickstart-intent-and-hier-entity.md) hierarchikus entitás használatával kapcsolatos további.

### <a name="composite-entity"></a>összetett entitást
Egy összetett entitást az egész részét alkotó más entitások épül fel. 

![összetett entitást](./media/luis-plan-your-app/composite-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#composite-entity-data) tudhat meg többet az összetett entitás kinyerését a végpont JSON lekérdezési válasz. Próbálja ki az összetett entitás [oktatóanyag](luis-tutorial-composite-entity.md) egy összetett entitást használatával kapcsolatos további.

### <a name="prebuilt-entity"></a>Előre összeállított entitások
LUIS biztosít [előre összeállított entitások](luis-prebuilt-entities.md) gyakori típusok hasonló `Number`, amelyet használhat egy a jegy sorrendben jegyek száma.

![Előre összeállított entitások száma](./media/luis-plan-your-app/number-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#prebuilt-entity-data) tudhat meg többet a végpontról JSON lekérdezési válasz reguláris kifejezés entitások kinyeréséhez. 

### <a name="list-entity"></a>Listaentitás 
Egy lista entitás egy explicit módon megadott értékek listája. Minden egyes érték egy vagy több szinonimák áll. Egy utazási alkalmazás választhatja hozhat létre, amelyek repülőtér nevek listája entitásokat.

![lista entitás](./media/luis-plan-your-app/list-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#list-entity-data) tudhat meg többet a végpontról JSON lekérdezési válasz lista entitások kinyeréséhez. Próbálja ki a [rövid](luis-quickstart-intent-and-list-entity.md) egy lista entitás használatával kapcsolatos további.

### <a name="regular-expression-entity"></a>Reguláris kifejezés entitás
A reguláris kifejezésnek entitás lehetővé teszi, hogy a LUIS az adatok kinyerése az utterance (kifejezés) egy regex kifejezés alapján.

![Reguláris kifejezés entitás](./media/luis-plan-your-app/regex-entity.png)

Lásd: [Adatkinyerés](luis-concept-data-extraction.md#regular-expression-entity-data) tudhat meg többet a végpontról JSON lekérdezési válasz reguláris kifejezés entitások kinyeréséhez. Próbálja ki a [rövid](luis-quickstart-intents-regex-entity.md) egy reguláris kifejezésnek entitás használatával kapcsolatos további.

## <a name="after-getting-endpoint-utterances"></a>Ha végpont kimondott szöveg
Után az alkalmazás lekéri a végpont utterances, kívánnak megvalósítani az előrejelzési fejlesztései [aktív tanulás](luis-how-to-review-endoint-utt.md), [listák kifejezés](luis-concept-feature.md), és [minták](luis-concept-patterns.md). 

### <a name="patternany-entity"></a>Pattern.any entitás
Patterns.any csak a használt változó hosszúságú helyőrzőt egy [mintázatnak](luis-concept-patterns.md) sablon utterance (kifejezés) való megjelöléséhez, ahol az entitás kezdődik és ér véget. Felelnek meg a sablon utterances [helyes szintaxist](luis-concept-patterns.md#pattern-syntax) entitásokat, és figyelmen kívül hagyható, szöveges azonosításához.


## <a name="next-steps"></a>További lépések
* Lásd: [az első Language Understanding Intelligent Services (LUIS) alkalmazás létrehozása](luis-get-started-create-app.md) LUIS-alkalmazások létrehozása rövid leírását.