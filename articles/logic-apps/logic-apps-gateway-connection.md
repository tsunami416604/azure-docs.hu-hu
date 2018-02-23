---
title: "A helyszíni adatforrások elérése az Azure Logic Apps |} Microsoft Docs"
description: "A helyszíni adatátjáró beállítása, a helyszíni adatforrások eléréséhez a logic Apps alkalmazásokból"
keywords: "adatok, a helyszíni, az adatok átvitele, a titkosítás és a adatforrások eléréséhez"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: 216745f9f540235ee48661eae922a5ae0e716e01
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="connect-to-data-sources-on-premises-from-logic-apps-with-on-premises-data-gateway"></a>A helyszíni adatforrások csatlakozni a logic Apps alkalmazásokból, a helyszíni adatok átjáróval

A helyszíni adatforrások eléréséhez a logic Apps alkalmazásokat, egy helyszíni adatátjáró, támogatott összekötők logic Apps alkalmazásokat használhat beállítása. Az átjáró működik hídként, amelyen a gyors adatátvitel és a helyszíni adatforrások és a logic Apps alkalmazások között. Az átjáró továbbítja a titkosított csatornákon keresztül az Azure Service Bus helyszíni forrásból származó adatokat. Az összes forgalom származik, az átjáró ügynök biztonságos kimenő forgalmát. További információ [az átjáró működése](logic-apps-gateway-install.md#gateway-cloud-service). 

Az átjáró a helyszíni e adatforrásokkal létesített kapcsolatokat is támogatja:

*   BizTalk Server 2016
*   DB2  
*   Fájlrendszer
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP alkalmazáskiszolgáló 
*   SAP üzenetkiszolgáló
*   SharePoint
*   SQL Server
*   Teradata

Ezeket a lépéseket a helyszíni adatátjáró beállítása a logic apps használható szemléltetik. További információ a támogatott összekötők: [az Azure Logic Apps összekötők](../connectors/apis-list.md). 

Az átjáró más szolgáltatásokkal való használatával kapcsolatos információkért lásd: ezek a cikkek:

*   [Microsoft Power BI helyszíni adatátjáró](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Az Azure Analysis Services helyi adatátjáró](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow helyszíni adatátjáró](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps helyszíni adatátjáró](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>Követelmények

* Már rendelkeznie kell [az átjáró telepítve a helyi számítógép](logic-apps-gateway-install.md).

* Amikor bejelentkezik az Azure portálra, akkor alkalmazza, ugyanazzal a munkahelyi vagy iskolai fiókkal, de a használt [telepítse a helyszíni adatátjáró](logic-apps-gateway-install.md#requirements). A bejelentkezési fiók is rendelkeznie kell Azure-előfizetés létrehozásakor egy átjáró-erőforráshoz az Azure-portálon az átjáró telepítéséhez használandó.

* Az átjáró telepítése már nem engedte, hogy az Azure átjáró-erőforráshoz. Az átjáró telepítésének csak egy Azure átjáró-erőforráshoz lehet társítani. Jogcím történik, ha az átjáró erőforrás létrehozása, hogy a telepítés nem érhető el az egyéb erőforrások.

* Az a helyszíni átjáró egy Windows fut, és be van állítva a használandó `NT SERVICE\PBIEgwService` a Windows szolgáltatás bejelentkezési hitelesítő adatokat. Hozhatja létre és kezelheti az Azure portálon, az átjáró-erőforráshoz a [Windows szolgáltatásfiók](../logic-apps/logic-apps-gateway-install.md) rendelkeznie kell legalább **közreműködő** engedélyek. 

  > [!NOTE]
  > A Windows-fiók eltér a helyi adatforrások, való kapcsolódáshoz használt fiók és az Azure-ból munkahelyi vagy iskolai fiók használatával jelentkezzen be a felhőalapú szolgáltatások.

## <a name="install-the-on-premises-data-gateway"></a>A helyszíni adatátjáró telepítése

Ha még nem tette meg, kövesse a [az helyszíni átjáró telepítésének lépéseit](logic-apps-gateway-install.md). A többi lépés a folytatás előtt győződjön meg arról, hogy az átjáró a helyi számítógépen telepítve.

<a name="create-gateway-resource"></a>

## <a name="create-an-azure-resource-for-the-on-premises-data-gateway"></a>Hozzon létre egy Azure-erőforrás a helyszíni adatok átjáró

Miután telepítette az átjárót a helyi számítógépen, létre kell hoznia az Ön data gateway elemmel olyan erőforrásként az Azure-ban. Ebben a lépésben az Azure-előfizetéshez is társítja az átjáró-erőforráshoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal") Győződjön meg arról, hogy az Azure ugyanazzal a munkahelyi vagy iskolai e-mail címet az átjáró telepítéséhez használt.

2. A fő Azure menüben válasszon **hozzon létre egy erőforrást** > **vállalati integrációs** > **helyszíni adatátjáró**:

   !["A helyszíni adatok gateway" található](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Az a **kapcsolat átjáró létrehozása** lapján meg az alábbi adatokat a data gateway erőforrás létrehozása:

    * **Név**: Adjon meg egy nevet az átjáró-erőforráshoz. 

    * **Előfizetés**: válassza ki az Azure-előfizetés társítása az átjáró-erőforráshoz. 
    Ehhez az előfizetéshez kell lennie a Logic Apps alkalmazást tárolóként ugyanazt az előfizetést.
   
      Az alapértelmezett előfizetés bejelentkezéshez használt Azure-fiók alapul.

    * **Erőforráscsoport**: hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot üzembe helyezéséhez az átjáró-erőforráshoz. 
    Erőforráscsoportok segítségével gyűjteményként kapcsolódó Azure eszközöket kezelheti.

    * **Hely**: Azure korlátozza a hely esetében az átjáró felhőszolgáltatáshoz során kiválasztott ugyanabban a régióban [átjáró telepítésének](logic-apps-gateway-install.md). 

      > [!NOTE]
      > Győződjön meg arról, hogy az átjáró erőforrás helye megegyezik-e az átjáró felhőalapú szolgáltatás helyét. Ellenkező esetben az átjáró telepítése nem jelenik meg, hogy a következő lépésben válassza ki a telepített átjárót listájában.
      > 
      > Különböző régiókban is használhatja, az átjáró erőforrás és a logikai alkalmazásnak.

    * **Telepítési neve**: Ha nincs kiválasztva, akkor az átjáró telepítésének, válassza ki a korábban telepített átjárót. 

    Az átjáró erőforrás hozzáadása az Azure irányítópultra, válassza a **rögzítés az irányítópulton**. 
    Ha elkészült, kattintson a **Létrehozás** gombra.

    Példa:

    ![Adja meg a helyszíni data gateway létrehozásához szükséges adatok](./media/logic-apps-gateway-connection/createblade.png)

    Található, vagy tekintse meg az Ön data gateway tetszőleges időpontban, akkor főmenüben az Azure Ugrás **több szolgáltatások** > **vállalati integrációs** > **helyszíni adatátjáró** .

    ![Ugrás a "Szolgáltatás", "Vállalati integrációs", "a helyszíni Data Gateways"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-your-logic-app-to-the-on-premises-data-gateway"></a>A Logic Apps alkalmazást csatlakozni a helyszíni adatátjáró

Most, hogy a data gateway erőforrás elkészítette és az Azure-előfizetéshez társított erőforrás, a Logic Apps alkalmazást és az átjáró közötti kapcsolat létrehozása.

> [!NOTE]
> Az átjáró kapcsolódási helyet léteznie kell a Logic Apps alkalmazást ugyanabban a régióban, de használhatja a data gateway már egy másik régióban.

1. Az Azure-portálon hozzon létre, vagy nyissa meg a Logic Apps alkalmazást a Logic App tervezőben.

2. Adjon hozzá egy összekötőt, amely támogatja a helyi kapcsolatokat, például az SQL Server.

3. Válassza ki a következő sorrendben történik, **keresztül, a helyszíni adatátjáró**, adjon meg egy egyedi kapcsolatnevet és a szükséges adatokat, és válassza ki a használni kívánt adatok átjáró-erőforráshoz. Ha elkészült, kattintson a **Létrehozás** gombra.

   > [!TIP]
   > Egy egyedi kapcsolatnevet segítségével könnyen később, hogy a kapcsolat azonosítására, különösen akkor, ha több kapcsolatot is létrehozhat. Ha alkalmazható, tartalmazzák a tartománynevet a felhasználónévhez. 

   ![Logic app és az adatok átjáró közötti kapcsolat létrehozása](./media/logic-apps-gateway-connection/blankconnection.png)

Gratulálunk, az átjáró kapcsolat készen áll a használatára a Logic Apps alkalmazást.

## <a name="edit-your-gateway-connection-settings"></a>Az átjáró kapcsolat beállításainak szerkesztése

Miután egy gateway-kapcsolatot hoz létre a Logic Apps alkalmazást, érdemes később frissíteni a beállításait, hogy adott kapcsolat.

1. Az átjárókapcsolathoz megkeresése:

   * A logic app menü alatti **Fejlesztőeszközök**, jelölje be **API kapcsolatok**. 
   
     A **API kapcsolatok** ablaktábla megjeleníti azokat a Logic Apps alkalmazást, beleértve az átjáró kapcsolatokat társított összes API kapcsolat.

     ![Nyissa meg a logikai alkalmazáshoz, jelölje be a "API-kapcsolatok"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Vagy a főmenüben az Azure Ugrás **több szolgáltatások** > **Web + mobil** > **API kapcsolatok** minden API-kommunikációhoz, beleértve az átjáró kapcsolatok száma, az Azure-előfizetéshez társított. 

   * Vagy az Azure főmenü Ugrás **összes erőforrás** minden API-kommunikációhoz, beleértve az átjáró kapcsolatokat, az Azure-előfizetéshez társított.

2. Válassza ki a megtekintése, szerkesztése és válassza a kívánt átjáró kapcsolatot **szerkesztése API-kapcsolat**.

   > [!TIP]
   > Ha a frissítések nem lép érvénybe, próbálja meg [leállításával és újraindításával az átjáró Windows-szolgáltatás](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>Váltás, vagy a helyszíni adatok átjáró erőforrás törlése

Hozzon létre egy másik átjáró erőforrást, rendelje hozzá az átjárót egy másik erőforráscsoportban, vagy távolítsa el az átjáró-erőforráshoz, törölheti az átjáró-erőforráshoz az átjáró telepítésének befolyásolása nélkül. 

1. A főmenüből Azure, nyissa meg a **összes erőforrás**. 
2. Keresse meg és jelölje ki az adatok átjáró-erőforráshoz.
3. Válasszon **helyszíni Data Gateway**, és az erőforrás eszköztáron válassza **törlése**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>További lépések

* [A logikai alkalmazások védelme](./logic-apps-securing-a-logic-app.md)
* [Gyakori példák és forgatókönyvek a logic Apps alkalmazások](./logic-apps-examples-and-scenarios.md)
