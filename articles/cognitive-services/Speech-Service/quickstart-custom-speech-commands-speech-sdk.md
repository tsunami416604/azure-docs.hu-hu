---
title: 'Rövid útmutató: Csatlakozás egyéni parancsok alkalmazáshoz beszédfelismerési SDK- beszédfelismerési szolgáltatással'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben egy beszédbeszéd SDK-ügyfélalkalmazást hoz létre egyéni parancsokkal.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 9e324af0b90f595b5b7af2a417a562efb193d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156777"
---
# <a name="quickstart-connect-to-a-custom-commands-application-with-the-speech-sdk-preview"></a>Rövid útmutató: Csatlakozás egyéni parancsok alkalmazáshoz a beszédfelismerési SDK-val (előzetes verzió)

A hosztolt egyéni parancsok alkalmazás létrehozása után megkezdheti a beszélgetést egy ügyféleszközről.

Ebben a cikkben a következőket fogja:

- Egyéni parancsok alkalmazás közzététele és alkalmazásazonosító (alkalmazásazonosító) bekésezése
- Ügyfélalkalmazás létrehozása a Beszéd SDK használatával, amely lehetővé teszi az egyéni parancsok alkalmazással való beszélgetéshez

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához egyéni parancsokra van szükség. Ha még nem hozott létre egyéni parancsok alkalmazást, ezt az alábbi rövid útmutatókban teheti meg:

- [Rövid útmutató: Egyéni parancs létrehozása (előzetes verzió)](./quickstart-custom-speech-commands-create-new.md)
- [Rövid útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)

A következőkre is szüksége lesz:

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- A Beszédszolgáltatások Azure-előfizetési kulcsa. [Szerezzen be egyet ingyen,](get-started.md) vagy hozza létre az [Azure Portalon](https://portal.azure.com)

## <a name="optional-get-started-fast"></a>Nem kötelező: Gyors kezdés

Ez a rövid útmutató lépésről lépésre ismerteti, hogyan lehet egy ügyfélalkalmazást az egyéni parancsok alkalmazáshoz való csatlakozáshoz. Ha közvetlenül a merülést szeretné használni, az ebben a rövid útmutatóban használt teljes, fordításra `quickstart` kész forráskód a mappában található [Beszéd SDK-mintákban](https://aka.ms/csspeech/samples) érhető el.

## <a name="step-1-publish-custom-commands-application"></a>1. lépés: Egyéni parancsok közzététele alkalmazás

1. A [korábban létrehozott Egyéni parancsok alkalmazás](./quickstart-custom-speech-commands-create-new.md) megnyitása és a **Közzététel**

   > [!div class="mx-imgBorder"]
   > ![Az alkalmazás közzététele](media/custom-speech-commands/fulfill-sdk-publish-application.png)

1. Az alkalmazásazonosító másolása a közzétételi értesítésből későbbi használatra

## <a name="step-2-create-a-visual-studio-project"></a>2. lépés: Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>3. lépés: Mintakód hozzáadása

Ebben a lépésben hozzáadjuk az XAML-kódot, amely meghatározza az alkalmazás felhasználói felületét, és hozzáadjuk a C# kód-mögötti implementációt.

### <a name="xaml-code"></a>XAML-kód

Az alkalmazás felhasználói felületének létrehozása az XAML-kód hozzáadásával.

1. A **Solution Explorer**programban nyissa meg a`MainPage.xaml`

1. A tervező XAML nézetében cserélje le a teljes tartalmat a következő kódrészletre:

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
               <Button x:Name="ListenButton" Content="Talk"
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

### <a name="c-code-behind-source"></a>C# háttérkód forrás

Adja hozzá a háttérkód forrását, hogy az alkalmazás a várt módon működjön. A kód mögötti forrás a következőket tartalmazza:

- A `using` névterekhez `Speech` `Speech.Dialog` és a névterekhez szükséges utasítások
- Egyszerű implementáció a mikrofonhoz való hozzáférés biztosításához, gombkezelőhöz kötve
- Alapvető felhasználói felületi segítők az alkalmazásban lévő üzenetek és hibák bemutatásához
- Az inicializálási kód útvonalának olyan leszállási pontja, amely később lesz feltöltve
- Segítő a szövegfelolvasás lelejátszásához (folyamatos átvitel támogatása nélkül)
- Üres gombkezelő, amely később kezdi el hallgatni a zenét

Adja hozzá a kód mögötti forrást az alábbiak szerint:

1. A **Solution Explorer**programban nyissa meg `MainPage.xaml.cs` a háttérkódforrásfájlt (a csoportba `MainPage.xaml`csoportosítva)

1. Cserélje le a fájl tartalmát a következő kódra:

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

1. Adja hozzá a következő kódot a`InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Cserélje le `YourApplicationId`a `YourSpeechSubscriptionKey`karakterláncokat , és `YourServiceRegion` az alkalmazás, a beszédalapú előfizetés és [a régió](regions.md) saját értékeire

1. Fűzze hozzá a következő kódrészletet a módszertörzs ének végéhez:`InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
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

1. Adja hozzá a következő kódrészletet `ListenButton_ButtonClicked` az `MainPage` osztályban lévő metódus törzséhez

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. A menüsorösszes **mentése** > a módosítások mentéséhez válassza az**összes mentése** lehetőséget.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. A menüsorban válassza a Build megoldás **összeállítása** > **Build Solution** az alkalmazás létrehozásához. A kód fordításának hiba nélkül kell végbe mennie.

1. Az alkalmazás elindításához válassza a **Debug** > **Start Debugging** (vagy **az F5)** billentyűt. Megjelenik **a helloworld** ablak.

   ![Minta UWP virtuális asszisztens alkalmazás C# - rövid útmutató](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Válassza **a Mikrofon engedélyezése**lehetőséget. Ha megjelenik a hozzáférési engedélykérés, válassza az **Igen**lehetőséget.

   ![Mikrofonhozzáférési engedély kérése](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Válassza a **Csevegés**lehetőséget, és beszéljen egy angol kifejezést vagy mondatot az eszköz mikrofonjába. A beszéd továbbítása a Közvetlen vonal beszédcsatornájára kerül, és átírja az ablakban megjelenő szöveggé.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: Parancsok teljesítése az ügyfélen a Speech SDK (előzetes verzió)](./how-to-custom-speech-commands-fulfill-sdk.md)
> [Hogyan: Érvényesítés hozzáadása az egyéni parancsparaméterekhez (előzetes verzió)](./how-to-custom-speech-commands-validations.md)
