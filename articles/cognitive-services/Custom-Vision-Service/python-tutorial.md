---
title: 'Rövid útmutató: Képosztályozási projekt létrehozása a Pythonhoz készült Custom Vision SDK-val'
titlesuffix: Azure Cognitive Services
description: Projekt létrehozása, címkék hozzáadása, képek feltöltése, projekt betanítása és előrejelzés létrehozása a Python SDK használatával.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 11/2/2018
ms.author: areddish
ms.openlocfilehash: c110abd9354134d52d4f82f7c828fc5e13f218a8
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262990"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Rövid útmutató: Képosztályozási projekt létrehozása a Custom Vision Python SDK-val

Ez a cikk ahhoz biztosít információt és mintakódot, hogy megismerkedhessen a Custom Vision SDK és a Python együttes használatával egy képosztályozási modell létrehozása céljából. Miután elkészült, adhat hozzá címkéket, tölthet fel képeket, betaníthatja a projektet, megkaphatja a projekt alapértelmezett előrejelzési végpont URL-címét és ezt a végpontot felhasználhatja kép programozott tesztelésére. Használja sablonként a példát a saját Python-alkalmazása létrehozásához. Ha az osztályozási modell létrehozásának és használatának folyamatán kód használata _nélkül_ szeretne végighaladni, tekintse meg a [böngészőalapú módszer útmutatóját](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Előfeltételek

- [A Python 2.7-es vagy újabb, illetve 3.5-ös vagy újabb verziója](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) eszköz

## <a name="install-the-custom-vision-sdk"></a>A Custom Vision SDK telepítése

A Pythonhoz készült Custom Vision Service SDK telepítéséhez futtassa a következő parancsot a PowerShellben:

```PowerShell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>A kód hozzáadása

Hozzon létre egy új fájlt *sample.py* néven a használni kívánt projektkönyvtárban.

### <a name="create-the-custom-vision-service-project"></a>A Custom Vision Service-projekt létrehozása

Adja hozzá a következő kódot a szkripthez egy új Custom Vision Service-projekt létrehozásához. Illessze be az előfizetői azonosítókat a megfelelő definíciókba.

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Címkék létrehozása a projektben

Ha besorolási címkéket szeretne létrehozni a projekthez, adja hozzá a következő kódot a *sample.py* fájl végéhez:

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

A minta képek projekthez adásához, helyezze el a következő kódot a címke létrehozása után. Ez a kód a képeket a hozzájuk tartozó címkékkel együtt tölti fel. Meg kell adnia az alapul szolgáló kép URL-címét az alapján, hogy hová töltötte le a Cognitive Services Python SDK-mintaprojektet.

> [!NOTE]
> Meg kell változtatnia a képek elérési útját az alapján, hogy hová töltötte le korábban a Cognitive Services Python SDK-mintaprojektet.

```Python
base_image_url = "<path to project>"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])
```

### <a name="train-the-classifier"></a>Az osztályozó betanítása

Ez a kód létrehozza az első iterációt a projektben, és alapértelmezett iterációként jelöli meg. Az alapértelmezett iteráció azt a modellverziót tükrözi, amely válaszolni fog az előrejelzési kérésekre. Mindig frissítse a modell újbóli betanításakor.

```Python
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

### <a name="get-and-use-the-default-prediction-endpoint"></a>Szerezze meg és használja az alapértelmezett előrejelzési végpontot

A képek előrejelzési végpontra való küldéséhez és az előrejelzés lekéréséhez adja hozzá a következő kódot a fájl végéhez:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa a *sample.py* fájlt.

```PowerShell
python sample.py
```

Az alkalmazás kimenetének az alábbi szöveghez hasonlóan kell kinéznie:

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

Ezután ellenőrizheti, hogy a tesztkép (az **<base_image_url>/Images/Test/** mappában található) megfelelően lett-e megcímkézve. Vissza is léphet a [Custom Vision webhelyére](https://customvision.ai), és megtekintheti az újonnan létrehozott projekt aktuális állapotát.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>További lépések

Láthatta, hogyan hajthatók végre a képosztályozási folyamat lépései kód használatával. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran előfordulhat, hogy a nagyobb pontosság érdekében többször is be kell tanítania és tesztelnie kell a modellt.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)