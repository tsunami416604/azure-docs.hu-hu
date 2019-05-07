---
title: Adatok előkészítése az egyéni beszédfelismerési – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Hozzon létre egy egyéni beszédfelismerési saját márkáját az Azure Speech Services. Studio-felvételt, és a kapcsolódó parancsprogramokat ad meg, a szolgáltatás használatára vannak konfigurálva a rögzített hangalapú egyéni modell állít elő. Használja ezt a hangot beszéd, a termékek, eszközök és alkalmazások szintetizálásához.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 18e1bb486c47baf7648a74e31451e2db73f72250
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156859"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Hozzon létre egy egyéni beszédfelismerési adatok előkészítése

Ha készen áll az alkalmazás egy egyéni szövegfelolvasás létrehozásához, az első lépés az gyűjtse össze a hanganyag és a kapcsolódó parancsprogramokat elindításához a hangalapú modell betanításához. A szolgáltatás az adatok használatával hozzon létre egy egyedi hang, a beszéd, a felvételt a megfelelő lehetőségeire. Miután a hangalapú már betanított, indítsa el az alkalmazásokat a beszédfelismerési szintetizáló.

Az egy kisebb mennyiségű adatot hozhat létre a koncepció igazolása megkezdése. Azonban minél több adatot, Ön által megadott, a további természetes az egyéni beszédfelismerési hang lesz. Mielőtt a saját szövegfelolvasás modell betanításához hanganyag és a hozzá tartozó szöveg beszédátírás lesz szüksége. Ezen a lapon áttekintheti az adattípusokat, miként használják, és hogyan kezelhetők.

## <a name="data-types"></a>Adattípusok

Hangalapú betanítási adatkészletet hanganyag és a kapcsolódó beszédátírás tartalmazó szövegfájl tartalmaz. Mindegyik hangfájlt kell tartalmaznia egy egyetlen utterance (kifejezés) (egyetlen mondatban vagy egyetlen kapcsolja párbeszédpanel rendszer esetén), és kevesebb mint 15 másodperc hosszúságú lehet.

Bizonyos esetekben előfordulhat, hogy nem rendelkezik a megfelelő adatkészlet készen áll, és érdemes teszteléséhez az egyéni beszédfelismerési képzési elérhető hangfájlok, rövid vagy hosszú, vagy anélkül szövegekben. Eszközök (bétaverzió) segítségével a hang oszthatja be kimondott szöveg és készítse elő az átiratok használatával biztosítunk a [Batch Beszédátírási API](batch-transcription.md).

Ez a táblázat felsorolja az adattípusokat, és hogyan egyes használja egy egyéni szövegfelolvasás modell létrehozásához.

| Adattípus | Leírás | A következő esetekben használja | További szolgáltatás szükséges | A modell betanításához mennyiség | Locale(s) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Az egyes utterances + egyező átiratok** | Gyűjteménye (.zip) hangfájlok (.wav), egyes kimondott szöveg. Mindegyik hangfájlt 15 másodperc vagy annál rövidebb, egy formázott szöveges (.txt) párosítva kell lennie. | Az átiratok egyező szakmai felvételek | Készen áll a betanításhoz. | En-US és zh-CN nem szigorú követelmény. Több mint 2000 más területi beállításokhoz for különböző kimondott szöveg. | Minden egyéni beszédfelismerési területi beállítások |
| **Hosszú hang + a szövegben (bétaverzió)** | Hosszú, szegmentálatlan hangfájlok (legfeljebb 20 másodperc), (.zip) gyűjteménye, amely tartalmazza az összes kimondott szavakat szöveges (.txt) párban. | Hangfájlok és egyező szövegekben rendelkezik, de azok vannak nem szegmentált be kimondott szöveg. | A Szegmentálás (kötegelt beszédátírási használatával).<br>Formát zvuku átalakítási ahol szükséges. | En-US és zh-CN nem szigorú követelmény. | `en-US` és `zh-CN` |
| **Csak hang (bétaverzió)** | Gyűjteménye (.zip) anélkül, hogy egy szöveges hangfájlok. | Csak akkor hangfájlok átiratok nélkül elérhető. | Szegmentálás + átiratok. generációs (kötegelt beszédátírási használatával).<br>Formát zvuku átalakítási ahol szükséges.| Nem rögzített követelmény `en-US` és `zh-CN`. | `en-US` és `zh-CN` |

Fájlok kell egy adatkészletbe típusa szerint csoportosítva és feltöltött zip-fájlként. Egyes adatkészletek csak egyetlen adattípushoz tartalmazhat.

> [!NOTE]
> Az adatkészletek importálhatók előfizetésenként engedélyezett maximális számát (F0) előfizetés felhasználóinak és a standard szintű előfizetés (S0) felhasználói 500 10 .zip fájlokat ingyenes.

## <a name="individual-utterances--matching-transcript"></a>Az egyes utterances + egyező átiratok

