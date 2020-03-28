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
ms.openlocfilehash: 631185c20b816191530158fab2b7cd1ed68c3092
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372235"
---
Használja a Language Understanding (LUIS) szerzői ügyfélkódtár python a következőkhöz:

* Hozzon létre egy alkalmazást.
* Szándékok, entitások és példa utterances hozzáadása.
* Adjon hozzá funkciókat, például egy kifejezéslistát.
* Alkalmazás betanítása és közzététele.

[Referenciadokumentációs](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [tár forráskódjának](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [szerzői csomag (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [mintái](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Előfeltételek

* Language Understanding (LUIS) portálfiók: [Hozzon létre egyet ingyen.](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Beállítása

### <a name="get-your-language-understanding-luis-starter-key"></a>A nyelvismertetési (LUIS) indítókulcs beszerezése

A [kezdőkulcs](../luis-how-to-azure-subscription.md#starter-key) beszerezése egy LUIS szerzői erőforrás létrehozásával. Tartsa meg a kulcsot, és a régió a kulcs a következő lépéshez.

### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

A kulcs és a régió használatával hozzon létre két környezeti változót a hitelesítéshez:

* `LUIS_AUTHORING_KEY`- A kérések hitelesítéséhez.
* `LUIS_REGION`- A kulcshoz társított régió. Például: `westus`.

Használja az operációs rendszerre vonatkozó utasításokat.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_REGION <replace-with-your-luis-region>
```

A környezeti változó hozzáadása után indítsa újra a konzolablakot.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[Macos](#tab/unix)

A program `.bash_profile`szerkesztése és a környezeti változó hozzáadása:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
***

### <a name="install-the-python-library-for-luis"></a>A LUIS Python-könyvtárának telepítése

Az alkalmazáskönyvtáron belül telepítse a Language Understanding (LUIS) authoring ügyfélkönyvtárat a python számára a következő paranccsal:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objektummodell

A language understanding (LUIS) szerzői ügyfél egy [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) objektum, amely hitelesíti az Azure-ban, amely tartalmazza a szerzői kulcsot.

Az ügyfél létrehozása után ezzel az ügyféllel érheti el a funkciókat, például a következőket:

* Alkalmazások - [létrehozás,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) [törlés,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-) [közzététel](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Példa kimondott szövegre - [kötegenkénti hozzáadás](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [törlés azonosító szerint](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Jellemzők - [kifejezéslisták](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) kezelése
* Modell – [leképezések](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) és entitások kezelése
* Minta – [minták](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-) kezelése
* Vonat - az alkalmazás [betanítása](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) és a szavazás a [képzési állapothoz](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Verziók](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) - kezelése klónozással, exportálással és törléssel


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan teheti meg a következőket a Language Understanding (LUIS) python ügyfélkódtárjával:

* [Alkalmazás létrehozása](#create-a-luis-app)
* [Entitások hozzáadása](#create-entities-for-the-app)
* [Leképezések hozzáadása](#create-intent-for-the-app)
* [Példa kimondott szövegek hozzáadása](#add-example-utterance-to-intent)
* [Az alkalmazás betanítása](#train-the-app)
* [Az alkalmazás közzététele](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Új python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást a kívánt szerkesztőben vagy IDE-ben. Ezután importálja a következő könyvtárakat.

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának. Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy [CognitiveServicesCredentials objektumot](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) a kulccsal, és használja azt a végpontsegítségével egy [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) objektum létrehozásához.

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>LUIS-alkalmazás létrehozása

1. Hozzon létre egy LUIS-alkalmazást a természetes nyelvi feldolgozási (NLP) modell leképezések, entitások és példa utterances tárolása.

1. Hozzon létre egy [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) objektum [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) metódus létrehozásához az alkalmazást. A név és a nyelvi kultúra kötelező tulajdonságok.

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Szándék létrehozása az alkalmazáshoz
A LUIS-alkalmazás modelljének elsődleges objektuma a szándék. A szándék a felhasználói utterance (kifejezés) szándékok csoportosításához _igazodik._ A felhasználó feltehet egy kérdést, vagy nyilatkozatot tehet, amelyben egy robot (vagy más ügyfélalkalmazás) konkrét _tervezett_ válaszát keresi. Szándékok például a repülőjegy foglalása, a célváros időjárásáról való kérdezés, valamint az ügyfélszolgálat elérhetőségi adatainak megkérdezése.

Használja a [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) metódust az egyedi szándék nevével, majd adja át az alkalmazásazonosítót, a verzióazonosítót és az új szándék nevet.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Entitások létrehozása az alkalmazáshoz

Bár az entitások nem szükségesek, a legtöbb alkalmazásban megtalálhatók. Az entitás kinyeri az információkat a felhasználói utterance (kifejezés), a felhasználó szándékának teljes lebontásához szükséges. Többféle előre [összeállított](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) és egyéni entitások, mindegyik saját adatátalakítási objektum (DTO) modellekkel.  Az alkalmazáshoz hozzáadni a közös előre összeállított entitások közé tartozik a [szám](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinális](../luis-reference-prebuilt-ordinal.md).

Ez **add_entities** módszer `Location` létrehozott egy egyszerű `Class` entitást két `Flight` szerepkörrel, egy egyszerű entitást, egy összetett entitást, és több előre összeállított entitást ad hozzá.

Fontos tudni, hogy az entitások nincsenek megjelölve szándékkal. Ezek, és általában nem vonatkozik a sok szándékot. Csak például a felhasználói utterances vannak megjelölve egy adott, egyetlen szándék.

Az entitások létrehozási módszerei a ModelOperations osztály részét [képezik.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) Minden entitástípus saját adatátalakítási objektum (DTO) modellel rendelkezik.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Példa utterance (kifejezés) hozzáadása a szándékhoz

Az utterance (kifejezés) szándékának meghatározásához és az entitások kinyeréséhez az alkalmazásnak példákat kell hoznia az utterances. A példáknak egy adott, egyetlen leképezést kell megcélozniuk, és meg kell jelölniük az összes egyéni entitást. Az előre összeállított entitásokat nem kell megjelölni.

Példa utterances hozzáadásával egy listát [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) objektumok, egy-egy objektum minden példa utterance. Minden példának meg kell jelölnie az összes entitást egy szótárral az entitásnév és az entitásérték név/érték párjaiból. Az entitás értékének pontosan úgy kell lennie, ahogy a példa utterance (kifejezés) szövegében megjelenik.

Hívja meg a [examples.batch fájlt](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) az alkalmazásazonosítóval, a verzióazonosítóval és a példák listájával. A hívás az eredmények listájával válaszol. Ellenőriznie kell az egyes példák eredményét, hogy megbizonyosodjon arról, hogy sikeresen hozzá lett adva a modellhez.

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]

## <a name="train-the-app"></a>Az alkalmazás betanítása

A modell létrehozása után a LUIS-alkalmazást be kell tanítani a modell ezen verziójához. A betanított modell használható egy [tárolóban,](../luis-container-howto.md)vagy [közzé az](../luis-how-to-publish-app.md) átmeneti vagy termék tárolókban.

A [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) metódusnak szüksége van az alkalmazásazonosítóra és a verzióazonosítóra.

Egy nagyon kis modell, mint például ez a rövid útmutató mutatja, a vonat nagyon gyorsan. Éles szintű alkalmazások esetén az alkalmazás betanításának tartalmaznia kell egy lekérdezési hívást a [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) metódushoz annak meghatározásához, hogy mikor és ha a betanítás sikeres volt. A válasz a [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) objektumok listája, amelyek minden objektumhoz külön állapotban vannak. Minden objektumnak sikeresnek kell lennie ahhoz, hogy a képzés teljesnek minősüljen.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Nyelvismertetési alkalmazás közzététele

Tegye közzé a LUIS alkalmazást az [app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) metódus használatával. Ez közzéteszi az aktuális betanított verziót a végpont megadott tárolóhelyére. Az ügyfélalkalmazás ezt a végpontot használja a szándék és az entitás kinyerésének előrejelzéséhez a felhasználó kimondott szövegének küldéséhez.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `python` alkalmazást a parancssegítségével a rövid útmutató fájlban.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az előrejelzésekkel, a fájl és az alkönyvtárak törlésével tisztítsa meg a munkát ebből a rövid útmutatóból.