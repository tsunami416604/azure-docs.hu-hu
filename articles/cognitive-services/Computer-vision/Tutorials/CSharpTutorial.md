---
title: 'Minta: Ismerje meg a lemezkép feldolgozási alkalmazásC#'
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Egy egyszerű Windows-alkalmazást, amely a Computer Vision API a Microsoft Cognitive Services bemutatása. OCR végrehajtása, miniatűrök létrehozása és képek vizuális jellemzőinek használata.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a23e45cb1e75305f41c81077c90282352327b72d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310943"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Minta: Ismerje meg a lemezkép feldolgozási alkalmazásC#

Fedezze fel egy egyszerű Windows-alkalmazás, amely hajtsa végre az optikai karakterfelismerés (OCR), hozzon létre intelligens csonkolva miniatűröket készíthet, és észlelheti, kategorizálását, a címke használ a Computer Vision, és vizuális szolgáltatások, többek között az arcok, a kép ismertetik. Az alábbi példában elküldheti egy kép URL-címét vagy egy helyileg tárolt fájlt. A nyílt forráskódú példa sablonként használható a saját alkalmazást készít a Windows a Computer Vision API és a Windows megjelenítési Alaprendszeri (WPF), a .NET-keretrendszer részeként.

> [!div class="checklist"]
> * A mintaalkalmazás beszerzése a Githubról
> * Nyissa meg és hozhat létre a mintaalkalmazást a Visual Studióban
> * Futtassa a mintaalkalmazást, és hajtsa végre a különböző forgatókönyvekben való használatához
> * A mintaalkalmazás mellékelt különböző-forgatókönyvek megismerése

## <a name="prerequisites"></a>Előfeltételek

A mintaalkalmazás haladóbb győződjön meg arról, hogy teljesítette az alábbi előfeltételek:

