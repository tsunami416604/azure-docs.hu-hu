---
title: Írjanak elő irányelvek egyéni beszéd szolgáltatásban az Azure-on |} Microsoft Docs
description: Ismerje meg, hogyan készíti elő az adatok kognitív szolgáltatások egyéni beszéd-szolgáltatása.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 2785a35ac7583ac3d9503cb721d10078d86aa365
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347171"
---
# <a name="transcription-guidelines"></a>Írjanak elő irányelvek
Ahhoz, hogy a lehető legjobb felhasználását a szöveges adatokat akusztikus és nyelvi modell testreszabáshoz, a következő írjanak elő irányelveket kell követni. Ezeket az irányelveket nyelvspecifikus.

## <a name="text-normalization"></a>Szöveg normalizálási

A akusztikus vagy nyelvi modell testreszabási optimális használható a szöveges adatokat normalizálni kell, ami azt jelenti, a rendszer egy szabványos egyértelműen űrlap olvasható alakítja át. Ez a szakasz ismerteti a szöveg normalizálási végzi el az egyéni beszéd szolgáltatás adatok importálásakor és a szöveg normalizálási, amely a felhasználói adatok importálása előtt kell végrehajtania.

## <a name="inverse-text-normalization"></a>Más néven Inverz szöveg normalizálási

A "nyers" formázatlan szöveges átalakítása vissza formázott szöveget, azaz a kis-és nagybetűk és írásjelek, folyamathoz Inverz szöveg normalizálási (ITN). A Microsoft kognitív szolgáltatások Diktálásfelismerési API által visszaadott eredmények ITN történik. Egy egyéni az egyéni beszéd szolgáltatással telepített végpont az azonos ITN használja, mint a Microsoft kognitív szolgáltatások Diktálásfelismerési API. Azonban ez a szolgáltatás jelenleg nem támogatja egyéni ITN, egy egyéni nyelvi modell bevezetett új feltételek elfogadását a felismerési eredmények nem lesz formázva.

## <a name="transcription-guidelines-for-en-us"></a>En-US írjanak elő irányelveiről

A szolgáltatásba való feltöltés szöveges adatok csak az ASCII nyomtatható használatával egyszerű szövegként kell megírni karakterkészlet. A fájl minden egyes sorának tartalmaznia kell egy egyetlen utterance szövege.

Fontos, hogy kerülje el a Unicode írásjeleket. Ez akkor fordulhat elő, akaratlanul Ha az adatok a word feldolgozásakor a program vagy adatai lekaparást előkészítése. Cserélje le ezeket a karaktereket megfelelő ASCII helyettesítések szempontjából. Példa:

| Unicode elkerülése érdekében | ASCII-helyettesítés |
|----- | ----- |
| "Hello world" (megnyitására és bezárására a dupla idézőjelek között) | "Hello world" (idézőjelek) |
| János napja (jobb oldali szimpla idézőjel) | János napja (aposztróf) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Az egyéni beszéd szolgáltatás által végrehajtott szöveg normalizálási

Ez a szolgáltatás akkor hajtja végre a következő szöveg normalizálási a nyelvi adatkészlet vagy egy akusztikus adatkészletnél transcriptions importált adatok. Ez magában foglalja

*   Alsó-kis-és az összes szöveg
*   A word-belső aposztrófot kivételével minden írásjelet eltávolítása
*   A szóbeli űrlaphoz, többek között a dollár összegek számok bővítése

Néhány példa

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| Starbucks kávé | starbucks kávé |
| "Szent vonatkozó!" említett Batman. | Szent vonatkozó említett batman |
| "Mi?" említett Batman sidekick, multiplexelés. | milyen említett batman sidekick multiplexelés |
| Nyissa meg a get - em! | Nyissa meg get em |
| Double-jointed vagyok | i kettős vagyok szimuláló |
| 104-es fő utca. | egy bizony négy fő utca. |
| Állítson be 102.7 | Állítson be egy bizony két pont hét |
| A pi készül 3.14 | a pi körülbelül három pont egy négy |
| $3.14 költséggel | három tizennégy költséggel |

### <a name="text-normalization-required-by-users"></a>Szöveg normalizálási felhasználók által

Ahhoz, hogy az adatok a lehető legjobb felhasználását, a következő normalizálási szabályokat alkalmazni kívánja az adatok importálása előtt.

*   Kell írható rövidítések szóbeli űrlap tükrözik a szavakat a
*   Nem szabványos numerikus karakterláncok kell megírni szavakat
*   Vegyes alfanumerikus karaktereket és nem alfabetikus karaktereket szavak kell kért, mert hangsúlyozottan
*   Közös mozaikszavak maradhatnak, pontokból vagy szóközökből nélkül egyetlen egységként a betűk között, de más mozaikszavak kell megírni külön betűk, minden egyes betűvel, egy szóközzel elválasztva

Néhány példa

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 14 Üzenetnek 3. Dr. | 14 northeast harmadik meghajtó |
| Dr. Strangelove | Orvosi Strangelove |
| 007 James kötés | James táblarelációk kettős bizony hét |
| Ke$ magas rendelkezésre állású | Kesha |
| Milyen hosszú legyen a 2 darab 4 | Milyen hosszú legyen a két négy |
| Az értekezlet kerül az 1-3 pm | Az értekezlet kerül egy három délután |
| a vér típus O + | A vér típus pozitív O: |
| Vízjel H20 | Vízjel H 2 O |
| Van Halen által OU812 lejátszása | O-U 8 1-2 szerint Van Halen lejátszása |

