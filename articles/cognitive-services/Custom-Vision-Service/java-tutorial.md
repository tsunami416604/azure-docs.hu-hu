---
title: 'Rövid útmutató: Képosztályozási projekt létrehozása a Javához készült Custom Vision SDK-val'
titleSuffix: Azure Cognitive Services
description: Projekt létrehozása, címkék hozzáadása, képek feltöltése, projekt betanítása és előrejelzés létrehozása a Java SDK használatával.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: areddish
ms.openlocfilehash: 271f80785d9db84b9184f9f3c6ede2725d4607dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403701"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Rövid útmutató: Képosztályozási projekt létrehozása a Javához készült Custom Vision SDK-val

Ez a cikk bemutatja, hogyan kezdheti el használni a Custom Vision Java SDK egy lemezkép besorolási modell létrehozásához. Miután elkészült, adhat hozzá címkéket, tölthet fel képeket, betaníthatja a projektet, megkaphatja a projekt alapértelmezett előrejelzési végpont URL-címét és ezt a végpontot felhasználhatja kép programozott tesztelésére. Ezt a példát használja sablonként saját Java-alkalmazása létrehozásához. Ha az osztályozási modell létrehozásának és használatának folyamatán kód használata _nélkül_ szeretne végighaladni, tekintse meg a [böngészőalapú módszer útmutatóját](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Előfeltételek

- Egy tetszőleges Java IDE
- Telepített [JDK 7 vagy 8](https://aka.ms/azure-jdks).
- [Maven](https://maven.apache.org/) telepítve
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Custom Vision SDK és mintakód beszerzése

A Custom Visiont használó Java-alkalmazás megírásához a Custom Vision Maven-csomagokra lesz szüksége. Ezek a csomagok a letöltött minta projekt részét képezik, de ezeket külön-külön is elérheti.

A Custom Vision SDK a maven központi adattárban található:

- [Betanítási SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Előrejelzési SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Klónozza vagy töltse le a [Cognitive Services Java SDK-mintákat](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) tartalmazó projektet. Lépjen a **Vision/CustomVision/** mappára.

Ez a Java-projekt létrehoz egy új képosztályozási Custom Vision-projektet __Sample Java Project__ néven, amely a [Custom Vision webhelyén](https://customvision.ai/) keresztül érhető el. Utána feltölti a képeket az osztályozó tanítására és kipróbálására. Ebben a projektben az osztályozónak meg kell határoznia, hogy a fa egy __hemlokfenyő__ vagy pedig egy __japáncseresznye__.

[!INCLUDE [get-keys](includes/get-keys.md)]

A program úgy van beállítva, hogy a kulcsadatokra környezeti változókként hivatkozzon. Nyissa meg a **Vision/CustomVision** mappát, és írja be a következő PowerShell-parancsokat a környezeti változók beállításához. 

> [!NOTE]
> Ha nem Windows operációs rendszert használ, olvassa el a [Környezeti változók konfigurálása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) című témakört.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>A kód értelmezése

Töltse be a `Vision/CustomVision` projektet a Java IDE-be, majd nyissa meg a _CustomVisionSamples.java_ fájlt. Keresse meg a **runSample** metódust,&mdash;és megjegyzést fűzzön a metódus **ObjectDetection_Sample** metódus hívása, ez a metódus végrehajtja az objektumészlelési forgatókönyvet, amelyet ez az útmutató nem fedez fel. Az **ImageClassification_Sample** metódus valósítja meg ennek a példának az elsődleges funkcióját – keresse meg a definícióját, és vizsgálja meg a kódot.

### <a name="create-a-custom-vision-service-project"></a>Custom Vision Service-projekt létrehozása

Az első kódrészlet létrehoz egy képosztályozási projektet. A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. Tekintse meg a [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) metódus túlterhelését, ha a projekt létrehozásakor további beállításokat is megadhat (ezt az [Osztályozó webportál készítése](getting-started-build-a-classifier.md) című útmutató ismerteti).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Címkék létrehozása a projektben

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

A mintaképeket a projekt **src/main/resources** mappája tartalmazza. Innen lesznek beolvasva, majd a megfelelő címkékkel feltöltve a szolgáltatásba.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

Az előző kódrészlet két segítő függvényt használ, amelyek erőforrás-adatfolyamként kérik le a képeket, és feltöltik őket a szolgáltatásba (legfeljebb 64 lemezképet tölthet fel egyetlen kötegben).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-classifier-and-publish"></a>Az osztályozó betanítása és közzététele

Ez a kód létrehozza az előrejelzési modell első iterációját, majd közzéteszi az előrejelzési végpontra vonatkozó iterációt. A közzétett iterációhoz megadott név felhasználható az előrejelzési kérelmek küldésére. Iteráció nem érhető el az előrejelzési végpontban, amíg közzé nem teszik.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

### <a name="use-the-prediction-endpoint"></a>Az előrejelzési végpont használata

Az előrejelzési végpont (itt a `predictor` objektum jelöli) az a hivatkozás, amellyel a képeket az aktuális modellnek elküldi, és osztályozási előrejelzéseket kér le. Ebben a példában a `predictor` máshol van definiálva az előrejelzés-azonosító környezeti változó használatával.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

A megoldás maven használatával történő fordításához és futtatásához keresse meg a projektkönyvtárat (**Vision/CustomVision**) egy parancssorban, és hajtsa végre a futtatási parancsot:

```bash
mvn compile exec:java
```

Az alkalmazás konzolkimenetének az alábbi szöveghez hasonlóan kell kinéznie:

```console
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

Ezután ellenőrizheti, hogy a tesztkép előrejelzése (a kimenet utolsó sorai) megfelelő-e.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>További lépések

Most már látta, hogy az objektumészlelési folyamat minden lépése hogyan végezhető el kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran többször is be kell képeznie és tesztelnie kell a modellt annak érdekében, hogy pontosabb legyen.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)
