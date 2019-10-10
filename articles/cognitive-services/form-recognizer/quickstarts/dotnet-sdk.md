---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtára a .NET-hez | Microsoft Docs'
description: Ismerkedés a .NET-hez készült űrlap-felismerő ügyféloldali kódtáraval.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: pafarley
ms.openlocfilehash: fc0964508f3031efd91db827524042bf0577ab5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242519"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Gyors útmutató: űrlap-felismerő ügyféloldali kódtára a .NET-hez

Ismerkedés a .NET-hez készült űrlap-felismerő ügyféloldali kódtáraval. Az űrlap-felismerő egy olyan kognitív szolgáltatás, amely gépi tanulási technológiával azonosítja és Kinyeri a kulcs/érték párokat és a táblák adatait az űrlap dokumentumaiból. Ezután strukturált adatokat ad vissza, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat. Az alábbi lépéseket követve telepítheti az SDK-csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.

Használja a .NET-hez készült űrlap-felismerő ügyféloldali kódtárat a következőhöz:

* [Egyéni űrlap-felismerő modell betanítása](#train-a-custom-model)
* [Kinyert kulcsok listájának beolvasása](#get-a-list-of-extracted-keys)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek listájának beolvasása](#get-a-list-of-custom-models)
* [Egyéni modell törlése](#delete-a-custom-model)

[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview)@no__t – 1[könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer) | [csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* Hozzáférés az űrlap-felismerő korlátozott hozzáférésének előzetes verziójához. Az előzetes verzió eléréséhez töltse ki és küldje el az [űrlap-felismerő hozzáférési kérelmének](https://aka.ms/FormRecognizerRequestAccess) űrlapját.
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adataival kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../build-training-data-set.md) létrehozása című témakört. Ehhez a rövid útmutatóhoz [minta adatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) is használhat.
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-form-recognizer-azure-resource"></a>Űrlap-felismerő Azure-erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

Miután beolvasott egy kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) `FORM_RECOGNIZER_KEY` nevű kulcshoz.

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) a `dotnet new` paranccsal hozzon létre egy új, `formrecognizer-quickstart` nevű Console-alkalmazást. Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C# projektet hoz létre egyetlen forrásfájlban: _program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Ezután hozza létre az alkalmazást az alábbiakkal:

```console
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

A projekt könyvtárában nyissa meg a _program.cs_ fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő `using`-utasításokat:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Ezután adja hozzá a következő kódot az alkalmazás **fő** metódusához. Ezt az aszinkron feladatot később is megadhatja.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse a .NET-hez készült űrlap-felismerő ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A következő osztályok az űrlap-felismerő SDK fő funkcióit kezelik.

|Név|Leírás|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Ez az osztály minden űrlap-felismerő funkció esetében szükséges. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Ezt az osztályt használja az egyéni űrlap-felismerő modell betanítására saját betanítási adatok használatával. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Ez az osztály egy egyéni modell-betanítási művelet eredményeit tartalmazza, beleértve a modell AZONOSÍTÓját is, amelyet aztán az űrlapok elemzésére használhat. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Ez az osztály az egyéni modell elemzési műveletének eredményeit jeleníti meg. Tartalmazza az **ExtractedPage** -példányok listáját. |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Ez az osztály az egyetlen dokumentumból kinyert összes adathalmazt jelképezi.|

## <a name="code-examples"></a>Példák a kódokra

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a .NET-hez készült űrlap-felismerő ügyféloldali kódtára használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Egyéni űrlap-felismerő modell betanítása](#train-a-custom-model)
* [Kinyert kulcsok listájának beolvasása](#get-a-list-of-extracted-keys)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek listájának beolvasása](#get-a-list-of-custom-models)
* [Egyéni modell törlése](#delete-a-custom-model)

## <a name="define-variables"></a>Változók meghatározása

A módszerek meghatározása előtt adja hozzá a következő változó definíciókat a **program** osztályának elejéhez. A változók némelyikét saját kezűleg kell kitöltenie. 

* A szolgáltatás végpontjának értékét a Azure Portal **Áttekintés** szakaszában találja. 
* A betanítási adataihoz tartozó SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, és válassza a **közös hozzáférési aláírás beolvasása**elemet. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A formátumnak a következőket kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A `Main` metódus alatt adja meg a `Main`-ben hivatkozott feladatot. Itt a fentiekben megadott előfizetési változók használatával hitelesítheti az ügyféloldali objektumot. A többi módszert később is megadhatja.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

A következő módszer az űrlap-felismerő ügyfél objektumát használja az Azure Blob-tárolóban tárolt dokumentumokra vonatkozó új felismerési modell betanításához. Egy segítő módszer segítségével jeleníti meg az újonnan betanított modell ( [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) -objektum által jelölt) adatait, és visszaadja a modell azonosítóját.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

A következő segítő módszer az űrlap-felismerő modellel kapcsolatos információkat jeleníti meg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Kinyert kulcsok listájának beolvasása

A betanítás befejezése után az egyéni modell megőrzi a kinyert kulcsok listáját a betanítási dokumentumokból. A jövőben a következő formátumú dokumentumokat fogja tartalmazni, amelyek tartalmazzák a kulcsokat, és Kinyeri a megfelelő értékeket az elemzési műveletben. A következő módszer használatával kérheti le a kinyert kulcsok listáját, és kinyomtathatja azt a-konzolra. Ez jó módszer a betanítási folyamat hatékonyságának ellenőrzésére.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a módszer az űrlap-felismerő ügyfelet és a modell AZONOSÍTÓját használja egy PDF-űrlap dokumentumának elemzéséhez és a kulcs/érték adatok kinyeréséhez. Egy segítő metódus használatával jeleníti meg az eredményeket (amelyet egy [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) objektum képvisel).

> [!NOTE]
> A következő módszer egy PDF-űrlapot elemez. A JPEG-és PNG-űrlapokat elemző hasonló módszerekhez tekintse meg a teljes mintakód a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

A következő segítő módszer az elemzési művelettel kapcsolatos információkat jeleníti meg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Egyéni modellek listájának beolvasása

Visszaállíthatja a fiókjához tartozó összes betanított modell listáját, és lekérheti a létrehozásuk időpontját. A modellek listáját egy [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) objektum képviseli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Egyéni modell törlése

Ha törölni szeretné az egyéni modellt a fiókjából, használja a következő metódust:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az `dotnet run` parancs meghívásával az alkalmazás könyvtárából.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ha a fiókból törölni kívánt egyéni modellt is betanított, futtassa a metódust az [Egyéni modell törlése](#delete-a-custom-model)lehetőséggel.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az űrlap felismerő .NET ügyféloldali függvénytárát használta az egyéni modell és az elemzési űrlapok betanításához. Következő lépésként Ismerkedjen meg a jobb betanítási adatkészlet létrehozásával és a pontosabb modellek előállításával.

> [!div class="nextstepaction"]
>[Betanítási adatkészlet létrehozása](../build-training-data-set.md)

* [Mi az az űrlap-felismerő?](../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer)található.
