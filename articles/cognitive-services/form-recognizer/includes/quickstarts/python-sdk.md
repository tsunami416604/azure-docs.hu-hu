---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtár a Pythonhoz'
description: Ebben a rövid útmutatóban megismerheti a Pythonhoz készült űrlap-felismerő ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/07/2020
ms.author: pafarley
ms.openlocfilehash: 1a2c5bfb2866e2cc28c013be60dbe791edeb9ac1
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997594"
---
[Dokumentáció](https://docs.microsoft.com/python/api/overview/azure/formrecognizer?view=azure-python-preview)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Csomag (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Példák](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört. Ebben a rövid útmutatóban használhatja a [minta adatkészletének](https://go.microsoft.com/fwlink/?linkid=2090451) **vonattal** mappában található fájlokat.
* [Python 2,7, vagy 3,5 vagy újabb](https://www.python.org/)

## <a name="setting-up"></a>Beállítás

### <a name="create-a-form-recognizer-azure-resource"></a>Űrlap-felismerő Azure-erőforrás létrehozása

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Környezeti változók létrehozása

[!INCLUDE [environment-variables](../environment-variables.md)]


### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. Ezután importálja a következő kódtárakat.

```python
import os
import azure.ai.formrecognizer
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import ResourceNotFoundError
```

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a rendszerhéjat.

```python
endpoint = os.environ["FORM_RECOGNIZER_ENDPOINT"]
key = os.environ["FORM_RECOGNIZER_KEY"]
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install azure-ai-formrecognizer
```

<!-- 
tbd object model
-->

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Pythonhoz készült űrlap-felismerő ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Űrlap tartalmának felismerése](#recognize-form-content)
* [Visszaigazolások felismerése](#recognize-receipts)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Itt két ügyfél-objektumot kell hitelesítenie a fent megadott előfizetési változók használatával. **AzureKeyCredential** objektumot fog használni, így ha szükséges, az API-kulcsot új ügyfélalkalmazások létrehozása nélkül is frissítheti.

```python
form_recognizer_client = FormRecognizerClient(endpoint=self.endpoint, credential=AzureKeyCredential(self.key))

form_training_client = FormTrainingClient(self.endpoint, AzureKeyCredential(self.key))
```

## <a name="define-variables"></a>Változók meghatározása

> [!NOTE]
> Az útmutatóban szereplő kódrészletek az URL-címek által elért távoli űrlapokat használják. Ha ehelyett a helyi űrlapos dokumentumokat szeretné feldolgozni, tekintse meg a kapcsolódó módszereket a [dokumentációban](https://docs.microsoft.com/python/api/overview/azure/formrecognizer?view=azure-python-preview) és a [mintákban](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Emellett a képzési és tesztelési adatok URL-címeihez is hozzá kell adnia a hivatkozásokat.
* Az egyéni modell betanítási adataihoz tartozó SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A formátumnak a következőket kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Egy olyan űrlap URL-címének lekéréséhez, amely a teszteléshez használható, a fenti lépésekkel lekérheti egy egyedi dokumentum SAS URL-címét a blob Storage-ban. Vagy vegyen fel egy máshol található dokumentum URL-címét.
* A fenti módszer használatával beolvashatja a beérkezési képek URL-címét is, vagy használhatja a mintaként megadott képet.

```python
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"
formUrl = "<SAS-URL-of-a-form-in-blob-storage>"
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"
```

## <a name="recognize-form-content"></a>Űrlap tartalmának felismerése

Az űrlap-felismerő használatával felismerheti a dokumentumokban szereplő táblákat, vonalakat és szavakat, anélkül, hogy egy modellt kellene betanítania.

Egy adott URL-címen található fájl tartalmának felismeréséhez használja a **begin_recognize_content** metódust.

```Python
poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
contents = poller.result()
```

A visszaadott érték egy **FormPage** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához. Az alábbi kód megismétli ezeket az objektumokat, és kinyomtatja a kinyert kulcs/érték párokat és a táblák adatait.

```python
for idx, content in enumerate(contents):
    print("----Recognizing content from page #{}----".format(idx))
    print("Has width: {} and height: {}, measured with unit: {}".format(
        content.width,
        content.height,
        content.unit
    ))
    for table_idx, table in enumerate(content.tables):
        print("Table # {} has {} rows and {} columns".format(table_idx, table.row_count, table.column_count))
        for cell in table.cells:
            print("...Cell[{}][{}] has text '{}' within bounding box '{}'".format(
                cell.row_index,
                cell.column_index,
                cell.text,
                format_bounding_box(cell.bounding_box)
            ))
    for line_idx, line in enumerate(content.lines):
        print("Line # {} has word count '{}' and text '{}' within bounding box '{}'".format(
            line_idx,
            len(line.words),
            line.text,
            format_bounding_box(line.bounding_box)
        ))
    print("----------------------------------------")
```

A fenti kód egy segítő függvényt használ a `format_bounding_box` határolókeret koordinátáinak leegyszerűsítéséhez. Külön definiálja:

```python
def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])
```

## <a name="recognize-receipts"></a>Visszaigazolások felismerése

Ez a szakasz bemutatja, hogyan ismerheti fel és kinyerheti az Egyesült államokbeli nyugták közös mezőit egy előre képzett beérkezési modell használatával. A visszaigazolások URL-címről való felismeréséhez használja a **begin_recognize_receipts_from_url** metódust. 

```python
poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
receipts = poller.result()
```

A visszaadott érték egy **USReceipt** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához. A következő kódrészlet az alapszintű bevételezési adatokat nyomtatja ki a konzolra.

```python
for idx, receipt in enumerate(receipts):
    print("--------Recognizing receipt #{}--------".format(idx))
    print("Receipt Type: {} has confidence: {}".format(receipt.receipt_type.type, receipt.receipt_type.confidence))
    print("Merchant Name: {} has confidence: {}".format(receipt.merchant_name.value, receipt.merchant_name.confidence))
    print("Transaction Date: {} has confidence: {}".format(receipt.transaction_date.value, receipt.transaction_date.confidence))
```

A kód következő blokkja megismétli a nyugtán észlelt egyes elemeket, és kiírja az adataikat a-konzolra.


```python
    print("Receipt items:")
    for item in receipt.receipt_items:
        print("...Item Name: {} has confidence: {}".format(item.name.value, item.name.confidence))
        print("...Item Quantity: {} has confidence: {}".format(item.quantity.value, item.quantity.confidence))
        print("...Individual Item Price: {} has confidence: {}".format(item.price.value, item.price.confidence))
        print("...Total Item Price: {} has confidence: {}".format(item.total_price.value, item.total_price.confidence))
```

Végül a kód utolsó blokkja kinyomtatja a fő visszaigazolás további részleteit.

```python
    print("Subtotal: {} has confidence: {}".format(receipt.subtotal.value, receipt.subtotal.confidence))
    print("Tax: {} has confidence: {}".format(receipt.tax.value, receipt.tax.confidence))
    print("Tip: {} has confidence: {}".format(receipt.tip.value, receipt.tip.confidence))
    print("Total: {} has confidence: {}".format(receipt.total.value, receipt.total.confidence))
    print("--------------------------------------")
```


## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan lehet a modelleket saját adataival betanítani. A betanított modellek olyan strukturált adatokat tudnak kialakítani, amelyek az eredeti dokumentum kulcs/érték kapcsolatait tartalmazzák. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például az [űrlap-felismerő minta feliratozási eszközét](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték felismeréséhez a betanítási dokumentumok manuális címkézése nélkül.

A következő kód a betanítási ügyfelet használja a **begin_train_model** függvénnyel egy modell betanítására egy adott dokumentumon.

```python
poller = form_training_client.begin_train_model(self.trainingDataUrl)
model = poller.result()
```

A visszaadott **CustomFormModel** objektum a modell által felismerhető űrlap-típusokkal és az egyes űrlapokból kinyerhető mezőkkel kapcsolatos információkat tartalmaz. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.created_on))
print("Last modified: {}".format(model.last_modified))

print("Recognized fields:")
# Looping through the submodels, which contains the fields they were trained on
for submodel in model.models:
    print("...The submodel has form type '{}'".format(submodel.form_type))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have label '{}'".format(
            name, field.label
        ))
