---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: a822ab52024a801f4443a9dd864b4b96ec52d000
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511307"
---
Ebből a cikkből megtudhatja, hogyan kezdheti el az első lépéseket az Custom Vision ügyféloldali kódtár használatával a Java segítségével egy objektum-észlelési modell létrehozásához. A létrehozást követően címkézett régiókat adhat hozzá, képeket tölthet fel, betaníthatja a projektet, beolvashatja a projekt alapértelmezett előrejelzési végpontjának URL-címét, és a végpont használatával programozott módon tesztelheti a lemezképeket. Ezt a példát használja sablonként saját Java-alkalmazása létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Egy tetszőleges Java IDE
- Telepített [JDK 7 vagy 8](https://aka.ms/azure-jdks).
- Telepített [Maven](https://maven.apache.org/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Az Custom Vision ügyféloldali kódtár és mintakód beszerzése

A Custom Visiont használó Java-alkalmazás megírásához a Custom Vision Maven-csomagokra lesz szüksége. Ezek a csomagok a letöltött minta projekt részét képezik, de ezeket külön-külön is elérheti.

A Custom Vision ügyféloldali kódtár a Maven Central adattárában található:
- [Betanítási SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Előrejelzési SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Klónozza vagy töltse le a [Cognitive Services Java SDK-mintákat](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) tartalmazó projektet. Lépjen a **Vision/CustomVision/** mappára.

Ez a Java-projekt létrehoz egy új objektumészlelési Custom Vision-projektet __Sample Java OD Project__ néven, amely a [Custom Vision webhelyén](https://customvision.ai/) keresztül érhető el. Utána feltölti a képeket az osztályozó tanítására és kipróbálására. Ebben a projektben az osztályozó célja annak megállapítása, hogy az objektum egy **elágazás** vagy **olló**.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

A program úgy van konfigurálva, hogy környezeti változókként hivatkozzon a legfontosabb adataira. Lépjen a **jövőkép/CustomVision** mappára, és adja meg a következő PowerShell-parancsokat a környezeti változók beállításához. 

> [!NOTE]
> Ha nem Windows operációs rendszert használ, tekintse meg a [környezeti változók konfigurálása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) az utasításokhoz című témakört.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>A kód értelmezése

Töltse be a `Vision/CustomVision` projektet a Java IDE-be, majd nyissa meg a _CustomVisionSamples.java_ fájlt. Keresse meg a **runSample** metódust, és véleményezze a **ImageClassification_Sample** metódus hívása &mdash; Ez a metódus végrehajtja a képbesorolási forgatókönyvet, amely nem szerepel ebben az útmutatóban. Az **ObjectDetection_Sample** metódus valósítja meg ennek a rövid útmutatónak az elsődleges funkcióját – keresse meg a definícióját, és vizsgálja meg a kódot. 

### <a name="create-a-new-custom-vision-service-project"></a>Új Custom Vision Service projekt létrehozása

Lépjen a kódblokkra, amely egy betanítási ügyfelet és egy objektumészlelési projektet hoz létre. A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. A projekt létrehozásakor további beállítások megadásához tekintse meg a [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) metódus túlterhelését (a [Kiderítő webportál összeállításának](../../get-started-build-detector.md) útmutatója).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Címkék hozzáadása a projekthez

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ha képeket címkéz az objektum-észlelési projektekben, az egyes címkézett objektumok régióját normalizált koordináták használatával kell megadnia. Lépjen a `regionMap` térkép definíciójára. A kód mindegyik mintaképet a címkével ellátott régiójához társítja.

> [!NOTE]
> Ha nem rendelkezik kattintással és húzással a régiók koordinátáinak megjelöléséhez, használhatja a webes felhasználói felületet a következő címen: [Customvision.ai](https://www.customvision.ai/). Ebben a példában a koordináták már meg vannak biztosítva.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Ezután ugorjon a kódblokkra, amely a képeket adja hozzá a projekthez. A képek a projekt **src/main/resources** mappájából lesznek beolvasva, majd a megfelelő címkékkel és régiókoordinátákkal lesznek feltöltve a szolgáltatásba.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Az előző kódrészlet két segítő függvényt használ, amely erőforrás-adatfolyamként kéri le a lemezképeket, és feltölti őket a szolgáltatásba (egy kötegben akár 64 képet is feltölthet).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>A projekt betanítása és közzététel

Ez a kód létrehozza az előrejelzési modell első iterációját, majd közzéteszi ezt az iterációt az előrejelzési végponton. A közzétett iterációhoz megadott név felhasználható az előrejelzési kérelmek küldésére. Egy iteráció nem érhető el az előrejelzési végponton, amíg közzé nem teszi.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Az előrejelzési végpont használata

Az előrejelzési végpont (itt a `predictor` objektum jelöli) az a hivatkozás, amellyel a képeket az aktuális modellnek elküldi, és osztályozási előrejelzéseket kér le. Ebben a példában a `predictor` máshol van definiálva az előrejelzés-azonosító környezeti változó használatával.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

A megoldás a Maven használatával történő fordításához és futtatásához navigáljon a Project Directory (**jövőkép/CustomVision**) parancssorba, és hajtsa végre a Run parancsot:

```bash
mvn compile exec:java
```

Tekintse meg a konzolon a naplózási és az előrejelzési eredményeket. Ezután ellenőrizheti, hogy a tesztkép megfelelően lett-e megcímkézve, és helyes-e az észlelési régió.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan végezhető el az objektum-észlelési folyamat minden lépése a kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran több alkalommal kell betanítania és tesztelni a modellt, hogy pontosabb legyen. A következő képzési útmutató a képbesorolással foglalkozik, de az alapelvei hasonlóak az objektumok észleléséhez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)
