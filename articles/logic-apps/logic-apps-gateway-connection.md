---
title: Adatforrások elérése a helyszínen Azure Logic Apps
description: Helyszíni adatforrásokhoz való kapcsolódás a logikai alkalmazásokból helyszíni adatátjáró létrehozásával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: ed2ba70b803940700044e900a1b2bb6607c0f051
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934070"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Helyi adatforrásokhoz való kapcsolódás Azure Logic Apps

Ha a logikai alkalmazásokból szeretné elérni a helyszíni adatforrásokat, hozzon létre egy helyszíni adatátjáró-erőforrást a Azure Portal. A logikai alkalmazások ezután használhatják a helyszíni [összekötőket](../connectors/apis-list.md#on-premises-connectors). Ez a cikk bemutatja, hogyan hozhatja létre az Azure Gateway-erőforrást, *miután* [letöltötte és telepítette az átjárót egy helyi számítógépen](../logic-apps/logic-apps-gateway-install.md). Az átjáróval kapcsolatos további információkért tekintse meg [az átjáró működését](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)ismertető témakört.

> [!TIP]
> Az Azure-beli virtuális hálózatokhoz való csatlakozáshoz érdemes inkább [*integrációs szolgáltatási környezetet*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) létrehozni. 

Az átjáró más szolgáltatásokkal való használatáról a következő cikkekben talál további információt:

* [Microsoft Power BI helyszíni adatátjáró](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Helyszíni adatátjáró Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Helyszíni adatátjáró Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Helyszíni adatátjáró Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Támogatott adatforrások

Azure Logic Apps esetében a helyszíni adatátjáró támogatja [a helyszíni összekötőket](../connectors/apis-list.md#on-premises-connectors) a következő adatforrásokhoz:

* BizTalk Server 2016
* Fájlrendszer
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Bár maga az átjáró nem jár további költségekkel, a [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md) a Azure Logic Appsban található összekötőre és egyéb műveletekre is vonatkozik.

## <a name="prerequisites"></a>Előfeltételek

* Már [telepítette a helyszíni adatátjárót egy helyi számítógépen](../logic-apps/logic-apps-gateway-install.md).

* [Ugyanazzal az Azure-fiókkal és Azure-előfizetéssel](../logic-apps/logic-apps-gateway-install.md#requirements) rendelkezik, amelyet a helyszíni adatátjáró telepítésekor használt.

* Korábban még nem csatolta az átjáró telepítését egy másik átjáró-erőforráshoz az Azure-ban.

  Átjáró-erőforrás létrehozásakor ki kell választania egy átjáró-telepítést, amely az átjáró erőforrásához társítva van. Egy már csatolt átjáró telepítése nem érhető el az átjáró erőforrásainak létrehozásakor való kiválasztásához.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure Gateway-erőforrás létrehozása

Miután telepítette az átjárót egy helyi számítógépre, hozza létre az Azure-erőforrást az átjáróhoz. 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) ugyanazzal az Azure-fiókkal, amelyet az átjáró telepítéséhez használt.

1. A Azure Portal keresőmezőbe írja be a "helyszíni adatátjáró" kifejezést, és válassza a helyszíni **adatátjárók**lehetőséget.

   !["Helyszíni adatátjáró" keresése](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. **A helyszíni adatátjárók**területen válassza a **Hozzáadás**lehetőséget.

   ![Adatátjáró hozzáadása](./media/logic-apps-gateway-connection/add-gateway.png)

1. Adja meg ezt az információt az átjáró-erőforráshoz a **kapcsolatok átjárójának létrehozása**területen. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Erőforrás neve** | Az átjáró erőforrásának neve, amely`-`csak betűket, számokat, kötőjeleket (), aláhúzásokat (`_`), zárójeleket (`(`, `)`) és pontokat (`.`) tartalmazhat. |
   | **Előfizetés** | Az Azure-előfizetése, amelynek meg kell egyeznie az átjáró telepítésével és a logikai alkalmazással. Az alapértelmezett előfizetés a bejelentkezéshez használt Azure-fiókon alapul. |
   | **Erőforráscsoport** | A használni kívánt [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) |
   | **Location** | Ugyanaz a régió, mint az átjáró Cloud Service-hez az [átjáró telepítésekor](../logic-apps/logic-apps-gateway-install.md)kiválasztott hely. Ellenkező esetben az átjáró telepítése nem fog megjelenni a **telepítési név** listában a kiválasztáshoz. A logikai alkalmazás helye eltérő lehet az átjáró erőforrásának helyétől. |
   | **Telepítési név** | Ha az átjáró telepítése még nincs kiválasztva, válassza ki a korábban telepített átjárót. A korábban csatolt átjáró telepítése nem jelenik meg ebben a listában a kijelöléshez. |
   |||

   Például:

   ![A helyszíni adatátjáró létrehozásához szükséges adatok megadása](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Csatlakozás helyszíni adatokhoz

Miután létrehozta az átjáró-erőforrást, és társítja az Azure-előfizetést ehhez az erőforráshoz, mostantól létrehozhat egy kapcsolatot a logikai alkalmazás és a helyszíni adatforrás között az átjáró használatával.

1. A Azure Portal hozza létre vagy nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Adjon hozzá egy olyan összekötőt, amely támogatja a helyszíni kapcsolatokat, például **SQL Server**.

1. **A helyszíni adatátjárón keresztül válassza a kapcsolat**lehetőséget. 

1. **Átjárók**esetében válassza ki a létrehozott átjáró-erőforrást.

   > [!NOTE]
   > Az átjárók listája más régiókban is tartalmaz átjáró-erőforrásokat, mivel a logikai alkalmazás helye eltérhet az átjáró erőforrásának helyétől.

1. Adjon meg egyedi nevet és egyéb szükséges adatokat, amelyek a létrehozni kívánt hálózattól függenek.

   Az egyedi kapcsolati név segítségével később könnyebben megtalálhatja a kapcsolatot, különösen akkor, ha több kapcsolatot hoz létre. Ha szükséges, adja meg a felhasználónévhez tartozó minősített tartományt is.
   
   Például:

   ![Kapcsolat létrehozása a logikai alkalmazás és az adatátjáró között](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget. 

Az átjáró-kapcsolatok most már készen állnak a logikai alkalmazás használatára.

## <a name="edit-connection"></a>Kapcsolatok szerkesztése

Az átjáró-kapcsolatok beállításainak frissítéséhez szerkesztheti a kapcsolatokat.

1. A logikai alkalmazás összes API-kapcsolatának megkereséséhez a logikai alkalmazás menüjében, a **fejlesztői eszközök**területen válassza az **API-kapcsolatok**elemet.
   
   ![A logikai alkalmazás menüjében válassza az "API-kapcsolatok" lehetőséget.](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. Válassza ki a kívánt átjáró-kapcsolatokat, majd válassza az **API-kapcsolatok szerkesztése**lehetőséget.

   > [!TIP]
   > Ha a frissítések nem lépnek érvénybe, próbálkozzon [az átjáró Windows-szolgáltatásfiók leállításával és újraindításával](../logic-apps/logic-apps-gateway-install.md#restart-gateway) az átjáró telepítéséhez.

Az Azure-előfizetéshez társított összes API-kapcsolat megkeresése: 

* Az Azure fő menüjében lépjen a **minden szolgáltatás** > **webes** > **API-kapcsolatok**menüpontra.
* Vagy az Azure főmenüjében válassza az **összes erőforrás**lehetőséget. Állítsa a **típus** szűrőt **API-kapcsolatok**értékre.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Átjáró erőforrásának törlése

Egy másik átjáró-erőforrás létrehozásához csatolja az átjáró telepítését egy másik átjáró-erőforráshoz, vagy távolítsa el az átjáró erőforrását anélkül, hogy ez befolyásolná az átjáró telepítését. 

1. Az Azure főmenüjében válassza az **összes erőforrás**lehetőséget. Keresse meg és válassza ki az átjáró erőforrását.

1. Ha még nincs bejelölve, az átjáró erőforrás menüjében válassza **a helyszíni adatátjáró**lehetőséget. Az átjáró erőforrás eszköztárán válassza a **Törlés**lehetőséget.

   Példa:

   ![Átjáró törlése](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K**: Miért nem látom az átjáróm telepítését az Azure-beli átjáró-erőforrás létrehozásakor? <br/>
**A**: Ez a probléma a következő okok miatt fordulhat elő:

* Az átjáró telepítése már regisztrálva van, és egy másik átjáró-erőforrás igényli az Azure-ban. Az átjáró-telepítések nem jelennek meg a példányok listáján az átjáró erőforrásainak létrehozása után. Ha ellenőrizni szeretné az átjáró regisztrációját a Azure Portalban, tekintse át az összes Azure-erőforrást a helyszíni **adatátjárók** típusával az *összes* Azure-előfizetéshez.

* Az átjárót telepítő személy Azure AD-identitása eltér a Azure Portalba bejelentkezett személytől. Győződjön meg arról, hogy ugyanazzal az identitással jelentkezett be, amely az átjárót telepítette.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>További lépések

* [A logikai alkalmazások védelme](./logic-apps-securing-a-logic-app.md)
* [Gyakori példák és forgatókönyvek logikai alkalmazásokhoz](./logic-apps-examples-and-scenarios.md)
