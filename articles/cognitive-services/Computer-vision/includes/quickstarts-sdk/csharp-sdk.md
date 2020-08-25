---
title: 'Rövid útmutató: a .NET-hez készült ügyféloldali kódtár Computer Vision'
description: Ebben a rövid útmutatóban megismerheti a .NET-hez készült Computer Vision ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: ebeea9cd6e928a9d9d77a8475cee774f3d33b28e
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88753033"
---
<a name="HOLTop"></a>

[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Példák](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [.net Core SDK](https://dotnet.microsoft.com/download/)legújabb verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* [Hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs és a végpont URL-címéhez, illetve a nevet `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. 

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `computer-vision-quickstart` . Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: *ComputerVisionQuickstart.cs*.

```dotnetcli
dotnet new console -n computer-vision-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```dotnetcli
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A projekt könyvtárában nyissa meg a *ComputerVisionQuickstart.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

Az alkalmazás **program** osztályában hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse az Computer Vision .NET-hez készült ügyféloldali kódtárat a következő paranccsal:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0-preview.1
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Computer Vision .NET SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Ez az osztály minden Computer Vision funkcióhoz szükséges. Ezt az előfizetési adatokkal hozza létre, és a legtöbb képművelet végrehajtásához használja.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Ez az osztály további metódusokat tartalmaz a **ComputerVisionClient**.|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Ez az enumerálás a képelemzés különböző típusait határozza meg, amelyeket szabványos elemzési műveletekben lehet elvégezni. Az igényeinek megfelelően adja meg a VisualFeatureTypes-értékek készletét. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat az Computer Vision .NET-hez készült ügyféloldali kódtára használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)
* [Nyomtatott és kézzel írt szöveg olvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a Computer Vision kulcshoz és a végponthoz `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

Egy új metódusban hozza létre az ügyfelet a végponttal és a kulccsal. Hozzon létre egy **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** objektumot a kulccsal, és használja a végpontján egy **[ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)** objektum létrehozásához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

Valószínűleg ezt a metódust szeretné hívni a `Main` metódusban.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A következő kód egy metódust határoz meg, `AnalyzeImageUrl` amely az ügyfél-objektumot használja egy távoli rendszerkép elemzéséhez és az eredmények kinyomtatásához. A metódus a szöveges leírást, a kategorizálást, a címkék listáját, az észlelt arcokat, a felnőtt tartalom jelzőit, a fő színeket és a képtípust adja vissza.

Adja hozzá a metódus hívását a `Main` metódushoz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Tesztelési rendszerkép beállítása

A **program** osztályban mentse az elemezni kívánt rendszerkép URL-címére mutató hivatkozást.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

> [!NOTE]
> Elemezheti a helyi rendszerképet is. A helyi rendszerképeket érintő forgatókönyvek a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) találhatók.

### <a name="specify-visual-features"></a>Vizuális funkciók meghatározása

Adja meg az új módszert a képelemzéshez. Adja hozzá az alábbi kódot, amely meghatározza, hogy mely vizuális funkciókat szeretné kibontani az elemzésbe. A teljes listát a **[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** enumerálásban tekintheti meg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

A funkciók megvalósításához szúrja be az alábbi kódrészletek egyikét a **AnalyzeImageUrl** metódusba. Ne felejtse el felvenni a záró szögletes zárójelet.

```csharp
}
```

### <a name="analyze"></a>Elemzés

A **AnalyzeImageAsync** metódus egy **ImageAnalysis** objektumot ad vissza, amely az összes kinyert információt tartalmazza.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

Az alábbi részek azt mutatják be, hogyan elemezheti ezeket az információkat részletesen.

### <a name="get-image-description"></a>Rendszerkép lekérése – Leírás

A következő kód beolvassa a rendszerképhez létrehozott feliratok listáját. További részletekért tekintse meg a [képek leírása](../../concept-describing-images.md) című témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Rendszerkép beolvasása kategória

A következő kód lekéri a rendszerkép észlelt kategóriáját. További részletekért tekintse meg a [képek kategorizálása](../../concept-categorizing-images.md) című témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Képcímkék beolvasása

A következő kód beolvassa az észlelt címkék készletét a képen. További részletekért tekintse meg a [tartalom címkéit](../../concept-tagging-images.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Objektumok észlelése

A következő kód észleli a rendszerképben szereplő általános objektumokat, és kiírja azokat a konzolra. További részletekért lásd az [objektum észlelése](../../concept-object-detection.md) című témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a vállalati márkákat és emblémákat a rendszerképben, és kinyomtatja őket a konzolra. További részletekért lásd a [márka észlelését](../../concept-brand-detection.md) ismertető témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

A következő kód az észlelt arcokat adja vissza a képen a téglalap koordinátáival, majd a Face attribútumok elemet. További részletekért tekintse meg az [Arcfelismerés](../../concept-detecting-faces.md) című témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtt, zamatos vagy véres tartalom észlelése

A következő kód kinyomtatja a felnőtt tartalom észlelt jelenlétét a képen. További részletekért tekintse meg a [felnőtt, a zamatos és a véres tartalmat](../../concept-detecting-adult-content.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Rendszerképek színsémájának beolvasása

A következő kód az észlelt színattribútumokat nyomtatja ki a képen, például a domináns színeket és a kiejtés színét. További részletekért lásd: [Színsémák](../../concept-detecting-color-schemes.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Tartományhoz tartozó tartalom beolvasása

A Computer Vision speciális modelleket használhat a képek további elemzéséhez. További részletekért tekintse meg a [tartományra vonatkozó tartalmat](../../concept-detecting-domain-content.md) . 

A következő kód az észlelt hírességek adatait elemzi a képen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

A következő kód az észlelt tereptárgyak adatait elemzi a képen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>A rendszerkép típusának beolvasása

Az alábbi kód a képtípussal kapcsolatos információkat jeleníti &mdash; meg, függetlenül attól, hogy a ClipArt vagy egy vonalas rajzról van szó.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

A Computer Vision a képen látható szöveget olvashatja, és átalakíthatja a karakteres adatfolyamba. A szöveges felismeréssel kapcsolatos további információkért tekintse meg az [optikai karakterfelismerés (OCR)](../../concept-recognizing-text.md#read-api) fogalmi dokumentációját. Az ebben a szakaszban található kód az 3,0-es [olvasáshoz a legújabb Computer Vision SDK-kiadást](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) használja, és definiál egy metódust, `BatchReadFileUrl` amely az ügyfélalkalmazás segítségével azonosítja és Kinyeri a szöveget a lemezképben.

Adja hozzá a metódus hívását a `Main` metódushoz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Tesztelési rendszerkép beállítása

A **program** osztályban mentsen egy hivatkozást azon rendszerkép URL-címére, amelyből szöveget szeretne kinyerni. Ez a kódrészlet a nyomtatott és a kézírásos szöveghez is tartalmaz mintaképeket.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

> [!NOTE]
> Helyi rendszerképből is kinyerheti a szöveget. A helyi rendszerképeket érintő forgatókönyvek a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) találhatók.

### <a name="call-the-read-api"></a>Az olvasási API meghívása

Adja meg az új metódust a szöveg olvasásához. Adja hozzá az alábbi kódot, amely meghívja a **ReadAsync** metódust az adott képhez. Ez egy műveleti azonosítót ad vissza, és elindít egy aszinkron folyamatot a rendszerkép tartalmának olvasásához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_url)]

### <a name="get-read-results"></a>Olvasási eredmények beolvasása

Ezután kérje le a **ReadAsync** -hívás által visszaadott művelet azonosítóját, és használja a szolgáltatás lekérdezésére a művelet eredményeihez. A következő kód ellenőrzi a műveletet, amíg vissza nem adja az eredményeket. Ezután kinyomtatja a kinyert szöveges adatát a konzolon.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_response)]

### <a name="display-read-results"></a>Olvasási eredmények megjelenítése

Adja hozzá a következő kódot a beolvasott szöveges információk elemzéséhez és megjelenítéséhez, majd fejezze be a metódus definícióját.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_display)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Computer Vision API-hivatkozás (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [Mi a Computer Vision?](../../Home.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)található.
