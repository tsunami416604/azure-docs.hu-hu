---
title: Understanding leképezések LUIS alkalmazásokban az Azure-ban |} Microsoft Docs
description: Ismerteti, milyen leképezések nyelvi ismertetése intelligens szolgáltatás (LUIS) alkalmazások vannak.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 11aaa6c0891d7087556a82f3a818ef865869b766
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35350083"
---
# <a name="intents-in-luis"></a>A LUIS leképezések

Megjelölésű jelöli egy feladatot, vagy a művelet a felhasználó szeretné elvégezni. Egy célra vagy a felhasználó a kifejezett cél [utterance](luis-concept-utterance.md).

Leképezések olyan készlete, amely megfelel az felhasználók szeretné, hogy az alkalmazás érvénybe műveletek megadása. Például egy utazás app meghatározza, hogy több leképezések:

Alkalmazás-leképezések utazik   |   Példa utterances   | 
------|------|
 BookFlight     |   "Könyv me Rio és a felhőszolgáltató közötti átviteléhez jövő héten" <br/> "Keresnie me Rio a 24th a" <br/> "Kell vezérlősík jegy Rio de Janeiróban következő vasárnap"    |
 Üdvözlés     |   "Hi" <br/>"Hello"szövegrészt. <br/>"Jó reggeli"  |
 CheckWeather | "Mi az a időjárási, például a Boston?" <br/> "Jelenjen meg ez hétvégi előrejelzését" |
 None         | "Beolvasása me egy cookie-k receptet"<br>"Volt a Lakers win?" |

Előre definiált biztonsági mentés eleve minden "[nincs](#none-intent-is-fallback-for-app)" Ez az a tartalék célt. 

## <a name="prebuilt-domains-provide-intents"></a>Előre elkészített tartományok leképezések adja meg.
Mellett megadhat leképezések előre elkészített leképezések egy előre elkészített tartományokat is használhatja. További információkért lásd: [LUIS alkalmazásokban előre elkészített tartományok](luis-how-to-use-prebuilt-domains.md) a további tudnivalók az alkalmazása egy előre elkészített tartományból leképezések testreszabása.

