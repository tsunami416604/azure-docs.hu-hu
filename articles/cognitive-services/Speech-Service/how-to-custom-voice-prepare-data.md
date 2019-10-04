---
title: Az egyéni hangvezérelt szolgáltatáshoz tartozó adatelőkészítés
titleSuffix: Azure Cognitive Services
description: Hozzon létre egy egyéni hangot a márka számára a Speech Service szolgáltatással. A Studio-felvételek és a hozzájuk tartozó parancsfájlok megadásával a szolgáltatás egy egyedi hangmodellt hoz létre a rögzített hangra hangolva. Ezzel a hanggal megtaníthatja termékeit, eszközeit és alkalmazásait.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8b4b5553605042499a9a8f3343ac4e6678e7006f
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640427"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Az adatelőkészítés egyéni hang létrehozásához

Ha készen áll egy egyéni szöveg-beszéd hang létrehozására az alkalmazáshoz, az első lépés a hangfelvételek és a hozzájuk tartozó parancsfájlok összegyűjtése a hangmodell betanításának megkezdéséhez. A szolgáltatás ezeket az adatkészleteket használja egy egyedi hanghangolás létrehozásához, amely megfelel a hangfelvételek hangjának. A hang betanítása után elindíthatja a beszédfelismerést az alkalmazásaiban.

Kis mennyiségű adattal kezdheti meg a koncepció igazolását. Azonban minél több adattal rendelkezik, annál természetesebb lesz az egyéni hang. A saját szöveg-beszéd hangmodell tanítása előtt hangfelvételekre és a kapcsolódó szöveges átírásokra lesz szüksége. Ezen az oldalon áttekintjük az adattípusokat, azok használatát, valamint az egyes eszközök kezelését.

## <a name="data-types"></a>Adattípusok

A hangalapú betanítási adatkészlet hangfelvételeket, valamint a hozzájuk tartozó átírásokat tartalmazó szövegfájlt tartalmaz. Minden hangfájlnak tartalmaznia kell egyetlen részletet (egyetlen mondattal vagy egy párbeszédpanelrendszer egyetlen bekapcsolásával), és 15 másodpercnél rövidebbnek kell lennie.

Bizonyos esetekben előfordulhat, hogy nem áll készen a megfelelő adatkészletre, és tesztelni szeretné az egyéni hangképzést a rendelkezésre álló hangfájlokkal, illetve rövid vagy hosszú, átiratokkal vagy anélkül. Olyan eszközöket biztosítunk (bétaverzió), amelyek segítenek a hang hosszúságú kimondott szöveg való szegmentálásában és átiratok előkészítésében a Batch-átírási [API](batch-transcription.md)használatával.

Ez a táblázat felsorolja az adattípusokat, valamint azt, hogy miként lehet egyéni szöveg-beszéd hangmodelleket létrehozni.

| Adattípus | Leírás | A következő esetekben használja | További szolgáltatás szükséges | A modell betanításához szükséges mennyiség | Területi beállítás (ok) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Egyéni hosszúságú kimondott szöveg + egyező átirat** | A hangfájlok (. wav) gyűjteménye (. zip) egyéni hosszúságú kimondott szöveg. Az egyes hangfájloknak 15 másodpercnél rövidebbnek kell lenniük, és formázott átirattal (. txt) kell párosítani. | Professzionális felvételek a megfelelő átiratokkal | Képzésre kész. | Nem szükséges szigorú követelmény az en-US és a zh-CN számára. Több mint 2000 különböző hosszúságú kimondott szöveg más területi beállításokhoz. | Minden egyéni hang területi beállítása |
| **Hosszú hang + átirat (bétaverzió)** | Hosszú, nem szegmentált hangfájlok (20 másodpercnél hosszabb) gyűjteménye (. txt), amely az összes szóbeli szót tartalmazza. | Vannak hangfájlok és a hozzájuk tartozó átiratok, de nem a hosszúságú kimondott szöveg-ba vannak tagolva. | Szegmentálás (a Batch-átírással).<br>A hangformátum átalakítása, ahol szükséges. | Nem szükséges szigorú követelmény az en-US és a zh-CN számára. | `en-US` és `zh-CN` |
| **Csak hang (bétaverzió)** | Átirat nélküli hangfájlok gyűjteménye (. zip). | Csak hangfájlok érhetők el, átiratok nélkül. | Szegmentálás + átirat létrehozása (batch-átírással).<br>A hangformátum átalakítása, ahol szükséges.| A és `en-US` `zh-CN`a esetében nem szükséges nehéz követelmény. | `en-US` és `zh-CN` |

