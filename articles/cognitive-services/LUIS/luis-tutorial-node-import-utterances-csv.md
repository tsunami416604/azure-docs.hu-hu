---
title: Importálja a Node.js használatával kimondott szöveg
titleSuffix: Azure
description: Ismerje meg, hogyan hozhat létre egy már létező adatokat CSV formátumban a LUIS jelentéskészítési API-val programozott módon a LUIS-alkalmazás.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: e38e71d72a62399512e348d97d4770ea48afd146
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224089"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Programozott módon a Node.js használatával a LUIS alkalmazás készítése

A LUIS programozható API-t, amely minden, amely biztosít a [LUIS](luis-reference-regions.md) webhely does. Ez Ha rendelkezik már meglévő adatokat, és írja be az adatokat manuálisan hozhat létre programozott módon, mint a LUIS-alkalmazások gyorsabb lenne időt takaríthat meg. 

## <a name="prerequisites"></a>Előfeltételek

* Jelentkezzen be a [LUIS](luis-reference-regions.md) webhelyet, és keresse meg a [kulcs létrehozási](luis-concept-keys.md#authoring-key) a fiók beállításait. Ezt a kulcsot a jelentéskészítési API-kat használhatja.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* Ebben az oktatóanyagban egy kitalált vállalat naplófájljainak a felhasználói kérések CSV kezdődik. Töltse le [Itt](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* A legfrissebb Node.js telepítése az npm-mel. Töltse le [Itt](https://nodejs.org/en/download/).
* **[Ajánlott]**  Visual Studio Code IntelliSense és a hibakereséshez, töltse le [Itt](https://code.visualstudio.com/) ingyenes.

## <a name="map-preexisting-data-to-intents-and-entities"></a>Már létező adatokat leképezze szándékok és entitások felismerésére
Ha már rendelkezik egy rendszer, amely szem előtt, az intelligens hangfelismerési szolgáltatással nem lett létrehozva, ha maps kezdhet a felhasználók számára szeretné szöveges adatok tartalmazza, lehet felhasználói bevitelt a LUIS szándék, a meglévő kategóriák egy leképezéssel merülnek fel. Amennyiben azonosítani lehet fontos szavak vagy kifejezések a felhasználók mondta, előfordulhat, hogy ezeknek a szavaknak képezze le entitásokat.

Nyissa meg az `IoT.csv` fájlt. Elméleti otthoni automation szolgáltatás, beleértve a hogyan lettek besorolva, a felhasználó mondta, és a néhány hasznos információkkal kihúzott azokat a felhasználói lekérdezések naplózása a tartalmazza. 

![Már meglévő adatok CSV-fájl](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Látni, hogy a **RequestType** oszlop lehet szándék fog vonatkozni, és a **kérelem** az oszlopban látható egy példa utterance (kifejezés). A többi mező entitások lehet, ha azok az utterance (kifejezés). Mivel szándék fog vonatkozni, az entitások és példa kimondott szöveg van, hogy egy egyszerű, mintaalkalmazást a követelmények.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>A LUIS adatok a LUIS-alkalmazások létrehozására vonatkozó lépéseket
Hozzon létre egy új LUIS-alkalmazást a forrásfájl, először, a CSV-fájlból az adatok elemzése és az adatok átalakítása a jelentéskészítési API-val LUIS feltölthet formátumú. Az elemzett adatokból Ön információkat gyűjthet mi szándékok és entitások vannak-e. Ezután API-hívásokat az alkalmazás létrehozásához, és adja hozzá a szándékok és entitások, amelyek az elemzett adatokból is megtörtént. Miután létrehozta a LUIS-alkalmazás, például megcímkézzen is hozzáadhat az elemzett adatokból. Ez a folyamat a következő kód utolsó részében tekintheti meg. Másolás vagy [letöltése](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) a kódot, és mentse `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>A fürt megosztott kötetei szolgáltatás elemzése

Az oszlopok bejegyzéseit, amelyek tartalmazzák a Megosztott fürtköteten megcímkézzen rendelkezik, amelyet az képes megérteni a LUIS JSON formátumban értelmezni. A JSON-formátumban tartalmaznia kell egy `intentName` azonosító szándéka az utterance (kifejezés) mezőben. Is tartalmaznia kell egy `entityLabels` lehet üres, ha nincsenek entitások a az utterance (kifejezés) mezőben. 

Például a bejegyzés a térképek szolgáltatáshoz "Kapcsolja fel a lámpákat" ezt a JSON:

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

Ebben a példában a `intentName` származik, a felhasználói kérelem alapján a **kérelem** oszlopfejlécre a CSV-fájl és a `entityName` adatait a többi oszlop származik. Például, ha van olyan bejegyzés **művelet** vagy **eszköz**, és, hogy a karakterlánc is bekövetkezik a tényleges kérelemben, majd egy egységként is címkével. A következő kód bemutatja ennek elemzési folyamatot. Másolhatja vagy [letöltése](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) , és mentse a `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>A LUIS-alkalmazás létrehozása
Miután az adatok rendelkezik elemzett JSON-ba, adja hozzá a LUIS-alkalmazás. A következő kódot a LUIS-alkalmazást hoz létre. Másolás vagy [letöltése](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) , és mentse őket `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Leképezések hozzáadása
Ha már rendelkezik egy alkalmazást, szüksége hozzá leképezések. A következő kódot a LUIS-alkalmazást hoz létre. Másolás vagy [letöltése](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) , és mentse őket `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Entitások hozzáadása
A következő kódot az entitásokat ad hozzá a LUIS-alkalmazás. Másolás vagy [letöltése](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) , és mentse őket `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Beszédmódok hozzáadása
Ha az entitások és a szándék fog vonatkozni a LUIS alkalmazás definiálva van, a kimondott szöveg is hozzáadhat. A következő kódban a [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API-t, amely lehetővé teszi, hogy egyszerre akár 100 beszédmódok hozzáadása.  Másolás vagy [letöltése](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) , és mentse őket `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>A kód futtatása


### <a name="install-nodejs-dependencies"></a>Telepítse a Node.js-függőségek
A Node.js-függőségek telepítése az npm-ből a terminálon/parancssorból.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Konfigurációs beállítások módosítása
Annak érdekében, hogy az alkalmazás használatához meg kell az index.js fájlt az értékeket módosítsa saját végponti kulcs, és adja meg azt szeretné, hogy az alkalmazás nevét. Állítsa be az alkalmazás kulturális környezet vagy a verziószám módosíthatja is.

Nyissa meg az index.js fájlt, és módosítsa ezeket az értékeket a fájl elején.


```nodejs
// Change these values
const LUIS_programmaticKey = "YOUR_PROGRAMMATIC_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>A szkript futtatása
Futtassa a szkriptet a terminálon/parancssorból a node.js használatával.

```console
> node index.js
```

vagy

```console
> npm start
```

### <a name="application-progress"></a>Alkalmazás folyamatban
Az alkalmazás futása közben a parancssori folyamatot mutatja. A parancssori kimenet tartalmazza a válaszokat a LUIS formátumát.

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




## <a name="open-the-luis-app"></a>Nyissa meg a LUIS-alkalmazás
Miután a parancsfájl lefutott, bejelentkezhet a [LUIS](luis-reference-regions.md) , és tekintse meg a LUIS alapján létrehozott alkalmazást **saját alkalmazások**. A hozzáadott megcímkézzen láthatja el a **bekapcsolása**, **Kikapcsolás**, és **nincs** szándék fog vonatkozni.

![Leképezés bekapcsolása](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [LUIS webhelyen az alkalmazást kész, és tesztelése](luis-interactive-test.md)

## <a name="additional-resources"></a>További források

Ez a mintaalkalmazás az alábbi intelligens HANGFELISMERÉSI API-kat használja:
- [alkalmazás létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [leképezések hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [entitások hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [beszédmódok hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
