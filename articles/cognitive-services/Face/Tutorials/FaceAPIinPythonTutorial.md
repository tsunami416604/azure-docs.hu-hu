---
title: 'Gyors útmutató: Képek észlelése és keretbe állítása a Python SDK-val'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy olyan Python-szkriptet hoz létre, amely a Face API használatával felderíti és keretet ad a távoli rendszerképeken.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: sbowles
ms.openlocfilehash: 502523e3c62f993af4484c8ab922c36fa51a60f1
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945894"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>Gyors útmutató: Python-szkript létrehozása az arcok észleléséhez és a képek keretének megjelenítéséhez

Ebben a rövid útmutatóban egy olyan Python-szkriptet hoz létre, amely az Azure Face API-t használja a Python SDK-n keresztül, hogy felismerje az emberi arcokat egy távoli rendszerképben. Az alkalmazás megjeleníti a kiválasztott kép, és megrajzolja az egyes észlelt face köré keretet.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- A Face API előfizetési kulcs. Megjelenik a származó ingyenes próba-előfizetését kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face API szolgáltatás és a kulcs beszerzése.
- [A Python 2.7-es vagy újabb, illetve 3.5-ös vagy újabb verziója](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) eszköz

## <a name="get-the-face-sdk"></a>A Face SDK beszerzése

A Face Python SDK telepítése a parancssor használatával megnyitásával, és futtassa a következő parancsot:

```shell
pip install azure-cognitiveservices-vision-face
```

## <a name="detect-faces-in-an-image"></a>A kép arcok észlelése

Hozzon létre egy új Python-szkriptet nevű _FaceQuickstart.py_ , és adja hozzá a következő kódot. Ez a kód a Arcfelismerés alapvető funkcióit kezeli. Le kell cserélnie `<Subscription Key>` a kulcs értékét. Is szükség lehet értékének módosítása `BASE_URL` a megfelelő régióazonosító használata a kulcshoz (lásd a [Face API-dokumentumok](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) minden régióban végpontok listáját). Ingyenes próba-előfizetését kulcsokat hoz létre a a **westus** régióban. Beállíthatja `img_url` URL-címét használni kívánt képet.

A parancsfájl az **FaceClient. Face. detect_with_url** metódus meghívásával fogja felderíteni az arcokat, [](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) amely az észlelési REST API és az arcok listáját adja vissza.

```python
from azure.cognitiveservices.vision.face import FaceClient

# Replace with a valid subscription key (keeping the quotes in place).
KEY = '<Subscription Key>'

# Replace with your regional Base URL
BASE_URL = 'https://westus.api.cognitive.microsoft.com/'

faceClient = FaceClient(BASE_URL, CognitiveServicesCredentials(KEY))

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = faceClient.face.detect_with_url(img_url)
print(faces)
```

### <a name="try-the-app"></a>Próbálja ki az alkalmazást

Az alkalmazás futtatása paranccsal `python FaceQuickstart.py`. A konzolablakban, a következőhöz hasonló szöveg választ kell kapnia:

```console
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

A kimenet az észlelt arcok listáját jelöli. A lista minden eleme van egy **dict** példányán, amelyen `faceId` van egy egyedi Azonosítót a felismert arc és `faceRectangle` ismerteti az észlelt face pozícióját. 

> [!NOTE]
> Face azonosítók 24 óra; után lejárnak arcfelismerési adatokat explicit módon tárolja, ha szeretné megtartani hosszú távú kell.

## <a name="draw-face-rectangles"></a>Arcjelző négyszögek rajzolása

Használja a koordináták, amelyet az előző parancs által kapott, rajzolhat, téglalapok vizuálisan képviselő minden a lemezképre. Párnád telepíteni kell (`pip install pillow`) a párnád lemezkép modult szeretné használni. Felső részén *FaceQuickstart.py*, adja hozzá a következő kódot:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

A szkript alján adja hozzá a következő kódot. Ez a kód egy egyszerű függvényt hoz létre a téglalap koordinátáinak elemzéséhez, és a párna használatával rajzolja a téglalapokat az eredeti képre. A lemezképet, majd az alapértelmezett image Viewer megjeleníti.

```python
# Convert width height to a point in a rectangle


def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))


# Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

# For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

# Display the image in the users default image browser.
img.show()
```

## <a name="run-the-app"></a>Az alkalmazás futtatása

A rendszer felkérheti először jelöljön ki egy alapértelmezett képmegtekintő. Ezt követően megtekintheti a következőhöz hasonló képet. Megjelenik a konzolablakban nyomtatott téglalap adatok is.

![A piros téglalap körül a face fiatal nő](../images/face-rectangle-result.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a megismerte az alapvető folyamat a Face API Python SDK-val és a egy szkriptet, és alkalmas keretet biztosítanak az arcok a képen. Ezután megkezdheti a Python SDK-t egy összetettebb példában használatát. Nyissa meg a Cognitive Face Python mintát a Githubon, klónozza, a projektmappa fájllistájának, majd kövesse az utasításokat a _README.md_ fájlt.

> [!div class="nextstepaction"]
> [A cognitive Face Python-minta](https://github.com/Microsoft/Cognitive-Face-Python)
