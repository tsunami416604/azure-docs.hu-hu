---
title: Az adatok előkészítése az egyéni hanghoz – beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Hozzon létre egy egyéni hangot a márkához a Beszédszolgáltatással. Adja meg a stúdió felvételeket és a kapcsolódó parancsfájlok, a szolgáltatás létrehoz egy egyedi hangmodell hangolt a rögzített hang. Ezzel a hanggal szintetizálhatja a beszédeket a termékeiben, eszközeiben és alkalmazásaiban.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 5427e9f996fb77d455aa8064fc7cb1c65e1fcf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805977"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Adatok előkészítése egyéni hang létrehozásához

Ha készen áll arra, hogy egyéni szövegfelolvasó hangot hozzon létre az alkalmazáshoz, az első lépés a hangfelvételek és a kapcsolódó parancsfájlok összegyűjtése a hangmodell betanításának megkezdéséhez. A beszédszolgáltatás ezeket az adatokat arra használja, hogy egyedi hangot hozzon létre, amely megfelel a felvételek hangjának. Miután betanította a hangot, elkezdheti szintetizálni a beszédeket az alkalmazásokban.

Kezdheti egy kis mennyiségű adattal, hogy hozzon létre egy igazolást a koncepció. Azonban minél több adatot ad meg, annál természetesebb lesz az egyéni hangja. A saját szövegfelolvasó hangmodell betanítása előtt hangfelvételekre és a hozzá juk tartozó szövegátiratokra lesz szüksége. Ezen az oldalon áttekintjük az adattípusokat, azok felhasználási módját és azok kezelését.

## <a name="data-types"></a>Adattípusok

A hangbetanítási adatkészlet hangfelvételeket és a kapcsolódó átiratokat tartalmazó szövegfájlt tartalmaz. Minden hangfájlnak egyetlen kimondott szöveget kell tartalmaznia (egyetlen mondatot vagy egyetlen fordulatot egy párbeszédrendszerhez), és 15 másodpercnél rövidebbnek kell lennie.

Bizonyos esetekben előfordulhat, hogy nem rendelkezik a megfelelő adatkészletkészsel, és szeretné tesztelni az egyéni hangoktatást a rendelkezésre álló hangfájlokkal, rövid vagy hosszú, átiratokkal vagy anélkül. Olyan eszközöket (béta) biztosítunk, amelyek segítségével a hanganyagot kimondott szövegre szegmentálhatja, és a [Batch Transcription API használatával](batch-transcription.md)átiratokat készíthet.

Ez a táblázat felsorolja az adattípusokat és azt, hogy ezek hogyan használhatók egyéni szövegfelolvasó hangmodell létrehozásához.