```

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

Egyéni modelleket is betaníthat, ha manuálisan címkézi a betanítási dokumentumokat. A címkékkel való képzés bizonyos helyzetekben jobb teljesítményt eredményez. 

> [!IMPORTANT]
> Ha címkéket szeretne betanítani, a blob Storage-tárolóban speciális címke-információs fájlokat (* \<filename\> . PDF. labels. JSON*) kell használnia a betanítási dokumentumok mellett. Az [űrlap-felismerő minta címkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkék létrehozásához. Miután megadta őket, meghívhatja a **begin_train_model** függvényt a következőre beállított *use_labels* paraméterrel: `true` .

```python
poller = form_training_client.begin_train_model(self.trainingDataUrl, use_labels=True)
model = poller.result()
```

A visszaadott **CustomFormModel** jelzi a modell által kinyerhető mezőket, valamint az egyes mezők becsült pontosságát. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.created_on))
print("Last modified: {}".format(model.last_modified))

print("Recognized fields:")
# looping through the submodels, which contains the fields they were trained on
# The labels are based on the ones you gave the training document.
for submodel in model.models:
    print("...The submodel with form type {} has accuracy '{}'".format(submodel.form_type, submodel.accuracy))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have name '{}' with an accuracy of {}".format(
            name, field.name, field.accuracy
        ))
```

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a szakasz azt mutatja be, hogyan lehet kinyerni a kulcs/érték információkat és az egyéb tartalmakat az egyéni űrlapok típusaiból, a saját űrlapjaival betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az ID-t az alábbi metódusba tudja adni. Lásd a [modell betanítása](#train-a-model-without-labels) szakaszt.

A **begin_recognize_custom_forms_from_url** metódust fogja használni. A visszaadott érték egy **RecognizedForm** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához.

```python
# Make sure your form's type is included in the list of form types the custom model can recognize
poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model.model_id, url=formUrl)
forms = poller.result()
```

A következő kód kinyomtatja az elemzési eredményeket a-konzolra. Kinyomtatja az egyes felismert mezőket és a hozzá tartozó értékeket, valamint a megbízhatósági pontszámot.

```python
for idx, form in enumerate(forms):
    print("--------Recognizing Form #{}--------".format(idx))
    print("Form {} has type {}".format(idx, form.form_type))
    for name, field in form.fields.items():
        # each field is of type FormField
        # The value of the field can also be a FormField, or a list of FormFields
        # In our sample, it is just a FormField.
        print("...Field '{}' has value '{}' with a confidence score of {}".format(
            name, field.value, field.confidence
        ))
        # label data is populated if you are using a model trained with unlabeled data, since the service needs to make predictions for
        # labels if not explicitly given to it.
        if field.label_data:
            print("...Field '{}' has label '{}' with a confidence score of {}".format(
                name,
                field.label_data.text,
                field.confidence
            ))
    print("-----------------------------------")
```

## <a name="manage-your-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>A FormRecognizer-erőforrás fiókban található modellek számának megkeresése

A következő kódrészlet ellenőrzi, hogy az űrlap-felismerő fiókban hány modell lett mentve, és összehasonlítja azt a fiókra vonatkozó korláttal.

```python
# First, we see how many custom models we have, and what our limit is
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Az erőforrás-fiókban jelenleg tárolt modellek listázása

A következő kódrészlet felsorolja a fiókban lévő aktuális modelleket, és kiírja az adataikat a-konzolra. Emellett az első modellre mutató hivatkozást is ment.

```python
# Next, we get a paged list of all of our custom models
custom_models = form_training_client.list_model_infos()

print("We have models with the following ids:")

# Let's pull out the first model
first_model = next(custom_models)
print(first_model.model_id)
for model in custom_models:
    print(model.model_id)
```

### <a name="get-a-specific-model-using-the-models-id"></a>Adott modell beszerzése a modell AZONOSÍTÓjának használatával

A következő kódrészlet az előző szakaszban mentett modell AZONOSÍTÓját használja, és a használatával kéri le a modell részleteit.

```python
# Now we'll get the first custom model in the paged list
custom_model = form_training_client.get_custom_model(model_id=first_model.model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Created on: {}".format(custom_model.created_on))
print("Last modified: {}".format(custom_model.last_modified))
```

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrás-fiókból

Az AZONOSÍTÓra hivatkozva egy modellt is törölhet a fiókjából. Ez a kód törli az előző szakaszban használt modellt.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

# Confirm deletion:
try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
}
```

## <a name="run-the-application"></a>Alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `python` fájlban található paranccsal.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

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

* [Mi a Form Recognizer?](../../overview.md)
* A jelen útmutatóban szereplő mintakód (és egyebek) megtalálható a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).
