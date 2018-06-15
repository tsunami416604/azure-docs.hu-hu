---
title: Gyakorlati tanácsok LUIS - Azure ismertetése |} Microsoft Docs
description: Ismerje meg a LUIS gyakorlati tanácsok a legjobb eredményt.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 729f510de59fe27761389fb1f6edb4025021565a
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35350063"
---
# <a name="best-practices"></a>Ajánlott eljárások
Az alkalmazás szerzői folyamat segítségével hozza létre a LUIS alkalmazását. 

* A nyelvi modell létrehozása
* Néhány képzési példa utterances (10 – 15 / leképezés) hozzáadása
* Közzététel 
* Tesztelje a végpont 
* Szolgáltatások hozzáadása

Ha az alkalmazás [közzétett](publishapp.md), használja a szolgáltatás hozzáadása a szerzői műveletekhez ciklust, közzétételét és tesztelje a végpont. További példa utterances felvételével kezdődik a következő szerzői ciklus. Ismerje meg a modell valós felhasználói utterances LUIS, amely nem engedélyezi. 

Ahhoz, hogy a feladata elvégzéséhez a tanulás következő hatékony LUIS nem bontsa ki a utterances aktuális készletében lévő Példa és a végpont utterances vannak visszaadó biztosnak, magas előrejelzés pontszámait. Aktív tanulás használatával pontszámukat [minták](luis-concept-patterns.md), és [listák kifejezés](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Hajtsa végre, és nem
Az alábbi lista LUIS alkalmazások gyakorlati tanácsokat tartalmaz:

|Ajánlott|Nem ajánlott|
|--|--|
|[Adja meg a különböző leképezések](#do-define-distinct-intents) |[Sok példa utterances leképezések hozzáadása](#dont-add-many-example-utterances-to-intents) |
|[Keres étkezési helyet túl általános és túl adott közötti minden leképezés a](#do-find-sweet-spot-for-intents)|[LUIS használatát képzés platformként](#dont-use-luis-as-a-training-platform)|
|[Az alkalmazás elkészítésére ismételt](#do-build-the-app-iteratively)|[Sok példa utterances, ugyanazt a formátumot, figyelmen kívül más formátumú hozzáadása](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Kifejezés listák és a minták beépülő modul újabb ismétlés](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Vegyes leképezések és entitások](#dont-mix-the-definition-of-intents-and-entities)|
|[Példa utterances nincs leképezési hozzáadása](#do-add-example-utterances-to-none-intent)|[Az összes lehetséges értékét kifejezés listák létrehozása](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Az aktív tanulási funkcióját a javasolt](#do-leverage-the-suggest-feature-for-active-learning)|[Adja hozzá az olyan sok minták](#dont-add-many-patterns)|
|[Az alkalmazás teljesítményének figyelése](#do-monitor-the-performance-of-your-app)|[A vonat, és minden hozzá egyetlen példa utterance közzététele](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Adja meg a különböző leképezések
Ellenőrizze, hogy az egyes leképezés szóhasználatának csak az adott célt és egy másik biztonsági mentés nem átfedő. Például ha egy alkalmazás, amelyet kezelni utazás szabályok például légitársaság járatok és a szállodák szeretne használni, is választja ezen külön leképezések vagy az adott adatok belül a utterance entitások azonos leképezés.

Ha két leképezések közötti szóhasználatának megegyezik, kombinálhatja a célt, és entitások. 

Vegye figyelembe a következő példa utterances:

```
Book a flight
Book a hotel
```

"A felhőszolgáltató közötti átviteléhez könyv" és "Lefoglalhatja a szállodák" használja az ugyanazon szóhasználatának "könyv egy". Ez átfedésben van azonosnak kell lennie, leképezés repülési és Szálloda különböző szóból álló kibontott entitásokat. 

## <a name="do-find-sweet-spot-for-intents"></a>A leképezések étkezési helyszíni található
Előrejelzési adatokat LUIS segítségével határozhatja meg, ha a leképezések átfedésben. Leképezések átfedő confuses LUIS. Az eredménye, pontozási leképezés felső túl van egy másik célt. LUIS nem használja a pontos ugyanazt az elérési utat keresztül az adatok minden alkalommal, amikor betanítása, mert átfedő megjelölésű rendelkezik, hogy az első és második képzés egy alkalommal. Azt szeretné, hogy minden egyes szándékáról távolabb egymástól kell, ez nem fordulhat elő, a utterance pontszámot. A leképezések jó különbséget eredményez a várt felső célt minden alkalommal. 
 
## <a name="do-build-the-app-iteratively"></a>Az alkalmazás ismételt létrehozása
Tartsa egy külön *vak* tesztelése, amely nem [példa utterances](luis-concept-utterance.md) vagy a végpont utterances. Tartsa meg a tesztelési készlethez az alkalmazás javítására. A vizsgálat, hogy az megfeleljen a felhasználó utterances alkalmazkodáshoz. Mindegyik iteráció kiértékelése a vak vizsgálati használja. 

A fejlesztők adatok három különböző kell rendelkeznie. Az egyik a példa utterances a modell készítéséhez. A második pedig a végpont a modell teszteléséhez. A harmadik pedig a blind Tesztadatok használt [kötegelt tesztelés](luis-how-to-batch-test.md). Az utolsó beállítása nem szerepel az alkalmazás betanítása és nem küldött a végponton.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>A későbbi Ismétlési kifejezés listák és a minták hozzáadása
[Listák kifejezés](luis-concept-feature.md) segítségével meghatározhatja az alkalmazástartomány kapcsolódó szavak szótárak. A kifejezés néhány szót tartalmazó lista, akkor a javasolt szolgáltatást használ, így a szóhasználatának további szavak ismer LUIS kezdőérték. Nem adható hozzá minden szó a szóhasználatának, mert a kifejezéslista nem pontos egyezést. 

A végpont, nagyon hasonlít egymáshoz, a felhasználó utterances felfedheti word választott és elhelyezési. A [mintát](luis-concept-patterns.md) szolgáltatás időt vesz igénybe, a word kiválasztása és elhelyezési együtt reguláris kifejezések, az előrejelzési pontosság növeléséhez. A mintában reguláris kifejezés lehetővé teszi, hogy szavak és közben továbbra is a mintának megfelelő figyelmen kívül kívánja absztrakt. 

Minta választható szintaxist használják írásjelek, központozási figyelmen kívül hagyható.

Mielőtt az alkalmazás rendelkezik végpont kérelem érkezett, mert az vetett bizalmat megdönti, ne alkalmazza ezeket a gyakorlatokat.  

## <a name="do-add-example-utterances-to-none-intent"></a>Nincs leképezési példa utterances hozzáadása
Ez az, hogy a tartalék leképezés, feltüntetett minden, az alkalmazáson kívül. Egy példa utterance hozzáadása a nincs a minden 10 példa utterances LUIS alkalmazása a többi leképezési.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>A javasolt a szolgáltatás aktív biztonságával használja ki.
Használja [aktív tanulási](label-suggested-utterances.md)tartozó **tekintse át a végpont utterances** rendszeresen, további példa utterances hozzáadása leképezések helyett. Az alkalmazás folyamatosan kap végpont utterances, mert a lista növekvő és módosítása.

## <a name="do-monitor-the-performance-of-your-app"></a>Az alkalmazás teljesítményének figyeléséhez.
Az előrejelzés pontosságát TesztKészlet használatával figyelheti. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Sok példa utterances leképezések nem tölti fel
Miután az alkalmazás közzé van téve, csak adja hozzá utterances aktív tanulási a iteratív folyamat. Ha utterances túl hasonló, adjon hozzá egy mintát. 

## <a name="dont-use-luis-as-a-training-platform"></a>Ne használjon LUIS képzési platformként
LUIS csak egy nyelvi modell tartományhoz. Nem célja, hogy általános képzés platformként működik. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Sok példa utterances ugyanazt a formátumot, figyelmen kívül más formátumú, nem ad hozzá.
LUIS változata egy leképezés utterances vár. A utterances eltérőek lehetnek, ugyanakkor az általános jelentéssel bír. Változatok utterance hossza, a word választott és a word elhelyezési lehetnek. 

|Ne használja ugyanazt a formátumot|Csak akkor használja a változó formátumban|
|--|--|
|A jegy Budapest megvásárlása<br>A jegy Párizsi megvásárlása<br>A jegy székesfehérvári megvásárlása|Vásároljon Budapest 1 jegy<br>Foglaljon le a két helyek Párizsba piros kövesse a következő hétfő<br>3 jegyek lefoglalja a székesfehérvári rugó break szeretnék|

A második oszlop által használt más műveletek (vásárlás, tartalék, könyv), a másik mennyiségek (1, két, 3), és a szavakat, de minden más szabályok légitársaság jegyek utazás beszerzési azonos szándékkal kell. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Angol szövegben ne keverje leképezések és entitások
Hozzon létre egy leképezésének semmilyen művelet a botot érvénybe. Entitások használják, amelyek lehetővé teszik, hogy a művelet paraméterek. 

Amely foglalható le lesz légitársaság járatok chatbot, hozzon létre egy **BookFlight** leképezés. Ne hozzon létre egy minden cél- és minden légitársaság leképezésének. Használja a adatoknak egyes [entitások](luis-concept-entity-types.md) és megjelölése a példa utterances. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Ne hozzon létre kifejezés listák a lehetséges értékek
Adja meg a néhány példa a [listák kifejezés](luis-concept-feature.md) , de nem minden szó. LUIS használatúvá és figyelembe veszi a környezetben. 

## <a name="dont-add-many-patterns"></a>Ne adja hozzá a sok minták
Ne adjon túl sok [minták](luis-concept-patterns.md). LUIS arra szolgál, hogy ismerje meg gyorsan kevesebb példákkal. A rendszer feleslegesen metódusok nem értelmezhetők.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nem betanítása és minden egyetlen példa utterance közzététele
Vegye fel a 10-es vagy 15 utterances képzési és a közzététel előtt. Amely lehetővé teszi, hogy a hatás megjelenik az előrejelzés pontosságát. Egyetlen utterance hozzáadása nem látható hatással lehet az eredményt. 

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [tervezze meg az alkalmazás](plan-your-app.md) az LUIS alkalmazásban.
[LUIS]: luis-referencia-regions.md #luis-webhely
