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
ms.openlocfilehash: 19ea9c749b58f6f81dc2087caa77573062d883b5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39485830"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza az **Erőforrás létrehozása** > **Mobil** > **Értesítési központ** elemet.
   
      ![Azure Portal – Értesítési központ létrehozása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
1. Az **Értesítési központ** mezőbe írjon be egy egyedi nevet. Válassza ki a **Helyet**, **Előfizetést** és **Erőforráscsoportot** (ha már rendelkezik ilyennel). 
   
      Ha még nem rendelkezik szolgáltatásbusz-névtérrel, használhatja a központ neve alapján létrehozott alapértelmezett nevet (ha a névtér neve elérhető).
    
      Ha már rendelkezik szolgáltatásbusz-névtérrel, amelyben létrehozná a központot, kövesse ezeket a lépéseket

    a. A **Névtér** területen válassza a **Meglévő használata** hivatkozást. 
   
    b. Kattintson a **Létrehozás** gombra.
   
      ![Azure Portal – értesítési központ tulajdonságainak megadása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Válassza az **Értesítések** (harang ikon), majd az **Erőforrás megnyitása** lehetőséget. 

      ![Azure Portal – Értesítések -> Erőforrás megnyitása](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)    
1. A listáról válassza a **Hozzáférési szabályzatok** lehetőséget. Figyelje meg a két elérhető kapcsolati sztringet. Ezekre később szükség lesz a leküldéses értesítések kezeléséhez.

      >[!IMPORTANT]
      >**NE** használja a DefaultFullSharedAccessSignature elemet az alkalmazásban. Ezt csak a háttérben használhatja.
      >
   
      ![Azure Portal – értesítési központ kapcsolati sztringjei](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

