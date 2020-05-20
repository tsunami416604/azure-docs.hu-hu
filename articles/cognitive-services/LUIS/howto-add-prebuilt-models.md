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
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: abf149d621d283744a71e08ea4ac2199a1994d57
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680951"
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

1. Válasszon ki egy leképezést az előugró párbeszédpanelről.

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

## <a name="add-a-prebuilt-domain-entity"></a>Előre elkészített tartományi entitás hozzáadása
1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. Válassza ki a bal oldali **entitásokat** .

1. Az **entitások** lapon válassza az **előre elkészített tartomány entitás hozzáadása**elemet.

1. Az **előre elkészített tartományi modellek hozzáadása** párbeszédpanelen válassza ki az előre elkészített tartomány entitást.

1. Válassza a **Done** (Kész) lehetőséget. Az entitás hozzáadása után nem kell betanítania az alkalmazást.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Közzététel az előre elkészített modell előrejelzési végpontból való megtekintéséhez

Egy előre elkészített modell értékének a legegyszerűbb módja a közzétett végpontról történő lekérdezés.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Előre elkészített entitási jogkivonatot tartalmazó entitások

Ha olyan gépi tanulási entitással rendelkezik, amely egy előre elkészített entitás kötelező funkcióját igényli, vegyen fel egy alentitást a gépi tanulási entitásba, majd adja hozzá az előre elkészített entitás _kötelező_ funkcióját.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Modell létrehozása a. csv-ből REST API-kkal](./luis-tutorial-node-import-utterances-csv.md)
