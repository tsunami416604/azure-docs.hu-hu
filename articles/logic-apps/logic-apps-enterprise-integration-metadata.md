---
title: "Integráció fiók összetevő metaadat - Azure Logic Apps alkalmazásokat kezeléséhez |} Microsoft Docs"
description: "Adja hozzá vagy integrációs fiókok az Azure Logic Apps összetevő metaadatok lekérése"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 11/21/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 5eebae624ea024f2ff8c1fa4764027c05220a15f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Integrációs fiókok logic Apps alkalmazásokat a hitelesítendő metaadatok kezelése

Integrációs fiókok egyéni metaadatait az összetevők ad meg, és a metaadatok lekérése futtatás során a logikai alkalmazásnak. Például megadhatja, hogy az összetevők, például a partnerek, egyezmények, sémák és maps metaadat - összes metaadatok kulcs-érték párok tárolására. Jelenleg az összetevők nem hozható létre a metaadatok felhasználói felületén keresztül, de a REST API-k hozhat létre a metaadatok. Metaadatok hozzáadása a létrehozásakor vagy egy partner, a szerződés vagy a séma kiválasztása az Azure portálon, válassza a **szerkesztése**. Logic Apps alkalmazásokat a hitelesítendő metaadatainak beolvasására, használhatja a összetevő Fiókkeresés integrációs szolgáltatást.

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Integrációs fiókok lévő metaadatok hozzáadása

1. Hozzon létre egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md).

2. Adja hozzá az összetevő integrációs fiókjába, egy [partner](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), [megállapodás](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements), vagy [séma](logic-apps-enterprise-integration-schemas.md).

3.  Válassza ki a összetevő, válassza a **szerkesztése**, és írja be a metaadatok adatait.

    ![Adja meg a metaadatok](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>A logic apps összetevőkhöz metaadatok lekérése

1. Hozzon létre egy [logikai alkalmazás](quickstart-create-first-logic-app-workflow.md).

2. Hozzon létre egy [integrációs fiókjába a Logic Apps alkalmazást kapcsolatot](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app). 

3. Logic App Designer, adja hozzá egy eseményindító például *kérelem* vagy *HTTP* a logikai alkalmazáshoz.

4.  Válasszon **tovább** > **művelet hozzáadása**. Keresse meg *integrációs* , keresse meg és jelölje **integrációs fiók - integráció összetevő Fiókkeresés**.

    ![Válassza ki az integráció összetevő Fiókkeresés](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Válassza ki a **összetevő típus**, és adja meg a **Adatösszetevőt nevét**.

    ![Összetevő típusa, és adja meg az összetevő neve](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Példa: Beolvasni az erőforráspartner adatokat

Partner metaadatok rendelkezik, ezek `routingUrl` részletek:

![Partner "routingURL" metaadatok keresése](media/logic-apps-enterprise-integration-metadata/image6.png)

1. A Logic Apps alkalmazást, adja hozzá az eseményindító egy **integrációs fiók - integráció összetevő Fiókkeresés** művelet esetén a partner és egy **HTTP**.

    ![A Logic Apps alkalmazást eseményindító, a keresési összetevő és a "HTTP" hozzáadása](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Az URI azonosító lekéréséhez használja a logikai alkalmazásnak kód nézetre. A logic app-definíciót a példához hasonlóan kell kinéznie:

    ![Keresési keresési](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>További lépések
* [További információ a megállapodások](logic-apps-enterprise-integration-agreements.md "vállalati integrációs megállapodások ismertetése")  
