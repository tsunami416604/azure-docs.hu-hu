---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtár a JavaScripthez'
description: Ebben a rövid útmutatóban megkezdheti a JavaScripthez készült űrlap-felismerő ügyféloldali kódtár használatának első lépéseit.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 08/21/2020
ms.author: pafarley
ms.openlocfilehash: 1c24eba79c26c4540e9d97a3e2b6646fd0b5439c
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864890"
---
> [!IMPORTANT]
> * Az űrlap-felismerő SDK jelenleg a from felismerő szolgáltatás v 2.0-s verzióját célozza meg.
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Tekintse meg az alábbi dokumentációt. 

[Dokumentáció](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Példák](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört. Ebben a rövid útmutatóban használhatja a [minta adatkészletének](https://go.microsoft.com/fwlink/?linkid=2090451) **vonattal** mappában található fájlokat.
* A [Node.js](https://nodejs.org/) aktuális verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hozzon létre egy űrlap-felismerő erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Az Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

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
const { FormRecognizerClient, FormTrainingClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ai-form-recognizer` NPM csomagot:

```console
npm install @azure/ai-form-recognizer
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

## <a name="object-model"></a>Objektummodell 

Az űrlap-felismerő használatával két különböző típusú ügyfél hozható létre. Az első az, `FormRecognizerClient` hogy a szolgáltatás lekérdezését felismerő űrlapmezők és tartalom használatával kérdezi le. A második a `FormTrainingClient` használatával egyéni modelleket hozhat létre és kezelhet, amelyeket az elismerés javítása érdekében használhat. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`FormRecognizerClient` a következő műveleteit biztosítja:

 * Az űrlap mezőinek és tartalmának felismerése az egyéni űrlapok felismerése céljából betanított egyéni modellek használatával. Ezeket az értékeket az objektumok egy gyűjteménye adja vissza `RecognizedForm` .
 * Űrlap tartalmának felismerése, beleértve a táblákat, a sorokat és a szavakat, anélkül, hogy be kellene tanítani a modellt. Az űrlap tartalma objektumok gyűjteményében lesz visszaadva `FormPage` .
 * A beérkezések gyakori mezőinek felismerése, az űrlap-felismerő szolgáltatásban egy előre betanított modell használatával. Ezeket a mezőket és a metaadatokat a gyűjteménye adja vissza `RecognizedReceipt` .

### <a name="formtrainingclient"></a>FormTrainingClient
`FormTrainingClient` a következő műveleteit biztosítja:

* Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték felismeréséhez. A `CustomFormModel` visszatérési értéke jelzi, hogy a modell milyen típusú adattípusokat fog felismerni, és hogy milyen mezőket kell kibontania az egyes űrlapok típusaihoz. A betanítási adatkészletek létrehozásával kapcsolatos részletesebb magyarázatért tekintse [meg a szolgáltatás dokumentációját a címke nélküli modell betanításához](#train-a-model-without-labels) .
* Egyéni modellek betanítása az egyéni űrlapok címkézésével megadott mezők és értékek felismeréséhez. A `CustomFormModel` visszaadja a modell által kinyert mezőket, valamint az egyes mezők becsült pontosságát. Tekintse [meg a szolgáltatás dokumentációját a modell betanítása](#train-a-model-with-labels) című témakörben, amely részletesebben ismerteti a címkék egy betanítási adatkészletbe való alkalmazásának részletes ismertetését.
* A fiókban létrehozott modellek kezelése.
* Egyéni modell másolása az egyik űrlap-felismerő erőforrásból egy másikba.

Vegye figyelembe, hogy a modellek grafikus felhasználói felülettel is betanítható, például az [űrlap-felismerő címkéző eszköz](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool)használatával.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat az űrlap-felismerő ügyféloldali kódtár használatával a JavaScripthez:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Űrlap tartalmának felismerése](#recognize-form-content)
* [Visszaigazolások felismerése](#recognize-receipts)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az alkalmazásban hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának megfelelően. 

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = "<paste-your-form-recognizer-endpoint-here>";
const apiKey = "<paste-your-form-recognizer-key-here>";
```

Ezután hitelesítse az ügyféloldali objektumokat a definiált előfizetési változók használatával. Egy objektumot fog használni `AzureKeyCredential` , így ha szükséges, az API-kulcsot új ügyfélalkalmazások létrehozása nélkül is frissítheti. Létre kell hoznia egy képzési ügyfél objektumot is.

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="assets-for-testing"></a>Tesztelési eszközök

Az útmutatóban szereplő kódrészletek az URL-címek által elért távoli űrlapokat használják. Ha ehelyett a helyi űrlapos dokumentumokat szeretné feldolgozni, tekintse meg a kapcsolódó módszereket a [dokumentációban](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) és a [mintákban](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Emellett a képzési és tesztelési adatok URL-címeihez is hozzá kell adnia a hivatkozásokat.
* Az egyéni modell betanítási adataihoz tartozó SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A formátumnak a következőket kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Használja az alábbi mintákban található minta-és beérkezési képeket (a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) is elérhetők), vagy a fenti lépésekkel lekérheti az egyes dokumentumok sas URL-címét a blob Storage-ban. 

> [!NOTE]
> Az útmutatóban szereplő kódrészletek az URL-címek által elért távoli űrlapokat használják. Ha ehelyett a helyi űrlapos dokumentumokat szeretné feldolgozni, tekintse meg a kapcsolódó módszereket a [dokumentációban](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

## <a name="recognize-form-content"></a>Űrlap tartalmának felismerése

Az űrlap-felismerő használatával felismerheti a dokumentumokban szereplő táblákat, vonalakat és szavakat, anélkül, hogy egy modellt kellene betanítania. Egy adott URI-fájl tartalmának felismeréséhez használja a `beginRecognizeContentFromUrl` metódust.

```javascript
async function recognizeContent() {
    const formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";
    const poller = await client.beginRecognizeContentFromUrl(formUrl);
    const pages = await poller.pollUntilDone();

    if (!pages || pages.length === 0) {
        throw new Error("Expecting non-empty list of pages!");
    }

    for (const page of pages) {
        console.log(
            `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
        );
        for (const table of page.tables) {
            for (const cell of table.cells) {
                console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
            }
        }
    }
}

recognizeContent().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Kimenet

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="recognize-receipts"></a>Visszaigazolások felismerése

Ez a szakasz bemutatja, hogyan ismerheti fel és kinyerheti az Egyesült államokbeli nyugták közös mezőit egy előre képzett beérkezési modell használatával.

Az URI-ból származó nyugták felismeréséhez használja a `beginRecognizeReceiptsFromUrl` metódust. A következő kód egy nyugtát dolgoz fel a megadott URI-n, és kiírja a fő mezőket és értékeket a konzolra.

```javascript
async function recognizeReceipt() {
    receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png";
    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeReceiptsFromUrl(receiptUrl, {
        onProgress: (state) => { console.log(`status: ${state.status}`); }
    });

    const receipts = await poller.pollUntilDone();

    if (!receipts || receipts.length <= 0) {
        throw new Error("Expecting at lease one receipt in analysis result");
    }

    const receipt = receipts[0];
    console.log("First receipt:");
    const receiptTypeField = receipt.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
    const itemsField = receipt.fields["Items"];
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
    const totalField = receipt.fields["Total"];
    if (totalField.valueType === "number") {
        console.log(`  Total: '${totalField.value || "<missing>"}', with confidence of ${totalField.confidence}`);
    }
}

recognizeReceipt().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Kimenet

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan lehet a modelleket saját adataival betanítani. A betanított modellek olyan strukturált adatokat tudnak kialakítani, amelyek az eredeti dokumentum kulcs/érték kapcsolatait tartalmazzák. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például az [űrlap-felismerő minta feliratozási eszközét](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték felismeréséhez a betanítási dokumentumok manuális címkézése nélkül.

A következő függvény egy modellt hoz létre egy adott dokumentumon, és kiírja a modell állapotát a konzolra. 

```javascript
async function trainModel() {

    const containerSasUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await trainingClient.beginTraining(containerSasUrl, false, {
        onProgress: (state) => { console.log(`training status: ${state.status}`); }
    });
    const model = await poller.pollUntilDone();

    if (!model) {
        throw new Error("Expecting valid training result!");
    }

    console.log(`Model ID: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log(`Training started on: ${model.trainingStartedOn}`);
    console.log(`Training completed on: ${model.trainingCompletedOn}`);

    if (model.submodels) {
        for (const submodel of model.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
    // Training document information
    if (model.trainingDocuments) {
        for (const doc of model.trainingDocuments) {
            console.log(`Document name: ${doc.name}`);
            console.log(`Document status: ${doc.status}`);
            console.log(`Document page count: ${doc.pageCount}`);
            console.log(`Document errors: ${doc.errors}`);
        }
    }
}

trainModel().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Kimenet

A [PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)-ból elérhető betanítási adatokkal betanított modell kimenete. A minta kimenete az olvashatóság érdekében csonkítva lett.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: 
...
```

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

Egyéni modelleket is betaníthat, ha manuálisan címkézi a betanítási dokumentumokat. A címkékkel való képzés bizonyos helyzetekben jobb teljesítményt eredményez. Ha címkéket szeretne betanítani, `\<filename\>.pdf.labels.json` a blob Storage-tárolóban speciális címke-információs fájlokkal () kell rendelkeznie a betanítási dokumentumok mellett. Az [űrlap-felismerő minta címkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkék létrehozásához. Miután megadta őket, meghívhatja a metódust a következőre `beginTraining` `uselabels` beállított paraméterrel: `true` .

```javascript
async function trainModelLabels() {

    const containerSasUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await trainingClient.beginTraining(containerSasUrl, true, {
        onProgress: (state) => { console.log(`training status: ${state.status}`); }
    });
    const model = await poller.pollUntilDone();

    if (!model) {
        throw new Error("Expecting valid training result!");
    }

    console.log(`Model ID: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log(`Training started on: ${model.trainingStartedOn}`);
    console.log(`Training completed on: ${model.trainingCompletedOn}`);

    if (model.submodels) {
        for (const submodel of model.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
    // Training document information
    if (model.trainingDocuments) {
        for (const doc of model.trainingDocuments) {
            console.log(`Document name: ${doc.name}`);
            console.log(`Document status: ${doc.status}`);
            console.log(`Document page count: ${doc.pageCount}`);
            console.log(`Document errors: ${doc.errors}`);
        }
    }
}

trainModelLabels().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Kimenet 

A [PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)-ból elérhető betanítási adatokkal betanított modell kimenete. A minta kimenete az olvashatóság érdekében csonkítva lett.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a szakasz azt mutatja be, hogyan lehet kinyerni a kulcs/érték információkat és az egyéb tartalmakat az egyéni űrlapok típusaiból, a saját űrlapjaival betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az ID-t az alábbi metódusba tudja adni. Lásd a [modell betanítása](#train-a-model-without-labels) szakaszt.

A `beginRecognizeCustomFormsFromUrl` metódust fogja használni. A visszaadott érték objektumok gyűjteménye `RecognizedForm` : egyet a beküldött dokumentum minden lapján.

```javascript
async function recognizeCustom() {
    // Model ID from when you trained your model.
    const modelId = "<modelId>";
    const formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";

    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeCustomForms(modelId, formUrl, {
        onProgress: (state) => { console.log(`status: ${state.status}`); }
    });
    const forms = await poller.pollUntilDone();

    console.log("Forms:");
    for (const form of forms || []) {
        console.log(`${form.formType}, page range: ${form.pageRange}`);
        console.log("Pages:");
        for (const page of form.pages || []) {
            console.log(`Page number: ${page.pageNumber}`);
            console.log("Tables");
            for (const table of page.tables || []) {
                for (const cell of table.cells) {
                    console.log(`cell (${cell.rowIndex},${cell.columnIndex}) ${cell.text}`);
                }
            }
        }

        console.log("Fields:");
        for (const fieldName in form.fields) {
            // each field is of type FormField
            const field = form.fields[fieldName];
            console.log(
                `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
            );
        }
    }
}

recognizeCustom().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Kimenet

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. A következő kód az összes modell-felügyeleti feladatot egyetlen függvényben hajtja végre, példaként. 

### <a name="get-list-of-models-in-account"></a>A fiókban található modellek listájának beolvasása

A következő kódrészlet a fiókban elérhető modellek teljes listáját tartalmazza, beleértve a modell létrehozásának és aktuális állapotának adatait.

```javascript
async function listModels() {
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    // returns an async iteratable iterator that supports paging
    const result = client.listCustomModels();
    let i = 0;
    for await (const modelInfo of result) {
        console.log(`model ${i++}:`);
        console.log(modelInfo);
    }
}

listModels().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Kimenet

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids"></a>Modell-azonosítók listájának beolvasása

Ez a kódrészlet a modellek és a modell-azonosítók listáját tartalmazza.

```javascript
async function listModelIds(){
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
    // using `iter.next()`
    i = 1;
    let iter = client.listCustomModels();
    let modelItem = await iter.next();
    while (!modelItem.done) {
        console.log(`model ${i++}: ${modelItem.value.modelId}`);
        modelItem = await iter.next();
    }
}
```

### <a name="output"></a>Kimenet

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-list-of-model-ids-by-page"></a>Modell-azonosítók listájának beolvasása oldal alapján

Ez a kódrészlet a modellek és a modell-azonosítók többoldalas listáját tartalmazza.

```javascript
async function listModelsByPage(){
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
    // using `byPage()`
    i = 1;
    for await (const response of client.listCustomModels().byPage()) {
        for (const modelInfo of response.modelList) {
            console.log(`model ${i++}: ${modelInfo.modelId}`);
        }
    }
}

listModelsByPage().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Kimenet

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
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

### <a name="output"></a>Kimenet

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást bármikor futtathatja, ha a rövid útmutatóban szereplő összes funkcióval elolvasta ezt a parancsot:

```console
node index.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

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

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](../../overview.md)
