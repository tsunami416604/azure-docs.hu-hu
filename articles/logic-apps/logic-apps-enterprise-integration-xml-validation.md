---
title: XML ellenőrzése a B2B vállalati integrációhoz
description: XML ellenőrzése sémák használatával az Azure Logic Apps vállalati integrációs csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792164"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Xml ellenőrzése a B2B vállalati integrációhoz az Azure Logic Apps alkalmazásban a vállalati integrációs csomaggal

A B2B-forgatókönyvekben gyakran a megállapodásban részt vehető kereskedelmi partnereknek meg kell győződniük arról, hogy az általuk kicserélt üzenetek érvényesek, mielőtt bármilyen adatkezelés megkezdődhetne. A dokumentumokat egy előre definiált séma alapján érvényesítheti az XML-érvényesítési művelettel, amely a vállalati integrációs csomaggal érhető el.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* Üres vagy meglévő logikai alkalmazás, ahol az XML-érvényesítési műveletet szeretné használni. Ha most kezdi meg a logikai alkalmazásokat, tekintse át [az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Rövid útmutató: Az első logikai alkalmazás létrehozása című ismertetése című ismertetése című ismertetése. [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az Azure-előfizetéshez társított [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) kapcsolódik ahhoz a logikai alkalmazáshoz, amelyen az XML-érvényesítési műveletet használni kívánja, és tartalmazza az XML-tartalom érvényesítéséhez használni kívánt sémát. Mind a logikai alkalmazás és az integrációs fiók léteznie kell ugyanazon a helyen vagy az Azure-régióban.

## <a name="add-xml-validation-action"></a>XML-érvényesítési művelet hozzáadása

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

1. Ha egy üres logikai alkalmazás, a Logic App Designer, `HTTP request` a keresőmezőbe, írja be a szűrőt, és válassza a **HTTP-kérelem fogadása** kor. Ellenkező esetben folytassa a következő lépéssel.

1. A munkafolyamat utolsó lépése alatt válassza az **Új lépés lehetőséget.**

   Ha meglévő lépések közé szeretne műveletet hozzáadni, vigye az egérmutatót a**+** lépéseket összekötő nyíl fölé, hogy a pluszjel ( ) megjelenjen. Jelölje ki a pluszjelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A **Művelet kiválasztása** **csoportban**válassza a Beépített lehetőséget. A keresőmezőbe írja `xml validation` be szűrőként. A műveletek listájában válassza az **XML-érvényesítés**lehetőséget.

   ![Az "XML-érvényesítés" művelet megkeresése és kijelölése](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Az ellenőrizni kívánt XML-tartalom megadásához kattintson a **Tartalom** mezőben, hogy megjelenjen a dinamikus tartalomlista.

   ![Dinamikus tartalomlista megnyitása](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   A dinamikus tartalomlista olyan tulajdonságtokeneket jelenít meg, amelyek a munkafolyamat előző lépéseinek kimeneteit jelölik. Ha a listában nem látható a várt tulajdonság, ellenőrizze az eseményindító vagy a műveletcímsort, hogy választhat-e **a Tovább**lehetőséget.

1. A dinamikus tartalomlistában válassza ki azt a tulajdonságot, amely az ellenőrizni kívánt tartalmat tartalmazza.

   Ebben a példában kiválasztja a **Törzs** kimenetet az eseményindítóból.

   ![Az ellenőrizve kívánt tartalom kiválasztása](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Az ellenőrzéshez használni kívánt séma megadásához nyissa meg a **Sémanév** listát, és válassza ki a csatolt integrációs fiókhoz hozzáadott érvényesítési sémát.

   ![Az ellenőrzéshez használandó séma kiválasztása](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Mentse a logikai alkalmazást.

   Most már befejezte az érvényesítés beállítását. Egy valós alkalmazásban előfordulhat, hogy az érvényesített adatokat egy üzletági (LOB) alkalmazásban, például a SalesForce-ban szeretné tárolni. Ha el szeretné küldeni az érvényesített kimenetet a Salesforce-nak, adjon hozzá egy műveletet.

1. Az érvényesítési művelet teszteléséhez küldjön egy kérelmet a logikai alkalmazás munkafolyamatának elindításához.

## <a name="next-steps"></a>További lépések

* További információ az [Enterprise Integration Pack csomagról](../logic-apps/logic-apps-enterprise-integration-overview.md)