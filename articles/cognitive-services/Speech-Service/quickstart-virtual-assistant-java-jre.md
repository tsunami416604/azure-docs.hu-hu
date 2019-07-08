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
ms.date: 07/05/2019
ms.author: bidishac
ms.openlocfilehash: 78e80b276a13ee6e27fdf0515f2901fdeaa20c5d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604929"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Gyors útmutató: Hozzon létre egy hang-és felhőközpontú virtuális asszisztensek a Speech SDK-Java

Rövid útmutatók érhetők el is [hang-szöveg transzformációs](quickstart-java-jre.md) és [tolmácsolás –](quickstart-translate-speech-java-jre.md).

Ez a cikk segítségével létrehozhat egy Java-konzolalkalmazást a [Cognitive Services beszédfelismerő SDK](speech-sdk.md). Az alkalmazás egy korábban létrehozott bot konfigurálva a közvetlen vonal beszédfelismerő csatornát használja, a hangalapú kérés küldése és (Ha be van állítva), adja vissza a voice-tevékenységet fog csatlakozni. Az alkalmazást a létrehozása a Speech SDK Maven-csomag és az Eclipse a Java IDE Windows, Ubuntu Linux vagy macOS rendszeren. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Operációs rendszer: (64 bites) Windows, Ubuntu Linux 16.04/18.04 (64 bites) vagy a macOS 10.13 vagy újabb
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Egy Azure-előfizetés beszédszolgáltatások kulcs. [Igényeljen ingyenesen egy](get-started.md) vagy hozza létre a [az Azure portal](https://portal.azure.com).
* A Bot Framework 4.2-es verzió használatával létrehozott előre konfigurált robot vagy újabb. A robot hangalapú bemenetek fogadni az új "A közvetlen vonal Speech" csatorna feliratkozás kellene.

    > [!NOTE]
    > A közvetlen vonal Speech (előzetes verzió) egy részhalmazát beszédszolgáltatások régiók jelenleg érhető el. Tekintse meg [hang-és felhőközpontú virtuális asszisztensek támogatott régiók listáját](regions.md#Voice-first virtual assistants) és ellenőrizze, hogy az erőforrások üzembe ezen régiók egyikében.

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

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;
    import java.io.InputStream;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }

        private void playAudioStream(PullAudioOutputStream audio) {
            ActivityAudioStream stream = new ActivityAudioStream(audio);
            final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
            final AudioFormat format = new AudioFormat(
                    AudioFormat.Encoding.PCM_SIGNED,
                    audioFormat.getSamplesPerSecond(),
                    audioFormat.getBitsPerSample(),
                    audioFormat.getChannels(),
                    audioFormat.getFrameSize(),
                    audioFormat.getSamplesPerSecond(),
                    false);
            try {
                int bufferSize = format.getFrameSize();
                final byte[] data = new byte[bufferSize];

                SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
                SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
                line.open(format);

                if (line != null) {
                    line.start();
                    int nBytesRead = 0;
                    while (nBytesRead != -1) {
                        nBytesRead = stream.read(data);
                        if (nBytesRead != -1) {
                            line.write(data, 0, nBytesRead);
                        }
                    }
                    line.drain();
                    line.stop();
                    line.close();
                }
                stream.close();

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }
    ```

1. Az a **fő** metódus, akkor először konfigurálja a `DialogServiceConfig` , és ezzel hozzon létre egy `DialogServiceConnector` példány. Ez a közvetlen vonal beszédfelismerő csatornát a robottal lépnek kapcsolatba fog csatlakozni. Egy `AudioConfig` példány is használható a hangbemeneti forrást adja meg. Ebben a példában az alapértelmezett mikrofon együttes `AudioConfig.fromDefaultMicrophoneInput()`.

    * Cserélje le a karakterláncot `YourSubscriptionKey` az előfizetési kulcs, amely kérhet le a [Itt](get-started.md).
    * Cserélje le a karakterláncot `YourServiceRegion` együtt a [régió](regions.md) az Ön előfizetéséhez rendelve.
    * Cserélje le a karakterláncot `YourChannelSecret` a közvetlen vonal beszédfelismerő csatornát titokkal.

    > [!NOTE]
    > A közvetlen vonal Speech (előzetes verzió) egy részhalmazát beszédszolgáltatások régiók jelenleg érhető el. Tekintse meg [hang-és felhőközpontú virtuális asszisztensek támogatott régiók listáját](regions.md#voice-first-virtual-assistants) és ellenőrizze, hogy az erőforrások üzembe ezen régiók egyikében.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
    final String region = "YourServiceRegion"; // Your speech subscription service region. Note: only a subset of regions are currently supported
    final DialogServiceConfig botConfig = DialogServiceConfig.fromBotSecret(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a DialogServiceConnector instance
    final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
    ```

1. `DialogServiceConnector` a robot tevékenységei, speech recognition eredmények és más információkat több eseményt támaszkodik. Ezután adjon hozzá ezen esemény figyelők.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        connector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
    connector.activityReceived.addEventListener((o, activityEventArgs) -> {
        final String act = activityEventArgs.getActivity().serialize();
            log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
            if (activityEventArgs.hasAudio()) {
                playAudioStream(activityEventArgs.getAudio());
            }
        });
    ```

1. Csatlakozás a `DialogServiceConnector` meghívásával át a közvetlen vonal a `connectAsync()` metódust. A robot teszteléséhez hívhat a `listenOnceAsync` hangbemenet elküldeni a mikrofon metódust. Ezenkívül használhatja a `sendActivityAsync` metódus küldése egyéni tevékenységek szerializált karakterláncként. Ezek egyéni tevékenységek biztosíthat további adatokat, amelyek a robot a beszélgetésben fogja használni.

    ```java
    connector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    connector.listenOnceAsync();

    // connector.sendActivityAsync(...)
    ```

1. Menti a változásokat a `Main` fájlt.

1. A támogatási válasz lejátszás, egy további osztály, amely átalakítja a getAudio() API a javával InputStream megkönnyítése érdekében kezelési visszaadott PullAudioOutputStream objektum fogja hozzáadni. Ez ActivityAudioStream egy specializált osztály, amely a "közvetlen vonal beszédfelismerő csatornát" hang válasza fogja kezelni. Leíró lejátszási kezeléséhez szükséges hangformátum adatokat beolvasni biztosítja: Válassza ki, amely a **fájl** > **új** > **osztály**.

1. Az a **új Java-osztály** ablakban adja meg **speechsdk.quickstart** be a **csomag** mező, és **ActivityAudioStream** a be **Név** mező.

1. Nyissa meg az újonnan létrehozott **ActivityAudioStream** osztályt, és cserélje le az alábbi kódot.

    ```java
    package com.speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

    import java.io.IOException;
    import java.io.InputStream;


    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second. (16 kHz)
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format. (16 bits)
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample and the # channels
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

    ```

1. Menti a változásokat a `ActivityAudioStream` fájlt.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

Nyomja le az F11 billentyűt, vagy válassza a **Run** > **Debug** (Futtatás, Hibakeresés) lehetőséget.
A konzolban megjelenik egy üzenet "Például: valami" ezen a ponton, előfordulhat, hogy egy angol nyelvű kifejezést vagy mondat, amely a robot megértse beszél. A beszéd továbbítja a rendszer a robot, ahol felismerhető lesz, a közvetlen vonal Speech-csatornán keresztül a robot által feldolgozott, és a választ küldi vissza a rendszer egy tevékenységet. A robot speech, választ ad vissza, ha a hanganyag játssza vissza használatával a `AudioPlayer` osztály.

![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>További lépések

További példákat, például a beszéd beolvasása egy hangfájlt a Githubon érhetők el.

> [!div class="nextstepaction"]
> [Létrehozni és üzembe helyezni egy alapszintű robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Lásd még

- [Hang-és felhőközpontú virtuális asszisztensek kapcsolatban](voice-first-virtual-assistants.md)
- [Ingyenes beszédszolgáltatások előfizetési kulcs lekérése](get-started.md)
- [Egyéni ébresztési szavakat](speech-devices-sdk-create-kws.md)
- [A közvetlen vonal Speech csatlakozni a robot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Ismerkedés a Java-példák a Githubon](https://aka.ms/csspeech/samples)
