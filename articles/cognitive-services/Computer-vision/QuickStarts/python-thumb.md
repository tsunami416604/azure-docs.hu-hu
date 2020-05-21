---
title: 'Rövid útmutató: Miniatűr létrehozása – REST, Python'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és Python használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9f80d9be8966d1e2d8350f7fbc3c34182cb7b63e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684130"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Gyors útmutató: miniatűr létrehozása a Computer Vision REST API és a Python használatával

Ebben a rövid útmutatóban egy miniatűrt fog létrehozni egy képből a Computer Vision REST API használatával. A [miniatűr beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) módszerrel megadhatja a kívánt magasságot és szélességet, a Computer Vision pedig intelligens levágást használ a fontos terület intelligens azonosításához és az adott régió alapján történő levágási koordináták létrehozásához.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Előfeltételek

- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. A [kipróbálási Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)ingyenes próbaverziós kulcsot is beszerezhet. Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Computer Visionra való előfizetéshez és a kulcs beszerzéséhez című témakör utasításait. Ezután [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs-és szolgáltatás végponti karakterláncához, a nevet és a-t `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .
- Egy Kódszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához másolja a következő kódot a kódszerkesztőba. 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following lines.
# %matplotlib inline
# import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v2.1/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

# Construct URL
headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
# Call API
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Open the image from bytes
thumbnail = Image.open(BytesIO(response.content))

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))

# Save thumbnail to file
thumbnail.save('thumbnail.png')

# Display image
thumbnail.show()

# Optional. Display the thumbnail from Jupyter.
# plt.imshow(thumbnail)
# plt.axis("off")
```

Ezután tegye a következőket:

1. Választható Cserélje le a értékét `image_url` a saját rendszerképének URL-címére.
1. Mentse a kódot egy `.py` kiterjesztésű fájlként. Például: `get-thumbnail.py`.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python get-thumbnail.py`.

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer sikeres választ ad vissza bináris adatként, amely a miniatűr képéneket jelöli. A mintának ezt a képet kell megjelenítenie. Ha a kérelem sikertelen, a válasz megjelenik a parancssorablakban, és tartalmaznia kell egy hibakódot.

## <a name="run-in-jupyter-optional"></a>Futtatás a Jupyter (nem kötelező)

A rövid útmutató lépésről lépésre elindítható a [MyBinder](https://mybinder.org)Jupyter notebookjának használatával. A Binder indításához válassza az alábbi gombot:

[![Iratgyűjtő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Következő lépések

Ezután Ismerkedjen meg egy olyan Python-alkalmazással, amely a Computer Visiont használja az optikai karakterfelismerés (OCR) végrehajtásához. intelligens vágású miniatűrök létrehozása; a képek vizuális funkcióinak felismerése, kategorizálása, címkézése és leírása.

> [!div class="nextstepaction"]
> [Computer Vision API – Python-oktatóanyag](../Tutorials/PythonTutorial.md)

* A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
