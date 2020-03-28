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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895429"
---
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Jelölje ki a kezdőlap oldalsávát, majd **kattintson az Erőforrás létrehozása**elemre. 

   [![Bontsa ki a kezdőlap oldalsávja, majd válassza a + Erőforrás létrehozása lehetőséget](./media/create-digital-twins-portal/azure-portal-create-a-resource.png)](./media/create-digital-twins-portal/azure-portal-create-a-resource.png#lightbox)

1. Keressen **digitális ikreket,** és válassza a **Digitális ikrek**lehetőséget. 

   [![Új digitális twins példány létrehozásának kiválasztása](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png)](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png#lightbox)

   Másik lehetőségként válassza **a Tárgyak internete**lehetőséget, majd a Digitális ikrek **(előnézet)** lehetőséget.

1. Az üzembehelyezési folyamat elindításához válassza a **Létrehozás** elemet.

   [![Az erőforrás telepítésének létrehozása és megerősítése](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png)](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png#lightbox)

1. A **Digital Twins** panelen adja meg a következő adatokat:
   * **Erőforrás neve**: Adjon meg egy egyedi nevet a Digital Twins-példány számára.
   * **Előfizetés**: Válassza ki az adott Digital Twins-példány létrehozásához használni kívánt előfizetést. 
   * **Erőforráscsoport**: A listából válasszon ki vagy hozzon létre egy [erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) a Digital Twins-példány számára.
   * **Hely**: Válassza ki az eszközökhöz legközelebbi helyet.

     [![Digitális Twins ablaktábla a megadott adatokkal](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Tekintse át a digitális twins-adatokat, és válassza a **Létrehozás gombot.** A digitális twins példány létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

1. Nyissa meg a Digital Twins-példány **Áttekintés** paneljét. Vegye figyelembe a **Felügyeleti API hivatkozását.** A **Felügyeleti API** URL-címe a következő formátumban van formázva: 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   Ez az URL-cím az Ön példányára vonatkozó Azure Digital Twins REST API-dokumentációra mutat. Az API-dokumentáció értelmezésével és használatával kapcsolatban tekintse át [az Azure Digital Twins Swagger használatát](../articles/digital-twins/how-to-use-swagger.md) ismertető cikket. Másolja és módosítsa a **Felügyeleti API** URL-címét erre a formátumra: 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   Az alkalmazás a módosított URL-címet használja majd kiindulási URL-címként a példány eléréséhez. Másolja ezt a módosított URL-címet egy ideiglenes fájlba. Erre a következő részben lesz szüksége.

   [![Felügyeleti API – áttekintés](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)