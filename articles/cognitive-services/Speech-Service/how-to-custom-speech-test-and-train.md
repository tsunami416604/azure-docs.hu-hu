---
title: Adatok előkészítése egyéni beszédfelismeréshez – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A Microsoft beszédfelismerésének pontosságának tesztelése vagy az egyéni modellek betanítása során hang- és szöveges adatokra lesz szüksége. Ezen az oldalon bemutatjuk az adatok típusát, a felhasználásmódját és kezelését.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 78857709447f99895c36f23d8760f44f8468ba7c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402140"
---
# <a name="prepare-data-for-custom-speech"></a>Adatok előkészítése a Custom Speech szolgáltatáshoz

A Microsoft beszédfelismerésének pontosságának tesztelése vagy az egyéni modellek betanítása során hang- és szöveges adatokra lesz szüksége. Ezen az oldalon bemutatjuk az adatok típusát, a felhasználásmódját és kezelését.

## <a name="data-types"></a>Adattípusok

Ez a táblázat felsorolja az elfogadott adattípusokat, amikor az egyes adattípusokat kell használni, és az ajánlott mennyiséget. Nem minden adattípus szükséges a modell létrehozásához. Az adatkövetelmények attól függően változnak, hogy tesztet hoz létre vagy modellt próbál beképezni.

