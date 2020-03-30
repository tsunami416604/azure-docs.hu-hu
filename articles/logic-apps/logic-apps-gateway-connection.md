---
title: Hozzáférés a helyszínen lévő adatforrásokhoz
description: Csatlakozás helyszíni adatforrásokhoz az Azure Logic Apps alkalmazásból egy helyszíni Azure-adatátjáró-erőforrás létrehozásával
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: 29c1aaf18ea45d869d32a8817aeb03faa3b67c32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456577"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Csatlakozás helyszíni adatforrásokhoz az Azure Logic Appsből

Mielőtt a helyszíni adatforrásokat a logikai alkalmazások, létre kell hoznia egy [ *Azure-erőforrás* telepítése után a helyszíni adatátjáró egy helyi számítógépre.](../logic-apps/logic-apps-gateway-install.md) A logikai alkalmazások majd használja ezt az [Azure-átjáró](../connectors/apis-list.md#on-premises-connectors) erőforrást az eseményindítók és a helyszíni összekötők által biztosított, az Azure Logic Apps elérhető műveletekben.

Ez a cikk bemutatja, hogyan hozhat létre az Azure átjáró-erőforrást egy korábban [telepített átjáróhoz a helyi számítógépen.](../logic-apps/logic-apps-gateway-install.md) Az átjáróról további információt [az átjáró működéséről](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)talál.

> [!TIP]
> Az Azure virtuális hálózatokhoz való csatlakozáshoz fontolja meg egy [*integrációs szolgáltatási környezet*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) létrehozását. 

Az átjáró más szolgáltatásokkal való használatáról az alábbi cikkekben talál tájékoztatást:

* [A Microsoft Power Automate helyszíni adatátjárója](/power-automate/gateway-reference)
* [Helyszíni Microsoft Power BI adatátjáró](/power-bi/service-gateway-onprem)
* [Helyszíni Microsoft Power Apps-átjáró](/powerapps/maker/canvas-apps/gateway-reference)
* [Helyszíni Azure Analysis Services helyszíni adatátjáró](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Támogatott adatforrások

Az Azure Logic Apps, a helyszíni adatátjáró támogatja a helyszíni összekötők ezen [adatforrások:](../connectors/apis-list.md#on-premises-connectors)

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

Az Azure Logic Apps támogatja az olvasási és írási műveleteket az adatátjárón keresztül. Ezek a műveletek azonban [korlátozzák a hasznos teher méretét](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Bár maga az átjáró nem merülfel további költségekkel, a [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md) vonatkozik ezekre az összekötőkre és egyéb műveletek az Azure Logic Apps.

## <a name="prerequisites"></a>Előfeltételek

* Már [telepítette a helyszíni adatátjárót egy helyi számítógépre.](../logic-apps/logic-apps-gateway-install.md)

* Ugyanazt az [Azure-fiókot és előfizetést](../logic-apps/logic-apps-gateway-install.md#requirements) használja, amelyet az adatátjáró telepítésekor használt. Ennek az Azure-fióknak egyetlen [Azure Active Directory (Azure AD) bérlőhöz vagy könyvtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology)kell tartoznia.

* Az átjáró telepítése még nincs regisztrálva, és egy másik Azure-átjáró-erőforrás igényelt.

  Amikor létrehoz egy átjáró-erőforrást az Azure Portalon, kiválaszt egy átjáró-telepítést, amely az átjáró-erőforráshoz kapcsolódik, és csak az átjáró-erőforráshoz kapcsolódik. Az Azure Logic Apps, helyszíni eseményindítók és műveletek, majd az átjáró erőforrás helyszíni adatforrásokhoz való csatlakozáshoz. Ezekben az eseményindítókban és műveletekben kiválaszthatja az Azure-előfizetést és a használni kívánt átjáró-erőforrást. Minden átjáró-erőforrás csak egy átjáró-telepítésre hivatkozik, amely csak egy Azure-fiókra hivatkozik.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure átjáró-erőforrás létrehozása

Miután telepítette az átjárót egy helyi számítógépre, hozza létre az Azure-erőforrást az átjáróhoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) ugyanazzal az Azure-fiókkal, amelyet az átjáró telepítéséhez használtak.

1. Az Azure Portal keresőmezőjébe írja be a "helyszíni adatátjáró" kifejezést, és válassza **a helyszíni adatátjárók lehetőséget.**

   !["Helyszíni adatátjáró keresése"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. A **helyszíni adatátjárók csoportban**válassza a **Hozzáadás**lehetőséget.

   ![Új Azure-erőforrás hozzáadása adatátjáróhoz](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. A **Kapcsolatátjáró létrehozása**csoportban adja meg ezeket az adatokat az átjáróerőforráshoz. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Erőforrás neve** | Adjon nevet az átjáróerőforrásnak, amely csak betűket,`-`számokat,`_`kötőjeleket (`(`), `)`aláhúzásjeleket`.`( , zárójeleket ( , ) vagy pont ( jeleket ) tartalmaz. |
   | **Előfizetés** | Válassza ki az Azure-előfizetést az átjáró telepítéséhez használt Azure-fiókhoz. Az alapértelmezett előfizetés a bejelentkezéshez használt Azure-fiókon alapul. |
   | **Erőforráscsoport** | A használni kívánt [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) |
   | **Helyen** | Ugyanaz a régió vagy hely, amelyet az átjáró felhőszolgáltatásához az [átjáró telepítése](../logic-apps/logic-apps-gateway-install.md)során választottak ki. Ellenkező esetben az átjáró telepítése nem jelenik meg a **telepítés neve** listában. A logikai alkalmazás helye eltérhet az átjáró erőforrás helyét. |
   | **Telepítés neve** | Válasszon egy átjárótelepítést, amely csak akkor jelenik meg a listában, ha ezek a feltételek teljesülnek: <p><p>- Az átjáró telepítése ugyanazt a régiót használja, mint a létrehozni kívánt átjáróerőforrás. <br>- Az átjáró telepítése nem kapcsolódik egy másik Azure-átjáró-erőforráshoz. <br>- Az átjáró telepítése ugyanahhoz az Azure-fiókhoz kapcsolódik, amelyet az átjáró-erőforrás létrehozásához használ. <br>- Az Azure-fiók egyetlen [Azure Active Directory (Azure AD) bérlőhöz vagy könyvtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology) tartozik, és ugyanaz a fiók, amelyet az átjáró telepítéséhez használtak. <p><p>További információt a [Gyakran ismételt kérdések](#faq) című részben talál. |
   |||

   Íme egy példa, amely egy átjáró-telepítést mutat be, amely ugyanabban a régióban található, mint az átjáró-erőforrás, és ugyanahhoz az Azure-fiókhoz kapcsolódik:

   ![Adatátjáró-erőforrás létrehozásához részletezés](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Csatlakozás helyszíni adatokhoz

Miután létrehozta az átjáró-erőforrást, és társítja az Azure-előfizetést ezzel az erőforrással, most már létrehozhat egy kapcsolatot a logikai alkalmazás és a helyszíni adatforrás között az átjáró használatával.

1. Az Azure Portalon hozza létre vagy nyissa meg a logikai alkalmazást a Logic App Designerben.

1. Adjon hozzá egy olyan összekötőt, amely támogatja a helyszíni kapcsolatokat, például **az SQL Server kiszolgálót.**

1. Válassza a **Csatlakozás a helyszíni adatátjárón keresztül**lehetőséget.

1. Az **Átjárók** **csoportban** válassza ki azt az Azure-előfizetést, amely rendelkezik a kívánt átjáró-erőforrással.

1. A **Kapcsolatátjáró** listából, amely a kijelölt előfizetésben elérhető átjáró-erőforrásokat jeleníti meg, válassza ki a kívánt átjáró-erőforrást. Minden átjáró-erőforrás egyetlen átjárótelepítéshez kapcsolódik.

   > [!NOTE]
   > Az átjárók listája más régiókban lévő átjáró-erőforrásokat is tartalmaz, mivel a logikai alkalmazás helye eltérhet az átjáróerőforrás helyétől. 

1. Adjon meg egyedi kapcsolatnevet és egyéb szükséges információkat, amelyek a létrehozni kívánt kapcsolattól függenek.

   Az egyedi kapcsolatnév segítségével később könnyen megtalálhatja a kapcsolatot, különösen akkor, ha több kapcsolatot hoz létre. Adott esetben adja meg a felhasználónevének minősített tartományát is.

   Például:

   ![Kapcsolat létrehozása a logikai alkalmazás és az adatátjáró között](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

Az átjárókapcsolat most már készen áll a logikai alkalmazás használatára.

## <a name="edit-connection"></a>Kapcsolat szerkesztése

Az átjárókapcsolat beállításainak frissítéséhez szerkesztheti a kapcsolatot.

1. Ha csak a logikai alkalmazás összes API-kapcsolatát szeretné megtalálni, a logikai alkalmazás menüjében válassza **az** **API-kapcsolatok**lehetőséget.

   ![A logikai alkalmazás menüjében válassza az "API-kapcsolatok" lehetőséget.](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Jelölje ki a kívánt átjárókapcsolatot, majd válassza **az API-kapcsolat szerkesztése**lehetőséget.

   > [!TIP]
   > Ha a frissítések nem lépnek érvénybe, próbálja meg [leállítani és újraindítani az átjáró Windows szolgáltatásfiókját](../logic-apps/logic-apps-gateway-install.md#restart-gateway) az átjáró telepítéséhez.

Az Azure-előfizetéséhez társított összes API-kapcsolat megkeresése:

* Az Azure Portal menüben válassza a **Minden szolgáltatás** > **webes** > **API-kapcsolatok**lehetőséget.
* Vagy válassza az Azure Portal menüben válassza az **Összes erőforrás lehetőséget.** Állítsa a **Típus** szűrőt **AZ API-kapcsolat beállításra.**

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Átjáró-erőforrás törlése

Ha másik átjáró-erőforrást szeretne létrehozni, az átjáró telepítését egy másik átjáró-erőforráshoz szeretné kapcsolni, vagy el szeretné távolítani az átjáró-erőforrást, törölheti az átjáró-erőforrást anélkül, hogy ez befolyásolná az átjáró telepítését.

1. Az Azure Portal menüben válassza az **Összes erőforrás**lehetőséget, vagy keresse meg és válassza az **Összes erőforrást** bármely lapon. Keresse meg és válassza ki az átjáró-erőforrást.

1. Ha még nincs bejelölve, válassza az átjáró erőforrásmenüjében válassza **a Helyszíni adatátjáró lehetőséget.** Az átjáróerőforrás eszköztárán válassza a **Törlés**gombot.

   Példa:

   ![Átjáró-erőforrás törlése az Azure-ban](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K:** Miért nem jelenik meg az átjáró telepítése, amikor létrehozom az átjáró-erőforrásomat az Azure-ban? <br/>
**A**: Ez a probléma a következő okok miatt fordulhat elő:

* Az Azure-fióknak meg kell egyeznie azzal a fiókkal, amely kapcsolódik az átjáró telepítéséhez a helyi számítógépen. Ellenőrizze, hogy be van-e jelentkezve az Azure Portalon az átjáró telepítéséhez kapcsolódó identitással. Győződjön meg arról is, hogy az Azure-fiók egyetlen [Azure AD-bérlőhöz vagy -címtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology) tartozik, és ugyanabba az Azure AD-bérlőre vagy könyvtárra van beállítva, amelyet az átjáró telepítése során használtak.

* Az átjáró-erőforrás és az átjáró telepítése kell használni ugyanazt a régiót. A logikai alkalmazás helye azonban eltérhet az átjáróerőforrás helyétől.

* Az átjáró telepítése már regisztrálva van, és egy másik átjáró-erőforrás igényli. Ezek a telepítések nem jelennek meg a **telepítés neve** listában. Az azure-beli portálon az átjáróregisztrációk áttekintéséhez keresse meg az összes Olyan Azure-erőforrást, amely a **helyszíni adatátjárók** típusa az *összes* Azure-előfizetésében. Az átjárótelepítés másik átjáróerőforrásról való leválasztásáról az [Átjáró-erőforrás törlése](#change-delete-gateway-resource)című témakörben található.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>További lépések

* [A logikai alkalmazások védelme](./logic-apps-securing-a-logic-app.md)
* [Gyakori példák és forgatókönyvek a logikai alkalmazásokhoz](./logic-apps-examples-and-scenarios.md)
