---
title: 'Gyors útmutató: Rendszerkép-besorolási projekt létrehozása a Node. js-hez készült Custom Vision SDK-val'
titleSuffix: Azure Cognitive Services
description: Létrehozhat egy projektet, címkéket adhat hozzá, képeket tölthet fel, betaníthatja a projektet, és elvégezheti az előrejelzést a Node. js SDK használatával.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: edb9e50cceed95d8ca77567e7c1228704b5f177d
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982364"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-nodejs-sdk"></a>Gyors útmutató: Rendszerkép-besorolási projekt létrehozása a Custom Vision Node. js SDK-val

Ez a cikk az Custom Vision SDK és a Node. js használatával való ismerkedéshez nyújt segítséget a képbesorolási modell létrehozásához. A létrehozást követően címkéket adhat hozzá, képeket tölthet fel, betaníthatja a projektet, beolvashatja a projekt közzétett előrejelzési végpontjának URL-címét, és a végpont használatával programozott módon tesztelheti a lemezképeket. Ez a példa sablonként használható a saját Node. js-alkalmazás létrehozásához. Ha az osztályozási modell létrehozásának és használatának folyamatán kód használata _nélkül_ szeretne végighaladni, tekintse meg a [böngészőalapú módszer útmutatóját](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Előfeltételek

- A [Node. js 8](https://www.nodejs.org/en/download/) vagy újabb verziója telepítve van.
- a [NPM](https://www.npmjs.com/) telepítve van.

## <a name="install-the-custom-vision-sdk"></a>A Custom Vision SDK telepítése

A Node. js-hez készült Custom Vision Service SDK telepítéséhez futtassa a következő parancsot a PowerShellben:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [node-get-images](includes/node-get-images.md)]

## <a name="add-the-code"></a>A kód hozzáadása

Hozzon létre egy *sample. js* nevű új fájlt a kívánt Project-címtárban.

### <a name="create-the-custom-vision-service-project"></a>A Custom Vision Service-projekt létrehozása

Adja hozzá a következő kódot a szkripthez egy új Custom Vision Service-projekt létrehozásához. Szúrja be az előfizetési kulcsokat a megfelelő definíciók között, és állítsa a sampleDataRoot Path értéket a rendszerkép mappájának elérési útjára. Győződjön meg arról, hogy a végpont értéke megegyezik a [Customvision.ai](https://www.customvision.ai/)címen létrehozott betanítási és előrejelzési végpontokkal.

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApiClient = require("@azure/cognitiveservices-customvision-training");
const PredictionApiClient = require("@azure/cognitiveservices-customvision-prediction");

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

Ha besorolási címkéket szeretne létrehozni a projekthez, adja hozzá a következő kódot a *sample. js*végéhez:

```javascript
const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

A minta képek projekthez adásához, helyezze el a következő kódot a címke létrehozása után. Ez a kód a képeket a hozzájuk tartozó címkékkel együtt tölti fel. Egyetlen kötegben akár 64 képet is feltölthet.

> [!NOTE]
> A *sampleDataRoot* a lemezképek elérési útjára kell váltania, attól függően, hogy a Cognitive Services Node. js SDK Samples projektet korábban letöltötte.

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

### <a name="train-the-classifier-and-publish"></a>Az osztályozó és a közzététel betanítása

Ez a kód létrehozza az első iterációt a projektben, majd közzéteszi az iterációt az előrejelzési végponton. A közzétett iterációhoz megadott név felhasználható az előrejelzési kérelmek küldésére. Egy iteráció nem érhető el az előrejelzési végponton, amíg közzé nem teszi.

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

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>A közzétett iteráció lekérése és használata az előrejelzési végponton

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

Futtassa a *sample. js fájlt*.

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
