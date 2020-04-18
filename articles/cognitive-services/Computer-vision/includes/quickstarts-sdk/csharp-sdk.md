---
title: 'Rövid útmutató: Computer Vision ügyfélkönyvtár a .NET-hez'
description: Ebben a rövid útmutatóban ismerkedje meg a Computer Vision ügyfélkódtár .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: ce311d2deaf0eec76ef0dec3ea279a387d14fd22
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81637128"
---
<a name="HOLTop"></a>

[Referenciadokumentációkönyvtár](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [forráskódjának](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [csomagja (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [mintái](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója .

## <a name="setting-up"></a>Beállítása

### <a name="create-a-computer-vision-azure-resource"></a>Computer Vision Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a Computer Vision az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes részére hét napok részére szabad. A regisztráció után elérhető lesz az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Tekintse meg az erőforrást az [Azure Portalon.](https://portal.azure.com/)

Miután lekért egy kulcsot a próba-előfizetés vagy erőforrás, [hozzon létre környezeti változók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) at a kulcs és a végpont URL-cím, nevű, `COMPUTER_VISION_SUBSCRIPTION_KEY` illetve. `COMPUTER_VISION_ENDPOINT`

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core alkalmazást a kívánt szerkesztőben vagy IDE-ben. 

Egy konzolablakban (például cmd, PowerShell vagy `dotnet new` Bash) a paranccsal `computer-vision-quickstart`hozhat létre egy új konzolalkalmazást a .. Ez a parancs egy egyszerű "Hello World" C# projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára. Az alkalmazást a következőkkel hozhatja létre:

```console
dotnet build
```

A build kimenetnem tartalmazhat figyelmeztetéseket vagy hibákat. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A projekt könyvtárából nyissa meg a *Program.cs* fájlt a kívánt szerkesztőben vagy IDE-ben. Adja hozzá `using` a következő irányelveket:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_using)]

Az alkalmazás **Program** osztályában hozzon létre változókat az erőforrás Azure-végpontés kulcs.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Az alkalmazáskönyvtáron belül telepítse a Computer Vision ügyfélkönyvtárat a .NET programhoz a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

Ha a Visual Studio IDE-t használja, az ügyfélkönyvtár letölthető NuGet csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Computer Vision .NET SDK néhány főbb jellemzőjét.

|Name (Név)|Leírás|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Ez az osztály szükséges az összes Computer Vision funkcióhoz. Az előfizetési adatokkal azonnal hozzágyűjtheti azt, és a legtöbb képművelethez használhatja őket.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Ez az osztály további módszereket tartalmaz a **ComputerVisionClient számára.**|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Ez a felsorítás határozza meg a különböző típusú képelemzés, hogy lehet tenni egy szabványos Elemzés művelet. A VisualFeatureTypes értékek et az igényektől függően adhatja meg. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a Computer Vision ügyfélkódtárban .NET esetén:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)
* [Nyomtatott és kézzel írt szöveg elolvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy [környezeti változókat hozott](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) létre `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` a Computer Vision kulcshoz és végponthoz, amelyeket elnevezett és megfelelően.

Egy új módszer, példányosítja az ügyfél a végpont és a kulcs. Hozzon létre egy **[ApiKeyServiceClientS objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** a kulccsal, és használja azt a végponttal **[egy ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)** objektum létrehozásához.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_auth)]

Ezt a módszert valószínűleg meg `Main` szeretné hívni a metódusban.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A következő kód egy `AnalyzeImageUrl`metódust határoz meg, amely az ügyfélobjektummal elemzi a távoli lemezképet, és kinyomtatja az eredményeket. A metódus szöveges leírást, kategorizálást, címkék listáját, észlelt arcokat, felnőtt tartalomjelzőket, főszíneket és képtípust ad vissza.

Adja hozzá a `Main` metódushívása a módszer.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Tesztkép beállítása

A **Program** osztályban mentse az elemezni kívánt kép URL-címére mutató hivatkozást.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_url)]

> [!NOTE]
> A helyi lemezképet is elemezheti. Tekintse meg a mintakódot a [GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) a helyi lemezképeket érintő forgatókönyvek.

### <a name="specify-visual-features"></a>Vizuális szolgáltatások megadása

Határozza meg az új képelemzési módszert. Adja hozzá az alábbi kódot, amely meghatározza az elemzéssorán kivonni kívánt vizuális funkciókat. A teljes listát a **[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** enum című témakörben láthatja.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_visualfeatures)]

