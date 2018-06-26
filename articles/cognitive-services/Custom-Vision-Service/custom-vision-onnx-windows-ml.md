---
title: Egyéni stratégiai ONNX modell Windows ml - kognitív szolgáltatások |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Windows UWP-alkalmazást, amely kognitív szolgáltatások exportált ONNX modellt használ.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 0b128ba1800e74c20c09a9c5711c8473f1dd00d0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939473"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Oktatóanyag: Az egyéni stratégiai ONNX modell használata Windows ML (előzetes verzió)

Ismerje meg, hogyan használható az exportált Windows ml (előzetes verzió) egyéni stratégiai szolgáltatásból ONNX modell.

A jelen dokumentumban szereplő információk az egyéni stratégiai szolgáltatás Windows ml-ból exportált ONNX fájl mutatja be. Például Windows UWP-alkalmazás biztosítja. A példa a betanított modell, amely fel tudja ismerni kutya és macska része. Hogyan használhatja a saját modell az ebben a példában a lépéseket is biztosítja.

> [!div class="checklist"]
> * A példa alkalmazással kapcsolatos
> * A mintakód beolvasása
> * A példa futtatásához
> * A saját modellt használnak

## <a name="prerequisites"></a>Előfeltételek

* A Windows 10-eszközre:

    * A kamera.

    * A Visual Studio 2017 15.7 vagy újabb verziója a __univerzális Windows Platform fejlesztési__ munkaterhelés engedélyezve van.

    * Fejlesztői mód engedélyezve van. További információkért lásd: a [lehetővé teszik az eszköz fejlesztési](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentum.

* (Választható) Egy egyéni stratégiai szolgáltatásból exportált ONNX fájlt. További információkért lásd: a [a modell használatra exportálása a mobileszközökkel](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentum.

    > [!NOTE]
    > A saját modell használatához kövesse a [használhatja a saját](#use-your-own-model) szakasz.

## <a name="about-the-example-app"></a>A példa alkalmazással kapcsolatos

Az alkalmazásra az általános Windows UWP-alkalmazást. Adja meg a képet, hogy a modell a kamera használ a Windows 10-eszközön. A címkék és a modell által visszaadott eredmények a villámnézet alatt jelennek meg.

* Mivel az adatok származnak, ha a kamera [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) egyes keretek kibontására. A képkockák pontozó kell küldeni a modell.

* A modell a címkéket a be lett tanítva, és egy lebegőpontos érték, amely jelzi, hogyan abban, hogy a kép tartalmazza-e, hogy az elem adja vissza.

### <a name="the-ui"></a>A felhasználói felületen

A mintaalkalmazás felhasználói felülete létre __CaptureElement__ és __TextBlock__ szabályozza. A CaptureElement a kamera videó előnézetét, valamint a TextBlock a modellből az adott eredmények jeleníti meg. 

### <a name="the-model"></a>A modell

A modell (`cat-or-dog.onnx`) található meg a példa hozta létre, és a szolgáltatások egyéni kognitív stratégiai szolgáltatással betanítása. A betanított modell majd egy ONNX modell típusúként lett exportálva. Ez a szolgáltatás használatával további információkért lásd: a [hogyan hozhat létre egy osztályozó](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) és [a modell használatra exportálása a mobileszközökkel](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentumok.

> [!IMPORTANT]
> A példa a megadott mintának kutya és cat képek kis számú lett képezni. Ezért nem lehet a világ legjobb: kutya és macska ismer fel.

### <a name="the-model-class-file"></a>A modellfájl osztály

ONNX fájl hozzáadása egy Windows UWP-alkalmazást, létrehoz egy .cs fájlt. Ez a fájl rendelkezik neve megegyezik a `.onnx` fájl (`cat-or-dog` ebben a példában), és tartalmazza a modell C# használt osztályok. Azonban a létrehozott osztály entitást lehet például a `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Biztonságosan nevezze át ezeket a bejegyzéseket (kattintson a jobb gombbal, nevezze át) egy rövid nevet.

> [!NOTE]
> A példakódot tartalmaz átkerült a generált osztály és a metódus nevét a következő:
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Kamera hozzáférés

A __képességek__ lapra a `Package.appxmanifest` fájl van konfigurálva, hogy hozzáférjenek a felhasználó webkamerája és mikrofonja.

> [!NOTE]
> Annak ellenére, hogy ez a példa hang nem használ, szükség lenne a mikrofon engedélyezése előtt szeretnék tudni elérni az eszközön a kamera.

Az alkalmazás megpróbálja beolvasni hátoldalán található az eszköz kamerájának, ha elérhető ilyen. Használja a [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) el a kamera videó osztály. [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) videó keretek rögzítése, és küldje el a modell használatával.

## <a name="get-the-example-code"></a>A mintakód beolvasása

A mintaalkalmazás érhető el: [ https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP ](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>A példa futtatásához

1. Használja a `F5` kulcs az alkalmazás indításához a Visual Studio eszközből. Fejlesztői mód engedélyezése kérheti. További információkért lásd: a [lehetővé teszik az eszköz fejlesztési](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentum.

2. Amikor a rendszer kéri, lehetővé teszi az alkalmazás hozzáférjen a kamera és mikrofon az eszközön.

3. A kamera kutya vagy cat mutasson. Az alkalmazás előzetes alatt jelennek meg, hogy a kép kutya vagy cat tartalmaz-e a pontszám.

    > [!TIP]
    > Ha nem rendelkezik egy kutya vagy cat lesz szüksége, kutya vagy cat fénykép is használhatja.

## <a name="use-your-own-model"></a>A saját modellt használnak

A saját modell használatára, tegye a következőket:

> [!IMPORTANT]
> A jelen szakaszban szereplő lépéseket nevezze át az aktuális modell (cat-vagy-dog.cs), és az osztály és a metódus nevének, az új modell refactor. Ez a példa modell ütközések elkerüléséhez.

1. Az egyéni stratégiai szolgáltatással a modell betanításához. A modell betanításához kapcsolatos információkért lásd: a [hogyan hozhat létre egy osztályozó](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier).

2. Exportálja a betanított modell egy ONNX modellre. Egy modell exportálásával kapcsolatos információkért lásd: a [a modell használatra exportálása a mobileszközökkel](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentum.

3. A Megoldáskezelőben kattintson a jobb gombbal a __cat vagy dog.cs__ nevezze át és __cat vagy dog.txt__. Átnevezése megakadályozza, hogy az új modell összeütközések nevét.

    > [!TIP]
    > Az új modell az osztályneveket a különböző neveket is használhatja, de újból felhasználja a meglévő nevekkel egyszerűbb.

4. A Megoldáskezelőben kattintson a jobb gombbal a __VisionApp__ bejegyzést, majd válassza ki __Hozzáadás__ > __meglévő cikk...__ .

5. A modell osztályt létrehozni, válassza ki az ONNX fájlt importálja, majd válassza ki a __Hozzáadás__ gombra. Egy új osztályt a ONNX fájllal azonos nevű (azonban egy `.cs` bővítmény) kerül a Megoldáskezelőben.

6. Nyissa meg a létrehozott .cs fájlt, és keresse meg a következő elemek nevei:

    > [!IMPORTANT]
    > A példa `cat-or-dog.txt` ismeri fel a osztályok és funkciók útmutatójaként fájlt.

    * Az osztály, amely meghatározza a bemeneti modell. Lehet, hogy a létrehozott név hasonló `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Nevezze át ezt az osztályt az __ModelInput__.
    * Az osztály, amely meghatározza a modell kimeneti. Lehet, hogy a létrehozott név hasonló `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Nevezze át ezt az osztályt az __ModelOutput__.
    * Az osztály, amely meghatározza a modell. Lehet, hogy a létrehozott név hasonló `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Nevezze át ezt az osztályt az __modell__.
    * A módszer, amely a modell létrehozása. Lehet, hogy a létrehozott név hasonló `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Nevezze át ezt a metódust __CreateModel__.

7. A Megoldáskezelőben, helyezze át a `.onnx` fájlt a __eszközök__ mappát. 

8. Az alkalmazáscsomag felvenni a ONNX fájlt, jelölje ki a `.onnx` fájlt, és __Build művelet__ való __tartalom__ tulajdonságai.

9. Nyissa meg a __MainPage.xaml.cs__ fájlt. A következő sorban, majd módosítsa a fájl nevét az új található `.onnx` fájlt:

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    Ez a változás az új modell futásidőben betölti.

10. Hozza létre, és futtassa az alkalmazást. Most használja az új modell pontozása képek.

## <a name="next-steps"></a>További lépések

Annak megállapításához, egyéb módjai exportálása és egyéni stratégiai modellt használnak, lásd a következő dokumentumokat:

* [A modell exportálása](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Exportált Tensorflow modellt használhatja az Android alkalmazás](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Használja az exportált CoreML modell Swift iOS-alkalmazásokban](https://go.microsoft.com/fwlink/?linkid=857726)
* [Használjon exportált CoreML modell a xamarin iOS-alkalmazás](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

A Windows ml ONNX modellek segítségével további információkért lásd: a [modell integrálja az alkalmazás a Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model) dokumentum.
