---
title: 'Gyors útmutató: Egyéni hang-és felhőközpontú virtuális asszisztensek (előzetes verzió), a Java (Windows, Linux) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban fog megtudhatja, hogyan használható a Cognitive Services beszédfelismerő szoftver Development Kit (SDK) egy Java-konzolalkalmazást. Megtudhatja, hogyan csatlakoztathatja az ügyfélalkalmazás egy korábban létrehozott Bot Framework bot konfigurálva a közvetlen vonal beszédfelismerő csatornát használja, és a egy hang-és felhőközpontú virtuális asszisztensek felület engedélyezéséhez.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027018"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Gyors útmutató: Hozzon létre egy hang-és felhőközpontú virtuális asszisztensek a Speech SDK-Java

Ez a cikk segítségével létrehozhat egy Java-konzolalkalmazást a [Cognitive Services beszédfelismerő SDK](speech-sdk.md). Az alkalmazás egy korábban létrehozott bot konfigurálva a közvetlen vonal beszédfelismerő csatornát használja, a hangalapú kérés küldése és (Ha be van állítva), adja vissza a voice-tevékenységet fog csatlakozni. Az alkalmazást a létrehozása a Speech SDK Maven-csomag és az Eclipse a Java IDE Windows, Ubuntu Linux vagy macOS rendszeren. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Operációs rendszer: (64 bites) Windows, Ubuntu Linux 16.04/18.04 (64 bites) vagy a macOS 10.13 vagy újabb
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).
* A Bot Framework 4.2-es verzió használatával létrehozott előre konfigurált robot vagy újabb. A robot hangalapú bemenetek fogadni az új "A közvetlen vonal Speech" csatorna feliratkozás kellene.

    > [!NOTE]
    > Előzetes verzióban elérhető a közvetlen vonal beszédfelismerő csatornát jelenleg csak támogatja a **westus2** régióban.

    > [!NOTE]
    > A 30 napos próbaverziójára, a standard tarifacsomag ismertetett [próbálja ki ingyenesen beszédszolgáltatások](get-started.md) korlátozódik **westus** (nem **westus2**) és így nem kompatibilis a közvetlen Sor Speech. Ingyenes és standard szintje **westus2** előfizetések kompatibilisek.

Ha Ubuntu 16.04/18.04 rendszert használ, győződjön meg arról, ezek a függősége telepítve van az Eclipse indítása előtt:

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Ha Windows (64 bites) futtat, győződjön meg arról, telepítette a Microsoft Visual C++ terjeszthető változatát a platformhoz:
* [A Microsoft Visual C++ terjeszthető csomag Visual Studio 2017 letöltése](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Nem kötelező: A gyors kezdéshez

Ez a rövid útmutató azt ismerteti, lépésről lépésre, hogyan, hogy egy egyszerű ügyfélalkalmazás beszédfeldolgozó robotjait csatlakozni. Ha inkább megvásárolná, a rövid útmutatóban használt teljes körű, készen áll-fordítási forráskód érhető el a [beszéd SDK-minták](https://aka.ms/csspeech/samples) alatt a `quickstart` mappát.

## <a name="create-and-configure-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Emellett a naplózás engedélyezéséhez frissítse a **pom.xml** -fájl a következő függőséget.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Ha új, üres osztályt szeretne hozzáadni a Java-projekthez, válassza a **File** > **New** > **Class** (Fájl, Új, Osztály) lehetőséget.

1. A **New Java Class** (Új Java-osztály) ablakban írja be a **speechsdk.quickstart** kifejezést a **Package** (Csomag), a **Main** (Fő) kifejezést pedig a **Név** mezőbe.

   ![A New Java Class varázsló képernyőképe](media/sdk/qs-java-jre-06-create-main-java.png)

1. Nyissa meg az újonnan létrehozott **fő** osztályt, és cserélje le a tartalmát a `Main.java` kiindulási kódot a következő fájl.

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. Az a **fő** metódus, akkor először konfigurálja a `BotConnectorConfig` , és ezzel hozzon létre egy `SpeechBotConnector` példány. Ez a közvetlen vonal beszédfelismerő csatornát a robottal lépnek kapcsolatba fog csatlakozni. Egy `AudioConfig` példány is használható a hangbemeneti forrást adja meg. Ebben a példában az alapértelmezett mikrofon együttes `AudioConfig.fromDefaultMicrophoneInput()`.

    * Cserélje le a karakterláncot `YourSubscriptionKey` az előfizetési kulcs, amely kérhet le a [Itt](get-started.md).
    * Cserélje le a karakterláncot `YourServiceRegion` együtt a [régió](regions.md) az Ön előfizetéséhez rendelve.
    * Cserélje le a karakterláncot `YourChannelSecret` a közvetlen vonal beszédfelismerő csatornát titokkal.

    > [!NOTE]
    > Előzetes verzióban elérhető a közvetlen vonal beszédfelismerő csatornát jelenleg csak támogatja a **westus2** régióban.

    > [!NOTE]
    > A 30 napos próbaverziójára, a standard tarifacsomag ismertetett [próbálja ki ingyenesen beszédszolgáltatások](get-started.md) korlátozódik **westus** (nem **westus2**) és így nem kompatibilis a közvetlen Sor Speech. Ingyenes és standard szintje **westus2** előfizetések kompatibilisek.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` a robot tevékenységei, speech recognition eredmények és más információkat több eseményt támaszkodik. Ezután adjon hozzá ezen esemény figyelők.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. Csatlakozás a `SpeechBotConnector` meghívásával át a közvetlen vonal a `connectAsync()` metódust. A robot teszteléséhez hívhat a `listenOnceAsync` hangbemenet elküldeni a mikrofon metódust. Ezenkívül használhatja a `sendActivityAsync` metódus küldése egyéni tevékenységek szerializált karakterláncként. Ezek egyéni tevékenységek biztosíthat további adatokat, amelyek a robot a beszélgetésben fogja használni.

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. Menti a változásokat a `Main` fájlt.

1. Válasz a lejátszás támogatásához, hozzáadja egy további osztályban, amelyek tartalmazzák a segédprogram-metódusokat hang támogatásához. Ahhoz, hogy a hang, adjon hozzá egy másik új üres osztályt a Java-projektek: válasszon **fájl** > **új** > **osztály**.

1. Az a **új Java-osztály** ablakban adja meg **speechsdk.quickstart** be a **csomag** mező, és **AudioPlayer** be a **Neve** mező.

   ![A New Java Class varázsló képernyőképe](media/sdk/qs-java-jre-06-create-main-java.png)

1. Nyissa meg az újonnan létrehozott **AudioPlayer** osztályt, és cserélje le az alábbi kódot.

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. Menti a változásokat a `AudioPlayer` fájlt.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

Nyomja le az F11 billentyűt, vagy válassza a **Run** > **Debug** (Futtatás, Hibakeresés) lehetőséget.
A konzolban megjelenik egy üzenet "Például: valami" ezen a ponton, előfordulhat, hogy egy angol nyelvű kifejezést vagy mondat, amely a robot megértse beszél. A beszéd továbbítja a rendszer a robot, ahol felismerhető lesz, a közvetlen vonal Speech-csatornán keresztül a robot által feldolgozott, és a választ küldi vissza a rendszer egy tevékenységet. A robot speech, választ ad vissza, ha a hanganyag játssza vissza használatával a `AudioPlayer` osztály.

![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>További lépések

További példákat, például a beszéd beolvasása egy hangfájlt a Githubon érhetők el.

> [!div class="nextstepaction"]
> [Ismerkedés a Java-példák a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Rövid útmutató: Translate speech, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
