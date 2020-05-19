---
title: Előre elkészített modellek a Language Understandinghoz
titleSuffix: Azure Cognitive Services
description: A LUIS előre elkészített modelleket tartalmaz, amelyekkel gyorsan hozzáadhat gyakori, társalgási felhasználói forgatókönyveket.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 653882db4b62d7731123faf7b177da44dbd74e3f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584994"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Előre összeépített modellek hozzáadása általános használati forgatókönyvekhez

A LUIS előre elkészített modelleket tartalmaz, amelyekkel gyorsan hozzáadhat gyakori, társalgási felhasználói forgatókönyveket. Ezzel a módszerrel gyorsan és egyszerűen hozzáadhat képességeket a társalgási ügyfélalkalmazás számára anélkül, hogy a modelleket meg kellene terveznie az adott képességekhez.

## <a name="add-a-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.

1. Válassza az **előre elkészített tartományok** lehetőséget a bal oldali eszköztárból.

1. Keresse meg azt a tartományt, amelyet hozzá szeretne adni az alkalmazáshoz, majd válassza a **tartomány hozzáadása** gombot.

    > [!div class="mx-imgBorder"]
    > ![Előre összeépített naptári tartomány hozzáadása](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Előre összeépített leképezés hozzáadása

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.

1. A **leképezések** lapon válassza az **előre felépített tartományhoz rendelés hozzáadása** lehetőséget a leképezési lista fölötti eszköztárból.

1. Válassza ki a **segédprogramokat.** a felugró párbeszédpanelen törölje a szándékot.

    > [!div class="mx-imgBorder"]
    > ![Előre összeépített leképezés hozzáadása](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Kattintson a **kész** gombra.

## <a name="add-a-prebuilt-entity"></a>Előre összeépített entitás hozzáadása
1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. Válassza ki a bal oldali **entitásokat** .

1. Az **entitások** lapon válassza az **előre felépített entitás hozzáadása**elemet.

1. Az **előre elkészített entitások hozzáadása** párbeszédpanelen válassza ki az előre elkészített entitást.

    > [!div class="mx-imgBorder"]
    > ![Előre összeépített entitás hozzáadása párbeszédpanel](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Válassza a **Done** (Kész) lehetőséget. Az entitás hozzáadása után nem kell betanítania az alkalmazást.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Közzététel az előre elkészített modell előrejelzési végpontból való megtekintéséhez

Egy előre elkészített modell értékének a legegyszerűbb módja a közzétett végpontról történő lekérdezés.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Előre elkészített entitási jogkivonatot tartalmazó entitások

Ha olyan géppel megtanult entitással rendelkezik, amely egy előre elkészített entitás kötelező funkcióját igényli, vegyen fel egy alösszetevőt a géppel megismert entitásba, majd adja hozzá az előre elkészített entitás _kötelező_ funkcióját.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Modell létrehozása a. csv-ből REST API-kkal](./luis-tutorial-node-import-utterances-csv.md)
