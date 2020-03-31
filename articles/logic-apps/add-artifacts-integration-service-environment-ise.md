---
title: Erőforrások hozzáadása integrációs szolgáltatási környezetekhez
description: Logikai alkalmazások, integrációs fiókok, egyéni összekötők és felügyelt összekötők hozzáadása az integrációs szolgáltatáskörnyezethez (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164877"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Erőforrások hozzáadása az integrációs szolgáltatási környezethez (ISE) az Azure Logic Apps alkalmazásban

Miután létrehozott egy [integrációs szolgáltatási környezetet (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)erőforrásokat, például logikai alkalmazásokat, integrációs fiókokat és összekötőket adhat hozzá, hogy azok hozzáférhessenek az Azure virtuális hálózaterőforrásaihoz. Például a felügyelt ISE-összekötők, amelyek az ISE létrehozása után válnak elérhetővé, nem jelennek meg automatikusan a Logic App Designerben. Az ISE-összekötők használata előtt manuálisan kell [hozzáadnia és telepítenie ezeket az összekötőket az ISE-hez,](#add-ise-connectors-environment) hogy azok megjelenjenek a Logic App Designerben.

> [!IMPORTANT]
> Ahhoz, hogy a logikai alkalmazások és az integrációs fiókok együttműködjenek az ISE-ben, mindkettőnek ugyanazt az *ISE-t* kell használnia, mint a helyüket.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A logikai alkalmazások futtatásához létrehozott ISE. Ha nem rendelkezik ISE-vel, [először hozzon létre egy ISE-t.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

* Az ISE-be üzembe helyezett erőforrások létrehozásához, hozzáadásához vagy frissítéséhez hozzá kell rendelnie az ISE tulajdonosi vagy közreműködői szerepkörét, vagy rendelkezik az Azure-előfizetésen vagy az ISE-hez társított Azure-erőforráscsoporton keresztül örökölt engedélyekkel. Azok számára, akik nem rendelkeznek tulajdonosi, közreműködői vagy örökölt engedélyekkel, hozzárendelhetők az Integrációs szolgáltatás közreműködői szerepkörhöz vagy az Integrációs szolgáltatás környezeti fejlesztői szerepköréhez. A szerepköralapú hozzáférés-vezérlésről (RBAC) a [Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz?](../role-based-access-control/overview.md)

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Logikai alkalmazások létrehozása

Az integrációs szolgáltatási környezetben (ISE) futó logikai alkalmazások létrehozásához kövesse az alábbi lépéseket:

1. Keresse meg és nyissa meg az ISE-t, ha még nem nyitott. Az ISE menü **Beállítások**területén válassza a **Logikai alkalmazások** > **hozzáadása lehetőséget.**

   ![Új logikai alkalmazás hozzáadása az ISE-hez](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Adjon meg információkat a létrehozni kívánt logikai alkalmazásról, például:

   ![Integrációs szolgáltatáskörnyezet kiválasztása](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Név** | Igen | A létrehozandó logikai alkalmazás neve |
   | **Előfizetés** | Igen | A használandó Azure-előfizetés neve |
   | **Erőforráscsoport** | Igen | A használandó (új vagy meglévő) Azure-erőforráscsoport neve |
   | **Helyen** | Igen | Az **Integrációs szolgáltatás környezetei csoportban**válassza ki a használni kívánt ISE-t, ha még nincs kiválasztva. <p><p> **Fontos:** A logikai alkalmazások integrációs fiókkal való használatához mindkettőnek ugyanazt az ISE-t kell használnia. |
   ||||

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. Folytassa [a logikai alkalmazás létrehozását a szokásos módon.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

   Az eseményindítók és műveletek működésének különbségeit, valamint az ISE-t a több-bérlős Logic Apps szolgáltatáshoz képest az [Elszigetelt és többbérlős](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)szolgáltatások címkézését című témakörben találja.

1. A logikai alkalmazások és API-kapcsolatok ISE-ben való kezeléséről az [Integrációs szolgáltatás környezetének kezelése](../logic-apps/ise-manage-integration-service-environment.md)című témakörben található.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Integrációs fiókok létrehozása

A létrehozáskor kiválasztott [ISE termékváltozat](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) alapján az ISE további költségek nélkül tartalmaz speciális integrációs fiókhasználatot. Az integrációs szolgáltatási környezetben (ISE) létező logikai alkalmazások csak az ugyanabban az ISE-ben létező integrációs fiókokra hivatkozhatnak. Így ahhoz, hogy egy integrációs fiók működjön együtt az ISE logikai alkalmazásaival, mind az integrációs fióknak, mind a logikai alkalmazásoknak ugyanazt a *környezetet* kell használniuk, mint a helyüket. Az integrációs fiókokról és az ise-kről az Integrációs fiókok az [ISE-vel](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)című témakörben talál további információt.

Ha ISE-t használó integrációs fiókot szeretne létrehozni, kövesse az alábbi lépéseket:

1. Keresse meg és nyissa meg az ISE-t, ha még nem nyitott. Az ISE menü **Beállítások**területén válassza **az Integrációs fiókok** > **hozzáadása lehetőséget.**

   ![Új integrációs fiók hozzáadása az ISE-hez](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Adjon meg információkat a létrehozni kívánt logikai alkalmazásról, például:

   ![Integrációs szolgáltatáskörnyezet kiválasztása](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Név** | Igen | A létrehozni kívánt integrációs fiók neve |
   | **Előfizetés** | Igen | A használni kívánt Azure-előfizetés neve |
   | **Erőforráscsoport** | Igen | A használandó (új vagy meglévő) Azure-erőforráscsoport neve |
   | **Tarifacsomag** | Igen | Az integrációs fiókhoz használandó tarifacsomag |
   | **Helyen** | Igen | Az **Integrációs szolgáltatás környezetek csoportban**válassza ki ugyanazt az ISE-t, amelyet a logikai alkalmazások használnak, ha még nincs kiválasztva. <p><p> **Fontos:** Az integrációs fiók logikai alkalmazásokkal való használatához mindkettőnek ugyanazt az ISE-t kell használnia. |
   ||||

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. [A logikai alkalmazást a szokásos módon kapcsolja össze az integrációs fiókkal.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. Folytassa az erőforrások hozzáadását az integrációs fiókhoz, például [a kereskedelmi partnerekhez](../logic-apps/logic-apps-enterprise-integration-partners.md) és [a megállapodásokhoz.](../logic-apps/logic-apps-enterprise-integration-agreements.md)

1. Az integrációs fiókok ISE-ben való kezeléséhez olvassa el [az Integrációs szolgáltatás környezetének kezelése](../logic-apps/ise-manage-integration-service-environment.md)című témakört.

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE-összekötők hozzáadása

Az ISE létrehozása után elérhetővé vált Microsoft által felügyelt összekötők nem jelennek meg automatikusan a Logic App Designer összekötőválasztójában. Az ISE-összekötők használata előtt manuálisan kell hozzáadnia és telepítenie ezeket az összekötőket az ISE-hez, hogy azok megjelenjenek a Logic App Designerben.

1. Az ISE menü **Beállítások**területén válassza a **Felügyelt összekötők**lehetőséget. Az eszköztáron válassza a **Hozzáadás**gombot.

   ![Felügyelt összekötők megtekintése](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Az **Új felügyelt összekötő hozzáadása** ablaktáblán nyissa meg az **Összekötő keresése** listát. Válassza ki a használni kívánt ISE-összekötőt, de még nincs telepítve az ISE-ben. Kattintson a **Létrehozás** gombra.

   ![Válassza ki az ISE-ben telepíteni kívánt ISE-összekötőt](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Csak azok az ISE-összekötők érhetők el, amelyek jogosultak, de még nem vannak telepítve az ISE-ben. Az ISE-ben már üzembe helyezett összekötők nem választhatók ki.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Egyéni összekötők létrehozása

Egyéni összekötők használatához az ISE-ben hozza létre ezeket az egyéni összekötőket közvetlenül az ISE-n belülről.

1. Keresse meg és nyissa meg az ISE-t, ha még nem nyitott. Az ISE menü **Beállítások**területén válassza az **Egyéni összekötők** > **hozzáadása lehetőséget.**

   ![Egyéni összekötő létrehozása](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Adja meg a nevet, az Azure-előfizetést és az Azure erőforráscsoportot (új vagy meglévő) az egyéni összekötőhöz.

1. A **Hely** listában az **Integrációs szolgáltatás környezetek** csoportban válassza ki ugyanazt az ISE-t, amelyet a logikai alkalmazások használnak, és válassza a **Létrehozás**lehetőséget, például:

   ![Integrációs szolgáltatáskörnyezet kiválasztása](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Jelölje ki az új egyéni összekötőt, majd válassza a **Szerkesztés**lehetőséget, például:

   ![Egyéni összekötő kijelölése és szerkesztése](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Folytassa az összekötő tanévenként egy [OpenAPI-definícióból](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) vagy [SOAP-ból.](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)

1. Az ISE-ben lévő egyéni összekötők kezeléséhez olvassa [el az integrációs szolgáltatáskörnyezet kezelése című témakört.](../logic-apps/ise-manage-integration-service-environment.md)

## <a name="next-steps"></a>További lépések

* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md)
