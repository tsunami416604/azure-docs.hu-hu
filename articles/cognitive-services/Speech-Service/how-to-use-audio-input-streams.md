---
title: AudioInputStream fogalmak |} A Microsoft Docs
description: A AudioInputStream API-funkcióinak áttekintése.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
manager: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: 0eafa7e88df5d00a67646ca7f82ca027602a40b3
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071446"
---
# <a name="about-the-audio-input-stream-api"></a>Tudnivalók a hanganyag bemeneti stream API

A **hang bemeneti Stream** API lehetővé teszi a audiostreamek lejátszásával eseménysorozatot az felismerő helyett a mikrofon vagy a bemeneti fájl API-k használatával.

## <a name="api-overview"></a>Az API áttekintése

Az API-t használ a két összetevőt a `AudioInputStream` (a nyers hang adatok) és a `AudioInputStreamFormat`.

A `AudioInputStreamFormat` hang adatok formátumát határozza meg. A standard összehasonlíthatók `WAVEFORMAT` wave fájlok a Windows-szerkezet.

  - `FormatTag`

    A hanganyag formátumát. A beszédfelismerés SDK jelenleg csak a támogatja `format 1` (PCM - növekvő bájtsorrendű).

  - `Channels`

    A csatornák száma. A jelenlegi speech service támogatja a csak egy csatorna (monó) hang anyagot.

  - `SamplesPerSec`

    A mintavételi gyakoriság. Egy tipikus mikrofon rögzítése rendelkezik 16000 minták száma másodpercenként.

  - `AvgBytesPerSec`

    Átlagos bájtok száma másodpercenként, számítással `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. Bájtok átlagos száma másodpercenként audiostreamek lejátszásával, amely a változó bitsebességre való átkódolása használata esetén eltérők lehetnek.

  - `BlockAlign`

    Egy egyetlen képkockájának mérete számítással `Channels * ceil(wBitsPerSample, 8)`. Kitöltés, mert a tényleges érték nagyobb, mint ez az érték lehet.

  - `BitsPerSample`

    A bit / minta. Egy tipikus hang stream 16 bit / minta (CD minőségi) használja.

A `AudioInputStream` alaposztály felülírja a rendszer az egyéni stream adapter. Ez az adapter van, ezek a függvények végrehajtásához:

   - `GetFormat()`

     Ez a függvény neve beolvasni az audio-adatfolyam formátuma. Lekéri egy mutatót a AudioInputStreamFormat pufferbe.

   - `Read()`

     Ez a funkció le adatokat az audio-adatfolyamot nevezzük. Egy paraméter értéke az audio adatokat másolni a puffer mutató elemnek. A második paraméter és a puffer mérete. A függvény a puffert a másolt bájtok számát adja vissza. A visszatérési érték `0` azt jelzi, hogy a konce datového proudu.

   - `Close()`

     Ez a függvény neve gombra kattintva zárja be az audio-adatfolyamot.

## <a name="usage-examples"></a>Használati példák

Általában a következő lépést is igényel, hang bemeneti streamekhez használatakor:

  - Azonosítsa az audio-adatfolyam formátuma. A formátum támogatnia kell az SDK és a speech. Jelenleg a következő konfigurációt támogat:

    Formát zvuku egy címkét (PCM), egy csatorna, másodpercenként 16000 minták 32000 bájt / másodperc, két blokk igazítása (16 bites beleértve egy minta margóinak), 16 bit / minta

  - Ellenőrizze, hogy a kód biztosíthat a nyers hangadatokat feltárhatja, hogy a fenti adatait tartalmazza. Hangbemeneti forrás adatait nem felel meg a támogatott formátumok, ha a hanganyag átkódolt a szükséges formátumban kell lennie.

  - Az egyéni hang bemeneti stream osztály származtatott `AudioInputStream`. Alkalmazzon a `GetFormat()`, `Read()`, és `Close()` műveletet. A pontos függvényfej nyelvfüggő, de a kódot a kódminta hasonlóan néz ki:

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

  - A bemeneti audio-adatfolyam használata:

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

  - Néhány nyelven a `contosoStream` elismerését befejeződése után explicit módon kell törölni. Nem lehet engedélyezni a AudioStream, mielőtt a teljes bemeneti olvasható. Az egy forgatókönyv- `StopContinuousRecognitionAsync` és `StopContinuousRecognitionAsync` egy ebben a példában szemléltetett fogalom van szükség:

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

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
