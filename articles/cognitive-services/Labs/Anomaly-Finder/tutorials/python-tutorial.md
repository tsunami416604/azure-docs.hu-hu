---
title: 'Oktatóanyag: Rendellenességek észlelése, a Python'
titlesuffix: Azure Cognitive Services
description: Megismerkedhet egy Python-notebookkal, amely az Anomaly Detection API-t használja. Ha az eredeti adatpontokat elküldi az API-nak, visszakapja a várt értéket és az attól eltérő pontokat.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 5a2fb54658599e0500944aaae9225f314277f9da
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539088"
---
# <a name="tutorial-anomaly-detection-with-python-application"></a>Oktatóanyag: Rendellenességek észlelése, a Python-alkalmazás

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ebből az oktatóanyagból megtudhatja, hogyan használható az Anomaly Detection API a Pythonban, és hogyan vizualizálhatók az eredmények népszerű kódtárak használatával. Használja a Jupytert az oktatóanyag futtatásához és a saját adatainak az előfizetési kulccsal való kipróbálásához. Az interaktív Jupyter-notebookok használatával kapcsolatban lásd a [Jupyter dokumentációját](https://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Előfizetés az anomáliadetektálás használatára és előfizetői azonosító beszerzése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>A példakód letöltése

1. Keresse meg az [oktatóanyag notebookját a GitHubon](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Kattintson a zöld gombra az oktatóanyag klónozásához vagy letöltéséhez. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Az oktató notebook megnyitása a Jupyterben

1. Nyisson meg egy parancssort, és lépjen be a python-sample mappába.
2. Futtassa a parancssori felületen a Jupyter notebook parancsot, amely elindítja a Jupytert.
3. A Jupyter ablakában kattintson az <em>Anomaly Detection API Example.ipynb</em> fájlra az oktató notebook megnyitásához.   

## <a name="running-the-tutorial"></a>Az oktatóanyag futtatása

Az oktatóanyag használatához szüksége lesz egy előfizetési kulcsra az Anomaly Detection API-hoz. Lépjen az Előfizetés oldalra a feliratkozáshoz. A „Bejelentkezés” oldalon jelentkezzen be a Microsoft-fiókjával, majd ezután feliratkozhat és beszerezheti a kulcsait. A feliratkozási folyamat végeztével másolja be a kulcsot a notebook változókat tartalmazó szakaszába (az alább látható módon). Az elsődleges és a másodlagos kulcs egyaránt használható. Ügyeljen arra, hogy a kulcsot tegye idézőjelek közé, hogy a rendszer sztringként kezelje.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
