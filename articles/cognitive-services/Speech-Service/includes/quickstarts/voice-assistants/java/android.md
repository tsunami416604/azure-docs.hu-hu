---
title: 'Rövid útmutató: Egyéni hangasszisztens létrehozása, Java (Android) – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egyéni hangsegédet Az Android-alapú Java-ban a Beszéd SDK használatával.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: 24030a568dd4df7f3e0888e2bda8f1335e52ba38
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241170"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](~/articles/cognitive-services/speech-service/get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=android)
> * A [közvetlen vonalbeszédcsatornához](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) csatlakoztatott robot létrehozása
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez

  > [!NOTE]
  > Tekintse meg [a támogatott régiók listáját a hang-asszisztensek,](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) és győződjön meg arról, hogy az erőforrások telepítése az egyik ilyen régiókban.

## <a name="create-and-configure-a-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>A felhasználói felület létrehozása

Ebben a szakaszban létrehozunk egy egyszerű felhasználói felületet (UI) az alkalmazáshoz. Kezdjük a fő tevékenység megnyitásával: `activity_main.xml`. Az alapsablon tartalmaz egy címsort az alkalmazás `TextView` nevével, és egy a a "Hello world!" üzenettel.

Ezután cserélje ki `activity_main.xml` a tartalmát a következő kódra:

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

- Az `button` elem interakciót kezdeményez, `onBotButtonClicked` és kattintáskor meghívja a metódust.
- Az `recoText` elem megjeleníti a beszéd-szöveg eredményeket, ahogy beszél a bot.
- Az `activityText` elem megjeleníti a JSON hasznos teher a legújabb Bot Framework tevékenység a bot.

A felhasználói felület szövegének és grafikus ábrázolásának így kell kinéznie:

![](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa `MainActivity.java`meg a tartalmat, és cserélje le a tartalmát a következő kódra:

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
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
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

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
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

   * A `onCreate` módszer olyan kódot tartalmaz, amely mikrofon- és internet-engedélyeket kér.

   * Ahogy korábban már említettük, az `onBotButtonClicked` metódus kezeli azt, ha a gombra kattintanak. Egy gombnyomás egyetlen interakciót ("turn") vált ki a robottal.

   * A `registerEventListeners` módszer bemutatja a `DialogServiceConnector` bejövő tevékenységek alapvető kezelése által használt eseményeket.

1. Ugyanabban a fájlban cserélje le az erőforrásoknak megfelelő konfigurációs karakterláncokat:

    * A `YourSpeechSubscriptionKey` helyére írja be az előfizetési kulcsot.

    * Cserélje `YourServiceRegion` le az előfizetéshez társított [régióra:](~/articles/cognitive-services/speech-service/regions.md) Jelenleg csak a Beszédszolgáltatás-régiók egy részhalmaza támogatott a Direct Line Speech szolgáltatással. További információ: [regions](~/articles/cognitive-services/speech-service/regions.md#voice-assistants).

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Csatlakoztassa az Android-eszközt a fejlesztői számítógéphez. Győződjön meg róla, hogy engedélyezte a [fejlesztői módot és az USB-hibakeresést](https://developer.android.com/studio/debug/dev-options) az eszközön.

1. Az alkalmazás létrehozásához nyomja le a Ctrl+F9 billentyűkombinációt, vagy válassza a menüsor > **Project** **összeállítása**parancsát.

1. Az alkalmazás elindításához nyomja le a Shift+F10 billentyűkombinációt, vagy válassza a Run**Run 'app' (Alkalmazás futtatása)** **lehetőséget.** > 

1. A megjelenő, az üzembehelyezési cél megadására szolgáló ablakban válassza ki az Android-eszközt.

   ![A Select Deployment Target (Üzembehelyezési cél kiválasztása) ablak képernyőképe](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-12-deploy.png)

Miután az alkalmazás és annak tevékenysége elindult, kattintson a gombra, hogy elkezd beszélni a bot. Az átírt szöveg jelenik meg, amikor beszélsz, és a legutóbbi tevékenység, amit a robottól kaptál, akkor jelenik meg, amikor megérkezett. Ha a robot úgy van beállítva, hogy szóbeli válaszokat adjon, a szöveggé bírja automatikusan lejátssza a szöveget.

![Az Android-alkalmazás képernyőképe](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]

