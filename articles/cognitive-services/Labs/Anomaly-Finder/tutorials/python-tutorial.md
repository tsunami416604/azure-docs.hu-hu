---
title: Anomáliadetektálás Python-alkalmazás - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Megismerheti a Python notebook, amely az Anomáliadetektálási észlelési API kognitív Microsoft-szolgáltatásokban. Eredeti adatpontok küldeni API, és a várt érték és anomáliadetektálási pontok.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: d35f41ddab21aa155376ad52ff4084298dab8fc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348575"
---
# <a name="anomaly-detection-python-application"></a>Az anomáliadetektálási észlelési Python-alkalmazás

Az oktatóanyag bemutatja az Anomáliadetektálási észlelési API használata a Python és hogyan kell a népszerű könyvtárak segítségével eredményeinek képi megjelenítése. Az oktatóanyag futtatásához használt Jupyter, majd próbálkozzon a saját adatok az előfizetés kulccsal. Az interaktív Jupyter notebookok első lépések megismeréséhez olvassa el [Jupyter dokumentáció](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Az anomáliadetektálás szolgáltatásra, és egy előfizetés kulcs beszerzése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>A mintakód letöltése

1. Keresse meg a [github oktatóanyag notebook](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Kattintson a klónozáshoz vagy töltse le az oktatóanyag zöld gombra. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Az oktatóanyag notebook nyithatók meg a Jupyter

1. Nyisson meg egy parancssort, és nyissa meg a mappa python-mintát.
2. A parancs Jupyter notebook futtassa a parancssorból, Jupyter elindító.
3. A Jupyter ablakban kattintson a <em>Anomáliadetektálási észlelési API Example.ipynb</em> az oktatóanyag jegyzetfüzet megnyitása.   

## <a name="running-the-tutorial"></a>Az oktatóanyag futtatása

A notebook használatához szüksége lesz egy előfizetés kulcs az Anomáliadetektálási észlelési API-t. Látogasson el a regisztráció-előfizetés lapján. A "Bejelentkezés" oldalon a Microsoft-fiók használatával jelentkezzen be, és meg fogja tudni szolgáltatásra, és a kulcsok beolvasása. A regisztrációs folyamat befejezése után illessze be a kulcsot a változók szakaszban a notebook (az alábbiakban bemutatott). Működik az elsődleges vagy másodlagos kulcsát. Győződjön meg arról, hogy a kulcs tegye idézőjelek közé foglalt abba, hogy egy karakterláncot.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
