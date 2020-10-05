---
title: fájlbefoglalás
description: fájlbefoglalás
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: 2a3129e6a4ce51d9c50d210c6ce87a8d6fbda6de
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91545050"
---
Használja a .NET-hez készült Language Understanding (LUIS) ügyféloldali kódtárait a következőhöz:
* Alkalmazás létrehozása
* Egy cél, egy gépi megtanult entitás hozzáadása egy példához
* Alkalmazás betanítása és közzététele
* Előrejelzési futtatókörnyezet lekérdezése

[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)  |  [Szerzői műveletek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) és [előrejelzési](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) függvénytár forráskódja | NuGet [készítése](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) és [előrejelzése](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C# minta](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/sdk-3x//Program.cs)

## <a name="prerequisites"></a>Előfeltételek

* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core) és a [a .net Core parancssori felülete](https://docs.microsoft.com/dotnet/core/tools/)aktuális verziója.
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* Ha már rendelkezik Azure-előfizetéssel, [hozzon létre egy Language Understanding szerzői erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) a Azure Portal a kulcs és a végpont beszerzéséhez. Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a [létrehozott](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) erőforrás kulcsára és végpontra az alkalmazás Language Understanding létrehozásához való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja. A szolgáltatás kipróbálásához használhatja az ingyenes díjszabási szintet ( `F0` ).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben.

1. A konzol ablakban (például cmd, PowerShell vagy bash) a DotNet `new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `language-understanding-quickstart` . Ez a parancs egy egyszerű "„Helló világ!” alkalmazás" C#-projektet hoz létre egyetlen forrásfájl használatával: `Program.cs` .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába.

    ```dotnetcli
    cd language-understanding-quickstart
    ```

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

### <a name="install-the-nuget-libraries"></a>A NuGet-kódtárak telepítése

Az alkalmazás könyvtárában telepítse a .NET-hez készült Language Understanding (LUIS) ügyféloldali kódtárait az alábbi parancsokkal:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.2.0-preview.3
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.1.0-preview.1
```

## <a name="authoring-object-model"></a>Szerzői objektummodell

A Language Understanding (LUIS) authoring Client egy [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) objektum, amely a szerzői kulcsot tartalmazó Azure-ba hitelesíti.

## <a name="code-examples-for-authoring"></a>Példák a szerzői műveletekre

Az ügyfél létrehozása után ezt az ügyfelet használhatja a következő funkciók eléréséhez, többek között:

* Alkalmazások – [Létrehozás](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [Törlés](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [Közzététel](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Példa: hosszúságú kimondott szöveg- [Hozzáadás](https://docs.microsoft.com//dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.addasync?view=azure-dotnet), [Törlés azonosító alapján](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Funkciók – [kifejezések listája](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* Modell – [leképezések](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) és entitások kezelése
* Minta – [minták](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet) kezelése
* Betanítás [az](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) alkalmazáshoz és a lekérdezés a [betanítási állapothoz](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Verziók](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) – kezelés klónozással, exportálással és törléssel

## <a name="prediction-object-model"></a>Előrejelzési objektum modellje

A Language Understanding (LUIS) előrejelzési futtatókörnyezet ügyfele egy [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) objektum, amely az Azure-ba hitelesíti az erőforrás kulcsát.

## <a name="code-examples-for-prediction-runtime"></a>Példák az előrejelzési futtatókörnyezetre

Az ügyfél létrehozása után ezt az ügyfelet használhatja a következő funkciók eléréséhez, többek között:

* Előrejelzés [átmeneti vagy üzemi tárolóhely](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet) alapján
* Előrejelzés [verziója](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet) szerint


[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a *program.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Cserélje le a meglévő `using` kódot a következő `using` irányelvekre:

[!code-csharp[Add NuGet libraries to code file](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Szabványos kód hozzáadása

1. Módosítsa a metódus aláírását az `Main` aszinkron hívások engedélyezéséhez:

    ```csharp
    public static async Task Main()
    ```

1. Ha másként nincs megadva, adja hozzá a kód többi részét az `Main` osztály metódusához `Program` .

## <a name="create-variables-for-the-app"></a>Változók létrehozása az alkalmazáshoz

Hozzon létre két változót: az első módosítást, a második beállított értéket, ahogy a kód mintában szerepelnek. 

1. Hozzon létre változókat a szerzői kulcsok és az erőforrások neveinek tárolásához.

    [!code-csharp[Variables you need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouChange)]

1. Hozzon létre változókat a végpontok, az alkalmazás neve, a verzió és a leképezés nevének tárolásához.

    [!code-csharp[Variables you don't need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) objektumot a kulccsal, és használja a végpontján egy [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) objektum létrehozásához.

[!code-csharp[Authenticate the client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUIS-alkalmazás létrehozása

A LUIS-alkalmazás a természetes nyelvi feldolgozó (NLP) modellt tartalmazza, beleértve a szándékokat, az entitásokat és a példaként szolgáló hosszúságú kimondott szöveg.

Hozzon létre egy [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). A név és a nyelvi kultúra kötelező tulajdonságai. Hívja meg az [apps. AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) metódust. A válasz az alkalmazás azonosítója.

[!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Szándék létrehozása az alkalmazáshoz
A LUIS-alkalmazás modelljében lévő elsődleges objektum a szándék. A szándék a felhasználói Kimondás _céljainak_csoportosításával igazodik. Előfordulhat, hogy egy felhasználó felteheti a kérdést, vagy egy olyan utasítást, amely egy bot (vagy más ügyfélalkalmazás) által megadott _kívánt_ választ keres. Ilyenek például a repülőjáratok foglalása, az időjárás megkérdezése egy adott célállomáson, és az ügyfélszolgálat elérhetőségi adatai.

Hozzon létre egy [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) az egyedi szándék nevével, majd adja át az alkalmazás azonosítóját, a Version ID-t és a ModelCreateObject a [Model. AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) metódusnak. A válasz a szándék azonosítója.

Az `intentName` érték nem módosítható az `OrderPizzaIntent` [alkalmazáshoz tartozó változók létrehozása](#create-variables-for-the-app) változóinak részeként.

[!code-csharp[Create intent for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Entitások létrehozása az alkalmazáshoz

Habár az entitások nem kötelezőek, a legtöbb alkalmazásban megtalálhatók. Az entitás kinyeri az adatokat a felhasználótól, és a felhasználó szándékának fullfil szükséges. Az [előre elkészített](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) és az egyéni entitások több típusa is van, amelyek mindegyike saját Adatátalakítási objektum-(DTO-) modellel rendelkezik.  Az alkalmazásba felvenni kívánt közös előre összeépített entitások közé tartozik a [Number](../luis-reference-prebuilt-number.md), a [datetimeV2](../luis-reference-prebuilt-datetimev2.md), a [geographyV2](../luis-reference-prebuilt-geographyv2.md)és a [sorszám](../luis-reference-prebuilt-ordinal.md).

Fontos tudni, hogy az entitások nincsenek megjelölve szándékkal megjelölve. Ezek általában számos szándékra vonatkoznak. Csak a példaként megadott felhasználói hosszúságú kimondott szöveg van megjelölve egy adott, egyetlen szándékkal.

Az entitások létrehozási módszerei a [Model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) osztály részei. Minden entitás típusa saját Adatátalakítási objektum (DTO) modellt tartalmaz, amely általában a `model` [modell](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) névterében található szót tartalmazza.

Az entitás-létrehozási kód olyan gépi tanulási entitást hoz létre, amely alentitásokkal és az alentitásokra alkalmazott funkciókkal rendelkezik `Quantity` .

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Részleges képernyőkép a portálról, amely a létrehozott entitást, az alentitásokkal és a &quot;mennyiség&quot; alentitásokra alkalmazott funkciókkal rendelkező gépi tanulási entitást mutatja.":::

[!code-csharp[Create entities for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddEntities)]

A következő metódussal keresheti meg a mennyiség alentitásának AZONOSÍTÓját, hogy hozzárendelje az adott alentitáshoz tartozó szolgáltatásokat.

[!code-csharp[Find subentity id](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Példaként való Kimondás hozzáadása a szándékhoz

Ha meg szeretné határozni a teljes szándékot, és kinyeri az entitásokat, az alkalmazásnak példákat kell hosszúságú kimondott szöveg. A példákban egy adott, egyetlen szándékot kell megcélozni, és az összes egyéni entitást meg kell jelölni. Az előre elkészített entitásokat nem kell megjelölni.

Adja hozzá például a hosszúságú kimondott szöveg egy [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) -objektumok listájának létrehozásával, amely minden egyes példa kiírásának egy objektumát tartalmazza. Mindegyik példa minden entitást megjelöl az entitás neve és az entitás értéke név/érték párokkal rendelkező szótárával. Az entitás értékének pontosan úgy kell lennie, ahogy a példa szövegében megjelenik.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Részleges képernyőkép a portálról, amely a létrehozott entitást, az alentitásokkal és a &quot;mennyiség&quot; alentitásokra alkalmazott funkciókkal rendelkező gépi tanulási entitást mutatja.":::

Hívja meg a [példákat. AddAsync](https://docs.microsoft.com//dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.addasync?view=azure-dotnet) az alkalmazás azonosítóját, a verziószámot és a példát.

[!code-csharp[Add example utterance to intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Az alkalmazás betanítása

A modell létrehozása után a LUIS alkalmazást a modell ezen verziójára kell képezni. A betanított modell használható egy [tárolóban](../luis-container-howto.md), vagy [közzétehető](../luis-how-to-publish-app.md) az átmeneti vagy a termék tárolóhelyeken.

A [Train. TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) metódusnak meg kell felelnie az alkalmazás azonosítójának és a verzió azonosítójának.

Nagyon kis modell, például ez a rövid útmutató mutatja, nagyon gyorsan betanítja. Üzemi szintű alkalmazások esetén az alkalmazásnak be kell vonnia a [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) metódus lekérdezési hívását annak meghatározására, hogy mikor vagy mikor sikerült a képzés. A válasz az egyes objektumokhoz külön állapotú [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) objektumok listája. Az összes objektumnak sikeresnek kell lennie ahhoz, hogy a képzés befejezze.

[!code-csharp[Train the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Alkalmazás közzététele az üzemi tárolóhelyen

Tegye közzé a LUIS alkalmazást a [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) metódus használatával. Ez közzéteszi a jelenlegi betanított verziót a végponton megadott tárolóhelyre. Az ügyfélalkalmazás ezt a végpontot használja arra, hogy felhasználói hosszúságú kimondott szöveg küldjön a szándékok és az entitások kinyerésének előrejelzésére.

[!code-csharp[Publish app to production slot](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Az előrejelzési futtatókörnyezet ügyfelének hitelesítése

Használjon egy [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) -objektumot a kulccsal, és használja a végpontján egy [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) objektum létrehozásához.

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-csharp[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PredictionCreateClient)]


## <a name="get-prediction-from-runtime"></a>Előrejelzés lekérése futtatókörnyezetből

Adja hozzá a következő kódot az előrejelzési futtatókörnyezethez való kérelem létrehozásához.

A felhasználó teljes értéke a [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) objektum része.

A **GetSlotPredictionAsync** metódusnak több paraméterre van szüksége, például az alkalmazás azonosítója, a tárolóhely neve, az előrejelzési kérelem objektum a kérelem teljesítéséhez. A többi lehetőség, például a részletes, az összes leképezés megjelenítése és a napló megadása nem kötelező.

[!code-csharp[Get prediction from runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `dotnet run` paranccsal az alkalmazás könyvtárából.

```dotnetcli
dotnet run
```
