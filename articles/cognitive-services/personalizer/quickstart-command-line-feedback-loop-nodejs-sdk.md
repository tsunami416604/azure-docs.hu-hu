---
title: 'Rövid útmutató: személyre szabott ügyféloldali kódtár a Node. js-hez'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el az első lépéseket a Node. js-hez készült ügyféloldali kódtár egy tanulási ciklus használatával.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 01/09/2020
ms.author: diberry
ms.openlocfilehash: bec68cbb88a9acacbc1a9a081ce3d8612b709d18
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860231"
---
# <a name="quickstart-personalizer-client-library-for-nodejs"></a>Rövid útmutató: személyre szabott ügyféloldali kódtár a Node. js-hez

Személyre szabott tartalom megjelenítése ebben a Node. js-útmutatóban a személyre szabási szolgáltatással.

Ismerkedjen meg a következővel: Node. js-hez készült személyre szabott ügyféloldali kódtár. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.

 * A személyre szabási műveletek listájának rangsorolása.
 * A legjobb rangsorolt művelet sikerességét jelző jelentés jutalmazási pontszáma.

[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [minták](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node. js](https://nodejs.org) és a NPM jelenlegi verziója.

## <a name="using-this-quickstart"></a>A rövid útmutató használata


A rövid útmutató használatának számos lépése van:

* A Azure Portal hozzon létre egy személyre szabott erőforrást
* A Azure Portal a személyre szabott erőforráshoz a **konfiguráció** lapon módosítsa a modell frissítési gyakoriságát
* Egy Kódszerkesztő alkalmazásban hozzon létre egy kódot tartalmazó fájlt, és szerkessze a kódot.
* A parancssorban vagy a terminálban telepítse az SDK-t a parancssorból.
* A parancssorban vagy a terminálban futtassa a fájl kódját.


## <a name="create-a-personalizer-azure-resource"></a>Személyre szabott Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást a személyre szabáshoz a [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával a helyi gépen. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services) ingyenes 7 napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Tekintse meg az erőforrást a [Azure Portalon](https://portal.azure.com/).

A próbaverziós előfizetésből vagy erőforrásból származó kulcs lekérése után hozzon létre két [környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY` az erőforrás-kulcshoz.
* `PERSONALIZER_ENDPOINT` az erőforrás-végponthoz.

A Azure Portal mind a kulcs, mind a végpont értéke elérhető a **gyors üzembe helyezési** lapon.


## <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir myapp && cd myapp
```

`package.json` fájl létrehozásához futtassa a `npm init -y` parancsot.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>A Node. js-függvénytár telepítése személyre szabáshoz

Telepítse a (z) Node. js-hez készült személyre szabott ügyféloldali kódtárat a következő paranccsal:

```console
npm install @azure/cognitiveservices-personalizer --save
```

A további NPM-csomagok telepítése ehhez a rövid útmutatóhoz:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="change-the-model-update-frequency"></a>A modell frissítési gyakoriságának módosítása

A Azure Portal a **konfiguráció** lapon a személyre szabott erőforrásban módosítsa a **modell frissítésének gyakoriságát** 10 másodpercre. Ez gyorsan betanítja a szolgáltatást, így láthatja, hogy az egyes iterációk legfelső szintű művelete hogyan változik.

![Modell frissítési gyakoriságának módosítása](./media/settings/configure-model-update-frequency-settings.png)

Ha a rendszer először létrehoz egy személyre szabott hurkot, nem áll rendelkezésre modell, mert nem áll rendelkezésre jutalom API-hívás a betanításhoz. A rangsorban megjelenő hívások az egyes elemek esetében azonos valószínűségeket adnak vissza. Az alkalmazásnak mindig a RewardActionId kimenetével kell rangsorolnia a tartalmat.

## <a name="object-model"></a>Objektummodell

A személyre szabott ügyfél egy PersonalizerClient objektum, amely a kulcsot tartalmazó Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t.

A tartalom rangjának megadásához hozzon létre egy RankRequest, majd továbbítsa azt az ügyfélnek. Rangsor metódusa. A Rank metódus egy RankResponse ad vissza, amely a rangsorolt tartalmat tartalmazza.

A személyre szabott jutalom elküldéséhez hozzon létre egy RewardRequest, majd továbbítsa azt az ügyfélnek. Jutalmazási módszer.

A jutalom meghatározása ebben a rövid útmutatóban triviális. Éles rendszerekben annak meghatározása, hogy milyen hatással van a [jutalom pontszáma](concept-rewards.md) , és hogy mekkora mértékben lehet egy összetett folyamat, dönthet úgy, hogy idővel változhat. Ennek az egyik elsődleges tervezési döntésnek kell lennie a személyre szabott architektúrában.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek megmutatják, hogyan hajthatja végre a következőket a következővel: Node. js-hez készült személyre szabott ügyféloldali kódtár:

* [Személyre szabott ügyfél létrehozása](#create-a-personalizer-client)
* [Rangsor igénylése](#request-a-rank)
* [Jutalom küldése](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Hozzon létre egy új Node. js-alkalmazást az előnyben részesített szerkesztőben vagy `sample.js`nevű IDE.

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

Nyissa meg a **sample. js** fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő `requires` a NPM-csomagok hozzáadásához:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Személyre szabott erőforrás-információk hozzáadása

Hozzon létre változókat az erőforráshoz tartozó Azure-kulcshoz és végponthoz a `PERSONALIZER_KEY` és `PERSONALIZER_ENDPOINT`nevű környezeti változókból. Ha az alkalmazás elindítása után hozta létre a környezeti változókat, akkor a változó eléréséhez be kell zárnia és újra kell töltenie a szerkesztőt, az IDE-t vagy a shellt. A metódusokat később a rövid útmutatóban fogja létrehozni.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Személyre szabott ügyfél létrehozása

Ezután hozzon létre egy metódust, amely egy személyre szabott ügyfelet ad vissza. A metódus paraméterének értéke a `PERSONALIZER_RESOURCE_ENDPOINT`, a ApiKey pedig a `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Tevékenységekként jelölt tartalmak beolvasása

A műveletek a személyre szabáshoz használni kívánt tartalmi beállításokat jelölik. Adja hozzá a következő metódusokat a program osztályhoz, hogy beolvassa a felhasználó bemenetét a parancssorból a nap időpontjában és az aktuális élelmiszer-preferencia alapján.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>A tanulási hurok létrehozása

A személyre szabott tanulási hurok a [Range](#request-a-rank) és a [jutalmazási](#send-a-reward) hívások ciklusa. Ebben a rövid útmutatóban a tartalom személyre szabásához a rangsorban megjelenő minden egyes hívást egy jutalmazási hívás követ, amely azt jelzi, hogy a szolgáltatás milyen jól rangsorolja a tartalmat.

A következő hurkos kód hurkokat mutat egy ciklusban, amellyel a felhasználó a parancssorban megkérdezi a felhasználót, hogy az információt a személyre szabhatja a rangsorban, és megjelenítheti az ügyfélnek a listában kiválasztott kiválasztási lehetőséget, majd elküldheti a jutalmat Személyre szabott jelzés arról, hogy a szolgáltatás milyen mértékben volt rangsorolva a kijelölésben.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Tekintse meg alaposabban az alábbi részekben ismertetett rangot és jutalmazási hívásokat.

Adja hozzá a következő metódusokat, amelyek [megkapják a tartalom választási lehetőségeit](#get-content-choices-represented-as-actions)a kódlap futtatása előtt:

* getActionsList
* getContextFeaturesList

## <a name="request-a-rank"></a>Rangsor igénylése

A rangsorolási kérelem teljesítéséhez a program megkéri a felhasználó beállításait, hogy hozzon létre tartalmakat. A folyamat olyan tartalmat hozhat létre, amely kizárható a rangsorból, `excludeActions`ként jelenik meg. A rangsorolási kérelemnek szüksége van a [műveletekre](concepts-features.md#actions-represent-a-list-of-options), a LicenseManager CurrentContext, a excludeActions és az egyedi rangsorolt esemény-azonosítóra (GUID) a rangsorolt válasz fogadásához.

Ez a rövid útmutató a napszak és a felhasználói élelmiszer-beállítások egyszerű kontextusát tartalmazza. Az éles rendszerekben a [műveletek és szolgáltatások](concepts-features.md) meghatározása és [értékelése](concept-feature-evaluation.md) nem triviális kérdés lehet.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Jutalom küldése

A jutalmazási kérelem elvégzéséhez a program beolvassa a felhasználó kijelölését a parancssorból, hozzárendel egy numerikus értéket az egyes kijelölésekhez, majd elküldi az egyedi rangú esemény AZONOSÍTÓját és a numerikus értéket a jutalmazási metódusnak.

Ez a rövid útmutató egy egyszerű számot rendel hozzá jutalomként, akár nulla, akár 1. Az éles rendszerekben az adott igényektől függően nem triviális kérdés lehet annak meghatározása, hogy mikor és mit kell elküldeni a [jutalmazási](concept-rewards.md) hívásnak.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>A program futtatása

Futtassa az alkalmazást a Node. js-ből az alkalmazás könyvtárából.

```console
node sample.js
```

![A gyors üzembe helyezési program néhány kérdést tesz fel a felhasználói preferenciák (más néven funkciók) összegyűjtésére, majd megadja a legfontosabb műveletet.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

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
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)található.
