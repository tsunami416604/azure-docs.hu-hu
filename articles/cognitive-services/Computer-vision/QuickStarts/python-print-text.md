---
title: 'Rövid útmutató: Nyomtatott szöveg kinyerése (OCR) – REST, Python – Computer Vision'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban nyomtatott szöveget fog kinyerni egy képből a Computer Vision API Pythonnal való használatával.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: e206f3e716bed558c8041b5714433cecbf9500ac
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345113"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-rest-api-and-python-in-computer-vision"></a>Rövid útmutató: Nyomtatott szöveg kinyerése (OCR) a REST API-val és Pythonnal a Computer Visionben

Ebben a rövid útmutatóban optikai karakterfelismerést (OCR) használva nyomtatott szöveget fog kinyerni egy képből a Computer Vision REST API-jával. Az [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) metódussal nyomtatott szöveget észlelhet egy képen, és géppel olvasható karakterfolyamba nyerheti ki a felismert karaktereket.

Ezt a rövid útmutatót futtathatja lépésenként egy Jupyter-notebook segítségével a [MyBinderben](https://mybinder.org). A Binder indításához válassza az alábbi gombot:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A minta helyi futtatásához rendelkeznie kell a [Pythonnal](https://www.python.org/downloads/).
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Az előfizetői azonosító beszerzéséhez lásd az [előfizetői azonosítók beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a kód megfelelő területein:
    1. Cserélje le a `subscription_key` értéket az előfizetői azonosítóra.
    1. Ha szükséges, cserélje le az `vision_base_url` értéket azon Azure-régió Computer Vision-erőforrás metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
    1. Ha szeretné, cserélje le az `image_url` értéket egy másik olyan kép URL-címére, amelyből nyomtatott szöveget szeretne kinyerni.
1. Mentse a kódot fájlként `.py` kiterjesztéssel. Például: `get-printed-text.py`.
1. Nyisson meg egy parancssort.
1. Amikor a rendszer kéri, a `python` paranccsal futtassa a mintát. Például: `python get-printed-text.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
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

ocr_url = vision_base_url + "ocr"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" + \
    "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'language': 'unk', 'detectOrientation': 'true'}
data    = {'url': image_url}
response = requests.post(ocr_url, headers=headers, params=params, json=data)
response.raise_for_status()

analysis = response.json()

# Extract the word bounding boxes and text.
line_infos = [region["lines"] for region in analysis["regions"]]
word_infos = []
for line in line_infos:
    for word_metadata in line:
        for word_info in word_metadata["words"]:
            word_infos.append(word_info)
word_infos

# Display the image and overlay it with the extracted text.
plt.figure(figsize=(5, 5))
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image, alpha=0.5)
for word in word_infos:
    bbox = [int(num) for num in word["boundingBox"].split(",")]
    text = word["text"]
    origin = (bbox[0], bbox[1])
    patch  = Rectangle(origin, bbox[2], bbox[3], fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(origin[0], origin[1], text, fontsize=20, weight="bold", va="top")
plt.axis("off")
```

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A mintaweblap elemzi és megjeleníti a sikeres választ a parancssorban, a következő példához hasonló módon:

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a fájlt.

## <a name="next-steps"></a>További lépések

Ismerjen meg egy Python-alkalmazást, amely a Computer Vision segítségével végez optikai karakterfelismerést (OCR), és amellyel intelligens körbevágású miniatűröket hozhat létre, valamint képek vizuális jellemzőit, például arcokat észlelhet, kategorizálhat, címkézhet és írhat le. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API – Python-oktatóanyag](../Tutorials/PythonTutorial.md)
