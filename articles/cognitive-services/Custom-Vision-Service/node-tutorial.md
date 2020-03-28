---
title: 'Rövid útmutató: Hozzon létre egy lemezképbesorolási projektet a Node.js egyéni vision SDK-js-sel'
titleSuffix: Azure Cognitive Services
description: Hozzon létre egy projektet, címkéket adjon hozzá, képeket töltsön fel, tanítsa be a projektet, és készítsen előrejelzést a Node.js SDK használatával.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: f1c0d8f72fe59ff9a8c0fdba86d97ea588a9a808
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366622"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-nodejs-sdk"></a>Rövid útmutató: Hozzon létre egy képbesorolási projektet az egyéni látótérnode.js SDK-val

Ez a cikk bemutatja, hogyan kezdheti el használni a Custom Vision SDK node.js egy lemezkép besorolási modell létrehozásához. Létrehozása után hozzáadhat címkéket, képeket tölthet fel, betaníthatja a projektet, beszerezheti a projekt közzétett előrejelzési végpont URL-címét, és a végpont segítségével programozott módon tesztelhet egy képet. Ebben a példában sablonként hozhatja fel saját Node.js alkalmazását. Ha az osztályozási modell létrehozásának és használatának folyamatán kód használata _nélkül_ szeretne végighaladni, tekintse meg a [böngészőalapú módszer útmutatóját](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Előfeltételek

- [Node.js 8](https://www.nodejs.org/en/download/) vagy újabb telepítve.
- [npm](https://www.npmjs.com/) telepítve.
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>A Custom Vision SDK telepítése

A Node.js egyéni vision szolgáltatásának telepítéséhez futtassa a következő parancsot a PowerShellben:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [node-get-images](includes/node-get-images.md)]

## <a name="add-the-code"></a>A kód hozzáadása

Hozzon létre egy *új fájlt sample.js* néven az előnyben részesített projektkönyvtárban.

### <a name="create-the-custom-vision-service-project"></a>A Custom Vision Service-projekt létrehozása

Adja hozzá a következő kódot a szkripthez egy új Custom Vision Service-projekt létrehozásához. Szúrja be az előfizetési kulcsokat a megfelelő definíciókba, és állítsa be a sampleDataRoot elérési útját a képmappa elérési útjához. Győződjön meg arról, hogy a végpont értéke megegyezik a [Customvision.ai](https://www.customvision.ai/)létrehozott betanítási és előrejelzési végpontokkal. Vegye figyelembe, hogy az objektumészlelési és a képbesorolási projekt létrehozása közötti különbség a **createProject** hívásban megadott tartomány.

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApi = require("@azure/cognitiveservices-customvision-training");
const PredictionApi = require("@azure/cognitiveservices-customvision-prediction");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://<my-resource-name>.cognitiveservices.azure.com/"

const publishIterationName = "classifyModel";

const trainer = new TrainingApi.TrainingAPIClient(trainingKey, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project")
```

### <a name="create-tags-in-the-project"></a>Címkék létrehozása a projektben

Ha besorolási címkéket szeretne létrehozni a projekthez, adja hozzá a következő kódot a *sample.js végéhez:*

```javascript
const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

A minta képek projekthez adásához, helyezze el a következő kódot a címke létrehozása után. Ez a kód a képeket a hozzájuk tartozó címkékkel együtt tölti fel. Egy kötegben legfeljebb 64 képet tölthet fel.

> [!NOTE]
> A cognitive Services Node.js SDK Samples projekt korábbi letöltési helye alapján módosítania kell a *mintaDataRoot-ot* a képek elérési útján.

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

### <a name="train-the-classifier-and-publish"></a>Az osztályozó betanítása és közzététele

Ez a kód létrehozza az előrejelzési modell első iterációját, majd közzéteszi az előrejelzési végpontra vonatkozó iterációt. A közzétett iterációhoz megadott név felhasználható az előrejelzési kérelmek küldésére. Iteráció nem érhető el az előrejelzési végpontban, amíg közzé nem teszik.

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

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>A közzétett iteráció beszerezni és használata az előrejelzési végponton

A képek előrejelzési végpontra való küldéséhez és az előrejelzés lekéréséhez adja hozzá a következő kódot a fájl végéhez:

```javascript
    const predictor = new PredictionApi.PredictionAPIClient(predictionKey, endPoint);
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

*Futtassa a sample.js .*

```shell
node sample.js
```

Az alkalmazás kimenetének az alábbi szöveghez hasonlóan kell kinéznie:

```console
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
