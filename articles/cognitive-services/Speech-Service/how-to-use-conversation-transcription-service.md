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
ms.date: 05/06/2019
ms.author: jhakulin
ms.openlocfilehash: e9de4faf18c54f7c7582ef5a8ab0648629d4f48e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190154"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>A beszédfelismerés SDK-val több résztvevő beszélgetések alapuló átírás

A beszédfelismerés SDK **ConversationTranscriber** API lehetővé teszi lehetővé teszi az hozzáadása, eltávolítása és azonosíthatja a résztvevők a beszédszolgáltatások történő hang streamelési értekezletek/beszélgetések lefényképezze `PullStream` vagy `PushStream`.

## <a name="limitations"></a>Korlátozások

* Beszélgetés transcriber támogatott C++, C#, és a Java, a Windows, Linux- és Android.
* A ROOBO DevKit a támogatott hardveres környezet, beszélgetések létrehozásához, amely. kör alakú több mikrofon tömb, amely a beszélő felismerése a beszélgetés Beszédátírási szolgáltatás hatékonyan, amellyel biztosít. [További információkért tekintse meg a beszédfelismerés Devices SDK-val](speech-devices-sdk.md).
* Beszéd SDK támogatás hang lekéréses felhasználása és leküldési módban Streamek PCM hanganyagra nyolc csatornák korlátozódik.

## <a name="prerequisites"></a>Előfeltételek

* [Ismerje meg, hogyan használja a hang-szöveg transzformációs beszéd SDK-val.](quickstart-csharp-dotnet-windows.md)
* [A beszédfelismerés próba-előfizetés beszerzése.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures-for-participants"></a>A résztvevők hangalapú aláírások létrehozása

Az első lépéseként a beszélgetés résztvevők hangalapú aláírások létrehozásához. Hangalapú aláírások létrehozásához kötelező megadni a hatékony beszélő felismerése.
Az alábbi minta, küldünk egy [a REST API használatával beszédfelismerési aláírását.](https://aka.ms/cts/signaturegenservice)

Az alábbi példában látható két különböző módon hangalapú aláírások létrehozásához:
```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.signature.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature can be extracted from the jsonData
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature can be extracted from the jsonData
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
    private static string endpoint = "YourOwnEndpoint";

    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own endpoint and subscription key.
        var config = SpeechConfig.FromEndpoint(new Uri(endpoint), "YourSubScriptionKey");
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
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, SpeakerID={e.Result.SpeakerId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create data for voice signatures using REST API described in the earlier section in this document.
                // How to create voice signatureA, signatureB & signatureC variables, please check the SDK API samples.

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
