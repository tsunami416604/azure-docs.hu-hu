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
ms.date: 03/07/2018
ms.openlocfilehash: d5de908166e8de1d45a36f97aee8934653e59623
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163167"
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
