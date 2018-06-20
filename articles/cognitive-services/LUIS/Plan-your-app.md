---
title: A LUIS alkalmazások megtervezése |} Microsoft Docs
description: Megfelelő alkalmazást leképezések és entitásokat, és ezután hozzon létre az alkalmazás tervek a nyelvi ismertetése intelligens szolgáltatások (LUIS).
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 7aec5d5b90ac7145ce9f337ec74c590b4b88c6b1
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266362"
---
# <a name="plan-your-luis-app"></a>Tervezze meg az LUIS alkalmazás

Fontos létrehozni a LUIS megkezdése előtt tervezze meg az alkalmazás. Egy Vázlat vagy a lehetséges leképezések és entitások, amely kapcsolódik az alkalmazás témaköréhez tartományspecifikus a séma előkészítése.  

## <a name="identify-your-domain"></a>A tartomány azonosítására
Tartományspecifikus témakör középpontjában egy LUIS alkalmazást.  Például előfordulhat, hogy egy utazás alkalmazást, amely elvégzi a jegyektől, járatok, szállodák és bérleti autók foglalási. Egy másik alkalmazás által biztosított gyakorló, alkalmazhatóságra erőfeszítéseket követési és beállítás célok kapcsolódó tartalmat. 

> [!TIP]
> LUIS kínál [előre elkészített tartományok](luis-how-to-use-prebuilt-domains.md) sok szabhatják.
> Ellenőrizze, hogy ha használhatja egy előre elkészített tartomány kiindulási pontként az alkalmazásra vonatkozóan.

## <a name="identify-your-intents"></a>A leképezések azonosítása
Gondoljon kapcsolatos a [leképezések](luis-concept-intent.md) az alkalmazás a feladat fontos. Vegyünk egy utazás alkalmazást, a felhőszolgáltató közötti átviteléhez foglalható le, és ellenőrizze a felhasználó célhelyen időjárási funkciók példát. Ezek a műveletek a "BookFlight" és "GetWeather" leképezések adhat meg. Egy összetettebb alkalmazásban a további funkciók további leképezések rendelkezik, és azokat figyelmesen úgy, hogy nincs túl adott meg kell határozni. Például "BookFlight" és "BookHotel" szükség lehet a külön leképezések, de "BookInternationalFlight" és "BookDomesticFlight" túl hasonló lehet.

> [!NOTE]
> Ajánlott eljárás az, csak annyi leképezések, végre kell hajtania az alkalmazás funkcióit. Túl sok leképezések határozza meg, ha válik nehezebben LUIS utterances megfelelően besorolása. Ha túl meg néhány átfedésben, ezért általános lehet.


## <a name="identify-your-entities"></a>Az entitások azonosítása
A felhőszolgáltató közötti átviteléhez le, szükség lesz néhány adatra, például a cél, dátum, légitársaság, jegy kategória, és osztály változatlan marad. Hozzáadhat, ezek [entitások](luis-concept-entity-types.md) mert fontos való elvégzéséhez szükséges parancsokról megjelölésű. 

Amikor az alkalmazás használatához entitások határozza meg, vegye figyelembe, hogy nincsenek-e típusú objektumok közötti kapcsolatok rögzítésének entitások különböző típusú. [Bejegyzései szerepelnek LUIS](luis-concept-entity-types.md) biztosít részletes információkat a különböző olvashat.

### <a name="simple-entity"></a>Egyszerű entitás
Egy egyszerű entitás egyetlen elvét ismerteti.

![egyszerű entitás](./media/luis-plan-your-app/simple-entity.png)

