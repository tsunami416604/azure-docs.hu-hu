---
title: Szolgáltatás konfigurációja - QnA Maker
description: Ismerje meg, hogyan és hol kell konfigurálni az erőforrásokat.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 3be32d1778604121c2acac88415cbfbc4bdbca3d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804260"
---
# <a name="service-configuration"></a>Szolgáltatáskonfiguráció

A QnA Maker számos Azure-erőforrást (szolgáltatást) használ, beleértve a Cognitive Search, az App Service, az App Service-csomag és az Application Insights szolgáltatást.

A QnA Maker által támogatott beállítások összes testreszabása az alábbiakban található.

## <a name="app-service"></a>App Service

A QnA Maker az App Service segítségével biztosítja a [generateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)által használt lekérdezési futásidőt.


Ezek a beállítások az Azure Portalon érhetők el az App Service számára. A beállítások a **Beállítások**, majd a **Konfiguráció**lehetőség kiválasztásával érhetők el.

Az egyéni beállításokat az Alkalmazás beállítások listájában állíthatja be, vagy több beállítást is módosíthat a **Speciális szerkesztés**lehetőség kiválasztásával.

|Erőforrás|Beállítás|
|--|--|
|AzureSearchAdminKey|Kognitív keresés - használt QnA pár tárolás és Ranker #1|
|AzureSearchName|Kognitív keresés - használt QnA pár tárolás és Ranker #1|
|DefaultAnswer|Válasz szövege, ha nem található egyezés|
|UserAppInsightsAppId|Csevegési napló és telemetria|
|UserAppInsightsKulcs|Csevegési napló és telemetria|
|UserAppInsightsName|Csevegési napló és telemetria|

További információ [arról, hogyan adhat hozzá módosítási szolgáltatást](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) a szolgáltatáshoz.

Újra kell **indítania** a szolgáltatást az Azure Portal **áttekintése** lapról, miután végzett a módosításokkal.

## <a name="qna-maker-service"></a>QnA Maker szolgáltatás

A QnA Maker szolgáltatás a következő felhasználók számára biztosít konfigurációt egyetlen QnA Maker szolgáltatás és annak összes tudásbázisa közös en.

[Ismerje meg, hogyan vehet fel közreműködőket](./how-to/collaborate-knowledge-base.md) a szolgáltatásba.

## <a name="application-insights"></a>Application Insights

Az Application Insights nem rendelkezik a QnA Maker re vonatkozó konfigurációs beállításokkal.

## <a name="app-service-plan"></a>App Service-csomag

Az App Service-csomag nem rendelkezik a QnA Maker re vonatkozó konfigurációs beállításokkal.

## <a name="next-steps"></a>További lépések

További információ [formats](reference-document-format-guidelines.md) a tudásbázisba importálni kívánt dokumentumok és URL-ek formátumairól.