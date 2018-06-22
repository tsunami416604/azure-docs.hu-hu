---
title: Arcfelismerési API Python oktatóanyag |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Útmutató a Arcfelismerési API-t a Python SDK-val észlelheti az emberi lapok kognitív szolgáltatások képet.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 408f9759262d6ae2193df3193ee1c306d384afe6
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308788"
---
# <a name="getting-started-with-face-api-in-python-tutorial"></a>Az oktatóanyag a Python Ismerkedés a Arcfelismerési API

Ebből az oktatóanyagból megtudhatja, a Arcfelismerési API-t a Python SDK emberi lapok azonosíthatók a lemezkép használatával meghívni.

## <a name="prerequisites"></a> Előfeltételek

Az oktatóanyag használatához szüksége lesz a következőkre:

- Telepítse a Python 2.7 + vagy Python 3.5 +.
- A pip telepítése.
- A Python SDK telepítése a Arcfelismerési API-hoz az alábbiak szerint:

```bash
pip install cognitive_face
```

- Szerezzen be egy [előfizetés kulcs](https://azure.microsoft.com/try/cognitive-services/) Microsoft kognitív számára. Ebben az oktatóanyagban az elsődleges vagy a másodlagos kulcsot is használhatja. (Vegye figyelembe, hogy Arcfelismerési API-k használatához rendelkeznie kell egy érvényes előfizetés-kulcs.)

## <a name="sdk-example"></a> A kép egy ARC észlelése

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

Az alábbiakban van egy példa eredménye. Ez egy `list` az észlelt lapokat. A lista minden eleme van egy `dict` példányán, amelyen `faceId` észlelt maga az egyedi azonosító és `faceRectangle` ismerteti az észlelt arc pozícióját. A tapasztalt ID 24 óra múlva lejár.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>A lapok körül téglalapok megrajzolásához

A json-koordináták kapott az előző parancs használata, rajzolásához téglalapok vizuálisan képviselő minden lapot a lemezképre. Szüksége lesz a `pip install Pillow` használata a `PIL` lemezkép-készítési modul.  A fájl elejéhez adja hozzá a következő:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Követően `print(faces)`, a parancsfájl tartalmazza a következőt:

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name='further'></a> További feltárása

Segítségre van szüksége további megismerkedhet a Arcfelismerési API-t, ez az oktatóanyag egy grafikus felhasználói Felülettel minta kínál. Futtassa, először telepítenie kell [wxPython](https://wxpython.org/) futtassa az alábbi parancsokat.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a> Összefoglalás

Ebben az oktatóprogramban megtanulhatta, alapvető eljárása keresztül a Python SDK meghívása Arcfelismerési API használatával. API részletei további információkért tekintse meg az útmutató és [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Kapcsolódó témakörök

- [A csharp nyelvű API néző első lépések](FaceAPIinCSharpTutorial.md)
- [Ismerkedés az Arcfelismerési API-nak Java androidhoz](FaceAPIinJavaForAndroidTutorial.md)
