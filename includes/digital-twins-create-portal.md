---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 12/17/2018
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 0c89c8353d410ddd54e8a59f3838c914702b96ca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124525"
---
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása**. Keressen a **digital twins** kifejezésre, és válassza a **Digital Twins (előzetes verzió)** lehetőséget. Válassza ki **létrehozás** az üzembe helyezés elindításához.

   ![Hozzon létre egy új digitális Twins példányt szolgáló kiválasztások](./media/create-digital-twins-portal/create-digital-twins.png)

1. A **Digital Twins** panelen adja meg a következő adatokat:
   * **Erőforrás neve**: Hozzon létre egy egyedi nevet a digitális Twins példány.
   * **Előfizetés**: Válassza ki a digitális Twins példány létrehozásához használni kívánt előfizetést. 
   * **Erőforráscsoport**: Válasszon vagy hozzon létre egy [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) digitális Twins-példány.
   * **Hely**: Válassza ki az Önhöz legközelebbi helyet az eszközökön.

     ![A megadott adatok digitális Twins panel](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Tekintse át a digitális Twins adatokat, és válassza ki **létrehozás**. A digitális Twins-példány is igénybe vehet néhány percet, hozhatók létre. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

1. Nyissa meg a Digital Twins-példány **Áttekintés** paneljét. Vegye figyelembe a hivatkozás alatt **felügyeleti API**.

   A **felügyeleti API** URL-cím formátuma `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Ez az URL-cím az Ön példányára vonatkozó Azure Digital Twins REST API-dokumentációra mutat. Az API-dokumentáció értelmezésével és használatával kapcsolatban tekintse át [az Azure Digital Twins Swagger használatát](../articles/digital-twins/how-to-use-swagger.md) ismertető cikket.

    Módosítsa a **felügyeleti API** URL-címe ebbe a formátumba `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Az alkalmazás a módosított URL-címet használja majd kiindulási URL-címként a példány eléréséhez. Másolja ezt a módosított URL-címet egy ideiglenes fájlba. Ez a következő szakaszban kell.

    ![Felügyeleti API](./media/create-digital-twins-portal/digital-twins-management-api.png)