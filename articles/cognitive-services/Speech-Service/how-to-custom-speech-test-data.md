---
title: Custom Speech beszédfelismerési szolgáltatás tesztelési célú feldolgozásának előkészítése
titleSuffix: Azure Cognitive Services
description: Függetlenül attól, hogy a Microsoft beszédfelismerés pontos felismerése vagy a saját modelljeinek betanítása milyen módon történik, szüksége lesz az adatokra (hang és/vagy szöveg formájában). Ezen az oldalon bemutatjuk az adattípusokat, azok használatát, valamint a kezelésük módját.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: b18e1b755b4e1339bf00380d8228fc28e355d3e1
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802517"
---
# <a name="prepare-data-for-custom-speech"></a>Custom Speechi adatfeldolgozás előkészítése

Függetlenül attól, hogy a Microsoft beszédfelismerés pontos felismerése vagy a saját modelljeinek betanítása milyen módon történik, hang és szöveg formájában kell megadnia az adatokhoz. Ezen az oldalon bemutatjuk az adattípusokat, azok használatát, valamint a kezelésük módját.

## <a name="data-types"></a>Adattípusok

Ez a táblázat felsorolja az elfogadott adattípusokat, valamint az egyes adattípusok használatát, valamint az ajánlott mennyiséget. Nem minden adattípus szükséges a modell létrehozásához. Az adatkövetelmények attól függően változnak, hogy szeretne-e tesztet létrehozni vagy egy modellt betanítani.

