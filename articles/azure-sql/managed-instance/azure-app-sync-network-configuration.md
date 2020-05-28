---
title: Azure App Service hálózati konfigurációjának szinkronizálása
titleSuffix: Azure SQL Managed Instance
description: Ez a cikk azt ismerteti, hogyan szinkronizálhatja Azure App Service üzemeltetési terv hálózati konfigurációját az Azure SQL felügyelt példányával.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: 6e4f6789be2faa8d1af0fd75830541f8a9e27a7e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044569"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Azure App Service üzemeltetési csomag hálózati konfigurációjának szinkronizálása az Azure SQL felügyelt példányával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Előfordulhat, hogy bár az [alkalmazást Azure-Virtual Networkrel integrálta](../../app-service/web-sites-integrate-with-vnet.md), nem tud kapcsolatot LÉTESÍTENI az SQL felügyelt példányával. A frissítés vagy szinkronizálás során a szolgáltatási csomag hálózati konfigurációja megoldhatja a problémát. 

## <a name="sync-network-configuration"></a>Hálózati konfigurációja szinkronizálása 

Ehhez kövesse az alábbi lépéseket:  

1. Nyissa meg a Web Apps App Service csomagot.

   ![App Service-csomag](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Válassza a **hálózatkezelés** lehetőséget, majd **kattintson a kezelés elemre**.

   ![szolgáltatási csomag kezelése](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Válassza ki a **VNet** , és kattintson a **hálózat szinkronizálása**elemre.

   ![hálózat szinkronizálása](./media/azure-app-sync-network-configuration/sync.png)

4. Várjon, amíg a szinkronizálás elkészült.
  
   ![szinkronizálás kész](./media/azure-app-sync-network-configuration/sync-done.png)

Most már készen áll arra, hogy megpróbálja újból létrehozni a kapcsolatot az SQL felügyelt példányával.

## <a name="next-steps"></a>További lépések

- További információ a VNet SQL felügyelt példányhoz való konfigurálásáról: az [SQL felügyelt példányok VNet architektúrája](connectivity-architecture-overview.md) és [a meglévő VNet konfigurálása](vnet-existing-add-subnet.md).
