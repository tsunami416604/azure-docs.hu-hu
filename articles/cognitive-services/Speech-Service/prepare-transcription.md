---
title: Beszédfelismerés szolgáltatás írjanak elő irányelveiről |} Microsoft Docs
description: Útmutató szöveg akusztikus testreszabása és nyelvi modellek és hang betűtípusok előkészítése a beszédfelismerés szolgáltatás.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 93ab7c81a773f692b2b970bb1901d82b7aceb5a2
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "35349959"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>A Beszédfelismerés szolgáltatással írjanak elő irányelvek

Testreszabásához **szöveg beszédfelismerés** vagy **szöveg-beszéd átalakítás**, mellett beszéd szöveget kell megadnia. A szöveg soronként egy egyetlen utterance felel meg. A szöveg egy *Beszélgetés szövegének*, és egy meghatározott formátumban kell létrehozni.

A beszédfelismerés szolgáltatás néhány normalizations, annak érdekében, hogy a szöveg konzisztens hajtja végre. Más normalizálási feladatok képzési elküldve a szöveg előtt kell elvégezni. 

A cikk mindkét normalizations típusú. Az irányelvek különböző nyelvekhez némileg eltérőek lehetnek.

## <a name="us-english-en-us"></a>Amerikai angol (en-US)

Szöveg a szolgáltatásba való feltöltés kell lehet adatokat írni az egyszerű szöveges használata csak az ASCII karakterkészlet. A fájl minden egyes sorának tartalmaznia kell egy egyetlen utterance szövegét.

Fontos, hogy kerülje el a kiterjesztett (Latin-1) vagy a Unicode írásjeleket. Ezeket a karaktereket is, hogy tartalmazza az adatokat egy szövegszerkesztő program vagy adatai lekaparást előkészítésekor. Cserélje le ezeket a karaktereket megfelelő ASCII helyettesítések szempontjából. Példa:

| Karakterek elkerülésére | Helyettesítés |
|----- | ----- |
| "Hello world" (megnyitására és bezárására a dupla idézőjelek között) | "Hello world" (idézőjelek) |
| János napja (jobb oldali szimpla idézőjel) | János napja (aposztróf) |
| jó volt – nem volt nagyszerű! (em dash) | jó--volt nem, mert nagyszerű! (kötőjel) |

### <a name="text-normalization-performed-by-the-service"></a>A szolgáltatás által végzett szöveg normalizálási

A beszédfelismerés szolgáltatás végzi a következő szöveg normalizálási szöveg ki.

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

### <a name="text-normalization-you-must-perform"></a>Végre kell hajtania szöveg normalizálási

A szöveg ki a következő normalizálási vonatkozik.

*   Kell írható rövidítések szóbeli űrlap tükrözik a szavakat a
*   Nem szabványos numerikus karakterláncokat (például néhány dátum vagy számlázási űrlapok) kell megírni szavakat
*   Vegyes alfanumerikus karaktereket és nem alfabetikus karaktereket szavak kell kért, mert hangsúlyozottan
*   Rövidítéseket hagyása szavakat, hangsúlyozottan hagyja. Például sugárdiagram, lézernyomtatókra, RAM, NATO és Mr.
*   Külön betű szóközökkel elválasztott meghajtóbetűjellel rendelkező hangsúlyozottan rövidítések írni. Ha például IBM, a CPU, a FBI, a Feladatdiagnosztika, a NaN. 

Néhány példa:

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 14 Üzenetnek 3. Dr. | 14 northeast harmadik meghajtó |
| Dr. Strangelove | Orvosi Strangelove |
| 007 James kötés | James kötés duplán bizony hét |
| Ke$ magas rendelkezésre állású | Kesha |
| Milyen hosszú legyen a 2 darab 4 | Milyen hosszú legyen a két négy |
| Az értekezlet kerül az 1-3 pm | Az értekezlet kerül egy három délután |
| a vér típus O + | A vér típus pozitív O: |
| Vízjel H20 | Vízjel H 2 O |
| Van Halen által OU812 lejátszása | O-U 8 1-2 szerint Van Halen lejátszása |
| UTF-8 az Anyagjegyzék | U T F 8 az Anyagjegyzék |

## <a name="chinese-zh-cn"></a>Kínai (zh-CN)

