---
title: Integrációs fiókok B2B megoldások - Azure Logic Apps alkalmazások létrehozása és kezelése |} Microsoft Docs
description: Hozzon létre hivatkozásra, helyezze át, és vállalati integrációs és az Azure Logic Apps B2B megoldások integrációs fiókok törlése
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 2a1fe501386884e02657d4b6cbef58ffc533fa33
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297988"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>A logic apps B2B megoldások integrációs fiókok létrehozása és kezelése

Elkészítése előtt [vállalati integrációs és B2B megoldások](../logic-apps/logic-apps-enterprise-integration-overview.md) rendelkező [Azure Logic Apps](../logic-apps/logic-apps-overview.md), integrációs fiókot, ahol létrehozása, tárolja, és kezelheti B2B összetevők, például a kell mivel a partnerek, egyezmények, maps, sémák, tanúsítványok és stb. A Logic Apps alkalmazást az integráció fiókban az összetevők dolgozni, és használja a Logic Apps B2B összekötők, például az XML-érvényesítés előtt [a integrációs fiókját](#link-account) a logikai alkalmazáshoz. Összekapcsolhatja őket, rendelkeznie kell mind a integrációs fiók és a logikai alkalmazást a *azonos* Azure-beli hely, vagy a régiót.

Ez a cikk bemutatja, hogyan ezen feladatok végrehajtásával:

* A integrációs-fiók létrehozásához.
* Az integráció fiók összekötése a logikai alkalmazást.
* Az integráció fiók áthelyezése egy másik Azure erőforráscsoportba vagy előfizetésbe.
* Az integráció fiók törlése.

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a> az Azure-fiókja hitelesítő adataival.

## <a name="create-integration-account"></a>integráció-fiók létrehozása

1. Válassza a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be az "integrációs fiókok" szűrőként, és válassza ki **integrációs fiókok**.

   ![Integrációs fiókok keresése](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. A **integrációs fiókok**, válassza a **Hozzáadás**.

   ![Válassza ki a "Hozzáadás" integrációs-fiók létrehozása](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Adja meg az integrációs fiókkal kapcsolatos információk: 

   ![Részletek a integrációs fiók megadása](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Tulajdonság | Szükséges | Példaérték | Leírás | 
   |----------|----------|---------------|-------------|
   | Name (Név) | Igen | teszt-integráció-fiók | Az integráció fiók nevét. Ebben a példában a megadott nevet használja. | 
   | Előfizetés | Igen | <*Azure-előfizetés-neve*> | A használatára az Azure-előfizetés nevét | 
   | Erőforráscsoport | Igen | teszt-integráció-fiók-rg | A nevet a [Azure erőforráscsoport](../azure-resource-manager/resource-group-overview.md) kapcsolódó erőforrások rendszerezéséhez használja. Ehhez a példához hozzon létre egy új erőforráscsoportot a megadott névvel. | 
   | Tarifacsomag | Igen | Ingyenes | Ez a tarifacsomag használni kívánt. Ehhez a példához válassza ki a **szabad**, de további információkért lásd: [Logic Apps korlátozásai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md) és [Logic Apps árképzési](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Hely | Igen | USA nyugati régiója | A régió integrációs fiókadatait tárolási helyét. Jelölje ki a logikai alkalmazás ugyanazon a helyen vagy logikai alkalmazás létrehozása ugyanazon a helyen, a integrációs fiókkal. | 
   | Log Analytics | Nem | Ki | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. | 
   ||||| 

4. Mikor végzett, válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

   Miután Azure integrációs fiókja telepíti a kiválasztott helyre, amely általában egy percen belül befejeződik, Azure megnyitja integrációs fiókját.

   ![Azure integrációs fiókja megnyitása](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Most a logikai alkalmazás integrációs fiókját is használhatja, kell kapcsolni az integráció fiók a Logic Apps alkalmazást.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Logikai alkalmazás összekapcsolása

A a logic apps hozzáférésének integrációs-fiókkal, amely tartalmazza a B2B összetevők, például kereskedelmi partnerek, egyezmények, leképezések és sémák, integrációs fiókját kell kapcsolni a Logic Apps alkalmazást. 

> [!NOTE]
> Az integráció fiók és a logikai alkalmazás ugyanabban a régióban léteznie kell.

1. Az Azure portálon található, és nyissa meg a Logic Apps alkalmazást.

2. A logikai alkalmazás menü alatti **beállítások**, jelölje be **munkafolyamat-beállításokat**. Az a **válassza ki a integrációs fiókot** listára, válassza ki a integrációs fiók csatolása a Logic Apps alkalmazást.

   ![Válassza ki a integrációs fiókját](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Linking befejezéséhez válassza ki a **mentése**.

   ![Válassza ki a integrációs fiókját](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Ha integrációs fiókja sikeresen kapcsolódik, Azure egy megerősítő üzenetet jelenít meg. 

   ![Azure megerősíti, hogy sikeres hivatkozás](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Most a Logic Apps alkalmazást használható minden olyan és az összes az összetevők integrációs fiókját, valamint a B2B összekötők, például az XML-érvényesítés és egybesimított fájl kódolási vagy -dekódolási.  

## <a name="unlink-from-logic-app"></a>Leválasztás a logikai alkalmazás

A Logic Apps alkalmazást egy másik integrációs fiókkal összekapcsolni, vagy már nem integrációs fiók használata a Logic Apps alkalmazást, törölheti a hivatkozás az Azure erőforrás-kezelőben.

1. A böngészőben nyissa meg a <a href="https://resources.azure.com" target="_blank">Azure Resource Explorer (https://resources.azure.com)</a>. Győződjön meg arról, hogy be van jelentkezve az azonos Azure hitelesítő adataival.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. A keresési mezőbe írja be a logikai alkalmazás nevét, majd keresse meg és válassza ki a logikai alkalmazás.

   ![Keresse meg és jelölje ki a logikai alkalmazás](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. A explorer címsorában válassza **olvasási/írási**.

   ![Kapcsolja be a "Olvasási/írási" módban](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Az a **adatok** lapra, majd **szerkesztése**.

   ![A "Data" lapon válassza a "Szerkesztés"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. -Szerkesztőben keresse a `integrationAccount` tulajdonság az integráció a fiókot, és törölni, hogy tulajdonságot, amelynek ezt a formátumot:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Példa:

   ![Található "integrationAccount" tulajdonság meghatározása](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Az a **adatok** lapra, majd **Put** menti a módosításokat. 

   ![Válassza a "Put" menti a módosításokat](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. Az Azure portál, a logikai alkalmazás **munkafolyamat-beállításokat**, ellenőrizze, hogy a **integrációs fiók** tulajdonság megjelenik üres.

   ![Ellenőrizze, hogy integrációs fiók nem rendelkezik kapcsolással](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Integráció fiók áthelyezése

Integráció fiókja áthelyezheti egy másik Azure-előfizetés vagy az erőforrás-csoportok.

1. Válassza ki a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be az "integrációs fiókok" szűrőként, és válassza ki **integrációs fiókok**.

   ![Az integráció fiók](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. A **integrációs fiókok**, válassza ki a integrációs fiókot, amely át szeretné helyezni. Integráció fiókot menü alatti **beállítások**, válassza a **tulajdonságok**.

   ![A "Beállítások" válassza a "Tulajdonságok"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Módosítja az Azure erőforráscsoport vagy előfizetés integrációs fiókjához.

   ![Válassza a "Módosítás erőforráscsoport" vagy "Módosítása előfizetés"](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Amikor elkészült, győződjön meg arról, hogy frissíti minden parancsfájlok az új erőforrás-azonosítók esetében az összetevők.  

## <a name="delete-integration-account"></a>Integráció fiók törlése

1. Válassza ki a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be az "integrációs fiókok" szűrőként, és válassza ki **integrációs fiókok**.

   ![Az integráció fiók](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. A **integrációs fiókok**, válassza ki a törölni kívánt integrációs fiókot. Az integráció felhasználóifiók-menüjéből válassza **áttekintése**, majd válassza a **törlése**. 

   ![Válassza ki az integráció fiókot. A "Overview" lapon válassza a "Delete"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Győződjön meg arról, hogy szeretné-e az integráció fiók törlése, válassza a **Igen**.

   !["Yes" Válasszon törlésének megerősítése](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>További lépések

* [Kereskedelmi partnereknek létrehozása](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Hozza létre az egyezményeket](../logic-apps/logic-apps-enterprise-integration-agreements.md)
