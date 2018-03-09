---
title: "Az Azure App Service - sync hálózati konfigurációja |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan lehet szinkronizálni az Azure App Service-üzemeltetési terv a hálózati konfiguráció."
ms.service: sql-database
author: srdjan-bozovic
manager: cguyer
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 1968f736dbfc24495e4e932f9c8c5955dc607133
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Az Azure App Service-üzemeltetési terv fürttaghálózati konfiguráció szinkronizálása

Ez akkor fordulhat elő, bár meg [egy Azure virtuális hálózatra és az alkalmazás integrált](../app-service/web-sites-integrate-with-vnet.md), akkor nem lehet kapcsolatot létesíteni a felügyelt példány. Egy dolog, próbálja meg, hogy hálózati konfigurálásához a service-csomag frissítése. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Az alkalmazásszolgáltatási csomag üzemeltetési szinkronizálási hálózati konfigurációja

Ehhez kövesse az alábbi lépéseket:  

1. Ugrás a webalkalmazások App Service-csomag.
 
   ![app service-csomag](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Kattintson a **hálózati** majd **kattintson ide a kezelés**.
 
   ![kezelheti a service-csomag](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Válassza ki a **VNet** kattintson **szinkronizálási hálózati**. 
 
   ![sync network](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Várjon, amíg a szinkronizálás történik.
  
   ![végzett szinkronizálás](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Most már készen áll arra, hogy a felügyelt példányát a kapcsolatot létesíteni próbáljon.

## <a name="next-steps"></a>További lépések

- További információ a virtuális hálózat konfigurálása a felügyelt példány: [felügyelt példány VNet konfigurációját](sql-database-managed-instance-vnet-configuration.md).
