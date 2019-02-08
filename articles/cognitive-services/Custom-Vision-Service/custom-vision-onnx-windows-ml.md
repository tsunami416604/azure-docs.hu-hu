---
title: 'Oktatóanyag: Az ONNX-modell használata Windows gépi tanulás – Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Megismerheti, hogyan hozhat létre az Azure Cognitive Servicesből exportált ONNX modellt használó Windows UWP-alkalmazást.
services: cognitive-services
author: larryfr
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 10/16/2018
ms.author: larryfr
ms.openlocfilehash: 7f123187ea869931b0701fd9d68372deb0b272e1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879071"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Oktatóanyag: A Custom Vision az ONNX-modell használata Windows Machine Learning (előzetes verzió)

Megismerheti, hogyan használhatja a Custom Vision Service-ből exportált ONNX modellt Windows ML-gel (előzetes verzió).

A dokumentum bemutatja, hogyan lehet a Custom Vision Service-ből exportált ONNX-fájlt Windows ML-ben használni. Tartalmaz egy Windows UWP-alkalmazás példát is. A példához tartozik egy felismerésre képes betanított modell. Megadja azt is, hogyan használhatja ehhez a példához a saját modelljét.

> [!div class="checklist"]
> * A példa alkalmazásról
> * A példa kódjának letöltése
> * A példa futtatása
> * Saját modell használata

## <a name="prerequisites"></a>Előfeltételek

* Windows 10 17738-as vagy újabb build

* Windows SDK a 17738-as vagy újabb buildhez

* Visual Studio 2017 15.7-es vagy újabb verzió engedélyezett __Univerzális Windows-platform fejlesztési__ tevékenységprofillal.

* Engedélyezett fejlesztői mód. További információkért lásd a [Fejlesztés engedélyezése az eszközön](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentumot.

## <a name="about-the-example-app"></a>A példa alkalmazásról

Az alkalmazás általános Windows UWP alkalmazás. Lehetővé teszi, hogy kiválasszon egy képet a számítógépről, amelyet azután elküld a modellnek. A modell által visszaadott címkék és pontszámok a kép mellett jelennek meg.

## <a name="get-the-example-code"></a>A példa kódjának letöltése

A mintaalkalmazás itt érhető el: [https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/](https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/).

## <a name="run-the-example"></a>A példa futtatása

1. A `F5` kulcs használatával indítsa el az alkalmazást a Visual Studióból. Lehet, hogy a rendszer kéri a fejlesztői mód engedélyezését. További információkért lásd a [Fejlesztés engedélyezése az eszközön](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentumot.

2. Az alkalmazás indításakor a gombbal válasszon ki egy képet a pontozáshoz.

## <a name="use-your-own-model"></a>Saját modell használata

Saját modell esetén a következőképpen járjon el:

1. Besoroló [létrehozása és betanítása](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) a Custom Vision Service használatával. A modell exportálásához válasszon ki egy __kompakt__ tartományt, például az **Általános (kompakt)** tartományt. Egy meglévő besoroló exportálásához alakítsa kompakttá a tartományt a jobb felső részen lévő fogaskerék kiválasztásával. A __Beállítások__ területen válasszon egy kompakt modellt, mentse, majd tanítsa be a projektet.  

1. [A modell exportálásához](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) lépjen a Teljesítmény lapra. Válasszon ki egy kompakt tartománnyal betanított iterációt. Ekkor megjelenik egy "Exportálás" gomb. Válassza az *Exportálás*, *ONNX*, *ONNX1.2*, majd az *Exportálás* elemet. Amikor a fájl elkészült, kattintson a *Letöltés* gombra.

1. Helyezze az ONNX-fájlt a projekt __Assets__ (Objektumok) mappájába. 

1. A Megoldáskezelőben kattintson a jobb gombbal az Assets mappára, és válassza az __Add Existing Item__ (Meglévő elem hozzáadása) lehetőséget. Válassza ki az ONNX-fájlt.

1. A Megoldáskezelőben válassza ki az ONNX-fájlt az Assets mappából. Módosítsa a fájl következő tulajdonságait: 
    
    * __Build Action (Felépítési művelet)__ -> __Content (Tartalom)__
    * __Copy to Output Directory (Másolás a kimeneti könyvtárba)__ -> __Copy if newer (Másolás, ha újabb)__

1. Módosítsa az `_onnxFileNames` változót az ONNX-fájl nevére. A `ClassLabel` értékét is módosítsa a modellben szereplő címkék számára.

1. Hozza létre és futtassa a modellt.

1. Kattintson a gombra az értékelni kívánt kép kiválasztásához.

## <a name="next-steps"></a>További lépések

Egyéb exportálási lehetőségekről és a Custom Vision modell használatáról bővebb információt a következő dokumentumokban talál:

* [A modell exportálása](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Exportált Tensorflow-modell használata Android-alkalmazásban](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Exportált CoreML modell használata Swift iOS-alkalmazásban](https://go.microsoft.com/fwlink/?linkid=857726)
* [Exportált CoreML modell használata iOS alkalmazásban Xamarinnal](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Az ONNX-modellek Windows ML-lel történő használatáról bővebb információk találhatók a [Modell integrálása az alkalmazásba a Windows ML használatával](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model) dokumentumban.
