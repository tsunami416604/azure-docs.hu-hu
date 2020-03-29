---
title: Aszinkron beszélgetés átírása (előzetes verzió) - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az aszinkron beszélgetésátírást a Beszédszolgáltatás használatával. Csak Java-ban érhető el.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 57543f4a3779145ce66259eec1abac195b63c7ba
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384296"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Aszinkron beszélgetés átírása (előzetes verzió)

Ebben a cikkben az aszinkron beszélgetés átírása a **RemoteConversationTranscriptionClient** API használatával látható. Ha konfigurálta a beszélgetés átírását, hogy aszinkron átírás, és van egy, `conversationId`beszerezheti az átírás társított `conversationId` **remoteconversationtranscriptionclient** API-t.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Aszinkron vs. valós idejű + aszinkron

Az aszinkron átírással streamelheti a beszélgetés hangját, de nincs szüksége valós időben visszaküldött átírásra. Ehelyett a hang elküldése után `conversationId` `Conversation` használja a lekérdezés az aszinkron átírás állapotát. Amikor az aszinkron átírás készen áll, a `RemoteConversationTranscriptionResult`.

A valós idejű és aszinkron, megkapja az átírás valós időben, hanem kap az átírás `conversationId` lekérdezésével a (hasonló aszinkron forgatókönyv).

Az aszinkron átírás végrehajtásához két lépés szükséges. Az első lépés az, hogy töltse fel a hang, választotta vagy csak aszinkron vagy valós idejű plusz aszinkron. A második lépés az, hogy az átírás eredményeit.

## <a name="upload-the-audio"></a>A hang feltöltése

Az aszinkron átírás végrehajtása előtt el kell küldenie a hangot a Beszélgetésátírási szolgáltatásnak a Microsoft Cognitive Speech kliens SDK (1.8.0-s vagy újabb verzió) használatával.

Ez a példakód bemutatja, hogyan hozhat létre beszélgetés-átírót csak aszinkron módban. Annak érdekében, hogy a hang az átíró, meg kell adni audio streaming kódot származó [átírás beszélgetések valós időben a Speech SDK](how-to-use-conversation-transcription-service.md). A támogatott platformok és nyelvek API-k megtekintéséhez tekintse meg a témakör **Korlátozások** című szakaszát.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Ha azt szeretnénk, valós idejű _plusz_ aszinkron, megjegyzést, és uncomment a megfelelő kódsorokat az alábbiak szerint:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Transzkripciós eredmények beolvasása

Ez a lépés megkapja az aszinkron átíráseredményeit, de feltételezi, hogy minden olyan valós idejű feldolgozás, amelyet esetleg szükséges, máshol történik. További információ: [A beszélgetések valós idejű átírása a beszédbeszéd SDK-val](how-to-use-conversation-transcription-service.md)című témakörben található.

Az itt látható kódhoz az **1.8.0 távoli beszélgetésre**, amelyet csak Java (1.8.0 vagy újabb) támogat Windows, Linux és Android rendszeren (csak 26-os vagy újabb API-szint).

### <a name="obtaining-the-client-sdk"></a>Az ügyfél SDK-jának beszerzése

A **távoli beszélgetést** a pom.xml fájl szerkesztésével szerezheti be az alábbiak szerint.

1. A fájl végén, a záró `</project>`címke előtt `repositories` hozzon létre egy elemet, amely a Beszéd SDK Maven tárházára hivatkozik:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Is adjon `dependencies` hozzá egy elemet, a remoteconversation-client-sdk 1.8.0 függőségként:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. A módosítások mentése

### <a name="sample-transcription-code"></a>Mintaátírás kódja

Miután a `conversationId`, hozzon létre egy távoli beszélgetés átírása ügyfél **RemoteConversationTranscriptionClient** az ügyfélalkalmazásban az aszinkron átírás állapotának lekérdezéséhez. [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) objektum leolvasásához használja **a(z) GetTranscriptionOperation** metódust a **RemoteConversationTranscriptionClient** programban. A PollerFlux objektum információt kap a **RemoteConversationTranscriptionOperation** távoli műveletállapotáról és a **RemoteConversationTranscriptionResult**végeredményről. Miután a művelet befejeződött, **a(z) RemoteConversationTranscriptionResult** get **getFinalResult** metódus [tikaszóra](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java)történő hívásával kapja meg a getFinalResult metódust. Ebben a kódban egyszerűen kinyomtatjuk az eredmény tartalmát a rendszer kimenetére.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fedezze fel mintáinkat a GitHubon](https://aka.ms/csspeech/samples)
