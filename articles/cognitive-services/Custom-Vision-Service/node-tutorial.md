---
title: 'Gyors útmutató: Hozzon létre egy rendszerképet besorolási projektet az egyéni Látástechnológiai SDK-val a node.js-ben'
titlesuffix: Azure Cognitive Services
description: Hozzon létre egy projektet, adja hozzá a címkéket, tölthet fel képeket, a projekt betanítását és a Node.js SDK használatával előrejelzést.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 9d9021cd3acaebe689c583281e0316b30d5892c0
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482453"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-nodejs-sdk"></a>Gyors útmutató: A Custom Vision Node.js SDK-val kép besorolási projekt létrehozása

Ez a cikk ismerteti, információt és segítséget nyújtanak a mintakódot SDK használatának első lépései a Custom Vision a node.js használatával hozhat létre egy rendszerképet osztályozási modell. A létrehozást követően, akkor is címkéket adhat hozzá, tölthet fel képeket, betanítását a projekt, a projekt közzétett előrejelzési végponti URL-cím beszerzése és ezt a végpont programozott módon képet. Ez a példa sablonként használni a saját Node.js-alkalmazás létrehozásához. Ha az osztályozási modell létrehozásának és használatának folyamatán kód használata _nélkül_ szeretne végighaladni, tekintse meg a [böngészőalapú módszer útmutatóját](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Előfeltételek

- [NODE.js 8](https://www.nodejs.org/en/download/) vagy újabb verziója szükséges.
- [az npm](https://www.npmjs.com/) telepítve.

## <a name="install-the-custom-vision-sdk"></a>A Custom Vision SDK telepítése

A Custom Vision service SDK a Node.js telepítéséhez futtassa a következő parancsot a PowerShellben:

```powershell
npm install azure-cognitiveservices-customvision-training
npm install azure-cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [node-get-images](includes/node-get-images.md)]

## <a name="add-the-code"></a>A kód hozzáadása

Hozzon létre egy új fájlt *sample.js* az előnyben részesített projekt könyvtárában.

### <a name="create-the-custom-vision-service-project"></a>A Custom Vision Service-projekt létrehozása

Adja hozzá a következő kódot a szkripthez egy új Custom Vision Service-projekt létrehozásához. Illessze be az előfizetői azonosítókat a megfelelő definíciókba.

```javascript
const util = require('util');
const TrainingApi = require("azure-cognitiveservices-customvision-training");
const PredictionApi = require("azure-cognitiveservices-customvision-prediction");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://southcentralus.api.cognitive.microsoft.com"

const publishIterationName = "classifyModel";

const trainer = new TrainingApiClient(trainingKey, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project")
```

### <a name="create-tags-in-the-project"></a>Címkék létrehozása a projektben

Besorolási címkéket, hogy a projekt létrehozásához adja hozzá a következő kódot a végéig *sample.js*:

```javascript
    const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
    const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

A minta képek projekthez adásához, helyezze el a következő kódot a címke létrehozása után. Ez a kód a képeket a hozzájuk tartozó címkékkel együtt tölti fel. Adja meg az alaplemezkép elérési útja, amelybe letöltötte a Cognitive Services Node.js SDK-minták projekt alapján kell.

> [!NOTE]
> Módosítani kell *sampleDataRoot* , amelybe letöltötte a Cognitive Services Node.js SDK-minták alapján a képek elérési projektet korábban.

```javascript
    console.log("Adding images...");
    let fileUploadPromises = [];

    const hemlockDir = `${sampleDataRoot}/Hemlock`;
    const hemlockFiles = fs.readdirSync(hemlockDir);
    hemlockFiles.forEach(file => {
        fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${hemlockDir}/${file}`), { tagIds: [hemlockTag.id] }));
    });

    const cherryDir = `${sampleDataRoot}/Japanese Cherry`;
    const japaneseCherryFiles = fs.readdirSync(cherryDir);
    japaneseCherryFiles.forEach(file => {
        fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${cherryDir}/${file}`), { tagIds: [cherryTag.id] }));
    });

    await Promise.all(fileUploadPromises);
```

### <a name="train-the-classifier-and-publish"></a>Az osztályozó által igénybe vett betanítás, közzététel

Ez a kód a projektet hoz létre az első példányát, és majd az előrejelzési végpontot tesz közzé, hogy az iteráció. Név, a közzétett iteráció előrejelzési kérelmek küldésére használható. Egy iteráció nem áll rendelkezésre előrejelzési végpontját, amíg közzé van téve.

```javascript
    console.log("Training...");
    let trainingIteration = await trainer.trainProject(sampleProject.id);

    // Wait for training to complete
    console.log("Training started...");
    while (trainingIteration.status == "Training") {
        console.log("Training status: " + trainingIteration.status);
        await setTimeoutPromise(1000, null);
        trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
    }
    console.log("Training status: " + trainingIteration.status);
    
    // Publish the iteration to the end point
    await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Letöltheti a közzétett ismétléseinek előrejelzési végpont

A képek előrejelzési végpontra való küldéséhez és az előrejelzés lekéréséhez adja hozzá a következő kódot a fájl végéhez:

```javascript
    const predictor = new PredictionApiClient(predictionKey, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_image.jpg`);

    const results = await predictor.classifyImage(sampleProject.id, publishIterationName, testFile);

    // Step 6. Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}%`);
    });
})()
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtatás *sample.js*.

```powershell
node sample.js
```

Az alkalmazás kimenetének az alábbi szöveghez hasonlóan kell kinéznie:

```
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

Ezután ellenőrizheti, hogy a tesztkép (az **<base_image_url>/Images/Test/** mappában található) megfelelően lett-e megcímkézve. Vissza is léphet a [Custom Vision webhelyére](https://customvision.ai), és megtekintheti az újonnan létrehozott projekt aktuális állapotát.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>További lépések

Láthatta, hogyan hajthatók végre a képosztályozási folyamat lépései kód használatával. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran előfordulhat, hogy a nagyobb pontosság érdekében többször is be kell tanítania és tesztelnie kell a modellt.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)