Az egyéni beszéd szolgáltatás feltöltött szöveg adatok bájtsorrend jelölő UTF-8 kódolást kell használniuk. A fájl minden egyes sorának tartalmaznia kell egy egyetlen utterance szövegét.

Fontos, hogy kerülje el a teljes szélességű írásjeleket. Ezeket a karaktereket is, hogy tartalmazza az adatokat egy szövegszerkesztő program vagy adatai lekaparást előkészítésekor. Cserélje le azokat megfelelő teljes szélességű helyettesítések szempontjából. Példa:

| Karakterek elkerülésére | Helyettesítés |
|----- | ----- |
| "你好" (Megnyitás és Bezárás dupla idézőjelek között) | "你好" (dupla idézőjelek között) |
| 需要什么帮助? (kérdőjel) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-service"></a>A szolgáltatás által végzett szöveg normalizálási

A beszédfelismerés szolgáltatás végzi a következő szöveg normalizálási szöveg ki.

*   Minden írásjelet eltávolítása
*   A szóbeli űrlap bővülő számok
*   Kétbájtos karaktereket konvertálása félig karaktereket
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

### <a name="text-normalization-you-must-perform"></a>Végre kell hajtania szöveg normalizálási

A szöveg importálás előtt a következő normalizálási vonatkozik.

*   Kell írható rövidítések szóbeli űrlap tükrözik a szavakat a
*   Ez a szolgáltatás nem fedi le minden numerikus mennyiséget. További megbízható szóbeli formában numerikus karakterláncok kiírni.

Néhány példa:

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Egyéb nyelvek

Szöveges adatok fel van töltve a **szöveg beszédfelismerés** szolgáltatás bájtsorrend jelölő UTF-8 kódolást kell használnia. A fájl minden egyes sorának tartalmaznia kell egy egyetlen utterance szövegét.

> [!NOTE]
> Ezekben a példákban német. Azonban a szabályok érvényesek, amelyek nem angol vagy kínai összes nyelvet.

### <a name="text-normalization-performed-by-the-service"></a>A szolgáltatás által végzett szöveg normalizálási

A beszédfelismerés szolgáltatás végzi a következő szöveg normalizálási szöveg ki.

*   Alsó-kis-és az összes szöveg
*   Például különböző típusú ajánlatok minden írásjelet eltávolítása ("teszt", a "test", a "teszt" vagy a "teszt" ok)
*   Eldobja az összes sort tartalmazó speciális karakterek a készletből ^ ˘ l ¤ y ¦ § © ª ¬® ° ± ² µ × y Ø¬¬
*   Word űrlaphoz, beleértve a dollár vagy euró összegek számok bővítése
*   Umlautokat csak egy, a o elfogadás u; mások "th" váltja fel, vagy elveti a rendszer

Néhány példa

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| Frankfurter Ring | Frankfurter ring |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| WIR, haben | WIR haben |
| Das macht $10 | das macht zehn dollár |

### <a name="text-normalization-you-must-perform"></a>Végre kell hajtania szöveg normalizálási

A szöveg importálás előtt a következő normalizálási vonatkozik.

*   Decimális pont lehet ",", és nem".": 2,3-nem 2.3 %
*   Idő elválasztó óra és perc között kell lennie ":", és nem".": 12:00 Uhr
*   Rövidítések "ca.", "bzw." nem cserélhető. Azt javasoljuk, hogy a teljes formátumot használja.
*   A rendszer eltávolítja az öt fő matematikai operátorok: +, -, \*, /. Javasoljuk, hogy a literális forma felülírás: plusz és mínusz kárte, geteilt.
*   Ugyanez vonatkozik az összehasonlító operátor (=, <>,) - gleich, kleiner als, grösser als
*   Használja a törtek, 3 vagy 4, például a word-formátumban (például "drei viertel" ¾ helyett)
*   A "szimbólum cserélje le a word"Euro"képernyő

Néhány példa:

| Eredeti szöveg | Felhasználó normalizálási után | Rendszer normalizálási után
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei és zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 – 4 | 2 és 3 mínusz 4 | zwei és mínusz vier drei|
| Das macht 12 " | Das macht 12 Euros | das macht zwölf euros |

## <a name="next-steps"></a>További lépések

- [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A C# beszéd felismerésére](quickstart-csharp-windows.md)
