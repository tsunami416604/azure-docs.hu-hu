---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 0644dad9e8e6f2999acfa24ea1088207f6d5e692
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86028060"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>A kulcs-érték párok és táblák űrlapjainak elemzése

Ezután az újonnan betanított modellt fogja használni a dokumentumok elemzéséhez és a kulcs-érték párok és táblák kinyeréséhez. A következő kód egy új Python-parancsfájlban történő futtatásával hívja meg az elemzési **[űrlap](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API-ját. A szkript futtatása előtt végezze el a következő módosításokat:

1. Cserélje le az értékét `<file path>` az űrlap fájljának elérési útjára (például C:\temp\file.pdf). Ez egy távoli fájl URL-címe is lehet. Ebben a rövid útmutatóban a [minta adatkészletének](https://go.microsoft.com/fwlink/?linkid=2090451) **tesztelési** mappájában található fájlokat használhatja.
1. Cserélje le az `<model_id>` t az előző szakaszban kapott modell-azonosítóra.
1. Cserélje le `<endpoint>` az helyére az űrlapot felismerő előfizetési kulccsal beszerzett végpontot. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le `<file type>` a értéket a fájl típusára. Támogatott típusok: `application/pdf` , `image/jpeg` , `image/png` , `image/tiff` .
1. A `<subscription key>` helyére írja be az előfizetési kulcsot.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Mentse a kódot egy. file kiterjesztésű fájlba. Például: *Form-Recognizer-Analyze.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognizer-analyze.py`.

Az elemzési **űrlap** API meghívásakor a rendszer választ küld egy `201 (Success)` **műveleti hely** fejlécére. Ennek a fejlécnek az értéke egy azonosító, amelyet az elemzési művelet eredményeinek nyomon követéséhez fog használni. A fenti szkript kinyomtatja a fejléc értékét a konzolra.

## <a name="get-the-analyze-results"></a>Az elemzés eredményeinek beolvasása

Adja hozzá a következő kódot a Python-szkript aljához. Ez az előző hívás azonosító értékét használja egy új API-hívásban az elemzési eredmények lekéréséhez. Az elemzési **űrlap** művelete aszinkron módon történik, így a parancsfájl rendszeres időközönként meghívja az API-t, amíg az eredmények elérhetővé nem válnak. Javasoljuk, hogy egy vagy több másodperces intervallumot válasszon.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