## <a name="transcription-guidelines-for-zh-cn"></a>Zh-CN írjanak elő irányelveiről

Az egyéni beszéd szolgáltatás feltöltött szöveg adatok használandó **UTF-8 kódolást (beleértve a AJ)**. A fájl minden egyes sorának tartalmaznia kell egy egyetlen utterance szövege.

Fontos, hogy kerülje el a teljes szélességű írásjeleket. Ez akkor fordulhat elő, akaratlanul Ha az adatok a word feldolgozásakor a program vagy adatai lekaparást előkészítése. Cserélje le ezeket a karaktereket megfelelő teljes szélességű helyettesítések szempontjából. Példa:

| Unicode elkerülése érdekében | ASCII-helyettesítés |
|----- | ----- |
| "你好" (Megnyitás és Bezárás dupla idézőjelek között) | "你好" (dupla idézőjelek között) |
| 需要什么帮助? (kérdőjel) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Az egyéni beszéd szolgáltatás által végrehajtott szöveg normalizálási

A beszédfelismerés szolgáltatás akkor hajtja végre a következő szöveg normalizálási a nyelvi adatkészlet vagy egy akusztikus adatkészletnél transcriptions importált adatok. Ez magában foglalja

*   Minden írásjelet eltávolítása
*   A számok szóbeli űrlaphoz bővítése
*   Kétbájtos karaktereket átalakítása félig karaktereket.
*   Felső – kis-és az összes angol szavak

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

Ahhoz, hogy az adatok a lehető legjobb felhasználását, a következő normalizálási szabályokat alkalmazni kell az adatok _előzetes_ való importálásához.

*   Kell írható rövidítések szóbeli űrlap tükrözik a szavakat a
*   Ez a szolgáltatás nem fedi le az összes numerikus mennyiség. További megbízható numerikus karakterláncok kiírni szóbeli formában

Néhány példa

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>De-DE írjanak elő irányelveiről

Az egyéni beszéd szolgáltatás feltöltött szöveg adatok csak használja a **UTF-8 kódolást (beleértve a AJ)**. A fájl minden egyes sorának tartalmaznia kell egy egyetlen utterance szövege.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Az egyéni beszéd szolgáltatás által végrehajtott szöveg normalizálási

Ez a szolgáltatás akkor hajtja végre a következő szöveg normalizálási a nyelvi adatkészlet vagy egy akusztikus adatkészletnél transcriptions importált adatok. Ez magában foglalja

*   Alsó-kis-és az összes szöveg
*   Például angol vagy német idézőjelek ("teszt", a "test", a "teszt" vagy a "teszt" ok) minden írásjelet eltávolítása
*   Vesse el az összes sort tartalmazó semmilyen különleges karakter többek között: ^ ˘ l ¤ y ¦ § © ª ¬® ° ± ² µ × y Ø¬¬
*   Word űrlaphoz, beleértve a dollár vagy euró összegek számok bővítése
*   Jelenleg csak umlautokat fogadja el az olyan, o u; mások lesz cserélve "th" vagy elvetett

Néhány példa

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| Frankfurter Ring | Frankfurter ring |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| WIR, haben | WIR haben |
| Das macht $10 | das macht zehn dollár |


### <a name="text-normalization-required-by-users"></a>Szöveg normalizálási felhasználók által

Ahhoz, hogy az adatok a lehető legjobb felhasználását, a következő normalizálási szabályokat alkalmazni kívánja az adatok importálása előtt.

*   Tizedesjel kell lennie, és nem. például 2,3-nem 2.3 %
*   Idő elválasztó óra és perc között kell lennie: és nem., pl., 12:00 Uhr
*   Rövidítések "ca.", "bzw." nem cserélhető. A teljes formátumot használja ahhoz, hogy rendelkezik a megfelelő kiejtés javasoljuk.
*   A rendszer eltávolítja az öt fő matematikai operátorok: +, -, \*, /.
 Azt javasoljuk, hogy cserélje le a szövegkonstans formájukban plusz és mínusz kárte, geteilt.
*   Ugyanez vonatkozik a szaporodó (=, <>,) – a gleich, kleiner als, grösser als
*   Azon részét, 3 vagy 4 például használja a "drei viertel" helyett ¾ word-formátumban
*   A "szimbólum cserélje le a word"Euro"képernyő


Néhány példa

| Eredeti szöveg | Felhasználó normalizálási után | Rendszer normalizálási után
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei és zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 – 4 | 2 és 3 mínusz 4 | zwei és mínusz vier drei|
| Das macht 12 " | Das macht 12 Euros | das macht zwölf euros |



## <a name="next-steps"></a>További lépések
* [Egy egyéni beszéd-szöveg endpoint használatáról](cognitive-services-custom-speech-create-endpoint.md)
* A pontosság növeléséhez a [egyéni akusztikus modell](cognitive-services-custom-speech-create-acoustic-model.md)
* A pontosság növeléséhez egy [egyéni nyelvi modell](cognitive-services-custom-speech-create-language-model.md)
