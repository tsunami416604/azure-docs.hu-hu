---
title: Kereskedelmi partneri megállapodások
description: Kereskedelmi partnerek közötti megállapodások létrehozása és kezelése az Azure Logic Apps és az Enterprise Integration Pack használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790737"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Kereskedelmi partneri megállapodások létrehozása és kezelése az Azure Logic Apps alkalmazásban

A [kereskedelmi partneri](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*megállapodás* segít a szervezeteknek és a vállalkozásoknak zökkenőmentesen kommunikálni egymással azáltal, hogy meghatározzák a vállalatközi (B2B) üzenetek cseréjekor használandó iparági szabványnak megfelelő protokollt. A megállapodások közös előnyöket biztosítanak, például:

* Lehetővé teszi a szervezetek számára, hogy jól ismert formátumban cseréljenek információt.
* A hatékonyság javítása a B2B tranzakciók végrehajtása során.
* Könnyen létrehozhat, kezelhető és használható vállalati integrációs megoldások létrehozásához.

Ez a cikk bemutatja, hogyan hozhat létre egy AS2, EDIFACT vagy X12-megállapodást, amelyet a Vállalati [integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md) és az [Azure Logic Apps](../logic-apps/logic-apps-overview.md)használatával vállalati integrációs megoldások létrehozásakor használhat B2B-forgatókönyvekhez. Miután létrehozott egy megállapodást, ezután használhatja az AS2, EDIFACT vagy X12 összekötők b2B üzenetek cseréjére.

A RosettaNet-üzenetek cseréjére vonatkozó megállapodások létrehozásáról az Exchange RosettaNet üzenetek című [témakörben van.](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* Integrációs [fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a szerződés és más B2B-összetevők tárolásához. Ezt az integrációs fiókot az Azure-előfizetéshez kell társtársosan kezelni.

* Legalább két [olyan kereskedelmi partner,](../logic-apps/logic-apps-enterprise-integration-partners.md) amelyet már létrehozott az integrációs fiókjában. A megállapodáshoz fogadó partnerre és vendégpartnerre is szükség van. Mindkét partnernek ugyanazt az "üzleti identitás" minősítőt kell használnia, mint a létrehozni kívánt szerződés, például AZ AS2, x12 vagy EDIFACT.

* Nem kötelező: A logikai alkalmazás, ahol szeretné használni a megállapodást, és egy eseményindító, amely elindítja a logikai alkalmazás munkafolyamatát. Az integrációs fiók és a B2B-összetevők létrehozásához nincs szükség logikai alkalmazásra. Azonban mielőtt a logikai alkalmazás az integrációs fiók B2B-összetevőit használhatna, össze kell kapcsolnia az integrációs fiókot a logikai alkalmazással. Ha most kezdi meg a logikai alkalmazásokat, tekintse át [az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Rövid útmutató: Az első logikai alkalmazás létrehozása című ismertetése című ismertetése című ismertetése. [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-agreements"></a>Megállapodások létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
Az Azure főmenüjében válassza a **Minden szolgáltatás lehetőséget.** A keresőmezőbe írja be az "integráció" szót szűrőként. Az eredmények közül válassza ki ezt az erőforrást: **Integrációs fiókok**

   ![Az integrációs fiók megkeresése](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Az **Integrációs fiókok csoportban**válassza ki azt az integrációs fiókot, amelyhez létre szeretné hozni a szerződést.

   ![Válassza ki azt az integrációs fiókot, amelyhez létre szeretné hozni a megállapodást.](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. A jobb oldali ablaktáblában **az Összetevők csoportban**válassza a **Megállapodások csempét.**

   ![Válassza ki a "Megállapodások"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. A **Megállapodások csoportban**válassza **a Hozzáadás**lehetőséget. A **Hozzáadás** ablaktáblán adja meg a szerződéssel kapcsolatos információkat, például:

   ![Válassza a "Hozzáadás" lehetőséget](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Név** | Igen | <*megállapodás neve*> | A megállapodás neve |
   | **Megállapodás típusa** | Igen | **AS2**, **X12**vagy **EDIFACT** | A megállapodás protokolltípusa. A szerződésfájl létrehozásakor a fájl tartalmának meg kell egyeznie a szerződéstípussal. | |  
   | **Fogadó partner** | Igen | <*állomás-partner név*> | A fogadó partner képviseli a megállapodást megadó szervezetet. |
   | **Állomás identitása** | Igen | <*állomás-partner azonosító*> | A gazdapartner azonosítója |
   | **Vendégpartner** | Igen | <*vendégpartner-név*> | A vendégpartner képviseli azt a szervezetet, amely a fogadó partnerrel üzleti kapcsolatban áll |
   | **Vendég identitása** | Igen | <*vendégpartner-azonosító*> | A vendégpartner azonosítója |
   | **Fogadási beállítások** | Változó | Változó | Ezek a tulajdonságok határozzák meg, hogy a gazdapartner hogyan fogadja az összes bejövő üzenetet a megállapodásban szereplő vendégpartnertől. További információ: a megfelelő megállapodástípus: <p>- [AS2 üzenetbeállítások](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT üzenet beállítások](logic-apps-enterprise-integration-edifact.md) <br>- [X12 üzenet beállításai](logic-apps-enterprise-integration-x12.md) |
   | **Beállítások küldése** | Változó | Változó | Ezek a tulajdonságok határozzák meg, hogy a gazdapartner hogyan küldi el az összes kimenő üzenetet a megállapodásban szereplő vendégpartnernek. További információ: a megfelelő megállapodástípus: <p>- [AS2 üzenetbeállítások](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT üzenet beállítások](logic-apps-enterprise-integration-edifact.md) <br>- [X12 üzenet beállításai](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Ha végzett a szerződés létrehozásával, a **Hozzáadás** lapon válassza az **OK**gombot, és térjen vissza az integrációs fiókhoz.

   A **Megállapodások** lista mostantól az új megállapodást mutatja.

## <a name="edit-agreements"></a>Megállapodások szerkesztése

1. Az [Azure Portalon](https://portal.azure.com)válassza az Összes **szolgáltatás**lehetőséget az Azure-portál főmenüjében.

1. A keresőmezőbe írja be az "integráció" szót szűrőként. Az eredmények közül válassza ki ezt az erőforrást: **Integrációs fiókok**

1. Az **Integrációs fiókok csoportban**válassza ki azt az integrációs fiókot, amely rendelkezik a szerkesztendő szerződéssel.

1. A jobb oldali ablaktáblában **az Összetevők csoportban**válassza a **Megállapodások csempét.**

1. A **Megállapodások csoportban**válassza ki a szerződést, és válassza a **Szerkesztés**lehetőséget.

1. Hajtsa végre, majd mentse a módosításokat.

## <a name="delete-agreements"></a>Megállapodások törlése

1. Az [Azure Portalon](https://portal.azure.com)válassza az Összes **szolgáltatás**lehetőséget az Azure-portál főmenüjében.

1. A keresőmezőbe írja be az "integráció" szót szűrőként. Az eredmények közül válassza ki ezt az erőforrást: **Integrációs fiókok**

1. Az **Integrációs fiókok csoportban**válassza ki azt az integrációs fiókot, amely rendelkezik a törölni kívánt szerződéssel.

1. A jobb oldali ablaktáblában **az Összetevők csoportban**válassza a **Megállapodások csempét.**

1. A **Megállapodások csoportban**válassza ki a szerződést, és válassza a **Törlés**lehetőséget.

1. Erősítse meg, hogy törölni szeretné a kijelölt szerződést.

## <a name="next-steps"></a>További lépések

* [AS2-üzenetek váltása](logic-apps-enterprise-integration-as2.md)
* [EDIFACT-üzenetek váltása](logic-apps-enterprise-integration-edifact.md)
* [X12-üzenetek váltása](logic-apps-enterprise-integration-x12.md)
