---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtár a Pythonhoz'
description: Ebben a rövid útmutatóban megismerheti a Pythonhoz készült űrlap-felismerő ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 08/21/2020
ms.author: pafarley
ms.openlocfilehash: b178a0b347888f22d9a3c0ee88a203e377cb15be
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864927"
---
> [!IMPORTANT]
> * Az űrlap-felismerő SDK jelenleg a from felismerő szolgáltatás v 2.0-s verzióját célozza meg.
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Tekintse meg az alábbi dokumentációt. 

[Dokumentáció](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Csomag (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Példák](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört. Ebben a rövid útmutatóban használhatja a [minta adatkészletének](https://go.microsoft.com/fwlink/?linkid=2090451) **vonattal** mappában található fájlokat.
* [Python 2,7, vagy 3,5 vagy újabb](https://www.python.org/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hozzon létre egy űrlap-felismerő erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Az Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="object-model"></a>Objektummodell 

Az űrlap-felismerő használatával két különböző típusú ügyfél hozható létre. Az első az, `form_recognizer_client` hogy a szolgáltatás lekérdezését felismerő űrlapmezők és tartalom használatával kérdezi le. A második a `form_training_client` használatával egyéni modelleket hozhat létre és kezelhet, amelyeket az elismerés javítása érdekében használhat. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`form_recognizer_client` a következő műveleteit biztosítja:

 * Az űrlap mezőinek és tartalmának felismerése az egyéni űrlapok felismerése céljából betanított egyéni modellek használatával. 
 * Űrlap tartalmának felismerése, beleértve a táblákat, a sorokat és a szavakat, anélkül, hogy be kellene tanítani a modellt. 
 * A beérkezések gyakori mezőinek felismerése, az űrlap-felismerő szolgáltatásban egy előre betanított modell használatával.

### <a name="formtrainingclient"></a>FormTrainingClient
`form_training_client` a következő műveleteit biztosítja:

* Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték felismeréséhez. A betanítási adatkészletek létrehozásával kapcsolatos részletesebb magyarázatért tekintse [meg a szolgáltatás dokumentációját a címke nélküli modell betanításához](#train-a-model-without-labels) .
* Egyéni modellek betanítása az egyéni űrlapok címkézésével megadott mezők és értékek felismeréséhez. Tekintse [meg a szolgáltatás dokumentációját a modell betanítása](#train-a-model-with-labels) című témakörben, amely részletesebben ismerteti a címkék egy betanítási adatkészletbe való alkalmazásának részletes ismertetését.
* A fiókban létrehozott modellek kezelése.
* Egyéni modell másolása az egyik űrlap-felismerő erőforrásból egy másikba.

Vegye figyelembe, hogy a modellek grafikus felhasználói felülettel is betanítható, például az [űrlap-felismerő címkéző eszköz](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool) használatával.

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után a következő módon telepítheti az űrlap-felismerő ügyféloldali kódtár legújabb verzióját:

```console
pip install azure-ai-formrecognizer
```

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. Ezután importálja a következő kódtárakat. Ne feledje, hogy a betanításhoz és az űrlap-felismeréshez szükséges kódtárak importálása folyamatban van.

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import FormRecognizerClient
from azure.ai.formrecognizer import FormTrainingClient
from azure.core.credentials import AzureKeyCredential
```

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. 

```python
endpoint = "<paste-your-form-recognizer-endpoint-here>"
key = "<paste-your-form-recognizer-key-here>"
```

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Pythonhoz készült űrlap-felismerő ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Űrlap tartalmának felismerése](#recognize-form-content)
* [Visszaigazolások felismerése](#recognize-receipts)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Itt két ügyfél-objektumot kell hitelesítenie a fent megadott előfizetési változók használatával. Egy objektumot fog használni `AzureKeyCredential` , így ha szükséges, az API-kulcsot új ügyfélalkalmazások létrehozása nélkül is frissítheti.

```python
form_recognizer_client = FormRecognizerClient(endpoint, AzureKeyCredential(key))
form_training_client = FormTrainingClient(endpoint, AzureKeyCredential(key))
```

## <a name="assets-for-testing"></a>Tesztelési eszközök

Az útmutatóban szereplő kódrészletek az URL-címek által elért távoli űrlapokat használják. Ha ehelyett a helyi űrlapos dokumentumokat szeretné feldolgozni, tekintse meg a kapcsolódó módszereket a [dokumentációban](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) és a [mintákban](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Emellett a képzési és tesztelési adatok URL-címeihez is hozzá kell adnia a hivatkozásokat.
* Az egyéni modell betanítási adataihoz tartozó SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A formátumnak a következőket kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Használja az alábbi mintákban található minta-és beérkezési képeket (a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) is elérhetők), vagy a fenti lépésekkel lekérheti az egyes dokumentumok sas URL-címét a blob Storage-ban. 

> [!NOTE]
> Az útmutatóban szereplő kódrészletek az URL-címek által elért távoli űrlapokat használják. Ha ehelyett a helyi űrlapos dokumentumokat szeretné feldolgozni, tekintse meg a kapcsolódó módszereket a [dokumentációban](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

## <a name="recognize-form-content"></a>Űrlap tartalmának felismerése

Az űrlap-felismerő használatával felismerheti a dokumentumokban szereplő táblákat, vonalakat és szavakat, anélkül, hogy egy modellt kellene betanítania.

Egy adott URL-címen található fájl tartalmának felismeréséhez használja a `begin_recognize_content` metódust. A visszaadott érték objektumok gyűjteménye `FormPage` : egyet a beküldött dokumentum minden lapján. Az alábbi kód megismétli ezeket az objektumokat, és kinyomtatja a kinyert kulcs/érték párokat és a táblák adatait.

```Python
formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf"

poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
page = poller.result()

table = page[0].tables[0] # page 1, table 1
print("Table found on page {}:".format(table.page_number))
for cell in table.cells:
    print("Cell text: {}".format(cell.text))
    print("Location: {}".format(cell.bounding_box))
    print("Confidence score: {}\n".format(cell.confidence))
```

### <a name="output"></a>Kimenet

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0

...

```

## <a name="recognize-receipts"></a>Visszaigazolások felismerése

Ez a szakasz bemutatja, hogyan ismerheti fel és kinyerheti az Egyesült államokbeli nyugták közös mezőit egy előre képzett beérkezési modell használatával. A beérkezések URL-címről való felismeréséhez használja a `begin_recognize_receipts_from_url` metódust. 

```python
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"

poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
result = poller.result()

for receipt in result:
    for name, field in receipt.fields.items():
        if name == "Items":
            print("Receipt Items:")
            for idx, items in enumerate(field.value):
                print("...Item #{}".format(idx + 1))
                for item_name, item in items.value.items():
                    print("......{}: {} has confidence {}".format(item_name, item.value, item.confidence))
        else:
            print("{}: {} has confidence {}".format(name, field.value, field.confidence))
```

### <a name="output"></a>Kimenet

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan lehet a modelleket saját adataival betanítani. A betanított modellek olyan strukturált adatokat tudnak kialakítani, amelyek az eredeti dokumentum kulcs/érték kapcsolatait tartalmazzák. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például az [űrlap-felismerő minta feliratozási eszközét](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték felismeréséhez a betanítási dokumentumok manuális címkézése nélkül.

A következő kód a betanítási ügyfelet használja a `begin_training` modell betanításához egy adott dokumentumon. A visszaadott `CustomFormModel` objektum a modell által felismerhető űrlap-típusokkal és az egyes űrlapokból kinyerhető mezőkkel kapcsolatos információkat tartalmaz. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

```python
# To train a model you need an Azure Storage account.
# Use the SAS URL to access your training files.
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"

poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=False)
model = poller.result()

print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Training started on: {}".format(model.training_started_on))
print("Training completed on: {}".format(model.training_completed_on))

print("\nRecognized fields:")
for submodel in model.submodels:
    print(
        "The submodel with form type '{}' has recognized the following fields: {}".format(
            submodel.form_type,
            ", ".join(
                [
                    field.label if field.label else name
                    for name, field in submodel.fields.items()
                ]
            ),
        )
    )

# Training result information
for doc in model.training_documents:
    print("Document name: {}".format(doc.name))
    print("Document status: {}".format(doc.status))
    print("Document page count: {}".format(doc.page_count))
    print("Document errors: {}".format(doc.errors))
```

### <a name="output"></a>Kimenet

A [PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)-ból elérhető betanítási adatokkal betanított modell kimenete.

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

Egyéni modelleket is betaníthat, ha manuálisan címkézi a betanítási dokumentumokat. A címkékkel való képzés bizonyos helyzetekben jobb teljesítményt eredményez. A visszaadott `CustomFormModel` érték jelzi a modell által kinyerhető mezőket, valamint az egyes mezők becsült pontosságát. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

> [!IMPORTANT]
> Ha címkéket szeretne betanítani, `\<filename\>.pdf.labels.json` a blob Storage-tárolóban speciális címke-információs fájlokkal () kell rendelkeznie a betanítási dokumentumok mellett. Az [űrlap-felismerő minta címkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkék létrehozásához. Ha megkapta őket, meghívhatja a `begin_training` függvényt a következőre beállított *use_training_labels* paraméterrel: `true` .

```python
# To train a model you need an Azure Storage account.
# Use the SAS URL to access your training files.
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"

poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=True)
model = poller.result()

print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Training started on: {}".format(model.training_started_on))
print("Training completed on: {}".format(model.training_completed_on))

print("\nRecognized fields:")
for submodel in model.submodels:
    print(
        "The submodel with form type '{}' has recognized the following fields: {}".format(
            submodel.form_type,
            ", ".join(
                [
                    field.label if field.label else name
                    for name, field in submodel.fields.items()
                ]
            ),
        )
    )

# Training result information
for doc in model.training_documents:
    print("Document name: {}".format(doc.name))
    print("Document status: {}".format(doc.status))
    print("Document page count: {}".format(doc.page_count))
    print("Document errors: {}".format(doc.errors))
```

### <a name="output"></a>Kimenet

A [PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)-ból elérhető betanítási adatokkal betanított modell kimenete.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a szakasz azt mutatja be, hogyan lehet kinyerni a kulcs/érték információkat és az egyéb tartalmakat az egyéni űrlapok típusaiból, a saját űrlapjaival betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az ID-t az alábbi metódusba tudja adni. Lásd a [modell betanítása](#train-a-model-without-labels) szakaszt.

A `begin_recognize_custom_forms_from_url` metódust fogja használni. A visszaadott érték objektumok gyűjteménye `RecognizedForm` : egyet a beküldött dokumentum minden lapján. A következő kód kinyomtatja az elemzési eredményeket a-konzolra. Kinyomtatja az egyes felismert mezőket és a hozzá tartozó értékeket, valamint a megbízhatósági pontszámot.

```python
# Model ID from when you trained your model.
model_id = "<your custom model id>"

poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model_id, form_url=formUrl)
result = poller.result()

for recognized_form in result:
    print("Form type: {}".format(recognized_form.form_type))
    for name, field in recognized_form.fields.items():
        print("Field '{}' has label '{}' with value '{}' and a confidence score of {}".format(
            name,
            field.label_data.text if field.label_data else name,
            field.value,
            field.confidence
        ))
```

### <a name="output"></a>Kimenet

Az előző példa modelljét használva a következő kimenet van megadva.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="manage-your-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>A FormRecognizer-erőforrás fiókban található modellek számának megkeresése

A következő kódrészlet ellenőrzi, hogy az űrlap-felismerő fiókban hány modell lett mentve, és összehasonlítja azt a fiókra vonatkozó korláttal.

```python
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
```

### <a name="output"></a>Kimenet

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Az erőforrás-fiókban jelenleg tárolt modellek listázása

A következő kódrészlet felsorolja a fiókban lévő aktuális modelleket, és kiírja az adataikat a-konzolra. Emellett az első modellre mutató hivatkozást is ment.

```python
# Next, we get a paged list of all of our custom models
custom_models = form_training_client.list_custom_models()

print("We have models with the following ids:")

# Let's pull out the first model
first_model = next(custom_models)
print(first_model.model_id)
for model in custom_models:
    print(model.model_id)
```

### <a name="output"></a>Kimenet

Ez egy minta kimenet a teszt fiókhoz.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Adott modell beszerzése a modell AZONOSÍTÓjának használatával

A következő kódrészlet az előző szakaszban mentett modell AZONOSÍTÓját használja, és a használatával kéri le a modell részleteit.

```python
model_id = "<model_id from the Train a Model sample>"

custom_model = form_training_client.get_custom_model(model_id=model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Training started on: {}".format(custom_model.training_started_on))
print("Training completed on: {}".format(custom_model.training_completed_on))
```

### <a name="output"></a>Kimenet

Ez az előző példában létrehozott egyéni modell kimenete.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrás-fiókból

Az AZONOSÍTÓra hivatkozva egy modellt is törölhet a fiókjából. Ez a kód törli az előző szakaszban használt modellt.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást bármikor futtathatja, ha a rövid útmutatóban szereplő összes funkcióval elolvasta ezt a parancsot:

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="general"></a>Általános kérdések

Az űrlap-felismerő ügyféloldali kódtára az [Azure Core](https://aka.ms/azsdk-python-azure-core)-ban definiált kivételeket fogja növelni.

## <a name="logging"></a>Naplózás

Ez a függvénytár a [szabványos naplózási függvénytárat](https://docs.python.org/3/library/logging.html) használja a naplózáshoz. A HTTP-munkamenetek (URL-címek, fejlécek stb.) alapszintű információit a rendszer az információs szinten naplózza.

A HIBAKERESÉSi szint részletes naplózása, beleértve a kérés/válasz törzseit és a nem kitakart fejléceket is, a kulcsszó argumentummal rendelkező ügyfélen engedélyezhető `logging_enable` :

```python
import sys
import logging
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "https://<my-custom-subdomain>.cognitiveservices.azure.com/"
credential = AzureKeyCredential("<api_key>")

# This client will log detailed information about its HTTP sessions, at DEBUG level
form_recognizer_client = FormRecognizerClient(endpoint, credential, logging_enable=True)
```

Hasonlóképpen, `logging_enable` egy művelet részletes naplózását is engedélyezheti, még akkor is, ha nincs engedélyezve az ügyfél számára:

```python
poller = form_recognizer_client.begin_recognize_receipts(receipt, logging_enable=True)
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az űrlap felismerő Python ügyféloldali függvénytárát használta a modellek tanításához és az űrlapok különböző módokon történő elemzéséhez. Következő lépésként Ismerkedjen meg a jobb betanítási adatkészlet létrehozásával és a pontosabb modellek előállításával.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](../../overview.md)
