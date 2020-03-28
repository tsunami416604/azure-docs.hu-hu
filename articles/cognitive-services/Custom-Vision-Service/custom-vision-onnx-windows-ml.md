---
title: 'Oktatóanyag: Az ONNX-modell használata Windows ML-gel – Custom Vision Service'
titleSuffix: Azure Cognitive Services
description: Megismerheti, hogyan hozhat létre az Azure Cognitive Servicesből exportált ONNX modellt használó Windows UWP-alkalmazást.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 3b56cd12aec21cffd98a0143f5cf3f083ec55e94
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74960553"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Oktatóanyag: Custom Vision ONNX modell használata Windows ML-lel (előzetes verzió)

Megismerheti, hogyan használhatja a Custom Vision Service-ből exportált ONNX modellt Windows ML-gel (előzetes verzió).

A dokumentum bemutatja, hogyan lehet a Custom Vision Service-ből exportált ONNX-fájlt Windows ML-ben használni. Tartalmaz egy Windows UWP-alkalmazás példát is. A példához tartozik egy felismerésre képes betanított modell. Megadja azt is, hogyan használhatja ehhez a példához a saját modelljét.

> [!div class="checklist"]
> * A példa alkalmazásról
> * A példa kódjának letöltése
> * A példa futtatása
> * Saját modell használata

## <a name="prerequisites"></a>Előfeltételek

* Windows 10 1809-es vagy újabb verzió

* 17763-as vagy újabb buildre szánt Windows SDK

* Visual Studio 2017 15.7-es vagy újabb verzió engedélyezett __Univerzális Windows-platform fejlesztési__ tevékenységprofillal.

* Engedélyezett fejlesztői mód. További információkért lásd a [Fejlesztés engedélyezése az eszközön](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentumot.

## <a name="about-the-example-app"></a>A példa alkalmazásról

Az alkalmazás általános Windows UWP alkalmazás. Lehetővé teszi, hogy kiválasszon egy képet a számítógépről, amelyet azután elküld a modellnek. A modell által visszaadott címkék és pontszámok a kép mellett jelennek meg.

## <a name="get-the-example-code"></a>A példa kódjának letöltése

A példaalkalmazás a [https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample)következő helyen érhető el: .

## <a name="run-the-example"></a>A példa futtatása

1. A `F5` kulcs használatával indítsa el az alkalmazást a Visual Studióból. Lehet, hogy a rendszer kéri a fejlesztői mód engedélyezését. További információkért lásd a [Fejlesztés engedélyezése az eszközön](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentumot.

1. Az alkalmazás indításakor a gombbal válasszon ki egy képet a pontozáshoz.

## <a name="use-your-own-model"></a>Saját modell használata

Saját modell esetén a következőképpen járjon el:

1. Besoroló [létrehozása és betanítása](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) a Custom Vision Service használatával. A modell exportálásához válasszon ki egy __kompakt__ tartományt, például az **Általános (kompakt)** tartományt. Egy meglévő besoroló exportálásához alakítsa kompakttá a tartományt a jobb felső részen lévő fogaskerék kiválasztásával. A __Beállítások__ területen válasszon egy kompakt modellt, mentse, majd tanítsa be a projektet.  

1. [Exportálja a modellt](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) a Teljesítmény lapra. Válasszon egy kompakt tartománnyal betanított ismétlést, és megjelenik egy "Exportálás" gomb. Válassza *az Exportálás*, *ONNX*, majd *az Exportálás*lehetőséget. Amikor a fájl elkészült, kattintson a *Letöltés* gombra.

1. Helyezze az ONNX-fájlt a projekt __Assets__ (Objektumok) mappájába. 

1. A Megoldáskezelőben kattintson a jobb gombbal az Assets mappára, és válassza az __Add Existing Item__ (Meglévő elem hozzáadása) lehetőséget. Válassza ki az ONNX-fájlt.

1. A Megoldáskezelőben válassza ki az ONNX-fájlt az Assets mappából. Módosítsa a fájl következő tulajdonságait:

    * __Művelettartalom__ -> __Content__ létrehozása
    * __Másolás a kimeneti könyvtárba__ -> __másolásra, ha újabb__

1. Módosítsa az `_onnxFileNames` változót az ONNX-fájl nevére. A `ClassLabel` értékét is módosítsa a modellben szereplő címkék számára.

1. Hozza létre és futtassa a modellt.

1. Kattintson a gombra az értékelni kívánt kép kiválasztásához.

## <a name="next-steps"></a>További lépések

Egyéb exportálási lehetőségekről és a Custom Vision modell használatáról bővebb információt a következő dokumentumokban talál:

* [A modell exportálása](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Exportált Tensorflow-modell használata Android-alkalmazásban](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Exportált CoreML modell használata Swift iOS-alkalmazásban](https://go.microsoft.com/fwlink/?linkid=857726)
* [Exportált CoreML modell használata iOS alkalmazásban Xamarinnal](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Az ONNX-modellek Windows ML-lel történő használatáról bővebb információk találhatók a [Modell integrálása az alkalmazásba a Windows ML használatával](/windows/ai/windows-ml/integrate-model) dokumentumban.
