---
title: 'Gyors útmutató: A Góhoz készült egyéni Látástechnológiai SDK egy kép besorolási projekt létrehozása'
titlesuffix: Azure Cognitive Services
description: Hozzon létre egy projektet, adja hozzá a címkéket, tölthet fel képeket, a projekt betanítását és a Go SDK használatával előrejelzést.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: f740974d17ad5f95bca6530a61619ee0283f819a
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58479980"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-go-sdk"></a>Gyors útmutató: A Custom Vision Go SDK egy kép besorolási projekt létrehozása

Ez a cikk ismerteti, információt és segítséget nyújtanak a mintakódot SDK használatának első lépései a Custom Vision a Go hozhat létre egy rendszerképet osztályozási modell. A létrehozást követően, akkor is címkéket adhat hozzá, tölthet fel képeket, betanítását a projekt, a projekt közzétett előrejelzési végponti URL-cím beszerzése és ezt a végpont programozott módon képet. Ez a példa sablonként használni, amellyel a Go-alkalmazást. Ha az osztályozási modell létrehozásának és használatának folyamatán kód használata _nélkül_ szeretne végighaladni, tekintse meg a [böngészőalapú módszer útmutatóját](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Előfeltételek

- [Go 1.8-as +](https://golang.org/doc/install)

## <a name="install-the-custom-vision-sdk"></a>A Custom Vision SDK telepítése

A Custom Vision service SDK a Góhoz készült telepítéséhez futtassa a következő parancsot a PowerShellben:

```
go get -u github.com/Azure/azure-sdk-for-go/...
```

vagy futtassa a tárházban lévő dep, használatakor:
```
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>A kód hozzáadása

Hozzon létre egy új fájlt *sample.go* az előnyben részesített projekt könyvtárában.

### <a name="create-the-custom-vision-service-project"></a>A Custom Vision Service-projekt létrehozása

Adja hozzá a következő kódot a szkripthez egy új Custom Vision Service-projekt létrehozásához. Illessze be az előfizetői azonosítókat a megfelelő definíciókba.

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "https://southcentralus.api.cognitive.microsoft.com"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

### <a name="create-tags-in-the-project"></a>Címkék létrehozása a projektben

Besorolási címkéket, hogy a projekt létrehozásához adja hozzá a következő kódot a végéig *sample.go*:

```go
    // Make two tags in the new project
    hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
    cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

A minta képek projekthez adásához, helyezze el a következő kódot a címke létrehozása után. Ez a kód a képeket a hozzájuk tartozó címkékkel együtt tölti fel. Adja meg az alaplemezkép URL-címet, ahová letöltötte a Cognitive Services-Go SDK minták projekt alapján kell.

> [!NOTE]
> Az elérési utat módosítsa arra a képek, ahol a Cognitive Services-Go SDK minták projektet korábban letöltött alapján kell.

```go
    fmt.Println("Adding images...")
    japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
    if err != nil {
        fmt.Println("Error finding Sample images")
    }

    hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
    if err != nil {
        fmt.Println("Error finding Sample images")
    }

    for _, file := range hemLockImages {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
        imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

        trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
    }

    for _, file := range japaneseCherryImages {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
        imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
        trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
    }
```

### <a name="train-the-classifier-and-publish"></a>Az osztályozó által igénybe vett betanítás, közzététel

Ez a kód a projektet hoz létre az első példányát, és majd az előrejelzési végpontot tesz közzé, hogy az iteráció. Név, a közzétett iteráció előrejelzési kérelmek küldésére használható. Egy iteráció nem áll rendelkezésre előrejelzési végpontját, amíg közzé van téve.

```go
    fmt.Println("Training...")
    iteration, _ := trainer.TrainProject(ctx, *project.ID)
    for {
        if *iteration.Status != "Training" {
            break
        }
        fmt.Println("Training status: " + *iteration.Status)
        time.Sleep(1 * time.Second)
        iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
    }
    fmt.Println("Training status: " + *iteration.Status)

    trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Letöltheti a közzétett ismétléseinek előrejelzési végpont

A képek előrejelzési végpontra való küldéséhez és az előrejelzés lekéréséhez adja hozzá a következő kódot a fájl végéhez:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtatás *sample.go*.

```powershell
go run sample.go
```

Az alkalmazás kimenetének az alábbi szöveghez hasonlóan kell kinéznie:

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
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