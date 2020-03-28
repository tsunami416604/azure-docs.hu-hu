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
ms.openlocfilehash: 070de0f65e890c38acd5075286b349e95cd19f3b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372098"
---
Használja a Nyelv-megértés (LUIS) szerzői ügyfélkódtár a Node.js a következőkhöz:

* Hozzon létre egy alkalmazást.
* Szándékok, entitások és példa utterances hozzáadása.
* Adjon hozzá funkciókat, például egy kifejezéslistát.
* Alkalmazás betanítása és közzététele.
* Alkalmazás törlése

[Dokumentációs](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [referenciatár forráskódjának](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | szerzői[csomagja (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), [futásidejű csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Előfeltételek

* Nyelvtudás szerzői erőforrás: [Hozzon létre egyet az Azure Portalon](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Beállítása

### <a name="get-your-language-understanding-luis-starter-key"></a>A nyelvismertetési (LUIS) indítókulcs beszerezése

A [kezdőkulcs](../luis-how-to-azure-subscription.md#starter-key) beszerezése egy LUIS szerzői erőforrás létrehozásával. Tartsa meg a kulcsot, és a végpont a kulcs a következő lépéshez.

### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

A kulcs és a régió használatával hozzon létre két környezeti változót a hitelesítéshez:

* `LUIS_AUTHORING_KEY`- A kérések hitelesítéséhez.
* `LUIS_AUTHORING_ENDPOINT`- A kulcshoz kapcsolódó végpont.

Használja az operációs rendszerre vonatkozó utasításokat.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

A környezeti változó hozzáadása után indítsa újra a konzolablakot.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[Macos](#tab/unix)

A program `.bash_profile`szerkesztése és a környezeti változó hozzáadása:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Az NPM-kódtár telepítése a LUIS-szerzői hez

Az alkalmazáskönyvtáron belül telepítse a függőségeket a következő paranccsal:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Objektummodell

A language understanding (LUIS) szerzői ügyfél egy [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) objektum, amely hitelesíti az Azure-ban, amely tartalmazza a szerzői kulcsot.

Az ügyfél létrehozása után ezzel az ügyféllel érheti el a funkciókat, például a következőket:

* Alkalmazások - [hozzáadás,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-) [törlés](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [közzététel](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Példa kimondott szövegre - [kötegenkénti hozzáadás](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [törlés azonosító szerint](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Jellemzők - [kifejezéslisták](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-) kezelése
* Modell – [leképezések](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) és entitások kezelése
* Minta – [minták](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-) kezelése
* Vonat - az alkalmazás [betanítása](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) és a szavazás a [képzési állapothoz](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Verziók](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - kezelése klónozással, exportálással és törléssel


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan teheti meg a következőket a Nyelvközt ismerés (LUIS) készítő ügyfélkódtára node.js:

* [Alkalmazás létrehozása](#create-a-luis-app)
* [Entitások hozzáadása](#create-entities-for-the-app)
* [Leképezések hozzáadása](#create-intent-for-the-app)
* [Példa kimondott szövegek hozzáadása](#add-example-utterance-to-intent)
* [Az alkalmazás betanítása](#train-the-app)
* [Az alkalmazás közzététele](#publish-a-language-understanding-app)
* [Az alkalmazás törlése](#delete-a-language-understanding-app)
* [Alkalmazások listázása](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Hozzon létre egy új szövegfájlt `luis_authoring_quickstart.js`a kívánt szerkesztőben vagy ide nevű . Ezután adja hozzá a következő függőségeket.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

Hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának. Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy [CognitiveServicesCredentials objektumot](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) a kulccsal, és használja azt a végpontsegítségével egy [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) objektum létrehozásához.

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUIS-alkalmazás létrehozása

1. Hozzon létre egy LUIS-alkalmazást a természetes nyelvi feldolgozási (NLP) modell leképezések, entitások és példa utterances tárolása.

1. Hozzon létre egy [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) objektum [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) metódus létrehozásához az alkalmazást. A név és a nyelvi kultúra kötelező tulajdonságok.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Szándék létrehozása az alkalmazáshoz
A LUIS-alkalmazás modelljének elsődleges objektuma a szándék. A szándék a felhasználói utterance (kifejezés) szándékok csoportosításához _igazodik._ A felhasználó feltehet egy kérdést, vagy nyilatkozatot tehet, amelyben egy robot (vagy más ügyfélalkalmazás) konkrét _tervezett_ válaszát keresi. Szándékok például a repülőjegy foglalása, a célváros időjárásáról való kérdezés, valamint az ügyfélszolgálat elérhetőségi adatainak megkérdezése.

Használja a [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) metódust az egyedi szándék nevével, majd adja át az alkalmazásazonosítót, a verzióazonosítót és az új szándék nevet.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Entitások létrehozása az alkalmazáshoz

Bár az entitások nem szükségesek, a legtöbb alkalmazásban megtalálhatók. Az entitás kinyeri az információkat a felhasználói utterance (kifejezés), a felhasználó szándékának teljes lebontásához szükséges. Többféle előre [összeállított](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) és egyéni entitások, mindegyik saját adatátalakítási objektum (DTO) modellekkel.  Az alkalmazáshoz hozzáadni a közös előre összeállított entitások közé tartozik a [szám](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinális](../luis-reference-prebuilt-ordinal.md).

Ez **add_entities** módszer `Location` létrehozott egy egyszerű `Class` entitást két `Flight` szerepkörrel, egy egyszerű entitást, egy összetett entitást, és több előre összeállított entitást ad hozzá.

Fontos tudni, hogy az entitások nincsenek megjelölve szándékkal. Ezek, és általában nem vonatkozik a sok szándékot. Csak például a felhasználói utterances vannak megjelölve egy adott, egyetlen szándék.

Az entitások létrehozási módszerei a [Modell](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) osztály részét képezik. Minden entitástípus saját adatátalakítási objektum (DTO) modellel rendelkezik.

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Példa utterance (kifejezés) hozzáadása a szándékhoz

Az utterance (kifejezés) szándékának meghatározásához és az entitások kinyeréséhez az alkalmazásnak példákat kell hoznia az utterances. A példáknak egy adott, egyetlen leképezést kell megcélozniuk, és meg kell jelölniük az összes egyéni entitást. Az előre összeállított entitásokat nem kell megjelölni.

Példa utterances hozzáadásával egy listát [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) objektumok, egy-egy objektum minden példa utterance. Minden példának meg kell jelölnie az összes entitást egy szótárral az entitásnév és az entitásérték név/érték párjaiból. Az entitás értékének pontosan úgy kell lennie, ahogy a példa utterance (kifejezés) szövegében megjelenik.

Hívja meg a [examples.batch fájlt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) az alkalmazásazonosítóval, a verzióazonosítóval és a példák listájával. A hívás az eredmények listájával válaszol. Ellenőriznie kell az egyes példák eredményét, hogy megbizonyosodjon arról, hogy sikeresen hozzá lett adva a modellhez.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Az alkalmazás betanítása

A modell létrehozása után a LUIS-alkalmazást be kell tanítani a modell ezen verziójához. A betanított modell használható egy [tárolóban,](../luis-container-howto.md)vagy [közzé az](../luis-how-to-publish-app.md) átmeneti vagy termék tárolókban.

A [train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) metódusnak szüksége van az alkalmazásazonosítóra és a verzióazonosítóra.

Egy nagyon kis modell, mint például ez a rövid útmutató mutatja, a vonat nagyon gyorsan. Éles szintű alkalmazások esetén az alkalmazás betanításának tartalmaznia kell egy lekérdezési hívást a [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) metódushoz annak meghatározásához, hogy mikor és ha a betanítás sikeres volt. A válasz a [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) objektumok listája, amelyek minden objektumhoz külön állapotban vannak. Minden objektumnak sikeresnek kell lennie ahhoz, hogy a képzés teljesnek minősüljen.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Az összes modell betanítása időbe telik. Az OperationResult segítségével ellenőrizze a betanítási állapotot.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Nyelvismertetési alkalmazás közzététele

Tegye közzé a LUIS alkalmazást az [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) metódus használatával. Ez közzéteszi az aktuális betanított verziót a végpont megadott tárolóhelyére. Az ügyfélalkalmazás ezt a végpontot használja a szándék és az entitás kinyerésének előrejelzéséhez a felhasználó kimondott szövegének küldéséhez.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Nyelvismertetési alkalmazás törlése

Törölje a LUIS alkalmazást az [app.deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) metódussal. Ezzel törli az aktuális alkalmazást.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Nyelvismertetési alkalmazások listázása

A Nyelvismeret kulcshoz társított alkalmazások listájának beírása

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `node luis_authoring_quickstart.js` alkalmazást a parancssegítségével a rövid útmutató fájlban.

```console
node luis_authoring_quickstart.js
```

Az alkalmazás parancssori kimenete:

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
