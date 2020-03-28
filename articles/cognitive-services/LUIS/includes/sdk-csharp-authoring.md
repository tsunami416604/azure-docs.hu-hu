---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 53e6382cf8d046b2c9818b906890bc64642fd2ed
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372211"
---
A .NET nyelvismerése (LUIS) készítő ügyféltár segítségével tegye a következőket:

* Alkalmazás létrehozása
* Leképezések, entitások és példakimondott szöveg hozzáadása
* Szolgáltatások, például kifejezéslista hozzáadása
* Alkalmazás betanítása és közzététele

[Dokumentációs referenciatár](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | Szerzői csomag[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [C# minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Előfeltételek

* Language Understanding (LUIS) portálfiók - [Hozzon létre egyet ingyen](https://www.luis.ai)
* A [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója .


## <a name="setting-up"></a>Beállítása

### <a name="get-your-language-understanding-luis-starter-key"></a>A nyelvismertetési (LUIS) indítókulcs beszerezése

A [kezdőkulcs](../luis-how-to-azure-subscription.md#starter-key) beszerezése egy LUIS szerzői erőforrás létrehozásával. Tartsa meg a kulcsot, és a régió a kulcs a következő lépéshez.

### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

A kulcs és a régió használatával hozzon létre két környezeti változót a hitelesítéshez:

* `COGNITIVESERVICE_AUTHORING_KEY`- A kérések hitelesítéséhez.
* `COGNITIVESERVICE_REGION`- A kulcshoz társított régió. Például: `westus`.

Használja az operációs rendszerre vonatkozó utasításokat.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

A környezeti változó hozzáadása után indítsa újra a konzolablakot.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[Macos](#tab/unix)

A program `.bash_profile`szerkesztése és a környezeti változó hozzáadása:

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
***

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core alkalmazást a kívánt szerkesztőben vagy IDE-ben.

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) `new` a dotnet paranccsal `language-understanding-quickstart`hozzon létre egy új konzolalkalmazást a .. Ez a parancs egy egyszerű "Hello World" C# `Program.cs`projektet hoz létre egyetlen forrásfájllal: .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára.

1. Az alkalmazást a következőkkel hozhatja létre:

    ```dotnetcli
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


### <a name="install-the-sdk"></a>Az SDK telepítése

Az alkalmazáskönyvtáron belül telepítse a nyelvismertetés (LUIS) nyelvi ismeretek (LUIS) készítő ügyféltárat a .NET programhoz a következő paranccsal:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Ha a Visual Studio IDE-t használja, az ügyfélkönyvtár letölthető NuGet csomagként érhető el.


## <a name="object-model"></a>Objektummodell

A language understanding (LUIS) szerzői ügyfél egy [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) objektum, amely hitelesíti az Azure-ban, amely tartalmazza a szerzői kulcsot.

Az ügyfél létrehozása után ezzel az ügyféllel érheti el a funkciókat, például a következőket:

* Alkalmazások - [létrehozás,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) [törlés,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) [közzététel](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Példa kimondott szövegre - [kötegenkénti hozzáadás](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [törlés azonosító szerint](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Jellemzők - [kifejezéslisták](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) kezelése
* Modell – [leképezések](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) és entitások kezelése
* Minta – [minták](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet) kezelése
* Vonat - az alkalmazás [betanítása](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) és a szavazás a [képzési állapothoz](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Verziók](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - kezelése klónozással, exportálással és törléssel


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan teheti meg a következőket a .NET nyelvi ismeretek (LUIS) szerzői ügyfélkódtárjával:

* [Alkalmazás létrehozása](#create-a-luis-app)
* [Entitások hozzáadása](#create-entities-for-the-app)
* [Leképezések hozzáadása](#create-intent-for-the-app)
* [Példa kimondott szövegek hozzáadása](#add-example-utterance-to-intent)
* [Az alkalmazás betanítása](#train-the-app)
* [Az alkalmazás közzététele](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárából nyissa meg a *Program.cs* fájlt a kívánt szerkesztőben vagy IDE-ben. Cserélje le `using` a meglévő `using` kódot a következő irányelvekre:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

1. Hozzon létre egy változót a szerzői kulcs `COGNITIVESERVICES_AUTHORING_KEY`kezelésére, amelyet egy nevű környezeti változóból hívnak le. Ha az alkalmazás elindítása után hozta létre a környezeti változót, a szerkesztőt, az IDE-t vagy a rendszerhéjat futtató rendszernek be kell zárnia, és újra kell töltenie a változó eléréséhez. A metódusok később jönnek létre.

1. Hozzon létre változókat a szerzői régió és a végpont tárolására. A szerzői kulcs területe attól függ, hogy hol készül. A [három szerzői régió](../luis-reference-regions.md) a következő:

    * Ausztrália -`australiaeast`
    * Európa -`westeurope`
    * Egyesült Államok és más `westus` régiók - (Alapértelmezett)

    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Hozzon létre egy [ApiKeyServiceClientS objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) a kulccsal, és használja azt a végponttal egy [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) objektum létrehozásához.

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUIS-alkalmazás létrehozása

1. Hozzon létre egy LUIS-alkalmazást a természetes nyelvi feldolgozási (NLP) modell leképezések, entitások és példa utterances tárolása.

1. Hozzon létre egy [ApplicationCreateObject objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). A név és a nyelvi kultúra kötelező tulajdonságok.

1. Hívja meg az [Apps.AddAsync metódust.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) A válasz az alkalmazás azonosítója.

    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Szándék létrehozása az alkalmazáshoz
A LUIS-alkalmazás modelljének elsődleges objektuma a szándék. A szándék a felhasználói utterance (kifejezés) szándékok csoportosításához _igazodik._ A felhasználó feltehet egy kérdést, vagy nyilatkozatot tehet, amelyben egy robot (vagy más ügyfélalkalmazás) konkrét _tervezett_ válaszát keresi. Szándékok például a repülőjegy foglalása, a célváros időjárásáról való kérdezés, valamint az ügyfélszolgálat elérhetőségi adatainak megkérdezése.

Hozzon létre egy [ModelCreateObject objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) az egyedi szándék nevével, majd adja át az alkalmazásazonosítót, a verzióazonosítót és a ModelCreateObject objektumot a [Model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) metódusnak. A válasz a szándékazonosító.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Entitások létrehozása az alkalmazáshoz

Bár az entitások nem szükségesek, a legtöbb alkalmazásban megtalálhatók. Az entitás kinyeri az információkat a felhasználói utterance (kifejezés), a felhasználó szándékának teljes lebontásához szükséges. Többféle előre [összeállított](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) és egyéni entitások, mindegyik saját adatátalakítási objektum (DTO) modellekkel.  Az alkalmazáshoz hozzáadni a közös előre összeállított entitások közé tartozik a [szám](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinális](../luis-reference-prebuilt-ordinal.md).

Ez **az AddEntityes** metódus létrehozott egy `Class` egyszerű entitást `Flight` `Location` két szerepkörrel, egy egyszerű entitással, egy összetett entitással, és több előre összeállított entitást ad hozzá.

Fontos tudni, hogy az entitások nincsenek megjelölve szándékkal. Ezek, és általában nem vonatkozik a sok szándékot. Csak például a felhasználói utterances vannak megjelölve egy adott, egyetlen szándék.

Az entitások létrehozási módszerei a [Modell](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) osztály részét képezik. Minden entitástípus saját adatátalakítási objektum (DTO) modellel `model` rendelkezik, amely általában a Szót tartalmazza a [Modellek](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) névtérben.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Példa utterance (kifejezés) hozzáadása a szándékhoz

Az utterance (kifejezés) szándékának meghatározásához és az entitások kinyeréséhez az alkalmazásnak példákat kell hoznia az utterances. A példáknak egy adott, egyetlen leképezést kell megcélozniuk, és meg kell jelölniük az összes egyéni entitást. Az előre összeállított entitásokat nem kell megjelölni.

Példa utterances hozzáadásával egy listát [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) objektumok, egy-egy objektum minden példa utterance. Minden példának meg kell jelölnie az összes entitást egy szótárral az entitásnév és az entitásérték név/érték párjaiból. Az entitás értékének pontosan úgy kell lennie, ahogy a példa utterance (kifejezés) szövegében megjelenik.

Hívja [meg a Examples.BatchAsync fájlt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) az alkalmazásazonosítóval, a verzióazonosítóval és a példák listájával. A hívás az eredmények listájával válaszol. Ellenőriznie kell az egyes példák eredményét, hogy megbizonyosodjon arról, hogy sikeresen hozzá lett adva a modellhez.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]

A **CreateUtterance** és **createLabel** metódusok olyan segédprogram-módszerek, amelyek segítenek az objektumok létrehozásában.

## <a name="train-the-app"></a>Az alkalmazás betanítása

A modell létrehozása után a LUIS-alkalmazást be kell tanítani a modell ezen verziójához. A betanított modell használható egy [tárolóban,](../luis-container-howto.md)vagy [közzé az](../luis-how-to-publish-app.md) átmeneti vagy termék tárolókban.

A [Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) metódusnak szüksége van az alkalmazásazonosítóra és a verzióazonosítóra.

Egy nagyon kis modell, mint például ez a rövid útmutató mutatja, a vonat nagyon gyorsan. Éles szintű alkalmazások esetén az alkalmazás betanításának tartalmaznia kell egy lekérdezési hívást a [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) metódushoz, hogy meghatározza, mikor és ha a betanítás sikeres volt. A válasz a [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) objektumok listája, amelyek minden objektumhoz külön állapotban vannak. Minden objektumnak sikeresnek kell lennie ahhoz, hogy a képzés teljesnek minősüljen.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Nyelvismertetési alkalmazás közzététele

Tegye közzé a LUIS alkalmazást a [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) metódushasználatával. Ez közzéteszi az aktuális betanított verziót a végpont megadott tárolóhelyére. Az ügyfélalkalmazás ezt a végpontot használja a szándék és az entitás kinyerésének előrejelzéséhez a felhasználó kimondott szövegének küldéséhez.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `dotnet run` alkalmazást az alkalmazáskönyvtárból származó paranccsal.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy a karbantartás, törölheti a LUIS alkalmazást. Az alkalmazás törlése az [Apps.DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) metódussal történik. Az alkalmazást törölheti is a [LUIS portálról.](https://www.luis.ai)