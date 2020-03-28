---
title: 'Minta: Képfeldolgozó alkalmazás felfedezése C-ben #'
titleSuffix: Azure Cognitive Services
description: Fedezzen fel egy alapvető Windows-alkalmazást, amely az Azure Cognitive Services Computer Vision API-ját használja. OCR végrehajtása, miniatűrök létrehozása és képek vizuális jellemzőinek használata.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b492d8e3bdcf6d9a41df3eb79ef159985cc715cf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168884"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Minta: Képfeldolgozó alkalmazás felfedezése C-vel #

Fedezze fel az alapvető Windows-alkalmazást, amely a Computer Vision segítségével optikai karakterfelismerést (OCR) hajt végre, intelligens körülvágott miniatűröket hoz létre, valamint észleli, kategorizálja, címkézi és írja le a kép vizuális jellemzőit, beleértve az arcokat is. Az alábbi példában elküldheti egy kép URL-címét vagy egy helyileg tárolt fájlt. Ezt a nyílt forráskódú példát sablonként használhatja saját Windows-alkalmazásának létrehozásához a Computer Vision API és a Windows Presentation Foundation (WPF), amely a .

> [!div class="checklist"]
> * A mintaalkalmazás beszerezése a GitHubról
> * A mintaalkalmazás megnyitása és létrehozása a Visual Studióban
> * A mintaalkalmazás futtatása és a vele való interakció különböző forgatókönyvek végrehajtásához
> * A mintaalkalmazásban található különböző forgatókönyvek megismerése

## <a name="prerequisites"></a>Előfeltételek

A mintaalkalmazás feltárása előtt győződjön meg arról, hogy teljesítette az alábbi előfeltételeket:

* A [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) vagy újabb verzióval kell rendelkeznie.
* Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Ingyenes próbakulcsot a [Cognitive Services kipróbálásával](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)szerezheti be. Vagy kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) című részben található utasításokat, hogy előiratkozzon a Computer Vision szolgáltatásra, és bekésezse a kulcsot. Vegye figyelembe a szolgáltatás végpontjának URL-címét is.

## <a name="get-the-sample-app"></a>Mintaalkalmazás letöltése

A Computer Vision mintaalkalmazás érhető el `Microsoft/Cognitive-Vision-Windows` a GitHubon a tárházból. Ez a tárház `Microsoft/Cognitive-Common-Windows` git-almodulként is tartalmazza a tárházat. Ezt a tárházat rekurzívmódon klónozhatja, beleértve az `git clone --recurse-submodules` almodult is, akár a parancssorból származó paranccsal, akár a GitHub Desktop használatával.

Ha például rekurzívmódon klónozza a Computer Vision mintaalkalmazás tárházát egy parancssorból, futtassa a következő parancsot:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Ne töltse le ezt a tárházat ZIP-ként. A Git nem tartalmaz almodulokat, amikor zip-tárolóként tölt le egy tárházat.

### <a name="get-optional-sample-images"></a>Opcionális mintaképek beszereznie

Igény szerint használhatja a minta képeket a [Face](../../Face/Overview.md) minta `Microsoft/Cognitive-Face-Windows` alkalmazás, elérhető a GitHubon a tárházban. Ez a mintaalkalmazás `/Data`tartalmaz egy mappát, amely több képet tartalmaz személyekről. Ezt a tárházat a Computer Vision mintaalkalmazáshoz ismertetett módszerekkel is rekurzív módon klónozhatja.

Ha például rekurzívmódon klónozza a Face mintaalkalmazás tárházát egy parancssorból, futtassa a következő parancsot:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>A mintaalkalmazás megnyitása és létrehozása a Visual Studióban

Először létre kell építenie a mintaalkalmazást, hogy a Visual Studio fel tudja oldani a függőségeket, mielőtt futtatná vagy felfedezheti volna a mintaalkalmazást. A mintaalkalmazás megnyitásához és létrehozásához tegye a következő lépéseket:

