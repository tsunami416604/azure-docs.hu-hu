---
title: Beszéd SDK hangbemeneti stream fogalmak
description: A beszédfelismerés SDK hang bemeneti stream API-funkcióinak áttekintése.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: 6c2d7c5787305f60b73ab83ea17367b04e03ac12
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985180"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>A beszédfelismerés SDK hang kapcsolatos bemeneti stream API

A beszédfelismerés SDK **hang bemeneti Stream** API lehetővé teszi a audiostreamek lejátszásával eseménysorozatot az felismerő helyett a mikrofon vagy a bemeneti fájl API-k használatával.

A következő lépések szükségesek, ha bemenetét hang használatával:

- Azonosítsa az audio-adatfolyam formátuma. A formátum támogatnia kell a Speech SDK és a Speech. Jelenleg csak a következő konfiguráció használata támogatott:

  A PCM formátum, egy csatornát, másodpercenként 32000 bájt / másodperc, 16000 minták hangmintát két blokk igazítása (16 bites beleértve egy minta margóinak), 16 bit / minta.

  A megfelelő kódot az SDK hangformátum létrehozása a következőhöz hasonló:

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Ellenőrizze, hogy a kód megfelelően ezek a specifikációk hang nyers tud biztosítani. Hangbemeneti forrás adatait nem felel meg a támogatott formátumok, ha a hanganyag átkódolt a szükséges formátumban kell lennie.

- Hozzon létre saját hang bemeneti stream rácsvezérlőből származó osztályt `PullAudioInputStreamCallback`. Alkalmazzon a `Read()` és `Close()` tagokat. A pontos függvényfej nyelvfüggő, de a kódot a kódminta hasonlóan néz ki:

  ```
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
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

- Hozzon létre egy hang-konfigurációt a hang alapján formátum és a bemeneti stream. A felismerő létrehozásakor adja át a rendszeres beszédfelismerési és a hang bemeneti konfigurációt is. Példa:

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
