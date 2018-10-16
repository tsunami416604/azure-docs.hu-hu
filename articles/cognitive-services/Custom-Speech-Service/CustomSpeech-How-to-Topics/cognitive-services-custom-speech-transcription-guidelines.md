---
title: Custom Speech Service az Azure-ban az átírási irányelveket |} A Microsoft Docs
description: Ismerje meg, hogyan készíti elő az adatok a Cognitive Services Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 6c64b89c2eb933e9bcc24bdc3097e7a0b2aab368
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342466"
---
# <a name="transcription-guidelines"></a>Átírási irányelvek

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Ahhoz, hogy az a szöveges adatok akusztikai és nyelvi modell testreszabása a legjobb használatát, a következő beszédátírási irányelveket kell követni. Ezeket az irányelveket a nyelvspecifikus.

## <a name="text-normalization"></a>Szöveg normalizálási

Az akusztikai és nyelvi modell testreszabása az optimális használatra a szöveges adatokat normalizálni kell, ami azt jelenti, a rendszer által olvasható szabványos, egyértelmű űrlap alakítja át. Ez a szakasz ismerteti az adatok importálásakor a Custom Speech Service által végrehajtott szöveg normalizálási és a szöveg normalizálási, amely a felhasználói adatok importálása előtt kell végrehajtania.

## <a name="inverse-text-normalization"></a>Más néven Inverz szöveg normalizálási

Vissza a formázott szöveget, például a kis-és nagybetűk és írásjelek, a "raw" formázatlan szöveges átalakításának folyamata, más néven Inverz szöveg normalizálási (fel) nevezzük. A Microsoft Cognitive Services beszédfelismerő API által visszaadott eredmények fel történik. A Custom Speech Service használatával üzembe helyezett egyéni végpontok az azonos fel használja, mint a Microsoft Cognitive Services beszédfelismerő API. Azonban ez a szolgáltatás jelenleg nem támogatja egyéni fel, így egyéni nyelvi modell által bevezetett új feltételek elfogadását a felismerés eredmények nem lesz formázva.

## <a name="transcription-guidelines-for-en-us"></a>En-US beszédátírási irányelvek

Egyszerű szöveges használatával csak nyomtatható ASCII szöveges adatok feltöltődtek a szolgáltatásra kell írni karakterkészlet. A fájl minden sorának tartalmaznia kell egy egyetlen utterance szövegét.

Fontos Unicode írásjeleket használatának elkerülése érdekében. Ez akkor fordulhat elő véletlenül, ha az adatok a word programot feldolgozása vagy automatizované získávání dat weblapokról származó adatok előkészítése. Cserélje le a megfelelő ASCII helyettesítések ezeket a karaktereket. Példa:

| Unicode elkerülése érdekében | ASCII-helyettesítés |
|----- | ----- |
| "Hello world" (Nyissa meg, és zárja be az idézőjelekkel együtt) | "Hello world" (dupla idézőjel) |
| János napja (jobb szimpla idézőjel) | János napja (aposztrófot) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>A Custom Speech Service által végrehajtott szöveg normalizálási

Ez a szolgáltatás a következő szöveg normalizálási végrehajtják a nyelvi adatkészlet vagy akusztikai adatkészletnél beszédátírás importált adatokat. Ez magában foglalja

*   Alsó – kis-és a teljes szöveg
*   A word-belső aposztrófot kivételével az összes írásjelek eltávolítása
*   Használja a beszélt űrlaphoz, például összegeket számok bővítése

Íme néhány példa

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| Kávézik kávé | kávézik kávé |
| "Szent vonatkozó!" említett Batman. | Szent vonatkozó mondta batman |
| "Mi?" említett Batman sidekick, a terheléselosztást. | milyen említett batman sidekick multiplexelés |
| Nyissa meg a get - em! | Nyissa meg a get-em |
| Double-jointed vagyok | i double szimuláló vagyok |
| 104 fő utca. | egy hoppá négy fő út |
| A 102.7 hangolása | Állítson be egy hoppá két pont hét |
| A pi készül 3,14 | a pi körülbelül három pont egy négy |
| $3,14 költségei | három tizennégy költségei |

### <a name="text-normalization-required-by-users"></a>Szöveg normalizálási felhasználók által

Ahhoz, hogy az adatok használható a leghatékonyabban, az alábbi normalizálási szabályok az adatait, mielőtt importálná azokat a alkalmazni kell.

*   Rövidítések szavak kimondott űrlap megfelelően kell megírni
*   Nem szabványos numerikus karakterláncokat kell írni a szavak
*   Szavak vegyes alfanumerikus karakterek vagy nem alfabetikus karaktereket kell megjelenített érzéseket, mivel ejtsd
*   A betűk között köznapi mozaikszavak is hagyható pontokból vagy szóközökből nélkül egyetlen egységként, de minden más betűszavakat nyelven kell megírni külön betűket, minden egyes betűvel egy szóközzel elválasztva

Íme néhány példa

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 14 új 3. vészhelyreállítás. | tizennégy északkelet harmadik meghajtó |
| Dr. Strangelove | Orvos Strangelove |
| James Bond 007 | James táblarelációk double hoppá hét |
| Ke$ magas rendelkezésre állás | Kesha |
| Mennyi ideig tart a 2 x 4 | Mennyi ideig tart a két négy |
| Az értekezlet kerül az 1-3 pm | Az értekezlet kerül egy és három pm |
| saját vér típus O + | Saját vér típus pozitív O |
| víz H20 | víz H 2 O |
| által Van Halen OU812 lejátszása | O U 8 1-2 szerint Van Halen lejátszása |

