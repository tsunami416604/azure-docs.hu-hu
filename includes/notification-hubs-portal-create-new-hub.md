---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c718fa972b9d6cba13a13edd3c7e8df29fa63207
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33835760"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza az **Erőforrás létrehozása** > **Web + mobil** > **Értesítési központ** elemet.
   
      ![Azure Portal – Értesítési központ létrehozása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. Az **Értesítési központ** mezőbe írjon be egy egyedi nevet. Válassza ki a **Helyet**, **Előfizetést** és **Erőforráscsoportot** (ha már rendelkezik ilyennel). 
   
      Ha még nem rendelkezik szolgáltatásbusz-névtérrel, használhatja a központ neve alapján létrehozott alapértelmezett nevet (ha a névtér neve elérhető).
    
      Ha már rendelkezik szolgáltatásbusz-névtérrel, amelyben létrehozná a központot, kövesse ezeket a lépéseket

    a. A **Névtér** területen válassza a **Meglévő használata** hivatkozást. 
   
    b. Kattintson a **Létrehozás** gombra.
   
      ![Azure Portal – értesítési központ tulajdonságainak megadása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Válassza az **Értesítések** (harang ikon), majd az **Erőforrás megnyitása** lehetőséget. 

      ![Azure Portal – Értesítések -> Erőforrás megnyitása](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)    
5. A listáról válassza a **Hozzáférési szabályzatok** lehetőséget. Figyelje meg a két elérhető kapcsolati karakterláncot. Ezekre később szükség lesz a leküldéses értesítések kezeléséhez.

      >[!IMPORTANT]
      >**NE** használja a DefaultFullSharedAccessSignature elemet az alkalmazásban. Ezt csak a háttérben használhatja.
      >
   
      ![Azure Portal – értesítési központ kapcsolati karakterláncai](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

