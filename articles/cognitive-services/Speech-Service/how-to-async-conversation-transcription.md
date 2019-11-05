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
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506977"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Aszinkron beszélgetés átírása (előzetes verzió)

Ebben a cikkben az aszinkron beszélgetések átírását mutatjuk be a **RemoteConversationTranscriptionClient** API használatával. Ha úgy konfigurálta a beszélgetési átírást, hogy aszinkron átírást végezzen, és rendelkezik egy `conversationId`, akkor a **RemoteConversationTranscriptionClient** API használatával szerezheti be a `conversationId` társított átírást.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Aszinkron és valós idejű + aszinkron

Az aszinkron átírással továbbíthatja a beszélgetés hangját, de a valós időben nem kell visszaadnia az átírást. Ehelyett a hang elküldése után a `ConversationTranscriber` `conversationId` használatával kérdezheti le az aszinkron átírás állapotát. Ha az aszinkron átírás elkészült, `RemoteConversationTranscriptionResult`fog kapni.

A valós idejű és aszinkron megoldásokkal valós időben szerezheti be az átírást, de a `conversationId` lekérdezésével is lekérdezheti az átírást (az aszinkron forgatókönyvhöz hasonlóan).

Az aszinkron átírás végrehajtásához két lépés szükséges. Első lépésként töltse fel a hangot, válassza a csak aszinkron vagy a valós idejű és az aszinkron lehetőséget. A második lépés az átirat eredményeinek beolvasása.

## <a name="upload-the-audio"></a>A hang feltöltése

Az aszinkron átírás végrehajtása előtt el kell küldenie a hangot a beszélgetés átírásához a Microsoft kognitív beszéd Client SDK (1.8.0 vagy újabb verzió) használatával.

Ez a mintakód azt mutatja be, hogyan hozható létre a beszélgetési átirat a csak aszinkron üzemmódhoz. Ahhoz, hogy hangot továbbítson az átiratoknak, [a SPEECH SDK-val valós időben](how-to-use-conversation-transcription-service.md)kell felvennie a hangos adatfolyam-kódot, amelyből a beszédfelismerési beszélgetésekből származik. A támogatott platformok és nyelvek API-k megtekintéséhez tekintse meg a jelen témakör **korlátozások** című szakaszát.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
...
```

Ha azt szeretné, hogy a valós idejű _és_ az aszinkron, a megjegyzések és a megjegyzések a megfelelő sorokban legyenek megadva a következő módon:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Átirat eredményeinek beolvasása

Ez a lépés beolvassa az aszinkron átírási eredményeket, de feltételezi, hogy az esetlegesen szükséges valós idejű feldolgozás máshol történik. További információ: beszélgetések átírása [valós időben a SPEECH SDK-val](how-to-use-conversation-transcription-service.md).

Az itt látható kód esetében a **remoteconversation-Client-SDK 1.0.0 verzióra**van szükség, amely csak a Java (1,8-es vagy újabb) esetében támogatott Windows, Linux és Android rendszeren (API-szint 26 vagy újabb).

### <a name="obtaining-the-client-sdk"></a>Az ügyfél-SDK beszerzése

A **remoteconversation-Client-SDK** beszerzéséhez szerkessze a Pom. xml fájlt a következőképpen.

- A fájl végén a záró címke `</project>`előtt hozzon létre egy `repositories` elemet, amely a Speech SDK Maven-tárházára hivatkozik:

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- Vegyen fel egy `dependencies` elemet is a remoteconversation-Client-SDK 1.0.0-függőségként:

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- A módosítások mentése

### <a name="sample-transcription-code"></a>Példa transzkripciós kód

Miután elvégezte a `conversationId`, hozzon létre egy távoli műveleti objektum **RemoteConversationTranscriptionOperation** az ügyfélen az aszinkron átírás állapotának lekérdezéséhez. A **RemoteConversationTranscriptionOperation** ki van terjesztve a [pollerből](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java). A Poller befejezését követően a **RemoteConversationTranscriptionResult** -re való feliratkozással, valamint az objektum lekérdezésével kaphat segítséget. Ebben a kódban egyszerűen kinyomtatjuk az eredmény tartalmát a rendszer kimenetére.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
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
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A GitHubon található minták megismerése](https://aka.ms/csspeech/samples)
