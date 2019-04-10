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
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: 0d7920080fd61389741fbe785f5141003bef5251
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358785"
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