A fájlokat típus szerint kell csoportosítani egy adatkészletbe, és zip-fájlként kell feltölteni. Az egyes adatkészletek csak egyetlen adattípust tartalmazhatnak.

> [!NOTE]
> Az előfizetések által importálandó adatkészletek maximális száma 10. zip-fájl az ingyenes előfizetés (F0) felhasználóinak és a 500 a standard előfizetés (S0) felhasználói számára.

## <a name="individual-utterances--matching-transcript"></a>Egyéni hosszúságú kimondott szöveg + egyező átirat

Az egyes hosszúságú kimondott szöveg és a megfelelő átiratot kétféleképpen is előkészítheti. Írjon egy parancsfájlt, és olvassa el a hangvételt, vagy használja a nyilvánosan elérhető hangot, és írja át a szöveget. Ha ezt teszi, szerkessze a disfluencies a hangfájlokból, például az "UM" és más kitöltő hangokat, akadozik, motyogott szavakat vagy a kiejtéseket.

Jó hangbetűkészlet létrehozásához hozzon létre egy csendes helyen, magas színvonalú mikrofonnal. Elengedhetetlen a konzisztens kötet, a beszéd arány, a beszélő szurok és a beszéd kifejező jellege.

> [!TIP]
> Azt javasoljuk, hogy hozzon létre egy hangfelvételt az éles környezethez, és használja a Professional Recording studiót és a hangtehetséget. További információ: hangminták [rögzítése egyéni hanghoz](record-custom-voice-samples.md).

### <a name="audio-files"></a>Hangfájlok

Minden hangfájlnak tartalmaznia kell egy teljes (egyetlen mondatot vagy egy párbeszédpanelrendszer egyetlen bekapcsolását), amely kevesebb, mint 15 másodperc. Minden fájlnak ugyanabban a kimondott nyelven kell lennie. A többnyelvű egyéni szöveg-beszéd hangok nem támogatottak, kivéve a kínai – angol nyelveket. Minden hangfájlnak egyedi numerikus fájlnevet kell tartalmaznia, a fájlnév kiterjesztésével. wav.

A hang előkészítésekor kövesse az alábbi irányelveket.

| Tulajdonság | Value |
| -------- | ----- |
| Fájl formátuma | RIFF (. wav), egy. zip fájlba csoportosítva |
| Mintavételezési arány | Legalább 16 000 Hz |
| Minta formátuma | PCM, 16 bites |
| Fájlnév | Numerikus,. wav kiterjesztéssel. Nem engedélyezettek az ismétlődő fájlnevek. |
| Hang hossza | 15 másodpercnél rövidebb |
| Archív formátum | .zip |
| Archiválás maximális mérete | 200 MB |

> [!NOTE]
> a 16 000 Hz-nél kisebb mintavételi sebességgel rendelkező. wav-fájlokat a rendszer elutasítja. Ha a. zip fájl különböző mintavételi sebességű. wav-fájlokat tartalmaz, a rendszer csak a 16 000 Hz-es vagy annál újabb értékkel rendelkezőket importálja. A portál jelenleg a. zip archívumot akár 200 MB-ra is importálja. Azonban több Archívum is feltölthető.

### <a name="transcripts"></a>Átiratát

Az átírási fájl egy egyszerű szövegfájl. Ezeket az irányelveket az átírások előkészítéséhez használhatja.

