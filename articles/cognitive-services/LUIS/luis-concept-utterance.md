---
title: Utterances LUIS alkalmazásokban az Azure-ban |} Microsoft Docs
description: Adja hozzá a utterances nyelvi ismertetése intelligens szolgáltatás (LUIS) alkalmazásokban.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/13/2018
ms.author: v-geberr
ms.openlocfilehash: 7c2cd84df8f1eccbd30a7c8054ec8d06336cf2dd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264646"
---
# <a name="utterances-in-luis"></a>A LUIS utterances

**Utterances** vannak a bemeneti adatok a felhasználót, hogy a kell értelmezni. Kibontani leképezések és entitások őket LUIS betanításához fontos számos különböző bemeneteit minden leképezés rögzítése. Számítógép által ismert eszközintelligencia LUIS biztosító aktív tanulási, vagy a folyamat az új utterances betanítása folyamatos elengedhetetlen.

Kifejezések, amelyek úgy gondolja, hogy a felhasználóknak meg kell gyűjteni. Vegye fel, amelyek ugyanazt jelenti, de össze utterances másképp word hosszára, valamint a word elhelyezési. 

## <a name="how-to-choose-varied-utterances"></a>Változatos utterances kiválasztása
Amikor először megkezdésében által [példa utterances hozzáadása] [ add-example-utterances] a LUIS modell itt van néhány alapelvek szem előtt tartani.

### <a name="utterances-arent-always-well-formed"></a>Utterances nem mindig alakú
Lehet, hogy néhány mondatot, például az "Könyv me Párizsi jegy", illetve egy kódrészletet néhány mondatot, például a "Foglalási" vagy "Párizsi repülési."  Felhasználók gyakran vétett helyesen adta-e. Az alkalmazás megtervezésekor vegye figyelembe a függetlenül attól, hogy helyesírását felhasználói bevitel LUIS való továbbítás előtt. A [Bing helyesírás ellenőrizze API] [ BingSpellCheck] LUIS integrálható. Társíthatja az LUIS alkalmazást egy külső kulcs a Bing helyesírás ellenőrizze az API-hoz való közzétételekor. Ha nem írja be a jelölőnégyzetet felhasználói utterances, a gépelési és helyesírási hibák tartalmazó utterances kell még betanítani a LUIS.

### <a name="use-the-representative-language-of-the-user"></a>A felhasználó nyelvi használata
Utterances kiválasztásakor vegye figyelembe, hogy mit gondol gyakori kifejezés vagy kifejezés, nem feltétlenül a tipikus felhasználó számára az ügyfélalkalmazást. Előfordulhat, hogy nem rendelkeznek tartományi felhasználói élmény. Ezért legyen óvatos, szót vagy kifejezést, hogy a felhasználó csak volna fel ha voltak szakértőnek használatakor.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Válassza ki a változatos terminológia, valamint a mondatot a
Akkor is, ha azon törekvéseit, hogy hozzon létre változatos mondat minták tételéhez fog továbbra is ugyanezt az egyes szóhasználatának találja.

Hajtsa végre az alábbi példa utterances:
```
how do I get a computer?
Where do I get a computer?
I want to get a computer, how do I go about it?
When can I have a computer? 
```
Itt core kifejezés "számítógép", nem változott. Asztali számítógép, a hordozható számítógép, a munkaállomás vagy a gép még akkor is csak akkor parancsot. LUIS intelligens módon kikövetkezteti a környezetből szinonimák, de képzési utterances létrehozásakor még jobban eltérő őket.

## <a name="example-utterances-in-each-intent"></a>Példa utterances az egyes leképezés
Minden egyes leképezés példa utterances, legalább 10 – 15 rendelkeznie kell. Ha még nem rendelkező bármely példa utterances megjelölésű, csak akkor képes LUIS betanításához. Ha az egyik vagy nagyon kevés példa utterances megjelölésű, LUIS fog nem előre pontosan a célt. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>A szerzői műveletekhez törzsének 10 – 15 utterances kis csoportok hozzáadása
A modell minden egyes ismétlés esetén ne adjon hozzá egy nagy mennyiségű utterances. Adja hozzá a utterances több tíz mennyiségét. [Vonat](luis-how-to-train.md), [közzététele](publishapp.md), és [tesztelése](interactive-test.md) újra.  

LUIS épít, gondosan kijelölt utterances hatékony modellek. Túl sok utterances hozzáadása oka nem értékes zavart okozna.  

Érdemes néhány utterances, majd kezdje [tekintse át a végpont utterances](label-suggested-utterances.md) megfelelő leképezési előrejelzés és egyéb entitások kiolvasásához.

## <a name="training-utterances"></a>Képzési utterances
Képzési nem determinisztikus: utterance előrejelzését némileg eltérőek lehetnek verziója vagy az alkalmazások között.

## <a name="testing-utterances"></a>Tesztelési utterances 

A fejlesztők valós forgalommal LUIS alkalmazását tesztelés utterances küld a végpont kell kezdődnie. A leképezések és rendelkező entitások teljesítményének javítását szolgálják ezek utterances [tekintse át a utterances](label-suggested-utterances.md). A LUIS webhely ablaktábla tesztelése az elküldött tesztek keresztül a végpont nem kap, és ezért nem járulnak hozzá aktív tanulási. 

## <a name="review-utterances"></a>Tekintse át a utterances
Miután a modell betanított, a közzétett és a fogadó [végpont](luis-glossary.md#endpoint) lekérdezések, [tekintse át a utterances](label-suggested-utterances.md) LUIS által javasolt. LUIS választja ki, amelyek a leképezés vagy az entitást az alacsony pontszámait végpont utterances. 

## <a name="best-practices"></a>Ajánlott eljárások
Felülvizsgálati [ajánlott eljárások](luis-concept-best-practices.md) további.

## <a name="next-steps"></a>További lépések
Lásd: [adja hozzá például utterances] [ add-example-utterances] egy LUIS alkalmazás felhasználói utterances megértéséhez betanítása olvashat.

[add-example-utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-add-example-utterances
[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text