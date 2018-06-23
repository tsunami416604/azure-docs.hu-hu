---
title: Érzelemfelismerési API Python – első lépések |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében a Érzelemfelismerési API Python kognitív szolgáltatásban.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/05/2018
ms.author: anroth
ms.openlocfilehash: ff1f6b2ddc872d0ee63d9885b04b1f007bc86e33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347038"
---
# <a name="emotion-api-python-quickstart"></a>Érzelemfelismerési API Python gyors üzembe helyezés

> [!IMPORTANT]
> 2017. október 30 villámnézet API rendszerhez. Kipróbálhatja az új [videó indexelő API előnézete](https://azure.microsoft.com/services/cognitive-services/video-indexer/) insights könnyen kibontani videók, és tartalom felderítési lép, például a keresési eredmények, növelje a szóbeli szavakat, a lapok, a karakterek és a érzelmek észlelésével. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ez a forgatókönyv és a segítségével gyorsan kódmintákat az első lépéseiben a [Érzelemfelismerési API-t ismeri fel a metódust](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) Python ismeri fel a kép egy vagy több személy által kifejezett érzelmek. 

Futtathatja, ebben a példában Jupyter notebook [MyBinder](https://mybinder.org) az indítási kötő kattintva jelvények: [ ![kötő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>Előfeltétel
Az ingyenes előfizetés kulcs lekérése [Itt](https://azure.microsoft.com/try/cognitive-services/)

## <a name="running-the-walkthrough"></a>A forgatókönyv futtatása
Ez a forgatókönyv folytatásához, cserélje le a `subscription_key` korábban beszerzett API-kulcs.


```python
subscription_key = None
assert subscription_key
```

Ezt követően győződjön meg arról, hogy a szolgáltatás URL-címe megegyezik a régió, az API-kulcs beállítása során használt. Ha egy próba-kulcsot használ, nem kell hajtsa végre a módosításokat.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

Ez az útmutató használja, a lemezen tárolt lemezképet. Elérhető egy nyilvánosan elérhető URL-CÍMEN keresztül képek is használható. További információkért lásd: a [REST API-dokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

Mivel a képadatok megnevezése szerepel a kérelem törzse, figyelje meg, hogy be kell állítani a `Content-Type` fejlécének `application/octet-stream`. Ha a kép URL-címet keresztül átadott, ne felejtse el a fejléc értéke:
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
az URL-címet tartalmazó szótár létrehozása:
```python
data = {'url': image_url}
```
és adja át, hogy a `requests` könyvtár használatával:
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

Először töltse le a néhány minta lemezképet rögzíthet a [Érzelemfelismerési API](https://azure.microsoft.com/services/cognitive-services/emotion/) hely.


```bash
%%bash
mkdir -p images
curl -Ls https://aka.ms/csnb-emotion-1 -o images/emotion_1.jpg
curl -Ls https://aka.ms/csnb-emotion-2 -o images/emotion_2.jpg
```


```python
image_path = "images/emotion_1.jpg"
image_data = open(image_path, "rb").read()
```


```python
import requests
headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
response.raise_for_status()
analysis = response.json()
analysis
```




    [{'faceRectangle': {'height': 162, 'left': 130, 'top': 141, 'width': 162},
      'scores': {'anger': 9.29041e-06,
       'contempt': 0.000118981574,
       'disgust': 3.15619363e-05,
       'fear': 0.000589638,
       'happiness': 0.06630674,
       'neutral': 0.00555004273,
       'sadness': 7.44669524e-06,
       'surprise': 0.9273863}}]



A visszaadott JSON-objektum tartalmazza a lapok, amelyek együtt a észlelt érzelmek felismerhető a határoló jelölőnégyzetéből. Minden érzelemfelismerési rendelve egy 0 és 1 közötti pontszámot több azonosítóját egy érzelemfelismerési alacsonyabb pontszám-nál magasabb pontszám esetén. 

Az alábbi adatsorokat code meg a lemezkép használatával lapok észlelt érzelmek a `matplotlib` könyvtár. Rendezettség, csak az első három érzelmek jelennek meg.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from io import BytesIO
from PIL import Image

plt.figure(figsize=(5,5))

image  = Image.open(BytesIO(image_data))
ax     = plt.imshow(image, alpha=0.6)

for face in analysis:
    fr = face["faceRectangle"]
    em = face["scores"]
    origin = (fr["left"], fr["top"])
    p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
    plt.text(origin[0], origin[1], ct, fontsize=20)    
_ = plt.axis("off")
```

A `annotate_image` látható a következő függvény segítségével átfedő érzelmek fölött lemezképfájl elérési úttal megadott a fájlrendszerben. A korábban bemutatott Érzelemfelismerési API-be irányuló hívás kódjának alapul.


```python
def annotate_image(image_path):    
    image_data = open(image_path, "rb").read()
    headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
    response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
    response.raise_for_status()
    analysis = response.json()

    plt.figure()

    image  = Image.open(image_path)
    ax     = plt.imshow(image, alpha=0.6)

    for face in analysis:
        fr = face["faceRectangle"]
        em = face["scores"]
        origin = (fr["left"], fr["top"])
        p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
        ax.axes.add_patch(p)
        ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
        plt.text(origin[0], origin[1], ct, fontsize=20, va="bottom")    
    _ = plt.axis("off")
```

Végezetül a `annotate_image` függvény nem hívható meg a képfájl látható módon a következő sort:


```python
annotate_image("images/emotion_2.jpg")
```
