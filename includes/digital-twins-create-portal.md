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
ms.openlocfilehash: 92b9a4754769566feb3658e07081e9fdae78fcfc
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903815"
---
1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).

1. A Kezdőlap lapon válassza az **+ erőforrás létrehozása**lehetőséget. Keressen rá a **digitális ikrek**kifejezésre, és válassza a **digitális ikrek**lehetőséget. Az üzembehelyezési folyamat elindításához válassza a **Létrehozás** elemet.

   [új digitális Twins-példány létrehozásához ![kiválasztása](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

1. A **Digital Twins** panelen adja meg a következő adatokat:
   * **Erőforrás neve**: Adjon meg egy egyedi nevet a Digital Twins-példány számára.
   * **Előfizetés**: Válassza ki az adott Digital Twins-példány létrehozásához használni kívánt előfizetést. 
   * **Erőforráscsoport**: A listából válasszon ki vagy hozzon létre egy [erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) a Digital Twins-példány számára.
   * **Hely**: Válassza ki az eszközökhöz legközelebbi helyet.

     [Digitális Twins-ablaktábla ![bevitt adatokkal](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Tekintse át a digitális ikrek adatait, majd válassza a **Létrehozás**lehetőséget. A digitális Twins-példány létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

1. Nyissa meg a Digital Twins-példány **Áttekintés** paneljét. Jegyezze fel a hivatkozást a **Management API**alatt.

   A **felügyeleti API** URL-címe `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`ként van formázva. Ez az URL-cím az Ön példányára vonatkozó Azure Digital Twins REST API-dokumentációra mutat. Az API-dokumentáció értelmezésével és használatával kapcsolatban tekintse át [az Azure Digital Twins Swagger használatát](../articles/digital-twins/how-to-use-swagger.md) ismertető cikket.

    Másolja és módosítsa a **felügyeleti API** URL-címét a következő formátumra: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Az alkalmazás a módosított URL-címet használja majd kiindulási URL-címként a példány eléréséhez. Másolja ezt a módosított URL-címet egy ideiglenes fájlba. Ezt a következő szakaszban kell megadnia.

    [![felügyeleti API](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)