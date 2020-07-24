---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.openlocfilehash: ddf16334830b64c57e9d09a75dfc80fc319a9ccd
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133841"
---
[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)  | [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node.js](https://nodejs.org) és a NPM jelenlegi verziója.

## <a name="using-this-quickstart"></a>A rövid útmutató használata


A rövid útmutató használatának számos lépése van:

* A Azure Portal hozzon létre egy személyre szabott erőforrást
* A Azure Portal a személyre szabott erőforráshoz a **konfiguráció** lapon módosítsa a modell frissítésének gyakoriságát nagyon rövid időtartamra.
* Egy Kódszerkesztő alkalmazásban hozzon létre egy kódot tartalmazó fájlt, és szerkessze a kódot.
* A parancssorban vagy a terminálban telepítse az SDK-t a parancssorból.
* A parancssorban vagy a terminálban futtassa a fájl kódját.

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir myapp && cd myapp
```

`npm init -y`Fájl létrehozásához futtassa a parancsot `package.json` .

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>A Node.js könyvtárának telepítése személyre szabáshoz

Telepítse a Node.js személyre szabott ügyféloldali kódtárat a következő paranccsal:

```console
npm install @azure/cognitiveservices-personalizer --save
```

A további NPM-csomagok telepítése ehhez a rövid útmutatóhoz:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Objektummodell

A személyre szabott ügyfél egy [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) objektum, amely a kulcsot tartalmazó Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t.

Ha szeretné megkérdezni a tartalom egyetlen legjobb elemét, hozzon létre egy [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest), majd továbbítsa azt az [ügyfélnek. Rangsor](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) metódusa. A Rank metódus egy RankResponse ad vissza.

Ha szeretné elküldeni a személyre szabott jutalmat, hozzon létre egy [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest), majd továbbítsa azt az Events osztály [jutalmazási](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) metódusának.

A jutalom meghatározása ebben a rövid útmutatóban triviális. Éles rendszerekben annak meghatározása, hogy milyen hatással van a [jutalom pontszáma](../concept-rewards.md) , és hogy mekkora mértékben lehet egy összetett folyamat, dönthet úgy, hogy idővel változhat. Ennek az egyik elsődleges tervezési döntésnek kell lennie a személyre szabott architektúrában.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Node.js személyre szabott ügyféloldali kódtár használatával:

* [Személyre szabott ügyfél létrehozása](#create-a-personalizer-client)
* [Rangsor API](#request-the-best-action)
* [Jutalom API](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Hozzon létre egy új Node.js alkalmazást az előnyben részesített szerkesztőben vagy a nevű IDE-ben `sample.js` .

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

Nyissa meg a **sample.js** fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következőt a `requires` NPM-csomagok hozzáadásához:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Személyre szabott erőforrás-információk hozzáadása

Hozzon létre változókat az erőforráshoz tartozó Azure-kulcshoz és végponthoz a (z) és a (z) nevű környezeti változók alapján `PERSONALIZER_KEY` `PERSONALIZER_ENDPOINT` . Ha az alkalmazás elindítása után hozta létre a környezeti változókat, akkor a változó eléréséhez be kell zárnia és újra kell töltenie a szerkesztőt, az IDE-t vagy a shellt. A metódusokat később a rövid útmutatóban fogja létrehozni.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Személyre szabott ügyfél létrehozása

Ezután hozzon létre egy metódust, amely egy személyre szabott ügyfelet ad vissza. A metódus paramétere a `PERSONALIZER_RESOURCE_ENDPOINT` és a ApiKey `PERSONALIZER_RESOURCE_KEY` .

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Tevékenységekként jelölt tartalmak beolvasása

A műveletek azokat a tartalmi beállításokat jelentik, amelyeknek a személyre szabásával ki kell választania a legjobb tartalmi elemet. Adja hozzá a következő metódusokat a program osztályhoz a műveletek és a hozzájuk tartozó funkciók megjelenítéséhez.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>A tanulási hurok létrehozása

A személyre szabott tanulási hurok a [Range](#request-the-best-action) és a [jutalmazási](#send-a-reward) hívások ciklusa. Ebben a rövid útmutatóban a tartalom személyre szabásához a rangsorban megjelenő minden egyes hívást egy jutalmazási hívás követ, amely alapján eldöntheti, hogy milyen jól teljesíti a szolgáltatást.

A következő kód hurkokat mutat be a felhasználónak a parancssorban való megadására, az információknak a személyre szabására való kiválasztásához, az ügyfélnek a listából való kiválasztásához, majd a személyre szabási jelzés elküldéséhez, hogy a szolgáltatás milyen jól van kiválasztva.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Tekintse meg alaposabban az alábbi részekben ismertetett rangot és jutalmazási hívásokat.

Adja hozzá a következő metódusokat, amelyek [megkapják a tartalom választási lehetőségeit](#get-content-choices-represented-as-actions)a kódlap futtatása előtt:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>A legjobb művelet kérése

A rangsorolási kérelem teljesítéséhez a program megkéri a felhasználó beállításait, hogy hozzon létre tartalmakat. A folyamat létrehozhat olyan tartalmat, amely kizárható a műveletekből, a következő módon: `excludeActions` . A rangsorolási kérelemnek szüksége van a [műveletekre](../concepts-features.md#actions-represent-a-list-of-options) és azok szolgáltatásaira, a LicenseManager CurrentContext szolgáltatásaira, a excludeActions és egy egyedi rangú esemény-azonosítóra, hogy megkapja a rangsorolt választ.

Ez a rövid útmutató a napszak és a felhasználói élelmiszer-beállítások egyszerű kontextusát tartalmazza. Az éles rendszerekben a [műveletek és szolgáltatások](../concepts-features.md) meghatározása és [értékelése](../concept-feature-evaluation.md) nem triviális kérdés lehet.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Jutalom küldése


Ahhoz, hogy a jutalom pontszáma a jutalom iránti kérelemben legyen elküldve, a program beolvassa a felhasználó kijelölését a parancssorból, hozzárendel egy numerikus értéket a kijelöléshez, majd elküldi az egyedi eseményazonosító és a jutalom pontszámát a jutalmazási API-nak megfelelő numerikus értékként.

Ez a rövid útmutató egy egyszerű számot rendel hozzá a jutalom pontszámához, vagy nulla vagy 1 értéket. Az éles rendszerekben az adott igényektől függően nem triviális kérdés lehet annak meghatározása, hogy mikor és mit kell elküldeni a [jutalmazási](../concept-rewards.md) hívásnak.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>A program futtatása

Futtassa az alkalmazást a Node.js az alkalmazás könyvtárából.

```console
node sample.js
```

![A gyors üzembe helyezési program néhány kérdést tesz fel a felhasználói preferenciák (más néven funkciók) összegyűjtésére, majd megadja a legfontosabb műveletet.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
