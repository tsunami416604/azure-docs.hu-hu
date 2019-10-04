---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/24/2019
ms.custom: include file
ms.openlocfilehash: 7b2df437833f270a6e102257693426f4cc65b9d2
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949737"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **erőforrás létrehozása**lehetőséget. Keressen rá a **digitális ikrek**kifejezésre, és válassza a **digitális ikrek**lehetőséget. Válassza a **Létrehozás** lehetőséget a telepítési folyamat elindításához.

   [@no__t – 1Selections új Digital Twins-példány létrehozásához](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

1. A **Digital Twins** panelen adja meg a következő adatokat:
   * **Erőforrás neve**: Hozzon létre egy egyedi nevet a digitális Twins-példányhoz.
   * **Előfizetés**: Válassza ki a digitális Twins-példány létrehozásához használni kívánt előfizetést. 
   * **Erőforráscsoport**: Válasszon ki vagy hozzon létre egy [erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) a digitális Twins-példányhoz.
   * **Hely**: Válassza ki az eszközök legközelebbi helyét.

     [![Digital ikrek ablaktábla megadott adatokkal](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Tekintse át a digitális ikrek adatait, majd válassza a **Létrehozás**lehetőséget. A digitális Twins-példány létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

1. Nyissa meg a Digital Twins-példány **Áttekintés** paneljét. Jegyezze fel a hivatkozást a **Management API**alatt.

   A **felügyeleti API** URL-címének formátuma `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Ez az URL-cím az Ön példányára vonatkozó Azure Digital Twins REST API-dokumentációra mutat. Az API-dokumentáció értelmezésével és használatával kapcsolatban tekintse át [az Azure Digital Twins Swagger használatát](../articles/digital-twins/how-to-use-swagger.md) ismertető cikket.

    Módosítsa a **kezelési API** URL-címét erre a formátumra `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Az alkalmazás a módosított URL-címet használja majd kiindulási URL-címként a példány eléréséhez. Másolja ezt a módosított URL-címet egy ideiglenes fájlba. Ezt a következő szakaszban kell megadnia.

    [@no__t – 1Management API](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)