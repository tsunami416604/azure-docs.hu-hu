---
title: Beszéd képzéshez beszédátírási irányelvek |} A Microsoft Docs
description: Ismerje meg, hogyan készítse elő a szöveg testreszabása akusztikai és nyelvi modelleket és hangtípust a beszédfelismerési szolgáltatás.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: PanosPeriorellis
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: acca6f4cd2f4e7b452f5a70457d3d034e4d4aa7e
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345185"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>A Speech szolgáltatással beszédátírási irányelvek

Testreszabásához **Speech to Text** vagy **szöveg-beszéd átalakítás**, meg kell adnia a szöveg és beszéd. A szövegben minden sor egy egyetlen utterance (kifejezés) felel meg. A szöveg pontosan egyeznie kell a beszéd, a lehető. A szöveg egy *átirat*, és a egy meghatározott formátumban kell létrehoznia.

A beszédfelismerési szolgáltatás Normalizálja a bemeneti szöveg konzisztens. 

Ez a cikk normalizations mindkét típusát ismerteti. Az irányelvek különböző nyelveken némileg eltérőek lehetnek.

## <a name="us-english-en-us"></a>Amerikai angol (en-US)

Szöveges adatok kell írni, soronként egy utterance (kifejezés) egyszerű szöveges formátumban, csak az ASCII karakterkészlet használatával.

Kerülje a kiterjesztett (Latin-1) vagy Unicode írásjeleket használatát. Ezek a karakterek szerepelhetnek véletlenül az adatokat egy szövegszerkesztő program vagy automatizované získávání dat weblapokról származó adatok előkészítésekor. Cserélje le a megfelelő ASCII helyettesítések ezeket a karaktereket. Példa:

| Karakterek elkerülése érdekében | Helyettesítés |
|----- | ----- |
| "Hello world" (Nyissa meg, és zárja be az idézőjelekkel együtt) | "Hello world" (dupla idézőjel) |
| János napja (jobb szimpla idézőjel) | János napja (aposztrófot) |
| jó volt – nem, nagyszerű volt! (em dash) | jó--volt nem, mert nagyszerű! (kötőjel) |

### <a name="text-normalization-rules-for-english"></a>Szöveg normalizálási szabályok az angol nyelvű tájékoztatáshoz

A beszédfelismerési szolgáltatás hajtja végre az alábbi normalizálási szabályok.

*   Alsó – kis-és a teljes szöveg
*   A word-belső aposztrófot kivételével az összes írásjelek eltávolítása
*   Használja a beszélt űrlaphoz, például összegeket számok bővítése

Íme néhány példa

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

Az alábbi normalizálási alkalmazni a szöveg szövegekben.

*   Rövidítések szavak kell megírni
*   Nem szabványos numerikus karakterláncokat (például bizonyos dátum vagy számlázási űrlapok) kell írni a szavakat
*   Szavak vegyes alfanumerikus karakterek vagy nem alfabetikus karaktereket kell megjelenített érzéseket, mivel ejtsd
*   Rövidítések ejtsd: szavakat változatlanul hagyja. Ha például a mérlegeli, lézer, RAM, NATO.
*   Az írási ejtsd: külön betűket, szóközzel elválasztva meghajtóbetűjellel rendelkező rövidítéseket. Például, IBM, Processzor, az FBI, TBD, NaN. 

Néhány példa:

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 14 új 3. vészhelyreállítás. | tizennégy északkelet harmadik meghajtó |
| Dr. Bruce szalagcím | Orvos Bruce szalagcím |
| James Bond, 007 | James Bond, duplán hoppá hét |
| Ke$ magas rendelkezésre állás | Kesha |
| Mennyi ideig tart a 2 x 4 | Mennyi ideig tart a két négy |
| Az értekezlet kerül az 1-3 pm | Az értekezlet kerül egy és három pm |
| saját vér típus O + | Saját vér típus pozitív O |
| víz H20 | víz H 2 O |
| által Van Halen OU812 lejátszása | O U 8 1-2 szerint Van Halen lejátszása |
| UTF-8 az Anyagjegyzék | U T F 8 az Anyagjegyzék |

## <a name="chinese-zh-cn"></a>Kínai (zh-CN)

