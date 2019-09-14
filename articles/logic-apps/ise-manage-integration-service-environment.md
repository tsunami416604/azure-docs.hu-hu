---
title: Integrációs szolgáltatási környezetek kezelése Azure Logic Apps
description: A hálózati állapot és a Logic apps, kapcsolatok, egyéni összekötők és integrációs fiókok kezelése a Azure Logic Apps integrációs szolgáltatási környezetében (ISE)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 6a6b096911ac8596fe29aeb4596f1da6d5266794
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967812"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Az integrációs szolgáltatási környezet (ISE) kezelése Azure Logic Apps

Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) hálózati állapotának vizsgálatához és az ISE-ben létező logikai alkalmazások, kapcsolatok, integrációs fiókok és összekötők kezeléséhez kövesse a jelen témakör lépéseit. Az összetevők az ISE-ben való hozzáadásával kapcsolatban lásd: összetevők [hozzáadása az integrációs szolgáltatási környezethez](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Az ISE megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A portál keresési mezőjébe írja be az "integrációs szolgáltatási környezetek" kifejezést, majd válassza az **integrációs szolgáltatási környezetek**lehetőséget.

   ![Integrációs szolgáltatási környezetek keresése](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Az eredmények listából válassza ki az integrációs szolgáltatási környezetet.

   ![Integrációs szolgáltatási környezet kiválasztása](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Folytassa a következő szakasszal a logikai alkalmazások, kapcsolatok, összekötők vagy integrációs fiókok kereséséhez az ISE-ben.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Hálózati állapot ellenõrzése

Az ISE menü **Beállítások**területén válassza a **hálózati állapot**elemet. Ezen a panelen látható az alhálózatok és a kimenő függőségek állapota az egyéb szolgáltatásokban.

![Hálózati állapot ellenõrzése](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Logikai alkalmazások kezelése

Megtekintheti és kezelheti az ISE-ben található Logic apps-alkalmazásokat.

1. Az ISE menü **Beállítások**területén válassza a **logikai alkalmazások**elemet.

   ![Logikai alkalmazások megtekintése](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Ha el szeretné távolítani a már nem szükséges Logic apps-alkalmazásokat az ISE-ben, válassza ki a logikai alkalmazásokat, majd válassza a **Törlés**lehetőséget. A törölni kívánt törlés megerősítéséhez válassza az **Igen**lehetőséget.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API-kapcsolatok kezelése

Megtekintheti és kezelheti az ISE-ben futó logikai alkalmazások által létrehozott kapcsolatokat.

1. Az ISE menü **Beállítások**területén válassza az API- **kapcsolatok**elemet.

   ![API-kapcsolatok megtekintése](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Ha el szeretné távolítani a már nem szükséges kapcsolatokat az ISE-ben, válassza ki ezeket a kapcsolatokat, majd válassza a **Törlés**lehetőséget. A törölni kívánt törlés megerősítéséhez válassza az **Igen**lehetőséget.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>ISE-összekötők kezelése

Megtekintheti és kezelheti az ISE-ben üzembe helyezett API-összekötőket.

1. Az ISE menü **Beállítások**területén válassza a **felügyelt összekötők**lehetőséget.

   ![Felügyelt összekötők megtekintése](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Ha el szeretné távolítani az ISE-ben nem elérhető összekötőket, válassza ki az összekötőket, majd válassza a **Törlés**lehetőséget. A törölni kívánt törlés megerősítéséhez válassza az **Igen**lehetőséget.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Egyéni összekötők kezelése

Megtekintheti és kezelheti az ISE-ben üzembe helyezett egyéni összekötőket.

1. Az ISE menü **Beállítások**területén válassza az **Egyéni összekötők**lehetőséget.

   ![Egyéni összekötők keresése](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Ha el szeretné távolítani az ISE-ben már nem szükséges egyéni összekötőket, válassza ki az összekötőket, majd válassza a **Törlés**lehetőséget. A törölni kívánt törlés megerősítéséhez válassza az **Igen**lehetőséget.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Integrációs fiókok kezelése

1. Az ISE menü **Beállítások**területén válassza az **integrációs fiókok**elemet.

   ![Integrációs fiókok keresése](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Ha már nincs rá szükség az integrációs fiókok eltávolítására az ISE-ből, válassza ki ezeket az integrációs fiókokat, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [csatlakozhat az elkülönített logikai alkalmazásokból származó Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) -beli virtuális hálózatokhoz
