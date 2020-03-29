---
title: Előre összeállított nyelvi ismeretek modelljei
titleSuffix: Azure Cognitive Services
description: A LUIS egy előre elkészített modellkészletet tartalmaz a gyakori, társalgási felhasználói forgatókönyvek gyors hozzáadásához.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013584"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Előre összeállított modellek hozzáadása gyakori használati forgatókönyvekhez 

A LUIS egy előre elkészített modellkészletet tartalmaz a gyakori, társalgási felhasználói forgatókönyvek gyors hozzáadásához. Ez egy gyors és egyszerű módja annak, hogy adjunk képességek a társalgási kliens alkalmazás anélkül, hogy tervezze meg a modellek et ezeket a képességeket. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

1. A **Saját alkalmazások** lapon válassza ki az alkalmazást. Ez megnyitja az alkalmazást az alkalmazás **Build** szakaszában. 

1. Válassza **az Előre összeállított tartományok lehetőséget** a bal oldali eszköztáron. 

1. Keresse meg az alkalmazáshoz hozzáadni kívánt tartományt, majd válassza a **Tartomány hozzáadása** gombot.

    > [!div class="mx-imgBorder"]
    > ![Előre összeállított naptár hozzáadása](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Előre összeállított leképezés hozzáadása

1. A **Saját alkalmazások** lapon válassza ki az alkalmazást. Ez megnyitja az alkalmazást az alkalmazás **Build** szakaszában. 

1. A **Szándékok** lapon válassza **a Leképezések listájának hozzáadása előre összeállított tartományi leképezést** a leképezések listájának felett található eszköztáron. 

1. Válassza a **Utilities.Cancel** szándékot az előugró párbeszédpanelen. 

    > [!div class="mx-imgBorder"]
    > ![Előre összeállított leképezés hozzáadása](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Válassza a **Kész** gombot.

## <a name="add-a-prebuilt-entity"></a>Előre összeállított entitás hozzáadása

1. Nyissa meg az alkalmazást úgy, hogy a **nevére** kattint a Saját alkalmazások lapon, majd a bal oldalon az **Entitások** elemre. 

1. Az **Entitások** lapon kattintson az **Előre összeállított entitás hozzáadása gombra.**

1. Az **Előre összeállított entitások hozzáadása** párbeszédpanelen jelölje ki az előre összeállított entitást. 

    > [!div class="mx-imgBorder"]
    > ![Előre összeállított entitás hozzáadása párbeszédpanel](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Válassza a **Done** (Kész) lehetőséget. Az entitás hozzáadása után nem kell betanítania az alkalmazást. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Közzététel az előre összeállított modell megtekintéséhez az előrejelzési végpontról

Az előre összeállított modell értékének megtekintésének legegyszerűbb módja a közzétett végpontról történő lekérdezés. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Előre összeállított entitásjogkivonatot tartalmazó entitások
 
Ha egy előre összeállított entitás által korlátozott gépmegtanult entitást hoz létre, adjon hozzá egy alösszetevőt a gép megtanult entitáshoz, majd adjon hozzá egy előre összeállított entitás megkötését.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Modell létrehozása .csv-ből REST API-kkal](./luis-tutorial-node-import-utterances-csv.md)