Egyes kimondott szöveg és a megfelelő átiratok kétféleképpen felvételek készítheti elő. Írjon egy parancsfájlt, és a egy hang szakembereket olvassa vagy használja a nyilvánosan elérhető hang és lefényképezze azt a szöveget. Az utóbbi esetben az audio fájlokból, például a "um" és egyéb kitöltő hangok, akadozik, mumbled szavakat vagy mispronunciations disfluencies szerkesztése

Egy jó hangtípusú előállításához, hozzon létre csendes szoba magas színvonalú mikrofon a felvételt. Egységes kötet, és beszéljen sebességét, beszéd térközt és kifejező mannerisms beszéd elengedhetetlenek.

> [!TIP]
> Hozzon létre egy hang éles környezetben, azt javasoljuk egy professzionális felvétel studio és a hangalapú szakembereket használja. További információkért lásd: [hogyan rögzítése a voice-minták az egyéni beszédfelismerési](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/record-custom-voice-samples).

### <a name="audio-files"></a>Hangfájlok

Mindegyik hangfájlt tartalmaznia kell egy egyetlen utterance (kifejezés) (egyetlen mondatban vagy egyetlen kapcsolja be a rendszer párbeszédpanel), legfeljebb 15 másodperc hosszúságú. Minden fájl ugyanazt a beszélt nyelvet kell lennie. Egyéni szövegfelolvasás hangok többnyelvű nem támogatottak, a bi-nyelv az angol nyelvű kínai kivételével. Mindegyik hangfájlt rendelkeznie kell egy egyedi numerikus filename .wav a fájlnevet kiterjesztéssel együtt.

Kövesse ezeket az irányelveket, hang előkészítésekor.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | Egy .zip-fájlban vannak csoportosítva RIFF (.wav), |
| Mintavételi ráta | legalább 16 000 Hz |
| Minta formátumával | A PCM, 16-bit |
| Fájlnév | Numerikus, .wav kiterjesztéssel. Nincsenek engedélyezett ismétlődő nevek. |
| Hang hossza | Rövidebb, mint 15 másodperc |
| Archív formátum | .zip |
| Maximális archívum mérete | 200 MB |

> [!NOTE]
> mintavételi sebessége alacsonyabb, mint 16000 Hz .wav fájlokat a rendszer elutasítja. Egy .zip-fájlban található különböző mintavételezés .wav fájlokat, ha csak adott egyenlő vagy nagyobb, mint 16000 Hz fog kiterjedni. A portál jelenleg import .zip archívumok akár 200 MB. Azonban több archívumot is tölthető fel.

### <a name="transcripts"></a>Szövegekben

Az átírási fájl egyszerű szöveges fájlt. Ezeket az irányelveket a beszédátírás segítségével.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | Egyszerű szövegfájlt (.txt) |
| Kódolási formátum | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE, or UTF-16-BE. Zh-CN, az ANSI/ASCII és az UTF-8 kódolást nem támogatottak. |
| Kimondott szövegek száma soronként | **Egy** – a beszédátírási fájl minden sorának tartalmaznia kell egy hang fájlt, majd a megfelelő beszédátírási nevére. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani. |
| Maximális fájlméret | 50 MB |

Alább egy példát, hogyan az átiratok vannak rendszerezve utterance (kifejezés) által utterance (kifejezés) egy .txt fájlban a következő:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Fontos, hogy az átiratok-e a megfelelő hanganyag hosszának végéig a 100 %-os pontos beszédátírás. Hibák az átiratok a minőségi adatveszteség vezet be a betanítás során.

> [!TIP]
> Éles létrehozását, szöveg-hang transzformációs beszédhangot, válassza ki a kimondott szöveg (vagy parancsfájlokat), amelyek figyelembe kell venni a fiók fonetikus lefedettség és a hatékonyságot. Az eredmények problémákat okoz a következőket szeretne? [Lépjen kapcsolatba az egyéni beszédfelismerési](mailto:speechsupport@microsoft.com) csapat található ki kellene USA kapcsolatos további részletekért.

## <a name="long-audio--transcript-beta"></a>Hosszú hang + a szövegben (bétaverzió)

Bizonyos esetekben előfordulhat, hogy nem több szegmensből álló hang érhető el. A service (bétaverzió) segítségével hosszú hangfájlok szegmentálására és beszédátírás létrehozása az egyéni beszédfelismerési portálon keresztül biztosítunk. Tartsa szem előtt, ez a szolgáltatás díjat kell kifizetni a hang-szöveg transzformációs előfizetés használata.

> [!NOTE]
> A hosszú – hang Szegmentálás szolgáltatás hang-szöveg, amely csak a standard szintű előfizetés (S0) felhasználói támogatja a kötegelt beszédátírási funkcióját használja. A Szegmentálás feldolgozása során a zenei fájlok és az átiratok is kapnak a Custom Speech service, amellyel pontosíthatja a modell, így az adatok a pontosság javítása érdekében. Adatok nem lesznek megőrizve a folyamat során. Miután elkészült a szegmentálást, szegmentált megcímkézzen és azok leképezés szövegekben tárolja a letöltés és képzési.

### <a name="audio-files"></a>Hangfájlok

