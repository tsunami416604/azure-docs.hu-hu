---
title: Az adatforrások elérése a helyszínen
description: Kapcsolódás a Azure Logic Apps helyszíni adatforrásaihoz egy adatátjáró-erőforrás létrehozásával az Azure-ban
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, divswa, logicappspm
ms.topic: article
ms.date: 08/18/2020
ms.openlocfilehash: 2dd086ccc45458299cf6b8a7ad83d023055c96ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88661197"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Csatlakozás helyszíni adatforrásokhoz az Azure Logic Appsből

Miután [telepítette a helyszíni *adatátjárót* egy helyi számítógépre](../logic-apps/logic-apps-gateway-install.md) , és ahhoz, hogy a logikai alkalmazásaiból hozzáférhessen a helyszíni adatforrásokhoz, létre kell hoznia egy átjáró-erőforrást az Azure-ban az átjáró telepítéséhez. Ezután kiválaszthatja ezt az átjáró-erőforrást a Azure Logic Appsben elérhető [helyszíni összekötők](../connectors/apis-list.md#on-premises-connectors) esetében használni kívánt eseményindítókban és műveletekben. Azure Logic Apps támogatja az olvasási és írási műveleteket az adatátjárón keresztül. Ezek a műveletek azonban [korlátokkal rendelkeznek a hasznos adatok méretétől függően](/data-integration/gateway/service-gateway-onprem#considerations).

Ez a cikk bemutatja, hogyan hozhatja létre az Azure Gateway-erőforrást egy korábban [telepített átjáróhoz a helyi számítógépen](../logic-apps/logic-apps-gateway-install.md). Az átjáróval kapcsolatos további információkért tekintse meg [az átjáró működését](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)ismertető témakört.

> [!TIP]
> Ahhoz, hogy az átjáró használata nélkül közvetlenül hozzáférhessen a helyszíni erőforrásokhoz az Azure-beli virtuális hálózatokban, érdemes lehet [*integrációs szolgáltatási környezetet*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) létrehozni. 

Az átjáró más szolgáltatásokkal való használatáról a következő cikkekben talál további információt:

* [A Microsoft Power automatizálja a helyszíni adatátjárót](/power-automate/gateway-reference)
* [Microsoft Power BI helyszíni adatátjáró](/power-bi/service-gateway-onprem)
* [Microsoft Power apps helyszíni adatátjáró](/powerapps/maker/canvas-apps/gateway-reference)
* [Helyszíni adatátjáró Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Támogatott adatforrások

Azure Logic Apps a helyszíni adatátjáró támogatja a helyszíni [összekötőket](../connectors/apis-list.md#on-premises-connectors) a következő adatforrásokhoz:

* BizTalk Server 2016
* Fájlrendszer
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint-kiszolgáló
* SQL Server
* Teradata

A REST vagy a SOAP használatával olyan [Egyéni összekötőket](../logic-apps/custom-connector-overview.md) is létrehozhat, amelyek http vagy HTTPS protokollon keresztül csatlakoznak adatforrásokhoz. Bár maga az átjáró nem jár további költségekkel, a [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md) a Azure Logic Appsban található összekötőre és egyéb műveletekre is vonatkozik.

## <a name="prerequisites"></a>Előfeltételek

* Már [telepítette a helyszíni adatátjárót egy helyi számítógépen](../logic-apps/logic-apps-gateway-install.md). Az átjáró telepítése csak akkor szükséges, ha olyan átjáró-erőforrást hoz létre, amely erre a telepítésre hivatkozik.

* [Ugyanazzal az Azure-fiókkal és-előfizetéssel](../logic-apps/logic-apps-gateway-install.md#requirements) rendelkezik, amelyet az átjáró telepítéséhez használt. Az Azure-fióknak csak egyetlen [Azure Active Directory (Azure ad) bérlőhöz vagy címtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology)kell tartoznia. Ugyanazt az Azure-fiókot és-előfizetést kell használnia az átjáró-erőforrás létrehozásához az Azure-ban, mert csak az átjáró rendszergazdája hozhatja létre az átjáró-erőforrást az Azure-ban. Az egyszerű szolgáltatások jelenleg nem támogatottak.

  * Amikor átjáró-erőforrást hoz létre az Azure-ban, ki kell választania egy átjáró-telepítést, amely az átjáró-erőforráshoz és csak az átjáró erőforrásához kapcsolódik. Minden átjáró-erőforrás csak egy átjáró-telepítéshez tud kapcsolódni. Nem választhat olyan átjáró-telepítést, amely már hozzá van rendelve egy másik átjáró-erőforráshoz.
  
  * A logikai alkalmazásnak és az átjáró erőforrásnak nem kell ugyanabban az Azure-előfizetésben léteznie. A helyszíni adatforrásokhoz hozzáférő eseményindítókban és műveletekben olyan Azure-előfizetéseket is választhat, amelyek rendelkeznek átjáró-erőforrásokkal.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure Gateway-erőforrás létrehozása

Miután telepítette az átjárót egy helyi számítógépre, hozza létre az Azure-erőforrást az átjáróhoz.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) ugyanazzal az Azure-fiókkal, amelyet az átjáró telepítéséhez használt.

1. A Azure Portal keresőmezőbe írja be a "helyszíni adatátjáró" kifejezést, és válassza a helyszíni **adatátjárók**lehetőséget.

   !["Helyszíni adatátjáró" keresése](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. **A helyszíni adatátjárók**területen válassza a **Hozzáadás**lehetőséget.

   ![Új Azure-erőforrás hozzáadása az adatátjáróhoz](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Adja meg ezt az információt az átjáró-erőforráshoz a **kapcsolatok átjárójának létrehozása**területen. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Erőforrás neve** | Adja meg az átjáró-erőforrás nevét, amely csak betűket, számokat, kötőjeleket ( `-` ), aláhúzást ( `_` ), zárójeleket ( `(` , `)` ) vagy pontokat ( `.` ) tartalmaz. |
   | **Előfizetés** | Válassza ki az átjáró telepítéséhez használt Azure-fiókhoz tartozó Azure-előfizetést. Az alapértelmezett előfizetés a bejelentkezéshez használt Azure-fiókon alapul. |
   | **Erőforráscsoport** | A használni kívánt [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) |
   | **Hely** | Ugyanaz a régió vagy hely, amely az átjáró Cloud Service-hez lett kiválasztva az [átjáró telepítésekor](../logic-apps/logic-apps-gateway-install.md). Ellenkező esetben az átjáró telepítése nem jelenik meg a **telepítési név** listában. A logikai alkalmazás helye eltérő lehet az átjáró erőforrásának helyétől. |
   | **Telepítési név** | Válasszon ki egy átjáró-telepítést, amely csak akkor jelenik meg a listában, ha teljesülnek ezek a feltételek: <p><p>– Az átjáró telepítése ugyanazt a régiót használja, mint a létrehozni kívánt átjáró-erőforrás. <br>– Az átjáró telepítése nem kapcsolódik egy másik Azure Gateway-erőforráshoz. <br>– Az átjáró telepítése ugyanahhoz az Azure-fiókhoz van csatolva, amelyet az átjáró erőforrásának létrehozásához használ. <br>– Az Azure-fiókja egyetlen [Azure Active Directory (Azure ad) bérlőhöz vagy címtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology) tartozik, és ugyanaz a fiók, amelyet az átjáró telepítésekor használt. <p><p>További információt a [Gyakori kérdések](#faq) című szakaszban talál. |
   |||

   Az alábbi példa egy olyan átjáró telepítését mutatja be, amely ugyanabban a régióban található, mint az átjáró-erőforrás, és ugyanahhoz az Azure-fiókhoz van társítva:

   ![Adja meg az adatátjáró-erőforrás létrehozásának részleteit](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Csatlakozás helyszíni adatokhoz

Miután létrehozta az átjáró-erőforrást, és társítja az Azure-előfizetést ehhez az erőforráshoz, mostantól létrehozhat egy kapcsolatot a logikai alkalmazás és a helyszíni adatforrás között az átjáró használatával.

1. A Azure Portal hozza létre vagy nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Adjon hozzá egy olyan összekötőt, amely támogatja a helyszíni kapcsolatokat, például **SQL Server**.

1. **A helyszíni adatátjárón keresztül válassza a kapcsolat**lehetőséget.

1. Az **átjárók**területen az **előfizetések** listából válassza ki azt az Azure-előfizetést, amelyhez a kívánt átjáró-erőforrás tartozik.

   Ha rendelkezik előfizetés-hozzáféréssel, különböző Azure-előfizetések közül választhat, amelyek mindegyike egy másik átjáró-erőforráshoz van társítva. A logikai alkalmazásnak és az átjáró erőforrásnak nem kell ugyanabban az Azure-előfizetésben léteznie.

1. Válassza ki a kívánt átjáró-erőforrást a **csatlakozási átjáró** listából, amely megjeleníti a kiválasztott előfizetésben elérhető átjáró-erőforrásokat. Minden átjáró-erőforrás egyetlen átjáróhoz van csatolva.

   > [!NOTE]
   > Az átjárók listája más régiókban is tartalmaz átjáró-erőforrásokat, mivel a logikai alkalmazás helye eltérhet az átjáró erőforrásának helyétől. 

1. Adjon meg egyedi nevet és egyéb szükséges adatokat, amelyek a létrehozni kívánt hálózattól függenek.

   Az egyedi kapcsolati név segítségével később könnyebben megtalálhatja a kapcsolatot, különösen akkor, ha több kapcsolatot hoz létre. Ha szükséges, adja meg a felhasználónévhez tartozó minősített tartományt is.

   Alább bemutatunk egy példát:

   ![Kapcsolat létrehozása a logikai alkalmazás és az adatátjáró között](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

Az átjáró-kapcsolatok most már készen állnak a logikai alkalmazás használatára.

## <a name="edit-connection"></a>Kapcsolatok szerkesztése

Az átjáró-kapcsolatok beállításainak frissítéséhez szerkesztheti a kapcsolatokat.

1. A logikai alkalmazás összes API-kapcsolatának megkereséséhez a logikai alkalmazás menüjében, a **fejlesztői eszközök**területen válassza az **API-kapcsolatok**elemet.

   ![A logikai alkalmazás menüjében válassza az "API-kapcsolatok" lehetőséget.](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Válassza ki a kívánt átjáró-kapcsolatokat, majd válassza az **API-kapcsolatok szerkesztése**lehetőséget.

   > [!TIP]
   > Ha a frissítések nem lépnek érvénybe, próbálkozzon [az átjáró Windows-szolgáltatásfiók leállításával és újraindításával](../logic-apps/logic-apps-gateway-install.md#restart-gateway) az átjáró telepítéséhez.

Az Azure-előfizetéshez társított összes API-kapcsolat megkeresése:

* A Azure Portal menüben válassza a **minden szolgáltatás**  >  **webes**  >  **API-kapcsolatok**lehetőséget.
* Vagy a Azure Portal menüben válassza a **minden erőforrás**elemet. Állítsa a **típus** szűrőt **API-kapcsolatok**értékre.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Átjáró erőforrásának törlése

Egy másik átjáró-erőforrás létrehozásához csatolja az átjáró telepítését egy másik átjáró-erőforráshoz, vagy távolítsa el az átjáró erőforrását anélkül, hogy ez befolyásolná az átjáró telepítését.

1. A Azure Portal menüben válassza a **minden erőforrás**lehetőséget, vagy keresse meg és válassza ki az **összes erőforrás** elemet bármelyik oldalon. Keresse meg és válassza ki az átjáró erőforrását.

1. Ha még nincs bejelölve, az átjáró erőforrás menüjében válassza **a helyszíni adatátjáró**lehetőséget. Az átjáró erőforrás eszköztárán válassza a **Törlés**lehetőséget.

   Például:

   ![Átjáró erőforrásának törlése az Azure-ban](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K**: Miért nem jelenik meg az átjáróm telepítése, amikor létrehozom az átjáró-erőforrást az Azure-ban? <br/>
**A**: Ez a probléma a következő okok miatt fordulhat elő:

* Az Azure-fiókja nem ugyanaz a fiók, amelyet az átjáró telepítéséhez használt a helyi számítógépen. Győződjön meg arról, hogy bejelentkezett a Azure Portalba ugyanazzal az identitással, amelyet az átjáró telepítéséhez használt. Csak az átjáró rendszergazdája hozhatja létre az átjáró-erőforrást az Azure-ban. Az egyszerű szolgáltatások jelenleg nem támogatottak.

* Az Azure-fiókja nem csak egyetlen [Azure ad-bérlőhöz vagy-címtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology)tartozik. Győződjön meg arról, hogy ugyanazt az Azure AD-bérlőt vagy-címtárat használja, amelyet az átjáró telepítésekor használt.

* Az átjáró-erőforrás és az átjáró telepítése nem ugyanabban a régióban található. A logikai alkalmazás helye azonban eltérő lehet az átjáró erőforrásainak helyétől.

* Az átjáró telepítése már hozzá van rendelve egy másik átjáró-erőforráshoz. Mindegyik átjáró-erőforrás csak egy átjáró-telepítésre tud hivatkozni, amely csak egy Azure-fiókra és-előfizetésre mutathat. Ezért nem választhat olyan átjáró-telepítést, amely már társítva van egy másik átjáró-erőforráshoz. Ezek a telepítések nem jelennek meg a **telepítési név** listában.

  Ha szeretné áttekinteni az átjáró regisztrációját a Azure Portalban, keresse meg az összes Azure-erőforrást, amely rendelkezik a helyszíni **adatátjárók erőforrás-** típussal az *összes* Azure-előfizetésében. Az átjáró másik átjáró-erőforrással való összekapcsolásának megszüntetéséhez tekintse meg az [átjáró erőforrásának törlése](#change-delete-gateway-resource)című témakört.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>További lépések

* [A logikai alkalmazások védelme](./logic-apps-securing-a-logic-app.md)
* [Gyakori példák és forgatókönyvek logikai alkalmazásokhoz](./logic-apps-examples-and-scenarios.md)
