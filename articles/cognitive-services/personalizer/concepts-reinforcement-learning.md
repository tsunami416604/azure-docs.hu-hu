---
title: Megerősítő tanulást - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer kapcsolatos műveletek és jelenlegi környezet információk segítségével jobb rangsorolási javaslatokat. Ezeket a műveleteket és környezeti információ olyan attribútumok vagy a szolgáltatások nevezik.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b65073c0646db0cd0c27a71005bb4f74b091ae09
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506885"
---
# <a name="what-is-reinforcement-learning"></a>Mi a megerősítő tanulást?

Megerősítő tanulást egy megközelítés a Machine learning, amely megtanulja viselkedések visszajelzés beolvas annak használatára.
 
Megerősítő tanulást úgy működik:

* Biztosít lehetőséget vagy a szabadságfok egy működés - döntések vagy választási lehetőségek válnak elérhetővé, mint életbe léptetni.
* A környezet és a választási lehetőségek környezetfüggő információ biztosítása.
* Visszajelzés küldése a információ arról, hogy a viselkedés éri el bizonyos célokat.

Miközben sok altípusa, és a megerősítő tanulást stílusa, ezt a fogalmat Personalizer működését:

* Az alkalmazás a lehetőség megjelenítéséhez az alternatív megoldások listáját a tartalom egy részét biztosítja.
* Az alkalmazás minden egyes alternatív és a felhasználó kontextusában kapcsolatos információkat biztosít.
* Az alkalmazás számítási erőforrások egy _pontszám díjazza_.

Personalizer eltérően néhány olyan a megerősítő tanulási módszert, nem szükséges egy dolgozhat a szimuláció. A tanulási algoritmusok úgy tervezték, hogy egy külvilág reagálás (és szabályozhatja azt), és ismerje meg, hogy-e olyan egyedi lehetőséget, amely költségek időt és pénzt hozhat létre, és legyen egy nem nulla értékű magát (lehetséges ellenszolgáltatás elvesztését) Ha ismeretében egyes adatpontok optimálisnál rosszabb teljesítmény történik.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Milyen típusú megerősítő tanulási algoritmus használ Personalizer?

Personalizer verziójához használja **környezetfüggő bandits**, a megerősítő tanulást, amely megközelítés Keretes körül döntések vagy különálló műveletek, egy adott környezetben között.

A _memória döntési_, a modellt, amely rendelkezik betanítva adott egy környezetben, a lehető legjobb döntés, rögzítheti egy lineáris modellt használja. Ezek ismételten kimutatták, üzleti eredményeit, és egy jól bevált módszerrel részlegesen megismerése a való világból származó nagyon gyorsan anélkül, hogy több fázisban képzési, és részben is kiegészíti a felügyelt tanulási modelleket és hasznosítva hálózati modellek.

Felfedezés/biztonsági rés kiaknázása elleni forgalom elosztására véletlenszerűen követően a feltárási beállítása százalékos készült, és az alapértelmezett algoritmus feltárási epszilon mohó.

### <a name="history-of-contextual-bandits"></a>Környezetfüggő Bandits előzményeit

John Langford alkotta meg a nevet környezetfüggő Bandits (Langford és Zhang [2007]) írja le a megerősítő tanulást tractable részhalmazát, és a egy tucat tanulmányok javítása a sajátíthat el a paradigma ismeretekkel dolgozott:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a, b]
* Agarwal et al. [2014, 2012]
* Beygelzimer és Langford [2009]
* Li et al. [2010]

John is adott több oktatóanyagok korábban, többek között a közös előrejelzési (ICML 2015), környezetfüggő Bandit elmélet (NIPS 2013-hoz), aktív tanulás (ICML 2009) és minta összetettséget esik (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Milyen gépi tanulási keretrendszereket használ Personalizer?

Jelenleg használt personalizer [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) a machine Learning alapjaként. Ezt a keretrendszert lehetővé teszi, hogy a maximális átviteli sebesség és a legkisebb késés, ha személyre szabása így rangsorolja és az összes esemény a modell betanításához.

## <a name="references"></a>Hivatkozások

* [Az alacsony műszaki adósságot környezetfüggő döntések](https://arxiv.org/abs/1606.03966)
* [A csökkentési megközelítés valós besoroláshoz](https://arxiv.org/abs/1803.02453)
* [A nem helyhez kötött világok hatékony környezetfüggő Bandits](https://arxiv.org/abs/1708.01799)
* [Maradék adatveszteség előrejelzési: Megerősítő: tanulási rendelkező nem növekményes visszajelzés](https://openreview.net/pdf?id=HJNMYceCW)
* [Útmutatás és vizuális megfigyelés leképezése műveleteket a megerősítő tanulást](https://arxiv.org/abs/1704.08795)
* [Keresés jobban, mint az oktatói tanulás](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>További lépések

[Kapcsolat kiértékelése](concepts-offline-evaluation.md) 