---
title: Valós idejű beszélgetés átirata (előzetes verzió) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható a valós idejű beszélgetés a Speech SDK-val. Elérhető a C++, C#, és Javához.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 93f0117096a5601632ccced6b698e84a0714bbd4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805807"
---
# <a name="real-time-conversation-transcription-preview"></a>Valós idejű beszélgetés átirata (előzetes verzió)

A Speech SDK **ConversationTranscriber** API-jával több résztvevő felvételét, eltávolítását és azonosítását is lehetővé teheti, hogy a hang-és hangvételi szolgáltatást `PullStream` vagy `PushStream`használatával közvetíti a beszédfelismerési szolgáltatásnak. Ebből a témakörből megtudhatja, hogyan használható a Speech-Text a Speech SDK-val (1.8.0 vagy újabb verzió). További információ: [Mi a Speech Services](overview.md).

## <a name="limitations"></a>Korlátozások

- A ConversationTranscriber API-t a C++, C#a és a Java is támogatja Windows, Linux és Android rendszereken.
- Jelenleg a következő régiókban érhető el az "en-US" és a "zh-CN" nyelveken: _CentralUS_ és _eastasia_.
- 7 mikrofonos körkörös multi-mikrofonos tömböt igényel egy lejátszási hivatkozási adatfolyammal. A mikrofon tömbnek meg kell felelnie a [specifikációnak](https://aka.ms/sdsdk-microphone).
- A [Speech Devices SDK](speech-devices-sdk.md) megfelelő eszközöket és egy minta alkalmazást biztosít, amely a beszélgetés átírását szemlélteti.

## <a name="optional-sample-code-resources"></a>Nem kötelező mintakód-erőforrások

A Speech Device SDK a Javaban a valós idejű hangrögzítéshez 8 csatornán keresztül biztosít mintakód-kódot.

- [ROOBO-eszköz mintakód](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Azure Kinect dev Kit mintakód](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Előfeltételek

Beszédfelismerési szolgáltatás előfizetése. Ha még nem rendelkezik [előfizetéssel, lekérheti a beszédfelismerési próbaverziót](https://azure.microsoft.com/try/cognitive-services/) .

## <a name="create-voice-signatures"></a>Hangaláírások létrehozása

Első lépésként létre kell hoznia a beszélgetés résztvevőinek hangaláírásait a hatékony beszélők azonosításához.

### <a name="audio-input-requirements"></a>Hangbeviteli követelmények

- A hangaláírások létrehozásához használt bemeneti hanghullám-fájlnak 16 bites mintákban, 16 kHz-es mintavételi sebességgel és egyetlen csatornás (mono) formátumban kell lennie.
- Az egyes hangminták javasolt hossza harminc másodperc és két perc között lehet.

### <a name="sample-code"></a>Mintakód

Az alábbi példa két különböző módszert mutat be a hangaláírások létrehozásához [a REST API használatával](https://aka.ms/cts/signaturegenservice) C#. Vegye figyelembe, hogy a "YourSubscriptionKey", a "speakerVoice. wav" fájlhoz tartozó Wave-fájlnevet, valamint a `{region}` és a "YourServiceRegion" (_CentralUS_ vagy _eastasia_) régiójának valós adatait kell helyettesítenie.

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
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
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

## <a name="transcribe-conversations"></a>Beszélgetések átirata

Az alábbi mintakód bemutatja, hogyan lehet valós időben átírni a beszélgetéseket három beszélő számára. Feltételezi, hogy már létrehozott egy hangaláírást az egyes hangszórókhoz a fent látható módon. A SpeechConfig objektum létrehozásakor a "YourSubscriptionKey" és a "YourServiceRegion" valódi információit helyettesítse be.

A mintakód Kiemelt jellemzői a következők:

- `Conversation` objektum létrehozása a `SpeechConfig` objektumból a következő használatával generált Meeting-azonosító használatával: `Guid.NewGuid()`
- `ConversationTranscriber` objektum létrehozása és a beszélgetéshez való csatlakozás a `JoinConversationAsync()` az átírás indításához
- A fontos események regisztrálása
- Résztvevők hozzáadása vagy eltávolítása a beszélgetéshez a beszélgetési objektum használatával
- A hang továbbítása

Az átírás és a hangszóró azonosítója visszakerül a regisztrált eseményekre.

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
            using (var conversation = new Conversation(config, meetingId))
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
                    // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Aszinkron beszélgetés átírása](how-to-async-conversation-transcription.md)