A Custom Speech Service feltöltött szöveges adatok bájtsorrendjelző jelölő az UTF-8 kódolást kell használniuk. A fájl minden sorában egy utterance (kifejezés) kell írni.

Kerülje a teljes szélességű írásjelek karakterek használatát. Ezek a karakterek szerepelhetnek véletlenül az adatokat egy szövegszerkesztő program vagy automatizované získávání dat weblapokról származó adatok előkészítésekor. Cserélje le azokat megfelelő teljes szélességű helyettesítések. Példa:

| Karakterek elkerülése érdekében | Helyettesítés |
|----- | ----- |
| "你好" (Megnyitás és Bezárás idézőjelekkel együtt) | "你好" (az idézőjelekkel együtt) |
| 需要什么帮助? (kérdőjel) | 需要什么帮助? |

### <a name="text-normalization-rules-for-chinese"></a>Kínai szöveg normalizálási szabályok

A beszédfelismerési szolgáltatás hajtja végre az alábbi normalizálási szabályok.

*   Az összes írásjelek eltávolítása
*   Használja a beszélt űrlap növekvő számok
*   Kétbájtos karaktereket konvertálása félig karaktereket
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

Mielőtt importálná azokat a alkalmazni a szöveget az alábbi normalizálási.

*   Rövidítések szavak (mint a kimondott képernyő) kell megírni
*   Használja a beszélt formában numerikus karakterláncok kiírni.

Néhány példa:

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Más nyelveken

Szöveges adatot feltölteni a **Speech to Text** szolgáltatást kell használnia a bájtsorrendjelző jelölő UTF-8 kódolást. A fájl minden sorában egy utterance (kifejezés) kell írni.

> [!NOTE]
> Ezek a példák a német. Azonban ezeket az irányelveket, amelyek nem angol vagy kínai összes nyelv vonatkoznak.

### <a name="text-normalization-rules-for-german"></a>Szöveg normalizálási szabályok német

A beszédfelismerési szolgáltatás hajtja végre az alábbi normalizálási szabályok.

*   Alsó – kis-és a teljes szöveg
*   Az összes írásjelek, beleértve a különböző típusú ajánlatok eltávolítása ("teszt", "teszt", "teszt" vagy "teszt" olyan ok)
*   A set-˘ a különleges karakterek tartalmazó sorok elvetése ¤ y ¦ § © ª ¬® ° ± ² µ x y Ø¬¬
*   Számok szóformaként, többek között a dollár vagy euró összegek bővítése
*   Umlautokat csak egy, a o elfogadás u. mások "th" váltja fel, és elveti

Íme néhány példa

| Eredeti szöveg | Normalizálási után |
|----- | ----- |
| Frankfurter kör | Frankfurter kör |
| ¡Eine Frage! | eine frage |
| WIR, haben | WIR haben |

Mielőtt importálná azokat a alkalmazni a szöveget az alábbi normalizálási.

*   Tizedesjel legyen ","és nem"."
*   Idő elválasztó óra és perc között kell lennie ":"és nem".": 12:00 Uhr
*   Rövidítéseket, például a "ca". nem cserélhető le. Azt javasoljuk, hogy a teljes képernyőn.
*   Az öt fő matematikai operátorokat el lesznek távolítva: +, -, \*, /. Azt javasoljuk, és cserélje le őket a szövegkonstans forma: plusz fiókközpont, geteilt csökkentve.
*   Ugyanez vonatkozik az összehasonlítási operátor (=, <>,) – gleich, kleiner als, grösser als
*   Használja a percenkénti egységeinek törtrészeként 3/4-es, mint a word formátumban (például "drei viertel" ¾ helyett)
*   Cserélje le a szóformaként "Euró" a "szimbólumot

Néhány példa:

| Eredeti szöveg | A felhasználó normalizálási után | Rendszer normalizálási után
|--------  | ----- | -------- |
| Es Izraeli normál idő szerint 12.23Uhr | Es Izraeli normál idő szerint 12:23Uhr | es Izraeli normál idő szerint zwölf uhr drei és zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf ||
| 2 és 3-4 | 2 és 3-4 mínusz | zwei plusz -mínusz vier drei|

## <a name="next-steps"></a>További lépések

- [A beszédfelismerés próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A beszédfelismerést a C#-ban](quickstart-csharp-windows.md)
