---
title: 'Rövid útmutató: Érzelemfelismerés képeken szereplő arcokon – Emotion API, Python'
description: Információk és kódminták segítségével ismerkedhet meg az Emotion API Pythonnal való használatának első lépéseivel.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 02/05/2018
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: c7611628918cf40800d173dc9404b0948b9a68a4
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236567"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Rövid útmutató: Alkalmazás létrehozása a képeken szereplő arcokon tükröződő érzelmek felismeréséhez.

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként általánosan elérhető. 

Ez az útmutató információkkal és kódmintákkal szolgál, amelyeken keresztül gyorsan elsajátíthatja, hogyan ismerheti fel a képeken szereplő egy vagy több személy által kifejezett érzelmeket az [Emotion API Recognize metódusa](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) és a Python használatával.

Ezt a példát futtathatja Jupyter-notebookként a [MyBinderen](https://mybinder.org), az indítás Binder-jelvényére kattintva: [![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb).


## <a name="prerequisite"></a>Előfeltétel
Ingyenes előfizetői azonosítóját [itt](https://azure.microsoft.com/try/cognitive-services/) szerezheti be

## <a name="running-the-walkthrough"></a>Az útmutató futtatása
Az útmutató folytatásához cserélje le a `subscription_key` kulcsot a korábban beszerzett API-kulcsra.


```python
subscription_key = None
assert subscription_key
```

Ezután győződjön meg arról, hogy a szolgáltatás URL-címe megfelel az API-kulcs beállításakor használt régiónak. Ha próbaverziós kulcsot használ, nem kell módosításokat végeznie.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

Ez az útmutató lemezen tárolt képeket használ. Nyilvános URL-címen elérhető képeket is használhat. További információkért tekintse meg a [REST API dokumentációját](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

Mivel a képadatok a kérés törzsének részeként vannak átadva, figyelje meg, hogy a `Content-Type` fejlécet `application/octet-stream` értékre kell állítania. Ha URL-címen keresztül ad át egy képet, ne felejtse el a következőre állítani a fejlécet:
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
az URL-címet tartalmazó szótárt létrehozni:
```python
data = {'url': image_url}
```
és átadni azt a `requests` kódtárnak a következővel:
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

Először töltsön le néhány mintaképet az [Emotion API](https://azure.microsoft.com/services/cognitive-services/emotion/) webhelyéről.


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



A visszaadott JSON-objektum tartalmazza a felismert arcok határoló kereteit és a felismert érzelmeket. Minden érzelem 0 és 1 közötti pontszámmal rendelkezik, ahol a magasabb pontszám valószínűbbként jelöli az adott érzelmet, mint az alacsonyabb pontszám.

A következő kódsorok a képen szereplő arcok érzelmeit a `matplotlib` kódtárral ismerik fel. A zsúfoltság csökkentése érdekében csak az első három érzelem jelenik meg.


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

A következő `annotate_image` függvénnyel érzelmek helyezhetők a képfájlra, ha megadja annak a fájlrendszeren keresztüli elérési útját. Ez a korábban látott, Emotion API-ba küldött híváson alapul.


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

Végül egy képfájlon meghívható az `annotate_image` függvény, ahogyan a következő sorban látható:


```python
annotate_image("images/emotion_2.jpg")
```
