---
title: Helyszíni adatforrások elérése a Azure Logic Appsból | Microsoft Docs
description: Helyszíni adatforrásokhoz való kapcsolódás a logikai alkalmazásokból helyszíni adatátjáró létrehozásával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 029dc8daaf456c155d46eefa699772882bdabee5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982869"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Helyi adatforrásokhoz való kapcsolódás Azure Logic Apps

Ha a logikai alkalmazásokból szeretné elérni a helyszíni adatforrásokat, hozzon létre egy helyszíni adatátjáró-erőforrást a Azure Portal. A logikai alkalmazások ezután használhatják a helyszíni [összekötőket](../logic-apps/logic-apps-gateway-install.md#supported-connections). Ez a cikk bemutatja, hogyan hozhatja létre az Azure Gateway-erőforrást, *miután* [letöltötte és telepítette az átjárót a helyi számítógépen](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> Az Azure-beli virtuális hálózatokhoz való csatlakozáshoz érdemes inkább [*integrációs szolgáltatási környezetet*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) létrehozni. 

Az átjáró más szolgáltatásokkal való használatáról a következő cikkekben talál további információt:

* [Microsoft Power BI helyszíni adatátjáró](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Helyszíni adatátjáró Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Helyszíni adatátjáró Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Helyszíni adatátjáró Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Előfeltételek

* Már letöltötte [és telepítette az adatátjárót egy helyi számítógépen](../logic-apps/logic-apps-gateway-install.md).

* Az átjáró telepítése még nincs társítva az Azure-beli átjáró-erőforrásokhoz. Az átjárót csak egy átjáró-erőforráshoz csatolhatja, amely az átjáró erőforrásának létrehozásakor és az átjáró telepítésének kiválasztásakor történik. Ez a hivatkozás lehetővé teszi, hogy az átjáró telepítése ne legyen elérhető más erőforrásokhoz.

* Amikor bejelentkezik a Azure Portalba, és létrehozza az átjáró erőforrását, ügyeljen arra, hogy ugyanazt a bejelentkezési fiókot használja, amelyet korábban a helyszíni [adatátjáró telepítéséhez](../logic-apps/logic-apps-gateway-install.md#requirements) használt, és ugyanazzal az [Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) -előfizetéssel, amelyet a átjáró. Ha még nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure</a>-fiókra.

* Ha az átjáró erőforrását a Azure Portal szeretné létrehozni és karbantartani, a [Windows-szolgáltatásfiók](../logic-apps/logic-apps-gateway-install.md#windows-service-account) legalább **közreműködői** engedélyekkel kell rendelkeznie. A helyszíni adatátjáró Windows-szolgáltatásként fut, és a Windows-szolgáltatás bejelentkezési hitelesítő adatainak használatára `NT SERVICE\PBIEgwService` van beállítva. 

  > [!NOTE]
  > A Windows-szolgáltatásfiók eltér a helyszíni adatforrásokhoz való csatlakozáshoz használt fióktól és a Cloud Services szolgáltatásba való bejelentkezéshez használt Azure munkahelyi vagy iskolai fióktól.

## <a name="download-and-install-gateway"></a>Átjáró letöltése és telepítése

A cikk lépéseinek folytatásához ellenőrizze, hogy az átjáró már telepítve van-e a helyi számítógépen.
Ha még nem tette meg, kövesse a helyszíni [adatátjáró letöltésére és telepítésére](../logic-apps/logic-apps-gateway-install.md)vonatkozó lépéseket. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Azure-erőforrás létrehozása az átjáróhoz

Miután a helyi számítógépre telepítette az átjárót, létrehozhat egy Azure-erőforrást az átjáróhoz. Ez a lépés az átjáró erőforrását is társítja az Azure-előfizetésével.

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>. Ügyeljen arra, hogy ugyanazt az Azure munkahelyi vagy iskolai e-mail-címet használja, amelyet az átjáró telepítéséhez használ.

2. Az Azure fő menüjében válassza > az **erőforrás** > 
létrehozása helyszíni adatátjáró lehetőséget.

   !["Helyszíni adatátjáró" keresése](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. A **Csatlakozáskezelő-átjáró létrehozása** lapon adja meg az átjáró erőforrásának adatait:

   | Tulajdonság | Leírás | 
   |----------|-------------|
   | **Erőforrás neve** | Az átjáró erőforrásának neve, amely`-`csak betűket, számokat, kötőjeleket (), aláhúzásokat (`_`), zárójeleket (`(`, `)`) és pontokat (`.`) tartalmazhat. | 
   | **Előfizetés** | Az Azure-előfizetés neve, amelynek a logikai alkalmazással megegyező előfizetésnek kell lennie. Az alapértelmezett előfizetés a bejelentkezéshez használt Azure-fiókon alapul. | 
   | **Erőforráscsoport** | Az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) neve a kapcsolódó erőforrások rendszerezéséhez | 
   | **Location** | Az Azure ezt a helyet arra a régióra korlátozza, amelyet az átjáró Cloud Service-hez választott az [átjáró telepítésekor](../logic-apps/logic-apps-gateway-install.md). <p>**Megjegyzés**: Győződjön meg arról, hogy az átjáró-erőforrás helye megegyezik az átjáró Cloud Service-helyével. Ellenkező esetben előfordulhat, hogy az átjáró telepítése nem jelenik meg a telepített átjárók listájában a következő lépésben való kiválasztáshoz. Az átjáró-erőforráshoz és a logikai alkalmazáshoz különböző régiókat használhat. | 
   | **Telepítési név** | Ha az átjáró telepítése még nincs kiválasztva, válassza ki a korábban telepített átjárót. | 
   | | | 

   Például:

   ![A helyszíni adatátjáró létrehozásához szükséges adatok megadása](./media/logic-apps-gateway-connection/createblade.png)

4. Ha az átjáró-erőforrást az Azure-irányítópulton szeretné felvenni, válassza a **rögzítés az irányítópulton**lehetőséget. Ha elkészült, kattintson a **Létrehozás** gombra.

   Ha bármikor szeretné megkeresni vagy megtekinteni az átjárót, az Azure főmenüjében válassza a **minden szolgáltatás**lehetőséget. 
   A keresőmezőbe írja be a "helyszíni adatátjárók" kifejezést, majd válassza **a**helyszíni adatátjárók lehetőséget.

   !["Helyszíni adatátjárók" keresése](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Csatlakozás helyszíni adatokhoz

Miután létrehozta az átjáró-erőforrást, és társítja az Azure-előfizetést ehhez az erőforráshoz, mostantól létrehozhat egy kapcsolatot a logikai alkalmazás és a helyszíni adatforrás között az átjáró használatával.

1. A Azure Portal hozza létre vagy nyissa meg a logikai alkalmazást a Logic app Designerben.

2. Adjon hozzá egy olyan összekötőt, amely támogatja a helyszíni kapcsolatokat, például **SQL Server**.

3. Most állítsa be a-kapcsolatokat:

   1. A helyszíni adatátjárón **keresztül válassza a kapcsolat**lehetőséget. 

   2. **Átjárók**esetében válassza ki a korábban létrehozott átjáró-erőforrást. 

      Annak ellenére, hogy az átjáró-kapcsolatok helye a logikai alkalmazással megegyező régióban található, kijelölhet egy átjárót egy másik régióban.

   3. Adjon meg egyedi nevet és a többi szükséges információt. 

      Az egyedi kapcsolat neve megkönnyíti a kapcsolat egyszerű azonosítását, különösen akkor, ha több kapcsolatot hoz létre. Ha szükséges, adja meg a felhasználónévhez tartozó minősített tartományt is.
   
      Például:

      ![Kapcsolat létrehozása a logikai alkalmazás és az adatátjáró között](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Ha elkészült, kattintson a **Létrehozás** gombra. 

Az átjáró-kapcsolatok most már készen állnak a logikai alkalmazás használatára.

## <a name="edit-connection"></a>Kapcsolatok szerkesztése

Miután létrehozta az átjáró-kapcsolatokat a logikai alkalmazáshoz, érdemes később frissítenie az adott kapcsolódás beállításait.

1. Átjáró-kapcsolatok keresése:

   * A logikai alkalmazás összes API-kapcsolatának megkereséséhez a logikai alkalmazás menüjében, a **fejlesztői eszközök**területen válassza az **API-kapcsolatok**elemet. 
   
     ![Lépjen a logikai alkalmazáshoz, és válassza az "API-kapcsolatok" lehetőséget.](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Az Azure-előfizetéshez társított összes API-kapcsolat megkeresése: 

     * Az Azure fő menüjében lépjen a **minden szolgáltatás** > **webes** > **API-kapcsolatok**menüpontra. 
     * Vagy az Azure főmenüjében válassza az **összes erőforrás**lehetőséget.

2. Válassza ki a kívánt átjáró-kapcsolatokat, majd válassza az **API-kapcsolatok szerkesztése**lehetőséget.

   > [!TIP]
   > Ha a frissítések nem lépnek érvénybe, próbálkozzon [az átjáró Windows-szolgáltatásának leállításával és](./logic-apps-gateway-install.md#restart-gateway)újraindításával.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Átjáró erőforrásának törlése

Egy másik átjáró-erőforrás létrehozásához társítsa az átjárót egy másik erőforrással, vagy távolítsa el az átjáró erőforrását anélkül, hogy az átjáró telepítését befolyásolná. 

1. Az Azure főmenüjében lépjen a **minden erőforrás**elemre. 

2. Keresse meg és válassza ki az átjáró erőforrását.

3. Ha még nincs bejelölve, az átjáró erőforrás menüjében válassza **a**helyszíni adatátjáró lehetőséget. 

4. Az erőforrás eszköztárán válassza a **Törlés**lehetőséget.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [A logikai alkalmazások védelme](./logic-apps-securing-a-logic-app.md)
* [Gyakori példák és forgatókönyvek logikai alkalmazásokhoz](./logic-apps-examples-and-scenarios.md)
