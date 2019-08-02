---
title: 'Gyors útmutató: Egyéni hang – első virtuális asszisztens (előzetes verzió), Java (Android) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre egy hang-első virtuális asszisztensi alkalmazást Java-ban Androidon a Speech SDK használatával
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: 1c203bb39a90fdb1c77c3a2c844318a748df7c63
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559210"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Gyors útmutató: A Speech SDK használatával hozzon létre egy hang-első virtuális asszisztenst Java-on Android rendszeren

Egy rövid útmutató is elérhető a [beszéd és a szöveg](quickstart-java-android.md)között.

Ebben a cikkben a [SPEECH SDK](speech-sdk.md)használatával létrehoz egy, a Java for androidt használó hang-első virtuális asszisztenst. Ez az alkalmazás egy már létrehozott és a [közvetlen vonalas beszédfelismerési csatornával](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)konfigurált robothoz fog csatlakozni. Ezután elküld egy hangkérést a robotnak, és egy hangvezérelt reagálási tevékenységet fog bemutatni.

Ez az alkalmazás a Speech SDK Maven-csomaggal és Android Studio 3,3-mel készült. A Speech SDK jelenleg a 32/64 bites ARM, vagy Intel x86/x64 processzorokat használó Android-eszközökkel kompatibilis.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetési kulcs a Speech Serviceshez. [Szerezze be ingyen](get-started.md) , vagy hozza létre a [Azure Portal](https://portal.azure.com).
* Korábban létrehozott, a [közvetlen vonalas beszéd csatornával](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) konfigurált robot
* [Android Studio](https://developer.android.com/studio/) v 3.3-as vagy újabb verzió

    > [!NOTE]
    > A közvetlen vonalas beszéd (előzetes verzió) jelenleg a Speech Services-régiók egy részhalmazában érhető el. Tekintse meg [a támogatott régiók listáját a hangvezérelt virtuális asszisztensekhez](regions.md#Voice-first virtual assistants) , és gondoskodjon arról, hogy az erőforrások az egyik régióban legyenek telepítve.

## <a name="create-and-configure-a-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>A felhasználói felület létrehozása

Ebben a szakaszban egy alapszintű felhasználói felületet (UI) hozunk létre az alkalmazáshoz. Kezdjük a fő tevékenység megnyitásával: `activity_main.xml`. Az alapszintű sablon tartalmaz egy címsort az alkalmazás nevével, és `TextView` a "Hello World!" üzenettel.

Ezután cserélje le a tartalmát `activity_main.xml` a következő kódra:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Ez az XML egy egyszerű felhasználói felületet határoz meg a robottal való interakcióhoz.

* Az `button` elem interakciót kezdeményez, és rákattintáskor meghívja a `onBotButtonClicked` metódust.
* Az `recoText` elem megjeleníti a beszéd – szöveg eredményeket a robottal való kommunikáció során.
* Az `activityText` elem megjeleníti a robothoz tartozó legújabb bot Framework-tevékenység JSON-adattartalmát.

A felhasználói felület szöveg-és grafikus ábrázolásának ekkor a következőképpen kell kinéznie:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg `MainActivity.java`a (z) programot, és cserélje le a tartalmát a következő kódra:

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your bot's own Direct Line Speech channel secret
        private static String channelSecret = "YourChannelSecret";
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region (note: only a subset of regions are currently supported)
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from the channel and speech subscription information
            DialogServiceConfig config = DialogServiceConfig.fromBotSecret(channelSecret, speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * A `onCreate` metódus olyan kódot tartalmaz, amely mikrofon-és Internet-engedélyeket kér.

   * Ahogy korábban már említettük, az `onBotButtonClicked` metódus kezeli azt, ha a gombra kattintanak. Egy gomb megnyomásával egyetlen interakciót ("turn") indít a robottal.

   * A `registerEventListeners` metódus a bejövő tevékenységek és az `DialogServiceConnector` alapszintű kezelési műveletek által használt eseményeket mutatja be.

1. Ugyanebben a fájlban cserélje le a konfigurációs karakterláncokat a megfelelő erőforrásokra:

    * Cserélje `YourChannelSecret` le a szöveget a robot Direct line Speech Channel-titkára.

    * A `YourSpeechSubscriptionKey` helyére írja be az előfizetési kulcsot.

    * Cserélje `YourServiceRegion` le az [](regions.md) előfizetéshez tartozó régiót, és csak a Speech Services-régiók egy részhalmazát támogatja a közvetlen vonalas beszéd. További információ: [régiók](regions.md#voice-first-virtual-assistants).

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Csatlakoztassa az Android-eszközt a fejlesztői számítógéphez. Győződjön meg róla, hogy engedélyezte a [fejlesztői módot és az USB-hibakeresést](https://developer.android.com/studio/debug/dev-options) az eszközön.

1. Az alkalmazás kiépítéséhez nyomja le a Ctrl+F9 billentyűparancsot, vagy válassza a **Build (Kiépítés)**  > **Make Project (Projekt létrehozása)** elemet a menüsávon.

1. Az alkalmazás futtatásához nyomja le a Shift+F10 billentyűparancsot, vagy válassza a **Run (Futtatás)**  > **Run 'app' („Alkalmazás” futtatása)** elemeket.

1. A megjelenő, az üzembehelyezési cél megadására szolgáló ablakban válassza ki az Android-eszközt.

   ![A Select Deployment Target (Üzembehelyezési cél kiválasztása) ablak képernyőképe](media/sdk/qs-java-android-12-deploy.png)

Az alkalmazás és a tevékenység elindítása után a gombra kattintva megkezdheti a beszélgetést a robottal. Az átmásolt szöveg az Ön által beszélt módon jelenik meg, és a robottól kapott legújabb tevékenységek a fogadáskor megjelennek. Ha a robot úgy van konfigurálva, hogy a kimondott válaszokat adja meg, a beszédfelismerési szöveg automatikusan le lesz játszva.

![Az Android-alkalmazás képernyőképe](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alapszintű robot létrehozása és üzembe helyezése](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Lásd még
- [A Voice – First virtuális asszisztensek ismertetése](voice-first-virtual-assistants.md)
- [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
- [Egyéni ébresztési szavak](speech-devices-sdk-create-kws.md)
- [Közvetlen vonalas beszéd összekötése a robottal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [A Java-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)
