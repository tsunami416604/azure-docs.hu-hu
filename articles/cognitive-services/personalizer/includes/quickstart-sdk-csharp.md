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
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7fd63841cabd91d46dd311f571fd100bbcfdd0fe
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122886"
---
[Referenciadokumentációkönyvtár](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [forráskódjának](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [csomagja (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [mintái](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója .

## <a name="using-this-quickstart"></a>A rövid útmutató használata

A rövid útmutató számos lépésből áll:

* Az Azure Portalon hozzon létre egy personalizer erőforrást
* Az Azure Portalon a Personalizer erőforrás, a **konfiguráció lapon** módosítsa a modell frissítési gyakoriságát egy nagyon rövid időközre
* Kódszerkesztőben hozzon létre egy kódfájlt, és szerkesztse a kódfájlt
* A parancssorba vagy a terminálba telepítse az SDK-t a parancssorból
* A parancssorban vagy a terminálon futtassa a kódfájlt

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core alkalmazást a kívánt szerkesztőben vagy IDE-ben.

Egy konzolablakban (például cmd, PowerShell vagy Bash) `new` a dotnet paranccsal `personalizer-quickstart`hozzon létre egy új konzolalkalmazást a .. Ez a parancs egy egyszerű "Hello World" C# `Program.cs`projektet hoz létre egyetlen forrásfájllal: .

```console
dotnet new console -n personalizer-quickstart
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

## <a name="install-the-sdk"></a>Az SDK telepítése

Az alkalmazáskönyvtáron belül telepítse a .NET Személyre szabásó ügyfélkönyvtárát a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Ha a Visual Studio IDE-t használja, az ügyfélkönyvtár letölthető NuGet csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A Personalizer ügyfél egy [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) objektum, amely hitelesíti magát az Azure-ban a Microsoft.Rest.ServiceClientCredentials használatával, amely tartalmazza a kulcsot.

A tartalom egyetlen legjobb elemének kéréséhez hozzon létre egy [RankRequest-et,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)majd adja át az [ügyfélnek. Rang](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) módszer. A Rank metódus RankResponse függvényt ad vissza.

Ha jutalompontszámot szeretne küldeni a Personalizer-nak, hozzon létre egy [RewardRequest-et,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)majd adja át az [ügyfélnek. Jutalom](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) módszer.

A jutalompontszám meghatározása ebben a rövid útmutatóban triviális. A termelési rendszerben annak meghatározása, hogy milyen hatással van a [jutalompontszám,](../concept-rewards.md) és mennyi lehet egy összetett folyamat, hogy ön dönthet úgy, hogy idővel megváltozik. Ez a tervezési döntés kell az egyik elsődleges döntéseket a Personalizer architektúra.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a .NET Személyre szabásó ügyféltárában:

* [Személyre szabó ügyfél létrehozása](#create-a-personalizer-client)
* [Rang API](#request-the-best-action)
* [Jutalom API](#send-a-reward)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárából nyissa meg a **Program.cs** fájlt a kívánt szerkesztőben vagy IDE-ben. Cserélje le `using` a meglévő `using` kódot a következő irányelvekre:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Személyre szabászati erőforrásadatok hozzáadása

A **Program** osztályban hozzon létre változókat az erőforrás Azure-kulcsához és `PERSONALIZER_RESOURCE_KEY` végpontjának a nevű és `PERSONALIZER_RESOURCE_ENDPOINT`a nevű környezeti változókból. Ha az alkalmazás elindítása után hozta létre a környezeti változókat, a szerkesztőt, az IDE-t vagy a rendszerhéjat futtató rendszernek be kell zárnia és újra kell töltenie a változó eléréséhez. A módszerek a rövid útmutató későbbi részében jönnek létre.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Személyre szabó ügyfél létrehozása

Ezután hozzon létre egy módszert a Personalizer ügyfél visszaadására. A metódus paramétere `PERSONALIZER_RESOURCE_ENDPOINT` a, az ApiKey pedig a `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Élelmiszertermékek beszerezhetése rangsorolható műveletekként

A műveletek azt a tartalomválasztási lehetőséget jelölik, amelyből azt szeretné, hogy a Personalizer válassza ki a legjobb tartalomelemet. Adja hozzá a következő módszereket a Program osztályhoz a műveletek készletének és jellemzőiknek ábrázolására.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Felhasználói beállítások beszereznie a környezethez

Adja hozzá a következő módszereket a Program osztályhoz, hogy a felhasználó a parancssorból megkapja a felhasználó véleményét a napszakra és az aktuális élelmiszer-preferenciaparancsra vonatkozóan. Ezeket a környezet funkciókként fogja használni.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Mindkét módszer `GetKey` a módszerrel olvassa le a felhasználó kiválasztását a parancssorból.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>A tanulási hurok létrehozása

A Personalizer tanulási hurok a [Rang](#request-the-best-action) és [jutalom](#send-a-reward) hívások ciklusa. Ebben a rövid útmutatóban minden rangsorolási hívást a tartalom személyre szabásához jutalomhívás követ, hogy megmondja a Personalizer-nek, hogy milyen jól teljesített a szolgáltatás.

A következő kód hurkok egy ciklusban kérdezi a felhasználónak a preferenciák a parancssorban, elküldi ezt az információt A Personalizer, hogy válassza ki a legjobb műveletet, bemutatja a kiválasztás az ügyfél közül választhat a lista, majd elküldi a jutalom pontszám A személyre szabó jelzi, hogy milyen jól teljesített a szolgáltatás a kiválasztásban.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

A kódfájl futtatása előtt adja hozzá a következő módszereket, amelyek [a tartalommal kapcsolatos választási lehetőségeket kapják:](#get-food-items-as-rankable-actions)

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>A legjobb művelet kérése

A Rangsorolás kérés teljesítéséhez a program megkéri a `currentContent` felhasználó preferenciáit, hogy hozzanak létre egy tartalomválasztási lehetőséget. A folyamat létrehozhat olyan tartalmat, amelyet kizár a műveletekből, és a néven `excludeActions`jelenik meg. A Rang kérés nek szüksége van a műveletek és azok jellemzői, currentContext funkciók, excludeActions, és egy egyedi eseményazonosító, a válasz fogadásához.

Ez a rövid útmutató egyszerű környezetben funkciók a napszak és a felhasználó élelmiszer-preferencia. A termelési rendszerekben [evaluating](../concept-feature-evaluation.md) [az intézkedések és funkciók](../concepts-features.md) meghatározása és értékelése nem triviális kérdés lehet.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Jutalom küldése

A jutalompontszám beküldéséhez a program lekéri a felhasználó kiválasztását a parancssorból, numerikus értéket rendel a kijelöléshez, majd elküldi az egyedi eseményazonosítót és a jutalompontszámot numerikus értékként a Jutalom API-hoz.

Ez a rövid útmutató egy egyszerű számot rendel jutalompontszámként, nullát vagy 1-et. A termelési rendszerekben annak meghatározása, hogy mikor és mit kell küldeni a [Jutalom](../concept-rewards.md) hívás lehet egy nem triviális kérdés, attól függően, hogy az Ön egyedi igényeinek.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>A program futtatása

Futtassa az alkalmazást `run` az alkalmazáskönyvtárból származó dotnet paranccsal.

```console
dotnet run
```

![A rövid útmutató program feltesz néhány kérdést a felhasználói beállítások , más néven szolgáltatások összegyűjtéséhez, majd biztosítja a legjobb műveletet.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

A [rövid útmutató forráskódja](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) a Personalizer minták GitHub-tárházban érhető el.
