---
title: 'Rövid útmutató: Űrlapfelismerő ügyféltár a .NET-hez'
description: Első lépések a .NET űrlapfelismerő ügyféltárával a program strukturált adatkimenet betanításához, kibontásához, elemzéséhez és lekérnie kell ezt a rövid útmutatót.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 8d05f5ad07f9b3affba9442d6caa6e63162b813c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399424"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Rövid útmutató: Űrlapfelismerő ügyféltár a .NET-hez

Első lépések a .NET Űrlapfelismerő ügyféltárral. A Formafelismerő egy kognitív szolgáltatás, amely gépi tanulási technológiát használ a kulcs-/értékpárok és a táblaadatok azonosítására és kinyerésére űrlapdokumentumokból. Ezután strukturált adatokat ad vissza, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat. Az alábbi lépésekkel telepítheti az SDK-csomagot, és kipróbálhatja az alapvető feladatok példakódját.

A .NET űrlapfelismerő ügyféltár segítségével:

* [Egyéni űrlapfelismerő modell betanítása](#train-a-custom-model)
* [Kibontott kulcsok listájának beszereznie](#get-a-list-of-extracted-keys)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek listájának beszereznie](#get-a-list-of-custom-models)
* [Egyéni modell törlése](#delete-a-custom-model)

[Referenciadokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [könyvtár forráskódcsomagja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/FormRecognizer) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/)
* Egy Azure Storage blob, amely betanítási adatokat tartalmaz. Lásd: [Betanítási adatkészlet létrehozása egyéni modellhez](../build-training-data-set.md) tippeket és lehetőségeket a betanítási adatok összeállításához. Ehhez a rövid útmutatóhoz használhatja a [mintaadatkészlet](https://go.microsoft.com/fwlink/?linkid=2090451) **Betanítás** mappája alatti fájlokat.
* A [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója .

## <a name="setting-up"></a>Beállítása

### <a name="create-a-form-recognizer-azure-resource"></a>Űrlapfelismerő Azure-erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

Miután lekért egy kulcsot és egy végpontot, [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcshoz és a végponthoz, amelyet elnevezett `FORM_RECOGNIZER_KEY` és `FORM_RECOGNIZER_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy `dotnet new` Bash) a paranccsal `formrecognizer-quickstart`hozhat létre egy új konzolalkalmazást a .. Ez a parancs egy egyszerű "Hello World" C# projektet hoz létre egyetlen forrásfájllal: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára. Ezután készítse el az alkalmazást a következőkkel:

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

A projekt könyvtárából nyissa meg a _Program.cs_ fájlt a kívánt szerkesztőben vagy IDE-ben. Adja hozzá a következő `using`-utasításokat:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Ezután adja hozzá a következő kódot az alkalmazás **fő** metódusában. Ezt az aszinkron feladatot később fogja definiálni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Az alkalmazáskönyvtáron belül telepítse a .NET Űrlapfelismerő ügyféltárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Ha a Visual Studio IDE-t használja, az ügyfélkönyvtár letölthető NuGet csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A következő osztályok kezelik a Form Recognizer SDK fő funkcióit.

|Name (Név)|Leírás|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Ez az osztály szükséges az összes Űrlapfelismerő funkcióhoz. Az előfizetési adatokkal azonnal létrehozhatja azt, és más osztályok példányainak létrehozásához használhatja őket.|
|[TrainRequest (TrainRequest)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Ezzel az osztállyal egyéni űrlapfelismerő modellt taníthatja be a saját betanítási bemeneti adataival. |
|[TrainResult vonateredménye](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Ez az osztály egy egyéni modell Train művelet eredményeit adja meg, beleértve a modellazonosítót is, amelyet ezután űrlapok elemzésére használhat. |
|[AnalyzeResult (Elemzési eredmény)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Ez az osztály egy egyéni modell elemzési műveletének eredményeit szolgáltatja. Tartalmazza a **Kibontottoldal** példányok listáját. |
|[Kibontott oldal](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Ez az osztály az egyetlen űrlapdokumentumból kinyert összes adatot jelöli.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a .NET Űrlapfelismerő ügyféltárban:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Egyéni űrlapfelismerő modell betanítása](#train-a-custom-model)
* [Kibontott kulcsok listájának beszereznie](#get-a-list-of-extracted-keys)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek listájának beszereznie](#get-a-list-of-custom-models)
* [Egyéni modell törlése](#delete-a-custom-model)

## <a name="define-variables"></a>Változók meghatározása

Mielőtt bármilyen módszert definiálna, adja hozzá a következő változódefiníciókat a **Program** osztály tetejére. Önnek kell kitölteni néhány változó magát. 

* A betanítási adatok SAS-URL-címének beolvasásához nyissa meg a Microsoft Azure Storage Exploreralkalmazást, kattintson a jobb gombbal a tárolóra, és válassza **a Megosztott hozzáférésű aláírás beolvasása parancsot.** Ellenőrizze, hogy az **Olvasás** és **a Lista** engedélyek be vannak-e jelölve, majd kattintson a **Létrehozás gombra.** Ezután másolja az **URL-cím** szakasz értékét. Meg kell a `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`forma: .
* Ha elemzésre van szüksége mintaűrlapra, használhatja a mintaadatkészlet **Teszt** mappájában található fájlok [egyikét.](https://go.microsoft.com/fwlink/?linkid=2090451) Ez az útmutató csak PDF-űrlapokat használ.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A `Main` módszer alatt adja meg a `Main`alkalmazásban hivatkozott feladatot. Itt hitelesíti az ügyfélobjektumot a fent meghatározott előfizetési változók használatával. A többi módszert később határozza meg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

A következő módszer a Form Recognizer ügyfélobjektum ot használja egy új felismerési modell betanításához az Azure blob tárolójában tárolt dokumentumokon. Egy segítő módszert használ az újonnan betanított modell [(egy ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) objektum által képviselt) adatainak megjelenítéséhez, és a modellazonosítót adja vissza.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

A következő segítő módszer az űrlapfelismerő modell adatait jeleníti meg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Kibontott kulcsok listájának beszereznie

A betanítás befejezése után az egyéni modell megőrzi a betanítási dokumentumokból kinyert kulcsok listáját. Arra számít, hogy a jövőbeli űrlapdokumentumok tartalmazzák ezeket a kulcsokat, és kinyeri a megfelelő értékeket az Elemzés műveletben. A kibontott kulcsok listájának beolvasásához és a konzolra való nyomtatásához használja az alábbi módszert. Ez egy jó módja annak, hogy ellenőrizze, hogy a képzési folyamat hatékony volt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a módszer az Űrlapfelismerő ügyfélprogramot és egy modellazonosítót használ a PDF-űrlapdokumentum elemzéséhez és a kulcs-/értékadatok kinyeréséhez. Egy segítő módszert használ az eredmények megjelenítéséhez [(amelyet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) egy AnalyzeResult objektum képvisel).

> [!NOTE]
> A következő módszer egy PDF-űrlapot elemez. A JPEG- és PNG-űrlapokat elemző hasonló módszereket a GitHub teljes mintakódjában [láthatja.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

A következő segítő módszer az Elemzés műveletadatait jeleníti meg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Egyéni modellek listájának beszereznie

Visszaküldheti a fiókjához tartozó összes betanított modell listáját, és lekérheti a létrehozásuk időpontjára vonatkozó információkat. A modellek listáját egy [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) objektum képviseli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Egyéni modell törlése

Ha törölni szeretné az egyéni modellt a fiókjából, használja az alábbi módszert:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `dotnet run` alkalmazást úgy, hogy meghívja a parancsot az alkalmazáskönyvtárból.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ha betanított egy egyéni modellt, amelyet törölni szeretne a fiókjából, futtassa a [metódust](#delete-a-custom-model)az Egyéni modell törlése mezőben.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az Űrlapfelismerő .NET ügyfélkódtár segítségével betanításra és űrlapok elemzésére használt. Ezután ismerje meg a tippeket, hogy hozzon létre egy jobb betanítási adatkészletet, és pontosabb modelleket készítsen.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../build-training-data-set.md)

* [Mi a Form Recognizer?](../overview.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer)
