---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 09/17/2018
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: e5e015c1e12e68f54ae7fd32082faed1abfebe74
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51626391"
---
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása**. Keressen a **digital twins** kifejezésre, és válassza a **Digital Twins (előzetes verzió)** lehetőséget. Válassza ki **létrehozás** az üzembe helyezés elindításához.

   ![Hozzon létre egy új digitális Twins példányt szolgáló kiválasztások](./media/create-digital-twins-portal/create-digital-twins.png)

1. A **Digital Twins** panelen adja meg a következő adatokat:
   * **Erőforrás neve**: Adjon meg egy egyedi nevet a Digital Twins-példány számára.
   * **Előfizetés**: Válassza ki az adott Digital Twins-példány létrehozásához használni kívánt előfizetést. 
   * **Erőforráscsoport**: A listából válasszon ki vagy hozzon létre egy [erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) a Digital Twins-példány számára.
   * **Hely**: Válassza ki az eszközökhöz legközelebbi helyet.

    ![A megadott adatok digitális Twins panel](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Tekintse át a digitális Twins adatokat, és válassza ki **létrehozás**. A digitális Twins-példány is igénybe vehet néhány percet, hozhatók létre. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

1. Nyissa meg a Digital Twins-példány **Áttekintés** paneljét. Vegye figyelembe a hivatkozás alatt **felügyeleti API**.

   A **felügyeleti API** URL-cím formátuma: _https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger_. Ez az URL-cím az Ön példányára vonatkozó Azure Digital Twins REST API-dokumentációra mutat. Az API-dokumentáció értelmezésével és használatával kapcsolatban tekintse át [az Azure Digital Twins Swagger használatát](../articles/digital-twins/how-to-use-swagger.md) ismertető cikket.

    Módosítsa a **felügyeleti API** URL-cím a következő formátumban: _https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/_. Az alkalmazás a módosított URL-címet használja majd kiindulási URL-címként a példány eléréséhez. Másolja ezt a módosított URL-címet egy ideiglenes fájlba. Ez a következő szakaszban kell.

    ![Felügyeleti API](./media/create-digital-twins-portal/digital-twins-management-api.png)