---
title: Az adatforrások elérése a helyszínen
description: Helyi adatforrásokhoz való kapcsolódás Azure Logic Apps egy Azure helyszíni adatátjáró-erőforrás létrehozásával
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: 0e2dcec15566749b58c439b68532829b67716754
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815159"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Helyi adatforrásokhoz való kapcsolódás Azure Logic Apps

Mielőtt a logikai alkalmazásokból hozzáférhessen a helyszíni adatforrásokhoz, létre kell hoznia egy Azure-erőforrást, miután [telepítette a helyszíni *adatátjárót* a helyi számítógépen](../logic-apps/logic-apps-gateway-install.md). A logikai alkalmazások ezután ezt az Azure Gateway-erőforrást használják a Azure Logic Apps számára elérhető [helyszíni összekötők](../connectors/apis-list.md#on-premises-connectors) által biztosított triggerekben és műveletekben.

Ez a cikk bemutatja, hogyan hozhatja létre az Azure Gateway-erőforrást egy korábban [telepített átjáróhoz a helyi számítógépen](../logic-apps/logic-apps-gateway-install.md). Az átjáróval kapcsolatos további információkért tekintse meg [az átjáró működését](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)ismertető témakört.

> [!TIP]
> Az Azure-beli virtuális hálózatokhoz való csatlakozáshoz érdemes inkább [*integrációs szolgáltatási környezetet*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) létrehozni. 

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
* SharePoint Server
* SQL Server
* Teradata

Azure Logic Apps támogatja az olvasási és írási műveleteket az adatátjárón keresztül. Ezek a műveletek azonban [korlátokkal rendelkeznek a hasznos adatok méretétől függően](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Bár maga az átjáró nem jár további költségekkel, a [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md) a Azure Logic Appsban található összekötőre és egyéb műveletekre is vonatkozik.

## <a name="prerequisites"></a>Előfeltételek

* Már [telepítette a helyszíni adatátjárót egy helyi számítógépen](../logic-apps/logic-apps-gateway-install.md).

* [Ugyanazt az Azure-fiókot és-előfizetést](../logic-apps/logic-apps-gateway-install.md#requirements) használja, amelyet az adatátjáró telepítésekor használt. Az Azure-fióknak egyetlen [Azure Active Directory (Azure ad) bérlőhöz vagy címtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology)kell tartoznia.

* Az átjáró telepítése még nincs regisztrálva, és egy másik Azure Gateway-erőforrás igényli.

  Amikor átjáró-erőforrást hoz létre a Azure Portalban, ki kell választania egy átjáró-telepítést, amely az átjáró-erőforrásra és csak az átjáró erőforrására hivatkozik. Azure Logic Apps a helyszíni eseményindítók és műveletek a helyszíni adatforrásokhoz való kapcsolódáshoz az átjáró-erőforrást használják. Ezekben az eseményindítókban és műveletekben ki kell választania az Azure-előfizetését és a használni kívánt kapcsolódó átjáró-erőforrást. Minden átjáró-erőforrás csak egy Azure-fiókra hivatkozik.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure Gateway-erőforrás létrehozása

Miután telepítette az átjárót egy helyi számítógépre, hozza létre az Azure-erőforrást az átjáróhoz.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) ugyanazzal az Azure-fiókkal, amelyet az átjáró telepítéséhez használt.

1. A Azure Portal keresőmezőbe írja be a "helyszíni adatátjáró" kifejezést, és válassza a helyszíni **adatátjárók**lehetőséget.

   !["Helyszíni adatátjáró" keresése](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. **A helyszíni adatátjárók**területen válassza a **Hozzáadás**lehetőséget.

   ![Új Azure-erőforrás hozzáadása az adatátjáróhoz](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Adja meg ezt az információt az átjáró-erőforráshoz a **kapcsolatok átjárójának létrehozása**területen. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Erőforrás neve** | Adja meg az átjáró-erőforrás nevét, amely csak betűket, számokat, kötőjeleket (`-`), aláhúzást (`_`), zárójeleket (`(`, `)`) vagy pontokat (`.`) tartalmaz. |
   | **Előfizetés** | Válassza ki az átjáró telepítéséhez használt Azure-fiókhoz tartozó Azure-előfizetést. Az alapértelmezett előfizetés a bejelentkezéshez használt Azure-fiókon alapul. |
   | **Erőforráscsoport** | A használni kívánt [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) |
   | **Hely** | Ugyanaz a régió vagy hely, amely az átjáró Cloud Service-hez lett kiválasztva az [átjáró telepítésekor](../logic-apps/logic-apps-gateway-install.md). Ellenkező esetben az átjáró telepítése nem jelenik meg a **telepítési név** listában. A logikai alkalmazás helye eltérő lehet az átjáró erőforrásának helyétől. |
   | **Telepítési név** | Válasszon ki egy átjáró-telepítést, amely csak akkor jelenik meg a listában, ha teljesülnek ezek a feltételek: <p><p>– Az átjáró telepítése ugyanazt a régiót használja, mint a létrehozni kívánt átjáró-erőforrás. <br>– Az átjáró telepítése nem kapcsolódik egy másik Azure Gateway-erőforráshoz. <br>– Az átjáró telepítése ugyanahhoz az Azure-fiókhoz van csatolva, amelyet az átjáró erőforrásának létrehozásához használ. <br>– Az Azure-fiókja egyetlen [Azure Active Directory (Azure ad) bérlőhöz vagy címtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology) tartozik, és ugyanaz a fiók, amelyet az átjáró telepítésekor használt. <p><p>További információt a [Gyakori kérdések](#faq) című szakaszban talál. |
   |||

   Az alábbi példa egy olyan átjáró telepítését mutatja be, amely ugyanabban a régióban található, mint az átjáró-erőforrás, és ugyanahhoz az Azure-fiókhoz van társítva:

   ![Adja meg az adatátjáró-erőforrás létrehozásának részleteit](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Helyileg tárolt adatok elérése

Miután létrehozta az átjáró-erőforrást, és társítja az Azure-előfizetést ehhez az erőforráshoz, mostantól létrehozhat egy kapcsolatot a logikai alkalmazás és a helyszíni adatforrás között az átjáró használatával.

1. A Azure Portal hozza létre vagy nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Adjon hozzá egy olyan összekötőt, amely támogatja a helyszíni kapcsolatokat, például **SQL Server**.

1. **A helyszíni adatátjárón keresztül válassza a kapcsolat**lehetőséget.

1. Az **átjárók**területen az **előfizetések** listából válassza ki azt az Azure-előfizetést, amelyhez a kívánt átjáró-erőforrás tartozik.

1. Válassza ki a kívánt átjáró-erőforrást a **csatlakozási átjáró** listából, amely megjeleníti a kiválasztott előfizetésben elérhető átjáró-erőforrásokat. Minden átjáró-erőforrás egyetlen átjáróhoz van csatolva.

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

   ![A logikai alkalmazás menüjében válassza az "API-kapcsolatok" lehetőséget.](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Válassza ki a kívánt átjáró-kapcsolatokat, majd válassza az **API-kapcsolatok szerkesztése**lehetőséget.

   > [!TIP]
   > Ha a frissítések nem lépnek érvénybe, próbálkozzon [az átjáró Windows-szolgáltatásfiók leállításával és újraindításával](../logic-apps/logic-apps-gateway-install.md#restart-gateway) az átjáró telepítéséhez.

Az Azure-előfizetéshez társított összes API-kapcsolat megkeresése:

* A Azure Portal menüben válassza a **minden szolgáltatás** > **webes** > **API-kapcsolatok**elemet.
* Vagy a Azure Portal menüben válassza a **minden erőforrás**elemet. Állítsa a **típus** szűrőt **API-kapcsolatok**értékre.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Átjáró erőforrásának törlése

Egy másik átjáró-erőforrás létrehozásához csatolja az átjáró telepítését egy másik átjáró-erőforráshoz, vagy távolítsa el az átjáró erőforrását anélkül, hogy ez befolyásolná az átjáró telepítését.

1. A Azure Portal menüben válassza a **minden erőforrás**lehetőséget, vagy keresse meg és válassza ki az **összes erőforrás** elemet bármelyik oldalon. Keresse meg és válassza ki az átjáró erőforrását.

1. Ha még nincs bejelölve, az átjáró erőforrás menüjében válassza **a helyszíni adatátjáró**lehetőséget. Az átjáró erőforrás eszköztárán válassza a **Törlés**lehetőséget.

   Példa:

   ![Átjáró erőforrásának törlése az Azure-ban](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K**: Miért nem jelenik meg az átjáróm telepítése, amikor létrehozom az átjáró-erőforrást az Azure-ban? <br/>
**A**: Ez a probléma a következő okok miatt fordulhat elő:

* Az Azure-fióknak egyeznie kell azzal a fiókkal, amely a helyi számítógépen található átjáróhoz van csatolva. Győződjön meg arról, hogy bejelentkezett a Azure Portalba ugyanazzal az identitással, amely az átjáró telepítéséhez van társítva. Győződjön meg arról is, hogy az Azure-fiókja egyetlen [Azure ad-bérlőhöz vagy-címtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology) tartozik, és ugyanarra az Azure ad-bérlőre vagy könyvtárra van beállítva, amelyet az átjáró telepítésekor használt.

* Az átjáró erőforrásának és az átjáró telepítésének ugyanazt a régiót kell használnia. Azonban a logikai alkalmazás helye eltérő lehet az átjáró erőforrásának helyétől.

* Az átjáró telepítése már regisztrálva van, és egy másik átjáró-erőforrás igényli. Ezek a telepítések nem jelennek meg a **telepítési név** listában. Ha szeretné áttekinteni az átjáró regisztrációját a Azure Portalban, keresse meg az összes Azure-erőforrást, amely rendelkezik a helyszíni **adatátjárók** típusával az *összes* Azure-előfizetésében. Az átjáró másik átjáró-erőforrással való összekapcsolásának megszüntetéséhez tekintse meg az [átjáró erőforrásának törlése](#change-delete-gateway-resource)című témakört.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Következő lépések

* [A logikai alkalmazások védelme](./logic-apps-securing-a-logic-app.md)
* [Gyakori példák és forgatókönyvek logikai alkalmazásokhoz](./logic-apps-examples-and-scenarios.md)