## <a name="transcription-guidelines-for-zh-cn"></a>Zh-CN beszédátírási irányelvek

A Custom Speech Service feltöltött szöveges adatok használjon **UTF-8 kódolást (beleértve a AJ)**. A fájl minden sorának tartalmaznia kell egy egyetlen utterance szövegét.

Fontos fél szélességű írásjeleket használatának elkerülése érdekében. Ez akkor fordulhat elő véletlenül, ha az adatok a word programot feldolgozása vagy automatizované získávání dat weblapokról származó adatok előkészítése. Cserélje le a megfelelő teljes szélességű helyettesítések ezeket a karaktereket. Példa:

| Unicode elkerülése érdekében | ASCII-helyettesítés |
|----- | ----- |
| "你好" (Megnyitás és Bezárás idézőjelekkel együtt) | "你好" (az idézőjelekkel együtt) |
| 需要什么帮助? (kérdőjel) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>A Custom Speech Service által végrehajtott szöveg normalizálási

A beszédfelismerési szolgáltatás a következő szöveg normalizálási végrehajtják a nyelvi adatkészlet vagy akusztikai adatkészletnél beszédátírás importált adatokat. Ez magában foglalja

*   Az összes írásjelek eltávolítása
*   Használja a beszélt űrlap számokat bővítése
*   Kétbájtos karaktereket átalakítása félig karaktereket.
*   Felső – kis-és az összes angol szavakat

Néhány példa:

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-required-by-users"></a>Szöveg normalizálási felhasználók által

Ahhoz, hogy az adatok használható a leghatékonyabban, az alábbi normalizálási szabályokat kell alkalmazni az adatok _előzetes_ való importálás.

*   Rövidítések szavak kimondott űrlap megfelelően kell megírni
*   Ez a szolgáltatás nem fedi le minden numerikus mennyiséget. További megbízható numerikus karakterláncok kiírni a kimondott képernyőn

Íme néhány példa

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>De-DE beszédátírási irányelvek

A Custom Speech Service feltöltött szöveges adatok csak használja a **UTF-8 kódolást (beleértve a AJ)**. A fájl minden sorának tartalmaznia kell egy egyetlen utterance szövegét.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>A Custom Speech Service által végrehajtott szöveg normalizálási

Ez a szolgáltatás a következő szöveg normalizálási végrehajtják a nyelvi adatkészlet vagy akusztikai adatkészletnél beszédátírás importált adatokat. Ez magában foglalja

*   Alsó – kis-és a teljes szöveg
*   Minden írásjelek, beleértve az angol vagy német idézőjelek ("teszt", "teszt", "teszt" vagy "teszt" olyan ok) eltávolítása
*   Vesse el az összes sort tartalmazó, bármely speciális karaktert, köztük: ^ ˘ l ¤ y ¦ § © ª ¬® ° ± ² µ x y Ø¬¬
*   Számok szóformaként, többek között a dollár vagy euró összegek bővítése
*   Csak umlautokat fogadunk a, b, az u. mások lesz lecserélve az "adatok" vagy elvetett

Íme néhány példa

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| Frankfurter kör | Frankfurter kör |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| WIR, haben | WIR haben |
| Das macht 10 USD | das macht zehn dollár |


### <a name="text-normalization-required-by-users"></a>Szöveg normalizálási felhasználók által

Ahhoz, hogy az adatok használható a leghatékonyabban, az alábbi normalizálási szabályok az adatait, mielőtt importálná azokat a alkalmazni kell.

*   Tizedesjel kell lennie, és nem. például 2,3 és nem 2.3-as %
*   Idő elválasztó óra és perc között kell lennie:, és nem., pl.: 12:00 Uhr
*   Rövidítéseket, például a "ca.',"bzw." nem cserélhető le. Annak érdekében, hogy rendelkezik a megfelelő kiejtés a teljes képernyőn használata ajánlott.
*   Az öt fő matematikai operátorokat el lesznek távolítva: +, -, \*, /.
 Cserélje le a szövegkonstans formájukban plusz és kártevő, geteilt mínusz javasoljuk.
*   Ugyanez érvényes a szaporodó (=, <>,) – gleich, kleiner als, grösser als
*   3/4-es például töredék használata a szóformaként "drei viertel" ¾ helyett
*   Cserélje le a szóformaként "Euró" a "szimbólumot


Íme néhány példa

| Eredeti szöveg | A felhasználó normalizálási után | Rendszer normalizálási után
|--------  | ----- | -------- |
| Es Izraeli normál idő szerint 12.23Uhr | Es Izraeli normál idő szerint 12:23Uhr | es Izraeli normál idő szerint zwölf uhr drei és zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 és 3-4 | 2 és 3-4 mínusz | zwei plusz -mínusz vier drei|
| Das macht "12" | Das macht 12 számlázni | das macht zwölf számlázni |



## <a name="next-steps"></a>További lépések
* [Egy egyéni hang-szöveg transzformációs végpont használata](cognitive-services-custom-speech-create-endpoint.md)
* A pontosság növeléséhez a [importálni akusztikai modell](cognitive-services-custom-speech-create-acoustic-model.md)
* A pontosság növeléséhez egy [egyéni nyelvi modell](cognitive-services-custom-speech-create-language-model.md)
