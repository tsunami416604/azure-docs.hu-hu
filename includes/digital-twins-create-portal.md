---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/07/2020
ms.custom: include file
ms.openlocfilehash: 9ccdc7a438d1dade534d39dc97a39a3bdae37dc4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895429"
---
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Válassza ki a kezdőlapot, majd az **+ erőforrás létrehozása**lehetőséget. 

   [![bontsa ki a Kezdőlap sávot, majd válassza az + erőforrás létrehozása lehetőséget.](./media/create-digital-twins-portal/azure-portal-create-a-resource.png)](./media/create-digital-twins-portal/azure-portal-create-a-resource.png#lightbox)

1. Keressen rá a **digitális ikrek**kifejezésre, és válassza a **digitális ikrek**lehetőséget. 

   [új digitális Twins-példány létrehozásához ![kiválasztása](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png)](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png#lightbox)

   Másik lehetőségként válassza a **eszközök internetes hálózata**lehetőséget, majd válassza a **digitális Twins (előzetes verzió)** lehetőséget.

1. Az üzembehelyezési folyamat elindításához válassza a **Létrehozás** elemet.

   [![az erőforrás központi telepítésének létrehozása és megerősítése](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png)](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png#lightbox)

1. A **Digital Twins** panelen adja meg a következő adatokat:
   * **Erőforrás neve**: Adjon meg egy egyedi nevet a Digital Twins-példány számára.
   * **Előfizetés**: Válassza ki az adott Digital Twins-példány létrehozásához használni kívánt előfizetést. 
   * **Erőforráscsoport**: A listából válasszon ki vagy hozzon létre egy [erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) a Digital Twins-példány számára.
   * **Hely**: Válassza ki az eszközökhöz legközelebbi helyet.

     [Digitális Twins-ablaktábla ![bevitt adatokkal](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Tekintse át a digitális ikrek adatait, majd válassza a **Létrehozás**lehetőséget. A digitális Twins-példány létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

1. Nyissa meg a Digital Twins-példány **Áttekintés** paneljét. Jegyezze fel a hivatkozást a **Management API**alatt. A **felügyeleti API** URL-címe a következőképpen van formázva: 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   Ez az URL-cím az Ön példányára vonatkozó Azure Digital Twins REST API-dokumentációra mutat. Az API-dokumentáció értelmezésével és használatával kapcsolatban tekintse át [az Azure Digital Twins Swagger használatát](../articles/digital-twins/how-to-use-swagger.md) ismertető cikket. Másolja és módosítsa a **felügyeleti API** URL-címét erre a formátumra: 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   Az alkalmazás a módosított URL-címet használja majd kiindulási URL-címként a példány eléréséhez. Másolja ezt a módosított URL-címet egy ideiglenes fájlba. Ezt a következő szakaszban kell megadnia.

   [![Management API – áttekintés](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)