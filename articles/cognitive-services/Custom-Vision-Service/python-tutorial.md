---
title: 'Oktatóanyag: Képosztályozó projekt készítése – Custom Vision Service, Python'
titlesuffix: Azure Cognitive Services
description: Hozzon létre projektet, adjon hozzá címkéket, töltsön fel képeket, tanítsa be a projektet és adjon előrejelzést az alapértelmezett végpont használatával.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: 14b805a60637a889698132e169d5a41670a8bce0
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363377"
---
# <a name="tutorial-create-an-image-classification-project-using-the-custom-vision-service-with-python"></a>Oktatóanyag: Képosztályozó projekt készítése a Custom Vision Service és Python használatával

Megismerheti, hogyan lehet képosztályozó projektet készíteni a Custom Vision Service használatával és egy egyszerű Python parancsfájllal. Miután elkészült, adhat hozzá címkéket, tölthet fel képeket, betaníthatja a projektet, lekérheti a projekt alapértelmezett előrejelzési végpont URL-címét és ezt felhasználhatja a kép programozott tesztelésére. Ez a nyílt forráskódú példa sablonként használható saját, Custom Vision API használatával készülő alkalmazásaihoz.



## <a name="prerequisites"></a>Előfeltételek

- Python 2.7+ vagy Python 3.5+.
- A pip eszköz.

## <a name="get-the-training-and-prediction-keys"></a>A betanítási és előrejelzési kulcsok letöltése

Az ebben a példában használt kulcsok megszerzéséhez látogasson el a [Custom Vision weboldalra](https://customvision.ai), válassza ki a __fogaskerék ikont__ a jobb felső sarokban. A __Fiókok__ területen másolja ki a __Betanítási kulcs__ és __Előrejelzési kulcs__ mezők értékeit.

![A kulcsok felhasználói felület képe](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Telepítse a Custom Vision Service SDK-t

A Custom Vision Service SDK telepítéséhez használja a következő parancsot:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Minta képek lekérése

Ez a példa a képeket a [ https://github.com/Microsoft/Cognitive-CustomVision-Windows ](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) projekt `Samples/Images` könyvtárából veszi. Klónozza, vagy töltse le és csomagolja ki a projektet a saját fejlesztői környezetébe.

## <a name="create-a-custom-vision-service-project"></a>Custom Vision Service-projekt létrehozása

Új Custom Vision Service-projekt létrehozásához készítsen egy `sample.py` nevű új fájlt. A fájl tartalma a következő kód legyen:

> [!IMPORTANT]
> A `training_key` értékét állítsa be a korábban kapott betanítási kulcs értékére.
>
> A `prediction_key` értékét állítsa be a korábban kapott előrejelzési kulcs értékére.

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

A projekthez címkék hozzáadásához adja az alábbi kódot a `sample.py` fájl végéhez:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Képek feltöltése a projekthez

A minta képek projekthez adásához, helyezze el a következő kódot a címke létrehozása után. Ez a kód feltölti a képet a megfelelő címkével:

> [!IMPORTANT]
>
> A képekhez az elérési utat módosítsa arra, ahová a Cognitive-CustomVision – Windows-projektet korábban letöltötte.

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

## <a name="train-the-project"></a>A projekt tanítása

Az osztályozó betanítására adja hozzá az alábbi kódot a `sample.py` fájl végéhez:

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

## <a name="get-and-use-the-default-prediction-endpoint"></a>Szerezze meg és használja az alapértelmezett előrejelzési végpontot

Az előrejelzési végpontra kép küldéséhez és az előrejelzés lekéréséhez, adja a következő kódot a `sample.py` fájl végéhez:

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

## <a name="run-the-example"></a>A példa futtatása

Futtassa a megoldást. Az előrejelzési eredmények megjelennek a konzolon.

```
python sample.py
```

Az alkalmazás kimenete az alábbihoz szöveghez hasonló lesz:

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