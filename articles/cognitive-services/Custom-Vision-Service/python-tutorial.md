---
title: Hozzon létre egy egyéni vizuális szolgáltatás Python-oktatóanyag – Azure Cognitive Services |} A Microsoft Docs
description: Egy egyszerű Python-alkalmazást, amely a Custom Vision API a Microsoft Cognitive Services bemutatása. Hozzon létre egy projektet, adja hozzá a címkéket, tölthet fel képeket, a projekt betanítását és az alapértelmezett végpont használatával előrejelzést.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: df0bdc0bbd2768566336323851f366c9ae280a88
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301599"
---
# <a name="custom-vision-api-python-tutorial"></a>Egyéni Látástechnológiai API Python-oktatóprogram

Ismerje meg, hogy egy kép besorolási projekt létrehozása a Custom Vision Service és a egy alapszintű Python-szkriptet. A létrehozást követően, is címkéket adhat hozzá, tölthet fel képeket, betanítását a projektet, a projekt alapértelmezett előrejelzési végpont URL-cím lekérése és, amellyel programozott módon a lemezkép tesztelése. A nyílt forráskódú példa sablonként használni a saját alkalmazás létrehozásához a Custom Vision API használatával.



## <a name="prerequisites"></a>Előfeltételek

- Python 2.7 + vagy a Python 3.5-ös +.
- A pip eszköz.

## <a name="get-the-training-and-prediction-keys"></a>A betanítási és Predikciós kulcsok beolvasása

Ebben a példában használt kulcsok beszerzéséhez látogasson el a [Custom Vision weblap](https://customvision.ai) , és válassza ki a __fogaskerék ikont__ kattintson a jobb felső sarokban. Az a __fiókok__ területén másolja a __képzési kulcs__ és __előrejelzési kulcs__ mezőket.

![A felhasználói felület kulcsok képe](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>A Custom Vision Service SDK telepítése

A Custom Vision Service SDK telepítéséhez használja a következő parancsot:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>A példában képek bekérése

Ebben a példában a képeket a `Samples/Images` könyvtárát a [ https://github.com/Microsoft/Cognitive-CustomVision-Windows ](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) projekt. Klónozza, vagy töltse le és csomagolja ki a projektet a fejlesztői környezetbe.

## <a name="create-a-custom-vision-service-project"></a>A Custom Vision Service-projekt létrehozása

A Custom Vision Service új projekt, hozzon létre új fájlt `sample.py`. Használja a fájl tartalmát a következő kódot:

> [!IMPORTANT]
> Állítsa be a `training_key` , a korábban kapott képzési kulcs értékét.
>
> Állítsa be a `prediction_key` , a korábban kapott előrejelzési kulcs értékét.

```python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Project")
```

## <a name="add-tags-to-your-project"></a>Címkék hozzáadása a projekthez

Címkék hozzáadása a projekthez, adja hozzá a következő kódot, végén a `sample.py` fájlt:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>A projekt képek feltöltése

A minta képeket hozzáadása a projekthez, szúrja be az alábbi kódot a címke létrehozása után. Ez a kód feltölti a lemezképet a megfelelő címkével:

> [!IMPORTANT]
>
> Elérési utat módosítsa arra a képek, ahol a Cognitive-CustomVision – Windows-projekt korábban letöltött alapján.

```python
base_image_url = "https://raw.githubusercontent.com/Microsoft/Cognitive-CustomVision-Windows/master/Samples/"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])


# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following:
#
#import os
#hemlock_dir = "Images\\Hemlock"
#for image in os.listdir(os.fsencode("Images\\Hemlock")):
#    with open(hemlock_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ hemlock_tag.id ])
#
#cherry_dir = "Images\\Japanese Cherry"
#for image in os.listdir(os.fsencode("Images\\Japanese Cherry")):
#    with open(cherry_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ cherry_tag.id ])
```

## <a name="train-the-project"></a>A projekt betanítása

A besorolás betanítására, adja a következő kódot a végéhez a `sample.py` fájlt:

```python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Letöltheti a az alapértelmezett előrejelzési végpont

Kép küldése a előrejelzési végponthoz, és az előrejelzési lekéréséhez, adja a következő kódot a végéhez a `sample.py` fájlt:

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following.
#
# Open the sample image and get back the prediction results.
# with open("Images\\test\\test_image.jpg", mode="rb") as test_data:
#     results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-example"></a>A példa Futtatás

Futtassa a megoldást. Az előrejelzési eredmények jelennek meg a konzolon.

```
python sample.py
```

Az alkalmazás kimenete az alábbi szöveghez hasonló:

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```