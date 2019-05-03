---
title: Emberi címkével beszédátírás irányelvek – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Ha pontosságát, különösen szavak törölt vagy nem megfelelően helyettesített, okozó problémák javítása érdekében érdemes beszédátírás emberi címkével a hang adatokkal együtt használja. Mik azok az emberi címkével beszédátírás? Ez nem probléma, a word-word, a szó beszédátírás hangfájl azok.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7f0b467284872f3d936984741c6d092705008a5a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025922"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Emberi címkével beszédátírás létrehozása

Ha pontosságát, különösen szavak törölt vagy nem megfelelően helyettesített, okozó problémák javítása érdekében érdemes beszédátírás emberi címkével a hang adatokkal együtt használja. Mik azok az emberi címkével beszédátírás? Ez nem probléma, a word-word, a szó beszédátírás hangfájl azok.

Az átírási adatok nagy minta felismerés javítása szükséges, javasoljuk, hogy beszédátírási adatok 1000, és 10 óra közötti biztosít. Ezen a lapon áttekintheti az irányelveket, amelyek segítségével hozhat létre kiváló minőségű beszédátírás. Ez az útmutató a területi beállítást, az angol, Mandarin kínai és német szakaszok van osztva.

## <a name="us-english-en-us"></a>Amerikai angol (en-US)

Angol nyelvű Audio emberi címkével beszédátírás csak az ASCII-karakterek használatával egyszerű szövegként kell adni. Kerülje a Latin 1 vagy Unicode írásjeleket használatát. Ezek a karakterek gyakran véletlenül kerülnek, ha szöveget másol szövegszerkesztőből vagy automatizované získávání dat weblapokról származó adatok. Ha ezek a karakterek is szerepelnek, ügyeljen arra, hogy frissítse azokat a megfelelő ASCII-helyettesítés.

Íme, néhány példa:

| Karakterek elkerülése érdekében | Helyettesítés | Megjegyzések |
|---------------------|--------------|-------|
| "Hello world" | "Hello world" | A nyitó és záró idézőjelet rendelkezik gondolatjelek a megfelelő ASCII-karaktereket. |
| János napja | János napja | A megfelelő ASCII-karaktert az aposztróf lett kijelölve. |
| jó volt – nem, nagyszerű volt! | jó--volt nem, mert nagyszerű! | A gondolatjelet helyettesítette két kötőjellel. |

### <a name="text-normalization-for-us-english"></a>Az angol szöveget normalizálási

Szöveg normalizálás használt modell betanításakor konzisztens formátum szó az átalakítást. Néhány normalizálási szabályok érvényesek szöveg automatikusan, azonban azt javasoljuk, ezeket az irányelveket, előkészíti az emberi címkével beszédátírási adatokat:

* Írható ki rövidítések a szavakat.
* Kiírni a nem szabványos numerikus karakterláncokat (például számlázási feltételek) szavakat.
* Nem alfanumerikus karakterek vagy vegyes alfanumerikus karaktereket, ejtsd kell kért.
* Rövidítéseket, amelyek a rendszer ejtsd: szavakat (például "a mérlegeli", "lézernyomtatási", "RAM" vagy "NATO") nem szerkeszthető.
* Írási ki rövidítéseket, amelyek a rendszer ejtsd: külön tartalmazhat, minden betű, szóközzel elválasztva.

Íme néhány példa a normalizálási az átírási a végre kell hajtania:

| Eredeti szöveg | Normalizálási utáni szöveg |
|---------------|--------------------------|
| Dr. Bruce szalagcím | Orvos Bruce szalagcím |
| James Bond, 007 | James Bond, duplán hoppá hét |
| Ke$ha | Kesha |
| Mennyi ideig tart a 2 x 4 | Mennyi ideig tart a két négy |
| Az értekezlet kerül az 1-3 pm | Az értekezlet kerül egy és három pm |
| saját vér típus O + | Saját vér típus pozitív O |
| víz H20 | víz H 2 O |
| által Van Halen OU812 lejátszása | O U 8 1-2 szerint Van Halen lejátszása |
| UTF-8 az Anyagjegyzék | U T F 8 az Anyagjegyzék |

Az alábbi normalizálási szabályok beszédátírás automatikusan érvényesek:

* Kisbetűk használata.
* Távolítsa el a szavak belül aposztrófot kivételével az összes absztrakt.
* Bontsa ki a számok szavakat/beszélt űrlapra, például összegeket.

Íme néhány példa a normalizálási az átírási automatikusan végre:

| Eredeti szöveg | Normalizálási utáni szöveg |
|---------------|--------------------------|
| "Szent vonatkozó!" említett Batman. | Szent vonatkozó mondta batman |
| "Mi?" említett Batman sidekick, a terheléselosztást. | milyen említett batman sidekick multiplexelés |
| Nyissa meg a get - em! | Nyissa meg a get-em |
| Double-jointed vagyok | Kettős jointed vagyok |
| 104 utca utca. | egy hoppá négy utca utca. |
| A 102.7 hangolása | Állítson be egy hoppá két hét |
| A pi készül 3,14 | a pi körülbelül három pont egy négy |
$3,14 költségei| három tizennégy költségei |

