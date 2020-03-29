---
title: Modell exportálása mobilra - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan exportálhatja a modellt a mobilalkalmazások létrehozásához, vagy helyileg futtatható a valós idejű besoroláshoz.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718947"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>A modell exportálása mobileszközökkel való használatra

A Custom Vision Service lehetővé teszi az osztályozók offline futtatását. Az exportált osztályozót beágyazhatja egy alkalmazásba, és helyileg futtathatja egy eszközön a valós idejű besoroláshoz.

## <a name="export-options"></a>Exportálási beállítások

A Custom Vision Service a következő exportálásokat támogatja:

* __Tensorflow__ __Androidra__.
* __CoreML__ __az iOS11 rendszerhez.__
* __ONNX__ __for Windows ML__.
* __[Vision AI fejlesztői készlet](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* __Docker-tároló__ Windows, Linux vagy ARM architektúrához. A tároló tartalmaz egy Tensorflow-modellt és szolgáltatáskódot a Custom Vision API használatához.

> [!IMPORTANT]
> A Custom Vision Service csak __kompakt__ tartományokat exportál. A kompakt tartományok által létrehozott modellek a mobileszközökön a valós idejű besorolás korlátaira vannak optimalizálva. A kompakt tartománysal készült osztályozók kissé kevésbé pontosak lehetnek, mint az azonos mennyiségű betanítási adatokkal rendelkező szabványos tartomány.
>
> Az osztályozók fejlesztéséről az [Osztályozó javítása](getting-started-improving-your-classifier.md) című dokumentumban talál további információt.

## <a name="convert-to-a-compact-domain"></a>Konvertálás kompakt tartománysá

> [!NOTE]
> Ebben a szakaszban a lépések csak akkor érvényesek, ha egy meglévő modell, amely nincs beállítva a kompakt tartomány.

Meglévő modell tartományának konvertálásához tegye a következő lépéseket:

1. Az [Egyéni látás webhelyén](https://customvision.ai)válassza a __Kezdőlap__ ikont a projektek listájának megtekintéséhez.

    ![Az otthoni ikon és a projektek listájának képe](./media/export-your-model/projects-list.png)

1. Jelöljön ki egy projektet, majd válassza a __Gear__ ikont az oldal jobb felső részén.

    ![A fogaskerék ikonja](./media/export-your-model/gear-icon.png)

1. A __Tartományok csoportban__ válasszon egyet a __kompakt__ tartományok közül. A módosítások mentéséhez válassza a __Módosítások mentése__ lehetőséget. 

    > [!NOTE]
    > Vision AI dev kit esetén a projektet az __Általános (Kompakt)__ tartománnyal kell létrehozni, és meg kell adnia a **Vision AI Fejlesztői készlet** beállítást az **Exportálási képességek** szakaszban.

    ![A tartományok kijelölésének képe](./media/export-your-model/domains.png)

1. A lap tetején válassza a __Betanítás__ az új tartomány használatával történő újratanítás lehetőséget.

## <a name="export-your-model"></a>A modell exportálása

Ha átképzés után szeretné exportálni a modellt, kövesse az alábbi lépéseket:

1. Nyissa meg a **Teljesítmény** lapot, és válassza __az Exportálás__lehetőséget. 

    ![Az exportálásikon képe](./media/export-your-model/export.png)

    > [!TIP]
    > Ha az __Exportálás__ bejegyzés nem érhető el, akkor a kijelölt iteráció nem használ tömörített tartományt. A lap __Iterációk__ szakaszában jelöljön ki egy kompakt tartományt használó ismétlést, majd válassza az __Exportálás__lehetőséget.

1. Válassza ki a kívánt exportálási formátumot, majd a modell letöltéséhez válassza az __Exportálás__ lehetőséget.

## <a name="next-steps"></a>További lépések

Integrálja az exportált modellt egy alkalmazásba az alábbi cikkek vagy minták egyikének feltárásával:

* [A Tensorflow-modell használata pythonnal](export-model-python.md)
* [Az ONNX-modell használata a Windows Machine Learning szolgáltatással](custom-vision-onnx-windows-ml.md)
* Tekintse meg a mintát [coreML modell egy iOS-alkalmazás](https://go.microsoft.com/fwlink/?linkid=857726) valós idejű képbesorolás swift.
* Tekintse meg a minta [Tensorflow modell egy Android-alkalmazás](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) valós idejű képbesorolás Androidon.
* Tekintse meg a [xamarin-modellrel rendelkező CoreML-modell](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) mintáját a Xamarin iOS-alkalmazások valós idejű képbesorolásához.
