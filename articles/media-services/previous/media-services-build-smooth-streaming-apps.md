---
title: A Windows Áruházbeli alkalmazások zökkenőmentes streamelése bemutató | Microsoft dokumentumok
description: Megtudhatja, hogy az Azure Media Services használatával hogyan hozhat létre C# Windows Áruházbeli alkalmazást XML MediaElement vezérlővel a Sima adatfolyam-tartalom lejátszásához.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 9ff961638aa170948d51793a21e86d18dd7e1d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016792"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Hogyan készítsünk egy sima streaming Windows Áruházbeli alkalmazás  

A Windows 8 zökkenőmentes encikli-ügyfélsdka lehetővé teszi a fejlesztők számára, hogy olyan Windows Áruházbeli alkalmazásokat építsenek, amelyek igény szerint és élő, zökkenőmentes streamelésű tartalmakat képesek lejátszani. A Smooth Streaming tartalom alapvető lejátszása mellett az SDK olyan gazdag funkciókat is kínál, mint a Microsoft PlayReady védelem, a minőségi szint korlátozása, a Live DVR, az audio streamváltás, az állapotfrissítések hallgatása (például a minőségi szint módosítása) és hibaesemények, és így tovább. A támogatott szolgáltatásokról további információt a [kiadási megjegyzésekben talál.](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes) További információt a [Windows 8 Player Framework for For. című](https://playerframework.codeplex.com/)témakörben talál. 

Ez az oktatóanyag négy leckét tartalmaz:

1. Egyszerű, sima streamelési tárolóalkalmazás létrehozása
2. Csúszka hozzáadása a médiaállapot szabályozásához
3. Sima streamelési adatfolyamok kiválasztása
4. Sima streamelési sávok kiválasztása

## <a name="prerequisites"></a>Előfeltételek
> [!NOTE]
> A Visual Studio 2017 nem támogatja a 8.1-es vagy korábbi Windows Áruházbeli projekteket.  További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8 32 bites vagy 64 bites.
* Visual Studio 2012-től 2015-ig.
* [Microsoft Smooth Streaming Client SDK Windows 8 rendszerhez](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Az egyes leckék kitöltött megoldása letölthető az MSDN fejlesztői kódmintákból (Kódtár): 

* [1. lecke](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - Egy egyszerű Windows 8 Smooth Streaming Media Player, 
* [2. lecke](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - Egy egyszerű Windows 8 Smooth Streaming Media Player egy slidebar vezérlő, 
* [3. lecke](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - A Windows 8 Smooth Streaming Media Player a Stream Selection,  
* [4. lecke](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - A Windows 8 Smooth Streaming Media Player a Track Selection.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>1. lecke: Hozzon létre egy egyszerű smooth streaming store alkalmazást

Ebben a leckében egy MediaElement vezérlővel rendelkező Windows Áruházbeli alkalmazást hoz létre a Sima adatfolyam-tartalom lejátszásához.  A futó alkalmazás így néz ki:

![Példa a Zökkenőmentes streameléses Windows Áruházbeli alkalmazásra][PlayerApplication]

A Windows Áruházbeli alkalmazások fejlesztéséről a [Windows 8-hoz készült nagyszerű alkalmazások fejlesztése](https://msdn.microsoft.com/windows/apps/br229512.aspx)című témakörben talál további információt. Ez a lecke a következő eljárásokat tartalmazza:

1. Windows Áruházbeli projekt létrehozása
2. A felhasználói felület (XAML) tervezése
3. A fájl mögötti kód módosítása
4. Az alkalmazás fordítása és tesztelése

### <a name="to-create-a-windows-store-project"></a>Windows Áruházbeli projekt létrehozása

1. A Visual Studio futtatása; 2012 és 2015 között.
1. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.
1. Az Új projekt párbeszédpanelen írja be vagy jelölje ki a következő értékeket:

    | Név | Érték |
    | --- | --- |
    | Sabloncsoport |Telepített/Sablonok/Visual C#/Windows Áruház |
    | Sablon |Üres alkalmazás (XAML) |
    | Név |SSPlayer |
    | Hely |C:\SSTutorials |
    | Megoldás neve |SSPlayer |
    | Könyvtár létrehozása megoldáshoz |(kiválasztva) |

1. Kattintson az **OK** gombra.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Hivatkozás hozzáadása a simított streamelési ügyfél SDK-hoz

1. A Megoldáskezelőben kattintson a jobb gombbal az **SSPlayer**elemre, majd kattintson **a Hivatkozás hozzáadása parancsra.**
1. Írja be vagy válassza ki az alábbi értékeket:

    | Név | Érték |
    | --- | --- |
    | Referenciacsoport |Windows/Bővítmények |
    | Referencia |Válassza a Microsoft Smooth Streaming Client SDK for Windows 8 és a Microsoft Visual C++ runtime package csomagot |

1. Kattintson az **OK** gombra. 

A hivatkozások hozzáadása után ki kell választania a célzott platformot (x64 vagy x86), a hivatkozások hozzáadása nem fog működni bármely CPU platformkonfiguráció esetén.  A megoldáskezelőben sárga figyelmeztető jel jelenik meg a hozzáadott hivatkozásokhoz.

### <a name="to-design-the-player-user-interface"></a>A lejátszó felhasználói felületének megtervezése

1. A Megoldáskezelőben kattintson duplán a **MainPage.xaml** ikonra a tervezőnézetben való megnyitásához.
2. Keresse ** &lt;meg&gt; ** az XAML-fájlt címkéző rács és ** &lt;/grid&gt; ** címkéket, és illessze be a következő kódot a két címke közé:

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
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
   ```
   A MediaElement vezérlő az adathordozók lejátszására szolgál. A következő leckében a sliderProgress nevű csúszka vezérlővel szabályozza a médiaelőrehaladását.
3. A fájl mentéséhez nyomja le a **CTRL+S** billentyűkombinációt.

A MediaElement vezérlő nem támogatja a tartalom zökkenőmentes streamelését. A Simított streamelés támogatás engedélyezéséhez regisztrálnia kell a Smooth Streaming byte-stream kezelőt a fájlnévkiterjesztés és a MIME-típus szerint.  A regisztrációhoz használja a Windows.Media névtér MediaExtensionManager.RegisterByteStreamHandler metódusát.

Ebben az XAML-fájlban néhány eseménykezelő a vezérlőkhöz van társítva.  Meg kell határoznia ezeket az eseménykezelőket.

### <a name="to-modify-the-code-behind-file"></a>A fájl mögötti kód módosítása

1. A Megoldáskezelőben kattintson a jobb gombbal a **MainPage.xaml**elemre, majd kattintson **a Kód megtekintése parancsra.**
2. A fájl tetején adja hozzá a következő utasítást:
   
        using Windows.Media;
3. A **MainPage** osztály elején adja hozzá a következő adattagot:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. A **MainPage** konstruktor végén adja hozzá a következő két sort:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. A **MainPage** osztály végén illessze be a következő kódot:
   ```csharp
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
   ```
   A sliderProgress_PointerPressed eseménykezelő itt van definiálva.  Vannak több munkák -hoz csinál -hoz kap ez működő, melyik lesz borított -ban következő lecke ebből konzultáció.
6. A fájl mentéséhez nyomja le a **CTRL+S** billentyűkombinációt.

A kész a kód mögött fájlt így kell kinéznie:

![Codeview a Visual Studio of Smooth Streaming Windows Áruházalkalmazásban][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>Az alkalmazás fordítása és tesztelése

1. A **BUILD menüben** kattintson a **Configuration Manager parancsra.**
2. Az **Aktív megoldás platformot** a fejlesztői platformnak megfelelően módosíthatja.
3. A projekt fordításához nyomja le az **F6** billentyűt. 
4. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
5. Az alkalmazás tetején használhatja az alapértelmezett sima streamelési URL-címet, vagy megadhat egy másikat. 
6. Kattintson **a Forrás beállítása gombra.** Mivel az **automatikus lejátszás** alapértelmezés szerint engedélyezve van, a médiafájlok lejátszása automatikusan megtörténik.  A médiafájlokat a **Lejátszás**, **Szünet** és **Leállítás** gombokkal vezérelheti.  Az adathordozó hangerejét a függőleges csúszkával szabályozhatja.  A médiaállapot szabályozására szolgáló vízszintes csúszka azonban még nincs teljesen megvalósítva. 

Befejezted a leckét1.  Ebben a leckében mediaelement vezérlővel játszhatja le a Smooth Streaming tartalmat.  A következő leckében egy csúszkát ad hozzá a Smooth Streaming tartalom előrehaladásának szabályozásához.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>2. lecke: Csúszka hozzáadása a médiaállapot szabályozásához

Az első leckében mediaelement XAML vezérlővel létrehozott egy Windows Áruházbeli alkalmazást a Smooth Streaming médiatartalom lejátszásához.  Jön néhány alapvető média funkciók, mint a start, stop és szünet.  Ebben a leckében egy csúszkavezérlőt adhat az alkalmazáshoz.

Ebben az oktatóanyagban időzítőt használunk a csúszka pozíciójának frissítéséhez a MediaElement vezérlő aktuális helyzete alapján.  A csúszka kezdési és befejezési idejét is frissíteni kell élő tartalom esetén.  Ez jobban kezelhető az adaptív forrásfrissítési eseményben.

A médiaforrások olyan objektumok, amelyek médiaadatokat hoznak létre.  A forrásfeloldó vesz egy URL-címet vagy bájtot, és létrehozza a megfelelő médiaforrást az adott tartalomhoz.  A forrásfeloldó a szabványos módja annak, hogy az alkalmazások médiaforrásokat hozzanak létre. 

Ez a lecke a következő eljárásokat tartalmazza:

1. A sima streamelési kezelő regisztrálása 
2. Adaptív forráskezelő szintű eseménykezelők hozzáadása
3. Adaptív forrásszintű eseménykezelők hozzáadása
4. MediaElement eseménykezelők hozzáadása
5. Csúszkával kapcsolatos kód hozzáadása
6. Az alkalmazás fordítása és tesztelése

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>A Smooth Stream byte-stream kezelő regisztrálása és a tulajdonságkészlet átadása

1. A Megoldáskezelőben kattintson a jobb gombbal **a MainPage.xaml**elemre, majd kattintson **a Kód megtekintése parancsra.**
2. A fájl elején adja hozzá a következő utasítást:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. A MainPage osztály elején adja hozzá a következő adattagokat:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. A **MainPage** konstruktoron belül adja hozzá a következő **kódot. Az összetevők inicializálása();** sor és a regisztrációs kód sorokat írt az előző lecke:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. A **MainPage** konstruktoron belül módosítsa a két RegisterByteStreamHandler metódust a negyedik paraméterek hozzáadásához:

   ```csharp
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
   ```
6. A fájl mentéséhez nyomja le a **CTRL+S** billentyűkombinációt.

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Az adaptív forráskezelő szintű eseménykezelő hozzáadása

1. A Megoldáskezelőben kattintson a jobb gombbal **a MainPage.xaml**elemre, majd kattintson **a Kód megtekintése parancsra.**
2. A **MainPage-osztályon** belül adja hozzá a következő adattagot:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. A **MainPage** osztály végén adja hozzá a következő eseménykezelőt:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. A **MainPage** konstruktor végén adja hozzá a következő sort az adaptív forráskódú nyílt eseményre való előfizetéshez:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. A fájl mentéséhez nyomja le a **CTRL+S** billentyűkombinációt.

### <a name="to-add-adaptive-source-level-event-handlers"></a>Adaptív forrásszintű eseménykezelők hozzáadása

1. A Megoldáskezelőben kattintson a jobb gombbal **a MainPage.xaml**elemre, majd kattintson **a Kód megtekintése parancsra.**
2. A **MainPage-osztályon** belül adja hozzá a következő adattagot:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. A **MainPage** osztály végén adja hozzá a következő eseménykezelőket:

   ```csharp
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
   ```
4. A **mediaElement AdaptiveSourceOpened** metódus végén adja hozzá a következő kódot az eseményekre való feliratkozáshoz:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. A fájl mentéséhez nyomja le a **CTRL+S** billentyűkombinációt.

Ugyanezek az események érhetők el az Adaptive Source jászol szintjén is, amely az alkalmazás összes médiaelemére vonatkozó funkciók kezelésére használható. Minden AdaptiveSource saját eseményt tartalmaz, és az AdaptiveSource-események kaszkádolt lesznek az AdaptiveSourceManager alatt.

### <a name="to-add-media-element-event-handlers"></a>Media Element eseménykezelők hozzáadása

1. A Megoldáskezelőben kattintson a jobb gombbal **a MainPage.xaml**elemre, majd kattintson **a Kód megtekintése parancsra.**
2. A **MainPage** osztály végén adja hozzá a következő eseménykezelőket:

   ```csharp
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
   ```
3. A **MainPage** konstruktor végén adja hozzá a következő kódot az alsó indexhez az eseményekhez:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. A fájl mentéséhez nyomja le a **CTRL+S** billentyűkombinációt.

### <a name="to-add-slider-bar-related-code"></a>Csúszkával kapcsolatos kód hozzáadása

1. A Megoldáskezelőben kattintson a jobb gombbal **a MainPage.xaml**elemre, majd kattintson **a Kód megtekintése parancsra.**
2. A fájl elején adja hozzá a következő utasítást:

   ```csharp
        using Windows.UI.Core;
   ```
3. A **MainPage-osztályon** belül adja hozzá a következő adattagokat:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. A **MainPage** konstruktor végén adja hozzá a következő kódot:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. A **MainPage** osztály végén adja hozzá a következő kódot:

   ```csharp
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
   ```

   > [!NOTE]
   > A CoreDispatcher a felhasználói felület szálának nem felhasználói felületi szálból történő módosítására szolgál. Abban az esetben, ha szűk a diszpécser szál, fejlesztő választhat, hogy használja diszpécser által biztosított UI-elem kívánják frissíteni.  Példa:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. A **mediaElement_AdaptiveSourceStatusUpdated** módszer végén adja hozzá a következő kódot:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. A **MediaOpened** metódus végén adja hozzá a következő kódot:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. A fájl mentéséhez nyomja le a **CTRL+S** billentyűkombinációt.

### <a name="to-compile-and-test-the-application"></a>Az alkalmazás fordítása és tesztelése

1. A projekt fordításához nyomja le az **F6** billentyűt. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Az alkalmazás tetején használhatja az alapértelmezett sima streamelési URL-címet, vagy megadhat egy másikat. 
4. Kattintson **a Forrás beállítása gombra.** 
5. Tesztelje a csúszkát.

Befejezted a 2.  Ebben a leckében egy csúszkát adott az alkalmazáshoz. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>3. lecke: Válassza ki a sima streamfolyamokat
Smooth Streaming képes streamelni tartalmat több nyelvi hangsávot, amelyek választhatók a nézők.  Ebben a leckében lehetővé teszi a nézők számára, hogy kiválasszák az adatfolyamokat. Ez a lecke a következő eljárásokat tartalmazza:

1. Az XAML-fájl módosítása
2. A fájl mögötti kód módosítása
3. Az alkalmazás fordítása és tesztelése

### <a name="to-modify-the-xaml-file"></a>Az XAML-fájl módosítása

1. A Megoldáskezelőben kattintson a jobb gombbal a **MainPage.xaml**elemre, majd kattintson **a Tervező megtekintése**parancsra.
2. Keresse &lt;meg a&gt;Grid.RowDefinitions definíciókat, és módosítsa a RowDefinitions definíciókat úgy, hogy azok a következőkre hasonlítsanak:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. A &lt;&gt;&lt;Rács/Rács&gt; címkéken belül adja hozzá a következő kódot a listamező-vezérlő definiálásához, hogy a felhasználók megtekinthessék az elérhető adatfolyamok listáját, és kiválaszthassák az adatfolyamokat:

   ```xml
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
   ```
4. A módosítások mentéséhez nyomja le a **CTRL+S** billentyűkombinációt.

### <a name="to-modify-the-code-behind-file"></a>A fájl mögötti kód módosítása

1. A Megoldáskezelőben kattintson a jobb gombbal a **MainPage.xaml**elemre, majd kattintson **a Kód megtekintése parancsra.**
2. Az SSPlayer névtérben adjon hozzá egy új osztályt:

   ```csharp
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
   ```
3. A MainPage osztály elején adja hozzá a következő változódefiníciókat:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. A MainPage-osztályon belül adja hozzá a következő területet:
   ```csharp
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
   
            // Select the first video stream from the list if no video stream is selected
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
   
            // Select the first audio stream from the list if no audio steam is selected.
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
   ```
5. Keresse meg a mediaElement_ManifestReady metódust, fűzze hozzá a következő kódot a függvény végén:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Így amikor a MediaElement jegyzékfájl készen áll, a kód megkapja a rendelkezésre álló adatfolyamok listáját, és feltölti a felhasználói felület listáját a listával.
6. A MainPage-osztályon belül keresse meg a felhasználói felület gombjait, kattintson az események régiójára, majd adja hozzá a következő függvénydefiníciót:
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>Az alkalmazás fordítása és tesztelése

1. A projekt fordításához nyomja le az **F6** billentyűt. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Az alkalmazás tetején használhatja az alapértelmezett sima streamelési URL-címet, vagy megadhat egy másikat. 
4. Kattintson **a Forrás beállítása gombra.** 
5. Az alapértelmezett nyelv audio_eng. Próbálj meg váltani audio_eng és audio_es között. Minden alkalommal, amikor új adatfolyamot választ, a Küldés gombra kell kattintania.

Befejezted a 3.  Ebben a leckében hozzáadhatja az adatfolyamok kiválasztásához szükséges funkciókat.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>4. lecke: Válassza ki a Sima streamelési számokat

A Smooth Streaming bemutató több, különböző minőségi szinttel (bitsebességgel) és felbontással kódolt videofájlt tartalmazhat. Ebben a leckében lehetővé teszi a felhasználók számára a számok kiválasztását. Ez a lecke a következő eljárásokat tartalmazza:

1. Az XAML-fájl módosítása
2. A fájl mögötti kód módosítása
3. Az alkalmazás fordítása és tesztelése

### <a name="to-modify-the-xaml-file"></a>Az XAML-fájl módosítása

1. A Megoldáskezelőben kattintson a jobb gombbal a **MainPage.xaml**elemre, majd kattintson **a Tervező megtekintése**parancsra.
2. Keresse &lt;meg&gt; a Grid címkét **gridStreamAndBitrateSelection**néven, és fűzze hozzá a következő kódot a címke végén:
   ```xml
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
   ```
3. A módosítások mentéséhez nyomja le a **CTRL+S** billentyűkombinációt

### <a name="to-modify-the-code-behind-file"></a>A fájl mögötti kód módosítása

1. A Megoldáskezelőben kattintson a jobb gombbal a **MainPage.xaml**elemre, majd kattintson **a Kód megtekintése parancsra.**
2. Az SSPlayer névtérben adjon hozzá egy új osztályt:
   ```csharp
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
   ```
3. A MainPage osztály elején adja hozzá a következő változódefiníciókat:
   ```csharp
        private List<Track> availableTracks;
   ```
4. A MainPage-osztályon belül adja hozzá a következő területet:
   ```csharp
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
   ```
5. Keresse meg a mediaElement_ManifestReady metódust, fűzze hozzá a következő kódot a függvény végén:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. A MainPage-osztályon belül keresse meg a felhasználói felület gombjait, kattintson az események régiójára, majd adja hozzá a következő függvénydefiníciót:
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>Az alkalmazás fordítása és tesztelése

1. A projekt fordításához nyomja le az **F6** billentyűt. 
2. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.
3. Az alkalmazás tetején használhatja az alapértelmezett sima streamelési URL-címet, vagy megadhat egy másikat. 
4. Kattintson **a Forrás beállítása gombra.** 
5. Alapértelmezés szerint a videofolyam összes sávja ki van jelölve. A kísérletezéshez az átviteli sebesség változásait, kiválaszthatja a rendelkezésre álló legalacsonyabb átviteli sebességet, majd kiválaszthatja a rendelkezésre álló legmagasabb átviteli sebességet. Minden módosítás után a Küldés gombra kell kattintania.  Láthatja a videó minőségének változásait.

Befejezted a 4.  Ebben a leckében hozzáadhatja a számok kiválasztásához szükséges funkciókat.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Egyéb források:
* [Hogyan építsünk egy Sima Streaming Windows 8 JavaScript alkalmazás speciális funkciókkal](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Zökkenőmentes streameléstechnikai áttekintés](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

