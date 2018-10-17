---
title: 'Oktatóanyag: Az ONNX-modell használata Windows ML-gel – Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Megismerheti, hogyan hozhat létre az Azure Cognitive Servicesből exportált ONNX modellt használó Windows UWP-alkalmazást.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 3a9e9bc92ce38c4bb8d6d83c8017fa223342e7d2
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365604"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Oktatóanyag: Custom Vision ONNX modell használata Windows ML-lel (előzetes verzió)

Megismerheti, hogyan használhatja a Custom Vision Service-ből exportált ONNX modellt Windows ML-gel (előzetes verzió).

A dokumentum bemutatja, hogyan lehet a Custom Vision Service-ből exportált ONNX-fájlt Windows ML-ben használni. Tartalmaz egy Windows UWP-alkalmazás példát is. A példához tartozik egy kutyákat és macskákat felismerni képes betanított modell. Megadja azt is, hogyan használhatja ehhez a példához a saját modelljét.

> [!div class="checklist"]
> * A példa alkalmazásról
> * A példa kódjának letöltése
> * A példa futtatása
> * Saját modell használata

## <a name="prerequisites"></a>Előfeltételek

* Windows 10-es eszköz és:

    * Kamera.

    * Visual Studio 2017 15.7-es vagy újabb verzió engedélyezett __Univerzális Windows-platform fejlesztési__ tevékenységprofillal.

    * Engedélyezett fejlesztői mód. További információkért lásd a [Fejlesztés engedélyezése az eszközön](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentumot.

* (Nem kötelező) Custom Vision szolgáltatásból exportált ONNX-fájl. További információkért lásd a [Modell exportálása mobil készülékkel történő használatra](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentumot.

    > [!NOTE]
    > Ha a saját modellt kívánja használni, kövesse a [Saját modell használata](#use-your-own-model) részben megadott lépéseket.

## <a name="about-the-example-app"></a>A példa alkalmazásról

Az alkalmazás általános Windows UWP alkalmazás. A Windows 10-es eszközön a kamera használatával látja el a modellt képekkel. A modell által visszaadott címkék és pontszámok a videó villámnézet alatt láthatók.

* Ahogy az adatok érkeznek a kamerán keresztül, a képkockák kivonását a [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) végzi. A modell megkapja a képkockákat pontozásra.

* A modell visszaadja azokat a címkéket, amire tanították és egy lebegőpontos értéket, ami megadja, menyire biztos, hogy a kép tartalmazza az adott elemet.

### <a name="the-ui"></a>A felhasználói felület

A mintaalkalmazáshoz a felhasználói felület a __CaptureElement__ és __TextBlock__ vezérlők használatával készül. A CaptureElement megjeleníti a kameráról érkező videó villámnézetét a TextBlock pedig megjeleníti a modell által visszaadott eredményeket. 

### <a name="the-model"></a>A modell

A mintához tartozó modell (`cat-or-dog.onnx`) készítése és betanítása a Cognitive Services Custom Vision service használatával történt. A betanított modellt utána exportáltuk ONNX-modellként. A szolgáltatás használatával kapcsolatban lásd: az [Osztályozó létrehozása](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) és a [Modell exportálása mobil készülékkel történő használatra](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentumokat.

> [!IMPORTANT]
> A példához tartozó modellt kutya és macska képek kis halmazán tanítottuk be. Valószínűleg nem túl jó a kutyák és macskák felismerésében.

### <a name="the-model-class-file"></a>A modell osztály fájlja

Amikor az ONNX-fájlt hozzáadja a Windows UWP-alkalmazáshoz, egy .cs fájlt hoz létre. Ennek a fájlnak ugyanaz a neve, mint a `.onnx` fájlnak (`cat-or-dog` ebben a példában), és azokat az osztályokat tartalmazza, amelyekkel C# nyelven dolgozunk a modellel. Azonban a létrehozott osztályban az entitásoknak olyan nevük lehet, mint `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Ezeket nyugodtan át lehet nevezni (jobb egérgomb kattintás, átnevezés) rövidebb névre.

> [!NOTE]
> A példakód újrabontotta a létrehozott osztály és metódus neveket a következőkre:
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Kamera-hozzáférés

A __Képességek__ lapon a `Package.appxmanifest` fájlra engedélyezett a webkamera és mikrofon elérése.

> [!NOTE]
> Annak ellenére, hogy a példa nem használ hangot, engedélyeznem kellett a mikrofon elérését, csak utána tudtam az eszközön elérni a kamerát.

Az alkalmazás megpróbál hozzáférni a készülék hátoldali kamerájához, ha van. A [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) osztállyal kezdi meg a videó fogadását a kameráról. A [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) a videó képkockákat rögzíti és elküldi a modellnek.

## <a name="get-the-example-code"></a>A példa kódjának letöltése

A mintaalkalmazás itt érhető el: [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>A példa futtatása

1. A `F5` kulcs használatával indítsa el az alkalmazást a Visual Studióból. Lehet, hogy a rendszer kéri a fejlesztői mód engedélyezését. További információkért lásd a [Fejlesztés engedélyezése az eszközön](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentumot.

2. Amikor a rendszer kéri, engedélyezze az alkalmazás számára a hozzáférést az eszközön a kamerához és a mikrofonhoz.

3. Irányítsa a kamerát kutyára vagy macskára. A pontszám, hogy a képen van-e kutya vagy macska, az alkalmazásban a villámnézet alatt jelenik meg.

    > [!TIP]
    > Ha nincs a közelben macska vagy kutya, használhat macskáról, kutyáról készült fényképet is.

## <a name="use-your-own-model"></a>Saját modell használata

Saját modell esetén a következőképpen járjon el:

> [!IMPORTANT]
> Ebben a részben megadott lépésekkel átnevezzük a modellt (cat-or-dog.cs), és újrabontjuk az új modell osztály és metódus neveit. Ennek célja, hogy a példa modellel elkerüljük a névütközéseket.

1. Tanítsa be a modellt a Custom Vision service használatával. A modell betanításáról bővebben lásd: [Osztályozó létrehozása](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier).

2. Exportálja a betanított modellt ONNX-modellként. Azzal kapcsolatban, hogyan lehet a modellt exportálni, lásd a [Modell exportálása mobil készülékkel történő használatra](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentumot.

3. A Megoldáskezelőben kattintson jobb gombbal a __cat-or-dog.cs__ névre és nevezze át __cat-or-dog.txt__ fájlra. Az átnevezés megakadályozza a névütközéseket az új modellel.

    > [!TIP]
    > Használhatna más osztály neveket is az új modellben, de a létező neveket használni egyszerűbb.

4. A Megoldáskezelőben kattintson jobb gombbal a __VisionApp__ elemre, majd válassza a __Létező elem__ > __hozzáadása...__  pontot.

5. A modellhez az osztály generálásához válassza az importálandó ONNX fájlt, majd válassza a __Hozzáadás__ gombot. Az ONNX-fájllal azonos nevű új osztály (de a bővítménye `.cs`) jön létre a Megoldáskezelőben.

6. Nyissa meg a generált .cs fájlt, és keresse meg a következő elemek neveit:

    > [!IMPORTANT]
    > A `cat-or-dog.txt` példa fájl segítségével keresse meg az osztályokat és függvényeket.

    * A modell bemenetét definiáló osztály. A generált név ehhez hasonló lehet: `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Nevezze át az osztályt __ModelInput__ névre.
    * A modell kimenetét definiáló osztály. A generált név ehhez hasonló lehet: `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Nevezze át az osztályt __ModelOutput__ névre.
    * A modellt definiáló osztály. A generált név ehhez hasonló lehet: `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Nevezze át ezt az osztályt __Modell__ névre.
    * A modellt létrehozó metódus. A generált név ehhez hasonló lehet: `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Nevezze át ezt a metódust __CreateModel__ névre.

7. A Megoldáskezelőben helyezze át a `.onnx` fájlt az __Eszközök__ mappába. 

8. Az ONNX-fájlnak az alkalmazáscsomagba történő belevételéhez, válassza ki a `.onnx` fájlt, és a tulajdonságoknál állítsa be a __Fordítási művelet__ esetében a __Tartalom__ lehetőséget.

9. Nyissa meg a __MainPage.xaml.cs__ fájlt. Keresse meg a következő sort és írja át a fájl nevét az új `.onnx` fájlra:

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    A változás az új modellt tölti be futásidőben.

10. Hozza létre és futtassa az alkalmazást. Most már az új modellt használja a képek pontozására.

## <a name="next-steps"></a>További lépések

Egyéb exportálási lehetőségekről és a Custom Vision modell használatáról bővebb információt a következő dokumentumokban talál:

* [A modell exportálása](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Exportált Tensorflow-modell használata Android-alkalmazásban](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Exportált CoreML modell használata Swift iOS-alkalmazásban](https://go.microsoft.com/fwlink/?linkid=857726)
* [Exportált CoreML modell használata iOS alkalmazásban Xamarinnal](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Az ONNX-modellek Windows ML-lel történő használatáról bővebb információk találhatók a [Modell integrálása az alkalmazásba a Windows ML használatával](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model) dokumentumban.
