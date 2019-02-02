---
title: Az Azure App Service – hálózati konfigurációja szinkronizálása |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure App Service szolgáltatási csomagjában hálózati konfigurációja szinkronizálása.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: 02c64fcd51862d8ff9f107adb0ca5cfda5812a88
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567111"
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

- Felügyelt példány esetében a virtuális hálózat konfigurálásával kapcsolatos további információkért lásd: [felügyelt példány VNet architektúra](sql-database-managed-instance-connectivity-architecture.md) és [konfigurálása meglévő VNet](sql-database-managed-instance-configure-vnet-subnet.md).