| Tulajdonság | Value |
| -------- | ----- |
| Fájl formátuma | Egyszerű szöveg (. txt) |
| Kódolási formátum | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE vagy UTF-16-BE. A zh-CN, az ANSI/ASCII és az UTF-8 kódolások nem támogatottak. |
| Kimondott szövegek száma soronként | **Egy** – az átírási fájl minden sorának tartalmaznia kell az egyik hangfájl nevét, amelyet a megfelelő átírás követ. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani. |
| Maximális fájlméret | 50 MB |

Az alábbi példa bemutatja, hogyan rendezi a rendszer az átiratokat egy. txt fájlban:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Fontos, hogy az átiratok a megfelelő hang 100%-os pontos átiratával legyenek. Az átiratokban előforduló hibák a képzés során a minőség elvesztését mutatják be.

> [!TIP]
> Éles szöveg-beszéd hangok kiépítésekor válassza a hosszúságú kimondott szöveg (vagy a parancsfájlok írása) lehetőséget, amelyek figyelembe veszik a fonetikus lefedettséget és a hatékonyságot. Gondjai vannak a kívánt eredmények beszerzésével? Ha többet szeretne megtudni, [forduljon az egyéni](mailto:speechsupport@microsoft.com) hangcsapathoz.

## <a name="long-audio--transcript-beta"></a>Hosszú hang + átirat (bétaverzió)

Bizonyos esetekben előfordulhat, hogy nem érhető el szegmentált hang. Az egyéni hangportálon keresztül biztosítunk egy szolgáltatást (bétaverziót), amely segít a hosszú hangfájlok szegmentálásában és a transzkripciók létrehozásában. Vegye figyelembe, hogy a szolgáltatás a beszédfelismerés és a szöveges előfizetés használatának feltételeit terheli.

> [!NOTE]
> A hosszú hangú szegmentálási szolgáltatás kihasználja a beszéd és a szöveg batch-átírási funkcióját, amely csak a szabványos előfizetés (S0) felhasználóit támogatja. A szegmentálás feldolgozása során a rendszer a hangfájlokat és az átiratokat is elküldi a Custom Speech szolgáltatásnak az adatfelismerési modell pontosítása érdekében, hogy az adatok pontossága is javuljon. A folyamat során a rendszer nem őrzi meg az adatmegőrzési időszakot. A szegmentálás befejezése után a rendszer csak a szegmentált hosszúságú kimondott szöveg és a leképezési átiratokat fogja tárolni a letöltéshez és a képzéshez.

### <a name="audio-files"></a>Hangfájlok

Kövesse az alábbi irányelveket a hanganyagok szegmentálására való felkészülés során.

| Tulajdonság | Value |
| -------- | ----- |
| Fájl formátuma | A RIFF (. wav) mintavételezési sebessége legalább 16 kHz – 16 bit a PCM vagy a. mp3 esetében legalább 256 KBps, a. zip fájlba csoportosítva |
| Fájlnév | Csak ASCII-karakterek. A névben található Unicode-karakterek sikertelenek lesznek (például a kínai karakterek, vagy a "–" szimbólum). Nem engedélyezettek az ismétlődő nevek. |
| Hang hossza | 20 másodpercnél hosszabb |
| Archív formátum | .zip |
| Archiválás maximális mérete | 200 MB |

Az összes hangfájlt zip-fájlba kell csoportosítani. A. wav fájlok és az. mp3 fájlok egyetlen hangzip-fájlba helyezhetők, de a zip-fájlban nem engedélyezettek az almappa. Feltölthet például egy "kingstory. wav" nevű hangfájlt tartalmazó zip-fájlt, 45-másodpercet, egy másikat pedig "queenstory. mp3", 200-Second-Long, almappák nélkül. A rendszer az összes. MP3 fájlt a feldolgozás után. wav formátumúvá alakítja át.

### <a name="transcripts"></a>Átiratát

Az átiratokat fel kell készülnie az ebben a táblázatban szereplő specifikációba. Minden hangfájlnak meg kell egyeznie egy átirattal.

