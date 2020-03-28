---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 3c6059e131eadf1144fd189c47691b2352176745
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446442"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Kulcsérték-párok és táblák űrlapjainak elemzése

Ezután az újonnan betanított modell segítségével elemezhet egy dokumentumot, és kulcsérték-párokat és táblákat nyerhet ki belőle. Hívja meg az **[Űrlap elemzése](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** API-t a következő kód futtatásával egy új Python-parancsfájlban. A parancsfájl futtatása előtt hajtsa végre a következő módosításokat:

1. Cserélje `<file path>` le az űrlap fájlelérési útját (például C:\temp\file.pdf). Ez lehet egy távoli fájl URL-címe is. Ehhez a rövid útmutatóhoz használhatja a [mintaadatkészlet](https://go.microsoft.com/fwlink/?linkid=2090451) **Teszt** mappájában lévő fájlokat.
1. Cserélje `<model_id>` le az előző szakaszban kapott modellazonosítóra.
1. Cserélje `<endpoint>` le a kapott végpontra az Űrlapfelismerő előfizetési kulccsal. Ezt az Űrlapfelismerő erőforrás **áttekintése** lapon találja.
1. Cserélje `<file type>` le a fájltípusra. Támogatott `application/pdf`típusok: `image/jpeg` `image/png`, `image/tiff`, , .
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
    post_url = endpoint + "/formrecognizer/v2.0-preview/custom/models/%s/analyze" % model_id
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

1. Mentse a kódot egy .py kiterjesztésű fájlba. Például *form-recognizer-analyze.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognizer-analyze.py`.

Amikor meghívja az **Űrlap elemzése** API-t, `201 (Success)` egy **Operation-Location** fejléccel választ fog kapni. A fejléc értéke egy azonosító, amelyet az Elemzés művelet eredményeinek nyomon követésére használhat. A fenti parancsfájl kinyomtatja a fejléc értékét a konzolra.

## <a name="get-the-analyze-results"></a>Az Elemzés eredményeinek beszereznie

Adja hozzá a következő kódot a Python-parancsfájl aljához. Ez az új API-hívás előző hívásának azonosítóértékét használja az elemzési eredmények lekéréséhez. Az **Űrlap elemzése** művelet aszinkron, így ez a parancsfájl rendszeres időközönként meghívja az API-t, amíg az eredmények elérhetővé nem válikk. Javasoljuk, hogy egy másodperc vagy több időközt.

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
