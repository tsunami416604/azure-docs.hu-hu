---
title: Exportálja az egyéni stratégiai szolgáltatásmodell - egyéni stratégiai szolgáltatás – Azure kognitív mobilszolgáltatások |} Microsoft Docs
description: Megtudhatja, hogyan használható a mobil-alkalmazások létrehozása a modell exportálása.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: ce8f42d6239867dd217cddfc61a27d7835dc9c9b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349895"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>A modell használatra exportálása a mobileszköz

Egyéni stratégiai szolgáltatás lehetővé teszi, hogy a besorolások exportálható offline állapotban való futtatása. Az exportált osztályozó beágyazása egy alkalmazásba, és futtassa helyileg a valós idejű besorolás egy eszközön. 

Egyéni stratégiai szolgáltatás a következő kivitel támogatja:

* __Tensorflow__ a __Android__.
* __CoreML__ a __iOS11__.
* __ONNX__ a __Windows ML__.
* A Windows vagy Linux __tároló__. A tároló egy Tensorflow tartalmazza a modell, és az egyéni stratégiai Service API-kód szolgáltatás. 

> [!IMPORTANT]
> Egyéni stratégiai szolgáltatás csak azokat a exportálja __tömörítésével__ tartományok. A modellek kompakt tartományok állítja elő vannak optimalizálva, a valós idejű besorolás mobileszközökön korlátozásait. Kompakt tartománnyal rendelkező beépített osztályozó betanítási adatok akkora szabványos tartomány némileg kevésbé pontos lehet.
>
> A besorolások javításával kapcsolatos információkért lásd: a [javítása a osztályozó](getting-started-improving-your-classifier.md) dokumentum.

## <a name="convert-to-a-compact-domain"></a>A kompakt tartomány átalakítása

> [!NOTE]
> Ebben a szakaszban a lépések csak akkor érvényesek, ha egy meglévő osztályozó, amelyek tömörítése a tartomány nincs beállítva.
 
A tartomány egy már meglévő osztályozó alakításához tegye a következőket:

1. Az a [egyéni stratégiai lap](https://customvision.ai), jelölje be a __Home__ ikon a projektek megtekintéséhez. Használhatja a [ https://customvision.ai/projects ](https://customvision.ai/projects) a projektek megtekintéséhez.

    ![Az otthoni ikon és projektek listáján képe](./media/export-your-model/projects-list.png)

2. Válassza ki a projektet, majd válassza ki a __fogaskerék__ ikonra a képernyő jobb felső sarkában a lap.

    ![A fogaskerék ikonja](./media/export-your-model/gear-icon.png)

3. Az a __tartományok__ szakaszban jelölje be a __tömörítésével__ tartomány. Válassza ki __módosítások mentése__ menti a módosításokat.

    ![Tartományok kijelölés képe](./media/export-your-model/domains.png)

4. A lap tetején jelölje ki __vonat__ működik az új tartományt használó számára.

## <a name="export-your-model"></a>A modell exportálása

A modell után átképezési exportálásához használja a következő lépéseket:

1. Lépjen a **teljesítmény** lapot, és válasszon __exportálása__. 

    ![Az Exportálás ikonjára képe](./media/export-your-model/export.png)

    > [!TIP]
    > Ha a __exportálása__ bejegyzés nem érhető el, majd a kijelölt iterációs nem kompakt tartományt használ. Használja a __ismétlési__ ezen a lapon adhat egy iteráció kompakt tartományt használó, majd válassza ki és szakasza __exportálása__.

2. Az exportálási formátumát, majd válassza ki és __exportálása__ a modell letöltése.

## <a name="next-steps"></a>További lépések

Az exportált modellje integrálni egy alkalmazásba. Több mintaalkalmazások érhetők el:

* A minta [az exportált CoreML modell segítségével az iOS-alkalmazás](https://go.microsoft.com/fwlink/?linkid=857726) a Swift valós idejű kép besorolás
* Az iOS mintaalkalmazás [az exportált CoreML modell használatával xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) a valós idejű kép besorolás 
* A minta [az exportált Tensorflow modellt használja egy Android-alkalmazás](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) a valós idejű kép besorolás 
* [A Windows a Tensorflow modell segítségével](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/export-model-python)
* A minta [modellel az exportált ONNX Windows Machine Learning segítségével](https://azure.microsoft.com/en-us/resources/samples/cognitive-services-onnx-customvision-sample/)
