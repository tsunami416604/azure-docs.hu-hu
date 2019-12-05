---
title: Modell üzembe helyezése Custom Speech-Speech Service-hez
titleSuffix: Azure Cognitive Services
description: Ebből a dokumentumból megtudhatja, hogyan hozhat létre és helyezhet üzembe egy végpontot a Custom Speech portál használatával.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806113"
---
# <a name="deploy-a-custom-model"></a>Egyéni modell üzembe helyezése

Miután feltöltötte és megvizsgálta az adatait, értékelte a pontosságot, és betanított egy egyéni modellt, üzembe helyezhet egy egyéni végpontot az alkalmazásaival, eszközeivel és termékeivel való használatra. Ebből a dokumentumból megtudhatja, hogyan hozhat létre és helyezhet üzembe egy végpontot a [Custom Speech portál](https://speech.microsoft.com/customspeech)használatával.

## <a name="create-a-custom-endpoint"></a>Egyéni végpont létrehozása

Új egyéni végpont létrehozásához jelentkezzen be a [Custom Speech portálra](https://speech.microsoft.com/customspeech) , és válassza a **telepítés** lehetőséget az oldal tetején található Custom Speech menüből. Ha először futtatja, láthatja, hogy nincsenek a táblázatban felsorolt végpontok. Miután létrehozott egy végpontot, ezen a lapon követheti nyomon az egyes telepített végpontokat.

Ezután válassza a **végpont hozzáadása** lehetőséget, és adja meg az egyéni végpont **nevét** és **leírását** . Ezután válassza ki azt az egyéni modellt, amelyet hozzá szeretne rendelni ehhez a végponthoz. Ezen a lapon engedélyezheti a naplózást is. A naplózás lehetővé teszi a végponti forgalom figyelését. Ha le van tiltva, a rendszer nem tárolja a forgalmat.

![Modell üzembe helyezése](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Ne felejtse el elfogadni a használati feltételeket és a díjszabási adatokat.

Ezután válassza a **Létrehozás**lehetőséget. Ez a művelet visszaadja az **üzembe helyezési** lapot. A tábla mostantól egy olyan bejegyzést tartalmaz, amely megfelel az egyéni végpontnak. A végpont állapota megjeleníti a jelenlegi állapotát. Akár 30 percet is igénybe vehet, ha új végpontot hoz létre az egyéni modellek használatával. Ha a központi telepítés állapota **Befejezettre**változik, a végpont készen áll a használatra.

A végpont üzembe helyezése után a végpont neve hivatkozásként jelenik meg. Kattintson a hivatkozásra a végpontra jellemző információk megjelenítéséhez, például a végponti kulcs, a végpont URL-címe és a mintakód számára.

## <a name="view-logging-data"></a>Naplózási adatgyűjtés megtekintése

A naplózási adatok letölthetők a **Endpoint > részletei**területen.

## <a name="next-steps"></a>Következő lépések

* Egyéni végpont használata a [SPEECH SDK](speech-sdk.md) -val

## <a name="additional-resources"></a>További források

* [Az adatfeldolgozás előkészítése és tesztelése](how-to-custom-speech-test-data.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
* [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
