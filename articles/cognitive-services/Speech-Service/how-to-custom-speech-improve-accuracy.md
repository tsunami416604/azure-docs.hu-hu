---
title: Modell javítása Custom Speech-Speech Service-hez
titleSuffix: Azure Cognitive Services
description: Az emberi címkével ellátott átiratok és a kapcsolódó szövegek bizonyos típusai javíthatják a beszédfelismerési pontosságot egy beszéd – szöveg modell esetében a beszélő forgatókönyv alapján.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-demjoh
ms.openlocfilehash: bb904482f3cb5900b724803816269f1b10ab3720
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727877"
---
# <a name="improve-custom-speech-accuracy"></a>Custom Speech pontosság javítása

Ebből a cikkből megtudhatja, hogyan javíthatja az egyéni modell minőségét hang-, emberi címkével ellátott átiratok és kapcsolódó szövegek hozzáadásával.

## <a name="accuracy-in-different-scenarios"></a>Pontosság különböző forgatókönyvekben

A beszédfelismerési forgatókönyvek a hangminőség és a nyelv (szókincs és beszéd stílus) szerint változnak. A következő táblázat a négy gyakori forgatókönyvet vizsgálja:

| Forgatókönyv | Hangminőség | Szókincs | Beszéd stílusa |
|----------|---------------|------------|----------------|
| Call Center | Az alacsony, 8 kHz-es lehet 2 ember 1 hangcsatornán, tömörítve | Keskeny, egyedi tartományhoz és termékekhez | Társalgási, lazán strukturált |
| Hangsegéd (például Cortana vagy áteresztő ablak) | Magas, 16 kHz | Entitás – nehéz (Song titles, Products, locations) | Egyértelműen megadott szavak és kifejezések |
| Diktálás (azonnali üzenet, megjegyzések, keresés) | Magas, 16 kHz | Változatos | Megjegyzés – bevétel |
| Videós kódolt feliratok | Változatos, beleértve a különböző mikrofon-használatot, zenéket is hozzáadva | Változatos, a megbeszélések, a beszéd, a musical lyrics | Olvasás, előkészített vagy lazán strukturált |

A különböző forgatókönyvek eltérő minőségi eredményeket hoznak létre. Az alábbi táblázat azt vizsgálja, hogy a négy forgatókönyv tartalma milyen arányban szerepel a [Word Error Rate (WER)](how-to-custom-speech-evaluate-data.md)alapján. A táblázat az egyes forgatókönyvekben leggyakrabban használt hibákat mutatja.

| Forgatókönyv | Beszédfelismerési minőség | Beszúrási hibák | Törlési hibák | Helyettesítési hibák |
|----------|----------------------------|------------------|-----------------|---------------------|
| Call Center | Közepes (< 30% WER) | Alacsony, kivéve, ha mások a háttérben beszélgetnek | Magas lehet. A Call Centers lehet zajos, az átfedésben lévő hangszórók pedig megzavarják a modellt | Közepes. A termékek és a személyek nevei okozhatják ezeket a hibákat |
| Hangvezérelt asszisztens | Magas (lehet < 10% WER) | Alacsony | Alacsony | Közepes, a dalok címei, terméknév vagy helyei miatt |
| Diktálás | Magas (lehet < 10% WER) | Alacsony | Alacsony | Magasság |
| Videós kódolt feliratok | A videó típusától függ (lehet < 50% WER) | Alacsony | Zene, zajok, mikrofon minősége miatt magas lehet | A szakzsargon okozhatják ezeket a hibákat |

