---
title: Szolgáltatás konfigurációja – QnA Maker
description: Az erőforrások konfigurálásának ismertetése.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651860"
---
# <a name="service-configuration"></a>Szolgáltatás konfigurációja

A QnA Maker számos Azure-erőforrást (szolgáltatást) használ, beleértve a Cognitive Search, a App Service, a App Service-csomagot és a Application Insights.

Az QnA Maker által támogatott beállítások minden testreszabása alább látható.

## <a name="app-service"></a>App Service

A QnA Maker a App Service használatával biztosítja a [GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)által használt lekérdezési futtatókörnyezetet.


Ezek a beállítások a App Service Azure Portal érhetők el. A beállítások a **Beállítások**, majd a **konfiguráció**lehetőség kiválasztásával érhetők el.

Az egyes beállításokat az Alkalmazásbeállítások listáján keresztül is megadhatja, vagy a **Speciális szerkesztés**lehetőség kiválasztásával több beállítást is módosíthat.

|Erőforrás|Beállítás|
|--|--|
|AzureSearchAdminKey|Cognitive Search – a QnA-készlet tárolásához és a rangsorhoz használt #1|
|AzureSearchName|Cognitive Search – a QnA-készlet tárolásához és a rangsorhoz használt #1|
|DefaultAnswer|A válasz szövege, ha nem található egyezés|
|UserAppInsightsAppId|Csevegési napló és telemetria|
|UserAppInsightsKey|Csevegési napló és telemetria|
|UserAppInsightsName|Csevegési napló és telemetria|

Megtudhatja [, hogyan adhatja hozzá a Cognitive Search szolgáltatást](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) a szolgáltatáshoz.

A módosítások végrehajtása után **újra kell indítania** a szolgáltatást a Azure Portal **Áttekintés** lapjáról.

## <a name="qna-maker-service"></a>A QnA Maker szolgáltatás

A QnA Maker szolgáltatás olyan konfigurációt biztosít az alábbi felhasználók számára, akik együttműködnek egyetlen QnA Maker szolgáltatásban és annak összes tudásbázisában.

Ismerje meg [, hogyan adhat közreműködőket](./how-to/collaborate-knowledge-base.md) a szolgáltatáshoz.

## <a name="application-insights"></a>Application Insights

Application Insights nem rendelkezik a QnA Makerra vonatkozó konfigurációs beállításokkal.

## <a name="app-service-plan"></a>App Service-csomag

App Service a csomag nem rendelkezik a QnA Makerra vonatkozó konfigurációs beállításokkal.

## <a name="next-steps"></a>Következő lépések

További információ a tudásbázisba importálni kívánt dokumentumok és URL-címek [formátumáról](reference-document-format-guidelines.md) .