---
title: Távoli kép elemzése a Computer Vision és a Python használatával – rövid útmutató | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ebben a rövid útmutatóban egy távoli képet fog elemezni a Computer Vision és a Python használatával a Cognitive Servicesben.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 65f9b0d4fb007a6a9b8ef489ca59f384e047a0dd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770338"
---
# <a name="quickstart-analyze-a-remote-image---rest-python"></a>Rövid útmutató: Távoli kép elemzése – REST, Python

Ebben a rövid útmutatóban egy távoli képet fog elemezni a Computer Vision segítségével. Helyi kép elemzéséhez lásd: [Helyi kép elemzése a Python használatával](python-disk.md).

Ezt a rövid útmutatót futtathatja lépésenként egy Jupyter-notebook segítségével a [MyBinderben](https://mybinder.org). A Binder indításához válassza az alábbi gombot:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Előfeltételek

A Computer Vision használatához előfizetési kulcsra van szüksége, lásd az [előfizetési kulcsok beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="analyze-a-remote-image"></a>Távoli rendszerkép elemzése

Az [Analyze Image metódussal](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) vizuális jellemzőket nyerhet ki a képek tartalma alapján. Feltöltheti a képet, vagy megadhatja a kép URL-címét, és kiválaszthatja a kapni kívánt jellemzőket, úgymint:

* a kép tartalmához kapcsolódó címkék részletes listája;
* a kép tartalmának leírása teljes mondatban;
* a képen szereplő arcok koordinátái, neme és kora;
* a kép típusa (ClipArt vagy vonalrajz);
* a domináns és a kiegészítő színek, vagy hogy a kép fekete-fehér;
* az ebben a [taxonómiában](../Category-Taxonomy.md) meghatározott kategória;
* tartalmaz-e a kép felnőtteknek szóló és szexuális tartalmat.

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja a következő kódot egy új Python-szkriptfájlba.
1. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
1. Ha szükséges, változtassa meg az `vision_base_url` értékét arra a helyre, ahonnan az előfizetési kulcsot beszerezte.
1. Módosíthatja az `image_url` elem értékét egy másik képre.
1. Futtassa a szkriptet.

Az alábbi kód a Python `requests` kódtárát használja a Computer Vision képelemző API-jának meghívásához. Az eredményeket JSON-objektumként adja vissza. Az API-kulcsot a rendszer a `headers` szótárral adja át. A felismerni kívánt jellemzők típusait a rendszer a `params` szótárral adja át.

## <a name="analyze-image-request"></a>Képelemzési kérés

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
# Free trial subscription keys are generated in the westcentralus region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

analyze_url = vision_base_url + "analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/" + \
    "Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key }
params  = {'visualFeatures': 'Categories,Description,Color'}
data    = {'url': image_url}
response = requests.post(analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(analysis)
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
```

## <a name="analyze-image-response"></a>A képelemzés válasza

A rendszer JSON formátumban adja vissza a sikeres választ, például:

```json
{
  "categories": [
    {
      "name": "outdoor_",
      "score": 0.00390625,
      "detail": {
        "landmarks": []
      }
    },
    {
      "name": "outdoor_street",
      "score": 0.33984375,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "building",
      "outdoor",
      "street",
      "city",
      "people",
      "busy",
      "table",
      "walking",
      "traffic",
      "filled",
      "large",
      "many",
      "group",
      "night",
      "light",
      "crowded",
      "bunch",
      "standing",
      "man",
      "sign",
      "crowd",
      "umbrella",
      "riding",
      "tall",
      "woman",
      "bus"
    ],
    "captions": [
      {
        "text": "a group of people on a city street at night",
        "confidence": 0.9122243847383961
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown"
    ],
    "accentColor": "B54316",
    "isBwImg": false
  },
  "requestId": "c11894eb-de3e-451b-9257-7c8b168073d1",
  "metadata": {
    "height": 600,
    "width": 450,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>További lépések

Ismerjen meg egy Python-alkalmazást, amely a Computer Vision segítségével végez optikai karakterfelismerést (OCR), és amellyel intelligens körbevágású miniatűröket hozhat létre, valamint képek vizuális jellemzőit, például arcokat észlelhet, kategorizálhat, címkézhet és írhat le. A Computer Vision API-kkal való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API – Python-oktatóanyag](../Tutorials/PythonTutorial.md)
