---
title: Integrációs fiók összetevőjének metaadatainak kezelése
description: Műtermék-metaadatok hozzáadása vagy bekéselése az Azure Logic Apps vállalati integrációs csomaggal rendelkező integrációs fiókokból
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792472"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Műtermék-metaadatok kezelése az integrációs fiókokban az Azure Logic Apps és enterprise integration pack csomaggal

Egyéni metaadatokat adhat meg az integrációs fiókokban lévő műtermékekhez, és levezetheti a metaadatokat futásidőben a logikai alkalmazás használatához. Például metaadatokat adhat meg az összetevőkhöz, például a partnerekhez, megállapodásokhoz, sémákhoz és térképekhez – az összes metaadatot kulcs-érték párok használatával tárolja. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Egy alapszintű [integrációs fiók,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) amely rendelkezik az összetevők, ahol hozzá kívánja adni a metaadatokat, például: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Egyezmények](logic-apps-enterprise-integration-agreements.md)
  * [Séma](logic-apps-enterprise-integration-schemas.md)
  * [Térkép](logic-apps-enterprise-integration-maps.md)

* Egy logikai alkalmazás, amely kapcsolódik az integrációs fiók és a műtermék metaadatokat szeretne használni. Ha a logikai alkalmazás még nincs csatolva, ismerje meg, [hogyan kapcsolhatja össze a logikai alkalmazásokat az integrációs fiókokhoz.](logic-apps-enterprise-integration-create-integration-account.md#link-account) 

  Ha még nem rendelkezik logikai alkalmazással, ismerje meg, [hogyan hozhat létre logikai alkalmazásokat.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 
  Adja hozzá az eseményindítót és a műtermék metaadatainak kezeléséhez használni kívánt műveleteket. Vagy, hogy csak próbálja ki a dolgokat, adjon hozzá egy eseményindító, például **a kérelem** vagy **a HTTP** a logikai alkalmazás.

## <a name="add-metadata-to-artifacts"></a>Metaadatok hozzáadása műtermékekhez

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a> az Azure-fiókja hitelesítő adataival. Keresse meg és nyissa meg integrációs fiókját.

1. Jelölje ki azt a műterméket, ahová metaadatokat szeretne hozzáadni, és válassza **a Szerkesztés gombot.** Adja meg az adott összetevő metaadatait, például:

   ![Metaadatok megadása](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Ha elkészült, válassza az **OK** gombot.

1. Ha meg szeretné tekinteni ezt a metaadatot az integrációs fiók JavaScript objektumjelölés (JSON) definíciójában, válassza a **Szerkesztés JSON-ként** lehetőséget, hogy a JSON-szerkesztő megnyíljon: 

   ![JSON a partner metaadataihoz](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Műtermék-metaadatok beszereznie

1. Az Azure Portalon nyissa meg a logikai alkalmazást, amely a kívánt integrációs fiókhoz kapcsolódik. 

1. Ha a Logikai alkalmazástervezőben a metaadatok eseményindító vagy a munkafolyamat utolsó művelete alá való bejutásának lépése, válassza az **Új lépés** > **művelet hozzáadása**lehetőséget. 

1. A keresőmezőbe írja be az "integrációs fiók" kifejezést. A keresőmező alatt válassza az **Összes**lehetőséget. A műveletlistából válassza a következő műveletet: **Integrációs fiókösszetevő-megvizsgálás – Integrációs fiók**

   ![Válassza az "Integrációs fiók összetevőjének" lehetőséget](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Adja meg a keresett műtermék adatait:

   | Tulajdonság | Kötelező | Érték | Leírás | 
   |----------|---------|-------|-------------| 
   | **Műtermék típusa** | Igen | **Séma**, **térkép,** **partner,** **megállapodás**vagy egyéni típus | A kívánt műtermék típusa | 
   | **Műtermék neve** | Igen | <*műtermék-név*> | A kívánt műtermék neve | 
   ||| 

   Tegyük fel például, hogy egy kereskedelmi partner termékének metaadatait szeretné beszerezni:

   ![Válasszon műterméktípust, és írja be a műtermék nevét](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Adja hozzá a metaadatok kezeléséhez kívánt műveletet, például:

   1. Az **Integrációs fiók műtermék-leã¡lÃ¡s kiválasztása** művelet csoportban válassza a **Következő lépés**lehetőséget, majd a **Művelet hozzáadása**lehetőséget. 

   1. A keresőmezőbe írja be a "http" szót. A keresőmező alatt válassza a **Beépített lehetőséget,** és válassza a következő műveletet: **HTTP - HTTP**

      ![HTTP hozzáadása művelet](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Adja meg a kezelni kívánt műtermék metaadatainak adatait. 

      Tegyük fel például, `routingUrl` hogy a témakör korábbi részén hozzáadott metaadatokat szeretné beszerezni. Az alábbiakat adhatja meg a tulajdonságértékek: 

      | Tulajdonság | Kötelező | Érték | Leírás | 
      |----------|----------|-------|-------------| 
      | **Módszer** | Igen | <*művelet-to-run*> | A leleten futtatandó HTTP-művelet. Ez a HTTP-művelet például a **GET** metódust használja. | 
      | **Uri** | Igen | <*metaadatok helye*> | A `routingUrl` metaadat-érték eléréséhez a beolvasott műtermékből használhat egy kifejezést, például: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Fejlécek** | Nem | <*fejlécértékek*> | A HTTP-műveletbe átadandó eseményindító fejléckimenetei. Például az eseményindító tulajdonságértékének `headers` átadására: használhat egy kifejezést, például: <p>`@triggeroutputs()['headers']` | 
      | **Törzs** | Nem | <*testtartalom*> | Bármely más tartalom, amelyet át szeretne `body` adni a HTTP-művelet tulajdonságán. Ez a példa átadja `properties` a műtermék értékeit a HTTP-műveletnek: <p>1. Kattintson a **Body** tulajdonságon belülre, hogy megjelenjen a dinamikus tartalomlista. Ha nem jelennek meg tulajdonságok, válassza **a Továbbiak**lehetőséget. <br>2. A dinamikus tartalomlistában az **Integrációs fiók összetevőinek listája csoportban**válassza a **Tulajdonságok lehetőséget.** | 
      |||| 

      Példa:

      ![Értékek és kifejezések megadása HTTP művelethez](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. A HTTP-művelethez megadott információk ellenőrzéséhez tekintse meg a logikai alkalmazás JSON-definícióját. A Logic App Designer eszközsorban válassza a **Kód nézet** lehetőséget, hogy az alkalmazás JSON-definíciója megjelenjen, például:

      ![Logikai alkalmazás JSON-definíciója](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Miután visszaváltott a Logic App Designerre, a használt kifejezések most megoldódtak, például:

      ![Feloldott kifejezések a Logic App Designer alkalmazásban](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>További lépések

* [További információ a megállapodásokról](logic-apps-enterprise-integration-agreements.md)
