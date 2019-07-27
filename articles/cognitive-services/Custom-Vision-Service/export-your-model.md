---
title: Modell exportálása a Mobile-Custom Vision Serviceba
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan exportálhatja a modellt a Mobile-alkalmazások létrehozásához való használatra.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 554a392a7f815a6e646927f137b1e6c2856099bd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561081"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>A modell exportálása mobileszközökön való használatra

Custom Vision Service lehetővé teszi az osztályozók exportálását offline állapotba. Az exportált besorolást beillesztheti egy alkalmazásba, és helyileg futtathatja egy eszközön a valós idejű besoroláshoz.

A Custom Vision Service a következő exportálásokat támogatja:

* __Tensorflow__ __Android__rendszerhez.
* A __IOS11__ __CoreML__ .
* __ONNX__ a __Windows ml__-hez.
* Egy Windows-vagy Linux- __tároló__. A tároló tartalmaz egy Tensorflow modellt és egy szolgáltatási kódot a Custom Vision Service API használatához. 

> [!IMPORTANT]
> Custom Vision Service csak a __kompakt__ tartományokat exportálja. A kompakt tartományok által generált modellek a mobileszközök valós idejű besorolásának korlátaira vannak optimalizálva. A kompakt tartománnyal létrehozott osztályozók valamivel kevésbé pontosak, mint egy standard tartomány, amely azonos mennyiségű betanítási adattal rendelkezik.
>
> Az osztályozók fejlesztésével kapcsolatos információkért tekintse meg az osztályozó dokumentum [továbbfejlesztése](getting-started-improving-your-classifier.md) című témakört.

## <a name="convert-to-a-compact-domain"></a>Átalakítás kompakt tartományba

> [!NOTE]
> Az ebben a szakaszban ismertetett lépések csak akkor érvényesek, ha olyan meglévő osztályozó van, amely nem kompakt tartományra van beállítva.

Meglévő osztályozó tartományának átalakításához kövesse az alábbi lépéseket:

1. Az [Egyéni jövőkép lapon](https://customvision.ai)válassza a __Kezdőlap__ ikont a projektek listájának megtekintéséhez. A [https://customvision.ai/projects](https://customvision.ai/projects) segítségével is megtekintheti projektjeit.

    ![A Kezdőlap ikon és a projektek lista képe](./media/export-your-model/projects-list.png)

2. Válasszon ki egy projektet, majd kattintson a lap jobb felső sarkában található __fogaskerék__ ikonra.

    ![A fogaskerék ikon képe](./media/export-your-model/gear-icon.png)

3. A __tartományok__ szakaszban válasszon ki egy __kompakt__ tartományt. A módosítások mentéséhez kattintson a __módosítások mentése__ gombra.

    ![Tartományok kiválasztásának képe](./media/export-your-model/domains.png)

4. Az oldal tetején válassza a betanítás az  új tartomány használatával lehetőséget.

## <a name="export-your-model"></a>A modell exportálása

A modell átképzés utáni exportálásához kövesse az alábbi lépéseket:

1. Lépjen a **teljesítmény** lapra, és válassza az __Exportálás__lehetőséget. 

    ![Az Exportálás ikon képe](./media/export-your-model/export.png)

    > [!TIP]
    > Ha az __exportálási__ bejegyzés nem érhető el, akkor a kiválasztott iteráció nem használ kompakt tartományt. A lap __iterációk__ szakaszával olyan iterációt választhat ki, amely egy kompakt tartományt használ, majd válassza az __Exportálás__lehetőséget.

2. Válassza ki az exportálási formátumot, majd válassza az __Exportálás__ lehetőséget a modell letöltéséhez.

## <a name="next-steps"></a>További lépések

Integrálja az exportált modellt egy alkalmazásba az alábbi cikkek vagy minták egyikének feltárásával:

* [A Tensorflow-modell használata a Python használatával](export-model-python.md)
* [A ONNX-modell használata a Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Tekintse meg az [iOS](https://go.microsoft.com/fwlink/?linkid=857726) -alkalmazásban a CoreML modellhez készült mintát, amely valós idejű képbesorolást biztosít a Swift-mel.
* Tekintse meg az Android [-alkalmazásokban a Tensorflow modellhez](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) készült mintát az androidos valós idejű képbesoroláshoz.
* Tekintse meg a [CoreML modelt a Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) valós idejű képbesorolást biztosító modelljét egy Xamarin iOS-alkalmazásban.
