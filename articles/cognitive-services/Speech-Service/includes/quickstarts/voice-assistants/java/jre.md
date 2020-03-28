---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: 4bef6c20ab356f3a6bc2f8aeee23411ac254b3e0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241212"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](~/articles/cognitive-services/speech-service/get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=jre)
> * A [közvetlen vonalbeszédcsatornához](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) csatlakoztatott robot létrehozása
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez

  > [!NOTE]
  > Tekintse meg [a támogatott régiók listáját a hang-asszisztensek,](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) és győződjön meg arról, hogy az erőforrások telepítése az egyik ilyen régiókban.

## <a name="create-and-configure-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

A naplózás engedélyezéséhez frissítse a _pom.xml_ fájlt, hogy az tartalmazza a következő függőséget:

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Ha új üres osztályt szeretne hozzáadni a Java-projekthez, válassza az**Új** > **osztály** **fájlja** > lehetőséget.

1. Az **Új Java-osztály** ablakban írja be a _speechsdk.quickstart parancsot_ a **Csomagolás** mezőbe és a _Fő_ mezőbe a **Név** mezőbe.

   ![A New Java Class varázsló képernyőképe](~/articles/cognitive-services/speech-service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Nyissa meg `Main` az újonnan létrehozott osztályt, és cserélje le a `Main.java` fájl tartalmát a következő kezdőkódra:

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

1. A `main` módszerben először `DialogServiceConfig` konfigurálja a, `DialogServiceConnector` és használja egy példány létrehozásához. Ez a példány a közvetlen vonalbeszédcsatornához csatlakozik, hogy kommunikáljon a robottal. A `AudioConfig` példány a hangbemenet forrásának megadására is szolgál. Ebben a példában az alapértelmezett `AudioConfig.fromDefaultMicrophoneInput()`mikrofont használja a.

   - Cserélje le `YourSubscriptionKey` a karakterláncot az előfizetési kulcsra, amelyet [erről a webhelyről](~/articles/cognitive-services/speech-service/get-started.md)kaphat.
   - Cserélje le `YourServiceRegion` a karakterláncot az előfizetéshez társított [régióra.](~/articles/cognitive-services/speech-service/regions.md)

   > [!NOTE]
   > Tekintse meg [a támogatott régiók listáját a hang-asszisztensek,](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) és győződjön meg arról, hogy az erőforrások telepítése az egyik ilyen régiókban.

   ```java
   final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
   final String region = "YourServiceRegion"; // Your speech subscription service region
   final BotFrameworkConfig botConfig = BotFrameworkConfig.fromSubscription(subscriptionKey, region);

   // Configure audio input from a microphone.
   final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

   // Create a DialogServiceConnector instance.
   final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
   ```

1. Az `DialogServiceConnector` összekötő támaszkodik több esemény kommunikálni a robot tevékenységek, beszédfelismerés iés egyéb információk. Adja hozzá ezeket az eseményfigyelők következő.

   ```java
   // Recognizing will provide the intermediate recognized text while an audio stream is being processed.
   connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // Recognized will provide the final recognized text once audio capture is completed.
   connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // SessionStarted will notify when audio begins flowing to the service for a turn.
   connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
       log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
   });

   // SessionStopped will notify when a turn is complete and it's safe to begin listening again.
   connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
       log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
   });

   // Canceled will be signaled when a turn is aborted or experiences an error condition.
   connector.canceled.addEventListener((o, canceledEventArgs) -> {
       log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
       connector.disconnectAsync();
   });

   // ActivityReceived is the main way your bot will communicate with the client and uses Bot Framework activities.
   connector.activityReceived.addEventListener((o, activityEventArgs) -> {
       final String act = activityEventArgs.getActivity().serialize();
           log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
           if (activityEventArgs.hasAudio()) {
               playAudioStream(activityEventArgs.getAudio());
           }
       });
   ```

1. Csatlakozzon `DialogServiceConnector` a közvetlen vonalbeszédhez `connectAsync()` a metódus meghívásával. A robot teszteléséhez meghívhatja `listenOnceAsync` a módszert a mikrofonból érkező hangbevitel küldésére. Emellett a `sendActivityAsync` módszer relaializált karakterláncként is küldhet egyéni tevékenységet. Ezek az egyéni tevékenységek további adatokat is biztosíthatnak, amelyeket a robot a beszélgetésben használ.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. Mentse a `Main` fájl módosításait.

1. A válaszlejátszás támogatásához adjon hozzá egy további osztályt, amely átalakítja a getAudio() API-ból visszaadott PullAudioOutputStream objektumot egy Java InputStream-be a kezelés megkönnyítése érdekében. Ez `ActivityAudioStream` egy speciális osztály, amely a Közvetlen vonalbeszéd csatornáról érkező hangválaszt kezeli. A lejátszás kezeléséhez a hangformátum-információk lehívásához biztosít hozzáférést. Ezért válassza az**Új** > **osztály** **fájlja** > lehetőséget.

1. Az **Új Java-osztály** ablakban írja be a _speechsdk.quickstart fájlt_ a **Csomag** mezőbe, az _ActivityAudioStream_ fájlt pedig a **Név** mezőbe.

1. Nyissa meg `ActivityAudioStream` az újonnan létrehozott osztályt, és cserélje le a következő kódra:

   ```java
   package com.speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

   import java.io.IOException;
   import java.io.InputStream;

    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second (16 kHz).
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format (16 bits).
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
         * Reads up to a specified maximum number of bytes of data from the activity audio stream,
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
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample, and the # channels.
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
         * As this implementation does not buffer, this will be defaulted to 0
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
         * Enum defining the types of audio encoding supported by this stream.
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

Válassza az F11 vagy **a Debug futtatása** > **Debug**lehetőséget.
A konzol a "Mondj valamit" üzenetet jeleníti meg.
Ezen a ponton, beszélni egy angol kifejezést vagy mondatot, hogy a bot képes megérteni. A beszéd a közvetlen vonalbeszédcsatornán keresztül jut el a robothoz, ahol a robot felismeri és feldolgozza. A válasz tevékenységként kerül visszaadásra. Ha a robot válaszként visszaadja a beszédet, `AudioPlayer` a hang lejátszása az osztály használatával történik.

![Képernyőkép a konzolról a sikeres felismerést követően](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
