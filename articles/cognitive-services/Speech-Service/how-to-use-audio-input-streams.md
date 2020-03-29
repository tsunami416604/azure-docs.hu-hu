---
title: BeszédbeszédSDK hangbemeneti adatfolyamának fogalmai
titleSuffix: Azure Cognitive Services
description: A Speech SDK hangbemeneti API-jának képességeinek áttekintése.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74109937"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>A Speech SDK hangbemeneti API-ja

A Speech SDK **audiobemeneti stream** API-ja lehetővé teszi a hang adatfolyamként való streamelését a felismerőknek a mikrofon vagy a bemeneti fájl API-jai nak használata helyett.

A következő lépések szükségesek a hangbemeneti adatfolyamok használatakor:

- Azonosítsa a hangfolyam formátumát. A formátumot a beszédfelismerési SDK-nak és a Beszédfelismerés szolgáltatásnak támogatnia kell. Jelenleg csak a következő konfiguráció támogatott:

  Hangminták PCM formátumban, egy csatorna, 16000 minta másodpercenként, 32000 bájt másodpercenként, két blokk igazítás (16 bit, beleértve a minta kitöltését), mintánként 16 bit.

  Az sdk megfelelő kódja az audioformátum létrehozásához így néz ki:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Győződjön meg arról, hogy a kód képes biztosítani a RAW hangadatokat ezen előírásoknak megfelelően. Ha a hangforrás-adatok nem egyeznek meg a támogatott formátumokkal, a hangot át kell kódolni a kívánt formátumra.

- Hozzon létre saját hangbemeneti adatfolyam-osztályt a rendszerből. `PullAudioInputStreamCallback` Hajtsa `Close()` végre a és a `Read()` tagok. A pontos függvényaláírás nyelvfüggő, de a kód hasonló lesz ehhez a kódmintához:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Hozzon létre egy hangkonfigurációt a hangformátum és a bemeneti adatfolyam alapján. A felismerő létrehozásakor adja át a szokásos beszéd- és hangbemeneti konfigurációt is. Példa:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A beszéd felismerése C-ben #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
