---
title: 'Gyors útmutató: Egyéni hang – első virtuális asszisztens (előzetes verzió) C# , (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben egy C# univerzális Windows-platform-(UWP-) alkalmazást hoz létre a Cognitive Services Speech szoftverfejlesztői készlet (SDK) használatával. Az ügyfélalkalmazás egy korábban létrehozott, a Direct line Speech Channel használatára konfigurált robot-keretrendszerhez csatlakozik. Az alkalmazást a létrehozása a Speech SDK NuGet-csomagot és a Microsoft Visual Studio 2017-ben.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: 6d88bcc6d9d2c2e46ef3a3cb841c9268a544e37c
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609688"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>Gyors útmutató: Hozzon létre egy hang-első virtuális asszisztenst a Speech SDK-val, UWP

A gyors üzembe helyezési útmutató a [szöveg](quickstart-csharp-uwp.md)és a beszéd közötti [kommunikációhoz, valamint](quickstart-text-to-speech-csharp-uwp.md) a beszéd-és [fordításhoz](quickstart-translate-speech-uwp.md)is elérhető.

Ebben a cikkben a [SPEECH SDK](speech-sdk.md)használatával fejleszt C# egy univerzális Windows-platform-(UWP-) alkalmazást. A program egy korábban létrehozott és konfigurált robothoz fog csatlakozni, hogy az ügyfélalkalmazás első virtuális asszisztensi élményét engedélyezze. Az alkalmazás a [SPEECH SDK NuGet csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-es vagy újabb verziójával (bármely kiadással) készült.

