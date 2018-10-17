---
title: 'Oktatóanyag: Arcok észlelése és bekeretezése egy képen – Face API, Python'
titleSuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható a Face API-t a Python SDK-val egy képen az emberi arcok felismerésére.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 6cc3ac25d2196c0275b445503b79b9ac06a791d3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127737"
---
# <a name="tutorial-detect-and-frame-faces-with-the-face-api-and-python"></a>Oktatóanyag: Arcok észlelése és bekeretezése a Face API és Python használatával 

Ebben az oktatóanyagban megtanulhatja a Face API meghívását a Python SDK-n keresztül a képen az emberi arcok megkeresésére.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag használatához a következőkre lesz szüksége:

- Vagy Python 2.7 + vagy Python 3.5+ telepítése.
- Pip telepítése.
- A Python SDK-t a Face API-hoz a következőképpen telepítheti:

```bash
pip install cognitive_face
```

- Szerezzen be [előfizetési kulcsot](https://azure.microsoft.com/try/cognitive-services/) a Microsoft Cognitive Services szolgáltatáshoz. Ebben az oktatóanyagban az elsődleges vagy a másodlagos kulcsot is használhatja. (Ne feledje, mindegyik Face API használatához érvényes előfizetési kulccsal kell rendelkeznie.)

## <a name="detect-a-face-in-an-image"></a>Arc észlelése egy képen

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

Alább látható egy példa eredménye. Ez `list` az észlelt arcoknak. A lista minden eleme egy `dict` példány, ahol `faceId` az észlelt arc egyedi azonosítja és `faceRectangle` az észlelt arc helyzetét írja le. Az arcazonosító 24 óráig él.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Téglalapok rajzolása az arcok köré

Az előző parancsban megkapott json-koordinátákat használva téglalapokat rajzolhat képen az arcok jelzésére. A `pip install Pillow` szükséges lesz a `PIL` képalkotó modul használatához.  A fájl tetején adja hozzá a következőket:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Majd a `print(faces)` után tegye bele a következőket a parancsfájlba:

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

## <a name="further-exploration"></a>További ismerkedés

A Face API további felfedezéséhez az oktatóanyag egy grafikus felhasználói felületet példát biztosít. A futtatásához először telepítse a [wxPython](https://wxpython.org/pages/downloads/)-t, majd futtassa az alábbi parancsokat.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a>Összegzés

Ebben az oktatóanyagban megtanulhatta a Face API Python SDK-n keresztüli meghívásának alapvető folyamatát. Részletesebb API információk találhatók a Hogyan kell és [az API-referenciában](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Első lépések a Face API-val CSharp nyelven](FaceAPIinCSharpTutorial.md)
- [Első lépések a Face API-val Java for Android nyelven](FaceAPIinJavaForAndroidTutorial.md)