1. Nyissa meg a Visual `/Sample-WPF/VisionAPI-WPF-Samples.sln`Studio megoldásfájlját, a , a Visual Studióban.
1. Győződjön meg arról, hogy a Visual Studio-megoldás két projektet tartalmaz:  

   * SampleUserControlLibrary könyvtár
   * VisionAPI-WPF-minták  

   Ha a SampleUserControlLibrary projekt nem érhető el, ellenőrizze, hogy rekurzívmódon klónozta-e a `Microsoft/Cognitive-Vision-Windows` tárházat.
1. A Visual Studio alkalmazásban nyomja le a Ctrl+Shift+B billentyűkombinációt, vagy válassza a menüszalag **menübuild parancsát,** majd válassza a **Megoldás összeállítása** parancsot a megoldás létrehozásához.

## <a name="run-and-interact-with-the-sample-app"></a>A mintaalkalmazás futtatása és a vele való interakció

Futtathatja a mintaalkalmazást, és megtekintheti, hogyan lép kapcsolatba Önnel és a Computer Vision ügyfélkönyvtárral különböző feladatok végrehajtásakor, például miniatűrök létrehozása vagy képek címkézése során. A mintaalkalmazás futtatásához és kezeléséhez tegye a következő lépéseket:

1. A létrehozás befejezése után nyomja le az **F5 billentyűt,** vagy válassza a **menüszalag menühiba** parancsát, majd válassza a **Hibakeresés indítása parancsot** a mintaalkalmazás futtatásához.
1. Amikor megjelenik a mintaalkalmazás, válassza az **Előfizetési kulcs kezelése** lehetőséget a navigációs ablakban az Előfizetési kulcs kezelése lap megjelenítéséhez.
   ![Előfizetési kulcs kezelése lap](../Images/Vision_UI_Subscription.PNG)  
1. Adja meg az előfizetési kulcsot az **Előfizetési kulcs ban.**
1. Írja be a végpont URL-címét a **Végpont**mezőbe.  
   Ha például a Computer Vision ingyenes próbaverzió előfizetési kulcsát használja, adja meg a következő végpont URL-címét:`https://westcentralus.api.cognitive.microsoft.com`  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Ha a mintaalkalmazás következő futtatásakor nem szeretné megadni az előfizetési kulcsot és a végpont URL-címét, válassza a **Beállítás mentése** lehetőséget az előfizetési kulcs és a végpont URL-címének a számítógépre való mentéséhez. Ha törölni szeretné a korábban mentett előfizetési kulcsot és végpont URL-címét, válassza **a Beállítás törlése lehetőséget.**

   > [!NOTE]
   > A mintaalkalmazás elkülönített `System.IO.IsolatedStorage`tárolót használ, és az előfizetési kulcsot és a végpont URL-címét tárolja.

