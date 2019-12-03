---
title: Mi a Personalizer?
titleSuffix: Azure Cognitive Services
description: A személyre szabás egy felhőalapú API-szolgáltatás, amely lehetővé teszi a felhasználók számára a legjobb felhasználói élmény kiválasztását, a valós idejű viselkedésük megismerését.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: b5d38ffeda3600fd90c4ee84acdd29ed599886ae
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707954"
---
# <a name="what-is-personalizer"></a>Mi a Personalizer?

Az Azure megszemélyesítő egy felhőalapú API-szolgáltatás, amely lehetővé teszi az alkalmazás számára, hogy kiválassza a legjobb élményt a felhasználók számára, a kollektív valós idejű viselkedéstől való tanulást.

* Adja meg a felhasználókkal és a tartalommal kapcsolatos információkat, és fogadja a legfontosabb műveletet a felhasználók megjelenítéséhez. 
* A személyre szabás használata előtt nem kell megtisztítani és címkéznie az adatfeliratot.
* Visszajelzés küldése a személyre szabáshoz, ha az kényelmes Önnek. 
* Valós idejű elemzések megtekintése. 

Tekintse meg a [személyre szabási funkció működésének](https://personalizercontentdemo.azurewebsites.net/) bemutatóját

## <a name="how-does-personalizer-work"></a>Hogyan működik a személyre szabás?

A személyre szabott gépi tanulási modellekkel megismerheti, hogy milyen műveleteket lehet a legmagasabb szinten rangsorolni. Az ügyfélalkalmazás a lehetséges műveletek listáját, valamint a velük kapcsolatos információkat tartalmazza. és a kontextussal kapcsolatos információk, amelyek a felhasználóval, az eszközzel és egyéb információkkal is rendelkezhetnek. A személyre szabott művelet határozza meg a végrehajtandó műveletet. Miután az ügyfélalkalmazás a kiválasztott műveletet használja, visszajelzéseket küld a személynek a jutalom pontszám formájában. A visszajelzés beérkezése után a személyre szabás automatikusan frissíti a saját modelljét, amelyet a jövőbeli soraihoz használtak. Az idő múlásával a személyre szabott modell egy modellt fog képezni, amely arra utalhat, hogy az egyes kontextusokban a funkciók alapján melyik a legmegfelelőbb művelet.

## <a name="how-do-i-use-the-personalizer"></a>Hogyan használja a személyre szabott szolgáltatást?

![A felhasználó személyre szabásával kiválaszthatja, hogy melyik videó jelenjen meg a felhasználónak](media/what-is-personalizer/personalizer-example-highlevel.png)

1. A személyre szabáshoz válasszon egy felhasználói élményt az alkalmazásban.
1. Hozza létre és konfigurálja a megszemélyesítési szolgáltatás egy példányát a Azure Portal. Minden példány egy személyre szabott hurok.
1. A [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) -val a felhasználókra vonatkozó információkkal (_funkciókkal_) és a tartalommal (_műveletekkel_) hívhat személyre. A személyre szabás előtt nem kell megadnia tiszta, címkézett adattípust. Az API-k hívhatók közvetlenül vagy különböző programozási nyelvekhez elérhető SDK-k használatával.
1. Az ügyfélalkalmazás megjeleníti a felhasználó által a személyre szabott művelet által kiválasztott műveletet.
1. A [jutalmazási API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) -val visszajelzést adhat a személyre szabott személynek, amely jelzi, hogy a felhasználó kiválasztotta-e a testreszabási műveletet. Ez egy _[jutalom pontszám](concept-rewards.md)_ .
1. Megtekintheti az elemzéseket a Azure Portalban annak kiértékeléséhez, hogy a rendszer hogyan működik, és hogyan segíti az adatai személyre szabását.

## <a name="where-can-i-use-personalizer"></a>Hol használhatom a személyre szabott szolgáltatást?

Az ügyfélalkalmazás például a következőhöz adhat hozzá személyre szabott elemet:

* Személyre szabhatja, hogy mely cikkek legyenek kiemelve a hírek webhelyén.    
* Optimalizálja az ad-elhelyezést egy webhelyen.
* Személyre szabott "ajánlott elem" megjelenítése egy bevásárlási webhelyen.
* Javasoljon olyan felhasználói felületi elemeket, mint például a szűrők, amelyek egy adott fényképre vonatkoznak.
* Válassza ki a csevegési bot válaszát a felhasználó szándékának tisztázására, vagy tegyen javaslatot egy műveletre.
* A felhasználók által az üzleti folyamat következő lépéseként megjelenő javaslatok rangsorolása.

A személyre szabott szolgáltatás nem a felhasználói profil adatainak megőrzésére és kezelésére, illetve az egyes felhasználók preferenciáinak vagy előzményeinek naplózására szolgál. A személyre szabott információk az egyes interakciók minden funkcióját megismerik egyetlen modell kontextusában, amely a hasonló funkciók esetében maximális jutalmakat érhet el. 

## <a name="personalization-for-developers"></a>Személyre szabás a fejlesztők számára

A személyre szabott szolgáltatás két API-val rendelkezik:

* *Rank*: a Rank API segítségével meghatározhatja, hogy melyik _műveletet_ kell megjeleníteni, az aktuális _környezetben_. A műveletek a JSON-objektumok tömbje lesznek elküldve, az egyes azonosítókkal és információkkal (_funkciókkal_) együtt. a környezet egy másik JSON-objektumként lesz elküldve. Az API azt a Műveletazonosító adja vissza, amelyet az alkalmazásnak a felhasználónak kell megjelenítenie.
* *Jutalom*: miután a felhasználó kommunikált az alkalmazással, mérje fel, hogy a személyre szabás milyen mértékben működött 0 és 1 közötti számként, és [jutalom pontszámként](concept-rewards.md)küldje el. 

![Személyre szabott események alapszintű eseménysorozat](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Következő lépések

* [A személyre szabás újdonságai](whats-new.md)
* [Hogyan működik a megszemélyesítő?](how-personalizer-works.md)
* [Mi a megerősítő tanulás?](concepts-reinforcement-learning.md)
* [További információ a Rank kérelem szolgáltatásairól és műveleteiről](concepts-features.md)
* [Tudnivalók a jutalmazási kérelem pontszámának meghatározásáról](concept-rewards.md)
* [Az interaktív bemutató használata](https://personalizationdemo.azurewebsites.net/)