| Adattípus | Tesztelési módszer | Mennyiség | Képzéshez használatos | Mennyiség |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Igen<br>Vizuális vizsgálathoz használatos | 5 + hangfájl | Nem | N/a |
| [Hang + emberi – címkézett átiratok](#audio--human-labeled-transcript-data-for-testingtraining) | Igen<br>A pontosság kiértékeléséhez használatos | 0,5 – 5 órányi hang | Igen | 1 – 1 000 órányi hang |
| [Kapcsolódó szöveg](#related-text-data-for-training) | Nem | N/a | Igen | 1-200 MB kapcsolódó szöveg |

A fájlokat típus szerint kell csoportosítani egy adatkészletbe, és zip-fájlként kell feltölteni. Az egyes adatkészletek csak egyetlen adattípust tartalmazhatnak.

## <a name="upload-data"></a>Adatok feltöltése

Amikor készen áll az adatok feltöltésére, navigáljon a [Custom Speech portálra](https://speech.microsoft.com/customspeech), majd kattintson az **adatok feltöltése** elemre a varázsló indításához és az első adatkészlet létrehozásához. Az adatok feltöltésének engedélyezése előtt meg kell adnia, hogy ki kell választania egy beszédfelismerési adattípust az adatkészlethez.

![Hang kiválasztása a Speech Portalon](./media/custom-speech/custom-speech-select-audio.png)

A feltöltött adatkészleteknek meg kell felelniük a választott adattípusra vonatkozó követelményeknek. Fontos, hogy a feltöltés előtt megfelelően formázza az adatait. Ez biztosítja, hogy az Custom Speech szolgáltatás pontosan dolgozza fel az adatfeldolgozást. A követelmények a következő részekben találhatók.

Az adatkészlet feltöltése után néhány lehetőség közül választhat:

* Navigáljon a **Testing (tesztelés** ) lapra, és vizuálisan vizsgálja meg a csak hang-és hang-és emberi-címkézett átírási adatok.
* Az egyéni modell betanításához navigáljon a **betanítás** lapra, és használja a hang + emberi átírási adatok vagy a kapcsolódó szöveges adatok használatát.

## <a name="audio-data-for-testing"></a>Hangadatok teszteléshez

A hangadatok optimálisan tesztelik a Microsoft alapvető beszédfelismerési modelljét vagy egyéni modelljét. Ne feledje, hogy a hangadatok segítségével megvizsgálhatja a beszéd pontosságát egy adott modell teljesítményének tekintetében. Ha számszerűsíteni szeretné a modell pontosságát, használja a [hang + emberi feliratú átírási adatok](#audio--human-labeled-transcript-data-for-testingtraining)lehetőséget.

Ezzel a táblázattal ellenőrizheti, hogy a hangfájlok formátuma helyesen van-e formázva a Custom Speech használatával:

| Tulajdonság | Value |
|----------|-------|
| Fájl formátuma | RIFF (WAV) |
| Mintavételi frekvencia | 8 000 Hz vagy 16 000 Hz |
| Csatornák | 1 (mono) |
| Maximális hossz/hang | 2 óra |
| Minta formátuma | PCM, 16 bites |
| Archív formátum | .zip |
| Archiválás maximális mérete | 2 GB |

Ha a hang nem elégíti ki ezeket a tulajdonságokat, vagy ha igen, akkor javasoljuk, hogy a [rendszer a hanganyagot a letöltéshez](http://sox.sourceforge.net) . Az alábbiakban néhány példát láthat arra, hogyan végezheti el ezeket a tevékenységeket a parancssorból:

| Tevékenység | Leírás | SOx-parancs |
|----------|-------------|-------------|
| Hangformátum keresése | Ezzel a paranccsal ellenőrizhető a hangfájl formátuma. | `sox --i <filename>` |
| Hangformátum konvertálása | Ezzel a paranccsal a hangfájlt egyetlen csatornára, 16 bites, 16 KHz-re konvertálhatja. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Hang + emberi-feliratú átirati adatok tesztelési/betanítási célokra

Ha a Microsoft beszéd-szöveg pontosságát szeretné mérni a hangfájlok feldolgozásakor, az összehasonlításhoz meg kell adnia az emberi címkével ellátott átírásokat (Word-by-Word). Míg az emberi címkével ellátott átírás gyakran időigényes, a pontosság kiértékelése és a modell betanítása szükséges a használati esetekhez. Ne feledje, hogy az elismerés fejlesztése csak a megadott adatszolgáltatások esetében lesz megfelelő. Ezért fontos, hogy csak a kiváló minőségű átiratok legyenek feltöltve.  

| Tulajdonság | Value |
|----------|-------|
| Fájl formátuma | RIFF (WAV) |
| Mintavételi frekvencia | 8 000 Hz vagy 16 000 Hz |
| Csatornák | 1 (mono) |
| Maximális hossz/hang | 60 s |
| Minta formátuma | PCM, 16 bites |
| Archív formátum | .zip |
| Maximális zip-méret | 2 GB |

A Word törlési vagy helyettesítési problémák megoldásához jelentős mennyiségű adattal kell foglalkoznia az elismerés javítása érdekében. Általánosságban elmondható, hogy nagyjából 10 – 1 000 órányi hanganyagot biztosít a Word-by-Word típusú átírásoknak. Az összes WAV fájl átiratát egyetlen, egyszerű szöveges fájlnak kell tartalmaznia. Az átiratfájl minden sorának egy hangfájl nevét és az annak megfelelő átiratot kell tartalmaznia. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani.

  Példa:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Az átiratnak UTF-8 BOM (bájtsorrendjelzés) kódolásúnak kell lennie.

Az átiratokat a rendszer normalizálja, hogy fel tudja őket dolgozni. Bizonyos fontosabb normalizálási tevékenységeket azonban a felhasználónak kell végrehajtania az adatok a Custom Speech Service szolgáltatásba való feltöltése _előtt_. Az átírások előkészítésekor használandó megfelelő nyelvet lásd: [Human-címkével ellátott átirat létrehozása](how-to-custom-speech-human-labeled-transcriptions.md)

A hangfájlok és a megfelelő átírások gyűjtése után egyetlen. zip-fájlként kell őket becsomagolni a [Custom Speech portálra](https://speech.microsoft.com/customspeech)való feltöltés előtt. Ez egy példaként szolgáló adatkészlet három hangfájllal és egy emberi címkével ellátott átírási fájllal:

![Hang kiválasztása a Speech Portalon](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Kapcsolódó szöveges adat a betanításhoz

Ha olyan terméknév vagy szolgáltatás van, amely egyedi, és szeretné meggyőződni arról, hogy helyesen vannak-e felismerhetőek, fontos, hogy a kapcsolódó szöveges információkat is tartalmazza a betanításhoz. A következő két típusú kapcsolódó szöveges adat adható meg az elismerés javítása érdekében:

| Adattípus | Az adatfelismerés javítása |
|-----------|------------------------------------|
| Hosszúságú kimondott szöveg és/vagy mondatok | Ezek a termékek neveinak felismerése, illetve az iparágra jellemző Szószedet a mondatok kontextusában javíthatják a pontosságot. |
| Kiejtés | Ezek a meghatározatlan kifejezések, mozaikszavak vagy más szavak kiejtését javítják a nem definiált kiejtésekkel. |

A hosszúságú kimondott szöveg egyetlen vagy több szövegfájlként is megadhatók. Minél közelebb van a szöveges adatokhoz, hogy mi lesz a beszéd, annál nagyobb a valószínűsége annak, hogy javul a pontosság. A kiejtéseket egyetlen szövegfájlként kell megadni. Minden egyes zip-fájlként becsomagolható, és a [Custom Speech portálra](https://speech.microsoft.com/customspeech)tölthető fel.

### <a name="guidelines-to-create-an-utterances-file"></a>Útmutató hosszúságú kimondott szöveg-fájl létrehozásához

Ha kapcsolódó szöveggel szeretne egyéni modellt létrehozni, meg kell adnia a minta hosszúságú kimondott szöveg listáját. Ezeknek a hosszúságú kimondott szöveg nem kell teljes mondatot vagy nyelvtanilag helyesnek lenniük, de pontosan tükröznie kell az éles környezetben várhatóan megjelenő adatokat. Ha azt szeretné, hogy bizonyos kifejezések nagyobb súlyt adjanak, több mondatot is hozzáadhat a kapcsolódó adatfájlhoz, amely tartalmazza ezeket az adott feltételeket.

Ezzel a táblázattal ellenőrizheti, hogy a hosszúságú kimondott szöveg kapcsolódó adatfájlja helyesen van-e formázva:

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | UTF-8 BOM |
| Kimondott szövegek száma soronként | 1 |
| Maximális fájlméret | 200 MB |

Emellett a következő korlátozásokat is érdemes figyelembe vennie:

* Ne ismételje meg a karaktereket négyszer. Például: "AAAA" vagy "uuuu".
* Ne használjon speciális karaktereket és UTF-8 karaktereket az U + 00A1 fölé.
* Az URI-k el lesznek utasítva.

### <a name="guidelines-to-create-a-pronunciation-file"></a>A kiejtési fájl létrehozásához szükséges irányelvek

Ha nem általános feltételek vannak a felhasználók által tapasztalt vagy használt általános kiejtés nélkül, megadhat egy egyéni kiejtési fájlt, amely javítja az elismerést.

> [!IMPORTANT]
> A szolgáltatás használata nem ajánlott a gyakori szavak kiejtésének megváltoztatására.

Ide tartoznak a kimondott kifejezésekre vonatkozó példák, valamint a következő egyéni kiejtések:

| Felismert/megjelenített űrlap | Használja a beszélt űrlap |
|--------------|--------------------------|
| 3CPO | három c p o |  
| CNTK | c n t k |
| IEEE | i Triple e |

A kimondott űrlap a fonetikus sorszám. Betűből, szóból, szótagból vagy mindhárom kombinációból állhat.

A testreszabott kiejtés angol (en-US) és német (de-DE) nyelven érhető el. Ez a táblázat a támogatott karaktereket mutatja nyelv szerint:

| Nyelv | Területi beállítás | Karakterek |
|----------|--------|------------|
| Angol | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| német | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Ezzel a táblázattal ellenőrizheti, hogy a kiejtésekhez kapcsolódó adatfájl megfelelően van-e formázva. A kiejtési fájlok kicsik, és nem léphetnek túl néhány Tudásbázis.

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | UTF-8 AJ (ANSI is támogatott angol nyelven) |
| kiejtések száma soronként | 1 |
| Maximális fájlméret | 1 MB (1 KB ingyenes szinten) |

## <a name="next-steps"></a>További lépések

* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
* [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
