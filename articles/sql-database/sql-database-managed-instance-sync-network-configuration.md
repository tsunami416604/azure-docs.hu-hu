---
title: Az Azure App Service – hálózati konfigurációja szinkronizálása |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure App Service szolgáltatási csomagjában hálózati konfigurációja szinkronizálása.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 57c4dd523a5dffc48a2d2d403d2a440a8d6cde95
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257894"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Az Azure App Service szolgáltatási csomagjában hálózati konfigurációja szinkronizálása

Ez akkor fordulhat elő, hogy bár Ön [az alkalmazás integrálva van az Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), felügyelt példányokra való kapcsolat nem tudott létrehozni. Próbálja meg egyik dolog, hogy a szolgáltatáscsomag fürttaghálózati konfiguráció frissítése. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Az App Service szolgáltatási csomagjában hálózati konfigurációja szinkronizálása

Ehhez kövesse az alábbi lépéseket:  

1. Nyissa meg a web apps App Service-csomag.
 
   ![app service-csomag](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Kattintson a **hálózatkezelés** majd **kattintson ide a kezelés**.
 
   ![service-csomag kezelése](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Válassza ki a **VNet** kattintson **hálózat szinkronizálása**. 
 
   ![hálózat szinkronizálása](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Várjon, amíg a szinkronizálás történik.
  
   ![végzett szinkronizálás](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Most már készen áll, próbálja meg újból a kapcsolatot és a felügyelt példány.

## <a name="next-steps"></a>További lépések

- Felügyelt példány esetében a virtuális hálózat konfigurálásával kapcsolatos további információkért lásd: [felügyelt példány VNet-konfiguráció](sql-database-managed-instance-vnet-configuration.md).