## <a name="mandarin-chinese-zh-cn"></a>Mandarin kínai (zh-cn)

Mandarin kínai Audio emberi címkével beszédátírás UTF-8 kódolású a bájtsorrendjelző mutató kell lennie. Kerülje a teljes szélességű írásjelek karakterek használatát. Ezek a karakterek szerepelhetnek véletlenül a az adatelőkészítés szövegszerkesztő programokban vagy scrape weblapokról származó adatokat. Ha ezek a karakterek is szerepelnek, ügyeljen arra, hogy frissítse azokat a megfelelő teljes szélességű helyettesítés.

Íme, néhány példa:

| Karakterek elkerülése érdekében | Helyettesítés | Megjegyzések |
|---------------------|--------------|-------|
| "你好" | "你好" | A nyitó és záró idézőjelet rendelkezik gondolatjelek megfelelő karakterrel. |
| 需要什么帮助? | 需要什么帮助？ | A kérdőjel a megfelelő karakterrel lett kijelölve. |

### <a name="text-normalization-for-mandarin-chinese"></a>A kínai Mandarin szöveg normalizálási

Szöveg normalizálás használt modell betanításakor konzisztens formátum szó az átalakítást. Néhány normalizálási szabályok érvényesek szöveg automatikusan, azonban azt javasoljuk, ezeket az irányelveket, előkészíti az emberi címkével beszédátírási adatokat:

* Írható ki rövidítések a szavakat.
* Használja a beszélt formában numerikus karakterláncok kiírni.

Íme néhány példa a normalizálási az átírási a végre kell hajtania:

| Eredeti szöveg | Normalizálási utáni szöveg |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

Az alábbi normalizálási szabályok beszédátírás automatikusan érvényesek:

* Távolítsa el az összes írásjelek
* Bontsa ki a számokat a kimondott űrlap
* Kétbájtos karaktereket átalakítása félig karaktereket
* Nagybetűk használata az összes angol szavakat

Íme néhány példa a normalizálási az átírási automatikusan végre:

| Eredeti szöveg | Normalizálási utáni szöveg |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Német (de-DE) és más nyelveken

Emberi címkével beszédátírás német hang (és más nem angol nyelvű vagy Mandarin kínai nyelvek) UTF-8 kódolású a bájtsorrendjelző mutató kell lennie. Egy emberi címkével átiratok hang fájl esetében kell megadni.

### <a name="text-normalization-for-german"></a>Szöveg normalizálási német

Szöveg normalizálás használt modell betanításakor konzisztens formátum szó az átalakítást. Néhány normalizálási szabályok érvényesek szöveg automatikusan, azonban azt javasoljuk, ezeket az irányelveket, előkészíti az emberi címkével beszédátírási adatokat:

*   Tizedes tört pontot is írhat ","és nem".".
*   Írási elválasztója mint ":"és nem"." (például: 12:00 Uhr).
*   Rövidítéseket, például a "ca". nem cserélhető le. Azt javasoljuk, hogy használja-e teljes kimondott formájában.
*   A négy fő matematikai operátorokat (+, -, \*, és /) el lesznek távolítva. Azt javasoljuk, és cserélje le őket az írásos formába: "plusz," "mínusz," "kártevő" és "geteilt."
*   Törlődnek az összehasonlítási operátor (=, <, és >). Azt javasoljuk, és cserélje le őket "gleich", "kleiner als," és "grösser als."
*   Az írásos formába percenkénti egységeinek törtrészeként 3/4-es, mint például írási (például: "drei viertel" 3/4 helyett).
*   Cserélje le a """szimbólumra az írásos formába"Euró."

Íme néhány példa a normalizálási az átírási a végre kell hajtania:

| Eredeti szöveg | Felhasználói normalizálási utáni szöveg | Rendszer normalizálási utáni szöveg |
|---------------|-------------------------------|---------------------------------|
| Es Izraeli normál idő szerint 12.23 Uhr | Es Izraeli normál idő szerint 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 és 3-4 mínusz | zwei plusz -mínusz vier drei |

Az alábbi normalizálási szabályok beszédátírás automatikusan érvényesek:

* Kisbetűk használata a teljes szöveg.
* Távolítsa el az összes írásjelek, beleértve a különböző típusú idézőjelek ("teszt", "teszt", "teszt" és "teszt" megengedettek).
* Ez a különleges karaktereket tartalmazó sorok elvetéséhez: ˘ ¤ y ¦ § © ª ¬® ° ± ² µ x y Ø¬¬.
* Bontsa ki a számot használja a beszélt űrlaphoz, beleértve a dollár vagy összegek.
* Fogadja el a umlautokat csak az a, b, és meg. Mások "th" váltja fel, vagy lehet elvetni.

Íme néhány példa a normalizálási az átírási automatikusan végre:

| Eredeti szöveg | Normalizálási utáni szöveg |
|---------------|--------------------------|
| Frankfurter kör | Frankfurter kör |
| ¡Eine Frage! | eine frage |
| WIR, haben | WIR haben |

## <a name="next-steps"></a>További lépések

* [Készítse elő és az adatok tesztelése](how-to-custom-speech-test-data.md)
* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
* [Az adatok](how-to-custom-speech-evaluate-data.md)
* [A modell tanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