A WER összetevőinek (a Beszúrás, a törlés és a behelyettesítési hibák száma) meghatározása segít meghatározni, hogy milyen típusú adattípust kell hozzáadni a modell tökéletesítéséhez. Az alapmodellek minőségének megtekintéséhez használja a [Custom Speech portált](https://speech.microsoft.com/customspeech) . A portál a WER minőségi díjszabásában összevont beszúrási, helyettesítési és törlési hibák arányát jelenti.

## <a name="improve-model-recognition"></a>A modell felismerésének javítása

A [Custom Speech-portálon](https://speech.microsoft.com/customspeech)betanítási adatmennyiségek hozzáadásával csökkentheti a felismerési hibákat. 

Tervezze meg az egyéni modell karbantartását úgy, hogy rendszeresen hozzáadja a forrás-anyagokat. Az egyéni modellnek további képzésre van szüksége ahhoz, hogy tisztában legyen az entitások módosításaival. Előfordulhat például, hogy frissítenie kell a terméknév, a zeneszám neve vagy az új szolgáltatási helyet.

A következő szakaszok azt ismertetik, hogy a további betanítási információk milyen módon csökkenthetők a hibákkal.

### <a name="add-related-text-sentences"></a>Kapcsolódó szöveges mondatok hozzáadása

A további kapcsolódó szöveges mondatok elsősorban csökkentik a gyakori szavak és a tartományra vonatkozó szavak hibás felismerésével kapcsolatos helyettesítési hibákat, ha azok kontextusban vannak megjelenítve. A tartományra vonatkozó szavak nem lehetnek gyakoriak vagy felkészített szavak, de a kiejtésnek Egyértelműnek kell lennie.

> [!NOTE]
> Kerülje a kapcsolódó szöveges mondatokat, például a nem felismerhető karaktereket vagy szavakat.

### <a name="add-audio-with-human-labeled-transcripts"></a>Hang hozzáadása emberi címkével ellátott átiratokkal

Az emberi címkével ellátott átiratokkal rendelkező hang a lehető legnagyobb pontosságot nyújtja, ha a hang a cél használati esetből származik. A mintáknak a beszéd teljes körére kell kiterjedniük. A kiskereskedelmi tárolóhoz tartozó Call Center például a nyári hónapokban a fürdőruhával és a napszemüveggel kapcsolatos hívásokat fogja kérni. Gondoskodjon arról, hogy a minta tartalmazza a felderíteni kívánt beszédfelismerés teljes körét.

Tekintse meg az alábbi adatokat:

* A Custom Speech csak a behelyettesítési hibák, a beillesztési vagy törlési hibák csökkentése érdekében képes a Word-környezet rögzítésére.
* Kerülje az átírási hibákat tartalmazó mintákat, de a hangminőség sokféleségét is.
* Kerülje azokat a mondatokat, amelyek nem kapcsolódnak a problémás tartományhoz. A nem kapcsolódó mondatok károsíthatják a modellt.
* Ha az átiratok minősége változó, a kivételesen jó mondatok (például a kiváló átiratok, amelyek tartalmazzák a kulcsfontosságú kifejezéseket) megismétlődnek a súlyozásuk növeléséhez.

### <a name="add-new-words-with-pronunciation"></a>Új szavak hozzáadása a kiejtéssel

A létrehozott vagy különösen specializált szavak egyedi kiejtésekkel rendelkezhetnek. Ezeket a szavakat fel lehet ismerni, ha a szó kisebb szavakra bontható. Ha például fel szeretné ismerni az **xboxot**, "as **X Box**". Ez a megközelítés nem növeli az általános pontosságot, de a kulcsszavak felismerését is növelheti.

> [!NOTE]
> Ez a technika jelenleg csak bizonyos nyelvekhez érhető el. A részletekért tekintse meg a kiejtés testreszabását [a beszédfelismerési táblázatban](language-support.md) .

## <a name="sources-by-scenario"></a>Források forgatókönyv szerint

A következő táblázat a hangfelismerési forgatókönyveket mutatja be, és felsorolja azokat a forrás anyagokat, amelyeket figyelembe kell venni a fent felsorolt három képzési tartalom kategóriáján belül.

| Forgatókönyv | Kapcsolódó szöveges mondatok | Hang + emberi – címkézett átiratok | Új szavak a kiejtéssel |
|----------|------------------------|------------------------------|------------------------------|
| Call Center             | marketing dokumentumok, webhely, a Call Center tevékenységgel kapcsolatos termékekkel kapcsolatos felülvizsgálatok | az emberek által átadott Call Center-hívások | nem egyértelmű kiejtésekkel rendelkező kifejezések (lásd a fenti Xbox-t) |
| Hangvezérelt asszisztens         | mondatok listázása a parancsok és entitások összes kombinációjának használatával | hangokat rögzíthet az eszközön, és szöveget is leír | egyedi kiejtésekkel rendelkező nevek (filmek, dalok, termékek) |
| Diktálás               | írásos bevitel, például azonnali üzenetek vagy e-mailek | a fentiekhez hasonlóan | a fentiekhez hasonlóan |
| Videós kódolt feliratok | TV show-szkriptek, filmek, marketing-tartalmak, videó-összefoglalók | videók pontos átiratai | a fentiekhez hasonlóan |

## <a name="next-steps"></a>Következő lépések

- [A modell betanítása](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>További források

- [Az adatfeldolgozás előkészítése és tesztelése](how-to-custom-speech-test-data.md)
- [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)