---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/26/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 6f735831594e5084c56b6b1d88f18b27ddabcb7d
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871290"
---
A .NET-hez készült ügyféloldali kódtár Language Understanding (LUIS) létrehozásához használja a következőt:

* Alkalmazás létrehozása
* Leképezések, entitások és példa hosszúságú kimondott szöveg hozzáadása
* Olyan funkciók hozzáadása, mint például a kifejezések listája
* Alkalmazás betanítása és közzététele

[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring)  |  [Szerzői csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)  |  [C#-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.
* Ha már rendelkezik Azure-előfizetéssel, [hozzon létre egy Language Understanding szerzői erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) a Azure Portal a kulcs és a végpont beszerzéséhez. Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a [létrehozott](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) erőforrás kulcsára és végpontra az alkalmazás Language Understanding létrehozásához való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja. A szolgáltatás kipróbálásához használhatja az ingyenes díjszabási szintet ( `F0` ).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben.

1. A konzol ablakban (például cmd, PowerShell vagy bash) a DotNet `new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `language-understanding-quickstart` . Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájl használatával: `Program.cs` .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába.

1. Az alkalmazást az alábbiakkal hozhatja létre:

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


### <a name="install-the-sdk"></a>Az SDK telepítése

Az alkalmazás könyvtárában telepítse a .NET-hez készült Language Understanding (LUIS) authoring ügyféloldali kódtárat a következő paranccsal:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.


## <a name="object-model"></a>Objektummodell

A Language Understanding (LUIS) authoring Client egy [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) objektum, amely a szerzői kulcsot tartalmazó Azure-ba hitelesíti.

Az ügyfél létrehozása után ezt az ügyfelet használhatja a következő funkciók eléréséhez, többek között:

* Alkalmazások – [Létrehozás](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [Törlés](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [Közzététel](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Példa hosszúságú kimondott szöveg – [Hozzáadás köteg](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet)szerint, [Törlés azonosító alapján](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Funkciók – [kifejezések listája](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* Modell – [leképezések](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) és entitások kezelése
* Minta – [minták](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet) kezelése
* Betanítás [az](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) alkalmazáshoz és a lekérdezés a [betanítási állapothoz](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Verziók](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) – kezelés klónozással, exportálással és törléssel


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a .NET-hez készült Language Understanding (LUIS) authoring ügyféloldali kódtár használatával:

* [Alkalmazás létrehozása](#create-a-luis-app)
* [Entitások hozzáadása](#create-entities-for-the-app)
* [Leképezések hozzáadása](#create-intent-for-the-app)
* [Példa kimondott szövegek hozzáadása](#add-example-utterance-to-intent)
* [Az alkalmazás betanítása](#train-the-app)
* [Az alkalmazás közzététele](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a *program.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Cserélje le a meglévő `using` kódot a következő `using` irányelvekre:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

1. Hozzon létre egy változót, amely az authoring Key és a authoring végpontot fogja tárolni.

    [!code-csharp[Authorization to resource key](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=Variables)]

1. Hozzon létre egy [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) objektumot a kulccsal, és használja a végpontján egy [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) objektum létrehozásához.

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUIS-alkalmazás létrehozása

1. Hozzon létre egy LUIS-alkalmazást, amely tartalmazza a természetes nyelvi feldolgozó (NLP) modellt, amely a szándékok, az entitások és a példa hosszúságú kimondott szöveg.

1. Hozzon létre egy [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). A név és a nyelvi kultúra kötelező tulajdonságai.

1. Hívja meg az [apps. AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) metódust. A válasz az alkalmazás azonosítója.

    [!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Szándék létrehozása az alkalmazáshoz
A LUIS-alkalmazás modelljében lévő elsődleges objektum a szándék. A szándék összehangolja a felhasználói Kimondás _szándékait_tartalmazó csoporttal. Előfordulhat, hogy egy felhasználó felteheti a kérdést, vagy egy olyan utasítást, amely egy bot (vagy más ügyfélalkalmazás) által megadott _kívánt_ választ keres. Ilyenek például a repülőjáratok foglalása, az időjárás megkérdezése egy adott célállomáson, és az ügyfélszolgálat elérhetőségi adatai.

Hozzon létre egy [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) az egyedi szándék nevével, majd adja át az alkalmazás azonosítóját, a Version ID-t és a ModelCreateObject a [Model. AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) metódusnak. A válasz a szándék azonosítója.

[!code-csharp[Create intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Entitások létrehozása az alkalmazáshoz

Habár az entitások nem kötelezőek, a legtöbb alkalmazásban megtalálhatók. Az entitás kinyeri az adatokat a felhasználótól, és a felhasználó szándékának fullfil szükséges. Az [előre elkészített](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) és az egyéni entitások több típusa is van, amelyek mindegyike saját Adatátalakítási objektum-(DTO-) modellel rendelkezik.  Az alkalmazásba felvenni kívánt közös előre összeépített entitások közé tartozik a [Number](../luis-reference-prebuilt-number.md), a [datetimeV2](../luis-reference-prebuilt-datetimev2.md), a [geographyV2](../luis-reference-prebuilt-geographyv2.md)és a [sorszám](../luis-reference-prebuilt-ordinal.md).

Ez a **AddEntities** metódus egy `Location` egyszerű entitást hozott létre két szerepkörrel, egy `Class` egyszerű entitással, egy `Flight` összetett entitással és több előre elkészített entitás hozzáadásával.

Fontos tudni, hogy az entitások nincsenek megjelölve szándékkal megjelölve. Ezek általában számos szándékra vonatkoznak. Csak például a felhasználó hosszúságú kimondott szöveg van megjelölve egy adott, egyetlen szándékkal.

Az entitások létrehozási módszerei a [Model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) osztály részei. Minden entitás típusa saját Adatátalakítási objektum (DTO) modellt tartalmaz, amely általában a `model` [modell](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) névterében található szót tartalmazza.

[!code-csharp[Create entities](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Példaként való Kimondás hozzáadása a szándékhoz

Ha meg szeretné határozni a teljes szándékot, és kinyeri az entitásokat, az alkalmazásnak példákat kell hosszúságú kimondott szöveg. A példákban egy adott, egyetlen szándékot kell megcélozni, és az összes egyéni entitást meg kell jelölni. Az előre elkészített entitásokat nem kell megjelölni.

Adja hozzá például a hosszúságú kimondott szöveg egy [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) -objektumok listájának létrehozásával, amely minden egyes példa kiírásának egy objektumát tartalmazza. Mindegyik példa minden entitást megjelöl az entitás neve és az entitás értéke név/érték párokkal rendelkező szótárával. Az entitás értékének pontosan úgy kell lennie, ahogy a példa szövegében megjelenik.

Hívja meg a [példákat. BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) az alkalmazás-azonosítóval, a verzió azonosítóval és a példák listájával. A hívás az eredmények listájával válaszol. Ellenőriznie kell az egyes példák eredményeit, hogy biztosan hozzá lehessen adni a modellhez.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringBatchAddUtterancesForIntent)]

A **CreateUtterance** és a **CreateLabel** metódus az objektumok létrehozásához használható segédprogram-metódus.

## <a name="train-the-app"></a>Az alkalmazás betanítása

A modell létrehozása után a LUIS alkalmazást a modell ezen verziójára kell képezni. A betanított modell használható egy [tárolóban](../luis-container-howto.md), vagy [közzétehető](../luis-how-to-publish-app.md) az átmeneti vagy a termék tárolóhelyeken.

A [Train. TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) metódusnak meg kell felelnie az alkalmazás azonosítójának és a verzió azonosítójának.

Nagyon kis modell, például ez a rövid útmutató mutatja, nagyon gyorsan betanítja. Üzemi szintű alkalmazások esetén az alkalmazásnak be kell vonnia a [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) metódus lekérdezési hívását annak meghatározására, hogy mikor vagy mikor sikerült a képzés. A válasz az egyes objektumokhoz külön állapotú [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) objektumok listája. Az összes objektumnak sikeresnek kell lennie ahhoz, hogy a képzés befejezze.

[!code-csharp[Train the app's version](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Language Understanding alkalmazás közzététele

Tegye közzé a LUIS alkalmazást a [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) metódus használatával. Ez közzéteszi a jelenlegi betanított verziót a végponton megadott tárolóhelyre. Az ügyfélalkalmazás ezt a végpontot használja arra, hogy felhasználói hosszúságú kimondott szöveg küldjön a szándékok és az entitások kinyerésének előrejelzésére.

[!code-csharp[Create entities](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Alkalmazás futtatása

Futtassa az alkalmazást a `dotnet run` paranccsal az alkalmazás könyvtárából.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha törölni szeretné a programot, törölheti a LUIS alkalmazást. Az alkalmazás törlése az [apps. DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) metódussal történik. Az alkalmazást a [Luis portálról](https://www.luis.ai)is törölheti.