Helyezze be az alábbi kódblokkok bármelyikét az **AnalyzeImageUrl** metódusba a funkciók megvalósításához. Ne felejtsen el záró konzolt hozzáadni a végén.

```csharp
}
```

### <a name="analyze"></a>Elemzés

Az **AnalysisImageAsync** metódus egy **olyan ImageAnalysis** objektumot ad vissza, amely az összes kinyert információt tartalmazza.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_call)]

A következő szakaszok bemutatják, hogyan elemezze részletesen ezeket az információkat.

### <a name="get-image-description"></a>Kép leírásának beszerezése

A következő kód lekéri a képhez létrehozott feliratok listáját. További részletek a [Képek leírása](../../concept-describing-images.md) című témakörben talál.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Képkategória beszereznie

A következő kód leteszi a kép észlelt kategóriáját. További részletekért lásd: [Képek kategorizálása.](../../concept-categorizing-images.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Képcímkék beszerezése

A következő kód leveszi az észlelt címkék készletét a képen. További részletekért lásd a [Tartalomcímkéket.](../../concept-tagging-images.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Objektumok észlelése

A következő kód észleli a képen lévő gyakori objektumokat, és kinyomtatja azokat a konzolra. További részleteket az [Objektumészlelés](../../concept-object-detection.md) ben talál.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a vállalati márkákat és emblémákat a képen, és kinyomtatja azokat a konzolra. További részletek a [Márkaészlelés](../../concept-brand-detection.md) ben.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

A következő kód a képen lévő észlelt arcokat adja vissza a téglalap koordinátáival, és kiválasztja az arcjellemzőket. További részleteket az [Arcfelismerés](../../concept-detecting-faces.md) ben talál.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtt, pikáns vagy véres tartalom észlelése

A következő kód kinyomtatja a felnőtt tartalom észlelt jelenlétét a képen. További [részletek: Felnőtt, pikáns, véres tartalom.](../../concept-detecting-adult-content.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Képszínséma beszerezése

A következő kód kinyomtatja a kép észlelt színjellemzőit, például a domináns színeket és a kiemelőszínt. További részleteket a [Színsémák](../../concept-detecting-color-schemes.md) ban talál.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Tartományspecifikus tartalom beszereznie

Computer Vision segítségével speciális modellek további elemzést a képeken. További részletekért lásd [a Tartományspecifikus tartalmat.](../../concept-detecting-domain-content.md) 

A következő kód elemzi a képen észlelt hírességek adatait.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_celebs)]

A következő kód elemzi a lemezkép észlelt tájékozódási pontjainak adatait.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>A képtípus beszereznie

A következő kód a kép&mdash;típusára vonatkozó információkat nyomtatja ki, függetlenül attól, hogy ClipArt-elemről vagy vonalrajzról van-e szó.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg elolvasása

A Computer Vision képes olvasni a kép látható szövegét, és karakterfolyammá alakítani. Az ebben a szakaszban található `ExtractTextUrl`kód egy metódust határoz meg, amely az ügyfélobjektumot használja a képen lévő nyomtatott vagy kézzel írt szöveg észlelésére és kibontására.

Adja hozzá a `Main` metódushívása a módszer.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Tesztkép beállítása

A **Program** osztályban mentse annak a képnek az URL-címét, amelyből szöveget szeretne kinyerni.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttext_url)]

> [!NOTE]
> Helyi képből is kinyerheti a szöveget. Tekintse meg a mintakódot a [GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) a helyi lemezképeket érintő forgatókönyvek.

### <a name="call-the-read-api"></a>Az olvasási API hívása

Adja meg a szöveg olvasásának új módszerét. Adja hozzá az alábbi kódot, amely meghívja az adott lemezkép **BatchReadFileAsync** metódusát. Ez egy műveletazonosítót ad vissza, és aszinkron folyamatot indít el a lemezkép tartalmának olvasásához.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>Olvasási eredmények beolvasása

Ezután olvassa be a **BatchReadFileAsync** hívásból visszaadott műveletazonosítót, és ezzel kérdezze le a szolgáltatást a művelet eredményéhez. A következő kód egy másodperces időközönként ellenőrzi a műveletet, amíg az eredmények et vissza nem adja. Ezután kinyomtatja a kinyert szöveges adatokat a konzolra.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>Olvasási eredmények megjelenítése

Adja hozzá a következő kódot a beolvasott szöveges adatok elemzéséhez és megjelenítéséhez, és fejezze be a metódusdefiníciót.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az `dotnet run` alkalmazáskönyvtárból a paranccsal.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Computer Vision API referencia (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [Mi a Computer Vision?](../../Home.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs)
