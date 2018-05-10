---
title: Windows Áruházbeli alkalmazás oktatóanyag Streaming sima |} Microsoft Docs
description: Útmutató az Azure Media Services segítségével hozzon létre egy C# Windows Áruházbeli alkalmazást a lejátszás Smooth Stream XML MediaElement vezérlőt tartalom.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: juliako
ms.openlocfilehash: f55e8aa652d65ea751a77742fa5823b09d9ee87b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Egy Smooth Streaming Windows áruház-alkalmazás létrehozása

A Smooth Streaming ügyfél SDK a Windows 8 lehetővé teszi, hogy a fejlesztők számára a Windows Áruházbeli alkalmazások játszhatja igény szerinti és élő Smooth Streaming tartalmát. Tartalom Smooth Streaming alapvető lejátszását, mellett az SDK-t is biztosít gazdag jellegzetességeket, például Microsoft PlayReady védelmi, minőségi korlátozást, Live DVR, hangadatfolyam váltás, figyeli a állapot frissítéseket (például a minőségi megváltozik) és hibaesemények, és így tovább. A támogatott szolgáltatás további információkért lásd: a [kibocsátási megjegyzéseket](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). További információkért lásd: [Player keretrendszer Windows 8](http://playerframework.codeplex.com/). 

Ez az oktatóanyag során négy tapasztalatokat tartalmazza:

1. Alapszintű zökkenőmentes adatfolyam áruház-alkalmazás létrehozása
2. A csúszka sávjának Media végrehajtási vezérlésére hozzáadása
3. Válassza ki a Smooth Streaming adatfolyamok
4. Válassza ki a Smooth Streaming nyomon követi

## <a name="prerequisites"></a>Előfeltételek
> [!NOTE]
> Windows áruház projektek verzió 8.1 és korábbi verziók nem támogatottak a Visual Studio 2017.  További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8 rendszeren futó 32 bites vagy 64 bites.
* A Visual Studio-verziók 2012 2015 keresztül.
* [Microsoft Smooth Streaming ügyfél SDK a Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

A kész megoldást az egyes letölthető MSDN fejlesztői mintakódok (Kódgalériából): 

* [1 rész](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) – egy egyszerű Windows 8 Smooth Streaming Media Player 
* [2 rész](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) – egy egyszerű Windows 8 Smooth Streaming Media Player a csúszka a vezérlő, 
* [3 rész](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) – A Windows 8 Smooth Streaming Media Player adatfolyam választás  
* [Rész 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) – A Windows 8 Smooth Streaming Media Player követése választás.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>1. lecke: Alapvető zökkenőmentes adatfolyam áruház-alkalmazás létrehozása

Ez a lecke hoz létre a Windows Áruházbeli alkalmazások számára, hogy Smooth Stream egy MediaElement Control tartalom.  A futó alkalmazások néz ki:

![Példa a Smooth Streaming Windows Áruházbeli alkalmazás][PlayerApplication]

További információ a Windows Áruházbeli alkalmazások fejlesztése: [fejlesztése kiváló alkalmazások a Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). Ez a lecke az alábbi eljárásokat tartalmazza:

1. Windows áruház-projekt létrehozása
2. A felhasználói felület (XAML) tervezése
3. Módosítsa a fájl mögötti kódban
4. Fordítsa le, és az alkalmazás tesztelése

**A Windows áruház-projekt létrehozása**

1. Futtassa a Visual Studio; 2012 keresztül 2015-verziókat támogatja.
2. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.
3. Új projekt párbeszédpanelen írja be vagy válassza ki a következő értékeket:

| Name (Név) | Érték |
| --- | --- |
| Sablon csoport |Telepített/sablonok/Visual C# / Windows Áruházbeli |
| Sablon |Üres alkalmazás (XAML) |
| Name (Név) |SSPlayer |
| Hely |C:\SSTutorials |
| Megoldás neve |SSPlayer |
| A megoldáshoz könyvtár létrehozása |(kijelölt) |

1. Kattintson az **OK** gombra.

**A Smooth Streaming ügyfél SDK mutató hivatkozás hozzáadása**

1. A Megoldáskezelőben kattintson a jobb gombbal **SSPlayer**, és kattintson a **hivatkozás hozzáadása**.
2. Írja be vagy válassza ki az alábbi értékeket:

| Name (Név) | Érték |
| --- | --- |
| Referencia-csoport |Windows/bővítmények |
| Leírások |Válassza ki a Microsoft Smooth Streaming ügyfél SDK a Windows 8 és a Microsoft Visual C++ futásidejű csomag |

1. Kattintson az **OK** gombra. 

Miután hozzáadta a hivatkozásokat, ki kell választania a megcélzott platform (x64 vagy x86), Any CPU platform konfiguráció hozzáadása hivatkozások fog működni.  A megoldáskezelőben látni fogja, sárga figyelmeztető megjelölés ezek hozzá hivatkozásokat.

**Megtervezheti a player felhasználói felülete**

1. A Megoldáskezelőben kattintson duplán **MainPage.xaml** való megnyitásához a tervezési nézetben.
2. Keresse meg a **&lt;rács&gt;** és **&lt;/Grid&gt;** az XAML-fájlt, és a két címkék között az alábbi kódot:

         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   
   A MediaElement vezérlő lejátszás adathordozó segítségével. A csúszka sliderProgress nevű media végrehajtási vezérlésére használható a következő lecke.
3. Nyomja le az **CTRL + S** fájl mentéséhez.

A MediaElement vezérlő nem támogatja a Smooth Streaming tartalom out-of-box. A Smooth Streaming támogatását engedélyezi, regisztrálnia kell a Smooth Streaming bájtos-adatfolyam kezelő kiterjesztésű és MIME-típus.  Regisztrálásához a MediaExtensionManager.RegisterByteStremHandler módszer használatával a Windows.Media névtér.

Az XAML-fájl, a néhány eseménykezelők vezérlők társítva.  Meg kell adnia azokat eseménykezelők.

**A fájl mögötti kódban módosítása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. A fájl felső részén adja hozzá a következő using utasítást:
   
        using Windows.Media;
3. Elején a **MainPage** osztályban adja hozzá a következő adatelem:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. Végén a **MainPage** konstruktor, adja hozzá a következő sort:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. Végén a **MainPage** osztály, az alábbi kódot:
   
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion

A sliderProgress_PointerPressed eseménykezelő itt van definiálva.  Nincsenek további munkálatok ehhez működéséhez, ez az oktatóanyag következő lecke tárgyalja.
6. Nyomja le az **CTRL + S** fájl mentéséhez.

A kész fájl mögötti kódban kell kinéznie:

![A Visual Studio, Smooth Streaming Windows Áruházbeli alkalmazás Codeview][CodeViewPic]

**Fordításához és az alkalmazás tesztelése**

1. Az a **BUILD** menüben kattintson a **Configuration Manager**.
2. Változás **aktív megoldás platform** a fejlesztői platform kereséséhez.
3. Nyomja le az **F6** összeállítani a projektet. 
4. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
5. A lap tetején az alkalmazás használja az alapértelmezett Smooth Streaming URL-címet, vagy adjon meg egy másik. 
6. Kattintson a **forrás beállítása**. Mivel **automatikus lejátszása** engedélyezve van alapértelmezés szerint az adathordozót kell lejátszása automatikusan.  Az adathordozó segítségével szabályozhatja a **lejátszása**, **szünet** és **leállítása** gombok.  A függőleges csúszkát media kötet szabályozhatja.  Azonban a vízszintes csúszkát media végrehajtási szabályozásának teljesen még nem használható. 

Lesson1 befejeződött.  Ez a lecke Smooth Streaming tartalmak lejátszásához MediaElement vezérlőelem segítségével.  A következő lecke adhat egy csúszkát a Smooth Streaming tartalom állapotának vezérlésére.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>2. lecke: Hozzáadása egy csúszkát a szabályozhatja a Media folyamatban

1. lecke egy Windows Áruházbeli alkalmazással egy MediaElement XAML-vezérléssel lejátszásához médiatartalom Smooth Streaming létrehozott.  Néhány alapvető adathordozó funkciók, például a start, stop, várjon származik.  Ez a lecke a csúszka sávjának fog hozzáadni az alkalmazáshoz.

Ebben az oktatóanyagban egy időzítő segítségével frissítse a csúszka helyzete a MediaElement vezérlő aktuális helyzete alapján.  A csúszka kezdő és záró idő is élő tartalmak esetén frissítésre szorulnak.  Ez jobban kezelhető az adaptív forrás frissítés esemény.

Források olyan objektumok, amelyek hozhat létre a media adatokat.  A forrás-feloldó URL-címe vagy bájtos adatfolyam vesz igénybe, és létrehozza a megfelelő médiaforrást az adott tartalmakra vonatkozóan.  A forrás-feloldó szabványos módja a források létrehozásához az alkalmazásokhoz. 

Ez a lecke az alábbi eljárásokat tartalmazza:

1. Regisztrálja a Smooth Streaming-kezelő 
2. Az adaptív forrás manager szintű eseménykezelőinek hozzáadása
3. Az adaptív forrás eseménykezelőinek hozzáadása
4. Az eseménykezelők MediaElement hozzáadása
5. Adja hozzá a csúszka kapcsolódó vonalkódja
6. Fordítsa le, és az alkalmazás tesztelése

**A Smooth Streaming bájtos-adatfolyam kezelő regisztrálja, és adja át a propertyset**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. A fájl elején adja hozzá a következő using utasítást:

        using Microsoft.Media.AdaptiveStreaming;
3. A MainPage osztály elején a következő adatok tagok hozzáadása:

         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
4. Belül a **MainPage** konstruktor, után az alábbi kódot a **ez. Components(); inicializálása**  vonal- és a regisztrációs kód az előző leckében sorok:

        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
5. Belül a **MainPage** konstruktor, módosítsa a két RegisterByteStreamHandler módszerek hozzáadása az oda-paraméterek:

         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
6. Nyomja le az **CTRL + S** fájl mentéséhez.

**Az adaptív forrás manager szintű eseménykezelő**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. Belül a **MainPage** osztályban adja hozzá a következő adatelem:
   
     személyes AdaptiveSource adaptiveSource = null;
3. Végén a **MainPage** osztályban adja hozzá a következő eseménykezelő:
   
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
4. Végén a **MainPage** konstruktor, adja hozzá a következő sort az adaptív forrás open esemény előfizetni:
   
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
5. Nyomja le az **CTRL + S** fájl mentéséhez.

**Adaptív forrás szintű eseménykezelők hozzáadása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. Belül a **MainPage** osztályban adja hozzá a következő adatelem:
   
     személyes AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate;   személyes jegyzék manifestObject;
3. Végén a **MainPage** osztályban adja hozzá a következő eseménykezelők:

         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
4. Végén a **mediaElement AdaptiveSourceOpened** módszer, adja hozzá a következő kódot az események előfizetni:
   
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
5. Nyomja le az **CTRL + S** fájl mentéséhez.

Az azonos események adaptív forrás Manager szinten is, amely az alkalmazásban lévő összes adathordozót elemére közös funkciók kezelésére használható érhetők el. Minden egyes AdaptiveSource saját eseményeket is tartalmazza, és minden AdaptiveSource események átkerül a AdaptiveSourceManager.

**Media elem eseménykezelők hozzáadása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. Végén a **MainPage** osztályban adja hozzá a következő eseménykezelők:

         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
3. Végén a **MainPage** konstruktor aláírása az azokhoz az eseményekhez, adja hozzá a következő kódot:

         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
4. Nyomja le az **CTRL + S** fájl mentéséhez.

**Adja hozzá a csúszka sávjának kapcsolódó kódot**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. A fájl elején adja hozzá a következő using utasítást:
      
        using Windows.UI.Core;
3. Belül a **MainPage** osztályban adja hozzá a következő Adattagok:
   
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
4. Végén a **MainPage** konstruktor, adja hozzá a következő kódot:
   
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
5. Végén a **MainPage** osztály, adja hozzá a következő kódot:

         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
      
>[!NOTE]
>Módosíthatja a felhasználói felület szálán nem UI-szálból CoreDispatcher szolgál. Esetén a dispatcher száltól szűk többé kíván frissíteni a felhasználói felületi elem által megadott kézbesítő használhat fejlesztői.  Példa:
   
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
6. Végén a **mediaElement_AdaptiveSourceStatusUpdated** módszer, adja hozzá a következő kódot:

         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
7. Végén a **MediaOpened** módszer, adja hozzá a következő kódot:

         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
8. Nyomja le az **CTRL + S** fájl mentéséhez.

**Fordításához és az alkalmazás tesztelése**

1. Nyomja le az **F6** összeállítani a projektet. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. A lap tetején az alkalmazás használja az alapértelmezett Smooth Streaming URL-címet, vagy adjon meg egy másik. 
4. Kattintson a **forrás beállítása**. 
5. A csúszka sávjának tesztelése.

2. lecke befejeződött.  Ez a lecke hozzáadott alkalmazás egy csúszkát. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>3. lecke: Válassza ki a Smooth Streaming adatfolyamok
Smooth Streaming több nyelv zeneszámok, amelyek választható a hozzáférhetnek a tartalomátvitelre kezelésére képes.  Ez a lecke teszi lehetővé megjelenítők adatfolyamok kiválasztásához. Ez a lecke az alábbi eljárásokat tartalmazza:

1. Az XAML-fájl módosítása
2. A kód behand fájl módosítása
3. Fordítsa le, és az alkalmazás tesztelése

**Az XAML-fájl módosítása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **adatforrásnézet-tervezőből**.
2. Keresse meg &lt;Grid.RowDefinitions&gt;, és módosítja a RowDefinitions, azok a következőhöz hasonló:
   
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
3. Belül a &lt;rács&gt;&lt;/Grid&gt; címkék, adja hozzá a listbox vezérlő megadásához, így a felhasználók a rendelkezésre álló adatfolyamok listájának, és válassza ki az adatfolyamokat a következő kódot:

         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
4. Nyomja le az **CTRL + S** menti a módosításokat.

**A fájl mögötti kódban módosítása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. Az SSPlayer névtéren belül adjon meg egy új osztályt:
   
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
3. A MainPage osztály elején adja hozzá a következő változó definíciók:
   
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
4. Az MainPage osztály adja hozzá a következő régióban:
   
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the frist video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the frist audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
5. Keresse meg a mediaElement_ManifestReady metódust, az alábbi kódot a függvény végén hozzáfűzése:
   
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   
    Amikor készen áll a MediaElement jegyzék, a kódot a rendelkezésre álló adatfolyamok listájának lekérése, tölti fel a felhasználói felület listát a listában.
6. Az MainPage osztály keresse meg a felhasználói felület gombok események régió parancsára, és adja hozzá az a következő függvény definíciójának:
   
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Fordításához és az alkalmazás tesztelése**

1. Nyomja le az **F6** összeállítani a projektet. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. A lap tetején az alkalmazás használja az alapértelmezett Smooth Streaming URL-címet, vagy adjon meg egy másik. 
4. Kattintson a **forrás beállítása**. 
5. Az alapértelmezett nyelv a audio_eng. Próbálja audio_eng és audio_es közötti váltáshoz. Everytime, egy új adatfolyam választja, a Küldés gombra kell kattintania.

3. lecke befejeződött.  Ez a lecke adja hozzá a képességet az adatfolyamok válassza.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>4. lecke: Válassza ki a Smooth Streaming nyomon követi
Egy Smooth Streaming bemutató különböző szolgáltatásminőségi szinteket (átviteli sebességek) és a megoldások kódolású több videó fájlokat tartalmazza. Ez a lecke teszi lehetővé felhasználóknak, hogy nyomon követi. Ez a lecke az alábbi eljárásokat tartalmazza:

1. Az XAML-fájl módosítása
2. A kód behand fájl módosítása
3. Fordítsa le, és az alkalmazás tesztelése

**Az XAML-fájl módosítása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **adatforrásnézet-tervezőből**.
2. Keresse meg a &lt;rács&gt; nevű címke **gridStreamAndBitrateSelection**, a következő kódot a címke végén hozzáfűzése:
   
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
3. Nyomja le az **CTRL + S** helykiszolgálójához módosítások mentése

**A fájl mögötti kódban módosítása**

1. A Megoldáskezelőben kattintson a jobb gombbal **MainPage.xaml**, és kattintson a **nézet kód**.
2. Az SSPlayer névtéren belül adjon meg egy új osztályt:
   
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
3. A MainPage osztály elején adja hozzá a következő változó definíciók:
   
        private List<Track> availableTracks;
4. Az MainPage osztály adja hozzá a következő régióban:
   
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
5. Keresse meg a mediaElement_ManifestReady metódust, az alábbi kódot a függvény végén hozzáfűzése:
   
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
6. Az MainPage osztály keresse meg a felhasználói felület gombok események régió parancsára, és adja hozzá az a következő függvény definíciójának:
   
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }

**Fordításához és az alkalmazás tesztelése**

1. Nyomja le az **F6** összeállítani a projektet. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. A lap tetején az alkalmazás használja az alapértelmezett Smooth Streaming URL-címet, vagy adjon meg egy másik. 
4. Kattintson a **forrás beállítása**. 
5. Alapértelmezés szerint a nyomon követi a video-adatfolyammá alakítja az összes kijelölt. A átviteli sebesség módosítások kísérletezhet, jelölje ki a legalacsonyabb átviteli sebesség, és válassza ki a legnagyobb átviteli sebességet. Minden egyes módosítása után kattintson a küldés.  A jó minőségű módosításokat is láthatják.

4. lecke befejeződött.  Ez a lecke adja hozzá a Funkciók kiválasztása nyomon követi.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Egyéb erőforrások:
* [A speciális funkciók Smooth Streaming Windows 8 JavaScript alkalmazás létrehozása](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Zökkenőmentes adatfolyam műszaki áttekintése](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

