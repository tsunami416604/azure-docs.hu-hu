---
title: 'Gyors útmutató: névjegykártya-adatok kinyerése a Python-Form felismerővel'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő REST API a Python használatával kinyerheti az adatokból az angol nyelvű képeket az üzleti kártyákról.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 45e091fe1ed77a4efc90d426b1d9a2842ae00175
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88725446"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Gyors útmutató: névjegykártya-adatok kinyerése az űrlap-felismerő REST API és a Python használatával

Ebben a rövid útmutatóban az Azure űrlap-felismerő REST API a Python használatával kinyerheti és azonosíthatja az üzleti kártyákkal kapcsolatos releváns információkat.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőket kell tennie:
- [Python](https://www.python.org/downloads/) telepítve (ha helyileg szeretné futtatni a mintát).
- Egy névjegykártya-rendszerkép URL-címe. Ehhez a rövid útmutatóhoz [minta képet](../media/business-card-english.jpg) is használhat.

> [!NOTE]
> Ez a rövid útmutató egy, az URL-címen keresztül elérhető távoli névjegykártya-rendszerképet használ. Ha inkább helyi fájlokat szeretne használni, tekintse meg a [dokumentációt](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync).

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Névjegykártya elemzése

A névjegykártya elemzésének megkezdéséhez hívja meg a **[Business Card API elemzése](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** az alábbi Python-szkripttel. A szkript futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `<Endpoint>` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le a `<your business card URL>` címet egy névjegykártya-rendszerkép URL-címére.
1. Cserélje le az `<subscription key>` elemet az előző lépésből másolt előfizetési kulcsra.

    ```python
    ########### Python Form Recognizer Async Business cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults"
    source = r"<path to your business card>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
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

1. Mentse a kódot egy. file kiterjesztésű fájlba. Például: *Form-Recognizer-BusinessCards.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognizer-businesscards.py`.

Egy olyan választ fog kapni `202 (Success)` , amely egy **művelet – hely** fejlécet tartalmaz, amelyet a szkript a konzolra fog nyomtatni. Ez a fejléc egy műveleti azonosítót tartalmaz, amely segítségével lekérdezheti az aszinkron művelet állapotát, és lekérheti az eredményeket. A következő példában szereplő sztring a `operations/` művelet azonosítója.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults{operationID}
```

## <a name="get-the-business-card-results"></a>A névjegykártya eredményeinek beolvasása

Miután megismerte a **Business Card elemzése** API-t, hívja meg a **[Get elemezze Business Card result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** API-t, hogy lekérje a művelet állapotát és a kinyert adatmennyiséget. Adja hozzá a következő kódot a Python-szkript aljához. Ez a műveleti azonosító értékét használja egy új API-hívásban. Ez a parancsfájl rendszeres időközönként meghívja az API-t, amíg az eredmények elérhetővé nem válnak. Javasoljuk, hogy egy vagy több másodperces intervallumot válasszon.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
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
1. Ismét a `python` paranccsal futtassa a mintát. Például: `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>A válasz vizsgálata

A parancsfájl addig kinyomtatja a válaszokat a konzolra, amíg be nem fejeződik a **névjegykártya elemzése** művelet. Ezután JSON formátumban fogja kinyomtatni a kinyert szöveges adatfájlokat. A `"recognitionResults"` mező a névjegykártyából kinyert szöveg minden sorát tartalmazza, a mező pedig a `"understandingResults"` névjegykártyához tartozó legfontosabb részekre vonatkozó kulcs/érték információkat tartalmazza.

![A contoso vállalat üzleti kártyája](../media/business-card-english.jpg)

A `"recognitionResults"` csomópont tartalmazza az összes felismert szöveget. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. A `"understandingResults"` csomópont tartalmazza a modell által felderített névjegykártya-specifikus értékeket. Itt talál hasznos kulcs/érték párokat, mint például az adó, a teljes, a kereskedelmi címek és így tovább.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az űrlap-felismerő REST APIt használta a Python használatával a névjegykártya tartalmának kinyeréséhez. Következő lépésként tekintse meg a dokumentációt az űrlap-felismerő API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)