> [!NOTE]
> A Universal Windows Platformon olyan alkalmazásokat fejleszthet, amelyek a Windows 10-et támogató minden eszközön futtathatók: PC-n, Xboxon, Surface Hubon stb.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) vagy újabb
* Egy Azure-előfizetési kulcs a Speech Serviceshez. [Szerezze be ingyen](get-started.md) , vagy hozza létre a [Azure Portal](https://portal.azure.com).
* Korábban létrehozott, a [közvetlen vonalas beszéd csatornával](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) konfigurált robot

    > [!NOTE]
    > A közvetlen vonalas beszéd (előzetes verzió) jelenleg a Speech Services-régiók egy részhalmazában érhető el. Tekintse meg [a támogatott régiók listáját a hangvezérelt virtuális asszisztensekhez](regions.md#voice-first-virtual-assistants) , és gondoskodjon arról, hogy az erőforrások az egyik régióban legyenek telepítve.

## <a name="optional-get-started-fast"></a>Nem kötelező: Gyors első lépések

Ez a rövid útmutató részletesen leírja, hogyan lehet egy egyszerű ügyfélalkalmazás számára csatlakozni a beszédfelismerést támogató robothoz. Ha inkább a betöltést választja, az ebben a rövid útmutatóban használt teljes, a fordításra kész forráskód a `quickstart` mappában található [Speech SDK-mintákban](https://aka.ms/csspeech/samples) érhető el.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Az alkalmazás felhasználói felületét az XAML-lel lehet definiálni. Nyissa meg a `MainPage.xaml` fájlt a Megoldáskezelőben. A tervező XAML nézetében cserélje le a teljes tartalmat az alábbira.

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
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" Margin="0,10,10,0" Click="ListenButton_ButtonClicked" Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" RelativePanel.AlignBottomWithPanel="True" RelativePanel.AlignRightWithPanel="True" RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce the status update. -->
                            <TextBlock x:Name="StatusBlock" FontWeight="Bold" AutomationProperties.LiveSetting="Assertive"
                    MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

1. Nyissa meg a forráskód mögötti `MainPage.xaml.cs`forrásfájlt. A csoport a alatt `MainPage.xaml`található. Cserélje le a tartalmát az alábbi kódra. Ez a példa a következőt fedi le:

    * Utasítások használata a beszédfelismeréshez és a beszédekhez. a párbeszédablak névterei
    * Egyszerű implementáció a mikrofon elérésének biztosításához, amely a gomb kezelőjéhez van kötve
    * Alapvető felhasználói felületi segítők az alkalmazásban lévő üzenetek és hibák megjelenítéséhez
    * Az inicializálási kód elérési útjának kiinduló pontja, amelyet később fel kell tölteni
    * A szöveg és a beszéd visszajátszására szolgáló segítő (folyamatos átvitel támogatása nélkül)
    * Egy üres gomb kezelője a figyelés megkezdéséhez, amely később fel lesz töltve

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

            private async void EnableMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) ? strMessage : "\n" + strMessage;

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
                // Raise an event if necessary to enable a screen reader to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for accumulates all audio associated with a given PullAudioOutputStream and then plays it to the
            // MediaElement. Long spoken audio will create extra latency and a streaming playback solution (that plays
            // audio while it continues to be received) should be used -- see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(PullAudioOutputStream activityAudio)
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

                var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Ezután hozza létre az `DialogServiceConnector` előfizetési adatait. Adja hozzá a következőt a metódus törzséhez `InitializeDialogServiceConnector`, és cserélje le a `YourSpeechSubscriptionKey`karakterláncokat `YourChannelSecret`, és `YourServiceRegion` a saját értékeit a robot, a beszéd előfizetése és a [régió](regions.md)számára.

    > [!NOTE]
    > A közvetlen vonalas beszéd (előzetes verzió) jelenleg a Speech Services-régiók egy részhalmazában érhető el. Tekintse meg [a támogatott régiók listáját a hangvezérelt virtuális asszisztensekhez](regions.md#voice-first-virtual-assistants) , és gondoskodjon arról, hogy az erőforrások az egyik régióban legyenek telepítve.

    > [!NOTE]
    > A robot konfigurálásával és a csatorna titkos kódjának beolvasásával kapcsolatos információkért tekintse meg a [Direct line Speech Channel](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)robot Framework dokumentációját.

    ```csharp
    // create a DialogServiceConfig by providing a bot secret key and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret"; // Your channel secret
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region. Note: only a subset of regions are currently supported

    var botConfig = DialogServiceConfig.FromBotSecret(channelSecret, speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

1. `DialogServiceConnector`számos eseményre támaszkodik, hogy a robot tevékenységeit, beszédfelismerési eredményeit és egyéb információit tájékoztassa. Adja hozzá az események kezelőit, és illessze be a következőt a metódus törzsének `InitializeDialogServiceConnector`végére.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

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
            NotifyUser($"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };
    // Recognizing (not 'Recognized') will provide the intermediate recognized text while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };
    // Recognized (not 'Recognizing') will provide the final recognized text once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };
    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };
    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. A létrehozott konfiguráció és az eseménykezelők regisztrálása után a `DialogServiceConnector` most már csak figyelni kell. Adja hozzá a következőt a `ListenButton_ButtonClicked` metódus `MainPage` törzséhez a osztályban.

    ```csharp
    private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
    {
        if (connector == null)
        {
            InitializeDialogServiceConnector();
            // Optional step to speed up first interaction: if not called, connection happens automatically on first use
            var connectTask = connector.ConnectAsync();
        }

        try
        {
            // Start sending audio to your speech-enabled bot
            var listenTask = connector.ListenOnceAsync();

            // You can also send activities to your bot as JSON strings -- Microsoft.Bot.Schema can simplify this
            string speakActivity = @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
            await connector.SendActivityAsync(speakActivity);

        }
        catch (Exception ex)
        {
            NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
        }
    }
    ```

1. Mentse a projekten végrehajtott összes módosítást.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A Visual Studio menüsávján válassza a **Build** > **Build megoldás**elemet. A kód fordításának hiba nélkül végbe kell mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-uwp-08-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A Visual Studio menüsávján válassza a hibakeresés**Indítás hibakeresése** **lehetőséget** > , vagy nyomja le az **F5**billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-uwp-09-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Felugrik egy ablak. Az alkalmazásban válassza a **mikrofon engedélyezése**lehetőséget, és fogadja el az előugró engedélyezési kérelmet.

    ![Az engedélykérés képernyőképe](media/sdk/qs-csharp-uwp-10-access-prompt.png "Hibakeresés indítása az alkalmazáson")

1. Válassza **a beszélgetés**a robottal lehetőséget, és beszéljen egy angol kifejezéssel vagy mondattal az eszköz mikrofonjában. A beszéd a közvetlen vonalas beszéd csatornába kerül, és szövegbe kerül, amely az ablakban jelenik meg.

    ![Képernyőkép a sikeres bot turnről](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png "Sikeres bot-turn")

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alapszintű robot létrehozása és üzembe helyezése](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Lásd még

- [A Voice – First virtuális asszisztensek ismertetése](voice-first-virtual-assistants.md)
- [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
- [Egyéni ébresztési szavak](speech-devices-sdk-create-kws.md)
- [Közvetlen vonalas beszéd összekötése a robottal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)
