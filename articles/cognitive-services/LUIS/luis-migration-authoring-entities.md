---
title: Áttelepítés a V3-as gépmegtanult entitásra
titleSuffix: Azure Cognitive Services
description: A V3-as szerzői egy új entitástípust biztosít, a gép által megtanult entitást, valamint kapcsolatokat adhat hozzá a gép megtanult entitáshoz és az alkalmazás más entitásaihoz vagy szolgáltatásaihoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75563833"
---
# <a name="migrate-to-v3-authoring-entity"></a>Áttelepítés a V3 szerzői entitásra

A V3-as szerzői egy új entitástípust biztosít, a gép által megtanult entitást, valamint kapcsolatokat adhat hozzá a gép megtanult entitáshoz és az alkalmazás más entitásaihoz vagy szolgáltatásaihoz.

## <a name="entities-are-decomposable-in-v3"></a>Az entitások a V3-ban is lebomlanak

A V3-as szerzői API-kkal létrehozott entitások, akár az [API-k](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) használatával, akár az [előnézeti portállal,](https://preview.luis.ai/)lehetővé teszik egy réteges entitásmodell létrehozását szülővel és gyermekekkel. A szülő a **gép által megtanult entitásként** ismert, a gyermekek pedig a gép által megtanult entitás **alösszetevői.**

Minden egyes alösszetevő egy gép által megtanult entitás is, de a megkötések és leírók hozzáadott konfigurációs beállításaival.

* **A kényszerek** pontos szövegegyeztetési szabályok, amelyek garantálják, hogy egy entitás kinyerhető, ha megfelel egy szabálynak. A szabályt egy pontos szövegegyező entitás határozza meg, jelenleg: egy [előre összeállított entitás](luis-reference-prebuilt-entities.md), egy [reguláris kifejezés entitás](reference-entity-regular-expression.md)vagy [listaentitás.](reference-entity-list.md)
* **A leírók** olyan [jellemzők](luis-concept-feature.md), például kifejezéslisták vagy entitások, amelyek az entitás erős jelzésére szolgálnak.

A V3-as szerzői egy új entitástípust biztosít, a gép által megtanult entitást, valamint kapcsolatokat adhat hozzá a gép megtanult entitáshoz és az alkalmazás más entitásaihoz vagy szolgáltatásaihoz.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Hogyan viszonyulnak ezek az új kapcsolatok a V2-es szerzőséghez?

V2 authoring biztosított hierarchikus és összetett entitások mellett szerepkörök és szolgáltatások ugyanezen feladat elvégzéséhez. Mivel az entitások, a szolgáltatások és a szerepkörök nem voltak kifejezetten egymáshoz kapcsolódnak, nehéz volt megérteni, hogy a LUIS hogyan sugallta a kapcsolatokat az előrejelzés során.

A V3-mal a kapcsolat explicit, és az alkalmazás szerzők által tervezett. Ez lehetővé teszi, mint az alkalmazás szerzője, hogy:

* Vizuálisan láthatja, hogy a LUIS hogyan jósolja meg ezeket a kapcsolatokat, a példautterances
* Tesztelje ezeket a kapcsolatokat az [interaktív tesztablakkal](luis-interactive-test.md) vagy a végponton
* Használja ezeket a kapcsolatokat az ügyfélalkalmazásban egy jól strukturált, elnevezett, beágyazott [.json objektumon](reference-entity-machine-learned-entity.md) keresztül

## <a name="planning"></a>Tervezés

Az áttelepítés során vegye figyelembe az áttelepítési tervben az alábbiakat:

* Biztonsági másolatot a LUIS-alkalmazásról, és hajtsa végre az áttelepítést egy külön alkalmazásban. Miután egy V2-es és V3-as alkalmazás érhető el egy időben lehetővé teszi, hogy érvényesítse a szükséges módosításokat, és az előrejelzési eredményekre gyakorolt hatás.
* Az aktuális előrejelzési sikerességi mutatók rögzítése
* Az aktuális irányítópult-információk rögzítése az alkalmazás állapotának pillanatképeként
* Meglévő szándékok, entitások, kifejezéslisták, minták és kötegelt tesztek áttekintése
* A következő elemek **változtatás nélkül**áttelepíthetők:
    * Leképezések
    * Entitások
        * Reguláriskifejezés-entitás
        * Listaentitás
    * Szolgáltatások
        * Kifejezéslista
* A következő elemeket kell áttelepíteni **a módosításokkal:**
    * Entitások
        * Hierarchikus entitás
        * Összetett entitás
    * Szerepkörök – a szerepkörök csak egy gép által megtanult (szülő) entitásra alkalmazhatók. A szerepkörök nem alkalmazhatók alösszetevőkre
    * A hierarchikus és összetett entitásokat használó kötegtesztek és minták

Az áttelepítési terv tervezésekor hagyjon időt a végleges, gép megtanult entitások áttekintésére, miután az összes hierarchikus és összetett entitását áttelepítette. Míg az egyenes áttelepítés működni fog, miután elvégezheti a módosítást, és áttekintette a kötegvizsgálati eredményeket, és a JSON előrejelzése, az egységesebb JSON arra késztetheti, hogy módosításokat hajtson végre, így az ügyféloldali alkalmazásnak átadott végleges információk eltérő módon szerveződnek. Ez hasonló a kódrefactoringhoz, és ugyanazzal az ellenőrzési folyamattal kell kezelni, mint amelyet a szervezet a helyén.

Ha nem rendelkezik kötegelt tesztekkel a V2-modellhez, és az áttelepítés részeként áttelepíti a kötegteszteket a V3-as modellbe, nem fogja tudni ellenőrizni, hogy az áttelepítés milyen hatással lesz a végpont előrejelzési eredményeire.

## <a name="migrating-from-v2-entities"></a>Áttelepítés V2-es entitásokból

A V3-as szerzői modellre való áttéréskor érdemes megfontolnia, hogyan helyezheti át a gép által megtanult entitást és annak alösszetevőit, beleértve a kényszereket és a leírókat.

Az alábbi táblázat megjegyzi, hogy mely entitások nak kell áttérnie egy V2-ről egy V3 entitás tervére.

|V2 szerzői entitás típusa|V3 szerzői entitás típusa|Példa|
|--|--|--|
|Összetett entitás|Gép megtanult entitás|[tudj meg többet](#migrate-v2-composite-entity)|
|Hierarchikus entitás|A gép által megtanult entitás szerepe|[tudj meg többet](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>V2 összetett entitás áttelepítése

A V2-es kompozit minden gyermekének a V3 gép által megtanult entitás egy alösszetevőjével kell ábrázolnia. Ha az összetett gyermek egy előre összeállított, reguláris kifejezés vagy listaentitás, ezt a gyermek reprezentatot képviselő alösszetevő **reprezentaticiója** ként kell alkalmazni.

Az összetett entitás gépáltal megtanult entitásba történő áttelepítésének tervezésekor figyelembe vett szempontok:
* A gyermekentitások nem használhatók mintákban
* A gyermekentitások már nincsenek megosztva
* A gyermekentitásokat meg kell címkézni, ha korábban nem gépi legeltetésűek

### <a name="existing-descriptors"></a>Meglévő leírók

Az összetett entitásban lévő szavak kiemelésére használt kifejezéslistát leíróként kell alkalmazni a gép által megtanult (szülő) entitásra, az alösszetevőre (gyermek) vagy a szándékra (ha a kifejezéslista csak egy leletre vonatkozik). Tervezze meg, hogy hozzáadja a leírót az entitáshoz, amelyet a legnagyobb mértékben növelnie kell. Ne adja hozzá a leírót általánosan a gép által megtanult (szülő) entitáshoz, ha az a legjelentősebbmértékben növeli egy alösszetevő (gyermek) előrejelzését.

### <a name="new-descriptors"></a>Új leírók

A V3-as szerzői, adjon hozzá egy tervezési lépés kiértékelése entitások lehetséges leírók az összes entitásés leképezések.

### <a name="example-entity"></a>Példa entitás

Ez az entitás csak példa. A saját entitás áttelepítése más szempontokat is megkövetelhet.

V2 kompozitot vegyünk a `order` következő pizzák módosításához:
* előre összeállított datetimeV2 a szállítási idő
* kifejezéslistát, hogy bizonyos szavakat, például pizzát, pitét, héjat és feltétet
* lista egység kimutatására öntetek, mint a gomba, olajbogyó, pepperoni.

Az entitás egy példa utterance (kifejezés) a következő:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Az alábbi táblázat az áttelepítést mutatja be:

|V2 modellek|V3 modellek|
|--|--|
|Szülő - nevű összetevő entitás`Order`|Szülő – gép által megtanult entitás neve`Order`|
|Gyermek - Előre összeállított datetimeV2|* Áttelepítése előre elkészített entitás új alkalmazásba.<br>* Add megkötés a szülő előre összeállított datetimeV2.|
|Gyermek - lista entitás feltétek|* Telepítse a lista entitást az új alkalmazásba.<br>* Ezután adjon hozzá egy megkötést a listaentitás szülőjének.|


## <a name="migrate-v2-hierarchical-entity"></a>V2 hierarchikus entitás áttelepítése

A V2-es szerzői, hierarchikus entitás tadott meg a LUIS meglévő szerepkörök előtt. Mindkettő ugyanazt a célt szolgálta az entitások kibontása a környezet használat alapján. Ha hierarchikus entitásokkal rendelkezik, úgy is gondolhat rájuk, mint a szerepkörökkel rendelkező egyszerű entitásokra.

A V3 authoring:
* Egy szerepkör alkalmazható a gép megtanult (szülő) entitás.
* A szerepkör nem alkalmazható egyetlen alösszetevőre sem.

Ez az entitás csak példa. A saját entitás áttelepítése más szempontokat is megkövetelhet.

V2 hierarchikus entitást voksolásnak vegyünk egy pizza `order`módosításához:
* ahol minden gyermek meghatározza az eredeti feltöltést vagy a végső feltöltést

Az entitás egy példa utterance (kifejezés) a következő:

`Change the topping from mushrooms to olives`

Az alábbi táblázat az áttelepítést mutatja be:

|V2 modellek|V3 modellek|
|--|--|
|Szülő - nevű összetevő entitás`Order`|Szülő – gép által megtanult entitás neve`Order`|
|Gyermek - Hierarchikus entitás eredeti és végső pizza feltéttel|* Add `Order` szerepet minden feltöltési.|

## <a name="next-steps"></a>További lépések

* [Fejlesztői erőforrások](developer-reference-resource.md)
