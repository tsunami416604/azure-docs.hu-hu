---
title: 'Gyors útmutató: Egyéni hang – első virtuális asszisztens (előzetes verzió), Java (Windows, Linux) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Cognitive Services Speech szoftverfejlesztői készletet (SDK) Java-konzolos alkalmazásokban. Megtudhatja, hogyan csatlakoztatható az ügyfélalkalmazás egy korábban létrehozott bot Framework-robothoz úgy, hogy a közvetlen vonalas beszédfelismerési csatornát használja, és lehetővé tegye a hang-első virtuális asszisztensi élményt.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: bidishac
ms.openlocfilehash: c5a6042e4b181190849b3759325e4aab0c22413b
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71800037"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Gyors útmutató: Hozzon létre egy hang-első virtuális asszisztenst a Speech SDK, a Java használatával

A gyors üzembe helyezési lehetőség a [beszéd-szöveg](quickstart-java-jre.md), a [szöveg és a beszéd](quickstart-text-to-speech-java-jre.md) [fordítására](quickstart-translate-speech-java-jre.md)is használható.

Ebben a cikkben egy Java-konzol alkalmazást hoz létre a [Cognitive Services SPEECH SDK](speech-sdk.md)használatával. Az alkalmazás csatlakozni fog egy korábban létrehozott robothoz, amely a közvetlen vonal beszédfelismerési csatornájának használatára, hangkérés küldésére és hangválasztó tevékenység visszaküldésére van konfigurálva (ha be van állítva). Az alkalmazás a Speech SDK Maven-csomaggal és az Eclipse Java IDE Windows, Ubuntu Linux vagy macOS rendszeren készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Operációs rendszer: Windows (64 bites), Ubuntu Linux 16.04/18.04 (64 bites), vagy macOS 10,13 vagy újabb
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Egy Azure-előfizetési kulcs a Speech Serviceshez. [Szerezze be ingyen](get-started.md) , vagy hozza létre a [Azure Portal](https://portal.azure.com).
* A bot Framework 4,2-es vagy újabb verziójának használatával létrehozott, előre konfigurált robot. A robotnak a hangbemenetek fogadásához elő kell fizetnünk az új "Direct line Speech" csatornára.

    > [!NOTE]
    > A közvetlen vonalas beszéd (előzetes verzió) jelenleg a Speech Services-régiók egy részhalmazában érhető el. Tekintse meg [a támogatott régiók listáját a hangvezérelt virtuális asszisztensekhez](regions.md#Voice-first virtual assistants) , és gondoskodjon arról, hogy az erőforrások az egyik régióban legyenek telepítve.

Ha Ubuntu 16.04/18.04-t futtat, győződjön meg róla, hogy ezek a függőségek telepítve vannak az Eclipse elindítása előtt:

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Ha Windows rendszert futtat (64 bites), győződjön meg arról, hogy telepítette a platformhoz készült Microsoft vizualizációs C++ terjeszthető csomagját:
* [A Visual Studio C++ 2017-hoz készült Microsoft vizualizációs terjeszthető csomag letöltése](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Nem kötelező: Gyors első lépések

Ez a rövid útmutató részletesen leírja, hogyan lehet egy egyszerű ügyfélalkalmazás számára csatlakozni a beszédfelismerést támogató robothoz. Ha inkább a betöltést választja, az ebben a rövid útmutatóban használt teljes, előkészített forráskód a `quickstart` mappában található [SPEECH SDK-mintákban](https://aka.ms/csspeech/samples) érhető el.

## <a name="create-and-configure-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Emellett a naplózás engedélyezéséhez frissítse a **Pom. XML** fájlt, hogy az tartalmazza a következő függőséget.

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

1. Nyissa meg az **újonnan létrehozott** főosztályt, és cserélje le a `Main.java` fájl tartalmát a következő kiindulási kóddal.

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

1. A **Main** metódusban először konfigurálja a `DialogServiceConfig`-et, és használja egy `DialogServiceConnector` példány létrehozásához. Ezzel csatlakozni fog a közvetlen vonalas beszéd csatornához a robottal való interakcióhoz. A hangbemenet forrásának megadásához egy `AudioConfig` példány is használható. Ebben a példában az alapértelmezett mikrofon a `AudioConfig.fromDefaultMicrophoneInput()` értékkel van használatban.

    * Cserélje le a `YourSubscriptionKey` karakterláncot az előfizetési kulcsra, amelyet [itt](get-started.md)érhet el.
    * Cserélje le a `YourServiceRegion` karakterláncot az előfizetéséhez társított [régióra](regions.md) .
    * Cserélje le a `YourChannelSecret` karakterláncot a Direct line Speech Channel titkos kódjára.

    > [!NOTE]
    > A közvetlen vonalas beszéd (előzetes verzió) jelenleg a Speech Services-régiók egy részhalmazában érhető el. Tekintse meg [a támogatott régiók listáját a hangvezérelt virtuális asszisztensekhez](regions.md#voice-first-virtual-assistants) , és gondoskodjon arról, hogy az erőforrások az egyik régióban legyenek telepítve.

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

1. a `DialogServiceConnector` számos eseményre támaszkodik, hogy a robot tevékenységeit, beszédfelismerési eredményeit és egyéb információkat kommunikáljanak. Adja hozzá ezeket az esemény-figyelőket a következőhöz.

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

1. A `connectAsync()` metódus meghívásával kapcsolódjon a `DialogServiceConnector` és a közvetlen vonal beszédéhez. A robot teszteléséhez meghívja a `listenOnceAsync` metódust, hogy hangbemenetet küldjön a mikrofonból. Emellett a `sendActivityAsync` metódussal is elküldheti az egyéni tevékenységeket szerializált karakterláncként. Ezek az egyéni tevékenységek további, a robot által a beszélgetés során használt adatait is megadhatják.

    ```java
    connector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    connector.listenOnceAsync();

    // connector.sendActivityAsync(...)
    ```

1. Mentse a `Main` fájl módosításait.

1. A válaszok lejátszásának támogatásához hozzá kell adnia egy további osztályt, amely átalakítja a getAudio () API által visszaadott PullAudioOutputStream objektumot egy Java-InputStream a könnyű kezelhetőség érdekében. Ez a ActivityAudioStream egy speciális osztály, amely a "Direct line Speech Channel" hang-válaszait fogja kezelni. Biztosítja a hozzáférést a lejátszáshoz szükséges hangformátum-információk lekéréséhez: Ehhez válassza a **fájl** > **új** > **osztály**elemet.

1. Az **új Java-osztály** ablakban írja be a **speechsdk.** rövid útmutatót a **csomag** mezőbe, és **ActivityAudioStream** a **név** mezőbe.

1. Nyissa meg az újonnan létrehozott **ActivityAudioStream** osztályt, és cserélje le az alább megadott kóddal.

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

1. Mentse a `ActivityAudioStream` fájl módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

Nyomja le az F11 billentyűt, vagy válassza a **Run** > **Debug** (Futtatás, Hibakeresés) lehetőséget.
Ezen a ponton megjelenik egy üzenet, amely szerint a "mondja el valami" üzenetet, egy angol nyelvű kifejezéssel vagy mondattal fog megjelenni. A beszédfelismerés a közvetlen vonalas beszéd csatornán keresztül lesz továbbítva a robotba, ahol a rendszer felismeri, feldolgozza a robotot, és a válasz tevékenységként lesz visszaadva. Ha a robot válaszként adja vissza a beszédet, a hang a `AudioPlayer` osztály használatával lesz visszajátszva.

![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>További lépések

A GitHubon további minták is elérhetők, például a hangfájlok beszédének olvasása.

> [!div class="nextstepaction"]
> [Alapszintű robot létrehozása és üzembe helyezése](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Lásd még

- [A Voice – First virtuális asszisztensek ismertetése](voice-first-virtual-assistants.md)
- [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
- [Egyéni ébresztési szavak](speech-devices-sdk-create-kws.md)
- [Közvetlen vonalas beszéd összekötése a robottal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [A Java-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)
