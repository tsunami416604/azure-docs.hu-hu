---
title: Valós idejű beszélgetésátírás (előzetes verzió) – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja a valós idejű beszélgetésátírást a beszédfelismerési SDK-val. C++, C# és Java esetén érhető el.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 64a9e11cec7164fb4421dd018238de9f0670382b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76263727"
---
# <a name="real-time-conversation-transcription-preview"></a>Valós idejű beszélgetésátírás (előzetes verzió)

A Speech SDK **ConversationTranscriber** API-ja lehetővé teszi értekezletek és egyéb beszélgetések átírását több résztvevő hozzáadásával, eltávolításával `PushStream`és azonosításával a hang nak a beszédfelismerési szolgáltatásba történő streamelésével a használatával. `PullStream` Ez a témakör megköveteli, hogy tudja, hogyan használhatja a beszéd-szöveg et a beszédfelismerési SDK-val (1.8.0-s vagy újabb verzió). További információ: [Mik azok a beszédszolgáltatások.](overview.md)

## <a name="limitations"></a>Korlátozások

- A ConversationTranscriber API-t Támogatja a C++, C#, és Java Windows, Linux és Android rendszeren.
- Jelenleg "en-US" és "zh-CN" nyelveken érhető el a következő régiókban: _centralus_ és _eastasia_.
- 7-mic kör alakú, többmikrofonos tömbre van szükség, lejátszási referenciaadatfolyammal. A mikrofontömbnek meg kell [felelnie a specifikációnknak.](https://aka.ms/sdsdk-microphone)
- A [beszédeszközök SDK](speech-devices-sdk.md) megfelelő eszközöket és egy mintaalkalmazást biztosít, amely bemutatja a beszélgetésátírást.

## <a name="optional-sample-code-resources"></a>Választható mintakód-erőforrások

A Beszédeszköz SDK mintakódot biztosít Java-ban a valós idejű hangrögzítéshez 8 csatorna használatával.

- [ROOBO eszköz mintakódja](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Az Azure Kinect Dev Kit mintakódja](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Előfeltételek

Egy beszédszolgáltatás-előfizetés. Ha még nem rendelkezik [beszédpróba-előfizetéssel, akkor kaphat beszédpróba-előfizetést.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures"></a>Hangaláírások létrehozása

Az első lépés az, hogy hozzon létre hangaláírásokat a beszélgetés résztvevői számára a hatékony hangszóró-azonosítás érdekében.

### <a name="audio-input-requirements"></a>Audio bemeneti követelmények

- A hangaláírások létrehozásához a bemeneti hanghullámfájlnak 16 bites mintákban, 16 kHz-es mintavételi sebességben és egycsatornás (monó) formátumban kell lennie.
- Az egyes hangminták ajánlott hossza 30 másodperc és 2 perc között van.

### <a name="sample-code"></a>Mintakód

A következő példa két különböző módon hozhat létre hangaláírás t a REST API c#-ban [használatával.](https://aka.ms/cts/signaturegenservice) Vegye figyelembe, hogy a valós információkat a "YourSubscriptionKey"-ra, a "speakerVoice.wav" `{region}` nevű hullámfájl nevét, valamint a "YourServiceRegion"_(centralus_ vagy eastasia) régiót kell _helyettesítenie._

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Beszélgetések átírása

A következő mintakód bemutatja, hogyan lehet a beszélgetéseket valós időben átírni három hangszóró esetében. Feltételezi, hogy már létrehozott hangaláírásokat minden hangszóróhoz, ahogy az a fentiekben látható. A "YourSubscriptionKey" és a "YourServiceRegion" valódi információk helyett a SpeechConfig objektum létrehozásakor.

A mintakód kiemelt elemei a következők:

- `Conversation` Objektum létrehozása az `SpeechConfig` objektumból a használatával létrehozott értekezletazonosító használatával`Guid.NewGuid()`
- Objektum `ConversationTranscriber` létrehozása és a `JoinConversationAsync()` beszélgetéshez való csatlakozás az átírás elindításához
- Az érdekes események regisztrálása
- Résztvevők hozzáadása vagy eltávolítása a beszélgetéshez a Beszélgetés objektum mal
- A hang streamelése
- A Speech SDK 1.9.0-s `int` verziójában és atől, mind az `string` értéktípusokat támogatja a hangaláírás verziószáma.

Az átírás és a hangszóró azonosítója a regisztrált eseményekben található.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Aszinkron beszédátírás](how-to-async-conversation-transcription.md)
