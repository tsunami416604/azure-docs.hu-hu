---
title: Több résztvevős beszélgetések átírása a Speech SDK-Speech szolgáltatással
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a Speech SDK-val a társalgási átírást. Elérhető a C++, C#, és Javához.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 5ad912b1cee5495e18a5eb2da4d981eadd74dd7d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066426"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Több résztvevős beszélgetések átírása a Speech SDK-val

A Speech SDK **ConversationTranscriber** API-jával olyan értekezleteket és beszélgetéseket lehet átírni, amelyek lehetővé teszik a résztvevők `PullStream` `PushStream`hozzáadását, eltávolítását és azonosítását.

## <a name="limitations"></a>Korlátozások

* A beszélgetési átirat a C++, C#a és a Java Windows, Linux és Android rendszereken támogatott.
* A ROOBO fejlesztői készlet a támogatott hardveres környezet a beszélgetési átiratok létrehozásához, amely körkörös, több mikrofonos tömböt biztosít, amely hatékonyan használható a hangszórók azonosításához. [További információ: Speech Devices SDK](speech-devices-sdk.md).
* Az átírást bemutató beszédfelismerési SDK-támogatás a lekéréses és leküldéses üzemmódra korlátozódik, amely 8 csatornás 16 bites 16 kHz-es PCM-hanggal rendelkezik. Jelenleg csak az alábbi készletek támogatottak a 8 csatornás hangrögzítéshez:
   * [ROOBO intelligens audio kör alakú 7 – MIC DK](https://ddk.roobo.com/)
   * [Azure Kinect DK](https://azure.microsoft.com/en-in/services/kinect-dk/).
* A beszélgetés átírása jelenleg az "en-US" és a "zh-CN" nyelveken érhető el a következő régiókban: CentralUS és eastasia.

## <a name="prerequisites"></a>Előfeltételek

* [Megtudhatja, hogyan használhatja a beszédfelismerést a beszédfelismerési SDK-val.](quickstart-csharp-dotnet-windows.md)
* [A beszédfelismerési próbaverziós előfizetés beszerzése.](https://azure.microsoft.com/try/cognitive-services/)
* A Speech SDK 1.5.1-es vagy újabb verziója szükséges.

## <a name="create-voice-signatures-for-participants"></a>Hangaláírások létrehozása a résztvevők számára

Első lépésként létre kell hoznia a beszélgetés résztvevőinek hang-aláírásait. A hatékony hangfelismeréshez hangaláírások létrehozására van szükség.

### <a name="requirements-for-input-wave-file"></a>A bemeneti Wave-fájlra vonatkozó követelmények

* A hangaláírások létrehozásához használt bemeneti hanghullám-fájlnak 16 bites mintákban, 16 kHz-es mintavételi sebességgel és egy egycsatornás (monó) formátumban kell lennie.
* Az egyes hangmintákhoz javasolt hossz 30 másodperc és két perc között lehet.

Az alábbi példa két különböző módszert mutat be a hangaláírások létrehozásához [a REST API használatával](https://aka.ms/cts/signaturegenservice) C#:

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

## <a name="transcribing-conversations"></a>Beszélgetések átirata

A több résztvevővel folytatott beszélgetések átírásához `ConversationTranscriber` hozza létre a beszélgetési munkamenethez létrehozott objektumhoz társított `AudioConfig` objektumot, illetve a vagy `PullAudioInputStream` `PushAudioInputStream`a használatával továbbított hanganyagot.

Tegyük fel, hogy rendelkezik egy nevű `MyConversationTranscriber`ConversationTranscriber osztállyal. A kód így néz ki:

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
> [A GitHubon található minták megismerése](https://aka.ms/csspeech/samples)
