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
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013584"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Előre összeépített modellek hozzáadása általános használati forgatókönyvekhez 

A LUIS előre elkészített modelleket tartalmaz, amelyekkel gyorsan hozzáadhat gyakori, társalgási felhasználói forgatókönyveket. Ezzel a módszerrel gyorsan és egyszerűen hozzáadhat képességeket a társalgási ügyfélalkalmazás számára anélkül, hogy a modelleket meg kellene terveznie az adott képességekhez. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

1. Az a **saját alkalmazások** lapon, válassza ki az alkalmazást. Ekkor megnyílik az alkalmazás a **összeállítása** szakaszában. 

1. Válassza az **előre elkészített tartományok** lehetőséget a bal oldali eszköztárból. 

1. Keresse meg azt a tartományt, amelyet hozzá szeretne adni az alkalmazáshoz, majd válassza a **tartomány hozzáadása** gombot.

    > [!div class="mx-imgBorder"]
    > ![naptár előre elkészített tartományának hozzáadása](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Egy előre elkészített leképezés hozzáadása

1. Az a **saját alkalmazások** lapon, válassza ki az alkalmazást. Ekkor megnyílik az alkalmazás a **összeállítása** szakaszában. 

1. A **leképezések** lapon válassza az **előre felépített tartományhoz rendelés hozzáadása** lehetőséget a leképezési lista fölötti eszköztárból. 

1. Válassza ki a **Utilities.Cancel** szándék az előugró párbeszédpanelen. 

    > [!div class="mx-imgBorder"]
    > ![előre összeépített leképezés hozzáadása](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Válassza ki a **kész** gombra.

## <a name="add-a-prebuilt-entity"></a>Egy előre létrehozott entitás hozzáadása

1. Nyissa meg az alkalmazás nevére kattintva **saját alkalmazások** lapon, és kattintson a **entitások** a bal oldalon. 

1. Az a **entitások** kattintson **előre összeállított entitás hozzáadása**.

1. Az **előre elkészített entitások hozzáadása** párbeszédpanelen válassza ki az előre elkészített entitást. 

    > [!div class="mx-imgBorder"]
    > ![előre összeépített entitás hozzáadása párbeszédpanel](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Válassza a **Done** (Kész) lehetőséget. Az entitás hozzáadása után nem kell az alkalmazás betanításához. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Közzététel az előre elkészített modell előrejelzési végpontból való megtekintéséhez

Egy előre elkészített modell értékének a legegyszerűbb módja a közzétett végpontról történő lekérdezés. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Előre elkészített entitási jogkivonatot tartalmazó entitások
 
Ha olyan géppel megtanult entitást használ, amelyet egy előre elkészített entitás korlátoz, adjon hozzá egy alösszetevőt a géppel megismert entitáshoz, majd adjon meg egy előre elkészített entitáshoz tartozó korlátozást.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Modell létrehozása a. csv-ből REST API-kkal](./luis-tutorial-node-import-utterances-csv.md)
