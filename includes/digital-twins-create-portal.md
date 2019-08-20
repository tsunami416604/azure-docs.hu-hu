---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 87599c6e0c4bd4313eac154b4d07110cbe4389a0
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622949"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **erőforrás létrehozása**lehetőséget. Keressen rá a **digitális ikrek**kifejezésre, és válassza a **digitális ikrek**lehetőséget. Válassza a **Létrehozás** lehetőséget a telepítési folyamat elindításához.

   ![Új digitális Twins-példány létrehozásának kijelölése](./media/create-digital-twins-portal/create-digital-twins.png)

1. A **Digital Twins** panelen adja meg a következő adatokat:
   * **Erőforrás neve**: Hozzon létre egy egyedi nevet a digitális Twins-példányhoz.
   * **Előfizetés**: Válassza ki a digitális Twins-példány létrehozásához használni kívánt előfizetést. 
   * **Erőforráscsoport**: Válasszon ki vagy hozzon létre egy [erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) a digitális Twins-példányhoz.
   * **Hely**: Válassza ki az eszközök legközelebbi helyét.

     ![Digitális Twins-ablaktábla megadott adatokkal](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Tekintse át a digitális ikrek adatait, majd válassza a **Létrehozás**lehetőséget. A digitális Twins-példány létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

1. Nyissa meg a Digital Twins-példány **Áttekintés** paneljét. Jegyezze fel a hivatkozást a **Management API**alatt.

   A **felügyeleti API** URL-címe a `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`következőképpen van formázva:. Ez az URL-cím az Ön példányára vonatkozó Azure Digital Twins REST API-dokumentációra mutat. Az API-dokumentáció értelmezésével és használatával kapcsolatban tekintse át [az Azure Digital Twins Swagger használatát](../articles/digital-twins/how-to-use-swagger.md) ismertető cikket.

    Módosítsa a **felügyeleti API** URL-címét erre `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`a formátumra. Az alkalmazás a módosított URL-címet használja majd kiindulási URL-címként a példány eléréséhez. Másolja ezt a módosított URL-címet egy ideiglenes fájlba. Ezt a következő szakaszban kell megadnia.

    ![Felügyeleti API](./media/create-digital-twins-portal/digital-twins-management-api.png)