---
title: B2B integrációs fiókok létrehozása és kezelése
description: Integrációs fiókok létrehozása, csatolása és kezelése az Azure Logic Apps alkalmazásokkal való vállalati integrációhoz
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270328"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Integrációs fiókok létrehozása és kezelése a B2B vállalati integrációkhoz az Azure Logic Apps alkalmazásban

Mielőtt [vállalati integrációs és B2B-megoldásokat](../logic-apps/logic-apps-enterprise-integration-overview.md) hozhatna létre az [Azure Logic Appsszel](../logic-apps/logic-apps-overview.md), létre kell hoznia egy integrációs fiókot, amely egy különálló Azure-erőforrás, és biztonságos, skálázható, felügyelhető tárolót biztosít a logikaialkalmazás-munkafolyamatokkal meghatározott és használt integrációs összetevők számára.

Létrehozhat, tárolhat és kezelhet például B2B-összetevőket, például kereskedelmi partnereket, megállapodásokat, térképeket, sémákat, tanúsítványokat és kötegelt konfigurációkat. Emellett mielőtt a logikai alkalmazás együtt dolgozna ezekkel az összetevőkkel, és a Logic Apps B2B-összekötők használatával, [csatolnia](#link-account) kell az integrációs fiók a logikai alkalmazáshoz. Az integrációs fióknak és a logikai alkalmazásnak *is ugyanazon* a helyen vagy régióban kell léteznie.

> [!TIP]
> Ha integrációs fiókot szeretne létrehozni [integrációs szolgáltatási környezetben,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)olvassa el az [Integrációs fiókok létrehozása ISE-ben című témakört.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

Ez a témakör bemutatja, hogyan hajthatja végre ezeket a feladatokat:

* Hozza létre integrációs fiókját.
* Az integrációs fiók összekapcsolása egy logikai alkalmazással.
* Módosítsa az integrációs fiók tarifacsomagját.
* Az integrációs fiók leválasztása egy logikai alkalmazásról.
* Helyezze át integrációs fiókját egy másik Azure-erőforráscsoportba vagy-előfizetésbe.
* Törölje az integrációs fiókot.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Integrációs fiók létrehozása

Ehhez a feladathoz használhatja az Azure Portalt az ebben a szakaszban, az [Azure PowerShellben](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)vagy az [Azure CLI-ben](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create)leírt lépéseket követve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. Az Azure főmenüjében válassza az **Erőforrás létrehozása** lehetőséget. A keresőmezőbe írja be szűrőként az "integrációs fiók" kifejezést, és válassza **az Integrációs fiók lehetőséget.**

   ![Új integrációs fiók létrehozása](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Az **Integrációs fiók csoportban**válassza a **Létrehozás lehetőséget.**

   ![Integrációs fiók létrehozásához válassza a "Hozzáadás" lehetőséget](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Adja meg az integrációs fiókkal kapcsolatos adatokat:

   ![Integrációs fiók részleteinek megadásáról](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Név** | Igen | <*integrációs fiók név*> | Az integrációs fiók neve, amely csak betűket,`-`számokat, kötőjeleket ( ), aláhúzást (`_`), zárójeleket (`(`, `)`és pont (`.`) tartalmazhat. Ez a példa a "Fabrikam-Integration" (Fabrikam-Integration) kifejezést használja. |
   | **Előfizetés** | Igen | <*Azure-előfizetés-név*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | Igen | <*Azure-erőforrás-csoport név*> | A kapcsolódó erőforrások rendszerezéséhez használt [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) neve. Ebben a példában hozzon létre egy új erőforráscsoportot "FabrikamIntegration-RG" névvel. |
   | **Árképzési szint** | Igen | <*árképzési szint*> | Az integrációs fiók tarifacsomagja, amelyet később módosíthat. Ebben a példában válassza a **Szabad**lehetőséget. További információt az alábbi témakörökben talál: <p>- [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [A Logic Apps korlátai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [A Logic Apps árképzése](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Helyen** | Igen | <*Azure-régió*> | Az a régió, ahol az integrációs fiók metaadatait tárolni szeretné. Válassza ki ugyanazt a helyet, mint a logikai alkalmazás, vagy hozza létre a logikai alkalmazások ugyanazon a helyen, mint az integrációs fiók. Ebben a példában használja a "West USA" (USA nyugati és usa nyugati részére) kifejezést. <p>**Megjegyzés:** Integrációs fiók létrehozásához [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)válassza ki az ISE-t helyként. További információ: [Integrációs fiókok létrehozása ISE-ben](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)című témakörben talál. |
   | **Log Analytics** | Nem | Ki, Be | Tartsa meg a **Ki** beállítást ebben a példában. |
   |||||

1. Ha végzett, válassza a **Létrehozás gombot.**

   A központi telepítés befejezése után az Azure megnyitja az integrációs fiókot.

   ![Az Azure megnyitja az integrációs fiókot](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Mielőtt a logikai alkalmazás használhatja az integrációs fiók, kövesse a következő lépéseket az integrációs fiók és a logikai alkalmazás összekapcsolására.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Kapcsolódás a logikai alkalmazáshoz

Ahhoz, hogy a logikai alkalmazások hozzáférést egy integrációs fiók, amely tartalmazza a B2B-összetevők, először csatolnia kell az integrációs fiók a logikai alkalmazás. Mind a logikai alkalmazás, mind az integrációs fiók nak ugyanabban a régióban kell léteznie. A feladat végrehajtásához használhatja az Azure Portalon. Ha a Visual Studio-t használja, és a logikai alkalmazás egy [Azure Resource Group projektben](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)van, [a visual studio használatával összekapcsolhatja a logikai alkalmazást egy integrációs fiókkal.](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)

1. Az Azure Portalon keresse meg és nyissa meg a logikai alkalmazást.

1. Az [Azure Portalon](https://portal.azure.com)nyisson meg egy meglévő logikai alkalmazást, vagy hozzon létre egy új logikai alkalmazást.

1. A logikai alkalmazás menüjében, a **Beállítások**csoportban válassza a **Munkafolyamat-beállítások lehetőséget.** Az **Integrációs fiók csoportban**nyissa meg az **Integrációs fiók kiválasztása** listát. Válassza ki a logikai alkalmazáshoz csatolandó integrációs fiókot.

   ![Válassza ki integrációs fiókját](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. A csatolás befejezéséhez válassza a **Mentés gombot.**

   ![Válassza ki integrációs fiókját](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Az integrációs fiók sikeres összekötése után az Azure egy megerősítő üzenetet jelenít meg.

   ![Az Azure megerősíti a sikeres hivatkozást](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Most a logikai alkalmazás használhatja az összetevők az integrációs fiókban, valamint a B2B-összekötők, például az XML-érvényesítés és a fájl egyenvant, vagy dekódolás.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>A tarifacsomag módosítása

Az integrációs fiók [korlátainak](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) növeléséhez [magasabb tarifacsomagra](#upgrade-pricing-tier)frissíthet, ha elérhető. Például frissíthet az ingyenes szintről az alapszintű vagy standard szintre. Alacsonyabb szintre is [visszaválthat,](#downgrade-pricing-tier)ha van ilyen. További információ az árképzésről, lásd az alábbi témaköröket:

* [A Logic Apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Frissítési tarifacsomag

A módosítás végrehajtásához használhatja az Azure Portalt az ebben a szakaszban leírt lépéseket követve, vagy az [Azure CLI-t.](#upgrade-tier-azure-cli)

#### <a name="azure-portal"></a>Azure portál

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A fő Azure-keresőmezőbe írja be szűrőként az "integrációs fiókok" kifejezést, és válassza **az Integrációs fiókok lehetőséget.**

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Az Azure az Azure-előfizetések összes integrációs fiókját megjeleníti.

1. Az **Integrációs fiókok csoportban**válassza ki az áthelyezni kívánt integrációs fiókot. Az integrációs fiók menüben válassza **az Áttekintés**lehetőséget.

   ![Az integrációs fiók menüben válassza az "Áttekintés" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Az Áttekintő ablaktáblán válassza **a Frissítési tarifacsomag**lehetőséget, amely felsorolja az elérhető magasabb szinteket. Amikor kiválaszt egy szintet, a módosítás azonnal érvénybe lép.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. Ha még nem tette meg, [telepítse az Azure CLI előfeltételeit.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. Az Azure Portalon nyissa meg az Azure Cloud Shell-környezetet. [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Az Azure Cloud Shell megnyitása](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. A parancssorba írja be az [ **erőforrás** parancsot,](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)és állítsa `skuName` a kívánt magasabb szintre.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Ha például az Alapszintű szinttel `skuName` rendelkezik, a következőt állíthatja `Standard`be:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Visszalépési tarifacsomag

A módosításhoz használja az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Ha még nem tette meg, [telepítse az Azure CLI előfeltételeit.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. Az Azure Portalon nyissa meg az Azure Cloud Shell-környezetet. [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Az Azure Cloud Shell megnyitása](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. A parancssorban adja meg az az `skuName` [ **erőforrás** parancsot,](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) és állítsa a kívánt alsó szintre.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Ha például a Standard szinttel rendelkezik, a következőt állíthatja `skuName` `Basic`be:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Leválasztás a logikai alkalmazásról

Ha azt szeretné, hogy a logikai alkalmazás egy másik integrációs fiók, vagy már nem használja az integrációs fiókot a logikai alkalmazás, törölje a hivatkozást az Azure Resource Explorer használatával.

1. Nyissa meg a böngészőablakot, és nyissa meg az [Azure Resource Explorer ( .https://resources.azure.com)](https://resources.azure.com) Jelentkezzen be ugyanazzal az Azure-fiók hitelesítő adatokkal.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. A keresőmezőbe írja be a logikai alkalmazás nevét, hogy megtalálhassa és kiválaszthassa a logikai alkalmazást.

   ![Logikai alkalmazás keresése és kijelölése](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. A felfedező címsorán válassza az **Olvasás/írás**lehetőséget.

   ![Az "Olvasás/írás" mód bekapcsolása](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Az **Adatok** lapon válassza a **Szerkesztés**lehetőséget.

   ![Az "Adatok" lapon válassza a "Szerkesztés" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. A szerkesztőben keresse `integrationAccount` meg az objektumot, és törölje azt a tulajdonságot, amely ezt a formátumot rendelkezik:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Példa:

   ![Az "integrationAccount" objektum megkeresése](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Az **Adatok** lapon válassza az **Elhelyezés** a módosítások mentéséhez lehetőséget.

   ![A módosítások mentéséhez válassza a "Put" (Eltenni) lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Az Azure Portalon keresse meg és válassza ki a logikai alkalmazást. Az alkalmazás **munkafolyamat-beállításai**alatt ellenőrizze, hogy az **Integrációs fiók** tulajdonsága üresen jelenik-e meg.

   ![Annak ellenőrzése, hogy az integrációs fiók nincs-e csatolva](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Integrációs fiók áthelyezése

Az integrációs fiókot áthelyezheti egy másik Azure-erőforráscsoportba vagy Azure-előfizetésbe. Erőforrások áthelyezésekor az Azure új erőforrás-azonosítókat hoz létre, ezért győződjön meg arról, hogy az új azonosítók helyett, és frissíti az áthelyezett erőforrásokhoz társított parancsfájlok vagy eszközök. Ha módosítani szeretné az előfizetést, meg kell adnia egy meglévő vagy új erőforráscsoportot is.

Ehhez a feladathoz használhatja az Azure Portalt az ebben a szakaszban leírt lépések vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)használatával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A fő Azure-keresőmezőbe írja be szűrőként az "integrációs fiókok" kifejezést, és válassza **az Integrációs fiókok lehetőséget.**

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Az Azure az Azure-előfizetések összes integrációs fiókját megjeleníti.

1. Az **Integrációs fiókok csoportban**válassza ki az áthelyezni kívánt integrációs fiókot. Az integrációs fiók menüben válassza **az Áttekintés**lehetőséget.

   ![Az integrációs fiók menüben válassza az "Áttekintés" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Az **Erőforráscsoport** vagy **az Előfizetés neve csoport**mellett válassza a **Módosítás**lehetőséget.

   ![Erőforráscsoport vagy -előfizetés módosítása](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Jelölje ki az áthelyezni kívánt kapcsolódó erőforrásokat.

1. A választás alapján az alábbi lépésekkel módosíthatja az erőforráscsoportot vagy az előfizetést:

   * Erőforráscsoport: Az **Erőforráscsoport** csoport listában válassza ki a cél erőforráscsoportot. Másik lehetőségként másik erőforráscsoport létrehozásához válassza **az Új erőforráscsoport létrehozása**lehetőséget.

   * Előfizetés: Az **Előfizetés** listából válassza ki a cél-előfizetést. Az **Erőforráscsoport** listában válassza ki a cél erőforráscsoportot. Másik lehetőségként másik erőforráscsoport létrehozásához válassza **az Új erőforráscsoport létrehozása**lehetőséget.

1. Ha nyugtázni szeretné, hogy az áthelyezett erőforrásokhoz társított parancsfájlok vagy eszközök nem fognak működni, amíg nem frissíti őket az új erőforrásazonosítókkal, jelölje be a megerősítést kérő párbeszédpanelt, majd kattintson az **OK gombra.**

1. Miután befejezte, győződjön meg arról, hogy frissíti az összes és az összes parancsfájlt az áthelyezett erőforrások új erőforrás-azonosítóival.  

## <a name="delete-integration-account"></a>Integrációs fiók törlése

Ehhez a feladathoz használhatja az Azure Portalt az ebben a szakaszban, az [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)vagy az [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount)lépéseit követve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A fő Azure-keresőmezőbe írja be szűrőként az "integrációs fiókok" kifejezést, és válassza **az Integrációs fiókok lehetőséget.**

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Az Azure az Azure-előfizetések összes integrációs fiókját megjeleníti.

1. Az **Integrációs fiókok csoportban**válassza ki a törölni kívánt integrációs fiókot. Az integrációs fiók menüben válassza **az Áttekintés**lehetőséget.

   ![Az integrációs fiók menüben válassza az "Áttekintés" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Az Áttekintő ablaktáblán válassza a **Törlés**lehetőséget.

   ![Az "Áttekintés" ablaktáblán válassza a "Törlés" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Az integrációs fiók törlésének megerősítéséhez válassza az **Igen**lehetőséget.

   ![A törlés megerősítéséhez válassza az "Igen" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>További lépések

* [Kereskedelmi partnerek létrehozása integrációs számláján](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Megállapodások létrehozása partnerek között az integrációs fiókban](../logic-apps/logic-apps-enterprise-integration-agreements.md)
