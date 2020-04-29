---
title: Erőforrás hozzáadása integrációs szolgáltatási környezetekhez
description: Logikai alkalmazások, integrációs fiókok, egyéni összekötők és felügyelt összekötők hozzáadása az integrációs szolgáltatási környezethez (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79164877"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Erőforrások hozzáadása az integrációs szolgáltatási környezethez (ISE) Azure Logic Apps

Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)létrehozása után olyan erőforrásokat adhat hozzá, mint például a Logic apps, az integrációs fiókok és az összekötők, hogy hozzáférhessenek az Azure-beli virtuális hálózatban található erőforrásokhoz. Az ISE létrehozása után elérhető felügyelt ISE-összekötők például nem jelennek meg automatikusan a Logic app Designerben. Az ISE-összekötők használata előtt ezeket az [összekötőket manuálisan kell hozzáadnia és telepítenie az ISE](#add-ise-connectors-environment) -ben, hogy azok megjelenjenek a Logic app Designerben.

> [!IMPORTANT]
> Ahhoz, hogy a Logic apps és az integrációs fiókok együtt működjenek az ISE-ben, mindkettőnek *ugyanazt az ISE* -t kell használnia, mint a helyük.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A logikai alkalmazások futtatásához létrehozott ISE. Ha nem rendelkezik ISE-vel, [először hozzon létre egy ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)-t.

* Az ISE-ben központilag telepített erőforrások létrehozásához, hozzáadásához vagy frissítéséhez hozzá kell rendelnie a tulajdonosi vagy közreműködői szerepkört az ISE-hez, vagy ha rendelkezik az ISE-hez társított Azure-előfizetéssel vagy Azure-erőforráscsoport-hozzáféréssel. Azok számára, akik nem rendelkeznek tulajdonossal, közreműködővel vagy örökölt engedélyekkel, hozzá lehet rendelni a integrációs szolgáltatási környezet közreműködő szerepkört vagy integrációs szolgáltatási környezet fejlesztői szerepkört. További információ a szerepköralapú hozzáférés-vezérlésről (RBAC): [Mi az Azure-erőforrások szerepköralapú hozzáférés-vezérlése (RBAC)](../role-based-access-control/overview.md)?

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Logikai alkalmazások létrehozása

Az integrációs szolgáltatási környezetben (ISE) futó logikai alkalmazások létrehozásához kövesse az alábbi lépéseket:

1. Keresse meg és nyissa meg az ISE-t, ha még nincs megnyitva. Az ISE menü **Beállítások**területén válassza a **Logic apps** > **Hozzáadás**elemet.

   ![Új logikai alkalmazás hozzáadása az ISE-hez](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Adja meg a létrehozni kívánt logikai alkalmazás adatait, például:

   ![Integrációs szolgáltatási környezet kiválasztása](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Név** | Igen | A létrehozandó logikai alkalmazás neve |
   | **Előfizetés** | Igen | A használni kívánt Azure-előfizetés neve |
   | **Erőforráscsoport** | Igen | A használni kívánt Azure-erőforráscsoport (új vagy meglévő) neve |
   | **Hely** | Igen | Az **integrációs szolgáltatási környezetek**területen válassza ki a használni kívánt ISE-t, ha még nincs kiválasztva. <p><p> **Fontos**: Ha a logikai alkalmazásokat integrációs fiókkal szeretné használni, mindkettőnek ugyanazt az ISE-t kell használnia. |
   ||||

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. Folytassa a [logikai alkalmazás létrehozását a szokásos módon](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Az eseményindítók és műveletek működésének, valamint a több-bérlős Logic Apps szolgáltatáshoz képest az ISE használatakor megjelenő különbségekért lásd: [elkülönített és több-bérlős megoldás az ISE áttekintésében](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Az ISE-ben a Logic apps és az API-kapcsolatok kezeléséhez lásd: [az integrációs szolgáltatási környezet kezelése](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Integrációs fiókok létrehozása

A létrehozáskor kiválasztott [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) alapján az ISE külön díj nélkül tartalmazza az integrációs fiók használatát. Az integrációs szolgáltatási környezetekben (ISE) található logikai alkalmazások csak olyan integrációs fiókokra hivatkozhatnak, amelyek ugyanabban az ISE-ban találhatók. Tehát ahhoz, hogy egy integrációs fiók működjön a Logic apps szolgáltatással egy ISE-ben, az integrációs fióknak és a Logic apps-nek *ugyanazt a környezetet* kell használnia, mint a helyük. Az integrációs fiókokkal és a ISEs kapcsolatos további információkért lásd: [integrációs fiókok és ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

ISE-t használó integrációs fiók létrehozásához kövesse az alábbi lépéseket:

1. Keresse meg és nyissa meg az ISE-t, ha még nincs megnyitva. Az ISE menü **Beállítások**területén válassza az **integrációs fiókok** > **Hozzáadás**elemet.

   ![Új integrációs fiók hozzáadása az ISE-hez](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Adja meg a létrehozni kívánt logikai alkalmazás adatait, például:

   ![Integrációs szolgáltatási környezet kiválasztása](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Név** | Igen | A létrehozni kívánt integrációs fiók neve |
   | **Előfizetés** | Igen | A használni kívánt Azure-előfizetés neve |
   | **Erőforráscsoport** | Igen | A használni kívánt Azure-erőforráscsoport (új vagy meglévő) neve |
   | **Díjszabási csomag** | Igen | Az integrációs fiókhoz használandó díjszabási csomag |
   | **Hely** | Igen | Az **integrációs szolgáltatási környezetek**területen válassza ki ugyanazt az ISE-t, amelyet a Logic apps használ, ha még nincs kiválasztva. <p><p> **Fontos**: Ha az integrációs fiókot logikai alkalmazásokkal szeretné használni, mindkettőnek ugyanazt az ISE-t kell használnia. |
   ||||

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. [Kapcsolja össze a logikai alkalmazást az integrációs fiókjával a szokásos módon](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Folytassa az erőforrások hozzáadásával az integrációs fiókjához, például a [kereskedelmi partnerekhez](../logic-apps/logic-apps-enterprise-integration-partners.md) és a [szerződésekhez](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Az ISE integrációs fiókjainak kezeléséhez lásd: [az integrációs szolgáltatási környezet kezelése](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE-összekötők hozzáadása

Azok a Microsoft által felügyelt összekötők, amelyek az ISE létrehozása után válnak elérhetővé, nem jelennek meg automatikusan a Logic app Designer összekötő-választójában. Az ISE-összekötők használata előtt ezeket az összekötőket manuálisan kell hozzáadnia és telepítenie az ISE-ben, hogy azok megjelenjenek a Logic app Designerben.

1. Az ISE menü **Beállítások**területén válassza a **felügyelt összekötők**lehetőséget. Az eszköztáron válassza a **Hozzáadás**lehetőséget.

   ![Felügyelt összekötők megtekintése](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Az **új felügyelt összekötő hozzáadása** panelen nyissa meg az **összekötő keresése** listát. Válassza ki a használni kívánt ISE-összekötőt, de még nincs telepítve az ISE-ben. Kattintson a **Létrehozás** gombra.

   ![Válassza ki az ISE-ben telepíteni kívánt ISE-összekötőt](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Csak azok az ISE-összekötők érhetők el, amelyek jogosultak, de még nincsenek telepítve az ISE számára a kiválasztáshoz. Az ISE-ben már üzembe helyezett összekötők nem érhetők el a kijelöléshez.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Egyéni összekötők létrehozása

Ha az ISE-ben egyéni összekötőket szeretne használni, hozza létre ezeket az egyéni összekötőket közvetlenül az ISE-ben.

1. Keresse meg és nyissa meg az ISE-t, ha még nincs megnyitva. Az ISE menü **Beállítások**területén válassza az **Egyéni összekötők** > **Hozzáadás**elemet.

   ![Egyéni összekötő létrehozása](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Adja meg az egyéni összekötőhöz használni kívánt nevet, Azure-előfizetést és Azure-erőforráscsoportot (új vagy meglévő).

1. A **hely** listában az **integrációs szolgáltatási környezetek** szakaszban válassza ki ugyanazt az ISE-t, amelyet a Logic apps használ, majd válassza a **Létrehozás**lehetőséget, például:

   ![Integrációs szolgáltatási környezet kiválasztása](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Válassza ki az új egyéni összekötőt, majd válassza a **Szerkesztés**lehetőséget, például:

   ![Egyéni összekötő kiválasztása és szerkesztése](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Folytassa úgy, hogy az összekötőt a szokásos módon hozza létre egy [OpenAPI-definícióból](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) vagy [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)-ból.

1. Az ISE-ben lévő egyéni összekötők kezeléséhez lásd: [az integrációs szolgáltatási környezet kezelése](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>További lépések

* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md)
