---
title: Modell üzembe helyezése egyéni beszédfelismeréshez – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ebben a dokumentumban megtudhatja, hogyan hozhat létre és helyezhet üzembe végpontot az egyéni beszédportálon.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806113"
---
# <a name="deploy-a-custom-model"></a>Egyéni modell üzembe helyezése

Miután feltöltötte és megvizsgálta az adatokat, kiértékelte a pontosságot, és betanított egy egyéni modellt, üzembe helyezhet egy egyéni végpontot az alkalmazásokkal, eszközökkel és termékekkel való használatra. Ebből a dokumentumból megtudhatja, hogyan hozhat létre és helyezhet üzembe végpontot az [egyéni beszédportálon.](https://speech.microsoft.com/customspeech)

## <a name="create-a-custom-endpoint"></a>Egyéni végpont létrehozása

Új egyéni végpont létrehozásához jelentkezzen be az [egyéni beszédportálra,](https://speech.microsoft.com/customspeech) és válassza a Lap tetején található Egyéni beszédfelismerés menü **Telepítés parancsát.** Ha ez az első futtatása, észre fogja venni, hogy a táblázatban nincsenek végpontok. Miután létrehozott egy végpontot, ezen a lapon fogja nyomon követni az egyes üzembe helyezett végpontok.

Ezután válassza **a Végpont hozzáadása** lehetőséget, és adja meg az egyéni végpont **nevét** és **leírását.** Ezután válassza ki a végponthoz társítani kívánt egyéni modellt. Ezen az oldalon engedélyezheti a naplózást is. A naplózás lehetővé teszi a végpontforgalom figyelését. Ha le van tiltva, a forgalom nem lesz tárolva.

![Modell üzembe helyezése](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Ne felejtsd el elfogadni a használati feltételeket és az árképzési részleteket.

Ezután válassza a **Létrehozás gombot.** Ez a művelet visszaadja a **Központi telepítés** lapot. A tábla most már tartalmaz egy bejegyzést, amely megfelel az egyéni végpontnak. A végpont állapota az aktuális állapotát mutatja. Az egyéni modellek használatával akár 30 percet is igénybe vehet egy új végpont létrehozása. Amikor a központi telepítés állapota **kész**állapotra változik, a végpont készen áll a használatra.

A végpont üzembe helyezése után a végpont neve hivatkozásként jelenik meg. Kattintson a hivatkozásra a végpontra jellemző információk megjelenítéséhez, például a végpontkulcs, a végpont URL-címe és a mintakód.

## <a name="view-logging-data"></a>Naplózási adatok megtekintése

Az adatok naplózása az **Endpoint > Details**( Végpont> részletek ) alatt tölthető le.

## <a name="next-steps"></a>További lépések

* Az egyéni végpont használata a [beszédfelismerési SDK-val](speech-sdk.md)

## <a name="additional-resources"></a>További források

* [Az adatok előkészítése és tesztelése](how-to-custom-speech-test-data.md)
* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
* [Az adatok kiértékelése](how-to-custom-speech-evaluate-data.md)
* [A modell betanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
