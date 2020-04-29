---
title: Aszinkron beszélgetés átírása (előzetes verzió) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az aszinkron beszélgetéseket a Speech Service használatával. Csak Java esetén érhető el.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 57543f4a3779145ce66259eec1abac195b63c7ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80384296"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Aszinkron beszélgetés átírása (előzetes verzió)

Ebben a cikkben az aszinkron beszélgetések átírását mutatjuk be a **RemoteConversationTranscriptionClient** API használatával. Ha úgy konfigurálta a beszélgetési átírást `conversationId`, hogy aszinkron átírást végezzen, a **RemoteConversationTranscriptionClient** API- `conversationId` val társított átírást is beszerezheti.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Aszinkron és valós idejű + aszinkron

Az aszinkron átírással továbbíthatja a beszélgetés hangját, de a valós időben nem kell visszaadnia az átírást. Ehelyett a hang elküldése után használja `conversationId` `Conversation` a parancsot az aszinkron átírás állapotának lekérdezéséhez. Ha az aszinkron átírás elkészült, megkapja a `RemoteConversationTranscriptionResult`.

A valós idejű és aszinkron megoldásokkal valós időben érheti el az átírást, az átírást pedig a `conversationId` (az aszinkron forgatókönyvhöz hasonlóan) lekérdezéssel is lekérdezheti.

Az aszinkron átírás végrehajtásához két lépés szükséges. Első lépésként töltse fel a hangot, válassza a csak aszinkron vagy a valós idejű és az aszinkron lehetőséget. A második lépés az átirat eredményeinek beolvasása.

## <a name="upload-the-audio"></a>A hang feltöltése

Az aszinkron átírás végrehajtása előtt el kell küldenie a hang-és beszélgetés-átírási szolgáltatást a Microsoft kognitív Speech Client SDK (1.8.0 vagy újabb verzió) használatával.

Ez a mintakód azt mutatja be, hogyan hozható létre a beszélgetési átirat a csak aszinkron üzemmódhoz. Ahhoz, hogy hangot továbbítson az átiratoknak, [a SPEECH SDK-val valós időben](how-to-use-conversation-transcription-service.md)kell felvennie a hangos adatfolyam-kódot, amelyből a beszédfelismerési beszélgetésekből származik. A támogatott platformok és nyelvek API-k megtekintéséhez tekintse meg a jelen témakör **korlátozások** című szakaszát.

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

Ha azt szeretné, hogy a valós idejű _és_ az aszinkron, a megjegyzések és a megjegyzések a megfelelő sorokban legyenek megadva a következő módon:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Átirat eredményeinek beolvasása

Ez a lépés beolvassa az aszinkron átírási eredményeket, de feltételezi, hogy az esetlegesen szükséges valós idejű feldolgozás máshol történik. További információ: beszélgetések átírása [valós időben a SPEECH SDK-val](how-to-use-conversation-transcription-service.md).

Az itt látható kód esetében szükség van a **Remote-beszélgetés 1.8.0 verziójára**, amely csak a Java (1.8.0 vagy újabb verzió) esetén támogatott Windows, Linux és Android rendszeren (csak az API 26-os vagy újabb verziójában).

### <a name="obtaining-the-client-sdk"></a>Az ügyfél-SDK beszerzése

A következő módon állíthatja be a **távoli beszélgetést** a Pom. xml fájl szerkesztésével.

1. A fájl végén a záró címke `</project>`előtt hozzon létre egy `repositories` elemet, amely a Speech SDK Maven-tárházára hivatkozik:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Vegyen fel `dependencies` egy elemet is a remoteconversation-Client-SDK 1.8.0-függőségként:

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

### <a name="sample-transcription-code"></a>Példa transzkripciós kód

Miután létrehozta a `conversationId`-t, hozzon létre egy távoli beszélgetési **RemoteConversationTranscriptionClient** az ügyfélalkalmazás számára az aszinkron átírás állapotának lekérdezéséhez. [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) -objektum beszerzéséhez használja a **RemoteConversationTranscriptionClient** **getTranscriptionOperation** metódusát. A PollerFlux objektum információkkal fog rendelkezni a távoli műveleti állapot **RemoteConversationTranscriptionOperation** és a végső eredmény **RemoteConversationTranscriptionResult**. A művelet befejezését követően a GetFinalResult **RemoteConversationTranscriptionResult** hívja meg **getFinalResult** a [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). Ebben a kódban egyszerűen kinyomtatjuk az eredmény tartalmát a rendszer kimenetére.

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
> [A GitHubon található minták megismerése](https://aka.ms/csspeech/samples)
