---
title: Modell exportálása mobile – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Ismerje meg a mobilalkalmazások létrehozásához használt modell exportálása.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 7bf8217f5076c0a95d4db6c1c7cbea7bc93b91f3
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550540"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>A mobileszközök használatra modell exportálása

A Custom Vision Service lehetővé teszi, hogy deklarációkkal exportálható offline futtatásához. Az exportált osztályozó beágyazása egy alkalmazásba, és helyileg futtatja az alkalmazást az eszközön a valós idejű besorolási.

A Custom Vision Service a következő export támogatja:

* __Tensorflow__ a __Android__.
* __CoreML__ a __ios11-et__.
* __ONNX__ a __Windows gépi Tanulási__.
* Egy Windows vagy Linux rendszerű __tároló__. A tároló tartalmazza, a Tensorflow modellezheti, és kódot, ha a Custom Vision Service API-szolgáltatás. 

> [!IMPORTANT]
> A Custom Vision Service csak azokat a __compact__ tartományok. A modellek kompakt tartományok által generált a korlátozások a mobileszközökön a valós idejű besorolás vannak optimalizálva. Kompakt tartomány beépített deklarációkkal némileg kevésbé pontos, mint a standard szintű tartomány a betanítási adatok akkora lehet.
>
> A besorolások javításával kapcsolatos további információkért lásd: a [az osztályozót](getting-started-improving-your-classifier.md) dokumentumot.

## <a name="convert-to-a-compact-domain"></a>Kompakt tartomány átalakítása

> [!NOTE]
> A jelen szakaszban ismertetett lépések csak alkalmazni, ha rendelkezik egy meglévő osztályozó, amelyek kompakt tartomány nincs beállítva.

A tartomány egy már létező osztályozó konvertálásához használja a következő lépéseket:

1. Az a [Custom vision lapon](https://customvision.ai), jelölje be a __kezdőlap__ ikonra a projektek listájának megtekintéséhez. Is használhatja a [ https://customvision.ai/projects ](https://customvision.ai/projects) a projektek megtekintéséhez.

    ![A kezdőlap ikonra, és projektek lista képe](./media/export-your-model/projects-list.png)

2. Válassza ki a projektet, és válassza a __fogaskerék__ , az oldal jobb felső sarokban lévő ikonra.

    ![A fogaskerék ikonja](./media/export-your-model/gear-icon.png)

3. Az a __tartományok__ szakaszban jelölje be egy __compact__ tartományhoz. Válassza ki __módosítások mentése__ menti a módosításokat.

    ![Tartományok kiválasztása képe](./media/export-your-model/domains.png)

4. A lap tetején válassza __Train__ , az új tartományt használó újratanítása.

## <a name="export-your-model"></a>Modell exportálása

A modell exportálása után átképezési, használja az alábbi lépéseket:

1. Nyissa meg a **teljesítmény** lapot, és válasszon __exportálása__. 

    ![Az Exportálás ikonra képe](./media/export-your-model/export.png)

    > [!TIP]
    > Ha a __exportálása__ bejegyzés nem érhető el, majd a kiválasztott iteráció nem használ kompakt tartományhoz. Használja a __ismétléseinek__ ezen az oldalon egy iterációját kompakt tartományt használó, majd válassza ki és szakaszában __exportálása__.

2. Válasszon exportálási formátumot, és válassza __exportálása__ letöltéséhez a modellt.

## <a name="next-steps"></a>További lépések

Az exportált modell integrálása egy alkalmazásba elemzésével a következő cikkek vagy minták egyikét:

* [A Tensorflow-modell használata Pythonnal](export-model-python.md)
* [Az ONNX-modell használatához Windows gépi tanulással](custom-vision-onnx-windows-ml.md)
* Tekintse meg a mintát a [CoreML modell az IOS-es alkalmazások](https://go.microsoft.com/fwlink/?linkid=857726) a valós idejű képbesorolás Swiftet.
* Tekintse meg a mintát a [Tensorflow modell az Android-alkalmazás](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) valós idejű képbesorolás az Android esetében.
* Tekintse meg a mintát a [CoreML modell használatát a Xamarinnal](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) a valós idejű képek besorolása a Xamarin iOS-alkalmazásokhoz.
