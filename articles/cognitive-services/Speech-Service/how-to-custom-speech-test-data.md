---
title: Custom Speech beszédfelismerési szolgáltatás tesztelési célú feldolgozásának előkészítése
titleSuffix: Azure Cognitive Services
description: A Microsoft beszédfelismerés pontosságának tesztelésekor vagy egyéni modelljeinek betanításakor a hang-és szöveges adatokra lesz szüksége. Ezen az oldalon bemutatjuk az adattípusokat, a használatot és a felügyeletet.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 6100ac6a6b01a7d0eac74b0e83539bf4e671cb89
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660409"
---
# <a name="prepare-data-for-custom-speech"></a>Custom Speechi adatfeldolgozás előkészítése

A Microsoft beszédfelismerés pontosságának tesztelésekor vagy egyéni modelljeinek betanításakor a hang-és szöveges adatokra lesz szüksége. Ezen az oldalon bemutatjuk az adattípusokat, a használatot és a felügyeletet.

## <a name="data-types"></a>Adattípusok

Ez a táblázat felsorolja az elfogadott adattípusokat, valamint az egyes adattípusok használatát, valamint az ajánlott mennyiséget. Nem minden adattípus szükséges a modell létrehozásához. Az adatkövetelmények attól függően változnak, hogy szeretne-e tesztet létrehozni vagy egy modellt betanítani.

