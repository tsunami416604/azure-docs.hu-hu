---
title: Face API – C#-oktatóanyag | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ebben az oktatóanyagban egy Windows-alkalmazást hozunk létre, amely a Cognitive Services arcfelismerési szolgáltatása segítségével észleli és bekeretezi a képeken lévő arcokat.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: e4f2192c40f0b650b31ed59642dee89e42eca703
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125939"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image"></a>Oktatóanyag: WPF-alkalmazás létrehozása képeken lévő arcok észleléséhez és bekeretezéséhez

Ebben az oktatóanyagban egy Windows Presentation Framework- (WPF-) alkalmazást fogunk létrehozni, amely a .NET-ügyfélkódtáron keresztül használja az arcfelismerési szolgáltatást. Az alkalmazás észleli a képeken lévő arcokat, keretet rajzol köréjük, és megjeleníti az arc leírását az állapotsávon. A teljes mintakód elérhető a GitHubon: [Arcok észlelése és bekeretezése egy képen a Windowson](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample).

![Képernyőfelvétel téglalappal bekeretezett arcokról](../Images/getting-started-cs-detected.png)

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - WPF-alkalmazás létrehozása
> - Az arcfelismerési szolgáltatás ügyfélkódtárának telepítése
> - Az ügyfélkódtár használata a képeken lévő arcok észleléséhez
> - Keret rajzolása minden észlelt arc köré
> - Az arc leírásának megjelenítése az állapotsávon

## <a name="prerequisites"></a>Előfeltételek

