---
title: Hozza létre a programozott módon, Node.js LUIS alkalmazását |} Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan hozhat létre egy LUIS alkalmazást programozott már meglévő adatok CSV formátumban LUIS szerzői API használatával.
services: cognitive-services
author: DeniseMak
manager: rstand
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: v-geberr
ms.openlocfilehash: 09c9d4da835b7b30fd132770f9d13b33fa80a3f5
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268329"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Hozza létre a programozott módon, Node.js LUIS alkalmazását

LUIS biztosít egy programozott API-t, amelyet minden, a [LUIS] [ LUIS] webhely does. Ez Ha rendelkezik már létező adatokat, és gyorsabb programozott módon, mint LUIS alkalmazás létrehozása beírásával kézzel lenne időt takaríthat meg. 

## <a name="prerequisites"></a>Előfeltételek

* Jelentkezzen be a [LUIS] [ LUIS] webhely és a Keresés a [kulcs szerzői](luis-concept-keys.md#authoring-key) -fiókjának beállításai. Ez a kulcs használatával hívható meg a szerzői API-k.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* Ez az oktatóanyag a felhasználói kérelmek feltételezett a vállalat naplófájlokat egy CSV kezdődik. Töltse le [Itt](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Telepítse a legújabb Node.js NPM. Töltse le [Itt](https://nodejs.org/en/download/).
* **[Ajánlott]**  Visual Studio Code IntelliSense és a hibakeresést, töltse le [Itt](https://code.visualstudio.com/) szabad.

## <a name="map-preexisting-data-to-intents-and-entities"></a>Térkép már létező adatokat leképezések és entitások
Ha létrehozott nem LUIS szem előtt, a rendszer szöveges adatok, amelyek különböző fogalom felhasználók leképezve szeretné tartalmaz rendelkezik, valószínűleg leképezéseket kapja meg a felhasználó a leképezések LUIS a meglévő kategóriák. Ha fontos szavakat vagy kifejezéseket a a felhasználók említett azonosíthatja, ezeknek a szavaknak entitások le lehet képezni.

Nyissa meg az `IoT.csv` fájlt. Naplózása a felhasználói lekérdezések elméleti otthoni automation szolgáltatás, hogyan lettek besorolva, a felhasználó említett, és azokat az oszlopokat beleértve azokat kihúzott hasznos információkat tartalmaz. 

![A CSV-fájl](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Tekintse meg, amely a **RequestType** oszlop lehet leképezések, és a **kérelem** az oszlopban látható egy példa utterance. A többi mező okozhat a utterance jelentkeznek entitásokat. Mivel leképezések, entitások és példa utterances, hogy egy egyszerű, mintaalkalmazást követelményei.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Nem LUIS adatokból LUIS-alkalmazás létrehozása
Használatával hozzon létre egy új LUIS alkalmazást a forrásfájl, először a adatokat a CSV-fájlból olvassa be, majd ezek az adatok átalakítása feltöltheti a szerzői API használatával LUIS formátumú. Az elemzett adatokból hogy információkat gyűjt az mi leképezések és entitások vannak-e. Ezután API-hívások az alkalmazás létrehozásához, és adja hozzá a leképezések és az elemzett adatokat gyűjtött lettek entitások. Miután létrehozta a LUIS alkalmazást, a példában utterances az elemzett adatokat is hozzáadhat. Ez a folyamat az alábbi kód utolsó részében tekintheti meg. Másolás vagy [letöltése](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/index.js) a code, és mentse a `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>A fürt megosztott kötetei szolgáltatás elemzése

A megosztott fürtkötet utterances tartalmazó bejegyzést kell LUIS tudja értelmezni JSON formátumban értelmezni. A JSON formátum tartalmaznia kell egy `intentName` , amely azonosítja a utterance szándékával mező. Is tartalmaznia kell egy `entityLabels` mező, amely lehet üres, ha nincsenek a utterance az entitások. 

Például a bejegyzést a JSON "Kapcsolja be a fény" leképezve:

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

Ebben a példában a `intentName` származik, a felhasználói kérelem alatt a **kérelem** oszlopfejlécre kattint a CSV-fájlt, és a `entityName` kapcsolatos információkat a más oszlopok származik. Például, ha a bejegyzés **művelet** vagy **eszköz**, és, hogy a karakterlánc is a tényleges kérelem következik be, majd egy egységként neve. A következő kód bemutatja a folyamat elemzése. Másolhatja vagy [letöltése](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_parse.js) , majd mentse azokat `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>A LUIS-alkalmazás létrehozása
Ha az adatokat a JSON-ba rendelkezik elemzett, adja hozzá egy LUIS alkalmazást. A következő kódot a LUIS alkalmazást hoz létre. Másolás vagy [letöltése](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_create.js) , és mentse azokat `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Leképezések hozzáadása
Miután egy alkalmazást, kell leképezések rá. A következő kódot a LUIS alkalmazást hoz létre. Másolás vagy [letöltése](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_intents.js) , és mentse azokat `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Entitások hozzáadása
A következő kódot az entitások hozzáadja az LUIS alkalmazáshoz. Másolás vagy [letöltése](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_entities.js) , és mentse azokat `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Beszédmódok hozzáadása
Miután az entitásokat és a leképezések az LUIS alkalmazásban definiálva van, a utterances is hozzáadhat. A következő kódban a [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API, amely lehetővé teszi, hogy akár 100 utterances felvenni egy időben.  Másolás vagy [letöltése](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_upload.js) , és mentse azokat `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>A kód futtatása


### <a name="install-nodejs-dependencies"></a>Telepítse a Node.js-függőségek
Telepítse a Node.js-függőségek NPM a Terminálszolgáltatások/parancssorban.

````
> npm install
````

### <a name="change-configuration-settings"></a>Konfigurációs beállítások módosítása
Az alkalmazás használatához meg kell módosítsa az értékeket a index.js fájlban saját előfizetés kulcsot, és adjon meg egy nevet az alkalmazásnak. Állítsa be az alkalmazás kulturális környezet vagy a verziószám módosíthatja is.

Nyissa meg a index.js fájlt, és módosítsa a fájl elején ezeket az értékeket.


````JavaScript
// Change these values
const LUIS_programmaticKey = "YOUR_PROGRAMMATIC_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
````
### <a name="run-the-script"></a>A parancsfájl futtatása
Node.js egy terminál-/ parancssorból futtassa a parancsfájlt.

````
> node index.js
````
vagy
````
> npm start
````

### <a name="application-progress"></a>Alkalmazás folyamatban
Az alkalmazás futása közben, a parancssor folyamatban jeleníti meg. A parancssori kimenet tartalmazza a formátum LUIS válaszát.

````
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
````




## <a name="open-the-luis-app"></a>Nyissa meg a LUIS alkalmazást
A parancsfájl után is jelentkezik be a [LUIS] [ LUIS] , és megtekintheti a LUIS app alatt létrehozott **saját alkalmazások**. A hozzáadása az utterances láthatók el a **bekapcsolása**, **kikapcsolásának**, és **nincs** leképezések.

![Leképezés bekapcsolása](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tesztelje, és az alkalmazás LUIS webhelyen betanítása](interactive-test.md)

## <a name="additional-resources"></a>További források

Ez a minta az alkalmazás a következő LUIS API-kat használ:
- [Alkalmazás létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Leképezések hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [Entitás hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [Utterances hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