| Data type | Teszteléshez használatos | Ajánlott mennyiség | Képzéshez használatos | Ajánlott mennyiség |
|-----------|-----------------|----------|-------------------|----------|
| [Hang](#audio-data-for-testing) | Igen<br>Vizuális vizsgálathoz használatos | 5 + hangfájl | Nem | N/a |
| [Hang + emberi – címkézett átiratok](#audio--human-labeled-transcript-data-for-testingtraining) | Igen<br>A pontosság kiértékeléséhez használatos | 0,5-5 órányi hang | Igen | 1 – 1000 órányi hang |
| [Kapcsolódó szöveg](#related-text-data-for-training) | Nem | N/a | Igen | 1-200 MB kapcsolódó szöveg |

A fájlokat típus szerint kell csoportosítani egy adatkészletbe, és. zip fájlként kell feltölteni. Az egyes adatkészletek csak egyetlen adattípust tartalmazhatnak.

> [!TIP]
> A gyors kezdéshez vegye fontolóra a mintaadatok használatát. Tekintse meg a GitHub-tárházat a <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">minta Custom Speech <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

## <a name="upload-data"></a>Adatok feltöltése

Az adatok feltöltéséhez navigáljon a <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech portálra <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. A portálon kattintson az **adatok feltöltése** elemre a varázsló indításához és az első adatkészlet létrehozásához. Az adatok feltöltésének engedélyezése előtt meg kell adnia, hogy ki kell választania egy beszédfelismerési adattípust az adatkészlethez.

![Hang kiválasztása a Speech Portalon](./media/custom-speech/custom-speech-select-audio.png)

A feltöltött adatkészleteknek meg kell felelniük a választott adattípusra vonatkozó követelményeknek. Az adatait a feltöltés előtt helyesen kell formázni. A helyesen formázott adat biztosítja, hogy a Custom Speech szolgáltatás pontosan dolgozza fel azokat. A követelmények a következő részekben találhatók.

Az adatkészlet feltöltése után néhány lehetőség közül választhat:

* Navigáljon a **Testing (tesztelés** ) lapra, és vizuálisan vizsgálja meg a csak hang-és hang-és emberi-címkézett átírási adatok.
* Az egyéni modell betanításához navigáljon a **betanítás** lapra, és használja a hang + emberi átírási adatok vagy a kapcsolódó szöveges adatok használatát.

## <a name="audio-data-for-testing"></a>Hangadatok teszteléshez

A hangadatok optimálisan tesztelik a Microsoft alapvető beszédfelismerési modelljét vagy egyéni modelljét. Ne feledje, hogy a hangadatok segítségével megvizsgálhatja a beszéd pontosságát egy adott modell teljesítményének tekintetében. Ha számszerűsíteni szeretné a modell pontosságát, használja a [hang + emberi feliratú átírási adatok](#audio--human-labeled-transcript-data-for-testingtraining)lehetőséget.

Ezzel a táblázattal ellenőrizheti, hogy a hangfájlok formátuma helyesen van-e formázva a Custom Speech használatával:

| Tulajdonság | Value (Díj) |
|----------|-------|
| Fájl formátuma | RIFF (WAV) |
| Mintavételezési arány | 8 000 Hz vagy 16 000 Hz |
| Csatornák | 1 (mono) |
| Maximális hossz/hang | 2 óra |
| Minta formátuma | PCM, 16 bites |
| Archív formátum | .zip |
| Archiválás maximális mérete | 2 GB |

> [!TIP]
> A képzés és a tesztelési célú adatfeldolgozás során a. zip fájl mérete nem haladhatja meg a 2 GB-ot. Ha további adatokra van szüksége a betanításhoz, Ossza szét azt több. zip-fájlba, és töltse fel őket külön. Később is dönthet úgy, hogy *több* adatkészletből is betanít. Azonban csak *egyetlen* adatkészletből lehet tesztelni.

A <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">Sox <span class="docon docon-navigate-external x-hidden-focus"></span> </a> használatával ellenőrizze a hangtulajdonságokat, vagy alakítsa át a meglévő hangokat a megfelelő formátumokba. Az alábbiakban néhány példát láthat arra, hogyan végezheti el ezeket a tevékenységeket a SoX parancssorban:

| Tevékenység | Leírás | SoX-parancs |
|----------|-------------|-------------|
| Hangformátum keresése | Ezzel a paranccsal ellenőrizhető<br>a hangfájl formátuma. | `sox --i <filename>` |
| Hangformátum konvertálása | A parancs használata a konvertáláshoz<br>a hangfájl egyetlen csatornára, 16 bites, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Hang + emberi-feliratú átirati adatok tesztelési/betanítási célokra

Ha a Microsoft beszéd-szöveg pontosságát szeretné mérni a hangfájlok feldolgozásakor, az összehasonlításhoz meg kell adnia az emberi címkével ellátott átírásokat (Word-by-Word). Míg az emberi címkével ellátott átírás gyakran időigényes, a pontosság kiértékelése és a modell betanítása szükséges a használati esetekhez. Ne feledje, hogy az elismerés fejlesztése csak a megadott adatszolgáltatások esetében lesz megfelelő. Ezért fontos, hogy csak a kiváló minőségű átiratok legyenek feltöltve.

| Tulajdonság | Value (Díj) |
|----------|-------|
| Fájl formátuma | RIFF (WAV) |
| Mintavételezési arány | 8 000 Hz vagy 16 000 Hz |
| Csatornák | 1 (mono) |
| Maximális hossz/hang | 2 óra (tesztelés)/60 s (képzés) |
| Minta formátuma | PCM, 16 bites |
| Archív formátum | .zip |
| Maximális zip-méret | 2 GB |

> [!NOTE]
> A képzés és a tesztelési célú adatfeldolgozás során a. zip fájl mérete nem haladhatja meg a 2 GB-ot. A uou csak *egyetlen* adatkészletből tesztelhető, ügyeljen rá, hogy a megfelelő fájlméreten belül maradjon.

A Word törlési vagy helyettesítési problémák megoldásához jelentős mennyiségű adattal kell foglalkoznia az elismerés javítása érdekében. Általánosságban elmondható, hogy nagyjából 10 – 1 000 órányi hanganyagot biztosít a Word-by-Word típusú átírásoknak. Az összes WAV fájl átiratát egyetlen, egyszerű szöveges fájlnak kell tartalmaznia. Az átiratfájl minden sorának egy hangfájl nevét és az annak megfelelő átiratot kell tartalmaznia. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani.

  Példa:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> Az átiratnak UTF-8 BOM (bájtsorrendjelzés) kódolásúnak kell lennie.

Az átiratokat a rendszer normalizálja, hogy fel tudja őket dolgozni. Van azonban néhány fontos normalizálás, amelyeket el kell végezni, mielőtt feltölti az adatsort a Speech studióba. Az átírások előkészítésekor használandó megfelelő nyelvet lásd: [Human-címkével ellátott átirat létrehozása](how-to-custom-speech-human-labeled-transcriptions.md)

A hangfájlok és a megfelelő átírások összegyűjtése után egyetlen. zip fájlként csomagolja őket, mielőtt feltölti őket a <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech <span class="docon docon-navigate-external x-hidden-focus"> </span>-portálra </a>. Az alábbi példa egy három hangfájllal és egy emberi címkével ellátott átírási fájllal rendelkező adathalmazt mutat be:

> [!div class="mx-imgBorder"]
> ![válassza a hang lehetőséget a Speech Portalon](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Kapcsolódó szöveges adat a betanításhoz

Az egyedi terméknév vagy szolgáltatások esetében a betanításhoz kapcsolódó szöveges adatnak kell szerepelnie. A kapcsolódó szövegek segítenek biztosítani a helyes felismerést. A következő két típusú kapcsolódó szöveges adat adható meg az elismerés javítása érdekében:

| Data type | Az adatfelismerés javítása |
|-----------|------------------------------------|
| Mondatok (hosszúságú kimondott szöveg) | A termékek nevének felismerése, illetve az iparágra jellemző Szószedet használata a mondatok kontextusában. |
| Kiejtés | A nem definiált kiejtésekkel kapcsolatos gyakori kifejezések, mozaikszavak és egyéb szavak kiejtésének javítása. |

A mondatok egyetlen szövegfájlként vagy több szövegfájlként is megadhatók. A pontosság javítása érdekében a várt hosszúságú kimondott szöveg közelebbi szöveges adatok használatával. A kiejtéseket egyetlen szövegfájlként kell megadni. Minden egyes zip-fájlként becsomagolható, és a <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech portálra <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>tölthető fel.

### <a name="guidelines-to-create-a-sentences-file"></a>Irányelvek a mondatok létrehozásához

Egyéni modell mondatok használatával történő létrehozásához meg kell adnia a minta hosszúságú kimondott szöveg listáját. A hosszúságú kimondott szöveg _nem_ kell teljesnek vagy programozottan helyesnek lenniük, de pontosan tükröznie kell az éles környezetben várhatóan megjelenő adatokat. Ha azt szeretné, hogy bizonyos kifejezések nagyobb súlyt adjanak, adjon hozzá több mondatot is, amelyek tartalmazzák ezeket az adott feltételeket.

Általános útmutatásként a modellhez való alkalmazkodás a leghatékonyabb, ha a betanítási szöveg a lehető leghamarabb az éles környezetben várt valós szöveghez van lezárva. A fejlesztésre célzott, tartományra jellemző zsargont és kifejezéseket a betanítási szövegbe kell foglalni. Ha lehetséges, próbáljon meg egy mondatot vagy kulcsszót külön sorban vezérelni. Az Ön számára fontos kulcsszavakat és kifejezéseket (például a terméknévokat) néhány alkalommal másolhatja. Ne feledje azonban, hogy ne másoljon túl sokat – ez befolyásolhatja a teljes felismerési arányt.

Ezzel a táblázattal ellenőrizheti, hogy a hosszúságú kimondott szöveg kapcsolódó adatfájlja helyesen van-e formázva:

| Tulajdonság | Value (Díj) |
|----------|-------|
| Szövegkódolás | UTF-8 BOM |
| Kimondott szövegek száma soronként | 1 |
| Maximális fájlméret | 200 MB |

Emellett a következő korlátozásokat is érdemes figyelembe vennie:

* Ne ismételje meg a karaktereket négyszer. Például: "AAAA" vagy "uuuu".
* Ne használjon speciális karaktereket vagy UTF-8 karaktert a `U+00A1`fölé.
* Az URI-k el lesznek utasítva.

### <a name="guidelines-to-create-a-pronunciation-file"></a>A kiejtési fájl létrehozásához szükséges irányelvek

Ha nem általános feltételek vannak a felhasználók által tapasztalt vagy használt általános kiejtés nélkül, megadhat egy egyéni kiejtési fájlt, amely javítja az elismerést.

> [!IMPORTANT]
> Nem ajánlott egyéni kiejtési fájlokat használni a gyakori szavak kiejtésének módosításához.

Ide tartoznak a kimondott kifejezésekre vonatkozó példák, valamint a következő egyéni kiejtések:

| Felismert/megjelenített űrlap | Szóbeli űrlap |
|--------------|--------------------------|
| 3CPO | három c p o |
| CNTK | c n t k |
| IEEE | i Triple e |

A kimondott űrlap a fonetikus sorszám. Betűből, szóból, szótagból vagy mindhárom kombinációból állhat.

A testreszabott kiejtés angol (`en-US`) és német (`de-DE`) nyelven érhető el. Ez a táblázat a támogatott karaktereket mutatja nyelv szerint:

| Nyelv | Területi beállítás | Karaktereket |
|----------|--------|------------|
| Angol | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Német | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

A következő táblázat segítségével biztosíthatja, hogy a kiejtésekhez kapcsolódó adatfájl megfelelően legyen formázva. A kiejtési fájlok kicsik, és csak néhány kilobájt méretűek lehetnek.

| Tulajdonság | Value (Díj) |
|----------|-------|
| Szövegkódolás | UTF-8 AJ (ANSI is támogatott angol nyelven) |
| kiejtések száma soronként | 1 |
| Maximális fájlméret | 1 MB (1 KB ingyenes szinten) |

## <a name="next-steps"></a>Következő lépések

* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
* [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