| Adattípus | Tesztelésre szolgál | Ajánlott mennyiség | Képzésre használt | Ajánlott mennyiség |
|-----------|-----------------|----------|-------------------|----------|
| [Hang](#audio-data-for-testing) | Igen<br>Szemrevételezéses vizsgálatra szolgál | 5+ hangfájlok | Nem | N/a |
| [Audio + Emberi címkével ellátott átiratok](#audio--human-labeled-transcript-data-for-testingtraining) | Igen<br>A pontosság értékelésére szolgál | 0,5-5 órányi hang | Igen | 1-1000 órányi hang |
| [Kapcsolódó szöveg](#related-text-data-for-training) | Nem | N/a | Igen | 1-200 MB kapcsolódó szöveg |

A fájlokat típus szerint kell egy adatkészletbe csoportosítani, és .zip fájlként kell feltölteni. Minden adatkészlet csak egyetlen adattípust tartalmazhat.

> [!TIP]
> A gyors kezdéshez fontolja meg a mintaadatok használatát. Tekintse meg ezt a GitHub-tárházat <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">az egyéni beszédadatok <span class="docon docon-navigate-external x-hidden-focus"></span> mintamegtekintéséhez</a>

## <a name="upload-data"></a>Adatok feltöltése

Az adatok feltöltéséhez keresse meg az <a href="https://speech.microsoft.com/customspeech" target="_blank">Egyéni beszédportált. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a> A portálon kattintson az **Adatok feltöltése gombra** a varázsló elindításához és az első adatkészlet létrehozásához. Az adatok feltöltése előtt ki kell választania az adatkészlet beszédadattípusát.

![Hang kiválasztása a Beszédportálról](./media/custom-speech/custom-speech-select-audio.png)

Minden feltöltött adatkészletnek meg kell felelnie a kiválasztott adattípus követelményeinek. Az adatokat a feltöltés előtt megfelelően kell formázni. A megfelelően formázott adatok biztosítják, hogy az egyéni beszédfelismerési szolgáltatás pontosan feldolgozza azokat. A követelmények a következő szakaszokban találhatók.

Az adatkészlet feltöltése után néhány lehetőség közül választhat:

* Navigálhat a **Tesztelés** lapra, és vizuálisan ellenőrizheti a csak hangvagy hang + emberi címkével ellátott átírási adatokat.
* Navigálhat a **Képzés** lapon, és audio + emberi átírási adatok vagy kapcsolódó szöveges adatok használatával betaníthatja az egyéni modellt.

## <a name="audio-data-for-testing"></a>Audioadatok teszteléshez

A hangadatok optimálisak a Microsoft eredeti beszéd-szöveg modellvagy egyéni modell pontosságának teszteléséhez. Ne feledje, hogy a hangadatok at egy adott modell teljesítményével kapcsolatos beszédpontosság vizsgálatára használjuk. Ha egy modell pontosságát szeretné számszerűsíteni, használjon [hang + emberi címkével ellátott transzkripciós adatokat.](#audio--human-labeled-transcript-data-for-testingtraining)

Ebben a táblázatban biztosíthatja, hogy a hangfájlok formázása megfelelő legyen az egyéni beszédfelismeréshez:

| Tulajdonság                 | Érték                 |
|--------------------------|-----------------------|
| Fájlformátum              | RIFF (WAV)            |
| Mintavételi arány              | 8000 Hz vagy 16 000 Hz |
| Csatornák                 | 1 (mono)              |
| Maximális hossz hangonként | 2 óra               |
| Mintaformátum            | PCM, 16 bites           |
| Archiválási formátum           | .zip                  |
| Az archívum maximális mérete     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Betanítási és tesztelési adatok feltöltésekor a .zip fájl mérete nem haladhatja meg a 2 GB-ot. Ha több adatra van szüksége a betanításhoz, ossza fel több .zip fájlra, és töltse fel őket külön-külön. Később több *adatkészletből* is betaníthatja a betanítást. Azonban csak *egyetlen* adatkészletből tesztelhető.

A <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX <span class="docon docon-navigate-external x-hidden-focus"></span> </a> segítségével ellenőrizheti a hang tulajdonságait, vagy konvertálhatja a meglévő hangokat a megfelelő formátumokba. Az alábbiakban néhány példa látható arra, hogy ezek a tevékenységek hogyan végezhetők el a SoX parancssoron keresztül:

| Tevékenység | Leírás | SoX parancs |
|----------|-------------|-------------|
| Hangformátum ellenőrzése | Ezzel a paranccsal ellenőrizheti<br>a hangfájl formátumát. | `sox --i <filename>` |
| Hangformátum konvertálása | Ezzel a paranccsal konvertálható<br>a hangfájlt egycsatornás, 16 bites, 16 KHz-es hangfájlba. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio + emberi címkével ellátott átirat adatok tesztelés / képzés

A Microsoft beszéd-szöveg pontosságának pontosságának méréséhez a hangfájlok feldolgozása során meg kell adnia az emberi címkével ellátott átiratokat (szóról szóra) az összehasonlításhoz. Míg az emberi címkével ellátott transzkripció gyakran időigényes, szükséges a pontosság kiértékelése és a modell betanítása a használati esetekhez. Ne feledje, hogy az elismerés javítása csak olyan jó lesz, mint a megadott adatok. Ezért fontos, hogy csak jó minőségű átiratokat töltsön fel.

| Tulajdonság                 | Érték                               |
|--------------------------|-------------------------------------|
| Fájlformátum              | RIFF (WAV)                          |
| Mintavételi arány              | 8000 Hz vagy 16 000 Hz               |
| Csatornák                 | 1 (mono)                            |
| Maximális hossz hangonként | 2 óra (tesztelés) / 60 s (képzés) |
| Mintaformátum            | PCM, 16 bites                         |
| Archiválási formátum           | .zip                                |
| Maximális cipzárméret         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Betanítási és tesztelési adatok feltöltésekor a .zip fájl mérete nem haladhatja meg a 2 GB-ot. Csak *egyetlen* adatkészletből tesztelheti, ügyeljen arra, hogy a megfelelő fájlméreten belül maradjon. Emellett minden betanítási fájl nem haladhatja meg a 60 másodpercet, különben hiba ki.

Az olyan problémák megoldásához, mint a szó törlése vagy helyettesítése, jelentős mennyiségű adatra van szükség a felismerés javításához. Általában, ez ajánlott, hogy szóról-szóra átiratok nagyjából 10-1000 órányi hang. Az összes WAV fájl átiratát egyetlen, egyszerű szöveges fájlnak kell tartalmaznia. Az átiratfájl minden sorának egy hangfájl nevét és az annak megfelelő átiratot kell tartalmaznia. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani.

  Például:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> Az átiratnak UTF-8 BOM (bájtsorrendjelzés) kódolásúnak kell lennie.

Az átiratokat a rendszer normalizálja, hogy fel tudja őket dolgozni. Vannak azonban fontos normalizálások, amelyeket el kell végezni, mielőtt feltöltené az adatokat a Beszédstúdióba. Az átiratok előkészítésekor a megfelelő nyelv ről a [Hogyan hozhat létre emberi címkével ellátott átiratot](how-to-custom-speech-human-labeled-transcriptions.md)

Miután összegyűjtötte a hangfájlokat és a megfelelő átiratokat, csomagolja be őket egyetlen .zip fájlként, mielőtt feltöltené őket az <a href="https://speech.microsoft.com/customspeech" target="_blank">Egyéni beszédportálra. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a> Az alábbiakban egy példa adatkészlet három audio fájlokat és egy emberi címkével transzkripciós fájl:

> [!div class="mx-imgBorder"]
> ![Hang kiválasztása a Beszédportálról](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Kapcsolódó szöveges adatok a képzéshez

A termékneveknek vagy az egyedi szolgáltatásoknak tartalmazniuk kell a kapcsolódó szöveges adatokat a betanításhoz. A kapcsolódó szöveg segít a helyes felismerésben. A felismerés javítása érdekében kétféle kapcsolódó szöveges adat adható meg:

| Adattípus | Hogyan javítják ezek az adatok a felismerést? |
|-----------|------------------------------------|
| Mondatok (kimondott szöveg) | A pontosság javítása a terméknevek vagy az iparág-specifikus szókincs felismerésekor egy mondat kontextusában. |
| Kiejtés | Nem gyakori kifejezések, rövidítések vagy más szavak kiejtésének javítása meghatározatlan kiejtéssel. |

A mondatok megadhatók egyetlen szöveges fájlként vagy több szövegfájlként. A pontosság javítása érdekében használjon szöveges adatokat, amelyek közelebb állnak a várt szóbeli kimondott szöveghez. A kiejtéseket egyetlen szöveges fájlként kell megadni. Mindent lehet csomagolni, mint egy zip fájlt, és feltöltötte a <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech portál <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Mondatfájl létrehozásának irányelvei

Ha egy egyéni modell mondatok használatával, meg kell adnia egy listát a minta utterances. Utterances _nem_ kell teljes vagy nyelvtanilag helyes, de pontosan tükröznie kell a kimondott bemeneti elvár éles környezetben. Ha azt szeretné, hogy bizonyos kifejezések súlya növekedjen, adjon hozzá több mondatot, amelyek tartalmazzák ezeket a konkrét kifejezéseket.

Általános iránymutatásként a modelladaptáció akkor a leghatékonyabb, ha a betanítási szöveg a lehető legközelebb áll a termelésben elvárt valós szöveghez. A tartományspecifikus zsargont és a javítani kívánt kifejezéseket fel kell tüntetni a betanítási szövegben. Ha lehetséges, próbáljon meg egy mondatot vagy kulcsszót külön sorban vezérelni. Az Ön számára fontos kulcsszavak és kifejezések (például terméknevek) esetében néhányszor másolhatja őket. De ne feledje, ne másolja túl sokat - ez hatással lehet a teljes elismerési arány.

Ebben a táblában biztosíthatja, hogy a kimondott szöveghez kapcsolódó adatfájl megfelelőformázással legyen formázva:

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | UTF-8 BOM |
| Kimondott szövegek száma soronként | 1 |
| Maximális fájlméret | 200 MB |

Ezenkívül a következő korlátozásokkal kell számolnia:

* Kerülje a négynél többszöri karakter ismétlését. Például: "aaaa" vagy "uuuu".
* Ne használjon speciális karaktereket vagy UTF-8 karaktert a fenti `U+00A1`.
* Az URI-k elutasításra kerülnek.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Kiejtési fájl létrehozásának irányelvei

Ha a felhasználók nem gyakori kifejezések et nem tartalmazó kifejezéseket használ, egyéni kiejtési fájlt adhat meg a felismerés javítása érdekében.

> [!IMPORTANT]
> Nem ajánlott egyéni kiejtési fájlokat használni a gyakori szavak kiejtésének megváltoztatásához.

Ez példákat tartalmaz a szóbeli utterance (kifejezés) és az egyéni kiejtés minden:

| Elismert/megjelenített űrlap | Szóbeli forma |
|--------------|--------------------------|
| 3CPO | három c p o |
| CNTK | c n t k |
| Ieee | én hármas e |

A szóbeli forma a fonetikus sorrendben leírva. Ez állhat levél, szavak, szótagjai, vagy kombinációja mindhárom.

A testreszabott kiejtés angol`en-US`nyelven (`de-DE`) és németül ( ). Ez a táblázat a támogatott karaktereket nyelv szerint jeleníti meg:

| Nyelv | Területi beállítás | Karakterek |
|----------|--------|------------|
| Angol | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Német | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Az alábbi táblázat segítségével győződjön meg arról, hogy a kiejtéshez kapcsolódó adatfájl megfelelően van formázva. Kiejtésfájlok kicsi, és csak néhány kilobájt méretű.

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | UTF-8 anyagjegyzék (AZ ANSI angol nyelven is támogatott) |
| # a kiejtések soronként | 1 |
| Maximális fájlméret | 1 MB (1 KB ingyenes szint) |

## <a name="next-steps"></a>További lépések

* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
* [Az adatok kiértékelése](how-to-custom-speech-evaluate-data.md)
* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