| Tulajdonság | Value |
| -------- | ----- |
| Fájl formátuma | Egyszerű szöveg (. txt), egy. zip-fájlba csoportosítva |
| Fájlnév | Ugyanazt a nevet használja, mint a megfelelő hangfájl |
| Kódolási formátum | UTF-8 – csak ANYAGJEGYZÉK |
| Kimondott szövegek száma soronként | Korlátlan |
| Maximális fájlméret | 50 MB |

Az adattípusban található összes átirat fájlnak egy zip-fájlba kell csoportosítva lennie. A zip-fájlban nem szerepelhet almappa. Például feltöltött egy "kingstory. wav" nevű hangfájlt tartalmazó zip-fájlt, a 45 másodpercet, és egy másikat, a "queenstory. mp3" nevet, 200 másodpercet. Fel kell töltenie egy másik, két átiratot tartalmazó zip-fájlt, amely egy "kingstory. txt" nevű, a másik pedig "queenstory. txt". Minden egyszerű szövegfájlon belül meg kell adnia a megfelelő hang teljes átírását.

Az adatkészlet sikeres feltöltése után segítséget nyújtunk a hangfájlnak a megadott átirat alapján történő hosszúságú kimondott szöveg. Az adatkészlet letöltésével a szegmentált hosszúságú kimondott szöveg és a megfelelő átiratokat is megtekintheti. A szegmentált hosszúságú kimondott szöveg automatikusan egyedi azonosítók lesznek hozzárendelve. Fontos, hogy az Ön által megadott átiratok 100%-ban pontosak legyenek. Az átiratokban előforduló hibák csökkenthetik a hangszegmentálási pontosságot, és továbbra is bevezethetik a minőség elvesztését a betanítási fázisban, amely később következik be.

## <a name="audio-only-beta"></a>Csak hang (bétaverzió)

Ha nem rendelkezik átírásokkal a hangfelvételekhez, az adatok feltöltéséhez használja a **csak hang** lehetőséget. A rendszer segíthet a hangfájlok szegmentálásában és átírásában. Vegye figyelembe, hogy ez a szolgáltatás a beszédfelismerés és a szöveg közötti előfizetés használatát veszi figyelembe.

A hang előkészítésekor kövesse az alábbi irányelveket.

> [!NOTE]
> A hosszú hangú szegmentálási szolgáltatás kihasználja a beszéd és a szöveg batch-átírási funkcióját, amely csak a szabványos előfizetés (S0) felhasználóit támogatja.

| Tulajdonság | Value |
| -------- | ----- |
| Fájl formátuma | A RIFF (. wav) mintavételezési sebessége legalább 16 kHz – 16 bit a PCM vagy a. mp3 esetében legalább 256 KBps, a. zip fájlba csoportosítva |
| Fájlnév | Csak ASCII-karakterek. A névben található Unicode-karakterek sikertelenek lesznek (például a kínai karakterek, vagy a "–" szimbólum). Nem engedélyezett a duplikált név. |
| Hang hossza | 20 másodpercnél hosszabb |
| Archív formátum | .zip |
| Archiválás maximális mérete | 200 MB |

Az összes hangfájlt zip-fájlba kell csoportosítani. A zip-fájlban nem szerepelhet almappa. Miután sikeresen feltöltötte az adatkészletet, segítünk a hangfájl szegmentálásában a hosszúságú kimondott szöveg a Speech batch transzkripciós szolgáltatás alapján. A szegmentált hosszúságú kimondott szöveg automatikusan egyedi azonosítók lesznek hozzárendelve. A beszédfelismerés során a rendszer a megfelelő átiratokat hozza létre. A rendszer az összes. MP3 fájlt a feldolgozás után. wav formátumúvá alakítja át. Az adatkészlet letöltésével a szegmentált hosszúságú kimondott szöveg és a megfelelő átiratokat is megtekintheti.

## <a name="next-steps"></a>További lépések

- [Egyéni hang létrehozása](how-to-custom-voice-create-voice.md)
- [Útmutató Hangminták rögzítése](record-custom-voice-samples.md)
