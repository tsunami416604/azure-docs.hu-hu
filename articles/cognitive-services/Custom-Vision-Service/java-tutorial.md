---
title: 'Oktatóanyag: Képosztályozási projekt létrehozása a Javához készült Custom Vision SDK-val'
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
ms.openlocfilehash: d02dced071594732978f961c1b9d8effcd2e35ab
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415619"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Oktatóanyag: Képosztályozási projekt létrehozása a Javához készült Custom Vision SDK-val

Megismerheti, hogyan lehet képosztályozó projektet készíteni a Custom Vision Service használatával Java nyelven. Miután elkészült, adhat hozzá címkéket, tölthet fel képeket, betaníthatja a projektet, lekérheti a projekt alapértelmezett előrejelzési végpont URL-címét és ezt felhasználhatja a kép programozott tesztelésére. Ez a nyílt forráskódú példa sablonként használható saját, Custom Vision API használatával készülő alkalmazásaihoz.

## <a name="prerequisites"></a>Előfeltételek

- Telepített [JDK 7 vagy 8](https://aka.ms/azure-jdks).
- Maven telepítése.

## <a name="get-the-training-and-prediction-keys"></a>A betanítási és előrejelzési kulcsok letöltése

Az ebben a példában használt kulcsok megszerzéséhez látogasson el a [Custom Vision weboldalra](https://customvision.ai), válassza ki a __fogaskerék ikont__ a jobb felső sarokban. A __Fiókok__ területen másolja ki a __Betanítási kulcs__ és __Előrejelzési kulcs__ mezők értékeit.

![A kulcsok felhasználói felület képe](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Telepítse a Custom Vision Service SDK-t

A Custom Vision SDK telepíthető a maven központi tárából:
* [Betanítási SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Előrejelzési SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>A kód értelmezése

A teljes projekt a képeket is beleértve elérhető a [Java tárház Custom Vision Azure-minták](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) között. 

Kedvenc Java IDE környezetével nyissa meg a `Vision/CustomVision` projektet. 

Ez az alkalmazás a korábban a __Java Mintaprojekt__ nevű projekt létrehozásakor lekért betanítási kulcsot használja. Utána feltölti a képeket az osztályozó tanítására és kipróbálására. Az osztályozó azonosítja, hogy a fa __kanadai fenyő__ vagy __japán cseresznye__-e.

A példa fő funkcióit az alábbi kódrészlet valósítja meg:

## <a name="create-a-custom-vision-service-project"></a>Custom Vision Service-projekt létrehozása

> [!IMPORTANT]
> A `trainingApiKey` értékét állítsa be a korábban kapott betanítási kulcs értékére.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>Címkék hozzáadása a projekthez

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>Képek feltöltése a projekthez

A minta úgy van beállítva, hogy belevegye a képeket a végső csomagba. Kiolvassa a képeket a jar erőforrás részéről és feltölti a szolgáltatásnak.

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

Az előző kódrészlet azt a két segédfüggvényt használja, amelyek a képet erőforrás adatfolyamként feltöltik a szolgáltatásnak.

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>A projekt tanítása

Ez elkészíti az első iterációt a projektben és megjelöli alapértelmezett iterációként. 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Szerezze meg és használja az alapértelmezett előrejelzési végpontot

> [!IMPORTANT]
> A `predictionApiKey` értékét állítsa be a korábban kapott előrejelzési kulcs értékére.

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>A példa futtatása

A megoldást fordítsa és futtassa a maven segítségével:

```
mvn compile exec:java
```

Az alkalmazás kimenete az alábbihoz szöveghez hasonló lesz:

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```