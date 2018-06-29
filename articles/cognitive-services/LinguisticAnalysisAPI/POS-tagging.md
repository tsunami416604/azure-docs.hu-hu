---
title: A nyelvi elemzés API címkézés része a beszédfelismerés |} Microsoft Docs
description: Ismerje meg, hogyan része a beszédfelismerés címkézést kognitív Microsoft-szolgáltatásokban a kategóriát vagy az összes szó a szöveg részét melyik azonosítja.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 09/27/2016
ms.author: lesun
ms.openlocfilehash: 90fd5b05c2dabdac88c6c8da288ab629177be38d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082638"
---
# <a name="part-of-speech-tagging"></a>Része a beszéd-címkézés

## <a name="background-and-motivation"></a>Háttér és kifejlesztésének

Amennyiben az a szöveg el van választva, a mondatok és jogkivonatok, elemzés a következő lépés a kategóriát vagy rész-az-beszéd átalakítás minden szó azonosítására.
Ezek közé tartozik például a kategóriák *főnév* (általában képviselő személyek, helyek, dolog, ötleteket, stb.) és *művelet* (általában képviselő műveletek, módosítja, állapot, stb. Néhány szó a rész-az-beszéd átalakítás egyértelműen (például *quagmire* főnév csak valóban), de számos más, a merevlemez állapítható meg, hogy.
*Tábla* sikerült a hely, ahol elhelyezkedik (vagy a 2-D elrendezési szám), de akkor is "tábla döntéseken".

## <a name="list-of-part-of-speech-tags"></a>Része a beszéd címkék listája

| Címke | Leírás | Példa szavakat |
|-----|-------------|---------------|
| $ | Amerikai dollár | $ |
| \`\` | Nyitó idézőjel | \` \`\` |
| '' | záró idézőjel | ' '' |
| ( | nyitó zárójel | ( [ { |
| ) | záró zárójel | ) ] } |
| , | vesszővel | , |
| -- | kötőjel | -- |
| . | mondat lezáró | . ! ? |
| : | kettőspont vagy három ponttal | : ; ... |
| Másolat | együtt, koordinációs | és de vagy még|
| CD-RŐL | kardinális szám | kilenc 20 1980 "96 |
| DT | determiner |egy a az összes mindkét sem|
| AZ EX | meglétet vizsgáló ott | Hiba |
| FW | külső word | enfant terrible hoi polloi je ne sais quoi |
| IN | preposition vagy együtt tud| Ha, hogy a belső |
| JJ | melléknév vagy számok, sorszám | kilencedik közérthető execrable multimodális |
| JJR | összehasonlító melléknév | jobb gyorsabban olcsóbb |
| JJS | melléknév superlative | ajánlott leggyorsabb legolcsóbb | 
| LS | lista-elem jelölőjének | (a) (b) 1 2 A B A. a b kiszolgálóra. |
| MD | modális – kiegészítő | is előfordulhat, hogy kell fog sikerült előfordulhat, hogy meg kell kerülnie |
| NN | főnév, gyakori, egyes vagy háttértár | burgonya pénz cipő |
| NNP | főnév, megfelelő, szinguláris | Kennedy Roosevelt Chicagói Weehauken |
| NNPS | főnév, megfelelő, többes szám | Springfields cserjék |
| NNS | főnév, gyakori, többes szám | darab egerek mezők |
| PDT | előtti determiner | az összes mindkét félig sok elég ilyen meg arról, hogy ez |
| POS | a jelölő genitive | ' meg |
| JELSZÓ-REPLIKÁCIÓS HÁZIREND | Személyes névmás | ő Phil azt szeretnék azt azok meg |
| JELSZÓ-REPLIKÁCIÓS HÁZIREND$ | névmás birtokos | ellenőrzését annak a saját a azok a |
| RB | módosítószót | klinikailag csak |
| RBR | összehasonlító módosítószót | a további gloomier grander graver nagyobb grimmer nehezebben szankcióktól egészségesebb nehezebb magasabb azonban nagyobb újabb Karcsúbb hosszabb kevésbé tökéletesen kisebb lonelier hosszabb hangosabb alsó több... |
| RBS | módosítószót superlative | ajánlott legnagyobb bluntest legkorábbi legtávolabbi első legtávolabbi nagyon nehéz heartiest legmagasabb legnagyobb legkevésbé kevesebb mint a második legfontosabb legrosszabb legközelebbi legtöbb |
| FÜGGŐ ENTITÁS | alkotóelem | a kikapcsolva kapcsolatos akár |
| SYM | szimbólum | % & |
| ERRE: | "a", mint preposition vagy infinitive jelölő | erre: |
| UH | interjection | UH Kellemes ünnepeket howdy hello |
| VISUAL BASIC | művelet, alap űrlapot | Adjon hozzárendelése menet közben |
| VBD | művelet, korábban időben | adott hozzárendelt flew |
| VBG | művelet, a jelenlegi participle vagy a gerund | jogosultságot ad a hozzárendelése repülő |
| VBN | művelet, korábban participle | megadott hozzárendelt besorolásával |
| VBP | művelet, a jelenlegi időben, a nem 3. egyes személy | Adjon hozzárendelése menet közben |
| VBZ | művelet, a jelenlegi időben, a 3. egyes személy | által biztosított rendel közlekednek |
| WDT | Mit jelent-determiner | hogy milyen amely |
| KIJELZŐ | Mit jelent-névmás | aki akinek |
| WP$ | Mit jelent-névmás, birtokos | amelyek |
| WRB | Mit jelent-módosítószót | Hogyan azonban amikor ahol |

## <a name="specification"></a>Meghatározása

Jogkivonat-létrehozási, mint azt a meghatározás támaszkodjon a [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
