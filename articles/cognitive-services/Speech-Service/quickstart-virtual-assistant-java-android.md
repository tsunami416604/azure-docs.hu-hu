---
title: 'Gyors útmutató: Egyéni hang-és felhőközpontú virtuális asszisztensek (előzetes verzió), a Java (Android) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy hang-és felhőközpontú virtuális asszisztensek alkalmazást az Android, a beszéd SDK-val Java nyelven
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: c62402faa1995e1e992c8251ed87160a8f33d3a7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602751"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Gyors útmutató: Hozzon létre egy hang-és felhőközpontú virtuális asszisztensek a Java Android rendszeren a Speech SDK-val

Gyors üzembe helyezés érhető el is [hang-szöveg transzformációs](quickstart-java-android.md).

Ebben a cikkben az Android használatával fog létrehozni egy hang-és felhőközpontú virtuális asszisztensek és a Java együttes a [beszéd SDK](speech-sdk.md). Ez az alkalmazás, amely korábban már létrehozott és konfigurált egy robot fog csatlakozni a [közvetlen vonal beszédfelismerő csatornát](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Ezután a robot hangalapú kérést küldhet és jelenleg a voice-kompatibilis tevékenységet.

Ez az alkalmazás a Speech SDK Maven-csomag és az Android Studio 3.3 épül. A Speech SDK jelenleg a 32/64 bites ARM, vagy Intel x86/x64 processzorokat használó Android-eszközökkel kompatibilis.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés beszédszolgáltatások kulcs. [Igényeljen ingyenesen egy](get-started.md) vagy hozza létre a [az Azure portal](https://portal.azure.com).
* Egy korábban létrehozott bot konfigurálva a [közvetlen vonal beszédfelismerő csatornát](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
* [Az Android Studio](https://developer.android.com/studio/) v3.3 vagy újabb

    > [!NOTE]
    > A közvetlen vonal Speech (előzetes verzió) egy részhalmazát beszédszolgáltatások régiók jelenleg érhető el. Tekintse meg [hang-és felhőközpontú virtuális asszisztensek támogatott régiók listáját](regions.md#Voice-first virtual assistants) és ellenőrizze, hogy az erőforrások üzembe ezen régiók egyikében.

## <a name="create-and-configure-a-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>A felhasználói felület létrehozása

Ebben a szakaszban létrehozunk egy alapszintű felhasználói felület (UI) az alkalmazáshoz. Kezdjük a fő tevékenység megnyitásával: `activity_main.xml`. Az alapszintű sablon tartalmazza az alkalmazás nevét, a címsor és a egy `TextView` a következő üzenettel: "Hello world!".

Ezután cserélje le a tartalmát a `activity_main.xml` a következő kóddal:

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

Az XML-kód egy egyszerű felhasználói Felületét, hogy a robottal lépnek kapcsolatba határozza meg.

* A `button` elem közben kezdeményezi, és meghívja az `onBotButtonClicked` metódus kattintáskor.
* A `recoText` elem fog megjelenni a hang-szöveg transzformációs eredményeket, ahogy a robot a beszélgetés.
* A `activityText` elem jelenik meg a robot a legújabb Bot Framework tevékenység a JSON-adattartalmat.

A szöveg és a felhasználói felületének grafikus ábrázolása kell kinéznie:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg `MainActivity.java`, és cserélje ki annak tartalmát az alábbira:

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

   * A `onCreate` metódus tartalmazza a kódot, amely a mikrofon és internetes engedélyt kér.

   * Ahogy korábban már említettük, az `onBotButtonClicked` metódus kezeli azt, ha a gombra kattintanak. Egy gomb megnyomása elindítja a robot való egyetlen interakció ("be").

   * A `registerEventListeners` módszer bemutatja az események által használt a `DialogServiceConnector` és bejövő tevékenységek egyszerű kezelését.

1. Ugyanebben a fájlban cserélje le a konfigurációs karakterláncokat az erőforrások megfelelően:

    * Cserélje le `YourChannelSecret` robotja közvetlen vonal beszédfelismerő csatornát titokkal.

    * A `YourSpeechSubscriptionKey` helyére írja be az előfizetési kulcsot.

    * Cserélje le `YourServiceRegion` együtt a [régió](regions.md) beszédszolgáltatások régiók csak egy részhalmazát jelenleg támogatott a közvetlen vonal beszédszolgáltatásokkal az előfizetéshez társított. További információkért lásd: [régiók](regions.md#voice-first-virtual-assistants).

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Csatlakoztassa az Android-eszközt a fejlesztői számítógéphez. Győződjön meg róla, hogy engedélyezte a [fejlesztői módot és az USB-hibakeresést](https://developer.android.com/studio/debug/dev-options) az eszközön.

1. Az alkalmazás kiépítéséhez nyomja le a Ctrl+F9 billentyűparancsot, vagy válassza a **Build (Kiépítés)**  > **Make Project (Projekt létrehozása)** elemet a menüsávon.

1. Az alkalmazás futtatásához nyomja le a Shift+F10 billentyűparancsot, vagy válassza a **Run (Futtatás)**  > **Run 'app' („Alkalmazás” futtatása)** elemeket.

1. A megjelenő, az üzembehelyezési cél megadására szolgáló ablakban válassza ki az Android-eszközt.

   ![A Select Deployment Target (Üzembehelyezési cél kiválasztása) ablak képernyőképe](media/sdk/qs-java-android-12-deploy.png)

Után az alkalmazás és a hozzá tartozó tevékenység üzemeltet, kattintson a gombra kattintva megkezdheti a robot folytatott kommunikációra. Átírt szöveg megjelenik, illetve kimondható és a legújabb tevékenység van, a bot fog kapott jelennek meg, amikor a. Ha használja a beszélt válaszokat nyújt a robot van konfigurálva, a hang-szöveg transzformációs automatikusan lejátszása.

![Az Android-alkalmazás képernyőképe](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Létrehozni és üzembe helyezni egy alapszintű robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Lásd még
- [Hang-és felhőközpontú virtuális asszisztensek kapcsolatban](voice-first-virtual-assistants.md)
- [Ingyenes beszédszolgáltatások előfizetési kulcs lekérése](get-started.md)
- [Egyéni ébresztési szavakat](speech-devices-sdk-create-kws.md)
- [A közvetlen vonal Speech csatlakozni a robot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Ismerkedés a Java-példák a Githubon](https://aka.ms/csspeech/samples)
