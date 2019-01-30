---
title: A Speech Service képzéshez beszédátírási irányelvek
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan készíti elő az akusztikai és nyelvi modelleket és hangtípust testreszabása a beszédfelismerési szolgáltatás szöveg.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 44f2cb9e95a9a79c85c23b0eebd919851b94c1f8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228134"
---
# <a name="transcription-guidelines-for-using-the-speech-service"></a>A beszédfelismerési szolgáltatás beszédátírási irányelvek

Testreszabásához **Speech to Text** vagy **szöveg-beszéd átalakítás**, meg kell adnia a szöveg és beszéd. A szövegben minden sor egy egyetlen utterance (kifejezés) felel meg. A szöveg, lehető legközelebb egyeznie kell a speech. A szöveg egy *átirat*, és a egy meghatározott formátumban kell létrehoznia.

A beszédfelismerési szolgáltatás Normalizálja a bemeneti szöveg konzisztens. 

Ez a cikk normalizations mindkét típusát ismerteti. Az irányelvek különböző nyelveken némileg eltérőek lehetnek.

## <a name="us-english-en-us"></a>Amerikai angol (en-us)

Szöveges adatok kell írni, soronként egy utterance (kifejezés) egyszerű szöveges formátumban, csak az ASCII karakterkészlet használatával.

Kerülje a kiterjesztett (Latin-1) vagy Unicode írásjeleket használatát. Ezek a karakterek szerepelhetnek véletlenül scrape weblapok adatait vagy a az adatelőkészítés egy szövegszerkesztő programban. Cserélje le a megfelelő ASCII helyettesítések hozzáadása a karaktereket. Példa:

| Karakterek elkerülése érdekében | Helyettesítés |
|----- | ----- |
| "Hello world" (megnyitása és bezárása a dupla idézőjelekkel együtt) | "Hello world" (dupla idézőjelekkel együtt) |
| János napja (jobb szimpla idézőjel) | János napja (aposztrófot) |
| jó volt – nem, nagyszerű volt! (em dash) | jó--volt nem, mert nagyszerű! (kötőjel) |

### <a name="text-normalization-rules-for-english"></a>Szöveg normalizálási szabályok az angol nyelvű tájékoztatáshoz

A beszédfelismerési szolgáltatás hajtja végre az alábbi normalizálási szabályok:

* Kisbetűk használata a teljes szöveg
* A word-belső aposztrófot kivételével az összes írásjelek eltávolítása
* Használja a beszélt űrlaphoz, például összegeket növekvő számok

Néhány példa:

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| "Szent vonatkozó!" említett Batman. | Szent vonatkozó mondta batman |
| "Mi?" említett Batman sidekick, a terheléselosztást. | milyen említett batman sidekick multiplexelés |
| Nyissa meg a get - em! | Nyissa meg a get-em |
| Double-jointed vagyok | Kettős jointed vagyok |
| 104 utca utca. | egy hoppá négy utca utca. |
| A 102.7 hangolása | Állítson be egy hoppá két hét |
| A pi készül 3,14 | a pi körülbelül három pont egy négy |
| $3,14 költségei | három tizennégy költségei |

A szöveg szövegekben az alábbi normalizálási vonatkoznak:

* Rövidítések betűvel kell írni.
* (Például bizonyos dátum vagy számlázási űrlapok) nem szabványos numerikus karakterláncok betűvel kell írni.
* Szavak vegyes alfanumerikus karakterek vagy nem alfabetikus karaktereket kell megjelenített érzéseket, mivel ejtsd.
* Rövidítéseket, amelyek a rendszer ejtsd: szavakat változatlanul hagyja (például "sugárdiagram", "lézernyomtatási," "RAM" vagy "NATO").
* Írási rövidítéseket, amelyek szerint külön vannak ejtsd betűket, szóközzel elválasztva meghajtóbetűjellel rendelkező (például "IBM," "Processzor", "FBI", "Nem végleges" vagy "NaN"). 

Néhány példa:

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 14 NE 3rd Dr. | tizennégy északkelet harmadik meghajtó |
| Dr. Bruce szalagcím | Orvos Bruce szalagcím |
| James Bond, 007 | James Bond, duplán hoppá hét |
| Ke$ha | Kesha |
| Mennyi ideig tart a 2 x 4 | Mennyi ideig tart a két négy |
| Az értekezlet kerül az 1-3 pm | Az értekezlet kerül egy és három pm |
| saját vér típus O + | Saját vér típus pozitív O |
| víz H20 | víz H 2 O |
| által Van Halen OU812 lejátszása | O U 8 1-2 szerint Van Halen lejátszása |
| UTF-8 az Anyagjegyzék | U T F 8 az Anyagjegyzék |

