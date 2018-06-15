---
title: AudioInputStream fogalmak |} Microsoft Docs
description: A AudioInputStream API funkcióinak áttekintése.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
manager: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: 528356473c4221a815fa68cbec3426866c4cbd23
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "35350014"
---
# <a name="about-the-audio-input-stream-api"></a>Tudnivalók a hangbemenetet adatfolyam-API

A **hang bemeneti adatfolyam** API hang adatfolyamok adatfolyamként történő a mikrofon vagy a bemeneti fájl API-k használata helyett a felismerő lehetőséget kínál.

## <a name="api-overview"></a>Az API áttekintése

Az API-t két összetevőket, használja a `AudioInputStream` (a nyers hangadatok) és a `AudioInputStreamFormat`.

A `AudioInputStreamFormat` határozza meg a hangadatok formátuma. Normál hasonlítható `WAVEFORMAT` wave fájlok a Windows-struktúrája.

  - `FormatTag`

    A hang formátuma. A beszédfelismerés SDK jelenleg csak a támogatja `format 1` (PCM - little endian).

  - `Channels`

    A csatornák száma. A jelenlegi beszédfelismerési szolgáltatás csak egy csatorna (monó) hang anyagot támogatja.

  - `SamplesPerSec`

    A mintavételi gyakoriság. A tipikus mikrofon felvétel rendelkezik 16000 minták száma másodpercenként.

  - `AvgBytesPerSec`

    Átlagos bájt / másodperc, a program `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. Átlagos bájt / mp változó bitrates használó hang adatfolyamok esetén eltérők lehetnek.

  - `BlockAlign`

    A méret a egyetlen, a program `Channels * ceil(wBitsPerSample, 8)`. Kitöltési, mert a tényleges érték nagyobb, mint ez az érték lehet.

  - `BitsPerSample`

    A bit / minta. Egy tipikus hangadatfolyam 16 bit / minta (CD minőségű) használja.

A `AudioInputStream` alaposztály felülbírálják az egyéni adatfolyam-adapter. Ez az adapter van, ezek a függvények végrehajtásához:

   - `GetFormat()`

     Ez a funkció eléréséhez a hangadatfolyam formátuma nevezik. Mutató AudioInputStreamFormat puffer kap.

   - `Read()`

     Ez a funkció neve lehet adatokat lekérni a hangadatfolyam. Egy paraméter a pufferbe a hang adatok másolása mutató. A második paraméter a puffer mérete. A függvény a puffermérettel bájtok számát adja vissza. A visszatérési érték `0` az adatfolyam végét jelöli.

   - `Close()`

     Ez a funkció neve a hangadatfolyam bezárásához.

## <a name="usage-examples"></a>Használati példák

Általában a következőket is érintett hang bemeneti adatfolyam használata esetén:

  - Azonosítsa a hangadatfolyam formátuma. A formátum támogatnia kell az SDK és a beszédfelismerés. Jelenleg a következő konfiguráció támogatott:

    Az audioformátum egy címke (PCM), egy csatorna, másodpercenként 16000 minták 32000 bájt / mp, két blokk igazítása (16 bites minta térközét beleértve), 16 bit / minta

  - Ellenőrizze, hogy a kód biztosít, a fenti specifikációk hang nyers adatok. Ha a hang forrásadatok nem egyezik meg a támogatott formátumok, a hang kell alakíthatók át szükséges formátumra alakítja.

  - Az egyéni bemeneti hangadatfolyam osztály célosztályából `AudioInputStream`. Alkalmazzon a `GetFormat()`, `Read()`, és `Close()` műveletet. A pontos függvényaláíráshoz nyelvi-függő, de a kód alábbihoz hasonlóan fog megjelenni a fenti::

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
        }

        public size_t Read(byte *buffer, size_t size) {
            // returns audio data to the caller.
            // e.g. return read(config.YYY, buffer, size);
        }

        public void Close() {
            // close and cleanup resources.
        }
     };
    ```

  - A bemeneti hangadatfolyam használja:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - Néhány nyelv esetében a `contosoStream` törölni kell explicit módon a felismerési befejeződése után. A AudioStream nem lehet elengedni, mielőtt a teljes bemeneti olvasható. A forgatókönyv using `StopContinuousRecognitionAsync` és `StopContinuousRecognitionAsync` mutatja be ezt a mintát elvét van szükség:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>További lépések

* [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: how to a C# beszéd felismerésére](quickstart-csharp-windows.md)
