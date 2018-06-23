---
title: Az Anomáliadetektálási kereső API használata a Python - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében Anomáliadetektálási kereső Python kognitív szolgáltatásban.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: c14916b0644edab613b298d6e71f8bbb9a6bb804
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348158"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Az Anomáliadetektálási kereső API használata a Python

Ez a cikk és a segítségével gyorsan mintakódok használatának megkezdésében a Anomáliadetektálási kereső API Python feladatnak az anomáliadetektálási eredmény idő adatsorozat beolvasása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Az anomáliadetektálási pontok első Anomáliadetektálási kereső API-val pythonos környezetekben 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Adatsorozat időadatok – példa

A példa az adatsorozat pontjainak idő a következőképpen történik.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Adatok elemzése és anomáliadetektálási pontok Python példa beolvasása

Győződjön meg arról, python3 telepítette, majd hozzon létre egy detect.py nevű python végrehajtható fájlt. A detect.py a kódot kell tartalmaznia. A kód végrehajtása, előtt ne felejtse el lecserélni a `[YOUR_SUBSCRIPTION_KEY]` értéke az érvényes előfizetés-kulccsal.
Cserélje le a `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` a adatpontokként.

```python
import requests
import json


def detect(url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(url, data=json.dumps(request_data), headers=headers)
    if response.status_code == 200:
        return json.loads(response.content.decode("utf-8"))
    else:
        print(response.status_code)
        raise Exception(response.text)


sample_data = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]"
endpont = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
subscription_key = "[YOUR_SUBSCRIPTION_KEY]"

result = detect(endpont, subscription_key, sample_data)
print(result)

```

### <a name="example-response"></a>Példaválasz

A sikeres válasz JSON-ban adja vissza. Mintaválasz a következőképpen történik.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-alkalmazás](../tutorials/python-tutorial.md)
