---
title: Kereskedelmipartner-egyezmények – Azure Logic Apps elindíthatók
description: Létrehozása és kezelése a kereskedelmi partnerekkel az Azure Logic Apps és az Enterprise Integration Pack között létrejött megállapodások
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 4bfee4ec442c9e7b0351b0fd0c6a2b8e163a2541
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330306"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Létrehozásához és kezeléséhez kereskedelmipartner-egyezmények az Azure Logic Appsben

A [kereskedelmi partner](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*szerződés* segítségével a szervezetek és vállalatok egymással zökkenőmentesen szolgáltatásbusszal használni kívánt konkrét szabványos protokollt definiálásával vállalatközi (B2B) üzenet. Szerződés előnyei közös, például:

* Engedélyezze a szervezetek számára, hogy az exchange-adatokat egy jól ismert formátum használatával.
* Hatékonyságnövelés B2B-tranzakciók során.
* Is egyszerűen létrehozása, kezelése és használata a vállalati integrációs megoldások létrehozása.

Ez a cikk bemutatja, hogyan hozhat létre egy AS2, EDIFACT vagy X12, amelyet használhat a B2B-forgatókönyvekhez integrációs megoldások létrehozását, nagyvállalati szerződés a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) és [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Miután egy szerződést hoz létre, akkor használhatja az AS2, EDIFACT vagy X12 B2B-üzenetek kicserélni az összekötőket.

Szerződések RosettaNet üzenetváltásokban létrehozásával kapcsolatban lásd: [Exchange RosettaNet üzenetek](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetésem, [regisztráljon egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a szerződés és más B2B összetevők tárolására. Az integrációs fiók az Azure-előfizetése társítva kell lennie.

* Legalább két [kereskedelmi partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) már létrehozott az integrációs fiókban. Egy szerződést egy fogadó partner és a egy vendégpartner is szükség van. Mindkét partner kell használnia az azonos "üzleti identitás" minősítő szeretne létrehozni, például az AS2, X 12 vagy EDIFACT a szerződés szerint.

* Nem kötelező: A logikai alkalmazás, ahol szeretné használni, a szerződés és a egy eseményindítót, amely elindítja a logikai alkalmazás munkafolyamat. Az integrációs fiók és a B2B összetevők csak létrehozásának, nem kell egy logikai alkalmazást. Azonban ahhoz a logikai alkalmazás a B2B-összetevőket az integrációs fiókban lévő, hozzá kell rendelnie az integrációs fiók a logikai alkalmazáshoz. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Szerződések létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
Az Azure fő menüjéből válassza **minden szolgáltatás**. A keresőmezőbe írja be szűrőként "integráció névre". Az eredmények közül válassza ki az ehhez az erőforráshoz: **Integrációs fiókok**

   ![Keresse meg az integrációs fiók](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. A **integrációs fiókok**, válassza ki az integrációs fiók, ahol szeretné létrehozni a szerződést.

   ![Válassza ki az integrációs fiók, hová hozza létre a szerződés](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. A jobb oldali ablaktáblán a **összetevők**, válassza ki a **szerződések** csempére.

   ![Válassza a "Szerződés"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. A **szerződések**, válassza a **Hozzáadás**. Az a **Hozzáadás** panelen adja meg például a szerződéssel kapcsolatos információk:

   ![Válassza az "Add"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Tulajdonság | Szükséges | Value | Leírás |
   |----------|----------|-------|-------------|
   | **Name (Név)** | Igen | <*agreement-name*> | A Szerződés neve |
   | **Szerződés típusa** | Igen | **AS2**, **X12**, vagy **EDIFACT** | A szerződés protokoll típusa. A szerződés fájl létrehozásakor a tartalmat, az adott fájlban meg kell egyeznie a szerződés típusának. | |  
   | **Gazdagéppartner** | Igen | <*host-partner-name*> | A gazdagéppartner jelöli a szervezet, amely meghatározza a szerződés |
   | **Gazdagép-identitás** | Igen | <*host-partner-identifier*> | A gazdagéppartner azonosítója |
   | **Vendégpartner** | Igen | <*guest-partner-name*> | A vendégpartner a szervezet, amely a fogadó partner üzleti jelenti. |
   | **Vendégidentitás** | Igen | <*Vendég-partner-azonosító*> | A vendégpartner azonosítója |
   | **Beállítások** | Változó | Változó | Ezeket a tulajdonságokat adja meg, hogyan a fogadó partner a vendégpartner a szerződés minden bejövő üzenetet kap. További információkért tekintse meg a megfelelő típusát: <p>- [AS2-üzenetek beállításai](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-üzenetek beállításai](logic-apps-enterprise-integration-edifact.md) <br>- [X12 üzenet beállításai](logic-apps-enterprise-integration-x12.md) |
   | **Küldési beállítások** | Változó | Változó | Ezeket a tulajdonságokat adja meg, hogyan a fogadó partner a vendégpartner a szerződés minden kimenő üzeneteket küld. További információkért tekintse meg a megfelelő típusát: <p>- [AS2-üzenetek beállításai](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-üzenetek beállításai](logic-apps-enterprise-integration-edifact.md) <br>- [X12 üzenet beállításai](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Ha befejezte a szerződés létrehozása a **Hozzáadás** lapon a **OK**, és térjen vissza az integrációs fiók.

   A **szerződések** listában láthatók az új szerződés.

## <a name="edit-agreements"></a>Szerződés szerkesztése

1. Az a [az Azure portal](https://portal.azure.com), az Azure fő menüjéből válassza **minden szolgáltatás**.

1. A keresőmezőbe írja be szűrőként "integráció névre". Az eredmények közül válassza ki az ehhez az erőforráshoz: **Integrációs fiókok**

1. A **integrációs fiókok**, válassza ki az integrációs fiók, amely rendelkezik a megállapodás szerkesztéséhez.

1. A jobb oldali ablaktáblán a **összetevők**, válassza ki a **szerződések** csempére.

1. A **szerződések**, válassza ki a szerződését, és válassza a **szerkesztése**.

1. Győződjön meg arról, és mentse a módosításokat.

## <a name="delete-agreements"></a>A szerződések törlése

1. Az a [az Azure portal](https://portal.azure.com), az Azure fő menüjéből válassza **minden szolgáltatás**.

1. A keresőmezőbe írja be szűrőként "integráció névre". Az eredmények közül válassza ki az ehhez az erőforráshoz: **Integrációs fiókok**

1. A **integrációs fiókok**, válassza ki az integrációs fiók, amely rendelkezik a megállapodás törli.

1. A jobb oldali ablaktáblán a **összetevők**, válassza ki a **szerződések** csempére.

1. A **szerződések**, válassza ki a szerződését, és válassza a **törlése**.

1. Győződjön meg arról, hogy szeretné-e a kiválasztott szerződés törlése.

## <a name="next-steps"></a>További lépések

* [AS2-üzenetek váltása](logic-apps-enterprise-integration-as2.md)
* [EDIFACT-üzenetek](logic-apps-enterprise-integration-edifact.md)
* [Az Exchange X12 üzenetek](logic-apps-enterprise-integration-x12.md)
