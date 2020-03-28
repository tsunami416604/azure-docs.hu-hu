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
ms.openlocfilehash: 786efcb712557da4363384c9d05c33f4f16d6731
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122879"
---
[Referenciadokumentációs](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[könyvtár forráskódjának](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [csomagja (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Samples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node.js](https://nodejs.org) és az NPM jelenlegi verziója.

## <a name="using-this-quickstart"></a>A rövid útmutató használata


A rövid útmutató számos lépésből áll:

* Az Azure Portalon hozzon létre egy personalizer erőforrást
* Az Azure Portalon a Personalizer erőforrás, a **konfiguráció lapon** módosítsa a modell frissítési gyakoriságát egy nagyon rövid időközre
* Kódszerkesztőben hozzon létre egy kódfájlt, és szerkesztse a kódfájlt
* A parancssorba vagy a terminálba telepítse az SDK-t a parancssorból
* A parancssorban vagy a terminálon futtassa a kódfájlt

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt.

```console
mkdir myapp && cd myapp
```

Fájl `npm init -y` létrehozásához futtassa a parancsot. `package.json`

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>A Node.js kódtár telepítése a Personalizer számára

Telepítse a Node.js Personalizer ügyfélkönyvtárát a következő paranccsal:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Telepítse a többi NPM-csomagot ehhez a rövid útmutatóhoz:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Objektummodell

A Personalizer ügyfél egy [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) objektum, amely hitelesíti magát az Azure-ban a Microsoft.Rest.ServiceClientCredentials használatával, amely tartalmazza a kulcsot.

A tartalom egyetlen legjobb elemének kéréséhez hozzon létre egy [RankRequest-et,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)majd adja át az [ügyfélnek. Rang](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) módszer. A Rank metódus RankResponse függvényt ad vissza.

Ha jutalmat szeretne küldeni a Personalizer-nak, hozzon létre egy [RewardRequest-et,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)majd adja át azt az Események osztály [Jutalom](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) metódusának.

A jutalom meghatározása ebben a rövid útmutatóban triviális. A termelési rendszerben annak meghatározása, hogy milyen hatással van a [jutalompontszám,](../concept-rewards.md) és mennyi lehet egy összetett folyamat, hogy ön dönthet úgy, hogy idővel megváltozik. Ez az egyik elsődleges tervezési döntéseket a Personalizer architektúra.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a Node.js Személyre szabása ügyfélkódtárban végezni:

* [Személyre szabó ügyfél létrehozása](#create-a-personalizer-client)
* [Rang API](#request-the-best-action)
* [Jutalom API](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Hozzon létre egy új Node.js alkalmazást `sample.js`a kívánt szerkesztőben vagy IDE nevű.

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

Nyissa meg a **sample.js** fájlt a kívánt szerkesztőben vagy AZ IDE-ben. Az NPM-csomagok hozzáadásához adja hozzá a következőket: `requires`

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Személyre szabászati erőforrásadatok hozzáadása

Hozzon létre változókat az erőforrás Azure-kulcsához és végpontjából a nevű `PERSONALIZER_KEY` és `PERSONALIZER_ENDPOINT`a környezeti változókból lekért végponthoz. Ha az alkalmazás elindítása után hozta létre a környezeti változókat, a szerkesztőt, az IDE-t vagy a rendszerhéjat futtató rendszernek be kell zárnia és újra kell töltenie a változó eléréséhez. A módszerek a rövid útmutató későbbi részében jönnek létre.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Személyre szabó ügyfél létrehozása

Ezután hozzon létre egy módszert a Personalizer ügyfél visszaadására. A metódus paramétere `PERSONALIZER_RESOURCE_ENDPOINT` a, az ApiKey pedig a `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>A tartalomválasztási lehetőségek műveletként való ábrázolása

A műveletek azt a tartalomválasztási lehetőséget jelölik, amelyből azt szeretné, hogy a Personalizer válassza ki a legjobb tartalomelemet. Adja hozzá a következő módszereket a Program osztályhoz a műveletek készletének és jellemzőiknek ábrázolására.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>A tanulási hurok létrehozása

A Personalizer tanulási hurok a [Rang](#request-the-best-action) és [jutalom](#send-a-reward) hívások ciklusa. Ebben a rövid útmutatóban minden rangsorolási hívást a tartalom személyre szabásához jutalomhívás követ, hogy megmondja a Personalizer-nek, hogy milyen jól teljesített a szolgáltatás.

A következő kód egy cikluson keresztül halad, amikor megkérdezi a felhasználótól a preferenciáikat a parancssorban, elküldi ezt az információt a Personalizer-nek, hogy válassza ki a legjobb műveletet, bemutatva a választást az ügyfélnek, hogy válasszon a lista közül, majd jutalmat küld jön a A személyre szabó jelzi, hogy milyen jól teljesített a szolgáltatás a kiválasztásban.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

A következő szakaszokban közelebbről is megnézheted a rang- és jutalomhívásokat.

A kódfájl futtatása előtt adja hozzá a következő módszereket, amelyek [a tartalommal kapcsolatos választási lehetőségeket kapják:](#get-content-choices-represented-as-actions)

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>A legjobb művelet kérése

A Rangsorolás kérés teljesítéséhez a program megkéri a felhasználó beállításait, hogy hozzanak létre tartalomválasztási lehetőségeket. A folyamat létrehozhat olyan tartalmat, amelyet kizár a műveletekből, és a néven `excludeActions`jelenik meg. A Rangsorolási kérelemnek szüksége van a [műveletekre](../concepts-features.md#actions-represent-a-list-of-options) és azok jellemzőire, a currentContext funkciókra, a kizárási műveletekre és egy egyedi rangesemény-azonosítóra a rangsorolt válasz fogadásához.

Ez a rövid útmutató egyszerű környezetben funkciók a napszak és a felhasználó élelmiszer-preferencia. A termelési rendszerekben [evaluating](../concept-feature-evaluation.md) [az intézkedések és funkciók](../concepts-features.md) meghatározása és értékelése nem triviális kérdés lehet.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Jutalom küldése


A jutalompontszám beküldéséhez a program lekéri a felhasználó kiválasztását a parancssorból, numerikus értéket rendel a kijelöléshez, majd elküldi az egyedi eseményazonosítót és a jutalompontszámot numerikus értékként a Jutalom API-hoz.

Ez a rövid útmutató egy egyszerű számot rendel jutalompontszámként, nullát vagy 1-et. A termelési rendszerekben annak meghatározása, hogy mikor és mit kell küldeni a [Jutalom](../concept-rewards.md) hívás lehet egy nem triviális kérdés, attól függően, hogy az Ön egyedi igényeinek.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>A program futtatása

Futtassa az alkalmazást az alkalmazáskönyvtárnok.js fájljával.

```console
node sample.js
```

![A rövid útmutató program feltesz néhány kérdést a felhasználói beállítások , más néven szolgáltatások összegyűjtéséhez, majd biztosítja a legjobb műveletet.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
