---
title: Az Anomáliadetektálási kereső API a Pythonnal – a Microsoft Cognitive Services használatával |} A Microsoft Docs
description: Get information és kód minták segítségével gyorsan az Python a Cognitive Services-használatának Anomáliaészlelő.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: eb64bb4daece44d33775dca7417c51b216e04614
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49455436"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Használja a Anomáliaészlelő API a Pythonnal

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ez a cikk bemutatja, és kódminták segítségével gyorsan az Anomáliadetektálási kereső API-használatának Python a feladatnak az anomáliadetektálási eredmény idősorozat-adatok beolvasása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Az Anomáliadetektálási kereső API-jával Python anomáliadetektálási pontok beolvasása 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Idősorozat-adatok – példa

A példában az adatsorozat adatpontjainak idő a következőképpen történik.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Adatok elemzése és anomáliadetektálási pontok Python-példát beolvasása

Ellenőrizze, hogy telepítve van a python3, majd hozzon létre egy python végrehajtható fájlt detect.py. Detect.py tartalmaznia kell az alábbi kódot. Mielőtt a kód hajtana végre, ne felejtse el kicserélni az `[YOUR_SUBSCRIPTION_KEY]` értéke az érvényes előfizetési kulccsal végzett.
Cserélje le a `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` az adatpontok együtt.

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

A rendszer JSON formátumban adja vissza a sikeres választ. Mintaválasz a következőképpen történik.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-alkalmazás](../tutorials/python-tutorial.md)
