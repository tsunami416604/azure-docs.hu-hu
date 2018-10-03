---
title: Rész-az-szövegrészeket – nyelvi elemzési API
description: Ismerje meg, hogyan rész-, beszéd címkézést a nyelvi elemzési API azonosítja a kategóriát vagy a rész a beszéd szöveges minden szó.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 09/27/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: a01fcea4ae6c8950d578bacefc2f064586d7306b
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238520"
---
# <a name="part-of-speech-tagging"></a>Rész-az-szövegrészeket

> [!IMPORTANT]
> A nyelvi elemzés előzetes verziója 2018. augusztus 9 volt leszerelése. Azt javasoljuk, [Azure Machine Learning szövegelemzési moduljait](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) szöveg feldolgozásra és elemzésre.

## <a name="background-and-motivation"></a>Háttér-információkért és motiváció

A szöveg mondatokra és lexikális lett elválasztva, ha a következő lépés az elemzés az azonosítsa a kategória vagy rész-az-speech minden szó.
Ezek közé tartozik például a kategóriák *főnév* (általában jelölő személyek, helyek, dolog, ötleteket, stb.) és *művelet* (általában jelölő műveletek, megváltozik, állam stb. Az egyes szavak a rész-az-speech egyértelmű legyen (például *quagmire* csak egy főnevet van), azonban számos más, a állapítható meg, hogy nehéz.
*Tábla* sikerült a hely hol található (vagy a számokat 2-D elrendezését), de is "tábla egy hozzászólás" is.

## <a name="list-of-part-of-speech-tags"></a>Rész-, beszéd címkék listája

| Címke | Leírás | Például szavak |
|-----|-------------|---------------|
| $ | dollár | $ |
| \`\` | nyitó idézőjelhez | \` \`\` |
| '' | záró idézőjel | ' '' |
| ( | nyitó zárójel | ( [ { |
| ) | záró zárójel | ) ] } |
| ;  | vesszővel tagolt | ;  |
| -- | kötőjel | -- |
| . | mondat szegmenslezáró | . ! ? |
| : | pontosvesszővel vagy a három pontra | : ; ... |
| Másolat | együtt, a koordinációt | és, de vagy még|
| CD-RŐL | számok, kardinális | kilenc 20-at 1980 96 " |
| DT | determiner |egy a-minden mindkét sem|
| AZ EX | meglétet vizsgáló: Itt | Hiba |
| KERETRENDSZER | külső word | enfant terrible hoi polloi je ne sais quoi |
| INDIA | preposition vagy megemlítése együttes használata| Ha, hogy a benne lévő |
| JJ | melléknév vagy számok sorszámnál | kilencedik viszonylag execrable multimodális |
| JJR | összehasonlító melléknév | gyorsabb, hatékonyabb olcsóbb |
| JJS | melléknév superlative | ajánlott leggyorsabb legolcsóbb |
| LS | lista-elem jelölőjének | (a) (b) 1 2 A B A. B. |
| MD | modális – kiegészítő | is előfordulhat, hogy kell fog sikerült előfordulhat, hogy meg kell kerülnie |
| NEURÁLIS HÁLÓZAT | főnév, közös, egyes számban vagy háttértár | Potato pénzt cipő |
| NNP | főnév, megfelelő, egyes számban | Kennedy Roosevelt Chicago Weehauken |
| NNPS | főnév, megfelelő, plural | Springfields cserjék |
| NNS | főnév, közös, plural | kódrészletek mice mezők |
| PDT | üzem előtti determiner | az összes mindkét fél számos még ilyen meg arról, hogy ez |
| POS | genitive jelölő | "a |
| JELSZÓ-REPLIKÁCIÓS HÁZIREND | Személyes névmás | ő Phil azt tudom azt azokat, |
| JELSZÓ-REPLIKÁCIÓS HÁZIREND$ | névmás birtokos | hers ő a saját meg azok a |
| RB | módosítószót | klinikai csak |
| RBR | összehasonlító módosítószót | több további gloomier grander graver nagyobb grimmer nehezebb szankcióktól jobb nehezebb magasabb azonban nagyobb újabb Karcsúbb is meghatározhatnak kisebb, kevésbé tökéletesen lonelier hosszabb hangosabb alacsonyabb... |
| RBS | módosítószót superlative | ajánlott legnagyobb bluntest legkorábbi legtávolabbi első legtávolabbi nagyon nehéz heartiest legmagasabb legnagyobb legalább második legfontosabb legrosszabb a legközelebbi legtöbb kevesebb |
| FÜGGŐ ENTITÁS | particle | a kikapcsolva kapcsolatos ki beállítása |
| SYM | Szimbólum | a(z) % & |
| ERRE: | "a" preposition vagy infinitive jelölő | erre: |
| HŰHA | interjection | Hűha Hurrá howdy hello |
| VB | igeként alap űrlapot | Adja meg működés közben hozzárendelése |
| VBD | igeként múltbeli igeidőt | hozzárendelt rendelte flew |
| VBG | művelet, jelen participle vagy gerund | jogosultságot ad a repülő hozzárendelése |
| VBN | igeként múltbeli participle | adott hozzárendelt repült |
| VBP | művelet, a jelen idejű kifejezéseket tartalmaz, nem a 3. egyetlen személy | Adja meg működés közben hozzárendelése |
| VBZ | művelet, a jelen idejű kifejezéseket tartalmaz, a 3. egyetlen személy | lehetővé teszi a rendel közlekednek |
| WDT | Mit jelent-determiner | hogy milyen amely |
| WP | Mit jelent-névmás | akik akikkel |
| WP$ | Mit jelent-névmás, birtokos | amelynek |
| WRB | Mit jelent-módosítószót | Hogyan azonban, amikor ahol |

## <a name="specification"></a>Specifikáció

A jogkivonatok, mint hogy támaszkodik a specifikációnak a a [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
