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
ms.openlocfilehash: 7bac2b291bec2ceda118c877cde997a2fa9f9f37
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283996"
---
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

1. A bal oldali navigációs panelen kattintson az **Erőforrás létrehozása** elemre. Keressen a *digital twins* kifejezésre, és válassza a **Digital Twins (előzetes verzió)** lehetőséget. Az üzembe helyezés elindításához kattintson a **Létrehozás** gombra.

    ![Digitális ikereszközök létrehozása](./media/create-digital-twins-portal/create-digital-twins.png)

1. A **Digital Twins** panelen adja meg a következő adatokat:
   * **Erőforrás neve**: Adjon meg egy egyedi nevet a Digital Twins-példány számára.
   * **Előfizetés**: Válassza ki az adott Digital Twins-példány létrehozásához használni kívánt előfizetést. 
   * **Erőforráscsoport**: A listából válasszon ki vagy hozzon létre egy [erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) a Digital Twins-példány számára.
   * **Hely**: Válassza ki az eszközökhöz legközelebbi helyet.

    ![Digitális ikereszközök létrehozása](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Tekintse át a Digital Twins adatait, majd kattintson a **Létrehozás** gombra. A Digital Twins-példány létrehozása néhány percet vehet igénybe. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

1. Nyissa meg a Digital Twins-példány **Áttekintés** paneljét. A **felügyeleti API** alatt egy hivatkozás látható.

    1. A **felügyeleti API** URL-címének formátuma: **_https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger_**. Ez az URL-cím az Ön példányára vonatkozó Azure Digital Twins REST API-dokumentációra mutat. Az API-dokumentáció értelmezésével és használatával kapcsolatban tekintse át [az Azure Digital Twins Swagger használatát](../articles/digital-twins/how-to-use-swagger.md) ismertető cikket.

    1. Módosítsa a **felügyeleti API** URL-címét a következő formátumra: **_https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/_**. Az alkalmazás a módosított URL-címet használja majd kiindulási URL-címként a példány eléréséhez. Másolja ezt a módosított URL-címet egy ideiglenes fájlba. A következő szakaszban szükség lesz rá.

    ![Felügyeleti API-k](./media/create-digital-twins-portal/digital-twins-management-api.png)