- A minta futtatásához előfizetési kulcs szükséges. Ingyenes próba előfizetési kulcsot itt szerezhet: [A Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása. A Visual Studio 2017-hez szükséges a .NET asztali alkalmazásfejlesztési számítási feladat. Ez az oktatóanyag a Visual Studio 2017 Community Editiont használja.
- A [Microsoft.Azure.CognitiveServices.Vision.Face 2.0.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.0.0-preview) ügyfélkódtár NuGet-csomagja. A csomag letöltése nem szükséges. A telepítési utasításokat az alábbiakban találja.

## <a name="create-the-visual-studio-solution"></a>A Visual Studio-megoldás létrehozása

Kövesse az alábbi lépéseket a Windows WPF-alkalmazásprojekt létrehozásához.

1. Nyissa meg a Visual Studiót, és a **File** (Fájl) menüben kattintson a **New** (Új), majd a **Project** (Projekt) elemre.
   - A Visual Studio 2017-ben bontsa ki az **Installed** (Telepítve) elemet, majd az **Other Languages** (További nyelvek) elemet. Válassza a **Visual C#** lehetőséget, majd a **WPF App (.NET Framework)** (WPF-alkalmazás (.NET-keretrendszer)) elemet.
   - A Visual Studio 2015-ben bontsa ki az **Installed** (Telepítve) elemet, majd a **Templates** (Sablonok) elemet. Válassza a **Visual C#** lehetőséget, majd a **WPF Application** (WPF-alkalmazás) elemet.
1. Adja a **FaceTutorial** nevet az alkalmazásnak, majd kattintson az **OK** gombra.

## <a name="install-the-face-service-client-library"></a>Az arcfelismerési szolgáltatás ügyfélkódtárának telepítése

Kövesse ezeket az utasításokat az ügyfélkódtár telepítéséhez.

1. A **Tools** (Eszközök) menüben válassza a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Package Manager Console** (Csomagkezelői konzol) lehetőséget.
1. A **Package Manager Console** (Csomagkezelői konzol) felületén illessze be a következőt, majd nyomja le az **Enter** billentyűt.

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.0.0-preview`

## <a name="add-the-initial-code"></a>Kezdeti kód hozzáadása

### <a name="mainwindowxaml"></a>MainWindow.xaml

Nyissa meg a *MainWindow.xaml* elemet (tipp: a **fel/le mutató nyíl ikonokkal** válthat a panelek között), majd cserélje le a tartalmakat a következő kódra. Ezen xaml-kóddal jön létre a felhasználói felület ablaka. Megfigyelheti a `FacePhoto_MouseMove` és a `BrowseButton_Click` eseménykezelőt.

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Bontsa ki a *MainWindow.xaml* elemet, majd nyissa meg a *MainWindow.xaml.cs* fájlt, és cserélje le a tartalmát a következő kódra. Hagyja figyelmen kívül a piros hullámvonalas aláhúzást; ezek az első létrehozás után eltűnnek.

Az első két sor importálja az ügyfélkódtár névtereit. Ezután létrejön a `FaceClient`, amely az előfizetési kulcsban lesz átadva, és az Azure-régió be lesz állítva a `MainWindow` konstruktorban. A `BrowseButton_Click` és a `FacePhoto_MouseMove` metódus a *MainWindow.xaml* elemben deklarált eseménykezelőknek felel meg.

A `BrowseButton_Click` létrehoz egy `OpenFileDialog` elemet, ami lehetővé teszi, hogy a felhasználó kiválasszon egy jpg-képet. A képet a rendszer beolvassa és megjeleníti a főablakban. A `BrowseButton_Click` kódja és a `FacePhoto_MouseMove` kódjának fennmaradó része a következő lépésekben lesz beillesztve.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string baseUri =
            "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(baseUri, UriKind.Absolute))
            {
                faceClient.BaseUri = new Uri(baseUri);
            }
            else
            {
                MessageBox.Show(baseUri,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>Az előfizetési kulcs beillesztése és a régió ellenőrzése vagy módosítása

- Keresse meg a következő sort a *MainWindow.xaml.cs* elemben, és cserélje le a `<Subscription Key>` elemet a Face API előfizetési kulcsára:

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- Keresse meg a következő sort a *MainWindow.xaml.cs* elemben, és cserélje le vagy ellenőrizze az előfizetési kulcshoz társított Azure-régiót:

    ```csharp
    private const string baseUri =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
    ```

    Ellenőrizze, hogy a hely ugyanaz-e, ahonnan az előfizetési kulcsot beszerezte. Ha például a **westus** régióból szerezte be az előfizetési kulcsot, cserélje le a `Westcentralus` elemet a `Westus` elemre.

    Ha az ingyenes próbaverzió használatával szerezte be az előfizetési kulcsot, a kulcshoz tartozó régió a **westcentralus**, így nem szükséges módosítania.

### <a name="test-the-app"></a>Az alkalmazás tesztelése

Nyomja meg a **Start** (Indítás) gombot a menüben az alkalmazás teszteléséhez. Amikor megnyílik az ablak, kattintson a **Browse** (Tallózás) gombra a bal alsó sarokban. Megjelenik a **File Open** (Fájl megnyitása) párbeszédpanel, ahol megkereshet és kiválaszthat egy fényképet, amely aztán megjelenik az ablakban.

![Képernyőfelvétel arcokat ábrázoló képről, amely nem lett módosítva](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>Képek feltöltése arcok észleléséhez

Az arcok észlelésének legegyszerűbb módja a `FaceClient.Face.DetectWithStreamAsync` metódus meghívása, amely becsomagolja az [észlelési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-metódust a helyi kép feltöltéséhez.

Illessze be a következő metódust a `FacePhoto_MouseMove` metódus alatti `MainWindow` osztályba.

Létrejön az elemzendő arcattribútumok listája, és a rendszer az elküldött képfájlt beolvassa egy `Stream` elembe. Mindkettő át lesz adva a `DetectWithStreamAsync` hívásnak.

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>Téglalapok rajzolása az arcok köré

Adja hozzá azt a kódot, amellyel téglalap lesz rajzolva a képeken észlelt arcok köré.

A *MainWindow.xaml.cs* elemben adja hozzá az `async` módosítót a `BrowseButton_Click` metódushoz.

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

Illessze be a következő kódot a `BrowseButton_Click` metódus végére, a `FacePhoto.Source = bitmapSource` sor után.

Az észlelt arcok listáját kitölti az `UploadAndDetectFaces` hívása. Ezután a rendszer téglalapot rajzol minden arc köré, és a módosított kép megjelenik a főablakban.

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>A képen lévő arcok leírása

Fűzze a következő metódust az `UploadAndDetectFaces` metódus alatti `MainWindow` osztályhoz.

A metódus az arcot leíró sztringgé alakítja az arcattribútumokat. A sztring megjelenik, amikor az egérmutatóval rámutat a bekeretezett arcra.

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>Az arc leírásának megjelenítése

Cserélje le a `FacePhoto_MouseMove` metódust a következő kódra.

Ez az eseménykezelő megjeleníti az arcleírás sztringjét, amikor az egérmutatóval rámutat egy bekeretezett arcra.

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és keressen egy képet, amelyen egy arc látható. Várjon néhány másodpercet, amíg az arcfelismerési szolgáltatás válaszol. Ezt követően megjelenik egy piros színű téglalap a képen lévő arcok körül. Ha az egérmutatóval rámutat a bekeretezett arcra, megjelenik az arc leírása az állapotsávon.

![Képernyőfelvétel téglalappal bekeretezett arcokról](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>Összegzés

Ebben az oktatóanyagban megismerte az arcfelismerési szolgáltatás ügyfélkódtárának használatához szükséges alapvető folyamatot, és létrehozott egy alkalmazást, amely megjeleníti és bekeretezi az arcokat a képeken.

## <a name="next-steps"></a>További lépések

További tudnivalók arcok részeinek észleléséről és használatáról.

> [!div class="nextstepaction"]
> [Arcok észlelése egy képen](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
