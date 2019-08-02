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
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517461"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Az integrációs szolgáltatási környezet (ISE) kezelése Azure Logic Apps

Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) hálózati állapotának, valamint a Logic apps, kapcsolatok, integrációs fiókok és az ISE-ben létező egyéni összekötők felügyeletéhez kövesse a jelen témakör lépéseit.

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

1. Az ISE menü **Beállítások**területén válassza a **logikai alkalmazások**elemet.

   ![Logikai alkalmazások keresése](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Ha a logikai alkalmazásokat el szeretné távolítani az ISE-ről, ha már nincs rá szükség, válassza ki a logikai alkalmazásokat, majd válassza a **Törlés**lehetőséget.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API-kapcsolatok kezelése

1. Az ISE-ben futó Logic apps által létrehozott API-kapcsolatok megtekintéséhez az ISE menüben, a **Beállítások**területen válassza az **API-kapcsolatok**elemet.

   ![API-kapcsolatok keresése](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Ha a továbbiakban nem szükséges a kapcsolatok eltávolítása az ISE-ből, válassza ki ezeket a kapcsolatokat, majd válassza a **Törlés**lehetőséget.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Egyéni összekötők kezelése

1. Az ISE-ben létrehozott egyéni összekötők megtekintéséhez az ISE menüben, a **Beállítások**területen válassza az **Egyéni összekötők**lehetőséget.

   ![Egyéni összekötők keresése](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Ha a továbbiakban nem szükséges az egyéni összekötők eltávolítása az ISE-ből, válassza ki az összekötőket, majd válassza a **Törlés**lehetőséget.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Integrációs fiókok kezelése

1. Az ISE menü **Beállítások**területén válassza az integrációs **fiókok**elemet.

   ![Integrációs fiókok keresése](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Ha már nincs rá szükség az integrációs fiókok eltávolítására az ISE-ből, válassza ki ezeket az integrációs fiókokat, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [csatlakozhat az elkülönített logikai alkalmazásokból származó Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) -beli virtuális hálózatokhoz