## <a name="return-all-intents-scores"></a>Térjen vissza az összes leképezések pontszámok
Egy utterance rendel egy egyetlen célt. A végpont egy utterance LUIS fogadásakor az egy adott utterance felső leképezésének adja vissza. Ha a utterance az összes leképezések pontszámok használni szeretne, megadhat `verbose=true` jelzőt a lekérdezési karakterlánc API [végpont hívás](https://aka.ms/v1-endpoint-api-docs). 

## <a name="intent-compared-to-entity"></a>Kísérlet történt entitás képest
A célt a chatbot a felhasználó gyorsabban, és a teljes utterance alapuló műveletet jelenti. Az entitás szavakat vagy kifejezéseket a utterance belül található jelöli. Egy utterance pontozási szándéka csak egy legfelső rendelkezhet, de sok entitás veheti fel. 

<a name="how-do-intents-relate-to-entities"></a> Egy leképezés létrehozásához amikor a felhasználó _szándékát_ kiváltották, az ügyfél-alkalmazás például a checkweather() függvény hívása művelet. Ezután hozzon létre a művelet végrehajtásához szükséges paraméterek képviselő entitás. 

|Példa leképezés   | Entitás | Példa utterances entitás   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "hely", "entitás": "seattle"}<br>{"type": "builtin.datetimeV2.date","entity": "holnap", "megoldás": "2018-05-23"} | Mi csomagazonosítóját az időjárási, például a `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "entitás": "a hétvégi"} | Az előrejelzés megjelenítése `this weekend` | 

## <a name="custom-intents"></a>Egyéni leképezések

Hasonlóképpen intentioned [utterances](luis-concept-utterance.md) felel meg egyetlen célt. A célt a utterances segítségével [entitás](luis-concept-entity-types.md) az alkalmazásban, mivel az entitások nem leképezés-specifikus. 

## <a name="prebuilt-domain-intents"></a>Előre elkészített tartomány leképezések

[Előre elkészített tartományok](luis-how-to-use-prebuilt-domains.md) utterances a leképezések rendelkezik.  

## <a name="none-intent-is-fallback-for-app"></a>Leképezési nincs tartalék alkalmazás
A **nincs** célja egy általános vagy tartalék leképezés. A következőkben LUIS utterances, amelyek nem fontos a alkalmazástartományban (tulajdonos terület) használatos. A **nincs** leképezés 10 és 20 százalékát az alkalmazás teljes utterances között kell lennie. Nem hagyja üresen. 

### <a name="none-intent-helps-conversation-direction"></a>Nincs leképezési segít a beszélgetés iránya
Ha egy utterance van előre jelezni, mint a nincs leképezési és az adott előrejelzés chatbot vissza a botot további kérdései vannak vagy adhat át tudja irányítani a felhasználót, hogy érvényes lehetőségek a chatbot a menü. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Nincs utterances sincs leképezési megdönti előrejelzés
Ha nem adja hozzá az összes utterances a **nincs** leképezési, LUIS kényszeríti az utterance, amely a tartomány egyik a tartományi leképezések kívül esik. Ez fogja döntés az előrejelzés pontszámok oktatási LUIS a utterance a megfelelő leképezésének. 

### <a name="add-utterances-to-the-none-intent"></a>A nincs leképezési utterances hozzáadása
A **nincs** leképezés már megtörtént, azonban szándékosan üres. Kitöltése utterances, amely a tartomány kívül esnek. Egy jó utterance a **nincs** valami teljesen kívül az alkalmazást, valamint az alkalmazás iparágban szolgál. Például egy utazás alkalmazás ne használjon a bármely utterances **nincs** haladnak, például a foglalásokat, a számlázást, a étele, tagjánál, rakomány, aktív Szórakozás kapcsolódó is. 

Milyen típusú utterances megmaradnak a sem leképezési? Kezdje valamilyen konkrét, hogy a botot ne fogadja a hívást ilyen "milyen dinosaur tartalmaz kék fog?" Ez egy sokkal kívül egy utazás alkalmazást egy olyan speciális kérdést. 

### <a name="none-is-a-required-intent"></a>Nincs szükség leképezés
A **nincs** leképezés szükséges leképezés, és nem lehet törölték vagy átnevezték.

## <a name="negative-intentions"></a>Negatív céljaira 
Ha szeretné-e határozza meg, mint a pozitív és negatív céljaira, "I **szeretné, hogy** egy autó" és "I **nem** szeretné, hogy egy autó", hozzon létre két leképezések (egy negatív, és egy negatív), és adja hozzá a megfelelő utterances minden egyes. Vagy egyetlen leképezés létrehozásához, és jelölje meg a két különböző pozitív és negatív feltételek egy egységként.  

## <a name="intent-balance"></a>Leképezési egyenleg
Az alkalmazás tartomány leképezések minden leképezés utterances egyensúly kell rendelkeznie. Nem rendelkezik egy leképezést és 10 utterances és egy másik leképezést és 500 utterances. Nem átgondolni. Ha ez a helyzet, tekintse át a leképezést, és 500 utterances megjelenítéséhez, ha a leképezések számos is rendezhető be egy [mintát](luis-concept-patterns.md). 

A **nincs** leképezés nem szerepel az egyenleg. Hogy a leképezés 10 %-a az alkalmazás teljes utterances tartalmaznia kell.

## <a name="intent-limits"></a>Leképezési korlátok
Felülvizsgálati [korlátok](luis-boundaries.md#model-boundaries) tudni, hogy hány leképezések is hozzáadhat egy olyan modell felé. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Ha több, mint a leképezések maximális száma 
Először vegye figyelembe, hogy a rendszer használja-e túl sok leképezések. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Több leképezések kombinálhatók egyetlen célt entitásokkal 
Túl hasonló leképezések teheti a különbségtétel LUIS. Leképezések legyen elég rögzítheti a felhasználó által kért, de nem kell minden elérési utat a kód rögzítése fő feladatok eltérőek. Például BookFlight és FlightCustomerService lehet, hogy külön leképezések utazás alkalmazásban, de BookInternationalFlight és BookDomesticFlight hasonlóak túl. Ha a rendszer megkülönböztetésükhöz, használja az entitások vagy más logikai helyett leképezések. 

### <a name="dispatcher-model"></a>A kézbesítő modell
Tudjon meg többet a LUIS és a kérdések és válaszok készítő alkalmazások kombinálásával a [feladó modell](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Segítség kérése az alkalmazások leképezések jelentős számú
Ha a leképezések számának csökkentését, vagy a leképezések osztani több alkalmazásokba, nem működik, forduljon a támogatási szolgálathoz. Ha az Azure-előfizetés magában foglalja a támogatási szolgálathoz, forduljon a [Azure technikai támogatás](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>További lépések

* További információ [entitások](luis-concept-entity-types.md), amelyeket leképezések vonatkozó fontos szavakat
* Megtudhatja, hogyan [fel és kezelheti a leképezések](luis-how-to-add-intents.md) az LUIS alkalmazásban.
* Tekintse át a cél [ajánlott eljárások](luis-concept-best-practices.md)

[LUIS]:luis-reference-regions.md#luis-website