---
title: Hozzon létre egy egyéni stratégiai szolgáltatás Python-oktatóanyag – Azure kognitív szolgáltatások |} Microsoft Docs
description: Fedezze fel egy alapszintű Python alkalmazást, amely az egyéni Látástechnológiai API kognitív Microsoft-szolgáltatásokban. Projekt létrehozása, címkéket, képek feltöltése, a projekt betanítását és az alapértelmezett végpont használatával előrejelzéshez ellenőrizze.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/07/2018
ms.author: areddish
ms.openlocfilehash: 0359935bf266d4f2a5cf845dd0d23183f4f77b72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348142"
---
# <a name="custom-vision-api-python-tutorial"></a>Egyéni Látástechnológiai API Python-oktatóanyag

Útmutató az egyéni stratégiai szolgáltatás, és egy alapszintű Python-parancsfájl egy kép besorolás-projekt létrehozása. Után létrejön, akkor is címkéket, képek feltöltése, a projekt betanítása, a projekt alapértelmezett előrejelzés végpont URL-cím beszerzése és programozott módon tesztelése a lemezkép használatával. A nyílt forráskódú példa sablonként használni a saját alkalmazás elkészítése az egyéni Látástechnológiai API-jával.



## <a name="prerequisites"></a>Előfeltételek

- Python 2.7 + vagy Python 3.5 +.
- A pip eszköz.

## <a name="get-the-training-and-prediction-keys"></a>A képzés és előrejelzés kulcsok beszerzése

Ebben a példában használt kulcsok beszerzéséhez látogassa meg a [egyéni stratégiai weblap](https://customvision.ai) válassza ki a __fogaskerék ikonra__ jobb felső részén található. Az a __fiókok__ szakaszban, másolja a __képzési kulcs__ és __előrejelzés kulcs__ mezőket.

![A felhasználói felület kulcsok képe](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Az egyéni stratégiai szolgáltatás SDK telepítése

Az egyéni stratégiai szolgáltatás SDK telepítéséhez használja a következő parancsot:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Példa képek beolvasása

Ez a példa a lemezképet rögzíthet a `Samples/Images` mappában található a [ https://github.com/Microsoft/Cognitive-CustomVision-Windows ](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) projekt. Klónozza, vagy töltse le, és bontsa ki a projekt a fejlesztői környezetében.

## <a name="create-a-custom-vision-service-project"></a>Egyéni stratégiai Service-projekt létrehozása

Hozzon létre egy új egyéni stratégiai szolgáltatási projektet, hozzon létre új fájlt `sample.py`. A fájl tartalmát az alábbira használata:

> [!IMPORTANT]
> Állítsa be a `training_key` korábban kapott képzési kulcs értékre.
>
> Állítsa be a `prediction_key` korábban kapott előrejelzés kulcs értékre.

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

Címkék hozzáadása a projekthez, vegye fel a következő kódot az végén a `sample.py` fájlt:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>A projekt képek feltöltése

A minta-lemezképek hozzáadása a projekthez, helyezze be a következő kódot a címke létrehozása után. Ez a kód feltölti a lemezképet a megfelelő címkével:

> [!IMPORTANT]
>
> A lemezképek, amelybe letöltötte a Cognitive-CustomVision-Windows-projekt korábbi alapján elérési útja módosítható.

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

## <a name="train-the-project"></a>A projekt képzése

A osztályozó betanításához adja hozzá a következő kódot végén a `sample.py` fájlt:

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

## <a name="get-and-use-the-default-prediction-endpoint"></a>Kérheti le és használja az alapértelmezett előrejelzés végpont

Kép küldése az előrejelzés a végponthoz, és az előrejelzés beolvasása, adja hozzá a következő kódot végén a `sample.py` fájlt:

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

## <a name="run-the-example"></a>A példa futtatásához

Futtassa a megoldást. Az előrejelzés eredményei jelennek meg a konzolon.

```
python sample.py
```

A kérelem kimenete az alábbihoz hasonló:

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