Lásd: [adatok kinyerése](luis-concept-data-extraction.md#simple-entity-data) tudhat meg többet az egyszerű entitás kibontása a végpont JSON választ. Az egyszerű entitás próbálja [gyors üzembe helyezés](luis-quickstart-primary-and-secondary-data.md) további egy egyszerű entitás használatáról.

### <a name="hierarchical-entity"></a>A hierarchikus
A hierarchikus speciális egy **egyszerű** entitás; adjon meg egy kategóriát, és annak tagjait a szülő-gyermek kapcsolat formájában.

![a hierarchikus](./media/luis-plan-your-app/hierarchical-entity.png)

Lásd: [adatok kinyerése](luis-concept-data-extraction.md#hierarchical-entity-data) tudhat meg többet a hierarchikus entitás kibontása a végpont JSON választ. A hierarchikus entitás próbálja [gyors üzembe helyezés](luis-quickstart-intent-and-hier-entity.md) további hierarchikus entitás használatáról.

### <a name="composite-entity"></a>Összetett entitás
Egy összetett entitást egész részét alkotó más entitásoktól tevődik össze. 

![összetett entitás](./media/luis-plan-your-app/composite-entity.png)

Lásd: [adatok kinyerése](luis-concept-data-extraction.md#composite-entity-data) tudhat meg többet az összetett entitást kibontása a végpont JSON választ. Az összetett entitást próbálja [oktatóanyag](luis-tutorial-composite-entity.md) egy összetett entitást használatáról további.

### <a name="prebuilt-entity"></a>Előre elkészített entitás
LUIS biztosít [előre elkészített entitások](Pre-builtEntities.md) az általános típusok, például `Number`, amellyel a jegyektől jegy sorrendben számát.

![Szám előre elkészített entitás](./media/luis-plan-your-app/number-entity.png)

Lásd: [adatok kinyerése](luis-concept-data-extraction.md#prebuilt-entity-data) tudhat meg többet a végpont JSON lekérdezésválaszt reguláris kifejezés entitások kibontása. 

### <a name="list-entity"></a>Lista entitás 
A lista entitás egy explicit módon megadott értékek listáját. Minden egyes érték egy vagy több szinonimák áll. Utazás alkalmazásban előfordulhat, hogy létrehozása mellett dönt a lista entitás repülőtéri képviseli.

![lista entitás](./media/luis-plan-your-app/list-entity.png)

Lásd: [adatok kinyerése](luis-concept-data-extraction.md#list-entity-data) tudhat meg többet a végpont JSON lekérdezésválaszt lista entitások kibontása. Próbálja meg a [gyors üzembe helyezés](luis-quickstart-intent-and-list-entity.md) további információt a lista entitás használata.

### <a name="regular-expression-entity"></a>Reguláris kifejezés entitás
A reguláris kifejezésnek entitás lehetővé teszi, hogy ha adatokat szeretne kinyerni az regex kifejezés alapján utterance LUIS.

![Reguláris kifejezés entitás](./media/luis-plan-your-app/regex-entity.png)

Lásd: [adatok kinyerése](luis-concept-data-extraction.md#regular-expression-entity-data) tudhat meg többet a végpont JSON lekérdezésválaszt reguláris kifejezés entitások kibontása. Próbálja meg a [gyors üzembe helyezés](luis-quickstart-intents-regex-entity.md) további információt a reguláris kifejezésnek entitás használata.

## <a name="after-getting-endpoint-utterances"></a>Első végpont utterances után
Az előrejelzés fejlesztései végrehajtásához megtervezése után az alkalmazás lekérdezi a végpont utterances, [aktív tanulási](label-suggested-utterances.md), [listák kifejezés](luis-concept-feature.md), és [minták](luis-concept-patterns.md). 

### <a name="patternany-entity"></a>Pattern.any entitás
Patterns.any használt csak a változó hosszúságú helyőrző egy [mintázatnak](luis-concept-patterns.md) sablon utterance megjelölni, ahol az entitás kezdődik, és ezzel véget ér. Sablon utterances megfelelnek [megfelelő szintaxis](luis-concept-patterns.md#pattern-syntax) entitásokat, és figyelmen kívül hagyható szöveg azonosításához.


## <a name="next-steps"></a>További lépések
* Lásd: [hozzon létre az első nyelvi ismertetése intelligens szolgáltatások (LUIS) alkalmazás] [ luis-get-started-create-app] egy gyors forgatókönyv bemutatja, hogyan hozzon létre egy LUIS alkalmazást.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app