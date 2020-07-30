---
title: 'Gyors útmutató: tartományi specifikus tartalom – REST, Python'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban tartománymodellekkel fog hírességeket és nevezetességeket azonosítani egy képen a Computer Vision API a Pythonnal való használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18, tracking-python
ms.openlocfilehash: a470166d0ed172d95e80fadeb26139a702f40bb2
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387455"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-python-in-computer-vision"></a>Rövid útmutató: Tartománymodell használata a REST és a Python használatával a Computer Visionben

Ebben a rövid útmutatóban egy tartományi modellt használ a tereptárgyak azonosítására, vagy opcionálisan egy távolról tárolt rendszerképben lévő hírességeket a Computer Vision REST API használatával. A [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) metódussal egy tartományspecifikus modellel ismerheti fel a képek tartalmait azonosíthat egy képen.

Ezt a rövid útmutatót futtathatja lépésenként egy Jupyter-notebook segítségével a [MyBinderben](https://mybinder.org). A Binder indításához válassza az alábbi gombot:

[![Iratgyűjtő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/downloads/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* [Hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs és a végpont URL-címéhez, illetve a nevet `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

## <a name="create-and-run-the-landmarks-sample"></a>A nevezetességminta létrehozása és futtatása

A nevezetességminta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Ha szeretné, cserélje le az `image_url` értéket annak a képnek az URL-címére, amelyen nevezetességeket szeretne azonosítani.
1. Mentse a kódot egy `.py` kiterjesztésű fájlként. Például: `get-landmarks.py`.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python get-landmarks.py`.

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

landmark_analyze_url = endpoint + "vision/v3.0/models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'landmarks'}
data = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
plt.show()
```

## <a name="examine-the-response-for-the-landmarks-sample"></a>A nevezetességminta válaszainak vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A mintawebhely kielemez és megjelenít egy sikeres, az alábbihoz hasonló választ a parancsablakban:

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="create-and-run-the-celebrities-sample"></a>A hírességminta létrehozása és futtatása

A nevezetességminta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Szükség szerint hajtsa végre a következő módosításokat a kódban:
    1. Cserélje le a `subscription_key` értéket az előfizetői azonosítóra.
    1. Ha szükséges, cserélje le az `vision_base_url` értéket azon Azure-régió Computer Vision-erőforrás metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
    1. Ha szeretné, cserélje le az `image_url` értéket annak a képnek az URL-címére, amelyen hírességeket szeretne azonosítani.
1. Mentse a kódot egy `.py` kiterjesztésű fájlként. Például: `get-celebrities.py`.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python get-celebrities.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'celebrities'}
data = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
plt.show()
```

## <a name="examine-the-response-for-the-celebrities-sample"></a>A hírességminta válaszainak vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A mintawebhely kielemez és megjelenít egy sikeres, az alábbihoz hasonló választ a parancsablakban:


```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje mindkét minta fájljait.

## <a name="next-steps"></a>Következő lépések

Ezután Ismerkedjen meg egy olyan Python-alkalmazással, amely a Computer Visiont használja az optikai karakterfelismerés (OCR) végrehajtásához. intelligens vágású miniatűrök létrehozása; a képek vizuális funkcióinak felismerése, kategorizálása, címkézése és leírása.

> [!div class="nextstepaction"]
> [Computer Vision API – Python-oktatóanyag](../Tutorials/PythonTutorial.md)

* A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
