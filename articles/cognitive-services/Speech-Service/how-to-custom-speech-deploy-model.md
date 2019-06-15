---
title: Modell üzembe helyezése a Custom Speech - beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Ebből a dokumentumból megismerheti, hogyan hozhat létre és telepíthet egy végpontot a Custom Speech-portál használatával fogja.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: fc51c1d9d47340da85d42f7c398c8ee21c601beb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025870"
---
# <a name="deploy-a-custom-model"></a>Egyéni modell üzembe helyezése

Feltöltött és adatok megvizsgálni, kiértékelése pontossága, és egy egyéni modell betanított, után telepíthet egy egyéni végpont használata az alkalmazásokat, eszközöket és termékeket. Ebből a dokumentumból megismerheti, hogyan hozhat létre és telepíthet egy végpontot a Custom Speech-portál használatával fogja.

## <a name="create-a-custom-endpoint"></a>Egyéni végpont létrehozása

Új egyéni végpont létrehozásához válassza **üzembe helyezési** az oldal tetején a Custom Speech menüből. Ha ez az első alkalommal történő futtatásakor, láthatja, hogy nincsenek-e a táblázatban felsorolt végpontok. Miután létrehozott egy végpontot, minden egyes telepített végpontjától nyomon kell ezen a lapon.

Majd **végpont hozzáadása** , és adja meg egy **neve** és **leírás** a egyéni végponthoz. Ezután válassza ki az egyéni modell, amely ezt a végpontot társítani szeretné. Ezen az oldalon is engedélyezheti a naplózást. Naplózás végpont forgalom figyelését teszi lehetővé. Ha le van tiltva, a forgalom nem tárolja.

![A modell üzembe helyezése](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Ne felejtse el fogadja el a használati feltételeit és díjszabásáról.

Majd **létrehozás**. Ez a művelet visszaadja, hogy a **üzembe helyezési** lapot. A táblázat most már tartalmaz egy bejegyzést, amely megfelel az egyéni végponthoz. A végpont állapotát jeleníti meg a jelenlegi állapotában. Egyéni modellek használatával új végpont vezérlőként akár 30 percet is igénybe vehet. Mikor változik a központi telepítés állapotát **Complete**, a végpont készen áll a használatra.

A végpont telepítése után a végpont neve hivatkozásként jelenik meg. Kattintson a hivatkozásra, adott információk megjelenítéséhez a végpontra, például a végponti kulcs, a végpont URL-címe és a mintakódot.

## <a name="view-logging-data"></a>Naplózási adatok megtekintése

Naplózási adatok érhető el a letöltési **végpont > részletek**.

## <a name="next-steps"></a>További lépések

* Az egyéni végpontjára használja a [beszéd SDK](speech-sdk.md)

## <a name="additional-resources"></a>További források

* [Készítse elő és az adatok tesztelése](how-to-custom-speech-test-data.md)
* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
* [Az adatok](how-to-custom-speech-evaluate-data.md)
* [A modell tanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
