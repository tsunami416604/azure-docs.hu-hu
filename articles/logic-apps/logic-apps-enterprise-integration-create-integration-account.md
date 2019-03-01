---
title: B2B-megoldások – Azure Logic Apps integrációs fiókok létrehozása és kezelése |} A Microsoft Docs
description: Hozzon létre, hivatkozásra, áthelyezése és törlése a vállalati integráció és az Azure Logic Apps B2B-megoldásoktól integrációs fiókok
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
ms.openlocfilehash: 43ecdafac4f0a5cdc9e619537cdbe2a42ff7fe1b
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191918"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>A logic apps B2B-megoldásoktól az integrációs fiókok létrehozása és kezelése

Elkészítése előtt [vállalati Integrációk és B2B-megoldásoktól](../logic-apps/logic-apps-enterprise-integration-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-overview.md), előbb rendelkeznie kell egy integrációs fiók, amely, hozzon létre, tárolhatja és kezelheti a B2B összetevők, például kereskedelmi partnerek, szerződések, térképeket, sémákat, tanúsítványok, és így tovább. A logikai alkalmazás az összetevőket az integrációs fiókban lévő dolgozhat, és a Logic Apps B2B-összekötőkkel, például XML-érvényesítés előtt [az integrációs fiókot](#link-account) a logikai alkalmazáshoz. Összekapcsolhatja őket, mind az integrációs fiók és a logikai alkalmazás kell rendelkeznie a *ugyanazon* Azure-beli hely vagy régió.

Ez a cikk bemutatja, hogyan hajthat végre ezeket a feladatokat:

* Az integrációs fiók létrehozása.
* Az integrációs fiók csatolása egy logikai alkalmazást.
* Az integrációs fiók áthelyezése az Azure-bA egy másik erőforráscsoportba vagy előfizetésbe.
* Integrációs fiók törlése.

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a> az Azure-fiókja hitelesítő adataival.

## <a name="create-integration-account"></a>integrációs fiók létrehozása

1. Az Azure fő menüjéből válassza **minden szolgáltatás**. A Keresés mezőbe írja be az "integrációs fiók" szűrőként, és válassza ki **integrációs fiókok**.

   ![Keresse meg az integrációs fiókok](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. A **integrációs fiókok**, válassza a **Hozzáadás**.

   ![Válassza a "Hozzáadás" integrációs fiók létrehozása](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Az integrációs fiók kapcsolatos adatok megadása: 

   ![Adja meg adatait az integrációs fiók](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Tulajdonság | Szükséges | Példaérték | Leírás | 
   |----------|----------|---------------|-------------|
   | Name (Név) | Igen | test-integration-account | Az integrációs fiók neve. Ebben a példában a megadott nevet használja. | 
   | Előfizetés | Igen | <*Azure-előfizetés-neve*> | Neve az Azure-előfizetés használata | 
   | Erőforráscsoport | Igen | test-integration-account-rg | A nevet a [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) kapcsolódó erőforrások rendezéséhez használt. Ebben a példában a megadott nevű hozzon létre egy új erőforráscsoportot. | 
   | Tarifacsomag | Igen | Ingyenes | A tarifacsomag, amelyet használni szeretne. Ebben a példában válassza **ingyenes**, de további információkért lásd: [Logic Apps-korlátozások és konfiguráció](../logic-apps/logic-apps-limits-and-config.md) és [Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Hely | Igen | USA nyugati régiója | A régió az integrációs fiók információk tárolására. Válassza ki ugyanazt a helyet, a logikai alkalmazás, vagy hozzon létre egy logikai alkalmazást az integrációs fiók ugyanazon a helyen. | 
   | Log Analytics-munkaterület | Nem | Ki | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. | 
   ||||| 

4. Mikor végzett, válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

   Miután az Azure üzembe helyezte az integrációs fiók a kijelölt helyre, amely általában egy percen belül befejeződik, az Azure megnyitja az integrációs fiók.

   ![Az Azure megnyitja az integrációs fiók](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Most ahhoz a logikai alkalmazást, az integrációs fiók, hozzá kell rendelnie az integrációs fiók a logikai alkalmazáshoz.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Logikai alkalmazás összekapcsolása

Egy integrációs fiók, amely tartalmazza a B2B összetevők, például a kereskedelmi partnerek, szerződések, leképezések és sémák, a logic apps hozzáférést biztosíthat az integrációs fiók kell rendelnie a logikai alkalmazást. 

> [!NOTE]
> Az integrációs fiók és a logikai alkalmazás ugyanabban a régióban léteznie kell.

1. Az Azure Portalon keresse meg és nyissa meg a logikai alkalmazást.

2. A logikai alkalmazás menüjében alatt **beállítások**válassza **munkafolyamat-beállítások**. Az a **válasszon egy integrációs fiókot** listájához, válassza ki a logikai alkalmazáshoz csatolni az integrációs fiókba.

   ![Válassza ki az integrációs fiók](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Csatolás a befejezéshez válassza **mentése**.

   ![Válassza ki az integrációs fiók](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Az integrációs fiók sikeresen össze van kapcsolva, az Azure egy megerősítő üzenetet jelenít meg. 

   ![Azure megerősíti, hogy a sikeres kapcsolat](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

A logikai alkalmazás most már használhatja az bármely és minden az összetevőket az integrációs fiók és a B2B-összekötők, például XML-érvényesítés és a kódolási vagy -dekódolási egybesimított fájl.  

## <a name="unlink-from-logic-app"></a>Logikai alkalmazás leválasztása

A logikai alkalmazás összekapcsolása egy másik integrációs fiók, vagy már nem használja a logikai alkalmazás egy integrációs fiókban, törölheti a hivatkozást az Azure erőforrás-kezelőben.

1. A böngészőben nyissa meg <a href="https://resources.azure.com" target="_blank">Azure erőforrás-kezelő (https://resources.azure.com)</a>. Győződjön meg arról, hogy van-e bejelentkezve az azonos Azure hitelesítő adatait.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. A keresőmezőbe adja meg a logikai alkalmazás nevét, majd keresse meg és válassza ki a logikai alkalmazást.

   ![Keresse meg és válassza ki a logikai alkalmazás](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. Explorer címsorában válassza **olvasási/írási**.

   !["Olvasási/írási" mód bekapcsolása](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Az a **adatok** lapra, majd **szerkesztése**.

   ![A "Data" lapon válassza a "Szerkesztés"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. A szerkesztőben keresse meg a `integrationAccount` tulajdonsága az integrációs fiókot, és törölje az adott tulajdonságot használja, amely ebben a formátumban van:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Példa:

   ![Keresse meg az "integrationAccount" tulajdonság meghatározása](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Az a **adatok** lapra, majd **Put** a módosítások mentéséhez. 

   ![Válassza a "Put" a módosítások mentése](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. Az Azure Portalon, a logikai alkalmazás alatt **munkafolyamat-beállítások**, ellenőrizze, hogy a **integrációs fiók** tulajdonság jelenik meg, üres.

   ![Ellenőrizze, hogy az integrációs fiók nem kapcsolódik](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Integrációs fiók áthelyezése

Az integrációs fiók áthelyezheti egy másik Azure vagy erőforráscsoportonként csoportba.

1. Az Azure fő menüjéből válassza **minden szolgáltatás**. A Keresés mezőbe írja be az "integrációs fiók" szűrőként, és válassza ki **integrációs fiókok**.

   ![Keresse meg az integrációs fiók](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. A **integrációs fiókok**, válassza ki az áthelyezni kívánt integrációs fiókba. A az integrációs fiók menü alatt **beállítások**, válassza a **tulajdonságok**.

   ![A "Beállítások" területen válassza a "Tulajdonságok"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Az Azure-erőforráscsoportot vagy az előfizetés az integrációs fiók módosítása

   ![Válassza a "Change erőforráscsoport" vagy "Change előfizetés"](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Ha elkészült, győződjön meg arról, hogy frissítenie minden szkripteket az új erőforrás-azonosítókat az összetevők.  

## <a name="delete-integration-account"></a>Integrációs fiók törlése

1. Az Azure fő menüjéből válassza **minden szolgáltatás**. A Keresés mezőbe írja be az "integrációs fiók" szűrőként, és válassza ki **integrációs fiókok**.

   ![Keresse meg az integrációs fiók](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. A **integrációs fiókok**, válassza ki a törölni kívánt integrációs fiókot. Az integrációs fiók menüben válassza **áttekintése**, majd válassza a **törlése**. 

   ![Válassza ki az integrációs fiókban. A "Áttekintés" lapon válassza a "Delete"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Győződjön meg arról, hogy szeretné-e az integrációs fiók törlése, válassza a **Igen**.

   ![A törlés jóváhagyásához, válassza az "Igen"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>További lépések

* [Kereskedelmi partnerek létrehozása](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Szerződések létrehozása](../logic-apps/logic-apps-enterprise-integration-agreements.md)
