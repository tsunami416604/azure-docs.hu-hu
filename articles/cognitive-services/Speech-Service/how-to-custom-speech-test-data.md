---
title: Egyéni beszédfelismerés – beszédszolgáltatások a Tesztadatok előkészítéséhez
titlesuffix: Azure Cognitive Services
description: Tesztel, hogy hogyan pontos Microsoft beszédfelismerés megtekintéséhez vagy saját modellek betanítása, kell adatokat (a hang-és/vagy szöveg formájában). Ezen a lapon bemutatjuk, milyen típusú adatokat, azok használata és kezelésük módjával.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 1e689d7ce65fda43e5657383ed44890c90c095cd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025883"
---
# <a name="prepare-data-for-custom-speech"></a>Adatok előkészítése az egyéni beszéd

Tesztel, hogy hogyan pontos Microsoft beszédfelismerés megtekintéséhez vagy hang- és szöveg formájában adatokat kell a saját modellek betanítása. Ezen a lapon bemutatjuk, milyen típusú adatokat, azok használata és kezelésük módjával.

## <a name="data-types"></a>Adattípusok

Ez a táblázat felsorolja az elfogadott adattípusok, ha adatokat kell használni, és az ajánlott mennyiség. Nem minden adattípus egy modell létrehozásához szükséges. Adatok változhatnak attól függően, hogy egy teszt létrehozásakor és a modell tanítása.

