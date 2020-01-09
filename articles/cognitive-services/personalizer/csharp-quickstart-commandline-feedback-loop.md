---
title: 'Gyors útmutató: személyre szabott ügyféloldali kódtár a .NET-hez'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el az első lépéseket a .NET-hez készült ügyféloldali kódtárat egy tanulási ciklus használatával.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: c17bf54d89e3a98ca667eeba40f2d2b166550833
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446391"
---
# <a name="quickstart-personalizer-client-library-for-net"></a>Gyors útmutató: személyre szabott ügyféloldali kódtár a .NET-hez

Személyre szabott tartalom megjelenítése C# ebben a rövid útmutatóban a személyre szabási szolgáltatással.

Ismerkedés a .NET-hez készült személyre szabott ügyféloldali kódtár használatával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.

 * A személyre szabási műveletek listájának rangsorolása.
 * A legjobb rangsorolt művelet sikerességét jelző jelentés jutalmazási pontszáma.

[Dokumentáció](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [minták](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.

## <a name="using-this-quickstart"></a>A rövid útmutató használata

A rövid útmutató használatának számos lépése van:

* A Azure Portal hozzon létre egy személyre szabott erőforrást
* A Azure Portal a személyre szabott erőforráshoz a **konfiguráció** lapon módosítsa a modell frissítési gyakoriságát
* Egy Kódszerkesztő alkalmazásban hozzon létre egy kódot tartalmazó fájlt, és szerkessze a kódot.
* A parancssorban vagy a terminálban telepítse az SDK-t a parancssorból.
* A parancssorban vagy a terminálban futtassa a fájl kódját.

## <a name="create-a-personalizer-azure-resource"></a>Személyre szabott Azure-erőforrás létrehozása

Hozzon létre egy erőforrást a személyre szabáshoz a [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával a helyi gépen. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services) ingyenes 7 napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Tekintse meg az erőforrást a [Azure Portalon](https://portal.azure.com/).

A próbaverziós előfizetésből vagy erőforrásból származó kulcs lekérése után hozzon létre két [környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY` az erőforrás-kulcshoz.
* `PERSONALIZER_RESOURCE_ENDPOINT` az erőforrás-végponthoz.

A Azure Portalban a kulcs és a végpont értéke egyaránt elérhető a rövid útmutató **lapon.**

## <a name="change-the-model-update-frequency"></a>A modell frissítési gyakoriságának módosítása

A Azure Portal a **konfiguráció** lapon a személyre szabott erőforrásban módosítsa a **modell frissítésének gyakoriságát** 10 másodpercre. Ez a rövid időtartam gyorsan betanítja a szolgáltatást, így megtekintheti, hogyan változik az egyes iterációk legfelső szintű művelete.

![Modell frissítési gyakoriságának módosítása](./media/settings/configure-model-update-frequency-settings.png)

Ha a rendszer először létrehoz egy személyre szabott hurkot, nem áll rendelkezésre modell, mert nem áll rendelkezésre jutalom API-hívás a betanításhoz. A rangsorban megjelenő hívások az egyes elemek esetében azonos valószínűségeket adnak vissza. Az alkalmazásnak mindig a RewardActionId kimenetével kell rangsorolnia a tartalmat.

## <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. 

A konzol ablakban (például cmd, PowerShell vagy bash) a DotNet `new` paranccsal hozzon létre egy új, `personalizer-quickstart`nevű Console-alkalmazást. Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C# projektet hoz létre egyetlen forrásfájlban: `Program.cs`. 

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

A tartalom rangjának megadásához hozzon létre egy [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview), majd továbbítsa azt az [ügyfélnek. Rangsor](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) metódusa. A Rank metódus egy RankResponse ad vissza, amely a rangsorolt tartalmat tartalmazza. 

A személyre szabott jutalom elküldéséhez hozzon létre egy [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview), majd továbbítsa azt az [ügyfélnek. Jutalmazási](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) módszer. 

A jutalom meghatározása ebben a rövid útmutatóban triviális. Éles rendszerekben annak meghatározása, hogy milyen hatással van a [jutalom pontszáma](concept-rewards.md) , és hogy mekkora mértékben lehet egy összetett folyamat, dönthet úgy, hogy idővel változhat. Ennek a tervezési döntésnek a személyre szabott architektúrájának egyik elsődleges döntésének kell lennie. 

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a személyre szabott ügyféloldali kódtáraval a .NET-hez:

* [Személyre szabott ügyfél létrehozása](#create-a-personalizer-client)
* [Rangsor igénylése](#request-a-rank)
* [Jutalom küldése](#send-a-reward)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a **program.cs** fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Cserélje le a meglévő `using` kódot a következő `using` irányelvekre:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Személyre szabott erőforrás-információk hozzáadása

A **program** osztályban hozzon létre változókat az erőforrás Azure-kulcsához, és a végpontot a `PERSONALIZER_RESOURCE_KEY` és `PERSONALIZER_RESOURCE_ENDPOINT`nevű környezeti változókból kihúzta. Ha az alkalmazás elindítása után hozta létre a környezeti változókat, akkor a változó eléréséhez be kell zárnia és újra kell töltenie a szerkesztőt, az IDE-t vagy a shellt. A metódusokat később a rövid útmutatóban fogja létrehozni.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Személyre szabott ügyfél létrehozása

Ezután hozzon létre egy metódust, amely egy személyre szabott ügyfelet ad vissza. A metódus paraméterének értéke a `PERSONALIZER_RESOURCE_ENDPOINT`, a ApiKey pedig a `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Élelmiszeripari elemek beolvasása sorba besorolt műveletként

A műveletek a személyre szabáshoz használni kívánt tartalmi beállításokat jelölik. Adja hozzá a következő metódusokat a program osztályhoz, hogy a rangsorolni kívánt műveletek halmazát képviseljék.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>A környezet felhasználói beállításainak beolvasása

Adja hozzá a következő metódusokat a program osztályhoz, hogy beolvassa a felhasználó bemenetét a parancssorból a nap időpontjában és az aktuális élelmiszer-preferencia alapján. Ezek a környezetek a műveletek rangsorolásakor lesznek felhasználva.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Mindkét módszer a `GetKey` metódus használatával olvassa be a felhasználó kijelölését a parancssorból. 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>A tanulási hurok létrehozása

A személyre szabott tanulási hurok a Range és a jutalmazási hívások ciklusa. Ebben a rövid útmutatóban a tartalom személyre szabásához a rangsorban megjelenő minden egyes hívást egy jutalmazási hívás követ, amely azt jelzi, hogy a szolgáltatás milyen jól rangsorolja a tartalmat. 

A program `main` metódusában a következő kód hurkot mutat be, amely arra kéri a felhasználót, hogy adja meg a beállításait a parancssorban, és küldje el az információt a személyre szabásra, amely bemutatja az ügyfélnek a listában kiválasztott kiválasztási lehetőséget.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Adja hozzá a következő metódusokat, amelyek [megkapják a tartalom választási lehetőségeit](#get-food-items-as-rankable-actions)a kódlap futtatása előtt:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-a-rank"></a>Rangsor igénylése

A rangsorolási kérelem elvégzéséhez a program megkéri a felhasználó beállításait, hogy hozzon létre egy `currentContent` a tartalom megválasztásához. A folyamat olyan tartalmat hozhat létre, amely kizárható a rangsorból, `excludeActions`ként jelenik meg. A rangsorolási kérelemnek szüksége van a műveletekre, a LicenseManager CurrentContext, a excludeActions és az egyedi rangsorolt esemény-AZONOSÍTÓra (GUID) a rangsorolt válasz fogadásához. 

Ez a rövid útmutató a napszak és a felhasználói élelmiszer-beállítások egyszerű kontextusát tartalmazza. Az éles rendszerekben a [műveletek és szolgáltatások](concepts-features.md) meghatározása és [értékelése](concept-feature-evaluation.md) nem triviális kérdés lehet.  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Jutalom küldése

A jutalmazási kérelem elvégzéséhez a program beolvassa a felhasználó kijelölését a parancssorból, hozzárendel egy numerikus értéket az egyes kijelölésekhez, majd elküldi az egyedi rangú esemény AZONOSÍTÓját és a numerikus értéket a jutalmazási metódusnak.

Ez a rövid útmutató egy egyszerű számot rendel hozzá jutalomként, akár nulla, akár 1. Az éles rendszerekben az adott igényektől függően nem triviális kérdés lehet annak meghatározása, hogy mikor és mit kell elküldeni a [jutalmazási](concept-rewards.md) hívásnak. 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>A program futtatása

Futtassa az alkalmazást a DotNet `run` paranccsal az alkalmazás könyvtárából.

```console
dotnet run
```

![A gyors üzembe helyezési program néhány kérdést tesz fel a felhasználói preferenciák (más néven funkciók) összegyűjtésére, majd megadja a legfontosabb műveletet.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

A rövid útmutató [forráskódja](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) elérhető a személyre szabott minták GitHub-adattárában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[A megszemélyesítő működése](how-personalizer-works.md)

* [Mi az a személyre szabott?](what-is-personalizer.md)
* [Hol használható a személyre szabás?](where-can-you-use-personalizer.md)
* [hibaelhárítással](troubleshooting.md)