| Adattípus | Leírás | A következő esetekben használja | További szolgáltatás szükséges | Modell betanításához szükséges mennyiség | Területi beállítás(ok) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Egyéni kimondott szöveg + egyező átirat** | Hangfájlok (.zip) gyűjteménye (.wav) egyéni kimondott szövegként. Minden hangfájl nak legrövidebbnek kell lennie, és egy formázott átirattal (.txt) kell párosulnia. | Szakmai felvételek egyező átiratokkal | Készen állunk az edzésre. | Nem kemény követelmény az en-US és a zh-CN. Több mint 2000+ különböző kimondott szöveg más területi beállításokhoz. | [Minden egyéni hangterületi beállítás](language-support.md#customization) |
| **Hosszú audio + átirata (béta)** | Hosszú, szegmentálatlan (20 másodpercnél hosszabb) hangfájlokgyűjteménye (.zip), amelyhez egy átirat (.txt) kapcsolódik, amely tartalmazza az összes kimondott szót. | Van hangfájlok at és a megfelelő átiratok, de ezek nem vannak mentve utterances. | Szegmentálás (kötegtranszkripció használatával).<br>Audio formátum átalakítása, ahol szükséges. | Nincs kemény követelmény  | [Minden egyéni hangterületi beállítás](language-support.md#customization) |
| **Csak hang (béta)** | A gyűjtemény (.zip) audio fájlok at átirat nélkül. | Csak audio fájlok állnak rendelkezésre, átiratok nélkül. | Szegmentálás + átirat generálása (kötegelt átírás használatával).<br>Audio formátum átalakítása, ahol szükséges.| Nincs kemény követelmény | [Minden egyéni hangterületi beállítás](language-support.md#customization) |

A fájlokat típus szerint kell egy adatkészletbe csoportosítani, és zip fájlként kell feltölteni. Minden adatkészlet csak egyetlen adattípust tartalmazhat.

> [!NOTE]
> Előfizetésenként legfeljebb 10 .zip fájl importálható ingyenes előfizetéses (F0) és 500 normál előfizetéses (S0) felhasználó számára.

## <a name="individual-utterances--matching-transcript"></a>Egyéni kimondott szöveg + egyező átirat

Az egyes kimondott szövegeket és a megfelelő átiratot kétféleképpen készítheti el. Írjon egy szkriptet, és olvassa el egy hangtehetség, vagy használja a nyilvánosan elérhető hang-és átírni, hogy a szöveget. Ha az utóbbit teszi, szerkesztési disfluencies a hangfájlokat, mint például "um" és más töltőanyag hangok, akadozik, motyogott szavak, vagy mispronunciations.

A jó hangbetűtípus létrehozásához hozza létre a felvételeket egy csendes szobában, kiváló minőségű mikrofonnal. A következetes hangerő, a beszédsebesség, a beszédhangés a beszéd kifejező modora elengedhetetlen.

> [!TIP]
> Ahhoz, hogy éles hanghoz hangot hozzon létre, javasoljuk, hogy professzionális stúdiót és hangalapú tehetséget használjon. További információ: [Hangminták rögzítése egyéni hanghoz.](record-custom-voice-samples.md)

### <a name="audio-files"></a>Hangfájlok

Minden hangfájlnak tartalmaznia kell egy kimondott szöveget (egy mondatot vagy egy párbeszédrendszer egyetlen fordulatát), amely kevesebb, mint 15 másodperc hosszú. Minden fájlnak ugyanazon a beszélt nyelven kell lennie. A kínai-angol kétnyelvű szavak kivételével a többnyelvű, egyéni szövegfelolvasó hangok nem támogatottak. Minden hangfájlnak egyedi numerikus fájlnévvel kell rendelkeznie, amelynek a .wav kiterjesztésű kiterjesztése van.

Hangkészítéskor kövesse ezeket az irányelveket.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | RIFF (.wav), .zip fájlba csoportosítva |
| Mintavételi arány | Legalább 16 000 Hz |
| Mintaformátum | PCM, 16 bites |
| Fájlnév | Numerikus, .wav kiterjesztéssel. Ismétlődő fájlnevek nem engedélyezettek. |
| Hang hossza | 15 másodpercnél rövidebb |
| Archiválási formátum | .zip |
| Az archívum maximális mérete | 2048 MB |

> [!NOTE]
> A .wav fájlok, amelyek mintavételi sebessége alacsonyabb, mint 16 000 Hz, elutasításra kerülnek. Ha a .zip fájl különböző mintavételi sebességgel rendelkező .wav fájlokat tartalmaz, csak a 16 000 Hz-es vagy annál nagyobb fájlok lesznek importálva. A portál jelenleg importálja a .zip archívumokat 200 MB-ig. Azonban több archívumot lehet feltölteni.

### <a name="transcripts"></a>Átiratát

Az átírási fájl egy egyszerű szöveges fájl. Az átiratok elkészítéséhez használja ezeket az irányelveket.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | Egyszerű szöveg (.txt) |
| Kódolási formátum | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE vagy UTF-16-BE. Zh-CN esetén az ANSI/ASCII és az UTF-8 kódolások nem támogatottak. |
| Kimondott szövegek száma soronként | **Egy** - Az átírási fájl minden sorának tartalmaznia kell az egyik hangfájl nevét, majd a megfelelő átírást. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani. |
| Maximális fájlméret | 2048 MB |

Az alábbiakban egy példa látható arra, hogy az átiratok hogyan vannak rendszerezve az utterance (kifejezés) kifejezéssel egy .txt fájlban:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Fontos, hogy az átiratok 100%-ban pontos átiratok a megfelelő hang. Az átiratok hibái minőségi veszteséget eredményeznek a képzés során.

> [!TIP]
> Éles szövegfelolvasó hangok létrehozásakor válassza ki azokat a kimondott szövegeket (vagy parancsfájlok írását), amelyek figyelembe veszik a fonetikus lefedettséget és a hatékonyságot is. Problémái vannak a kívánt eredmények megszerzésével? [Lépjen kapcsolatba a Custom Voice](mailto:speechsupport@microsoft.com) csapatával, ha többet szeretne megtudni arról, hogy konzultáljunk velünk.

## <a name="long-audio--transcript-beta"></a>Hosszú audio + átirata (béta)

Bizonyos esetekben előfordulhat, hogy nem áll rendelkezésre szegmentált hang. Az egyéni hangportálon keresztül nyújtunk egy szolgáltatást (béta) a hosszú hangfájlok szegmentálásához és az átiratok létrehozásához. Ne feledje, hogy ezt a szolgáltatást a beszédfelismerési előfizetés használata díja ként számítjuk fel.

> [!NOTE]
> A hosszú hangszegmentálási szolgáltatás kihasználja a beszéd-szöveg kötegelt átírási funkcióját, amely csak a szabványos előfizetéses (S0) felhasználókat támogatja. A szegmentálás feldolgozása során a hangfájlokat és az átiratokat is elküldi az egyéni beszédfelismerési szolgáltatásnak, hogy finomítsa a felismerési modellt, hogy az adatok pontossága javítható legyen. A folyamat során nem őrizünk meg adatokat. A szegmentálás befejezése után csak a kimondott szövegszegések és a leképezési átiratok tárolják a letöltés és a betanítás.

### <a name="audio-files"></a>Hangfájlok

A hang szegmentálásra való előkészítésekor kövesse ezeket az irányelveket.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | RIFF (.wav) legalább 16 khz-16 bites mintavételi sebességgel PCM-ben vagy .mp3-ban, legalább 256 Kb/s átviteli sebességű, .zip fájlba csoportosítva |
| Fájlnév | Az ASCII és unicode karakterek támogatottak. Ismétlődő nevek nem engedélyezettek. |
| Hang hossza | 20 másodpercnél hosszabb |
| Archiválási formátum | .zip |
| Az archívum maximális mérete | 2048 MB |

Minden hangfájlt zip fájlba kell csoportosítani. Ez rendben van, hogy .wav fájlokat és .mp3 fájlokat egy audio zip. Feltölthet például egy "kingstory.wav" nevű hangfájlt, 45 másodperc ig tartó és egy másik hangfájlt, amelynek neve "queenstory.mp3", 200 másodperces. Minden .mp3 fájl feldolgozás után .wav formátumba alakul át.

### <a name="transcripts"></a>Átiratát

Az átiratokat az ebben a táblázatban felsorolt specifikációknak megfelelően kell elkészíteni. Minden hangfájlt egy átiratgal kell egyeztetni.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | Egyszerű szöveg (.txt), .zip-be csoportosítva |
| Fájlnév | A megfelelő hangfájl nevével megegyező név használata |
| Kódolási formátum | CSAK UTF-8-Anyagjegyzék |
| Kimondott szövegek száma soronként | Nincs korlátozás |
| Maximális fájlméret | 2048 MB |

Az ilyen adattípusban lévő összes átiratfájlt zip fájlba kell csoportosítani. Például feltöltött egy zip fájlt, amely egy "kingstory.wav" nevű hangfájlt tartalmaz, 45 másodperc hosszú, és egy másikat ,"queenstory.mp3", 200 másodperc hosszú. Egy másik zip fájlt kell feltöltenie, amely két átiratot tartalmaz, az egyiket "kingstory.txt",a másikat "queenstory.txt"-nek hívják. Minden egyszerű szöveges fájlon belül megadja a megfelelő hang teljes helyes átírását.

Az adatkészlet sikeres feltöltése után segítünk a hangfájl szegmensére a megadott átirat alapján. Ellenőrizheti a szegmentált utterances és a megfelelő átiratokat az adatkészlet letöltésével. Egyedi azonosítók lesznek rendelve a szegmentált utterances automatikusan hozzá. Fontos, hogy győződjön meg róla, hogy az ön által megadott átiratok 100%-ban pontosak. Az átiratok hibái csökkenthetik az audio szegmentálás során a pontosságot, és tovább vezethetik be a minőségi veszteséget a későbbi képzési fázisban.

## <a name="audio-only-beta"></a>Csak hang (béta)

Ha nincs átirata a hangfelvételekhez, használja a **Csak hang** lehetőséget az adatok feltöltéséhez. Rendszerünk segít a hangfájlok szegmentálásában és átírásában. Ne feledje, hogy ez a szolgáltatás beleszámít a beszéd-szöveg előfizetés használatába.

Hangkészítéskor kövesse ezeket az irányelveket.

> [!NOTE]
> A hosszú hangszegmentálási szolgáltatás kihasználja a beszéd-szöveg kötegelt átírási funkcióját, amely csak a szabványos előfizetéses (S0) felhasználókat támogatja.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | RIFF (.wav) legalább 16 khz-16 bites mintavételi sebességgel PCM-ben vagy .mp3-ban, legalább 256 Kb/s átviteli sebességű, .zip fájlba csoportosítva |
| Fájlnév | Az ASCII és unicode karakterek támogatottak. Nem engedélyezett ismétlődő név. |
| Hang hossza | 20 másodpercnél hosszabb |
| Archiválási formátum | .zip |
| Az archívum maximális mérete | 2048 MB |

Minden hangfájlt zip fájlba kell csoportosítani. Az adatkészlet sikeres feltöltése után segítünk a hangfájl szegmensek utterances alapján a beszédbatch átírási szolgáltatás. Egyedi azonosítók lesznek rendelve a szegmentált utterances automatikusan hozzá. Az egyező átiratok a beszédfelismerésen keresztül jönnek létre. Minden .mp3 fájl feldolgozás után .wav formátumba alakul át. Ellenőrizheti a szegmentált utterances és a megfelelő átiratokat az adatkészlet letöltésével.

## <a name="next-steps"></a>További lépések

- [Egyéni hang létrehozása](how-to-custom-voice-create-voice.md)
- [Útmutató: Hangminták rögzítése](record-custom-voice-samples.md)