| Adattípus | A tesztelési használt | Mennyiség | Használt képzéshez | Mennyiség |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Igen<br>Használt vizuális ellenőrzése | 5 + hangfájlok | Nem | N/a |
| [Hang + emberi címkével szövegekben](#audio--human-labeled-transcript-data-for-testingtraining) | Igen<br>Pontosság értékeli ki, hogy | 0,5 -, hang 5 óra | Igen | 1 - audio 1000 órányi |
| [Kapcsolódó szöveg](##related-text-data-for-training) | Nem | N/a | Igen | 1 – 200 MB kapcsolódó szöveg |

Fájlok kell egy adatkészletbe típusa szerint csoportosítva és feltöltött zip-fájlként. Egyes adatkészletek csak egyetlen adattípushoz tartalmazhat.

## <a name="upload-data"></a>Adatok feltöltése

Amikor készen áll az adatok feltöltéséhez, kattintson az **adatfeltöltés** indítsa el a varázslót, és az első adatkészlet létrehozásához. A rendszer felszólítja az adatkészlet egy beszéd adattípus kiválasztása lehetővé teszi az adatok feltöltése előtt.

![Válassza ki a hangot a Speech portálról](./media/custom-speech/custom-speech-select-audio.png)

Minden egyes feltöltött adatkészlet meg kell felelnie az Ön által választott adattípus követelményeinek. Fontos megfelelően formázza az adatokat, mielőtt fel lesz töltve. Ez biztosítja, hogy az adatok pontos általi feldolgozásának a Custom Speech service. A következő szakaszokban felsorolt követelmények.

Miután feltöltötte az adatkészlet, több lehetőség közül választhat:

* Navigálhat a **tesztelés** lapra, és vizuálisan megvizsgálják a csak az audio- és audio- és emberi címkével beszédátírási adatokat.
* Navigálhat a **képzési** lap és USA hang + emberi beszédátírási adatok vagy a kapcsolódó szöveges adatok egy egyéni modell betanításához.

## <a name="audio-data-for-testing"></a>Tesztelési hívásaiból

Hang adata optimális a Microsoft baseline hang-szöveg transzformációs modell vagy egy egyéni modell pontosságának ellenőrzésére. Tartsa szem előtt, hívásaiból vizsgálhatja meg a beszédfelismerés egy adott modell teljesítményének tartományállapot pontosságát szolgál. Ha a modell pontosságát számszerűsítése keres, használja a [hang + emberi címkével beszédátírási adatok](#audio--human-labeled-transcript-data-for-testingtraining).

Ez a táblázat segítségével győződjön meg arról, hogy a hangfájlok a segítségével egyéni beszédfelismerési megfelelően formázott:

| Tulajdonság | Érték |
|----------|-------|
| Fájlformátum | RIFF (WAV) |
| Mintavételi frekvencia | 8000 Hz vagy 16 000 Hz |
| Csatornák | 1 (mono) |
| Hang maximális hossza | 2 óra |
| Minta formátumával | A PCM, 16-bit |
| Archív formátum | .zip |
| Maximális archívum mérete | 2 GB |

Ha a hang nem elégíti ki ezeket a tulajdonságokat, vagy ellenőrizze, hogy ha nem szeretné, javasoljuk, hogy letöltése [sox](http://sox.sourceforge.net) ellenőrzéséhez vagy a hanganyag konvertálni. Az alábbiakban néhány példát, hogyan ezeket a tevékenységeket mindegyike végezhető el a parancssorban:

| Tevékenység | Leírás | SOx parancs |
|----------|-------------|-------------|
| Formát zvuku ellenőrzése | Ez a parancs használatával ellenőrizze a hangfájl formátuma. | `soxi <filename>.wav` |
| Formát zvuku konvertálása | Használja ezt a parancsot a hangfájl átalakítása egy csatornát, 16 bites, 48 KHz. | `sox <filename>.wav -b 16 -3 signed-integer -c l -r 48k -t wav <filename>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Hang + emberi címkével szöveges adatok tesztelés/képzés

Azoknak a Microsoft hang-szöveg transzformációs pontossága pontosságának, a zenei fájlok feldolgozása során, meg kell adnia az emberi címkével beszédátírás (a word-szavanként) az összehasonlítása. Noha az emberi címkével beszédátírási gyakran időigényes, szükség kiértékelni a pontosság és a használati esetek a modell betanításához. Tartsa szem előtt, felismerés fejlesztések csak akkor olyan jól, mint a megadott adatokat. Éppen ezért fontos, hogy csak a magas színvonalú szövegekben lesznek feltöltve.  

| Tulajdonság | Érték |
|----------|-------|
| Fájlformátum | RIFF (WAV) |
| Mintavételi frekvencia | 8000 Hz vagy 16 000 Hz |
| Csatornák | 1 (mono) |
| Hang maximális hossza | 60 s |
| Minta formátumával | A PCM, 16-bit |
| Archív formátum | .zip |
| Zip maximális mérete | 2 GB |

Problémák elhárításához, mint a word törlése vagy helyettesítő, jelentős mennyiségű adat van szükség a felismerés javítása. Általában azt javasoljuk, hogy adja meg a word-word beszédátírás hang nagyjából 10, 1000 órányi. Az összes WAV fájl átiratát egyetlen, egyszerű szöveges fájlnak kell tartalmaznia. Az átiratfájl minden sorának egy hangfájl nevét és az annak megfelelő átiratot kell tartalmaznia. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani.

  Példa:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Az átiratnak UTF-8 BOM (bájtsorrendjelzés) kódolásúnak kell lennie.

Az átiratokat a rendszer normalizálja, hogy fel tudja őket dolgozni. Bizonyos fontosabb normalizálási tevékenységeket azonban a felhasználónak kell végrehajtania az adatok a Custom Speech Service szolgáltatásba való feltöltése _előtt_. A megfelelő nyelvet, amikor előkészíti a beszédátírás használatára, lásd: [egy emberi címkével beszédátírási létrehozása](how-to-custom-speech-human-labeled-transcriptions.md)

Összegyűjtött a zenei fájlok és a megfelelő beszédátírás, miután azok kell csomagolható egyetlen .zip-fájlként előtt a Custom Speech-portálra való feltöltését. Ez a három hangfájlok és a egy emberi címkével beszédátírási fájl egy példa adatkészlet:

![Válassza ki a hangot a Speech portálról](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Kapcsolódó szöveges adatok képzéshez

Ha termékneveket vagy egyedi funkciókat, és azt szeretné, hogy helyesen ismeri, fontos képzéshez kapcsolódó szöveges adatokat. Kapcsolódó szöveges adatok kétféle felismerés javításának adható meg:

| Adattípus | Hogyan növeli az ezeket az adatokat a felismerés |
|-----------|------------------------------------|
| Beszédmódok, illetve mondatok | Ezek is javítják a pontossága FELISMERVE terméknevekhez vagy iparág-specifikus szószedet mondatok kontextusában. |
| Kiejtés | Ezek nem szokványos feltételek, betűszavakat vagy más szavakat nem definiált kiejtés írásmódja javíthatja. |

Beszédmódok is meg kell adni egy vagy több szöveges fájlok. Minél közelebb a szöveges adatokat, hogy mi lesz beszélt, annál nagyobb a valószínűségét, hogy a pontosság növelése. Egyetlen szöveges fájlból kiejtés meg kell adni. Minden egyetlen zip-fájlként csomagolva és feltöltött a Custom Speech-portál.

### <a name="guidelines-to-create-an-utterances-file"></a>A szabályokat, utterances fájl létrehozása

Szeretne létrehozni egy egyéni modell használatával kapcsolatos szöveget, kell adhatja meg, a minta kimondott szöveg. Ezeket a kimondott szöveg teljes mondatokból vagy nyelvtanilag javítsa nem szükséges, de kell pontosan tükrözik a kimondott bemeneti várt éles környezetben. Ha azt szeretné, hogy bizonyos feltételek súly növekedett, a kapcsolódó adatfájlt, amely tartalmazza ezeket a feltételeket több mondatok is hozzáadhat.

Ez a táblázat segítségével győződjön meg arról, hogy a kapcsolódó adatfájlt kimondott szöveg formátuma helytelen:

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | UTF-8 BOM |
| Kimondott szövegek száma soronként | 1 |
| Maximális fájlméret | 200 MB |

Emellett érdemes figyelembe a következő korlátozásokkal:

* Kerülje a ismétlődő karaktereket tartalmaz a több mint négy alkalommal. Például: "aaaa" vagy "uuuu".
* Ne használja a speciális karakterek vagy UTF-8 karakter U + 00A1 felett.
* URI-k a rendszer elutasítja.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Irányelvek írásmódja fájl létrehozása

Standard kiejtés, a felhasználók lép fel vagy használata nélkül nem szokványos feltételek esetén megadhat egy egyéni írásmódja fájl felismerés javítása érdekében.

> [!IMPORTANT]
> A funkció használatához a gyakori szavakat írásmódja módosítása nem ajánlott.

Az egyes példák a kimondott utterance (kifejezés) és a egy egyéni kiejtés Ez tartalmazza:

| Használja a beszélt űrlap | Felismert/megjelenített képernyő |
|--------------|--------------------------|
| három c p o | 3CPO |  
| c n t k | CNTK |
| i háromszoros e | IEE |

A beszédből kinyert képernyő az a betűvel fonetikus során. Betűvel, szavak, szótagokat határoznak vagy mindhárom kombinációja állhat.

Testre szabott írásmódja (en-US) angol és német (de-DE) érhető el. Ez a táblázat bemutatja a támogatott karakterek nyelv szerint:

| Nyelv | Területi beállítás | Karakterek |
|----------|--------|------------|
| Angol | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| német | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Ez a táblázat segítségével győződjön meg arról, hogy a kapcsolódó adatfájlt kiejtés megfelelően van formázva. Írásmódja fájlok kicsik, és nem lehet hosszabb néhány Tudásbázis.

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | UTF-8 Anyagjegyzék (ANSI is támogatott angol nyelven) |
| minden sorában kiejtés száma | 1 |
| Maximális fájlméret | 1 MB (ingyenes szint 1 KB) |

## <a name="next-steps"></a>További lépések

* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
* [Az adatok](how-to-custom-speech-evaluate-data.md)
* [A modell tanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
