---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: 0b09f15eb05a94bb62f4484ac362467fbdb0af8b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188863"
---
[A dokumentációs](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [-csomagjához (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | tartozó[minták](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.

## <a name="using-this-quickstart"></a>A rövid útmutató használata

A rövid útmutató használatának számos lépése van:

* A Azure Portal hozzon létre egy személyre szabott erőforrást
* A Azure Portal a személyre szabott erőforráshoz a **konfiguráció** lapon módosítsa a modell frissítésének gyakoriságát nagyon rövid időtartamra.
* Egy Kódszerkesztő alkalmazásban hozzon létre egy kódot tartalmazó fájlt, és szerkessze a kódot.
* A parancssorban vagy a terminálban telepítse az SDK-t a parancssorból.
* A parancssorban vagy a terminálban futtassa a fájl kódját.

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben.

A konzol ablakban (például cmd, PowerShell vagy bash) a DotNet `new` paranccsal hozzon létre egy új, a nevű `personalizer-quickstart`Console-alkalmazást. Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájl használatával `Program.cs`:.

```console
dotnet new console -n personalizer-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

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

## <a name="install-the-sdk"></a>Az SDK telepítése

Telepítse az alkalmazás könyvtárában a .NET személyre szabott ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A személyre szabott ügyfél egy [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) objektum, amely a kulcsot tartalmazó Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t.

Ha szeretné megkérdezni a tartalom egyetlen legjobb elemét, hozzon létre egy [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview), majd továbbítsa azt az [ügyfélnek. Rangsor](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) metódusa. A Rank metódus egy RankResponse ad vissza.

A jutalom pontszámának személyre szabásához hozzon létre egy [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview), majd továbbítsa azt az [ügyfélnek. Jutalmazási](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) módszer.

A jutalom pontszám meghatározása ebben a rövid útmutatóban triviális. Éles rendszerekben annak meghatározása, hogy milyen hatással van a [jutalom pontszáma](../concept-rewards.md) , és hogy mekkora mértékben lehet egy összetett folyamat, dönthet úgy, hogy idővel változhat. Ennek a tervezési döntésnek a személyre szabott architektúrájának egyik elsődleges döntésének kell lennie.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a személyre szabott ügyféloldali kódtáraval a .NET-hez:

* [Személyre szabott ügyfél létrehozása](#create-a-personalizer-client)
* [Rangsor API](#request-the-best-action)
* [Jutalom API](#send-a-reward)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a **program.cs** fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Cserélje le a `using` meglévő kódot a következő `using` irányelvekre:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Személyre szabott erőforrás-információk hozzáadása

A **program** osztályban hozzon létre változókat az erőforráshoz tartozó Azure-kulcshoz, és a végpontot a `PERSONALIZER_RESOURCE_KEY` named és `PERSONALIZER_RESOURCE_ENDPOINT`a nevű környezeti változóból kihúzta. Ha az alkalmazás elindítása után hozta létre a környezeti változókat, akkor a változó eléréséhez be kell zárnia és újra kell töltenie a szerkesztőt, az IDE-t vagy a shellt. A metódusokat később a rövid útmutatóban fogja létrehozni.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Személyre szabott ügyfél létrehozása

Ezután hozzon létre egy metódust, amely egy személyre szabott ügyfelet ad vissza. A metódus paramétere a `PERSONALIZER_RESOURCE_ENDPOINT` és a ApiKey. `PERSONALIZER_RESOURCE_KEY`

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Élelmiszeripari elemek beolvasása sorba besorolt műveletként

A műveletek azokat a tartalmi beállításokat jelentik, amelyeknek a személyre szabásával ki kell választania a legjobb tartalmi elemet. Adja hozzá a következő metódusokat a program osztályhoz a műveletek és a hozzájuk tartozó funkciók megjelenítéséhez. 

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>A környezet felhasználói beállításainak beolvasása

Adja hozzá a következő metódusokat a program osztályhoz, hogy beolvassa a felhasználó bemenetét a parancssorból a nap időpontjában és az aktuális élelmiszer-preferencia alapján. Ezek környezeti funkciókként lesznek felhasználva.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Mindkét módszer a `GetKey` metódus használatával olvassa be a felhasználó kijelölését a parancssorból.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>A tanulási hurok létrehozása

A személyre szabott tanulási hurok a [Range](#request-the-best-action) és a [jutalmazási](#send-a-reward) hívások ciklusa. Ebben a rövid útmutatóban a tartalom személyre szabásához a rangsorban megjelenő minden egyes hívást egy jutalmazási hívás követ, amely alapján eldöntheti, hogy milyen jól teljesíti a szolgáltatást.

A következő kód hurkokat mutat be a felhasználónak a parancssorban való megadására, az információknak a személyre szabására való kiválasztásához, az ügyfélnek a listából való kiválasztásához, majd a jutalom pontszámának elküldéséhez, hogy a szolgáltatás milyen jól jelenjen meg a kiválasztásában.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Adja hozzá a következő metódusokat, amelyek [megkapják a tartalom választási lehetőségeit](#get-food-items-as-rankable-actions)a kódlap futtatása előtt:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>A legjobb művelet kérése

A rangsorolási kérelem teljesítéséhez a program megkéri a felhasználó beállításait, hogy hozzon létre egy `currentContent` tartalom-választási lehetőséget. A folyamat létrehozhat olyan tartalmat, amely kizárható a műveletekből, `excludeActions`a következő módon:. A válasz fogadásához a Rank kérelemnek szüksége van a műveletekre és azok szolgáltatásaira, a LicenseManager CurrentContext szolgáltatásaira, a excludeActions és egy egyedi esemény-AZONOSÍTÓra.

Ez a rövid útmutató a napszak és a felhasználói élelmiszer-beállítások egyszerű kontextusát tartalmazza. Az éles rendszerekben a [műveletek és szolgáltatások](../concepts-features.md) meghatározása és [értékelése](../concept-feature-evaluation.md) nem triviális kérdés lehet.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Jutalom küldése

Ahhoz, hogy a jutalom pontszáma a jutalom iránti kérelemben legyen elküldve, a program beolvassa a felhasználó kijelölését a parancssorból, hozzárendel egy numerikus értéket a kijelöléshez, majd elküldi az egyedi eseményazonosító és a jutalom pontszámát a jutalmazási API-nak megfelelő numerikus értékként.

Ez a rövid útmutató egy egyszerű számot rendel hozzá a jutalom pontszámához, vagy nulla vagy 1 értéket. Az éles rendszerekben az adott igényektől függően nem triviális kérdés lehet annak meghatározása, hogy mikor és mit kell elküldeni a [jutalmazási](../concept-rewards.md) hívásnak.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>A program futtatása

Futtassa az alkalmazást a DotNet `run` paranccsal az alkalmazás könyvtárából.

```console
dotnet run
```

![A gyors üzembe helyezési program néhány kérdést tesz fel a felhasználói preferenciák (más néven funkciók) összegyűjtésére, majd megadja a legfontosabb műveletet.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

A rövid útmutató [forráskódja](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) elérhető a személyre szabott minták GitHub-adattárában.