* A [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) vagy újabb verzióval kell rendelkeznie.
* Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Az előfizetői azonosító beszerzéséhez lásd az [előfizetői azonosítók beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="get-the-sample-app"></a>Mintaalkalmazás letöltése

A Computer Vision mintaalkalmazást a githubon érhető el a `Microsoft/Cognitive-Vision-Windows` tárház. Ez a tárház is tartalmaz a `Microsoft/Cognitive-Common-Windows` tárházban, a Git submodule. Is rekurzív módon Klónozás az adattár, többek között a submodule keresztül a `git clone --recurse-submodules` parancsot a parancssorból, vagy a GitHub Desktop használatával.

Ha például a rekurzív módon klónozza a tárházat a Computer Vision mintaalkalmazást a parancsot a parancssorba, futtassa a következő parancsot:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Ne töltse le az adattár zip. A Git almodulok nem tartalmazza, egy adattár zip letöltése esetén.

### <a name="get-optional-sample-images"></a>Nem kötelező minta képek bekérése

Igény szerint használhatja a mellékelt mintaképeket a [Face](../../Face/Overview.md) elérhető a Githubon a mintaalkalmazást a `Microsoft/Cognitive-Face-Windows` tárház. A mintaalkalmazás tartalmaz egy mappa `/Data`, amely személyek több lemezképet tartalmaz. Így rekurzív módon Klónozás a tárházban, a Computer Vision mintaalkalmazás ismertetett módszerek.

Ha például a rekurzív módon klónozza a tárházat, a Face mintaalkalmazást a parancsot a parancssorba, futtassa a következő parancsot:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Nyissa meg és hozhat létre a mintaalkalmazást a Visual Studióban

A mintaalkalmazás kell létrehozni, először, úgy, hogy a Visual Studio fel tudja oldani függőségei, mielőtt elindítja a, vagy ismerje meg a mintaalkalmazást. Nyissa meg és hozhat létre a mintaalkalmazást, tegye a következőket:

1. Nyissa meg a megoldásfájlt a Visual Studio `/Sample-WPF/VisionAPI-WPF-Samples.sln`, a Visual Studióban.
1. Győződjön meg arról, hogy a Visual Studio-megoldás két projektet tartalmaz:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   Ha a SampleUserControlLibrary projekt nem érhető el, győződjön meg róla, klónozott rekurzív módon, hogy a `Microsoft/Cognitive-Vision-Windows` tárház.
1. A Visual Studióban nyomja le a Ctrl + Shift + B vagy válasszon **hozhat létre** a menüszalag menüben majd **megoldás fordítása** a megoldás létrehozásához.

## <a name="run-and-interact-with-the-sample-app"></a>Futtassa, és kezelheti a mintaalkalmazás

Futtathatja a mintaalkalmazást, megtekintéséhez, hogy milyen hatással, és a Computer Vision ügyféloldali kódtár a különböző feladatokat, például a miniatűrök létrehozásának vagy címkézési lemezképek végrehajtásakor. Futtassa a mintaalkalmazást interakcióba, és tegye a következőket:

1. A létrehozás befejezése után, vagy nyomja le az **F5** , vagy válasszon **Debug** a menüszalag menüből majd **hibakeresés indítása** , futtassa a mintaalkalmazást.
1. A mintaalkalmazás jelenik meg, amikor **előfizetés kulcskezelés** a navigációs ablaktáblán, az előfizetés kulcskezelés lap megjelenítéséhez.
   ![Előfizetés kulcskezelés lap](../Images/Vision_UI_Subscription.PNG)  
1. Adja meg az előfizetési kulcs a **előfizetési kulcs**.
1. Adja meg a végpont URL-címe, felsorolhatja az `/vision/v1.0`, a Computer Vision erőforrás az előfizetési kulcs **végpont**.  
   Írja be például a a Computer Vision ingyenes próbaverziójáról az előfizetési kulcsot használja, ha a következő végpont URL-címet a Nyugat-India középső velünk a Kapcsolatot az Azure-régió: `https://westcentralus.api.cognitive.microsoft.com`
1. Ha nem szeretné, adja meg az előfizetési kulcs és a végpont URL-cím a következő a mintaalkalmazás futtatásakor válassza **beállítás mentése** az előfizetési kulcs és a végpont URL-cím mentése a számítógépre. Ha törölni szeretné a korábban mentett előfizetési kulcs és a végpont URL-CÍMÉT, válassza a **törlése beállítás**.

   > [!NOTE]
   > A mintaalkalmazás elkülönített tároló és `System.IO.IsolatedStorage`, az előfizetési kulcs és a végpont URL-CÍMÉT tárolja.

1. A **alkalmazási helyzet kiválasztása** a navigációs ablaktáblán válassza ki egy jelenleg is a mintaalkalmazás forgatókönyv:  

   | Forgatókönyv | Leírás |
   |----------|-------------|
   |Kép elemzése | Használja a [kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) művelet egy helyi vagy távoli rendszerképet elemzéséhez. Válassza ki a vizuális jellemzőinek és az elemzés nyelvét, és tekintse meg a lemezkép és az eredményeket is.  |
   |Kép tartományi modell elemzése | Használja a [lista tartományspecifikus modellek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) művelet, amelyből kiválaszthatja, a tartomány-modellek listáját és a [ismeri fel a tartomány adott tartalom](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) művelet egy helyi vagy távoli rendszerkép használatával elemezheti a a kijelölt tartomány modell. Az elemzés nyelvét is beállíthatja. |
   |Kép leírása | Használja a [ismertetik kép](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) művelet meghívásával létrehozott egy helyi vagy távoli rendszerképet emberek számára olvasható leírása. A leírás nyelvét is beállíthatja. |
   |Címkék létrehozása | Használja a [címke lemezkép](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) címkézése a helyi vagy távoli képek vizuális jellemzőinek műveletet. A címkék nyelvét is beállíthatja. |
   |Ismeri fel a szöveget (optikai Karakterfelismerés) | Használja a [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) felismerése és kinyerése művelet nyomtatott szöveg egy rendszerképből. Válassza ki a használni kívánt nyelvet, vagy lehetővé teszik a Computer Vision automatikusan észlelje a nyelvet. |
   |Ismeri fel a szöveg V2 (angol nyelven) | Használja a [szöveg felismerése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) és [első ismeri fel a szöveg művelet eredményének](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) műveletek aszinkron módon ismeri fel, és nyomtatott vagy kézzel írt szöveg kinyerése egy képet. |
   |Miniatűr beolvasása | Használja a [miniatűr beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) művelet végrehajtásával létrehozott egy helyi vagy távoli kép miniatűrjét. |

   Az alábbi képernyőfelvételen látható az oldal, a kép elemzése a forgatókönyvben egy képet elemzése után megadott mutatja be.
   ![Az elemzés kép oldalát bemutató képernyőkép](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Ismerje meg a mintaalkalmazás

A Visual Studio-megoldás esetében a Computer Vision mintaalkalmazás két projektet tartalmaz:

* SampleUserControlLibrary  
  A SampleUserControlLibrary projekt közösen használnak a Cognitive Services-minták több funkciót biztosít. A projekt tartalmazza a következőket:
  * SampleScenarios  
    Szabványos bemutató, például a címsor, a navigációs ablaktábla és a tartalmú ablaktáblában minták biztosító UserControl. A Computer Vision mintaalkalmazás ezt a vezérlőt a MainWindow.xaml ablakban forgatókönyv oldalak megjelenítése és forgatókönyvek, például az előfizetési kulcs és a végpont URL-cím között megosztott információk eléréséhez.
  * SubscriptionKeyPage  
    Egy oldal, amely egy előfizetési kulcs és a végpont URL-címet adjon meg a mintaalkalmazást a szabványos elrendezés biztosít. A Computer Vision mintaalkalmazás ezen a lapon kezelheti az előfizetési kulcsot, és a forgatókönyv-lapok által használt végpont URL-címe.
  * VideoResultControl  
    Szabványos bemutató videó információkat biztosító UserControl. A Computer Vision mintaalkalmazás nem használja ezt a vezérlőt.
* VisionAPI-WPF-Samples  
  A fő projektet a Computer Vision mintaalkalmazást, ez a projekt tartalmazza az érdekes funkciók mindegyikét a Computer Vision. A projekt tartalmazza a következőket:
  * AnalyzeInDomainPage.xaml  
    A forgatókönyv lapon tartományi modell forgatókönyvvel elemzése lemezképhez.
  * AnalyzeImage.xaml  
    A forgatókönyv lapon, a kép elemzése forgatókönyvhöz.
  * DescribePage.xaml  
    A forgatókönyv lapon, a lemezkép leírása a forgatókönyvhöz.
  * ImageScenarioPage.cs  
    ImageScenarioPage osztály, amely összes forgatókönyv oldalt a mintaalkalmazás származnak. Ez az osztály kezeli a funkciókat, például a hitelesítő adatokkal, és a kimenet, az összes forgatókönyv oldalak megosztott formázása.
  * MainWindow.xaml  
    A mintaalkalmazás fő ablakában, használja a SampleScenarios vezérlő a SubscriptionKeyPage és a forgatókönyv lapok.
  * OCRPage.xaml  
    A forgatókönyv lapon, a szöveg felismerése (OCR) forgatókönyvhöz.
  * RecognizeLanguage.cs  
    A RecognizeLanguage osztály, amely ismerteti a különböző módszerek a mintaalkalmazás által támogatott nyelveket.
  * TagsPage.xaml  
    A forgatókönyv lapon, a Címkék készítése a forgatókönyvhöz.
  * TextRecognitionPage.xaml  
    A forgatókönyv lapon ismeri fel a szöveg V2 (angol nyelvű) forgatókönyvhöz.
  * ThumbnailPage.xaml  
    A forgatókönyv lapon, a miniatűr beolvasása a forgatókönyvhöz.

### <a name="explore-the-sample-code"></a>Áttekintjük a mintakódot

Kód fő részeit is Keretes Megjegyzés blokkokkal kezdődő `KEY SAMPLE CODE STARTS HERE` , és `KEY SAMPLE CODE ENDS HERE`, hogy egyszerűbb legyen a mintaalkalmazást megismerheti. Ezek mintakód legfontosabb részeit a legfontosabb, hogy különféle műveleteket végezhetnek a Computer Vision API ügyféloldali kódtár kód tartalmaznak. Kereshet `KEY SAMPLE CODE STARTS HERE` között a leginkább releváns szakaszait a Computer Vision mintaalkalmazás kódját a Visual studióban. 

Ha például a `UploadAndAnalyzeImageAsync` metódus, jelenik meg a következő, és szerepel a AnalyzePage.xaml, azt ismerteti, hogyan elemezheti a helyi rendszerképet meghívásával az ügyféloldali kódtár használatával a `ComputerVisionClient.AnalyzeImageInStreamAsync` metódust.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Ismerje meg az ügyféloldali kódtár

Ez a mintaalkalmazás használ a Computer Vision API ügyféloldali kódtár, egy vékony C# ügyfél burkoló a Computer Vision API az Azure Cognitive Servicesben. Az ügyféloldali kódtár érhető el a NuGet a [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) csomagot. A Visual Studio alkalmazást a létrehozása során az ügyféloldali kódtár lekért a megfelelő NuGet-csomagot. Az az ügyféloldali kódtár forráskódját is megtekintheti a `/ClientLibrary` mappában található a `Microsoft/Cognitive-Vision-Windows` tárház.

Az ügyféloldali kódtár funkció központok körül a `ComputerVisionClient` osztályhoz, a a `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` névtér, miközben a modell által használt a `ComputerVisionClient` osztály keresztkiemelést számítógépes Látástechnológiai is találhatók a `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` névtér. A különböző XAML forgatókönyv lapokon mellékelt a mintaalkalmazást, megtalálhatja a következő `using` azokat a névtereket irányelveket:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

A mellékelt különböző módszerekkel kapcsolatos további a `ComputerVisionClient` osztály a Computer Vision minta alkalmazással az eseteket is.

## <a name="explore-the-analyze-image-scenario"></a>Ismerje meg a kép elemzése forgatókönyv

Ebben a forgatókönyvben a AnalyzePage.xaml lap kezeli. Válassza ki a vizuális jellemzőinek és az elemzés nyelvét, és tekintse meg a lemezkép és az eredményeket is. A forgatókönyv lapon hajtja végre ezt a kép forrása attól függően az alábbi módszerek egyikével:

* UploadAndAnalyzeImageAsync  
  Ezt a módszert használják a helyi képek esetén, amelyben a rendszerkép módon kell kódolni egy `Stream` és a Computer Vision meghívásával küldi el a `ComputerVisionClient.AnalyzeImageInStreamAsync` metódus.
* AnalyzeUrlAsync  
  Ezt a módszert használják a távoli képek esetén, amelyben a kép URL-CÍMÉT a Computer Vision által küldött hívás a `ComputerVisionClient.AnalyzeImageAsync` metódust.

A `UploadAndAnalyzeImageAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. A mintaalkalmazást a helyi rendszerképet elemzi, mert rendelkezik küldendő Computer Vision, kép tartalmát. Ekkor megnyílik a megadott helyi fájl `imageFilePath` olvasásra, egy `Stream`, majd beolvassa a vizuális jellemzőinek és a forgatókönyv lapon kiválasztott nyelvvel. Meghívja a `ComputerVisionClient.AnalyzeImageInStreamAsync` módszer, átadja a `Stream` a fájlt, a vizuális jellemzőinek és a nyelvet, majd visszaadja az eredményt egy `ImageAnalysis` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

A `AnalyzeUrlAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. Lekéri a vizuális jellemzőinek és a forgatókönyv lapon kiválasztott nyelvvel. Meghívja a `ComputerVisionClient.AnalyzeImageInStreamAsync` módot, a kép URL-címe, a vizuális jellemzőinek és a nyelvet, majd visszaadja az eredményt egy `ImageAnalysis` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Fedezze fel a tartományi modellben forgatókönyvvel elemzése kép

Ebben a forgatókönyvben a AnalyzeInDomainPage.xaml lap kezeli. Kiválaszthatja például olyan tartományi modell `celebrities` vagy `landmarks`, és a nyelvet és a lemezkép egy tartomány-specifikus elemzését, és tekintse meg a lemezkép és az eredményeket is. A forgatókönyv lapon használja az alábbi módszerek attól függően, a kép forrását:

* GetAvailableDomainModelsAsync  
  Ez a módszer elérhető tartománymodellek listájának beolvasása a Computer Vision, és feltölti a `_domainModelComboBox` kombinált lista vezérlőelem a lapon használatával a `ComputerVisionClient.ListModelsAsync` metódust.
* UploadAndAnalyzeInDomainImageAsync  
  Ezt a módszert használják a helyi képek esetén, amelyben a rendszerkép módon kell kódolni egy `Stream` és a Computer Vision meghívásával küldi el a `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metódus.
* AnalyzeInDomainUrlAsync  
  Ezt a módszert használják a távoli képek esetén, amelyben a kép URL-CÍMÉT a Computer Vision által küldött hívás a `ComputerVisionClient.AnalyzeImageByDomainAsync` metódust.

A `UploadAndAnalyzeInDomainImageAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. A mintaalkalmazást a helyi rendszerképet elemzi, mert rendelkezik küldendő Computer Vision, kép tartalmát. Ekkor megnyílik a megadott helyi fájl `imageFilePath` olvasásra, egy `Stream`, majd beolvassa a nyelv, a forgatókönyv lapon kiválasztott. Meghívja a `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` módszer, átadja a `Stream` a fájl nevét a tartományi modellben, és a nyelvet, majd visszaadja az eredményt egy `DomainModelResults` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

A `AnalyzeInDomainUrlAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. A nyelv, a forgatókönyv lapon kiválasztott kap. Meghívja a `ComputerVisionClient.AnalyzeImageByDomainAsync` módot, a kép URL-címe, a vizuális jellemzőinek és a nyelvet, majd visszaadja az eredményt egy `DomainModelResults` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

## <a name="explore-the-describe-image-scenario"></a>Ismerje meg a lemezkép leírása forgatókönyv

Ebben a forgatókönyvben a DescribePage.xaml lap kezeli. Válasszon egy nyelvet létrehozni a lemezkép emberek számára olvasható leírását, és tekintse meg a lemezkép és az eredményeket is. A forgatókönyv lapon használja az alábbi módszerek attól függően, a kép forrását:

* UploadAndDescribeImageAsync  
  Ezt a módszert használják a helyi képek esetén, amelyben a rendszerkép módon kell kódolni egy `Stream` és a Computer Vision meghívásával küldi el a `ComputerVisionClient.DescribeImageInStreamAsync` metódus.
* DescribeUrlAsync  
  Ezt a módszert használják a távoli képek esetén, amelyben a kép URL-CÍMÉT a Computer Vision által küldött hívás a `ComputerVisionClient.DescribeImageAsync` metódust.

A `UploadAndDescribeImageAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. A mintaalkalmazást a helyi rendszerképet elemzi, mert rendelkezik küldendő Computer Vision, kép tartalmát. Ekkor megnyílik a megadott helyi fájl `imageFilePath` olvasásra, egy `Stream`, majd beolvassa a nyelv, a forgatókönyv lapon kiválasztott. Meghívja a `ComputerVisionClient.DescribeImageInStreamAsync` módszer, átadja a `Stream` a fájl maximális száma (az ebben az esetben, 3) a deduplikációra, és a nyelvet, majd visszaadja az eredményt egy `ImageDescription` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

A `DescribeUrlAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. A nyelv, a forgatókönyv lapon kiválasztott kap. Meghívja a `ComputerVisionClient.DescribeImageAsync` módszer, átadja a kép URL-címe, a maximális száma (az ebben az esetben, 3) a deduplikációra, és a nyelvet, majd visszaadja az eredményt egy `ImageDescription` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

## <a name="explore-the-generate-tags-scenario"></a>Ismerje meg a címkék létrehozása forgatókönyv

Ebben a forgatókönyvben a TagsPage.xaml lap kezeli. Válasszon egy nyelvet a képek vizuális jellemzőinek címkét, és tekintse meg a lemezkép és az eredményeket is. A forgatókönyv lapon használja az alábbi módszerek attól függően, a kép forrását:

* UploadAndGetTagsForImageAsync  
  Ezt a módszert használják a helyi képek esetén, amelyben a rendszerkép módon kell kódolni egy `Stream` és a Computer Vision meghívásával küldi el a `ComputerVisionClient.TagImageInStreamAsync` metódus.
* GenerateTagsForUrlAsync  
  Ezt a módszert használják a távoli képek esetén, amelyben a kép URL-CÍMÉT a Computer Vision által küldött hívás a `ComputerVisionClient.TagImageAsync` metódust.

A `UploadAndGetTagsForImageAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. A mintaalkalmazást a helyi rendszerképet elemzi, mert rendelkezik küldendő Computer Vision, kép tartalmát. Ekkor megnyílik a megadott helyi fájl `imageFilePath` olvasásra, egy `Stream`, majd beolvassa a nyelv, a forgatókönyv lapon kiválasztott. Meghívja a `ComputerVisionClient.TagImageInStreamAsync` módszer, átadja a `Stream` a fájl-és a nyelvet, majd visszaadja az eredményt egy `TagResult` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

A `GenerateTagsForUrlAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. A nyelv, a forgatókönyv lapon kiválasztott kap. Meghívja a `ComputerVisionClient.TagImageAsync` módot, a kép URL-címe és a nyelvet, majd visszaadja az eredményt egy `TagResult` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Ismerje meg a szöveg felismerése (OCR) forgatókönyv

Ebben a forgatókönyvben a OCRPage.xaml lap kezeli. Válasszon egy nyelvet, felismerni és nyomtatott szöveg kinyerése egy képet, és tekintse meg a lemezkép és az eredményeket is. A forgatókönyv lapon használja az alábbi módszerek attól függően, a kép forrását:

* UploadAndRecognizeImageAsync  
  Ezt a módszert használják a helyi képek esetén, amelyben a rendszerkép módon kell kódolni egy `Stream` és a Computer Vision meghívásával küldi el a `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metódus.
* RecognizeUrlAsync  
  Ezt a módszert használják a távoli képek esetén, amelyben a kép URL-CÍMÉT a Computer Vision által küldött hívás a `ComputerVisionClient.RecognizePrintedTextAsync` metódust.

A `UploadAndRecognizeImageAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. A mintaalkalmazást a helyi rendszerképet elemzi, mert rendelkezik küldendő Computer Vision, kép tartalmát. Ekkor megnyílik a megadott helyi fájl `imageFilePath` olvasásra, egy `Stream`, majd beolvassa a nyelv, a forgatókönyv lapon kiválasztott. Meghívja a `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metódus, jelezve, hogy a rendszer nem ismeri fel tájolás, és átadja a `Stream` a fájl-és a nyelvet, majd visszaadja az eredményt egy `OcrResult` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

A `RecognizeUrlAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. A nyelv, a forgatókönyv lapon kiválasztott kap. Meghívja a `ComputerVisionClient.RecognizePrintedTextAsync` módszer, jelezve, hogy a rendszer nem ismeri fel tájolást, és átadja a kép URL-címe és a nyelvet, majd visszaadja az eredményt egy `OcrResult` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Ismerje meg a szöveg V2 ismeri fel (angol nyelvű) forgatókönyv

Ebben a forgatókönyvben a TextRecognitionPage.xaml lap kezeli. Válassza ki a mód és a egy nyelv aszinkron módon felismerni és nyomtatott, vagy kézzel írt szöveg kinyerése egy képet, és tekintse meg a lemezkép és az eredményeket is. A forgatókönyv lapon használja az alábbi módszerek attól függően, a kép forrását:

* UploadAndRecognizeImageAsync  
  Ezt a módszert használják a helyi képek esetén, amelyben a rendszerkép módon kell kódolni egy `Stream` és küldi el a Computer Vision, hogy meghívásával a `RecognizeAsync` módszer és a egy paraméteres delegált átadása esetén a `ComputerVisionClient.RecognizeTextInStreamAsync` metódus.
* RecognizeUrlAsync  
  Ezt a módszert használják a távoli képek esetén, amelyben a kép URL-CÍMÉT a Computer Vision által küldött hívás a `RecognizeAsync` módszer és a egy paraméteres delegált átadása esetén az `ComputerVisionClient.RecognizeTextAsync` metódust.
* Ez a módszer RecognizeAsync kezeli az aszinkron meghívása nélkül is a `UploadAndRecognizeImageAsync` és `RecognizeUrlAsync` módszereket, valamint a lekérdezési eredmények meghívásával a `ComputerVisionClient.GetTextOperationResultAsync` metódus.

A Computer Vision mintaalkalmazás szerepel az egyéb forgatókönyvek, ellentétben ebben a forgatókönyvben aszinkron, hogy egy módszert hívja meg a folyamat elindításához, de egy másik módszert hívja meg állapotának ellenőrzéséhez és a folyamat eredményét adja vissza. Ebben a forgatókönyvben a logikai folyamat némileg eltér a más esetekben.

A `UploadAndRecognizeImageAsync` metódus megnyitja a helyi fájl a megadott `imageFilePath` olvasásra, egy `Stream`, ekkor meghívja a `RecognizeAsync` módszer, átmenő:

* A paraméteres aszinkron meghatalmazott lambda kifejezésnek a `ComputerVisionClient.RecognizeTextInStreamAsync` módot, az a `Stream` a fájl-és a paraméterek, mód a `GetHeadersAsyncFunc`.
* Az első meghatalmazott lambda kifejezésnek a `Operation-Location` válaszfejléc érték, a `GetOperationUrlFunc`.

A `RecognizeUrlAsync` metódust hívja a `RecognizeAsync` passing metódus:

* A paraméteres aszinkron meghatalmazott lambda kifejezésnek a `ComputerVisionClient.RecognizeTextAsync` metódus az URL-címét a távoli lemezkép és a paraméterek, mód a `GetHeadersAsyncFunc`.
* Az első meghatalmazott lambda kifejezésnek a `Operation-Location` válaszfejléc érték, a `GetOperationUrlFunc`.

Ha a `RecognizeAsync` metódus befejeződött, mindkettő `UploadAndRecognizeImageAsync` és `RecognizeUrlAsync` módszerek az eredményt adja vissza egy `TextOperationResult` példány. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

A `RecognizeAsync` metódus meghívja a paraméteres delegált bármelyik a `ComputerVisionClient.RecognizeTextInStreamAsync` vagy `ComputerVisionClient.RecognizeTextAsync` metódus az átadott `GetHeadersAsyncFunc` , és megvárja, amíg a választ. A metódus ekkor meghívja a delegált átadott `GetOperationUrlFunc` beolvasni a `Operation-Location` válasz fejléc értéke a válaszban szereplő. Kérje le az eredményeket a metódus az átadott használt URL-cím értéke `GetHeadersAsyncFunc` a Computer Vision.

A `RecognizeAsync` metódus ekkor meghívja a `ComputerVisionClient.GetTextOperationResultAsync` módot, az URL-cím lekért átadása a `Operation-Location` válaszfejléc, az állapota és eredménye a metódus az átadott `GetHeadersAsyncFunc`. Ha az állapot nem jelzi, hogy a metódus befejeződött, sikeresen vagy sikertelenül, a `RecognizeAsync` metódust hívja `ComputerVisionClient.GetTextOperationResultAsync` 3 többször, közötti hívások 3 másodpercet vár. A `RecognizeAsync` módszer, amelynek a neve, a metódus az eredményeket adja vissza.

## <a name="explore-the-get-thumbnail-scenario"></a>Ismerje meg a miniatűr első forgatókönyv

Ebben a forgatókönyvben a ThumbnailPage.xaml lap kezeli. Azt jelzik, hogy intelligens vágása használja-e, és adja meg a kívánt magasságát és szélességét, miniatűrkép generálása-lemezképről, és tekintse meg a lemezkép és az eredményeket is. A forgatókönyv lapon használja az alábbi módszerek attól függően, a kép forrását:

* UploadAndThumbnailImageAsync  
  Ezt a módszert használják a helyi képek esetén, amelyben a rendszerkép módon kell kódolni egy `Stream` és a Computer Vision meghívásával küldi el a `ComputerVisionClient.GenerateThumbnailInStreamAsync` metódus.
* ThumbnailUrlAsync  
  Ezt a módszert használják a távoli képek esetén, amelyben a kép URL-CÍMÉT a Computer Vision által küldött hívás a `ComputerVisionClient.GenerateThumbnailAsync` metódust.

A `UploadAndThumbnailImageAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. A mintaalkalmazást a helyi rendszerképet elemzi, mert rendelkezik küldendő Computer Vision, kép tartalmát. Ekkor megnyílik a megadott helyi fájl `imageFilePath` olvasásra, mint egy `Stream`. Meghívja a `ComputerVisionClient.GenerateThumbnailInStreamAsync` módot, a szélesség, hosszúság, átadja a `Stream` a fájlok és e használni az intelligens levágás, majd visszaadja az eredményt egy `Stream`. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

A `RecognizeUrlAsync` módszer létrehoz egy új `ComputerVisionClient` példányt, a megadott előfizetés kulcsának és végpontjának URL-cím használatával. Meghívja a `ComputerVisionClient.GenerateThumbnailAsync` metódus, átadja a szélességét, magasságát, a kép URL-CÍMÉT és e használni az intelligens levágás, majd adja vissza az eredményt egy `Stream`. A módszerek örökli a `ImageScenarioPage` osztály szerepelnek a forgatókönyv lapon a kapott találatok közül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a mappát, amelybe klónozta az `Microsoft/Cognitive-Vision-Windows` tárház. Ha úgy döntött, a minta-rendszerképek használatához, is törli a mappát, amelybe klónozta az `Microsoft/Cognitive-Face-Windows` tárház.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Face API használatának első lépései](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
