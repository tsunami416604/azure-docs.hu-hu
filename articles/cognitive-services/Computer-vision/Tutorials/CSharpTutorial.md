---
title: Számítógép Látástechnológiai API C# oktatóanyag |} Microsoft Docs
description: Fedezze fel egy alapszintű Windows-alkalmazást, amely a számítógép Látástechnológiai API kognitív Microsoft-szolgáltatásokban. Feldolgozhatjuk miniatűrök létrehozása és a kép visual funkciók.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347879"
---
# <a name="computer-vision-api-c35-tutorial"></a>Számítógép Látástechnológiai API C&#35; oktatóanyag

Fedezze fel az olyan alapvető Windows-alkalmazás, amely a számítógép Látástechnológiai API segítségével optikai karakter felismerési (OCR) hajtsa végre, hozzon létre az intelligenskártya-levágja miniatűröket, és észleli, kategorizálását, címke és vizuális funkciókat, arc, beleértve a kép ismertetik. Az alábbi példa segítségével elküldeni egy kép URL-CÍMÉT vagy egy helyileg tárolt fájlt. A nyílt forráskódú példa sablonként használhatja a saját alkalmazás a Windows rendszert a Látástechnológiai API és a WPF (a Windows Presentation Foundation), a .NET-keretrendszer része kiépítéséhez.

### <a name="prerequisites"></a>Előfeltételek

#### <a name="platform-requirements"></a>Platform-követelmények

