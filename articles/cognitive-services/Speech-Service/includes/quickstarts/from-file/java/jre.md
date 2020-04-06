---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: a33bc7dd59f8f5ac04d9d016c11e44efa438158d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671519"
---
## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Ha új üres osztályt szeretne hozzáadni a Java-projekthez, válassza az**Új** > **osztály** **fájlja** > lehetőséget.

1. A **New Java Class** (Új Java-osztály) ablakban írja be a **speechsdk.quickstart** kifejezést a **Package** (Csomag), a **Main** (Fő) kifejezést pedig a **Név** mezőbe.

   ![A New Java Class varázsló képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Cserélje le a `Main.java` összes kódját az alábbi kódrészletre:

   ```java
   package speechsdk.quickstart;

   import java.util.concurrent.Future;
   import com.microsoft.cognitiveservices.speech.*;

   /**
    * Quickstart: recognize speech using the Speech SDK for Java.
    */
   public class Main {

       /**
        * @param args Arguments are ignored in this sample.
        */
       public static void main(String[] args) {
           try {
               // Replace below with your own subscription key
               String speechSubscriptionKey = "YourSubscriptionKey";

               // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
               String serviceRegion = "YourServiceRegion";

               // Replace below with your own filename.
               String audioFileName = "whatstheweatherlike.wav";

               int exitCode = 1;
               SpeechConfig config = SpeechConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
               assert(config != null);

               AudioConfig audioInput = AudioConfig.fromWavFileInput(audioFileName);
               assert(audioInput != null);

               SpeechRecognizer reco = new SpeechRecognizer(config, audioInput);
               assert(reco != null);

               System.out.println("Recognizing first result...");

               Future<SpeechRecognitionResult> task = reco.recognizeOnceAsync();
               assert(task != null);

               SpeechRecognitionResult result = task.get();
               assert(result != null);

               switch (result.getReason()) {
                   case ResultReason.RecognizedSpeech: {
                           System.out.println("We recognized: " + result.getText());
                           exitCode = 0;
                       }
                       break;
                   case ResultReason.NoMatch:
                       System.out.println("NOMATCH: Speech could not be recognized.");
                       break;
                   case ResultReason.Canceled: {
                           CancellationDetails cancellation = CancellationDetails.fromResult(result);
                           System.out.println("CANCELED: Reason=" + cancellation.getReason());
        
                           if (cancellation.getReason() == CancellationReason.Error) {
                               System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                               System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                               System.out.println("CANCELED: Did you update the subscription info?");
                           }
                       }
                       break;
               }

               reco.close();

               System.exit(exitCode);
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());

               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Cserélje le `whatstheweatherlike.wav` a karakterláncot a saját fájlnevére.

1. Mentse a projekt módosításait.

> [!NOTE]
> A beszédsdk alapértelmezés szerint felismeri a nyelv en-us használatát, olvassa el a forrásnyelv megadása a [beszéd szöveghez című témakört](../../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

Nyomja le az F11 billentyűt, vagy válassza**a Debug futtatása** **lehetőséget.** > 
A hangfájlból származó beszédbevitel első 15 másodperceit a rendszer felismeri és beírja a konzol ablakába.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]