---
title: Megerősítése Learning - Personalizer
titleSuffix: Azure Cognitive Services
description: A Personalizer a műveletekre és az aktuális környezetre vonatkozó információkat használja fel a jobb rangsorolási javaslatok érdekében. A műveletekre és környezetre vonatkozó információk olyan attribútumok vagy tulajdonságok, amelyeket szolgáltatásoknak neveznek.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68662838"
---
# <a name="what-is-reinforcement-learning"></a>Mi a megerősítés tanulás?

A Megerősítése Learning a gépi tanulás olyan megközelítése, amely a használatból származó visszajelzések megszerzésével tanulja meg a viselkedést.
 
Megerősítése Learning működik:

* Lehetőség vagy szabadság biztosítása egy viselkedés életbe léptetésére – például döntések vagy döntések meghozatalára.
* Kontextuális információk biztosítása a környezetről és a választási lehetőségekről.
* Visszajelzést ad arról, hogy mennyire jól a viselkedés elér egy bizonyos célt.

Bár sok altípus és stílus megerősítése tanulás, ez hogyan működik a koncepció Personalizer:

* Az alkalmazás lehetőséget nyújt arra, hogy egy tartalom egy részét az alternatívák listájából jelenítse meg.
* Az alkalmazás tájékoztatást nyújt az egyes alternatív és a környezet a felhasználó.
* Az alkalmazás kiszámítja a _jutalom pontszámot._

Ellentétben néhány megközelítés megerősítése tanulás, Personalizer nem igényel szimulációt dolgozni. A tanulási algoritmusok célja, hogy reagáljon egy külső világ (szemben ellenőrzik azt), és tanulni minden adatpont azzal a feltétellel, hogy ez egy egyedülálló lehetőséget, hogy a költségek időt és pénzt létrehozni, és hogy van egy nem nulla sajnálom (elvesztése lehetséges jutalom), ha optimálistól elmaradó teljesítmény történik.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Milyen típusú megerősítés tanulási algoritmusokat használ a Personalizer?

A Personalizer jelenlegi verziója **környezetfüggő banditákat**használ, egy olyan megközelítést, amely a tanulás megerősítésére épül, és amely a diszkrét műveletek közötti döntések vagy döntések közötti döntéseket vagy döntéseket hoz egy adott környezetben.

A _döntési memória_, a modell, amely be van tanítva, hogy rögzítse a lehető legjobb döntést, adott környezetben, lineáris modellek készletét használja. Ezek többször is üzleti eredményeket mutattak, és bevált megközelítést jelentenek, részben azért, mert nagyon gyorsan tanulhatnak a valós világból anélkül, hogy többmenetes képzésre lenne szükségük, részben pedig azért, mert kiegészíthetik a felügyelt tanulási modelleket és a mély neurális hálózati modellek.

A feltárási/kizsákmányoló forgalom elosztása véletlenszerűen történik a feltáráshoz beállított százalékos érték után, és a feltárás alapértelmezett algoritmusa epsilon-greedy.

### <a name="history-of-contextual-bandits"></a>Története kontextuális banditák

John Langford megalkotta a kontextuális banditák (Langford és Zhang [2007]) nevet, hogy leírja a megerősítés tanulásának egy tractable részhalmazát, és fél tucat dolgozaton dolgozott, amelyek javítják a tanulás megértését ebben a paradigmában:

* Beygelzimer és mtsai [2011]
* Dudík et al. [2011a,b]
* Agarwal és mtsai [2014, 2012]
* Beygelzimer és Langford [2009]
* Li és mtsai [2010]

John korábban számos oktatóanyagot is adott olyan témákról, mint a Közös előrejelzés (ICML 2015), a Kontextuális banditaelmélet (NIPS 2013), az Active Learning (ICML 2009) és a Mintakomplexitási határok (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Milyen gépi tanulási keretrendszereket használ a Personalizer?

Personalizer jelenleg használ [Vowpal Wabbit,](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) mint az alapja a gépi tanulás. Ez a keretrendszer lehetővé teszi a maximális átviteli és a legalacsonyabb késést, amikor személyre szabási rangok és a modell betanítása az összes esemény.

## <a name="references"></a>Referencia

* [Kontextuális döntések meghozatala alacsony technikai adóssággal](https://arxiv.org/abs/1606.03966)
* [A méltányos osztályozás csökkentési megközelítése](https://arxiv.org/abs/1803.02453)
* [Hatékony kontextuális banditák a nem helyhez kötött világokban](https://arxiv.org/abs/1708.01799)
* [Maradék veszteség előrejelzése: Megerősítése: tanulás nem növekményes visszajelzés](https://openreview.net/pdf?id=HJNMYceCW)
* [Az utasítások és a vizuális megfigyelések feltérképezése a megerősítési tanulással végzett műveletekhez](https://arxiv.org/abs/1704.08795)
* [Tanulás keresés jobb, mint a tanár](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>További lépések

[Offline értékelés](concepts-offline-evaluation.md) 