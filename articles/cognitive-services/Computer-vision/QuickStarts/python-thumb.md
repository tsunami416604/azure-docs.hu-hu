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
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 042fc22e66924fb2ea1a6985f442c46061b7fc31
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176396"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Gyors útmutató: miniatűr létrehozása a Computer Vision REST API és a Python használatával

Ebben a rövid útmutatóban egy miniatűrt fog létrehozni a rendszerképből Computer Vision REST API használatával. A [miniatűr beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) módszerrel megadhatja a kívánt magasságot és szélességet, a Computer Vision pedig intelligens levágást használ a fontos terület intelligens azonosításához és az adott régió alapján történő levágási koordináták létrehozásához.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/try/cognitive-services/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. A [kipróbálási Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)ingyenes próbaverziós kulcsot is beszerezhet. Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Computer Visionra való előfizetéshez és a kulcs beszerzéséhez című témakör utasításait. Ezután [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs és szolgáltatás végponti karakterláncához, amelynek neve `COMPUTER_VISION_SUBSCRIPTION_KEY` és `COMPUTER_VISION_ENDPOINT`.
- Egy Kódszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához másolja a következő kódot a kódszerkesztőba. 

```python
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

thumbnail_url = endpoint + "vision/v2.1/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Ezután tegye a következőket:
1. Ha szeretné, cserélje le az `image_url` értéket annak a képnek az URL-címére, amelyhez miniatűrt szeretne létrehozni.
1. Mentse a kódot fájlként `.py` kiterjesztéssel. Például: `get-thumbnail.py`.
1. Nyisson meg egy parancssort.
1. Amikor a rendszer kéri, a `python` paranccsal futtassa a mintát. Például: `python get-thumbnail.py`.

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer sikeres választ ad vissza bináris adatként, amely a miniatűr képéneket jelöli. A mintának ezt a képet kell megjelenítenie. Ha a kérelem sikertelen, a válasz megjelenik a parancssorablakban, és tartalmaznia kell egy hibakódot.

## <a name="run-in-jupyter-optional"></a>Futtatás a Jupyter (nem kötelező)

A rövid útmutató lépésről lépésre elindítható a [MyBinder](https://mybinder.org)Jupyter notebookjának használatával. A Binder indításához válassza az alábbi gombot:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Következő lépések

Következő lépésként tájékozódjon részletesebben a miniatűr generálási funkciójával kapcsolatban.

> [!div class="nextstepaction"]
> [Miniatűrök létrehozása](../concept-generating-thumbnails.md)
