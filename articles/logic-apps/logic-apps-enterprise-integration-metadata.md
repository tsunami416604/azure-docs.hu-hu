---
title: Integrációs fiók összetevő metaadatai – Azure Logic Apps-alkalmazások kezelése |} A Microsoft Docs
description: Adja hozzá, vagy kérjen összetevő metaadatai Azure Logic Apps Enterprise Integration Pack-integrációs fiókok
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 01/17/2019
ms.openlocfilehash: 5ebdf45bec4e7cfceb75354af40c7a21c22c6eef
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446782"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Összetevő metaadatai a az Azure Logic Apps és az Enterprise Integration Pack-integrációs fiókok kezelése

Egyéni összetevők metaadatainak megadása az integrációs fiókok, és a metaadatok beolvasása a logikai alkalmazás használata során. Például megadhat metaadat-összetevők, például a partnerek, szerződések, sémákat és a maps - kulcs-érték párok metaadatait az összes áruház. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Egy alapszintű [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely rendelkezik az összetevők hol szeretne adja hozzá a metaadatokat, például: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Szerződés](logic-apps-enterprise-integration-agreements.md)
  * [séma](logic-apps-enterprise-integration-schemas.md)
  * [Térkép](logic-apps-enterprise-integration-maps.md)

* Egy logikai alkalmazást, amely kapcsolódik az integrációs fiók és az összetevő metaadatai szeretné használni. Ha a logikai alkalmazás nem már össze van kapcsolva, további [a logic apps és integrációs fiókok összekapcsolása](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Ha még nem rendelkezik egy logikai alkalmazást, további [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Adja hozzá az eseményindító és az összetevő metaadatai kezeléséhez használni kívánt műveleteket. Vagy próbálkozás csinálni, adjon hozzá egy eseményindító például **kérelem** vagy **HTTP** a logikai alkalmazáshoz.

## <a name="add-metadata-to-artifacts"></a>Adja hozzá a metaadatokat az összetevőket

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a> az Azure-fiókja hitelesítő adataival. Keresse meg és nyissa meg az integrációs fiókot.

1. Válassza ki az összetevőben, ahol szeretné ad hozzá metaadatokat, és válassza a **szerkesztése**. Adja meg, hogy metaadatokat adott összetevő, például:

   ![Enter metadata](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Ha elkészült, válassza ki a **OK**.

1. Az integrációs fiók JavaScript Object Notation (JSON) definíciója a metaadatok megtekintéséhez válassza **Szerkesztés JSON-ként** úgy, hogy a JSON-szerkesztő megnyitása: 

   ![A partner metaadatokat JSON](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Összetevők metaadatainak beolvasása

1. Az Azure Portalon nyissa meg a logikai alkalmazás, amely kapcsolódik a kívánt integrációs fiókban. 

1. A Logic App Designerben, ha a lépés a trigger vagy az utolsó művelet alatt metaadatok beolvasása a munkafolyamat számára, válasszon **új lépés** > **művelet hozzáadása**. 

1. A Keresés mezőbe írja be az "integrációs fiók". A keresőmező alatt válassza ki a **összes**. A műveletek listából válassza a következő műveletet: **Integrációs fiók Összetevőkeresése - integrációs fiók**

   ![Válassza ki a "Integrációs fiók Összetevőkeresése"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Adja meg a keresett összetevőre információkat:

   | Tulajdonság | Szükséges | Érték | Leírás | 
   |----------|---------|-------|-------------| 
   | **Összetevő típusa** | Igen | **Séma**, **térkép**, **Partner**, **szerződés**, vagy egy egyéni típus | A kívánt összetevő típusa | 
   | **Összetevő neve** | Igen | <*artifact-name*> | A kívánt összetevő neve | 
   ||| 

   Például tegyük fel, hogy a metaadatok lekérése egy kereskedelmi partneri összetevő:

   ![Válassza ki az összetevő típusa, és adja meg az összetevő neve](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Adja hozzá a műveletre, amelyet az adott metaadatokat, például:

   1. Alatt a **integrációs fiók Összetevőkeresése** műveletet, válassza a **következő lépés**, és válassza ki **művelet hozzáadása**. 

   1. A Keresés mezőbe írja be a "http". A keresőmező alatt válassza ki a **Built-ins**, és válassza a következő műveletet: **HTTP - HTTP**

      ![HTTP-művelet hozzáadása](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Az összetevő metaadatai kezelni kívánt információkat biztosítanak. 

      Tegyük fel például, hogy szeretne a `routingUrl` témakör korábbi szakaszában felvett metaadatait. Az alábbiakban megadhatja a tulajdonságértékek: 

      | Tulajdonság | Szükséges | Érték | Leírás | 
      |----------|----------|-------|-------------| 
      | **Metódus** | Igen | <*operation-to-run*> | A HTTP-műveletből a lehívandó összetevő fusson. Például a HTTP-művelet használja a **első** metódust. | 
      | **URI-T** | Igen | <*metadata-location*> | Hozzáférés a `routingUrl` metaadatok értéket az összetevőben, beolvassa, egy kifejezés használható, például: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Fejlécek** | Nem | <*header-values*> | Bármilyen fejlécet jelenít meg a HTTP-művelet át szeretné a trigger által. Például adja át az eseményindító `headers` tulajdonság értéke: kifejezés, például használhatja: <p>`@triggeroutputs()['headers']` | 
      | **Törzs** | Nem | <*body-content*> | A HTTP-művelet keresztül átadni kívánt egyéb tartalmat `body` tulajdonság. Ez a példa továbbítja a lehívandó összetevő `properties` értékeket cserélheti le a HTTP-művelet: <p>1. Kattintson a **törzs** tulajdonság, így a dinamikus tartalmak listája jelenik meg. Ha nincsenek megadva tulajdonságok jelennek meg, válassza ki a **Továbbiak**. <br>2. A dinamikus tartalmú listából alatt **integrációs fiók Összetevőkeresése**válassza **tulajdonságok**. | 
      |||| 

      Példa:

      ![Adja meg az értékeket, és a HTTP-művelet a kifejezések](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. A HTTP-művelet a megadott információk ellenőrzéséhez tekintse meg a logikai alkalmazás JSON-definícióját. Logikaialkalmazás-Tervező eszköztárán válassza a **Kódnézet** így az alkalmazás JSON-definíció jelenik meg, például:

      ![Logikai alkalmazás JSON-definíció](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Miután, váltson vissza a Logikaialkalmazás-Tervező, használt kifejezéseket most jelennek meg feloldani, például:

      ![Logic App Designerben megoldott kifejezések](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>További lépések

* [További tudnivalók a szerződések](logic-apps-enterprise-integration-agreements.md)