Kövesse ezeket az irányelveket, szegmentálását audio előkészítésekor.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | Mintavételi arány PCM vagy .mp3 khz-16-bit legalább 16 bites arány legalább 256 KB/s, egy .zip-fájlban vannak csoportosítva (.wav) RIFF |
| Fájlnév | Csak ASCII-karaktereket. Sikertelen lesz, a név az Unicode-karaktereket (például a kínai karakterek vagy szimbólumok, mint például "–"). Nincs engedélyezett ismétlődő nevek. |
| Hang hossza | Hosszabb 20 másodperc |
| Archív formátum | .zip |
| Maximális archívum mérete | 200 MB |

Az összes hangfájlok szerint kell csoportosítani a zip-fájlban. Rendben, elhelyezi egy hang zip .wav és .mp3-fájlokat, de nem almappa engedélyezve van-e a zip-fájlt. Például feltölthet egy hangfájlt "kingstory.wav", a hosszú második 45 és a egy másik egy elnevezett "queenstory.mp3", 200-második-long, bármely almappák nélkül nevű tartalmazó zip-fájlt. Az összes .mp3-fájlokat feldolgozása után a .wav formátumra lesz átalakítva.

### <a name="transcripts"></a>Szövegekben

Az alábbi táblázat tartalmazza a specifikációknak szövegekben kell készíteni. Egy szöveges mindegyik hangfájlt kell egyeztetését.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | Egyszerű szövegfájlt (.txt), egy .zip csoportosítva |
| Fájlnév | Használja ugyanazt a nevet a megfelelő audio-fájlként |
| Kódolási formátum | UTF-8-AJ csak |
| Kimondott szövegek száma soronként | Korlátlan |
| Maximális fájlméret | 50M |

Ez az adattípus az összes szövegekben fájl szerint kell csoportosítani a zip-fájlban. A zip-fájl nem almappa használata engedélyezett. Például egy hangfájlt "kingstory.wav", 45 másodperc hosszúságú nevű tartalmazó zip-fájlt töltött fel, és egy másik egy elnevezett "queenstory.mp3", 200 másodperc mennyi ideig. Szüksége lesz egy másik két szövegekben, egy elnevezett "kingstory.txt", a másik egy "queenstory.txt" tartalmazó zip-fájl feltöltése. Minden egyszerű szöveges fájlba Ön biztosítja a megfelelő Audio a teljes megfelelő beszédátírási.

Után az adatkészlet sikeresen feltöltve, segítünk Önnek a hangfájlt oszthatja be a megadott szöveges alapján kimondott szöveg. Töltse le az adatkészlet szegmentált megcímkézzen és a megfelelő szövegekben ellenőrizheti. Egyedi azonosítót rendel a szegmentált utterances automatikusan. Fontos győződjön meg arról, hogy megadta az átiratok 100 %-os pontos. Hibák az átiratok a pontossága csökkenthető szegmentálását audio során, és további bevezetni a betanítási fázisban, amelyek később minőségi adatvesztés.

## <a name="audio-only-beta"></a>Csak hang (bétaverzió)

Ha beszédátírás nincs számára audiotartalmak felvételeit, használja a **csak hang** az adatok feltöltését. A rendszer segítségével szegmentálást, a hangfájlokat lefényképezze. Ne feledje, ez a szolgáltatás beleszámít a hang-szöveg transzformációs előfizetés használata.

Kövesse ezeket az irányelveket, hang előkészítésekor.

> [!NOTE]
> A hosszú – hang Szegmentálás szolgáltatás hang-szöveg, amely csak a standard szintű előfizetés (S0) felhasználói támogatja a kötegelt beszédátírási funkcióját használja.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | Mintavételi arány PCM vagy .mp3 khz-16-bit legalább 16 bites arány legalább 256 KB/s, egy .zip-fájlban vannak csoportosítva (.wav) RIFF |
| Fájlnév | Csak ASCII-karaktereket. Sikertelen lesz, a név az Unicode-karaktereket (például a kínai karakterek vagy szimbólumok, mint például "–"). Nincs engedélyezett ismétlődő név. |
| Hang hossza | Hosszabb 20 másodperc |
| Archív formátum | .zip |
| Maximális archívum mérete | 200 MB |

Az összes hangfájlok szerint kell csoportosítani a zip-fájlban. A zip-fájl nem almappa használata engedélyezett. Miután sikeresen feltöltötte az adatkészlet, segítünk Önnek a hangfájl oszthatja be a batch beszédátírási beszédszolgáltatás alapján kimondott szöveg. Egyedi azonosítót rendel a szegmentált utterances automatikusan. Egyező átiratok beszédfelismerés jön létre. Az összes .mp3-fájlokat feldolgozása után a .wav formátumra lesz átalakítva. Töltse le az adatkészlet szegmentált megcímkézzen és a megfelelő szövegekben ellenőrizheti.

## <a name="next-steps"></a>További lépések

- [Hozzon létre egy egyéni beszédfelismerési](how-to-custom-voice-create-voice.md)
- [Útmutató: Rögzítése a voice-minták](record-custom-voice-samples.md)
