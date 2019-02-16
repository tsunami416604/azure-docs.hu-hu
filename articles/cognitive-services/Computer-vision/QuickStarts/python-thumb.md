---
title: 'Gyors útmutató: Miniatűrkép - REST, Python generálása'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és Python használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c29ea46513624215421845b99bd8306b73a5a9ac
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309012"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-python-in-computer-vision"></a>Gyors útmutató: A REST API-t és a Python használatával a Computer Vision miniatűrkép generálása

Ebben a rövid útmutatóban a miniatűr generál egy rendszerképből számítógépes Látástechnológiai REST API használatával. Az a [miniatűr beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódus, megadhatja a kívánt magasság és szélesség és régió alapján nyelvelemző, mind a terület hasznos helyek azonosításához, és körbevágási koordináták készítése intelligens vágása számítógépes Látástechnológiai használja.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Az előfizetői azonosító beszerzéséhez lásd az [előfizetői azonosítók beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.
- Például egy Kódszerkesztő [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

Hozhat létre, és futtassa a mintát, másolja a következő kódot a Kódszerkesztő. 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data    = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Ezután tegye a következőket:
1. Cserélje le a `subscription_key` értéket az előfizetői azonosítóra.
1. Ha szükséges, cserélje le az `vision_base_url` értéket azon Azure-régió Computer Vision-erőforrás metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
1. Ha szeretné, cserélje le az `image_url` értéket annak a képnek az URL-címére, amelyhez miniatűrt szeretne létrehozni.
1. Mentse a kódot egy `.py` kiterjesztésű fájlként. Például: `get-thumbnail.py`.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python get-thumbnail.py`.

## <a name="examine-the-response"></a>A válasz vizsgálata

A sikeres válasz, a bináris adatokat, amelyek a miniatűr kép adatait jelöli. A mintában Ez a kép megjelenjen. A kérelem meghiúsul, ha a válasz jelenik meg a parancsablakot, és tartalmaznia kell egy hibakódot.

## <a name="run-in-jupyter-optional"></a>Futtatása a Jupyter-(nem kötelező)

Ez a rövid útmutató lépés által lépés feldolgozza a Jupyter notebook használatával igény szerint futtathatja [MyBinder](https://mybinder.org). A Binder indításához válassza az alábbi gombot:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>További lépések

Következőként ismerje meg a miniatűr-létrehozást funkció részletesebb információkat.

> [!div class="nextstepaction"]
> [Miniatűrök létrehozása](../concept-generating-thumbnails.md)
