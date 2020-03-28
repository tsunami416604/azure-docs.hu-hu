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
ms.date: 02/25/2020
ms.author: areddish
ms.openlocfilehash: 78db95240974d1c9ca07546f8237eca2b564ecb2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77616327"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Rövid útmutató: Objektumészlelési projekt létrehozása a Javához készült Custom Vision SDK-val

Ez a cikk bemutatja, hogyan kezdheti el használni a Custom Vision SDK Java egy objektumészlelési modell létrehozásához. A létrehozást követően címkézett régiókat adhat hozzá, képeket tölthet fel, betaníthatja a projektet, beolvashatja a projekt alapértelmezett előrejelzési végpontjának URL-címét, és a végpont használatával programozott módon tesztelheti a lemezképeket. Ezt a példát használja sablonként saját Java-alkalmazása létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Egy tetszőleges Java IDE
- Telepített [JDK 7 vagy 8](https://aka.ms/azure-jdks).
- [Maven](https://maven.apache.org/) telepítve
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Custom Vision SDK és mintakód beszerzése

A Custom Visiont használó Java-alkalmazás megírásához a Custom Vision Maven-csomagokra lesz szüksége. Ezek a csomagok szerepelnek a letölteni kívánt mintaprojektben, de külön-külön is elérheti őket itt.

A Custom Vision SDK a maven központi adattárban található:
- [Betanítási SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Előrejelzési SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Klónozza vagy töltse le a [Cognitive Services Java SDK-mintákat](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) tartalmazó projektet. Lépjen a **Vision/CustomVision/** mappára.

Ez a Java-projekt létrehoz egy új objektumészlelési Custom Vision-projektet __Sample Java OD Project__ néven, amely a [Custom Vision webhelyén](https://customvision.ai/) keresztül érhető el. Utána feltölti a képeket az osztályozó tanítására és kipróbálására. Ebben a projektben az osztályozó célja annak meghatározása, hogy egy objektum **villa** vagy **olló.**

[!INCLUDE [get-keys](includes/get-keys.md)]

A program úgy van beállítva, hogy a kulcsadatokra környezeti változókként hivatkozzon. Nyissa meg a **Vision/CustomVision** mappát, és írja be a következő PowerShell-parancsokat a környezeti változók beállításához. 

> [!NOTE]
> Ha nem Windows operációs rendszert használ, olvassa el a [Környezeti változók konfigurálása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) című témakört.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>A kód értelmezése

Töltse be a `Vision/CustomVision` projektet a Java IDE-be, majd nyissa meg a _CustomVisionSamples.java_ fájlt. Keresse meg a **runSample** **ImageClassification_Sample** metódust,&mdash;és megjegyzést a ImageClassification_Sample metódus hívása ez a metódus végrehajtja a rendszerkép besorolási forgatókönyv, amely nem szerepel ebben az útmutatóban. Az **ObjectDetection_Sample** metódus valósítja meg ennek a rövid útmutatónak az elsődleges funkcióját – keresse meg a definícióját, és vizsgálja meg a kódot. 

### <a name="create-a-new-custom-vision-service-project"></a>Új Custom Vision Service projekt létrehozása

Lépjen a kódblokkra, amely egy betanítási ügyfelet és egy objektumészlelési projektet hoz létre. A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. Tekintse meg a [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) metódus túlterhelését, ha a projekt létrehozásakor további beállításokat is megadhat (ezt a forrás az [Érzékelő](get-started-build-detector.md) webportál-útmutatójában ismerteti).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Címkék hozzáadása a projekthez

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ha képeket címkéz az objektum-észlelési projektekben, az egyes címkézett objektumok régióját normalizált koordináták használatával kell megadnia. Lépjen a `regionMap` térkép definíciójára. A kód mindegyik mintaképet a címkével ellátott régiójához társítja.

> [!NOTE]
> Ha nem rendelkezik kattintással és húzással a régiók koordinátáinak megjelöléséhez, használhatja a webes felhasználói felületet [a Customvision.ai.](https://www.customvision.ai/) Ebben a példában a koordináták már meg vannak adva.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Ezután ugorjon a kódblokkra, amely a képeket adja hozzá a projekthez. A képek a projekt **src/main/resources** mappájából lesznek beolvasva, majd a megfelelő címkékkel és régiókoordinátákkal lesznek feltöltve a szolgáltatásba.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Az előző kódrészlet két segítő függvényt használ, amelyek erőforrás-adatfolyamként kérik le a képeket, és feltöltik őket a szolgáltatásba (legfeljebb 64 lemezképet tölthet fel egyetlen kötegben).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>A projekt betanítása és közzététele

Ez a kód létrehozza az előrejelzési modell első iterációját, majd közzéteszi az előrejelzési végpontra vonatkozó iterációt. A közzétett iterációhoz megadott név felhasználható az előrejelzési kérelmek küldésére. Iteráció nem érhető el az előrejelzési végpontban, amíg közzé nem teszik.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Az előrejelzési végpont használata

Az előrejelzési végpont (itt a `predictor` objektum jelöli) az a hivatkozás, amellyel a képeket az aktuális modellnek elküldi, és osztályozási előrejelzéseket kér le. Ebben a példában a `predictor` máshol van definiálva az előrejelzés-azonosító környezeti változó használatával.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

A megoldás maven használatával történő fordításához és futtatásához keresse meg a projektkönyvtárat (**Vision/CustomVision**) egy parancssorban, és hajtsa végre a futtatási parancsot:

```bash
mvn compile exec:java
```

Tekintse meg a konzolon a naplózási és az előrejelzési eredményeket. Ezután ellenőrizheti, hogy a tesztkép megfelelően lett-e megcímkézve, és helyes-e az észlelési régió.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>További lépések

Láthatta, hogyan hajthatók végre az objektumészlelési folyamat lépései kódok használatával. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran előfordulhat, hogy a nagyobb pontosság érdekében többször is be kell tanítania és tesztelnie kell a modellt. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)
