---
title: Szolgáltatás konfigurációja – QnA Maker
description: Az erőforrások konfigurálásának ismertetése.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 3be32d1778604121c2acac88415cbfbc4bdbca3d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804260"
---
# <a name="service-configuration"></a>Szolgáltatáskonfiguráció

A QnA Maker számos Azure-erőforrást (szolgáltatást) használ, beleértve a Cognitive Search, a App Service, a App Service-csomagot és a Application Insights.

Az QnA Maker által támogatott beállítások minden testreszabása alább látható.

## <a name="app-service"></a>App Service

A QnA Maker a App Service használatával biztosítja a [GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)által használt lekérdezési futtatókörnyezetet.


Ezek a beállítások a App Service Azure Portal érhetők el. A beállítások a **Beállítások**, majd a **konfiguráció**lehetőség kiválasztásával érhetők el.

Az egyes beállításokat az Alkalmazásbeállítások listáján keresztül is megadhatja, vagy a **Speciális szerkesztés**lehetőség kiválasztásával több beállítást is módosíthat.

|Erőforrás|Beállítás|
|--|--|
|AzureSearchAdminKey|Cognitive Search – QnA pár Storage és ranker #1|
|AzureSearchName|Cognitive Search – QnA pár Storage és ranker #1|
|DefaultAnswer|A válasz szövege, ha nem található egyezés|
|UserAppInsightsAppId|Csevegési napló és telemetria|
|UserAppInsightsKey|Csevegési napló és telemetria|
|UserAppInsightsName|Csevegési napló és telemetria|

Megtudhatja [, hogyan adhatja hozzá a Cognitive Search szolgáltatást](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) a szolgáltatáshoz.

A módosítások végrehajtása után **újra kell indítania** a szolgáltatást a Azure Portal **Áttekintés** lapjáról.

## <a name="qna-maker-service"></a>QnA Maker szolgáltatás

A QnA Maker szolgáltatás olyan konfigurációt biztosít az alábbi felhasználók számára, akik együttműködnek egyetlen QnA Maker szolgáltatásban és annak összes tudásbázisában.

Ismerje meg [, hogyan adhat közreműködőket](./how-to/collaborate-knowledge-base.md) a szolgáltatáshoz.

## <a name="application-insights"></a>Application Insights

Application Insights nem rendelkezik a QnA Makerra vonatkozó konfigurációs beállításokkal.

## <a name="app-service-plan"></a>App Service-csomag

App Service a csomag nem rendelkezik a QnA Makerra vonatkozó konfigurációs beállításokkal.

## <a name="next-steps"></a>További lépések

További információ a tudásbázisba importálni kívánt dokumentumok és URL-címek [formátumáról](reference-document-format-guidelines.md) .