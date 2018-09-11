---
title: Hozzon létre egy egyéni vizuális szolgáltatás Java-oktatóanyag – Azure Cognitive Services |} A Microsoft Docs
description: Egy egyszerű Java-alkalmazást, amely a Custom Vision API a Microsoft Cognitive Services bemutatása. Hozzon létre egy projektet, adja hozzá a címkéket, tölthet fel képeket, a projekt betanítását és az alapértelmezett végpont használatával előrejelzést.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: a83a2f5cac9281a4cd79c1a0cead0f2af82d73df
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305700"
---
# <a name="use-custom-vision-api-to-build-an-image-classification-project-with-java"></a>Használja a Custom Vision API egy kép besorolási projekt javával

Ismerje meg, hogyan hozhat létre egy rendszerképet besorolási projekt a Custom Vision Service, a Java használatával. A létrehozást követően, is címkéket adhat hozzá, tölthet fel képeket, betanítását a projektet, a projekt alapértelmezett előrejelzési végpont URL-cím lekérése és, amellyel programozott módon a lemezkép tesztelése. A nyílt forráskódú példa sablonként használni a saját alkalmazás létrehozásához a Custom Vision API használatával.

## <a name="prerequisites"></a>Előfeltételek

- JDK 7 vagy 8 telepítve.
- A maven telepítése.

## <a name="get-the-training-and-prediction-keys"></a>A betanítási és Predikciós kulcsok beolvasása

Ebben a példában használt kulcsok beszerzéséhez látogasson el a [Custom Vision weblap](https://customvision.ai) , és válassza ki a __fogaskerék ikont__ kattintson a jobb felső sarokban. Az a __fiókok__ területén másolja a __képzési kulcs__ és __előrejelzési kulcs__ mezőket.

![A felhasználói felület kulcsok képe](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>A Custom Vision Service SDK telepítése

Az egyéni Látástechnológiai SDK maven központi tárházból telepíthető:
* [Képzési SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Előrejelzési SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>A kód értelmezése

A teljes projekt, képek, beleértve a érhető el a [Java tárház Custom Vision Azure-minták](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

A kedvenc Java IDE segítségével nyissa meg a `Vision/CustomVision` projekt. 

Ez az alkalmazás használ a betanítási kulcs nevű új projekt létrehozásához korábban lekért __minta Java-projektek__. Ezután feltölti a lemezképeket taníthat vagy tesztelhet egy osztályozó által igénybe vett. Az osztályozó által igénybe vett azonosítja egy fa-e egy __Hemlock__ vagy egy __japán cseresznye__.

Az alábbi kódrészleteket megvalósítása ebben a példában az elsődleges funkciója:

## <a name="create-a-custom-vision-service-project"></a>A Custom Vision Service-projekt létrehozása

> [!IMPORTANT]
> Állítsa be a `trainingApiKey` , a korábban kapott képzési kulcs értékét.

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

## <a name="upload-images-to-the-project"></a>A projekt képek feltöltése

A minta úgy beállítva a képeket is tartalmaznak a végső csomagba. Képek a jar erőforrás szakaszába olvasása és feltöltődtek a szolgáltatásra.

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

Az előző kódrészletben kód két segédfüggvények találhatók, amelyek a rendszerképeket, erőforrás-adatfolyamokat, és feltölti őket a szolgáltatás használ.

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

## <a name="train-the-project"></a>A projekt betanítása

Ez az első példányát hozza létre a projektet, és jelöli meg, az alapértelmezett iteráció aktuális ismétlődésének tömbjében. 

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

## <a name="get-and-use-the-default-prediction-endpoint"></a>Letöltheti a az alapértelmezett előrejelzési végpont

> [!IMPORTANT]
> Állítsa be a `predictionApiKey` , a korábban kapott előrejelzési kulcs értékét.

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

## <a name="run-the-example"></a>A példa Futtatás

Fordítsa le és futtassa a maven használatával szeretné megoldást:

```
mvn compile exec:java
```

Az alkalmazás kimenete az alábbi szöveghez hasonló:

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