---
title: A beszédfelismerés SDK – beszédszolgáltatások több résztvevő beszélgetések alapuló átírás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan beszélgetés Beszédátírási használata a Speech SDK-val. Elérhető C++, C#, és a Java használatával.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: 80ec606fee30c239d47bca94188d3b9cbb7c82d5
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604413"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>A beszédfelismerés SDK-val több résztvevő beszélgetések alapuló átírás

A beszédfelismerés SDK **ConversationTranscriber** API lehetővé teszi lehetővé teszi az hozzáadása, eltávolítása és azonosíthatja a résztvevők a beszédszolgáltatások történő hang streamelési értekezletek/beszélgetések lefényképezze `PullStream` vagy `PushStream`.

## <a name="limitations"></a>Korlátozások

* Beszélgetés transcriber támogatott C++, C#, és a Java, a Windows, Linux- és Android.
* A ROOBO DevKit a támogatott hardveres környezetet biztosít, amellyel. kör alakú több mikrofon tárolótömbök hatékonyan számára a beszélő felismerése beszélgetés beszédátírás készítéséhez. [További információkért tekintse meg a beszédfelismerés Devices SDK-val](speech-devices-sdk.md).
* Beszéd SDK támogatása beszélgetés beszédátírási hang lekéréses felhasználása, és küldje le a 16 bites 16 kHz PCM hanganyagra nyolc csatornák mód Streamek korlátozódik.
* Beszélgetés Beszédátírási jelenleg nyelveken is elérhető "en-US" és "zh-CN" a következő régiókban: centralus és eastasia.

## <a name="prerequisites"></a>Előfeltételek

* [Ismerje meg, hogyan használja a hang-szöveg transzformációs beszéd SDK-val.](quickstart-csharp-dotnet-windows.md)
* [A beszédfelismerés próba-előfizetés beszerzése.](https://azure.microsoft.com/try/cognitive-services/)
* Beszéd SDK 1.5.1 verzió vagy újabb verzió szükséges.

## <a name="create-voice-signatures-for-participants"></a>A résztvevők hangalapú aláírások létrehozása

Az első lépéseként a beszélgetés résztvevők hangalapú aláírások létrehozásához. Hangalapú aláírások létrehozásához kötelező megadni a hatékony beszélő felismerése.

### <a name="requirements-for-input-wave-file"></a>A bemeneti hangfájl követelményei

* A bemeneti audio hangfájl hangalapú aláírások létrehozásához a 16 bites minták, 16 kHz mintavételi gyakoriság és a egy egyetlen csatornát (Mono) formátumot kell.
* Az ajánlott minden egyes hang minta hossza 30 másodperc és két perc között.

A következő példában két különböző módon hozhat létre hangalapú aláírása [a REST API használatával.] (https://aka.ms/cts/signaturegenservice) a C#:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>Beszélgetések átírás

Lefényképezze több résztvevők beszélgetést, hozzon létre a `ConversationTranscriber` objektum, amely társítva van a `AudioConfig` a beszélgetés munkamenet és a stream hang használatával létrehozott objektum `PullAudioInputStream` vagy `PushAudioInputStream`.

Tegyük fel, hogy rendelkezik-e nevű ConversationTranscriber osztály `MyConversationTranscriber`. A kód előfordulhat, hogy néznek ki:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
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

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document. 
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg a minták a Githubon](https://aka.ms/csspeech/samples)
