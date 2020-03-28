---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: fb78a78190a6817e1a199f9935fd55e173caa2b0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241163"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](~/articles/cognitive-services/speech-service/get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=uwp)
> * A [közvetlen vonalbeszédcsatornához](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) csatlakoztatott robot létrehozása
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez
> 
  > [!NOTE]
  > Tekintse meg [a támogatott régiók listáját a hang-asszisztensek,](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) és győződjön meg arról, hogy az erőforrások telepítése az egyik ilyen régiókban.

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Az első lépés annak biztosítása, hogy a projekt meg legyen nyitva a Visual Studióban.

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz.

1. A **Megoldáskezelőben**nyissa meg a programot. `MainPage.xaml`

1. A tervező XAML nézetében cserélje le a teljes tartalmat a következő kódrészletre, amely kezdetleges felhasználói felületet határoz meg:

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  
                        Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
                        Margin="0,10,10,0" Click="ListenButton_ButtonClicked" 
                        Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" 
                            RelativePanel.AlignBottomWithPanel="True" 
                            RelativePanel.AlignRightWithPanel="True" 
                            RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" 
                               TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  
                                      VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce 
                                 the status update. -->
                            <TextBlock 
                                x:Name="StatusBlock" FontWeight="Bold" 
                                AutomationProperties.LiveSetting="Assertive"
                                MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" 
                                Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

A Tervező nézet frissül, hogy az alkalmazás felhasználói felülete jelenjen meg.

1. A **Megoldáskezelőben**nyissa meg a `MainPage.xaml.cs`mögötti kód forrásfájlt. (Ez alatt csoportosítva `MainPage.xaml`.) Cserélje le a fájl tartalmát az alábbira, amely a következőket tartalmazza:

- `using`utasítások a `Speech` és `Speech.Dialog` a névterekhez
- Egyszerű implementáció a mikrofonhoz való hozzáférés biztosításához, gombkezelőhöz kötve
- Alapvető felhasználói felületi segítők az alkalmazásban lévő üzenetek és hibák bemutatásához
- Az inicializálási kód útvonalának olyan leszállási pontja, amely később lesz feltöltve
- Segítő a szövegfelolvasás lelejátszásához (folyamatos átvitel támogatása nélkül)
- Üres gombkezelő, amely később kezdi el hallgatni a zenét

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = 
                        new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = 
                        Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(
                        new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(
                string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(
                        Windows.UI.Core.CoreDispatcherPriority.Normal, 
                        () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) 
                    ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader 
                // to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(
                        Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for and accumulates all audio associated with a given 
            // PullAudioOutputStream and then plays it to the MediaElement. Long spoken 
            // audio will create extra latency and a streaming playback solution 
            // (that plays audio while it continues to be received) should be used -- 
            // see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(
                PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(
                    Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(
                        playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```
1. Adja hozzá a következő kódrészletet `InitializeDialogServiceConnector`a metódustörzséhez. Ez a `DialogServiceConnector` kód hozza létre az előfizetési adatokkal.

    ```csharp
    // Create a BotFrameworkConfig by providing a Speech service subscription key
    // the RecoLanguage property is optional (default en-US)
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region.

    var botConfig = BotFrameworkConfig.FromSubscription(speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > Tekintse meg [a támogatott régiók listáját a hang-asszisztensek,](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) és győződjön meg arról, hogy az erőforrások telepítése az egyik ilyen régiókban.

   > [!NOTE]
   > A robot konfigurálásáról a Közvetlen vonal [beszédcsatornájának](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)Bot Framework dokumentációjában olvashat.

1. Cserélje le `YourSpeechSubscriptionKey` a `YourServiceRegion` karakterláncokat és a saját értékeit a beszéd-előfizetéshez és [a régióhoz.](~/articles/cognitive-services/speech-service/regions.md)

1. Fűzze hozzá a következő kódrészletet a metódustörzs `InitializeDialogServiceConnector`végéhez. Ez a kód a robottevékenységeinek, a beszédfelismerési eredményeknek és egyéb információknak a kommunikációja által `DialogServiceConnector` támaszkodott események kezelőit állítja be.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
    connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
    {
        NotifyUser(
            $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser(
                $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };

    // Recognizing (not 'Recognized') will provide the intermediate recognized text 
    // while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };

    // Recognized (not 'Recognizing') will provide the final recognized text 
    // once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };

    // SessionStopped will notify when a turn is complete and 
    // it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Adja hozzá a következő kódrészletet `ListenButton_ButtonClicked` az `MainPage` osztályban lévő metódus törzséhez. Ez a `DialogServiceConnector` kód úgy van beállítva, hogy figyelje, mivel már létrehozta a konfigurációt, és regisztrálta az eseménykezelőket.

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```
    
## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll az alkalmazás létrehozására és az egyéni hangasszisztens tesztelésére a Beszédszolgáltatás használatával.

1. A menüsorban válassza a Build megoldás **összeállítása** > **Build Solution** az alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. Az alkalmazás elindításához válassza a **Debug** > **Start Debugging** (vagy **az F5)** billentyűt. Megjelenik **a helloworld** ablak.

   ![Minta UWP hangsegéd alkalmazás C# - rövid útmutató](~/articles/cognitive-services/Speech-Service/media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Válassza **a Mikrofon engedélyezése**lehetőséget, és amikor megjelenik a hozzáférési engedélykérelem, válassza az **Igen**lehetőséget.

   ![Mikrofonhozzáférési engedély kérése](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Válassza **a Beszélgetés a robottal**lehetőséget, és beszéljen egy angol kifejezést vagy mondatot az eszköz mikrofonjába. A beszéd továbbítása a Közvetlen vonal beszédcsatornájára kerül, és átírja az ablakban megjelenő szöveggé.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
