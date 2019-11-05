---
title: 'Rövid útmutató: Objektumészlelési projekt létrehozása a Javához készült Custom Vision SDK-val'
titleSuffix: Azure Cognitive Services
description: Projekt létrehozása, címkék hozzáadása, képek feltöltése, projekt betanítása és objektumok észlelése a Java SDK használatával.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 65bf9a88b86bc0e27d848c941f104be0b237d054
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "73519020"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Rövid útmutató: Objektumészlelési projekt létrehozása a Javához készült Custom Vision SDK-val

Ez a cikk ahhoz biztosít információt és mintakódot, hogy megismerkedhessen a Custom Vision SDK és a Java együttes használatával egy objektumészlelési modell létrehozása céljából. Miután elkészült, adhat hozzá címkézett régiókat, tölthet fel képeket, betaníthatja a projektet, megkaphatja a projekt alapértelmezett előrejelzési végpont URL-címét és ezt a végpontot felhasználhatja kép programozott tesztelésére. Ezt a példát használja sablonként saját Java-alkalmazása létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Egy tetszőleges Java IDE
- Telepített [JDK 7 vagy 8](https://aka.ms/azure-jdks).
- Telepített Maven
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>A Custom Vision SDK és a mintakód letöltése

A Custom Visiont használó Java-alkalmazás megírásához a Custom Vision Maven-csomagokra lesz szüksége. Ezek a csomagok a letöltött minta projekt részét képezik, de ezeket külön-külön is elérheti.

A Custom Vision SDK telepíthető a maven központi tárából:
- [Betanítási SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Előrejelzési SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Klónozza vagy töltse le a [Cognitive Services Java SDK-mintákat](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) tartalmazó projektet. Lépjen a **Vision/CustomVision/** mappára.

Ez a Java-projekt létrehoz egy új objektumészlelési Custom Vision-projektet __Sample Java OD Project__ néven, amely a [Custom Vision webhelyén](https://customvision.ai/) keresztül érhető el. Utána feltölti a képeket az osztályozó tanítására és kipróbálására. Ebben a projektben az osztályozónak meg kell határoznia, hogy a fa egy __hemlokfenyő__ vagy pedig egy __japáncseresznye__.

[!INCLUDE [get-keys](includes/get-keys.md)]

A program a konfigurációja szerint az azonosító adatait környezeti változókként tárolja. A változók beállításához lépjen a **Vision/CustomVision** mappára a PowerShellben. Ezután írja be a következő parancsokat:

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>A kód értelmezése

Töltse be a `Vision/CustomVision` projektet a Java IDE-be, majd nyissa meg a _CustomVisionSamples.java_ fájlt. Keresse meg a **runSample** metódust, és véleményezze a **ImageClassification_Sample** metódus hívását&mdash;ez a metódus végrehajtja a képbesorolási forgatókönyvet, amely nem szerepel ebben az útmutatóban. Az **ObjectDetection_Sample** metódus valósítja meg ennek a rövid útmutatónak az elsődleges funkcióját – keresse meg a definícióját, és vizsgálja meg a kódot. 

### <a name="create-a-new-custom-vision-service-project"></a>Új Custom Vision Service-projekt létrehozása

Lépjen a kódblokkra, amely egy betanítási ügyfelet és egy objektumészlelési projektet hoz létre. A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. A projekt létrehozásakor további beállítások megadásához tekintse meg a [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) metódus túlterhelését (a [Kiderítő webportál összeállításának](get-started-build-detector.md) útmutatója).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Címkék hozzáadása a projekthez

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ha képeket címkéz meg az objektumészlelési projektekben, meg kell adnia a címkével ellátott objektumok régióját a normalizált koordináták használatával. Lépjen a `regionMap` térkép definíciójára. A kód mindegyik mintaképet a címkével ellátott régiójához társítja.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Ezután ugorjon a kódblokkra, amely a képeket adja hozzá a projekthez. A képek a projekt **src/main/resources** mappájából lesznek beolvasva, majd a megfelelő címkékkel és régiókoordinátákkal lesznek feltöltve a szolgáltatásba.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Az előző kódrészlet két segítő függvényt használ, amely erőforrás-adatfolyamként kéri le a lemezképeket, és feltölti őket a szolgáltatásba (egy kötegben akár 64 képet is feltölthet).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>A projekt betanítása és közzététel

Ez a kód létrehozza az első iterációt a projektben, majd közzéteszi az iterációt az előrejelzési végponton. A közzétett iterációhoz megadott név felhasználható az előrejelzési kérelmek küldésére. Egy iteráció nem érhető el az előrejelzési végponton, amíg közzé nem teszi.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Az előrejelzési végpont használata

Az előrejelzési végpont (itt a `predictor` objektum jelöli) az a hivatkozás, amellyel a képeket az aktuális modellnek elküldi, és osztályozási előrejelzéseket kér le. Ebben a példában a `predictor` máshol van definiálva az előrejelzés-azonosító környezeti változó használatával.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

A megoldás a Mavennel való fordításához és futtatásához futtassa a következő parancsot a PowerShellben lévő projektkönyvtárban:

```powershell
mvn compile exec:java
```

Tekintse meg a konzolon a naplózási és az előrejelzési eredményeket. Ezután ellenőrizheti, hogy a tesztkép megfelelően lett-e megcímkézve, és helyes-e az észlelési régió.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>További lépések

Láthatta, hogyan hajthatók végre az objektumészlelési folyamat lépései kódok használatával. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran előfordulhat, hogy a nagyobb pontosság érdekében többször is be kell tanítania és tesztelnie kell a modellt. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)
