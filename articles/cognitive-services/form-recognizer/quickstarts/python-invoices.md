---
title: 'Gyors útmutató: számlázási adatok kinyerése a Python-Form felismerővel'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő REST API a Python használatával kinyerheti az adatokból a számlákat
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/12/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 7c6b5406728817c3dd700ec285d9af77c9d3cf60
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "96009313"
---
# <a name="quickstart-extract-invoice-data-using-the-form-recognizer-rest-api-with-python"></a>Gyors útmutató: számlázási adatok kinyerése az űrlap-felismerő REST API és a Python használatával

Ebben a rövid útmutatóban az Azure űrlap-felismerő REST API a Python használatával kinyeri és azonosítja a kapcsolódó információkat a számlákon.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőket kell tennie:
- [Python](https://www.python.org/downloads/) telepítve (ha helyileg szeretné futtatni a mintát).
- Egy számlázási dokumentum. Ehhez a rövid útmutatóhoz a [minta számlát](../media/sample-invoice.jpg) is használhatja.

> [!NOTE]
> Ez a rövid útmutató egy helyi fájlt használ. Az URL-cím alapján elérhető számlázási dokumentum használatához tekintse meg a [dokumentációt](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeInvoiceAsync).

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-an-invoice"></a>Számla elemzése

A számla elemzésének megkezdéséhez hívja meg a **[számla elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** API-t az alábbi Python-szkript használatával. A szkript futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `<Endpoint>` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le az `<subscription key>` elemet az előző lépésből másolt előfizetési kulcsra.
1. A helyére írja `<path to your invoice>` be azt a helyi elérési utat, ahol a minta számla el lett mentve.

    ```python
        ########### Python Form Recognizer Async Invoice #############
    
        import json
        import time
        from requests import get, post
        
        # Endpoint URL
        endpoint = r"<Endpoint>"
        apim_key = "<subscription key>"
        post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
        source = r"<path to your invoice>"
        
        headers = {
            # Request headers
            'Content-Type': '<file type>',
            'Ocp-Apim-Subscription-Key': apim_key,
        }
        
        params = {
            "includeTextDetails": True
            "locale": "en-US"
        }
        
        with open(source, "rb") as f:
            data_bytes = f.read()
        
        try:
            resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
            if resp.status_code != 202:
                print("POST analyze failed:\n%s" % resp.text)
                quit()
            print("POST analyze succeeded:\n%s" % resp.headers)
            get_url = resp.headers["operation-location"]
        except Exception as e:
            print("POST analyze failed:\n%s" % str(e))
            quit()
    ```

1. Mentse a kódot egy. file kiterjesztésű fájlba. Például: *Form-Recognizer-Invoice.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognizer-invoice.py`.

Egy olyan választ fog kapni `202 (Success)` , amely egy **művelet – hely** fejlécet tartalmaz, amelyet a szkript a konzolra fog nyomtatni. Ez a fejléc egy műveleti azonosítót tartalmaz, amely segítségével lekérdezheti az aszinkron művelet állapotát, és lekérheti az eredményeket. A következő példában szereplő sztring a `operations/` művelet azonosítója.

## <a name="get-the-invoice-results"></a>A számla eredményeinek beolvasása

Miután megismerte az elemzési **számla** API-t, hívja meg a **[Get elemzése számla eredménye](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** API-t a művelet és a kinyert adatmennyiség állapotának lekéréséhez. Adja hozzá a következő kódot a Python-szkript aljához. Ez a műveleti azonosító értékét használja egy új API-hívásban. Ez a parancsfájl rendszeres időközönként meghívja az API-t, amíg az eredmények elérhetővé nem válnak. Javasoljuk, hogy egy vagy több másodperces intervallumot válasszon.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Invoice results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Invoice Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Invoice Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Mentse a parancsfájlt.
1. Ismét a `python` paranccsal futtassa a mintát. Például: `python form-recognizer-invoice.py`.

### <a name="examine-the-response"></a>A válasz vizsgálata

A parancsfájl addig kinyomtatja a válaszokat a konzolra, amíg az elemzési **számla** művelete be nem fejeződik. Ezután JSON formátumban fogja kinyomtatni a kinyert szöveges adatfájlokat. A `"readResults"` mező a számlából kinyert szöveg minden sorát tartalmazza, a `"pageResults"` tartalmazza a számlából kinyert táblákat és kiválasztási jeleket, a mező pedig a `"documentResults"` számla legfontosabb részeire vonatkozó kulcs/érték információkat tartalmazza.

Tekintse meg az alábbi számlázási dokumentumot és a hozzá tartozó JSON-kimenetet:

* [Minta számla](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)
* [Példa a számla JSON-kimenetére](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)


### <a name="sample-python-script-to-extract-invoice-or-a-batch-of-invoices-into-a-csv-file"></a>Példa Python-szkriptre a számla vagy a számlák kötegének CSV-fájlba való kinyeréséhez

Ez a példa Python-szkript bemutatja, hogyan kezdheti el a számla API használatát. Egyetlen számlával is futtatható paraméterként vagy mappaként, és Kinyeri a ".invoice.json" nevű JSON-fájlt és egy CSV-fájlt _invoiceResutls.csv_ a kinyert értékek eredményeivel. Egy mappa futtatásakor a rendszer a "PDF", a "jpg", a "JPEG", a "png", a "BMP", a "TIF", a "TIFF" fájlokon és az API-val futtatja őket. 
 
```python
########### Python Form Recognizer Async Invoice #############

import json
import time
import os
import ntpath
import sys
from requests import get, post
import csv


def analyzeInvoice(filename):
    invoiceResultsFilename = filename + ".invoice.json"

    # do not run analyze if .invoice.json file is present on disk
    if os.path.isfile(invoiceResultsFilename):
        with open(invoiceResultsFilename) as json_file:
            return json.load(json_file)

    # Endpoint URL
    #endpoint = r"<Endpoint>"
    #apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
    headers = {
        # Request headers
        'Content-Type': 'application/octet-stream',
        'Ocp-Apim-Subscription-Key': apim_key,
    }

    params = {
        "includeTextDetails": True
    }

    with open(filename, "rb") as f:
        data_bytes = f.read()

    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            return None
        print("POST analyze succeeded: %s" % resp.headers["operation-location"])
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        return None

    n_tries = 50
    n_try = 0
    wait_sec = 6

    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
            resp_json = json.loads(resp.text)
            if resp.status_code != 200:
                print("GET Invoice results failed:\n%s" % resp_json)
                return None
            status = resp_json["status"]
            if status == "succeeded":
                print("Invoice analysis succeeded.")
                with open(invoiceResultsFilename, 'w') as outfile:
                    json.dump(resp_json, outfile, indent=4)
                return resp_json
            if status == "failed":
                print("Analysis failed:\n%s" % resp_json)
                return None
            # Analysis still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1     
        except Exception as e:
            msg = "GET analyze results failed:\n%s" % str(e)
            print(msg)
            return None

    return resp_json

def parseInvoiceResults(resp_json):
    docResults = resp_json["analyzeResult"]["documentResults"]
    invoiceResult = {}
    for docResult in docResults:
        for fieldName, fieldValue in sorted(docResult["fields"].items()):
            valueFields = list(filter(lambda item: ("value" in item[0]) and ("valueString" not in item[0]), fieldValue.items()))
            invoiceResult[fieldName] = fieldValue["text"]            
            if len(valueFields) == 1:
                print("{0:26} : {1:50}      NORMALIZED VALUE: {2}".format(fieldName , fieldValue["text"], valueFields[0][1]))
                invoiceResult[fieldName + "_normalized"] = valueFields[0][1]
            else:
                print("{0:26} : {1}".format(fieldName , fieldValue["text"]))
    print("")
    return invoiceResult

def main(argv):
    if (len(argv)  != 2):
        print("ERROR: Please provide invoice filename or root directory with invoice PDFs/images as an argument to the python script")
        return

    # list of invoice to analyze
    invoiceFiles = []
    csvPostfix = '-invoiceResults.csv'
    if os.path.isfile(argv[1]):
        # Single invoice
        invoiceFiles.append(argv[1])
        csvFileName = argv[1] + csvPostfix
    else:
        # Folder of invoices
        supportedExt = ['.pdf', '.jpg','.jpeg','.tif','.tiff','.png','.bmp']
        invoiceDirectory = argv[1]
        csvFileName = os.path.join(invoiceDirectory, os.path.basename(os.path.abspath(invoiceDirectory)) + csvPostfix)
        for root, directories, filenames in os.walk(invoiceDirectory):
            for invoiceFilename in filenames:
                ext = os.path.splitext(invoiceFilename)[-1].lower()
                if ext in supportedExt:
                    fullname = os.path.join(root, invoiceFilename)
                    invoiceFiles.append(fullname)
                    
    with open(csvFileName, mode='w', newline='\n', encoding='utf-8') as csv_file:
        fieldnames = ['Filename',
                      'FullFilename','InvoiceTotal','InvoiceTotal_normalized','AmountDue','AmountDue_normalized','SubTotal','SubTotal_normalized','TotalTax','TotalTax_normalized','CustomerName','VendorName',
                      'InvoiceId','CustomerId','PurchaseOrder','InvoiceDate','InvoiceDate_normalized','DueDate','DueDate_normalized',
                      'VendorAddress','VendorAddressRecipient','BillingAddress','BillingAddressRecipient','ShippingAddress','ShippingAddressRecipient','CustomerAddress','CustomerAddressRecipient','ServiceAddress','ServiceAddressRecipient','RemittanceAddress','RemittanceAddressRecipient', 'ServiceStartDate','ServiceStartDate_normalized','ServiceEndDate','ServiceEndDate_normalized','PreviousUnpaidBalance','PreviousUnpaidBalance_normalized']
        writer = csv.DictWriter(csv_file, fieldnames=fieldnames)
        writer.writeheader()
        counter = 0
        for invoiceFullFilename in invoiceFiles:
            counter = counter + 1
            invoiceFilename = ntpath.basename(invoiceFullFilename)
            print("----- Processing {0}/{1} : {2} -----".format(counter, len(invoiceFiles),invoiceFullFilename))
            
            resp_json = analyzeInvoice(invoiceFullFilename)

            if (resp_json is not None):
                invoiceResults = parseInvoiceResults(resp_json)
                invoiceResults["FullFilename"] = invoiceFullFilename
                invoiceResults["Filename"] = invoiceFilename
                writer.writerow(invoiceResults)
                    
if __name__ == '__main__':
    main(sys.argv)
```

1. Mentse a kódot egy. file kiterjesztésű fájlba. Például: *Form-Recognizer-Invoice-to-CSV.py*.
1. Cserélje le `<Endpoint>` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le az `<subscription key>` elemet az előző lépésből másolt előfizetési kulcsra.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. `python form-recognizer-invoice.py {file name or folder name}`A Python-szkript például egyetlen számlával vagy egy, a paraméterrel rendelkező mappával futtatható, és a ".invoice.json" nevű JSON-fájlt és a számlákból kinyert értékeket a "-invoiceResults.csv" CSV-fájlba küldi az eredményekkel. Egy mappa futtatásakor a rendszer a "PDF", a "jpg", a "JPEG", a "png", a "BMP", a "TIF", a "TIFF" fájlokon és az API-val futtatja őket.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az űrlap-felismerő REST APIt használta a Python használatával a tartalom kinyeréséhez a számlákon. Következő lépésként tekintse meg a dokumentációt az űrlap-felismerő API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeInvoiceAsync)

   
