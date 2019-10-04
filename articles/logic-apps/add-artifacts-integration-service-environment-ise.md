---
title: Összetevők hozzáadása az integrációs szolgáltatási környezetekhez (ISEs) Azure Logic Apps
description: Logikai alkalmazások, egyéni összekötők és integrációs fiókok hozzáadása az integrációs szolgáltatási környezethez (ISE) az Azure Virtual Networks (virtuális hálózatok) eléréséhez, a nyilvános vagy a "globális" Azure-ban elkülönített
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 6327f0c14b46ceaadbf7adaa58a70c32b39b7c2a
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960496"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Összetevők hozzáadása az integrációs szolgáltatási környezethez (ISE) Azure Logic Apps

Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)létrehozása után olyan összetevőket adhat hozzá, mint például a Logic apps, az integrációs fiókok és az összekötők, hogy hozzáférhessenek az Azure-beli virtuális hálózatban található erőforrásokhoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A logikai alkalmazások futtatásához létrehozott ISE. Ha nem rendelkezik ISE-vel, [először hozzon létre egy ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)-t.

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Logikai alkalmazások létrehozása

Az integrációs szolgáltatási környezetben (ISE) futó logikai alkalmazások létrehozásához kövesse az alábbi lépéseket:

1. Keresse meg és nyissa meg az ISE-t, ha még nincs megnyitva. Az ISE menü **Beállítások**területén válassza a **Logic apps** > **Hozzáadás**elemet.

   ![Új logikai alkalmazás hozzáadása az ISE-hez](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   – vagy –

   Az Azure fő menüjében válassza az **erőforrás létrehozása** > **integráció** > **logikai alkalmazás**lehetőséget.

1. Adja meg a logikai alkalmazáshoz használni kívánt nevet, Azure-előfizetést és Azure-erőforráscsoportot (új vagy meglévő).

1. A **hely** listában az **integrációs szolgáltatási környezetek** szakaszban válassza ki az ISE-t, például:

   ![Integrációs szolgáltatási környezet kiválasztása](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Ha a logikai alkalmazásait egy integrációs fiókkal szeretné használni, akkor ezeknek a logikai alkalmazásoknak és az integrációs fióknak ugyanazt az ISE-t kell használniuk.

1. Folytassa a [logikai alkalmazás létrehozását a szokásos módon](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Az eseményindítók és műveletek működésének, valamint a a globális Logic Apps szolgáltatással összehasonlított ISE használata esetén a következő témakörben talál további információt: [elkülönített versus Global az ISE áttekintésében](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Az ISE-ben a Logic apps és az API-kapcsolatok kezeléséhez lásd: [az integrációs szolgáltatási környezet kezelése](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Integrációs fiókok létrehozása

A létrehozáskor kiválasztott [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) alapján az ISE külön díj nélkül tartalmazza az integrációs fiók használatát. Az integrációs szolgáltatási környezetekben (ISE) található logikai alkalmazások csak olyan integrációs fiókokra hivatkozhatnak, amelyek ugyanabban az ISE-ban találhatók. Tehát ahhoz, hogy egy integrációs fiók működjön a Logic apps szolgáltatással egy ISE-ben, az integrációs fióknak és a Logic apps-nek *ugyanazt a környezetet* kell használnia, mint a helyük. Az integrációs fiókokkal és a ISEs kapcsolatos további információkért lásd: [integrációs fiókok és ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

ISE-t használó integrációs fiók létrehozásához kövesse az alábbi lépéseket:

1. Keresse meg és nyissa meg az ISE-t, ha még nincs megnyitva. Az ISE menü **Beállítások**területén válassza az **integrációs fiókok**@no__t – 2**Hozzáadás**lehetőséget.

   ![Új integrációs fiók hozzáadása az ISE-hez](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   – vagy –

   Az Azure fő menüjében válassza az **erőforrás létrehozása** > **integrációs** > **integrációs fiók**lehetőséget.

1. Adja meg az integrációs fiókjához használni kívánt nevet, Azure-előfizetést, Azure-erőforráscsoportot (új vagy meglévő) és díjszabási szintet.

1. A **hely** lista **integrációs szolgáltatási környezetek** szakaszában válassza ki ugyanazt az ISE-t, amelyet a Logic apps használ, például:

   ![Integrációs szolgáltatási környezet kiválasztása](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Kapcsolja össze a logikai alkalmazást az integrációs fiókjával a szokásos módon](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Folytassa az összetevők hozzáadásával az integrációs fiókjához, például a [kereskedelmi partnerekhez](../logic-apps/logic-apps-enterprise-integration-partners.md) és a [szerződésekhez](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Az ISE integrációs fiókjainak kezeléséhez lásd: [az integrációs szolgáltatási környezet kezelése](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE-összekötők hozzáadása

Felveheti a Microsoft által felügyelt összekötőket, amelyek elérhetők az ISE-ben, de nincsenek telepítve az ISE-ben.

1. Az ISE menü **Beállítások**területén válassza a **felügyelt összekötők**lehetőséget. Az eszköztáron válassza a **Hozzáadás**lehetőséget.

   ![Felügyelt összekötők megtekintése](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Az **új felügyelt összekötő hozzáadása** panelen nyissa meg az **összekötő keresése** listát. Ha a kívánt összekötő elérhető, válassza ki az összekötőt, majd válassza a **Létrehozás**lehetőséget.

   A lista csak azokat az összekötőket jeleníti meg, amelyek jogosultak, de nincsenek telepítve az ISE-ben. Az ISE-ben már üzembe helyezett összekötők nem érhetők el a kijelöléshez.

   ![Jogosult összekötő kiválasztása](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Egyéni összekötők létrehozása

Ha az ISE-ben egyéni összekötőket szeretne használni, hozza létre ezeket az egyéni összekötőket közvetlenül az ISE-ben.

1. Keresse meg és nyissa meg az ISE-t, ha még nincs megnyitva. Az ISE menü **Beállítások**területén válassza az **egyéni összekötők** > **Hozzáadás**elemet.

   ![Egyéni összekötő létrehozása](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Adja meg az egyéni összekötőhöz használni kívánt nevet, Azure-előfizetést és Azure-erőforráscsoportot (új vagy meglévő).

1. A **hely** listában az **integrációs szolgáltatási környezetek** szakaszban válassza ki ugyanazt az ISE-t, amelyet a Logic apps használ, majd válassza a **Létrehozás**lehetőséget, például:

   ![Integrációs szolgáltatási környezet kiválasztása](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Válassza ki az új egyéni összekötőt, majd válassza a **Szerkesztés**lehetőséget, például:

   ![Egyéni összekötő kiválasztása és szerkesztése](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Folytassa úgy, hogy az összekötőt a szokásos módon hozza létre egy [OpenAPI-definícióból](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) vagy [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)-ból.

1. Az ISE-ben lévő egyéni összekötők kezeléséhez lásd: [az integrációs szolgáltatási környezet kezelése](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>További lépések

* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md)
