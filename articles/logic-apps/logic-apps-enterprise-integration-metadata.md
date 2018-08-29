---
title: Integrációs fiók összetevő metaadatai – Azure Logic Apps-alkalmazások kezelése |} A Microsoft Docs
description: Adjon hozzá vagy összetevő metaadatai lekérése az Azure Logic Apps Enterprise Integration Pack-integrációs fiókok
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 02/23/2018
ms.openlocfilehash: 537014c2780fe94cfb35806759f8bcbd974c4c95
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128803"
---
# <a name="manage-artifact-metadata-from-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Összetevő metaadatai kezelése az Azure Logic Apps Enterprise Integration Pack-integrációs fiókok

Egyéni összetevők metaadatainak megadása az integrációs fiókok, és a metaadatok lekérése során a logikai alkalmazás. Például megadhatja metaadat-összetevők, például a partnerek, szerződések, sémákat és a maps - kulcs-érték párok metaadatait az összes áruház. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Adja hozzá a metaadatokat az összetevőket az integrációs fiókok

1. Az Azure Portalon hozzon létre egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md).

2. Adja hozzá például egy összetevő az integrációs fiók, amely egy [partner](logic-apps-enterprise-integration-partners.md), [szerződés](logic-apps-enterprise-integration-agreements.md), vagy [séma](logic-apps-enterprise-integration-schemas.md).

3. Válassza ki a lehívandó összetevő, válassza a **szerkesztése**, és adja meg a metaadatok részleteit.

   ![Adja meg a metaadatok](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Metaadatok beolvasása a logic apps összetevők

1. Az Azure Portalon hozzon létre egy [logikai alkalmazás](quickstart-create-first-logic-app-workflow.md).

2. Hozzon létre egy [hivatkozásra a logikai alkalmazás az integrációs fiókba](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. A Logikaialkalmazás-tervezőben, mint trigger hozzáadása **kérelem** vagy **HTTP** a logikai alkalmazáshoz.

4. Az eseményindító területén válassza a **új lépés** > **művelet hozzáadása**. Keressen a "integrációs fiók" úgy keresheti meg és válassza ki a következő műveletet: **integrációs fiók – integrációs fiók Összetevőkeresése**

   ![Válassza ki az integrációs fiók Összetevőkeresése](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Válassza ki a **Összetevőtípussal** , és adja meg a **összetevőnév**. Példa:

   ![Válassza ki az összetevő típusa, és adja meg az összetevő neve](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Példa: Lekérése partner metaadatok

Tegyük fel, hogy a partnerek számára a metaadat- `routingUrl` részletei:

![Partner "routingURL" metaadatok keresése](media/logic-apps-enterprise-integration-metadata/image6.png)

1. A logikai alkalmazást, adja hozzá az eseményindító- **integrációs fiók – integrációs fiók Összetevőkeresése** művelet esetén a partner, és a egy **HTTP** művelet, például:

   ![Az eseményindító összetevőkeresése és HTTP-művelet hozzáadása a logikai alkalmazás](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Az URI-t, a Logikaialkalmazás-Tervező eszköztáron lekéréséhez válassza **Kódnézet** a logikai alkalmazás. A logikai alkalmazás definíciójának példához hasonlóan kell kinéznie:

   ![Search keresési](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>További lépések

* [További tudnivalók a szerződések](logic-apps-enterprise-integration-agreements.md)