Az alábbi példában kidolgozott a .NET-keretrendszer használatával [Visual Studio 2015, Community Edition](https://www.visualstudio.com/downloads/).

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Számítógép Látástechnológiai API-ra szolgáltatásra, és egy előfizetés kulcs beszerzése 

Mielőtt létrehozná a példa, elő kell fizetnie a Microsoft kognitív szolgáltatások (korábbi nevén projekt Oxford) részét képező számítógép Látástechnológiai API. Előfizetés és a kulcskezelést részletek [előfizetések](https://azure.microsoft.com/try/cognitive-services/). Ebben az oktatóanyagban mind az elsődleges és másodlagos kulcsot is használható. 

> [!NOTE]
> Az oktatóanyag az célja, hogy az előfizetés-kulcsok használata a **westcentralus** régióban. Az előfizetés kulcsok jön létre a számítógép stratégiai szabad eljárást használja a **westcentralus** régió, így megfelelően működnek. Ha az előfizetés kulcsok használata az Azure-fiókjával keresztül hozta létre [ https://azure.microsoft.com/ ](https://azure.microsoft.com/), meg kell adnia a **westcentralus** régióban. Generált kívül kulcsok a **westcentralus** régió nem fog működni.

#### <a name="get-the-client-library-and-example"></a>Az ügyfél első könyvtár és példa

Előfordulhat, hogy a számítógép Látástechnológiai API és a példa ügyfélalkalmazás a számítógéphez keresztül klónozását [SDK](https://www.github.com/microsoft/cognitive-vision-windows). Ne töltse le, egy ZIP.

### <a name="Step1">1. lépés: A példában telepítése</a>

A GitHub asztalon nyissa meg a minta-WPF\VisionAPI-WPF-Samples.sln.

### <a name="Step2">2. lépés: A példában létrehozása</a>

* Nyomja le a Ctrl + Shift + B, vagy a menüszalag menü Build, majd válassza ki a megoldás felépítéséhez.

### <a name="Step3">3. lépés: A példában futtatása</a>

1. A létrehozás befejezése után nyomja le az **F5** , vagy kattintson a **Start** a menüszalag menü a példa futtatásához.
2. Keresse meg a számítógép Látástechnológiai API felhasználói felület ablakban az "Illessze be ide az Előfizetés kulcs elindításához" olvasása szövegmezőben.
Az Előfizetés kulcs a számítógépen vagy hordozható megőrizni a "Kulcs mentése" gombra kattintva választhat. Ha törölni szeretné az Előfizetés kulcs a rendszerből, kattintson a "Kulcs törlése" eltávolítása a PC- vagy hordozható.

    ![Stratégiai előfizetés kulcs](../Images/Vision_UI_Subscription.PNG)

3. A "Esetekben válassza" a hat forgatókönyvek közül kattintson, majd kövesse a képernyőn megjelenő utasításokat. A Microsoft a képek feltöltése, és előfordulhat, hogy felhasználja javítására számítógép Látástechnológiai API és a kapcsolódó szolgáltatások kap. Kép történő elküldésekor által meggyőződött róla, hogy elvégezte a [fejlesztői viselkedési](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).

    ![Kép felület elemzése](../Images/Analyze_Image_Example.PNG)

4. A mintaalkalmazás használható például képek vannak. Megtalálhatja ezeket a lemezképeket a Arcfelismerési API Windows Github-tárház, ebben a [Adatmappa](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data). Vegye figyelembe ezeket a lemezképeket használatát megállapodás licencbe [licenc-LEMEZKÉP](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md).

### <a name="Review">Tekintse át, és ismerje meg</a>

Most, hogy a futó alkalmazások, ossza meg velünk tekintse át hogyan e példában az alkalmazás integrálja az kognitív szolgáltatások technológiával. Ez megkönnyíti épület ezt az alkalmazást, vagy a saját Microsoft számítógép Látástechnológiai API-jával alkalmazás fejlesztése.

Ez például az alkalmazás lehetővé teszi, hogy a számítógép Látástechnológiai API ügyféloldali kódtár, a vékony C# ügyfél burkoló a Microsoft számítógép Látástechnológiai API-hoz használni. A fent ismertetett példa app parancsfájlkezelő, ha az ügyféloldali kódtár kapott NuGet-csomagot. Tekintse át a mappában, című ügyféloldali kódtár forráskódját "**ügyféloldali kódtár**" alatt **stratégiai**, **Windows**, **ügyféloldali kódtár**, amely a letöltött fájl tárház Előfeltételek a fent említett része.

Azt is megtudhatja, hogyan használható az ügyféloldali kódtár kódot a Solution Explorer: alatt **VisionAPI-WPF_Samples**, bontsa ki a **AnalyzePage.xaml** található **AnalyzePage.xaml.cs**, amely a képet, hogy a kép elemzés végpont küldésére szolgál. Kattintson duplán a. Ezek xaml.cs fájlok megnyitása a Visual Studio új windows.

Vizsgáljuk meg megtekintette a stratégiai ügyféloldali kódtár lekérdezi használatáról példa alkalmazásnak, a két kódtöredékek **AnalyzePage.xaml.cs**. A fájl "Kulcs minta kód KEZDŐDIK itt" és "Kulcs minta kód karakterlánccal végződik-e itt" hogy könnyebben megtalálja az alábbiakban bemutatott kódtöredékek kód jelző kód megjegyzéseket tartalmaz.

Az elemzés-végpont esetében dolgozhasson bemenetként kép URL-címe vagy bináris adatok (formájában egy octet-stream). Először keresse a using irányelv, amely lehetővé teszi, hogy a stratégiai ügyféloldali Kódtárára.

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(...)**  a kódrészletet bemutatja, hogyan nyújt az Előfizetés kulcs és az elemzések szolgáltatás végpontjához tartozó számítógép Látástechnológiai API egy helyileg tárolt lemezképet az ügyféloldali kódtár segítségével.

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(...)**  a kódrészletet bemutatja, hogyan nyújt az Előfizetés kulcs és az elemzések szolgáltatás végpontjához tartozó számítógép Látástechnológiai API fénykép URL-CÍMÉT az ügyféloldali kódtár segítségével.

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**Más lapok és a végpontok** hogyan kezelheti a többi végpont a számítógép Látástechnológiai API szolgáltatás által a mintában szereplő többi lapján megadandó megnézzük látható; például a OCR végpont OCRPage.xaml.cs a szabályzat részeként is látható 

### <a name="Related">Kapcsolódó témakörök</a>
 * [Ismerkedés a Arcfelismerési API](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


