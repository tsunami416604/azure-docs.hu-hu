---
title: Anomáliadetektálás Python-alkalmazás – a Microsoft Cognitive Services |} A Microsoft Docs
description: Fedezze fel a Microsoft Cognitive Services az Anomáliadetektáló API-t használó Python-jegyzetfüzetet. Küldés eredeti adatpontok API-hoz és a várt érték és anomáliadetektálási pontokat.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 87cd9e976d231291ad13acecf188cfd668d692b6
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248227"
---
# <a name="anomaly-detection-python-application"></a>Rendellenességek észlelése Python-alkalmazás

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ebből az oktatóanyagból megtudhatja, hogyan használható az Anomáliadetektáló API-t a Python és hogyan jelenítheti meg az eredményeket, népszerű kódtárak használatával. Az oktatóanyag futtatása a Jupyter használatával, és a saját adataival próbálja az előfizetési kulccsal végzett. Ismerje meg, hogyan interaktív Jupyter notebookok használatának első lépései, tekintse meg [Jupyter dokumentáció](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Fizessen elő a rendellenességek észlelése és a egy előfizetési kulcs lekérése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>A mintakód letöltése

1. Keresse meg a [github oktatóanyag notebook](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Kattintson a zöld gombot a Klónozás vagy letöltés az oktatóanyag. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Nyissa meg az oktatóanyag notebook Jupyter

1. Nyisson meg egy parancssort, és nyissa meg a mappa python-mintához.
2. Futtassa a parancsot a Jupyter notebook a parancssor használatával, amely elindítja a Jupyter.
3. A Jupyter ablakában kattintson a <em>rendellenességek észlelése API Example.ipynb</em> a oktatóanyag notebook megnyitásához.   

## <a name="running-the-tutorial"></a>Az oktatóanyag

Ez a jegyzetfüzet használatához szüksége lesz egy előfizetési kulcsot az Anomáliadetektáló API-t. Az előfizetés oldalán regisztráció. A "Sign-in" lapon a Microsoft-fiók használatával jelentkezzen be, és lesz regisztrációt és a kulcsokat. A regisztrációs folyamat befejezését követően illessze be a kulcsát a notebook (az alábbiakban találja), a változók szakaszban. Működik az elsődleges vagy másodlagos kulcsát. Ellenőrizze, hogy a kulcs tegye idézőjelek között, hogy egy karakterláncot.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