## <a name="chinese-zh-cn"></a>Kínai (zh-cn)

A Custom Speech Service feltöltött, szöveges adatok egy bájtsorrendjelző jelölő az UTF-8 kódolást kell használniuk. A fájl minden sorában egy utterance (kifejezés) kell írni.

Kerülje a teljes szélességű írásjelek karakterek használatát. Ezek a karakterek szerepelhetnek véletlenül scrape weblapok adatait vagy a az adatelőkészítés egy szövegszerkesztő programban. Cserélje le azokat megfelelő teljes szélességű helyettesítések. Példa:

| Karakterek elkerülése érdekében | Helyettesítés |
|----- | ----- |
| "你好" (megnyitása és bezárása a dupla idézőjelekkel együtt) | "你好" (dupla idézőjelekkel együtt) |
| 需要什么帮助? (kérdőjel) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>Kínai szöveg normalizálási szabályok

A beszédfelismerési szolgáltatás hajtja végre az alábbi normalizálási szabályok:

* Az összes írásjelek eltávolítása
* Használja a beszélt űrlap növekvő számok
* Kétbájtos karaktereket konvertálása félig karaktereket
* Nagybetűk használata az összes angol szavakat

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

Mielőtt importálja a szöveg, alkalmaz rá az alábbi normalizálási:

* Rövidítések a szavak (mint a kimondott képernyő) kell írni.
* Használja a beszélt formában numerikus karakterláncok kiírni.

Néhány példa:

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Más nyelveken

Szöveges adatot feltölteni a **Speech to Text** szolgáltatást kell használnia egy bájtsorrendjelző jelölő az UTF-8 kódolást. A fájl minden sorában egy utterance (kifejezés) kell írni.

> [!NOTE]
> A következő példákban német. Az irányelvek azonban minden nyelven, nem angol vagy kínai vonatkoznak.

### <a name="text-normalization-rules-for-german"></a>Szöveg normalizálási szabályok német

A beszédfelismerési szolgáltatás hajtja végre az alábbi normalizálási szabályok:

* Kisbetűk használata a teljes szöveg
* Eltávolítása az összes írásjelek, beleértve a különböző típusú idézőjelek ("teszt", "teszt", "teszt" és "teszt" olyan OK)
* A set-˘ a különleges karakterek tartalmazó sorok elvetése ¤ y ¦ § © ª ¬® ° ± ² µ x y Ø¬¬
* Szóformaként, beleértve a dollár vagy összegek növekvő számok
* Elfogadásával umlautokat csak az a, b, és hogy; mások "th" váltja fel, és elveti

Néhány példa:

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| Frankfurter kör | Frankfurter kör |
| ¡Eine Frage! | eine frage |
| WIR, haben | WIR haben |

Mielőtt importálja a szöveg, alkalmaz rá az alábbi normalizálási:

* Tizedes tört kell lenniük, ","és nem".".
* Elválasztója óra és perc között kell lennie ":"és nem"." (például 12:00 Uhr).
* Rövidítéseket, például a "ca". nem cserélhető le. Azt javasoljuk, hogy használja-e a teljes képernyőn.
* A négy fő matematikai operátorokat (+, -, \*, és /) el lesznek távolítva. Azt javasoljuk, és cserélje le őket a szövegkonstans forma: "plusz," "mínusz," "kártevő" és "geteilt."
* Összehasonlító operátorok ugyanaz a szabály vonatkozik (=, <, és >). Azt javasoljuk, és cserélje le őket "gleich", "kleiner als," és "grösser als."
* Használja a percenkénti egységeinek törtrészeként 3/4-es, például a word formátumban (például "drei viertel" ¾ helyett).
* Cserélje le a "szimbólumra az szóformaként"Euró."

Néhány példa:

| Eredeti szöveg | A felhasználó normalizálási után | Rendszer normalizálási után
|--------  | ----- | -------- |
| Es Izraeli normál idő szerint 12.23 Uhr | Es Izraeli normál idő szerint 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf ||
| 2 + 3 - 4 | 2 és 3-4 mínusz | zwei plusz -mínusz vier drei|

## <a name="next-steps"></a>További lépések

- [Speech Service próba-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Beszéd felismerése C# nyelven](quickstart-csharp-dotnet-windows.md)