1. A **Forgatókönyv kiválasztása** a navigációs ablakban csoportban válassza ki a mintaalkalmazásban jelenleg szereplő forgatókönyvek egyikét:  

   | Forgatókönyv | Leírás |
   |----------|-------------|
   |Kép elemzése | A [Kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) művelettel elemez het a helyi vagy távoli lemezképeket. Kiválaszthatja az elemzés vizuális jellemzőit és nyelvét, és megtekintheti a képet és az eredményeket is.  |
   |Lemezkép elemzése tartományi modellel | A [Tartományspecifikus modellek listázása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) művelettel sorolja fel azokat a tartománymodelleket, amelyekből kijelölheti a lehetőséget, valamint a [Tartományspecifikus tartalom felismerése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) művelettel helyi vagy távoli lemezképet elemezhet a kiválasztott tartománymodell használatával. Kiválaszthatja az elemzés nyelvét is. |
   |Kép leírása | A [Kép leírása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) művelettel helyi vagy távoli lemezkép ember által olvasható leírását hozza létre. Kiválaszthatja a leírás nyelvét is. |
   |Címkék létrehozása | A [Címkekép](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) művelettel címkézi meg a helyi vagy távoli kép vizuális jellemzőit. Kiválaszthatja a címkékhez használt nyelvet is. |
   |Szöveg felismerése (OCR) | Az [OCR-művelettel](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) felismeri és kinyeri a nyomtatott szöveget a képből. Kiválaszthatja a használni kívánt nyelvet, vagy beállíthatja, hogy a Computer Vision automatikusan észlelje a nyelvet. |
   |Szöveg felismerése V2 (magyar) | A [Szöveg felismerése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) és a [Szöveg felismerése művelet eredményének felismerése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) művelettel aszinkron módon felismeri és kinyeri a nyomtatott vagy kézzel írt szöveget a képből. |
   |Miniatűr beszerezni | A [Miniatűrök beszerezni](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) műveletet használja egy helyi vagy távoli kép miniatűrjének létrehozásához. |

   A következő képernyőkép a mintakép elemzése után a Kép elemzése forgatókönyvhez megadott oldalt mutatja be.
   ![Képernyőkép a Kép elemzése lapról](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>A mintaalkalmazás felfedezése

A Computer Vision mintaalkalmazás Visual Studio-megoldása két projektet tartalmaz:

* SampleUserControlLibrary könyvtár  
  A SampleUserControlLibrary projekt több Cognitive Services-minta által megosztott funkciókat biztosít. A projekt a következőket tartalmazza:
  * Mintaforgatókönyvek  
    Szabványos bemutatót, például címsort, navigációs ablaktáblát és tartalompanelt tartalmazó UserControl a mintákhoz. A Computer Vision mintaalkalmazás ezt a vezérlőt használja a MainWindow.xaml ablakban forgatókönyvoldalak megjelenítéséhez és a forgatókönyvek, például az előfizetési kulcs és a végpont URL-címe között megosztott információk eléréséhez.
  * SubscriptionKeyPage  
    Olyan oldal, amely szabványosított elrendezést biztosít a mintaalkalmazás előfizetési kulcsának és végponti URL-címének megadásához. A Computer Vision mintaalkalmazás ezen a lapon kezeli a forgatókönyvoldalak által használt előfizetési kulcsot és végpont-URL-címet.
  * VideoResultControl  
    UserControl, amely szabványos bemutatót biztosít a videoinformációkhoz. A Computer Vision mintaalkalmazás nem használja ezt a vezérlőt.
* VisionAPI-WPF-minták  
  A fő projekt a Computer Vision minta app, ez a projekt tartalmazza az összes érdekes funkciót Computer Vision. A projekt a következőket tartalmazza:
  * AnalyzeInDomainPage.xaml  
    A lemezkép elemzése a tartománymodellel forgatókönyv forgatókönyvlapja.
  * AnalyzeImage.xaml  
    A kép elemzése forgatókönyv forgatókönyv forgatókönyv.
  * LeírOldal.xaml  
    A Kép leírása forgatókönyv forgatókönyvének forgatókönyve.
  * ImageScenarioPage.cs  
    Az ImageScenarioPage osztály, amelyből a mintaalkalmazás összes forgatókönyvlapja származik. Ez az osztály kezeli az összes forgatókönyvlap által megosztott funkciókat, például a hitelesítő adatok és a formázási kimenet biztosítását.
  * MainWindow.xaml  
    A mintaalkalmazás fő ablaka a SampleScenarios vezérlőt használja a SubscriptionKeyPage és a forgatókönyvlapok bemutatásához.
  * OCRPage.xaml  
    A szöveg felismerése (OCR) forgatókönyv lap.
  * RecognizeLanguage.cs  
    A RecognizeLanguage osztály, amely a mintaalkalmazás különböző metódusai által támogatott nyelvekről nyújt tájékoztatást.
  * CímkékPage.xaml  
    A Címkék létrehozása forgatókönyv lap.
  * TextRecognitionPage.xaml  
    A Szöveg felismerése V2 (angol) forgatókönyv forgatókönyv lapja.
  * ThumbnailPage.xaml  
    A Miniatűrök bekerülése forgatókönyvlap.

### <a name="explore-the-sample-code"></a>A mintakód felfedezése

A mintakód legfontosabb részei a megjegyzésblokkokkal vannak keretezve, amelyek a - kezdetű `KEY SAMPLE CODE STARTS HERE` és végződésűek, `KEY SAMPLE CODE ENDS HERE`hogy megkönnyítsék a mintaalkalmazás felfedezését. Ezek a kulcsfontosságú részei mintakód tartalmazza a kódot leginkább releváns a tanulás, hogyan kell használni a Computer Vision API ügyfélkódtár különböző feladatok elvégzéséhez. A Visual `KEY SAMPLE CODE STARTS HERE` Studio alkalmazásban kereshet a Legrelevánsabb kódszakaszok közötti váltáshoz a Computer Vision mintaalkalmazásban. 

Az AnalyzePage.xaml fájlban látható és a programban található `UploadAndAnalyzeImageAsync` metódus például bemutatja, hogyan lehet az `ComputerVisionClient.AnalyzeImageInStreamAsync` ügyfélkódtár segítségével elemezni egy helyi lemezképet a metódus meghívásával.

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

### <a name="explore-the-client-library"></a>Az ügyfélkönyvtár felfedezése

Ez a mintaalkalmazás a Computer Vision API-ügyfélkódtárat használja, amely egy vékony C# ügyfélburkoló az Azure Cognitive Services Computer Vision API-jához. Az ügyfélkódtár a NuGet webhelyen érhető el a [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) csomagban. A Visual Studio-alkalmazás elkészítésekén lekérte az ügyfélkönyvtárat a megfelelő NuGet-csomagból. Az ügyféltár forráskódját a `/ClientLibrary` `Microsoft/Cognitive-Vision-Windows` tár mappájában is megtekintheti.

`ComputerVisionClient` Az ügyféltár funkciói az osztály körül összpontosulnak a `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` névtérben, míg az osztály által a `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` `ComputerVisionClient` Computer Vision programmal való interakció során használt modellek a névtérben találhatók. A mintaalkalmazásban található különböző XAML-forgatókönyvlapokon a `using` következő irányelvek találhatók a névterekhez:

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

A Computer Vision mintaalkalmazásban szereplő `ComputerVisionClient` forgatókönyvek megismerése során többet is megtudhat az osztályban található különböző módszerekről.

## <a name="explore-the-analyze-image-scenario"></a>A Kép elemzése forgatókönyv felfedezése

Ezt a forgatókönyvet az AnalyzePage.xaml lap kezeli. Kiválaszthatja az elemzés vizuális jellemzőit és nyelvét, és megtekintheti a képet és az eredményeket is. A forgatókönyv lap ezt a következő módszerek egyikével teszi, a lemezkép forrásától függően:

* UploadAndAnalyzeImageAsync  
  Ez a módszer a helyi képekhez használatos, amelyekben `Stream` a lemezképet a rendszerként kell kódolni, és a metódus hívásával el kell küldeni a `ComputerVisionClient.AnalyzeImageInStreamAsync` Computer Vision rendszernek.
* AnalyzeUrlAsync  
  Ez a módszer a távoli képek, amelyben az URL-címet a `ComputerVisionClient.AnalyzeImageAsync` rendszer a kép a Computer Vision hívja meg a metódust.

A `UploadAndAnalyzeImageAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. Mivel a mintaalkalmazás egy helyi lemezképet elemez, el kell küldenie a kép tartalmát a Computer Vision rendszernek. Megnyitja a helyi fájl `imageFilePath` olvasásához, `Stream`mint a , majd megkapja a vizuális funkciók és a nyelv kiválasztott a forgatókönyv oldalon. Meghívja `ComputerVisionClient.AnalyzeImageInStreamAsync` a metódust, átadva a `Stream` fájlt, a vizuális funkciókat és `ImageAnalysis` a nyelvet, majd az eredményt példányként adja vissza. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

A `AnalyzeUrlAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. A forgatókönyv oldalon kiválasztott vizuális funkciókat és nyelvet kapja. Meghívja `ComputerVisionClient.AnalyzeImageInStreamAsync` a metódust, átadja a kép URL-címét, a vizuális `ImageAnalysis` funkciókat és a nyelvet, majd az eredményt példányként adja vissza. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>A Lemezkép elemzése a tartománymodellel forgatókönyv

Ezt a forgatókönyvet az AnalyzeInDomainPage.xaml lap kezeli. A lemezkép tartományspecifikus elemzéséhez és a kép megtekintéséhez választhat tartománymodellt, például `celebrities` a `landmarks`, és a nyelvet. A forgatókönyvlap a következő módszereket használja a kép forrásától függően:

* GetAvailableDomainModelsAsync  
  Ez a módszer leveszi a rendelkezésre álló tartománymodellek `_domainModelComboBox` listáját a Computer Vision-ből, és feltölti a ComboBox vezérlőt az oldalon, a `ComputerVisionClient.ListModelsAsync` módszer rel.
* UploadAndAnalyzeInDomainImageAsync  
  Ez a módszer a helyi képekhez használatos, amelyekben `Stream` a lemezképet a rendszerként kell kódolni, és a metódus hívásával el kell küldeni a `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` Computer Vision rendszernek.
* AnalyzeInDomainUrlAsync  
  Ez a módszer a távoli képek, amelyben az URL-címet a `ComputerVisionClient.AnalyzeImageByDomainAsync` rendszer a kép a Computer Vision hívja meg a metódust.

A `UploadAndAnalyzeInDomainImageAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. Mivel a mintaalkalmazás egy helyi lemezképet elemez, el kell küldenie a kép tartalmát a Computer Vision rendszernek. Megnyitja a `imageFilePath` `Stream`helyi fájl olvasásához megadott , majd beolvassa a kiválasztott nyelvet a forgatókönyv oldalon. Meghívja `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` a metódust, átadja `Stream` a fájlt, a tartománymodell nevét és a nyelvet, majd `DomainModelResults` példányként adja vissza az eredményt. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

A `AnalyzeInDomainUrlAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. A forgatókönyv oldalon kiválasztott nyelvet kapja. Meghívja `ComputerVisionClient.AnalyzeImageByDomainAsync` a metódust, átadja a kép URL-címét, a vizuális `DomainModelResults` funkciókat és a nyelvet, majd az eredményt példányként adja vissza. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

## <a name="explore-the-describe-image-scenario"></a>A Kép leírása forgatókönyv felfedezése

Ezt a forgatókönyvet a DescribePage.xaml lap kezeli. Kiválaszthatja a nyelvet, hogy hozzon létre egy ember által olvasható leírást a kép, és látni mind a kép és az eredmények. A forgatókönyvlap a következő módszereket használja a kép forrásától függően:

* UploadAndDescribeImageAsync  
  Ez a módszer a helyi képekhez használatos, amelyekben `Stream` a lemezképet a rendszerként kell kódolni, és a metódus hívásával el kell küldeni a `ComputerVisionClient.DescribeImageInStreamAsync` Computer Vision rendszernek.
* DescribeUrlAsync  
  Ez a módszer a távoli képek, amelyben az URL-címet a `ComputerVisionClient.DescribeImageAsync` rendszer a kép a Computer Vision hívja meg a metódust.

A `UploadAndDescribeImageAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. Mivel a mintaalkalmazás egy helyi lemezképet elemez, el kell küldenie a kép tartalmát a Computer Vision rendszernek. Megnyitja a `imageFilePath` `Stream`helyi fájl olvasásához megadott , majd beolvassa a kiválasztott nyelvet a forgatókönyv oldalon. Meghívja `ComputerVisionClient.DescribeImageInStreamAsync` a metódust, átadja a `Stream` fájlt, a jelöltek maximális számát (ebben az esetben 3), `ImageDescription` és a nyelvet, majd az eredményt példányként adja vissza. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

A `DescribeUrlAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. A forgatókönyv oldalon kiválasztott nyelvet kapja. Meghívja `ComputerVisionClient.DescribeImageAsync` a metódust, átadja a kép URL-címét, a jelöltek maximális számát (ebben az `ImageDescription` esetben 3), és a nyelvet, majd az eredményt példányként adja vissza. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

## <a name="explore-the-generate-tags-scenario"></a>A Címkék létrehozása forgatókönyv felfedezése

Ezt a forgatókönyvet a TagsPage.xaml lap kezeli. Kiválaszthatja azt a nyelvet, amelyen megcímkézheti a kép vizuális jellemzőit, és megtekintheti a képet és az eredményeket is. A forgatókönyvlap a következő módszereket használja a kép forrásától függően:

* UploadAndGetTagsForImageAsync  
  Ez a módszer a helyi képekhez használatos, amelyekben `Stream` a lemezképet a rendszerként kell kódolni, és a metódus hívásával el kell küldeni a `ComputerVisionClient.TagImageInStreamAsync` Computer Vision rendszernek.
* GenerateTagsForUrlAsync  
  Ez a módszer a távoli képek, amelyben az URL-címet a `ComputerVisionClient.TagImageAsync` rendszer a kép a Computer Vision hívja meg a metódust.

A `UploadAndGetTagsForImageAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. Mivel a mintaalkalmazás egy helyi lemezképet elemez, el kell küldenie a kép tartalmát a Computer Vision rendszernek. Megnyitja a `imageFilePath` `Stream`helyi fájl olvasásához megadott , majd beolvassa a kiválasztott nyelvet a forgatókönyv oldalon. Meghívja `ComputerVisionClient.TagImageInStreamAsync` a metódust, átadva a `Stream` fájlt és a `TagResult` nyelvet, majd példányként visszaadja az eredményt. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

A `GenerateTagsForUrlAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. A forgatókönyv oldalon kiválasztott nyelvet kapja. Meghívja `ComputerVisionClient.TagImageAsync` a metódust, átadja a kép URL-címét `TagResult` és a nyelvet, majd az eredményt példányként adja vissza. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

## <a name="explore-the-recognize-text-ocr-scenario"></a>A Szöveg felismerése (OCR) forgatókönyv felfedezése

Ezt a forgatókönyvet az OCRPage.xaml lap kezeli. Kiválaszthatja azt a nyelvet, amely felismeri és kinyeri a nyomtatott szöveget a képből, és megtekintheti a képet és az eredményeket is. A forgatókönyvlap a következő módszereket használja a kép forrásától függően:

* UploadAndRecognizeImageAsync  
  Ez a módszer a helyi képekhez használatos, amelyekben `Stream` a lemezképet a rendszerként kell kódolni, és a metódus hívásával el kell küldeni a `ComputerVisionClient.RecognizePrintedTextInStreamAsync` Computer Vision rendszernek.
* RecognizeUrlAsync  
  Ez a módszer a távoli képek, amelyben az URL-címet a `ComputerVisionClient.RecognizePrintedTextAsync` rendszer a kép a Computer Vision hívja meg a metódust.

A `UploadAndRecognizeImageAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. Mivel a mintaalkalmazás egy helyi lemezképet elemez, el kell küldenie a kép tartalmát a Computer Vision rendszernek. Megnyitja a `imageFilePath` `Stream`helyi fájl olvasásához megadott , majd beolvassa a kiválasztott nyelvet a forgatókönyv oldalon. Meghívja `ComputerVisionClient.RecognizePrintedTextInStreamAsync` a metódust, jelezve, hogy `Stream` a tájolás nem észlelhető, és `OcrResult` átadja a fájlt és a nyelvet, majd az eredményt példányként adja vissza. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

A `RecognizeUrlAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. A forgatókönyv oldalon kiválasztott nyelvet kapja. Meghívja `ComputerVisionClient.RecognizePrintedTextAsync` a metódust, jelezve, hogy a tájolás nem észlelhető, és `OcrResult` átadja a kép URL-címét és a nyelvet, majd az eredményt példányként adja vissza. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>A Szöveg felismerése V2 (angol) forgatókönyv felfedezése

Ezt a forgatókönyvet a TextRecognitionPage.xaml lap kezeli. Kiválaszthatja a felismerési módot és a nyelvet, hogy aszinkron módon felismerje és kinyerje a nyomtatott vagy kézzel írt szöveget a képből, és mind a képet, mind az eredményeket láthassa. A forgatókönyvlap a következő módszereket használja a kép forrásától függően:

* UploadAndRecognizeImageAsync  
  Ez a módszer helyi képekhez használatos, amelyekben a `Stream` lemezképet a rendszerként `RecognizeAsync` kell kódolni, és el `ComputerVisionClient.RecognizeTextInStreamAsync` kell küldeni a Computer Vision rendszernek a metódus hívásával és a metódus paraméterezett delegálásával.
* RecognizeUrlAsync  
  Ez a módszer a távoli képekhez használatos, amelyekben a rendszerkép `RecognizeAsync` URL-címét a számítógép-látásnak küldi el a metódus hívásával és a `ComputerVisionClient.RecognizeTextAsync` metódus paraméterezett delegálásával.
* RecognizeAsync Ez a módszer kezeli az aszinkron `UploadAndRecognizeImageAsync` hívás `RecognizeUrlAsync` mind a és a metódusok, valamint a lekérdezés az eredmények a `ComputerVisionClient.GetTextOperationResultAsync` metódus hívásával.

A Computer Vision mintaalkalmazásban szereplő többi forgatókönyvtől eltérően ez a forgatókönyv aszinkron, mivel az egyik módszer a folyamat elindításához van meghívva, de egy másik módszer van meghívva az állapot ellenőrzésére és a folyamat eredményeinek visszaadására. Ebben a forgatókönyvben a logikai folyamat némileg eltér a többi forgatókönyvben.

A `UploadAndRecognizeImageAsync` metódus megnyitja a `imageFilePath` helyi fájl `Stream`megadott olvasási `RecognizeAsync` a , majd meghívja a metódust, halad:

* Lambda kifejezés a `ComputerVisionClient.RecognizeTextInStreamAsync` metódus paraméterezett aszinkron delegáltjának, a `Stream` fájlhoz és a `GetHeadersAsyncFunc`felismerési módparaméterhez a alkalmazásban.
* Lambda-kifejezés egy meghatalmazott számára `Operation-Location` a válaszfejléc `GetOperationUrlFunc`értékének leolvasásához a alkalmazásban.

A `RecognizeUrlAsync` módszer `RecognizeAsync` meghívja a módszert, átadva:

* Lambda kifejezés a `ComputerVisionClient.RecognizeTextAsync` metódus paraméterezett aszinkron delegáltahoz, a távoli lemezkép URL-címével és `GetHeadersAsyncFunc`a felismerési módparaméterként paraméterként a alkalmazásban.
* Lambda-kifejezés egy meghatalmazott számára `Operation-Location` a válaszfejléc `GetOperationUrlFunc`értékének leolvasásához a alkalmazásban.

Amikor `RecognizeAsync` a metódus befejeződött, mind a `UploadAndRecognizeImageAsync` `RecognizeUrlAsync` `TextOperationResult` metódusok, mind a metódusok példányként adják vissza az eredményt. Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

A `RecognizeAsync` metódus meghívja a paraméterezett `ComputerVisionClient.RecognizeTextAsync` delegáltot a vagy a `GetHeadersAsyncFunc` `ComputerVisionClient.RecognizeTextInStreamAsync` metódus átadott, és várja a választ. A metódus ezután meghívja az átadott `GetOperationUrlFunc` delegáltot, hogy megkapja a `Operation-Location` válasz fejlécének értékét a válaszból. Ez az érték a Computer Vision `GetHeadersAsyncFunc` metódusban átadott metódus eredményeinek beolvasásához használt URL-cím.

A `RecognizeAsync` metódus ezután meghívja a `ComputerVisionClient.GetTextOperationResultAsync` metódust, átadva a `Operation-Location` válaszfejlécből beolvasott `GetHeadersAsyncFunc`URL-címet, hogy megkapja a metódus ban átadott metódus állapotát és eredményét. Ha az állapot nem jelzi, hogy a metódus sikeresen `ComputerVisionClient.GetTextOperationResultAsync` vagy sikertelenül befejeződött, a metódus még 3 alkalommal hív, és 3 másodpercet vár a `RecognizeAsync` hívások között. A `RecognizeAsync` metódus visszaadja az eredményeket a metódusnak, amely azt nevezte.

## <a name="explore-the-get-thumbnail-scenario"></a>A Miniatűrök beszerezése forgatókönyv felfedezése

Ezt a forgatókönyvet a ThumbnailPage.xaml lap kezeli. Megadhatja, hogy intelligens körülvágást használjon-e, és megadhatja a kívánt magasságot és szélességet, hogy miniatűrt hozzon létre egy képből, és megtekintheti a képet és az eredményeket is. A forgatókönyvlap a következő módszereket használja a kép forrásától függően:

* UploadAndThumbnailImageAsync  
  Ez a módszer a helyi képekhez használatos, amelyekben `Stream` a lemezképet a rendszerként kell kódolni, és a metódus hívásával el kell küldeni a `ComputerVisionClient.GenerateThumbnailInStreamAsync` Computer Vision rendszernek.
* ThumbnailUrlAsync  
  Ez a módszer a távoli képek, amelyben az URL-címet a `ComputerVisionClient.GenerateThumbnailAsync` rendszer a kép a Computer Vision hívja meg a metódust.

A `UploadAndThumbnailImageAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. Mivel a mintaalkalmazás egy helyi lemezképet elemez, el kell küldenie a kép tartalmát a Computer Vision rendszernek. Megnyitja a helyi fájl `imageFilePath` olvasásához `Stream`. Meghívja `ComputerVisionClient.GenerateThumbnailInStreamAsync` a metódust, átadva `Stream` a szélességet, a magasságot, a fájlhoz, `Stream`és hogy intelligens körülvágást használjon-e, majd az eredményt a . Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

A `RecognizeUrlAsync` metódus létrehoz `ComputerVisionClient` egy új példányt a megadott előfizetési kulcs és végpont URL-címét használva. Meghívja `ComputerVisionClient.GenerateThumbnailAsync` a metódust, átadva a szélességet, a magasságot, a kép URL-címét, és hogy használja-e az intelligens körülvágást, majd az eredményt `Stream`a . Az `ImageScenarioPage` osztálytól örökölt metódusok a visszaadott eredményeket a forgatókönyvoldalon adják meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje azt `Microsoft/Cognitive-Vision-Windows` a mappát, amelybe a tárolót klónozta. Ha úgy döntött, hogy a mintaképeket használja, `Microsoft/Cognitive-Face-Windows` törölje azt a mappát is, amelybe a tárházat klónozta.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Első lépések a Face szolgáltatással](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
