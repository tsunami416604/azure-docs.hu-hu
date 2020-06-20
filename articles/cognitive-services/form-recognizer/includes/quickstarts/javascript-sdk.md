---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtár a JavaScripthez'
description: Ebben a rövid útmutatóban megkezdheti a JavaScripthez készült űrlap-felismerő ügyféloldali kódtár használatának első lépéseit.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/08/2020
ms.author: pafarley
ms.openlocfilehash: 0d8c498199d238f2414d4d9268cf466cd2d6b82d
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112031"
---
[Dokumentáció](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer?view=azure-node-preview)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Példák](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört. Ebben a rövid útmutatóban használhatja a [minta adatkészletének](https://go.microsoft.com/fwlink/?linkid=2090451) **vonattal** mappában található fájlokat.
* A [Node.js](https://nodejs.org/) aktuális verziója

## <a name="setting-up"></a>Beállítás

### <a name="create-a-form-recognizer-azure-resource"></a>Űrlap-felismerő Azure-erőforrás létrehozása

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Környezeti változók létrehozása

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` . 

```console
npm init
```

Hozzon létre egy nevű fájlt `index.js` , nyissa meg, és importálja a következő könyvtárakat:

```javascript
const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

Töltse be a környezeti változó fájlját is.

```javascript
// Load the .env file if it exists
require("dotenv").config();
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ai-form-recognizer` és a `dotenv` NPM csomagokat:

```console
npm install @azure/ai-form-recognizer dotenv
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.


<!-- 
    Object model
-->

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat az űrlap-felismerő ügyféloldali kódtár használatával a JavaScripthez:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Űrlap tartalmának felismerése](#recognize-form-content)
* [Visszaigazolások felismerése](#recognize-receipts)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A `main` függvényben hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a rendszerhéjat.

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = process.env["FORM_RECOGNIZER_ENDPOINT"] || "<cognitive services endpoint>";
const apiKey = process.env["FORM_RECOGNIZER_KEY"] || "<api key>";
```

Ezután hitelesítse az ügyféloldali objektumokat a definiált előfizetési változók használatával. **AzureKeyCredential** objektumot fog használni, így ha szükséges, az API-kulcsot új ügyfélalkalmazások létrehozása nélkül is frissítheti. Létre kell hoznia egy képzési ügyfél objektumot is.

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

### <a name="call-client-specific-functions"></a>Ügyfél-specifikus függvények hívása

A kód következő blokkja `main` az ügyfélalkalmazások használatával hívja meg a functions szolgáltatást az űrlap-FELISMERŐ SDK összes főbb feladatához. Ezeket a függvényeket később is megadhatja.

Emellett a képzési és tesztelési adatok URL-címeihez is hozzá kell adnia a hivatkozásokat.
* Az egyéni modell betanítási adataihoz tartozó SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A formátumnak a következőket kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Egy olyan űrlap URL-címének lekéréséhez, amely a teszteléshez használható, a fenti lépésekkel lekérheti egy egyedi dokumentum SAS URL-címét a blob Storage-ban. Vagy vegyen fel egy máshol található dokumentum URL-címét.
* A fenti módszer használatával beolvashatja a beérkezési képek URL-címét is, vagy használhatja a mintaként megadott képet.

> [!NOTE]
> Az útmutatóban szereplő kódrészletek az URL-címek által elért távoli űrlapokat használják. Ha ehelyett a helyi űrlapos dokumentumokat szeretné feldolgozni, tekintse meg a kapcsolódó módszereket a [dokumentációban](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer?view=azure-node-preview).


```javascript
const trainingDataUrl = "<url/path to the labeled training documents>";
const formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
const receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media/contoso-allinone.jpg";


// Call Form Recognizer scenarios:
await GetContent(recognizerClient, formUrl);
await AnalyzeReceipt(recognizerClient, receiptUrl);
modelId = await TrainModel(trainingClient, trainingDataUrl);
await AnalyzePdfForm(recognizerClient, modelId, formUrl);
await ManageModels(trainingClient, trainingDataUrl);
```

## <a name="recognize-form-content"></a>Űrlap tartalmának felismerése

Az űrlap-felismerő használatával felismerheti a dokumentumokban szereplő táblákat, vonalakat és szavakat, anélkül, hogy egy modellt kellene betanítania. Egy adott URI-fájl tartalmának felismeréséhez használja a **beginRecognizeContentFromUrl** metódust.

```javascript
async function GetContent( recognizerClient, invoiceUri)
{
    const poller = await client.beginRecognizeContentFromUrl(invoiceUri);
    await poller.pollUntilDone();
    const response = poller.getResult();
```

A visszaadott érték egy **FormPage** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához. Az alábbi kód megismétli ezeket az objektumokat, és kinyomtatja a kinyert kulcs/érték párokat és a táblák adatait.

```javascript
    for (const page of response.pages) {
    console.log(
        `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
    );
        for (const table of page.tables) {
            for (const row of table.rows) {
                for (const cell of row.cells) {
                    console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
                }
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Visszaigazolások felismerése

Ez a szakasz bemutatja, hogyan ismerheti fel és kinyerheti az Egyesült államokbeli nyugták közös mezőit egy előre képzett beérkezési modell használatával.

A visszaigazolások URI-ból való felismeréséhez használja a **beginRecognizeReceiptsFromUrl** metódust. A visszaadott érték egy **RecognizedReceipt** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához. A következő kód egy nyugtát dolgoz fel a megadott URI-n, és kiírja a fő mezőket és értékeket a konzolra.

```javascript
async function AnalyzeReceipt( client, receiptUri)
{
    const poller = await client.beginRecognizeReceiptsFromUrl(url, {
        includeTextDetails: true,
        onProgress: (state) => {
            console.log(`analyzing status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();


    const receipt = receipts[0];
    console.log("First receipt:");
    // For supported fields recognized by the service, please refer to https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeReceiptResult.
    const receiptTypeField = receipt.recognizedForm.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.recognizedForm.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.recognizedForm.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
```

A kód következő blokkja megismétli a nyugtán észlelt egyes elemeket, és kiírja az adataikat a-konzolra.

```javascript
    const itemsField = receipt.recognizedForm.fields["Items"];
    if (itemsField.valueType === "array") {
        for (const itemField of itemsField.value || []) {
            if (itemField.valueType === "object") {
                const itemNameField = itemField.value["Name"];
                if (itemNameField.valueType === "string") {
                    console.log(`    Item Name: '${itemNameField.value || "<missing>"}', with confidence of ${itemNameField.confidence}`);
                }
            }
        }
    }
}
```


## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan lehet a modelleket saját adataival betanítani. A betanított modellek olyan strukturált adatokat tudnak kialakítani, amelyek az eredeti dokumentum kulcs/érték kapcsolatait tartalmazzák. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például az [űrlap-felismerő minta feliratozási eszközét](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték felismeréséhez a betanítási dokumentumok manuális címkézése nélkül.

A következő függvény egy modellt hoz létre egy adott dokumentumon, és kiírja a modell állapotát a konzolra. 

```javascript
async function TrainModel(trainingClient, trainingDataUrl)
{
    const poller = await trainingClient.beginTraining(trainingDataUrl, false, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
    
    if (!response) {
        throw new Error("Expecting valid response!");
    }
    
    console.log(`Model ID: ${response.modelId}`);
    console.log(`Status: ${response.status}`);
    console.log(`Created on: ${response.requestedOn}`);
    console.log(`Last modified: ${response.completedOn}`);
```

A visszaadott **CustomFormModel** objektum a modell által felismerhető űrlap-típusokkal és az egyes űrlapokból kinyerhető mezőkkel kapcsolatos információkat tartalmaz. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

```javascript
    if (response.submodels) {
        for (const submodel of response.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
```

Végezetül ez a metódus a modell egyedi AZONOSÍTÓját adja vissza.

```csharp
    return response.modelId;
}
```

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

Egyéni modelleket is betaníthat, ha manuálisan címkézi a betanítási dokumentumokat. A címkékkel való képzés bizonyos helyzetekben jobb teljesítményt eredményez. Ha címkéket szeretne betanítani, a blob Storage-tárolóban a betanítási dokumentumokkal együtt speciális feliratú információs fájlokat (* \<filename\>.pdf.labels.js*) kell használnia. Az [űrlap-felismerő minta címkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkék létrehozásához. Miután megadta őket, meghívhatja a **beginTraining** metódust a *uselabels* paraméterrel, amely a következőre van beállítva: `true` .

```javascript
async function TrainModelWithLabelsAsync(
    const poller = await trainingClient.beginTraining(trainingDataUrl, true, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const model = poller.getResult();
    
    return model.modelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a szakasz azt mutatja be, hogyan lehet kinyerni a kulcs/érték információkat és az egyéb tartalmakat az egyéni űrlapok típusaiból, a saját űrlapjaival betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az ID-t az alábbi metódusba tudja adni. Lásd a [modell betanítása](#train-a-model-without-labels) szakaszt.

A **beginRecognizeCustomFormsFromUrl** metódust fogja használni. A visszaadott érték egy **RecognizedForm** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához.

```javascript
// Analyze PDF form document at an accessible URL
async function AnalyzePdfForm(client, modelId, formUrl)
{    
    const poller = await client.beginRecognizeCustomFormsFromUrl(modelId, formUrl, {
        onProgress: (state) => {
            console.log(`status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
```

A következő kód kinyomtatja az elemzési eredményeket a-konzolra. Kinyomtatja az egyes felismert mezőket és a hozzá tartozó értékeket, valamint a megbízhatósági pontszámot.

```javascript
    console.log("Fields:");
    for (const fieldName in form.fields) {
        // each field is of type FormField
        const field = form.fields[fieldName];
        console.log(
            `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
        );
    }
}
```

## <a name="manage-your-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. A következő kód az összes modell-felügyeleti feladatot egyetlen függvényben hajtja végre, példaként. Első lépésként másolja az alábbi függvény-aláírást:

```javascript
async function ManageModels(trainingClient, trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>A FormRecognizer-erőforrás fiókban található modellek számának megkeresése

A következő kódrészlet ellenőrzi, hogy az űrlap-felismerő fiókban hány modell lett mentve, és összehasonlítja azt a fiókra vonatkozó korláttal.

```csharp
    // First, we see how many custom models we have, and what our limit is
    const accountProperties = await trainingClient.getAccountProperties();
    console.log(
        `Our account has ${accountProperties.customModelCount} custom models, and we can have at most ${accountProperties.customModelLimit} custom models`
    );
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Az erőforrás-fiókban jelenleg tárolt modellek listázása

A következő kódrészlet felsorolja a fiókban lévő aktuális modelleket, és kiírja az adataikat a-konzolra. Emellett az első modellre mutató hivatkozást is ment.

```javascript
    // Next, we get a paged async iterator of all of our custom models
    const result = trainingClient.listCustomModels();

    // We could print out information about first ten models
    // and save the first model id for later use
    let i = 0;
    let firstModel;
    for await (const model of result) {
        console.log(`model ${i++}:`);
        console.log(model);
        if (i === 1) {
            firstModel = model;
        }
        if (i > 10) {
            break;
        }
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Adott modell beszerzése a modell AZONOSÍTÓjának használatával

A következő kódrészlet az előző szakaszban mentett modell AZONOSÍTÓját használja, és a használatával kéri le a modell részleteit.

```csharp
    // Now we'll get the first custom model in the paged list
    const model = await trainingClient.getModel(firstModel.modelId);
    console.log(`Model Id: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log("Documents used in training: [");
    for (const doc of model.trainingDocuments || []) {
        console.log(`  ${doc.documentName}`);
    }
    console.log("]");
```

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrás-fiókból

Az AZONOSÍTÓra hivatkozva egy modellt is törölhet a fiókjából. Ez a kód törli az előző szakaszban használt modellt.

```javascript
    await client.deleteModel(firstModel.modelId);
    try {
        const deleted = await trainingClient.deleteModel(firstModel.modelId);
        console.log(deleted);
    } catch (err) {
        // Expected
        console.log(`Model with id ${firstModel.modelId} has been deleted`);
    }
}
```


## <a name="run-the-application"></a>Alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `node` fájlban található paranccsal.

```console
node index.js
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="enable-logs"></a>Naplók engedélyezése
A következő környezeti változó megadásával megtekintheti a hibakeresési naplókat a tár használatakor.

```
export DEBUG=azure*
```

A naplók engedélyezésével kapcsolatos részletes információkért lásd a [ @azure/logger csomag dokumentációját](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az űrlap felismerő Python ügyféloldali függvénytárát használta a modellek tanításához és az űrlapok különböző módokon történő elemzéséhez. Következő lépésként Ismerkedjen meg a jobb betanítási adatkészlet létrehozásával és a pontosabb modellek előállításával.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

* [Mi a Form Recognizer?](../../overview.md)
* A jelen útmutatóban szereplő mintakód (és egyebek) megtalálható a [githubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).