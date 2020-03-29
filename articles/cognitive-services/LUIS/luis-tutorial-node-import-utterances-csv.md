---
title: Kimondott szöveg ek importálása a Node.js használatával – LUIS
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy LUIS-alkalmazást programozott módon a már meglévő adatok CSV formátumban a LUIS Authoring API használatával.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ef5f6967b7ad9500672d00d93dd8acaca99e5948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499458"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Luis-alkalmazás létrehozása programozott módon a Node.js használatával

A LUIS egy programozott API-t biztosít, amely mindent megtesz, amit a [LUIS-webhely](luis-reference-regions.md) tesz. Ez időt takaríthat meg, ha már meglévő adatokkal rendelkezik, és gyorsabb lenne a LUIS-alkalmazások programozott módon történő létrehozása, mint az információk kézzel történő bevitele. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Előfeltételek

* Jelentkezzen be a [LUIS](luis-reference-regions.md) webhelyre, és keresse meg a [szerzői kulcsot](luis-concept-keys.md#authoring-key) a Fiókbeállítások ban. Ezzel a kulccsal hívja meg a authoring API-kat.
* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
* Ez a cikk egy CSV-vel kezdődik egy hipotetikus vállalat felhasználói kérelmek naplófájljaihoz. Töltse le [itt](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Telepítse a legújabb Node.js-t az NPM-mel. Töltse le [innen](https://nodejs.org/en/download/).
* **[Ajánlott]** Visual Studio Kód részére IntelliSense és hibakeresés, letölt ez -ból [itt](https://code.visualstudio.com/) részére szabad.

A cikkben szereplő összes kód elérhető az [Azure-Minták nyelvének megértése GitHub-tárházban.](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv) 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Már meglévő adatok hozzárendelése leképezésekhez és entitásokhoz
Még akkor is, ha egy olyan rendszer, amely nem jött létre a LUIS szem előtt tartva, ha olyan szöveges adatokat tartalmaz, amelyek leképezi a különböző dolgokat, amelyeket a felhasználók szeretnének csinálni, előfordulhat, hogy képes lehet egy leképezést a meglévő felhasználói beviteli kategóriáka luis leképezések. Ha fontos szavakat vagy kifejezéseket tud azonosítani a felhasználók által elmondottakban, ezek a szavak entitásokhoz képezhetik le őket.

Nyissa [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) meg a fájlt. Ez tartalmaz egy naplót a felhasználói lekérdezések egy hipotetikus otthoni automatizálási szolgáltatás, beleértve, hogyan voltak kategorizálva, amit a felhasználó azt mondta, és néhány oszlop hasznos információkat húzta ki őket. 

![Már meglévő adatok CSV-fájlja](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Láthatja, hogy a **RequestType** oszlop lehet leképezések, és a **kérelem** oszlop egy példa utterance (kifejezés). A többi mező lehet entitások, ha azok az utterance (kifejezés) fordulnak elő. Mert vannak szándékok, entitások és példa utterances, egy egyszerű, mintaalkalmazás követelményeivel rendelkezik.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>A LUIS-alkalmazás nem LUIS-adatokból történő létrehozásának lépései
Új LUIS-alkalmazás létrehozása a CSV-fájlból:

* Elemezd a CSV-fájlból származó adatokat:
    * Konvertálja a LUIS-ra a Szerzői API használatával feltölthető formátumra. 
    * Az elemzött adatokból információkat gyűjthet a szándékokról és az entitásokról. 
* Szerzői API-hívások kezdeményezése:
    * Hozza létre az alkalmazást.
    * Adja hozzá az elemzött adatokból összegyűjtött szándékokat és entitásokat. 
    * Miután létrehozta a LUIS alkalmazást, hozzáadhatja a példa utterances az elemzési adatokból. 

Ez a programfolyamat a `index.js` fájl utolsó részében látható. Másolja vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) ezt `index.js`a kódot, és mentse a .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>A CSV-fájl elemzés

A CSV-ben a kimondott szövegeket tartalmazó oszlopbejegyzéseket olyan JSON formátumba kell elemezni, amelyet a LUIS megért. Ennek a JSON-formátumnak tartalmaznia kell egy mezőt, `intentName` amely azonosítja az utterance (kifejezés) szándékát. Tartalmaznia kell `entityLabels` egy mezőt is, amely üres lehet, ha nincsenek entitások az utterance (kifejezés) között. 

Például a "Fények bekapcsolása" bejegyzés erre a JSON-ra van leképezve:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

Ebben a példában a `intentName` származik a felhasználói kérelem a **kérelem** oszlop `entityName` fejlécében a CSV-fájlban, és a származik a többi oszlop kulcsfontosságú információkat. Ha például van egy bejegyzés **a Művelet** vagy az **Eszköz**, és ez a karakterlánc is előfordul a tényleges kérelemben, akkor entitásként címkézhető. A következő kód bemutatja ezt az elemzési folyamatot. Másolhatja vagy [letöltheti,](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) `_parse.js`és mentheti a programba.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>A LUIS alkalmazás létrehozása
Miután az adatok at elemezték a JSON-ba, adja hozzá egy LUIS-alkalmazáshoz. A következő kód létrehozza a LUIS alkalmazást. Másolja vagy [töltse le,](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) `_create.js`és mentse a alkalmazásba.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Leképezések hozzáadása
Ha van egy alkalmazás, meg kell, hogy szándékot. A következő kód létrehozza a LUIS alkalmazást. Másolja vagy [töltse le,](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) `_intents.js`és mentse a alkalmazásba.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Entitások hozzáadása
A következő kód hozzáadja az entitásokat a LUIS alkalmazáshoz. Másolja vagy [töltse le,](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) `_entities.js`és mentse a alkalmazásba.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Beszédmódok hozzáadása
Miután az entitások és a leképezések definiálva a LUIS-alkalmazásban, hozzáadhatja a kimondott szöveg. A következő kód a [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API-t használja, amely lehetővé teszi, hogy egyszerre legfeljebb 100 utterances összeadást adjon hozzá.  Másolja vagy [töltse le,](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) `_upload.js`és mentse a alkalmazásba.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>A kód futtatása


### <a name="install-nodejs-dependencies"></a>Node.js függőségek telepítése
Telepítse az NPM Node.js függőségeit a terminál/parancssorba.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Konfigurációs beállítások módosítása
Az alkalmazás használatához módosítania kell az index.js fájl értékeit a saját végpontkulcsára, és meg kell adnia az alkalmazás által kívánt nevet. Beállíthatja az alkalmazás kulturális környezetét is, vagy módosíthatja a verziószámot.

Nyissa meg az index.js fájlt, és módosítsa ezeket az értékeket a fájl tetején.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>A szkript futtatása
Futtassa a parancsfájlt terminálról/parancssorból a Node.js paranccsal.

```console
> node index.js
```

vagy

```console
> npm start
```

### <a name="application-progress"></a>Alkalmazás folyamata
Az alkalmazás futása közben a parancssor mutatja a haladást. A parancssori kimenet tartalmazza a LUIS válaszainak formátumát.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>A LUIS alkalmazás megnyitása
Miután a parancsfájl befejeződött, bejelentkezhet a [LUIS-ba,](luis-reference-regions.md) és megtekintheti a Saját **alkalmazások**csoportban létrehozott LUIS-alkalmazást. A **TurnOn**, **TurnOff**és **None** szándékok alatt hozzáadott kimondott szövegeket látnia kell.

![TurnOn szándék](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az alkalmazás tesztelése és betanítása a LUIS webhelyén](luis-interactive-test.md)

## <a name="additional-resources"></a>További források

Ez a mintaalkalmazás a következő LUIS API-kat használja:
- [alkalmazás létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [leképezések hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [entitások hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [kimondott szöveg hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
